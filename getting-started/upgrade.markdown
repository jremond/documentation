---
layout: default
title: Upgrade Instructions
sortkey: 2
categories: [Getting Started, Upgrade]
published: true
alias: getting-started-upgrade.html
tags: [getting started, upgrade]
---

Upgrading CFEngine Enterprise can be tricky, there are many dependencies on 
the customer's policy. We have created shell script to optionally do the job 
for you, trying to be as generic as possible to not interfere with existing 
policy. The script is not perfect because we cannot predict all factors, such 
as what files customers use or do not use, what kind of a directory structure 
they have, etc.  Manual work (especially cosmetic work to the policy) should 
be made after usage of the script. 

Of course, we do not want to force you to use the upgrade script. With this 
document we go through each step so you can choose whether you want to use the 
script, or upgrade manually by doing the same steps by yourself. Whichever 
option you choose:

**ALWAYS TEST UPGRADE IN A TEST ENVIRONMENT PRIOR TO UPGRADING PRODUCTION 
ENVIRONMENTS**

If you have any questions, please do not hesitate to contact your sales 
representative, or our support engineers through the [support 
channel](https://cfengine.com/otrs/customer.pl)

THE UPGRADE SCRIPT IS AVAILABLE AT 
https://cfengine.com/software/?dir=Enterprise-3.0.0/.upgrade_script
or in the document listing for CFEngine Enterprise Free 25.

## Upgrade procedure for the Hub

This is the upgrade script, step by step, and also applicable to manual 
upgrades (but you do not need to do all of the following steps)

### Prerequisites

* CFEngine 3 Enterprise HUB version 2.1.x/2.2.x
* Linux shell

***** WARNING *****
STANDARD LIBRARY: The script will upgrade cfengine_stdlib.cf automatically. If 
you have added your own bodies to the file, they will be overwritten. Also, 
CFEngine Nova 2.0.x clients will fail to execute the new standard library. If 
you use the script, we suggest backing up your old cfengine_stdlib.cf and 
copying it over the one installed by the script.
*** END WARNING ***

* Do not panic if the script fails. Please see how to rollback your 
  configuration in "THERE IS SOMETHING WRONG. HOW CAN I ROOLBACK MY BINARY 
  VERSION AND CONFIGURATION?"

* backup /var/cfengine/masterfiles to /var/cfengine/masterfiles_$(date)
  $ cp -r $WORKDIR/masterfiles $WORKDIR/masterfiles_$(date +%T_%F)

* upgrade cfengine-nova and cfengine-nova-expansion packages
  Redhat/SuSE
  $ rpm -Uvh $2/cfengine-nova*$1*.rpm
  Debian
  $ dpkg --install $2/cfengine-nova_*$1*.deb $2/cfengine-nova-*$1*.deb

* move/copy/update some files to the new policy framework (for more details 
  about the new framework, see 3.0.0-Release_Notes_CFEngine_3_Enterprise.txt)

    *
* update /var/cfengine/bin/cf-twin to the latest version
  $ cp -vf $WORKDIR/bin/cf-agent $WORKDIR/bin/cf-twin

* remove MongoDB lock file
  $ rm -f $WORKDIR/state/mongod.lock

* reset webroot directory to avoid confilct on 3.0.0
  $ rm -rf $DOCROOT/*

* edit /var/cfengine/masterfiles/promises.cf
    * add cfe_internal/ to path of all CFE_ prefixed files  (the new default 
      location of CFE_ prefixed files is now  
      `/var/cfengine/masterfiles/cfe_internal`)

      $ sed -i 's/"CFE_knowledge.cf",/"cfe_internal\/CFE_knowledge.cf",/g' $WORKDIR/masterfiles/promises.cf
      $ sed -i 's/"CFE_hub_specific.cf",/"cfe_internal\/CFE_hub_specific.cf",/g' $WORKDIR/masterfiles/promises.cf
      $ sed -i 's/"CFE_cfengine.cf",/"cfe_internal\/CFE_cfengine.cf",/g' $WORKDIR/masterfiles/promises.cf

     * add cfe_internal/example_use_goals.cf to inputs.

      $ sed -i 's/"cfe_internal\/CFE_knowledge.cf",/"cfe_internal\/CFE_knowledge.cf",\n                    "cfe_internal\/example_use_goals.cf",/g' $WORKDIR/masterfiles/promises.cf

     * add libraries/ to path of cfengine_stdlib.cf (the new default location of cfengine_stdlib.cf is now /var/cfengine/masterfiles/libraries)
    $ sed -i 's/"cfengine_stdlib.cf",/"libraries\/cfengine_stdlib.cf",/g' $WORKDIR/masterfiles/promises.cf

    * add services/ to path of file_change.cf (we group services together in a directory to avoid ending up cluttering the content of masterfiles when there are many service policies)
      $ sed -i 's/"file_change.cf",/"services\/file_change.cf",/g' $WORKDIR/masterfiles/promises.cf

       * rename cfengine_management to cfe_internal_management
      $ sed -i 's/cfengine_management/cfe_internal_management/g' $WORKDIR/masterfiles/promises.cf

       * add cfe_internal_hub_vars to bundlesequence
      $ sed -i 's/"def",/"def",\n                    "cfe_internal_hub_vars",/g' $WORKDIR/masterfiles/promises.cf

       * add cfsketch_run to bundlesequence and cf-sketch-runfile.cf to inputs
      $ sed -i 's/"cfe_internal_hub_vars",/"cfe_internal_hub_vars",\n                    "cfsketch_run",/g' $WORKDIR/masterfiles/promises.cf
      $ sed -i 's/"libraries\/cfengine_stdlib.cf",/"libraries\/cfengine_stdlib.cf",\n                    "cf-sketch-runfile.cf",/g' $WORKDIR/masterfiles/promises.cf

       * for a person who says "no", we replace update.cf with update_bins.cf and update_policy.cf
      $ sed -i 's/"update.cf",/"update_bins.cf",\n                    "update_policy.cf",/g' $WORKDIR/masterfiles/promises.cf

       * rename goal_1 to goal_infosec
      $ sed -i 's/goal_1/goal_infosec/g' $WORKDIR/masterfiles/promises.cf

       * rename goal_2 to goal_complicance
      $ sed -i 's/goal_2/goal_compliance/g' $WORKDIR/masterfiles/promises.cf

       * remove commercial_customer class
      $ sed -i '/commercial_customer::/d' $WORKDIR/masterfiles/promises.cf

       * remove nova_edition and constellation_editon classes
      $ sed -i '/nova_edition.*::/d' $WORKDIR/masterfiles/promises.cf

       * remove garbage_collection because we have one in CFE_cfengine.cf
      $ sed -i '/maintenance.*goal_3/d' $WORKDIR/masterfiles/promises.cf
      $ sed -i '/comment.*rotation.*Nova/d' $WORKDIR/masterfiles/promises.cf
      $ sed -i '/usebundle.*garbage_collection/d' $WORKDIR/masterfiles/promises.cf

       * since the depends_on attribute has changed behavior (see the release notes), having it there might cause problems.
      $ sed -i '/depends_on/d' $WORKDIR/masterfiles/services/file_change.cf
      $ sed -i '/depends_on/d' $WORKDIR/masterfiles/update.cf

## Once the script is done...

 * If you answered "no" to "Have you ever added your custom-built promises to /var/cfengine/bin/masterfiles/failsafe.cf or /var/cfengine/masterfiles/update.cf?":

    * Even if you anwered "no" to the question, please make sure your policy is correct by verifying the resultant policy files.

    * verify for syntax errors
      $ /var/cfengine/bin/cf-promises -f /var/cfengine/masterfiles/failsafe.cf
      $ /var/cfengine/bin/cf-promises -f /var/cfengine/masterfiles/promises.cf

    * if there is no error, please run failsafe to start up all CFEngine processes
      $ /var/cfengine/bin/cf-agent -f /var/cfengine/masterfiles/failsafe.cf -IK

    * We highly recommend cosmetic re-work of the resulting policy to ensure readability. See our new 3.0.0 framework in /var/cfengine/share/NovaBase

    * You should keep all control bodies (body agent/executor/server/hub/reporter/monitor/runagent control) and server access_rule() bundle to at least have some suggested attributes

 * If you answered "yes" to "Have you ever added your custom-built promises to /var/cfengine/bin/masterfiles/failsafe.cf or /var/cfengine/masterfiles/update.cf?":

    * you should synchronize the contents in failsafe.cf and update.cf manually. 

     *** For example; the promiser "/var/cfengine/bin/mongod <parameters>" in bundle update in update.cf: please change it to run in file-based configuration mode (this bundle is called update_policy in the 3.0.0 policy framework) .
       
       vars:

        "mongodb_dir"        string => "$(sys.workdir)/state",
                            comment => "Directory where MongoDB files will be stored on hub - if changed: requires DB shutdown and move of files",
                             handle => "cfe_internal_update_policy_mongodb_dir";

        "mongodb_conf_file"  string => translatepath("$(inputs_dir)/failsafe/mongod.conf"),
                            comment => "Path to MongoDB configuration file",
                             handle => "cfe_internal_update_policy_mongodb_conf_file";

       commands:

        !windows.am_policy_hub.start_mongod::
        
         "/var/cfengine/bin/mongod --dbpath $(update_policy.mongodb_dir) --config $(update_policy.mongodb_conf_file) > /dev/null < /dev/null 2>&1"

    * some contents of your current bundle agent update_bins in update.cf might be outdated. Please keep it similar to `/var/cfengine/share/NovaBase/failsafe/update_bins.cf` as much as possible.

## Something went wrong - how can I rollback my binary version and configuration?

 * remove /var/cfengine/masterfiles and /var/cfengine/inputs directories
 * rename /var/cfengine/masterfiles_$(date) to /var/cfengine/masterfiles
 * remove your current 3.0.0 cfengine-nova and cfengine-nova-expansion packages
 * reinstall your previous cfengine-nova and cfengien-nova-expansion packages
 * rebootstrap the HUB. MP should be up and running in less than 10 minutes



## Upgrade procedure for the hosts

For host upgrades there are 2 approaches: manual or automatic upgrade.

### Manual

Update cfengine-nova on each client by rpm, dpkg or corresponding Windows command. For Linux/UNIX systems, update cf-twin as follows:

    $ cp -vf /var/cfengine/bin/cf-agent /var/cfengine/bin/cf-twin

For Windows systems copy/overwrite the content of `C:\Program Files\Cfengine\bin` to `C:\Program Files\Cfengine\bin-twin`

### Automatic

On the hub, copy the client cfengine-nova packages to the operating system specific distribution directories in `/var/cfengine/master_software_updates` and CFEngine 3 Enterprise will take care of the rest.
