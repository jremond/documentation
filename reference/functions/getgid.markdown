---
layout: default
title: getgid
categories: [Reference, Functions, getgid]
published: true
alias: reference-functions-getgid.html
tags: [reference, functions, getgid]
---

### Function getgid

**Synopsis**: getgid(arg1) returns type **int**

  
 *arg1* : Group name in text, *in the range* .\*   

Return the integer group id of the named group on this host

**Example**:  
   

```cf3
body common control

{
bundlesequence  => { "example" };
}

###########################################################

bundle agent example

{     
vars:

  "gid" int => getgid("users");

reports:

  Yr2008::

    "Users gid is $(gid)";

}
```

**Notes**:  
   

If the named group does not exist, the variable will not be defined. On
Windows, which does not support group ids, the variable will not be
defined.
