---
layout: default
title: parsestringarray
categories: [Reference, Functions, parsestringarray]
published: true
alias: reference-functions-parsestringarray.html
tags: [reference, functions, parsestringarray]
---

### Function parsestringarray

**Synopsis**: parsestringarray(arg1,arg2,arg3,arg4,arg5,arg6) returns
type **int**

  
 *arg1* : Array identifier to populate, *in the range*
[a-zA-Z0-9\_\$(){}\\[\\].:]+   
 *arg2* : A string to parse for input data, *in the range* "?(/.\*)   
 *arg3* : Regex matching comments, *in the range* .\*   
 *arg4* : Regex to split data, *in the range* .\*   
 *arg5* : Maximum number of entries to read, *in the range*
0,99999999999   
 *arg6* : Maximum bytes to read, *in the range* 0,99999999999   

Read an array of strings from a file and assign the dimension to a
variable

**Example**:  
   

```cf3
bundle agent test(f) 
{
vars:

 #######################################
 # Define data inline for convenience
 #######################################

  "table"   string => 

"one: a
two: b
three: c";

#######################################

 "dim" int => parsestringarray(
                          "items",
                  "$(table)",
                  "\s*#[^\n]*",
                  ":",
                  "1000",
                  "200000"
                  );

 "keys" slist => getindices("items");

reports:
  cfengine_3::
    "$(keys)";
}
```

**Notes**:  
   

*History*: Was introduced in version 3.1.5, Nova 2.1.0 (2011)

This function mirrors the exact behaviour of `readstringarray()`, but
reads data from a variable instead of a file (See [Function
readstringarray](#Function-readstringarray)). By making data readable
from a variable, data driven policies can be kept inline. This means
that they will be visible in the CFEngine Knowledge Management portal.
