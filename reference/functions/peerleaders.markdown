---
layout: default
title: peerleaders
categories: [Reference, Functions, peerleaders]
published: true
alias: reference-functions-peerleaders.html
tags: [reference, functions, peerleaders]
---

### Function peerleaders

**Synopsis**: peerleaders(arg1,arg2,arg3) returns type **slist**

  
 *arg1* : File name of host list, *in the range* "?(/.\*)   
 *arg2* : Comment regex pattern, *in the range* .\*   
 *arg3* : Peer group size, *in the range* 0,99999999999   

Get a list of peer leaders from the named partitioning

**Example**:  
   

```cf3
bundle agent peers
{
vars:

  "mygroup" slist => peers("/tmp/hostlist","#.*",4);

  "myleader" string => peerleader("/tmp/hostlist","#.*",4);

  "all_leaders" slist => peerleaders("/tmp/hostlist","#.*",4);

reports:

 linux::

   "mypeer $(mygroup)";
   "myleader $(myleader)";
   "another leader $(all_leaders)";

}
```

**Notes**:  
   

```cf3
     
     (slist) peers(file of hosts,comment pattern,group size);
     
```

This function returns a list of hostnames that may be considered peer
leaders in the partitioning scheme described in the file of hosts. Peers
are defined according to a list of hosts, provided as a file in the
first argument. This file should contain a list (one per line), possible
with comments, of fully qualified host names. CFEngine breaks up this
list into non-overlapping groups of up to groupsize, each of which has a
leader that is the first host in the group.

The current host need not belong to this file.

**ARGUMENTS**:

File of hosts

A path to a list of hosts.   

Comment pattern

A pattern that matches a legal comment in the file. The regex is
unanchored, meaning it may match a partial line (see [Anchored vs.
unanchored regular
expressions](#Anchored-vs_002e-unanchored-regular-expressions)).
Comments are stripped as the file is read.   

Group size

A number between 2 and 64 which represents the number of peers in a
peer-group. An arbitrary limit of 64 is set on groups to avoid
nonsensical promises.

Example file:

```cf3
     one
     two
     three # this is a comment
     four
     five
     six
     seven
     eight
     nine
     ten
     eleven
     twelve
     etc
     
```
