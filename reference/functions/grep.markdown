---
layout: default
title: grep
categories: [Reference, Functions, grep]
published: true
alias: reference-functions-grep.html
tags: [reference, functions, grep]
---

### Function grep

**Synopsis**: grep(arg1,arg2) returns type **slist**

  
 *arg1* : Regular expression, *in the range* .\*   
 *arg2* : CFEngine list identifier, *in the range*
[a-zA-Z0-9\_\$(){}\\[\\].:]+   

Extract the sub-list if items matching the regular expression in arg1 of
the list named in arg2

**Example**:  
   

```cf3
bundle agent test

{
vars:

  "mylist" slist => { "One", "Two", "Three", "Four", "Five" };

  "sublist" slist => grep("T.*","mylist");

  "empty_list" slist => grep("ive","mylist");

reports:

 linux::

  "Item: $(sublist)";

}
```

**Notes**:  
   

Extracts a sublist of elements matching the regular expression in arg1
from a list variable specified in arg2. The regex is anchored (See
[Anchored vs. unanchored regular
expressions](#Anchored-vs_002e-unanchored-regular-expressions)).
