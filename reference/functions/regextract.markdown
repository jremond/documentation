---
layout: default
title: regextract
categories: [Reference, Functions, regextract]
published: true
alias: reference-functions-regextract.html
tags: [reference, functions, regextract]
---

### Function regextract

**Synopsis**: regextract(arg1,arg2,arg3) returns type **class**

  
 *arg1* : Regular expression, *in the range* .\*   
 *arg2* : Match string, *in the range* .\*   
 *arg3* : Identifier for back-references, *in the range*
[a-zA-Z0-9\_\$(){}\\[\\].:]+   

True if the regular expression in arg 1 matches the string in arg2 and
sets a non-empty array of backreferences named arg3

**Example**:  
   

```cf3
bundle agent testbundle
{
classes:

    # Extract regex backreferences and put them in an array

    "ok" expression => regextract(
                                 "xx ([^\s]+) ([^\s]+).* xx",
                                 "xx one two three four xx",
                                 "myarray"
                                 );
reports:

 ok::

   "ok - \"$(myarray[0])\" = xx + \"$(myarray[1])\" + \"$(myarray[2])\" + .. + xx";
}

```

**Notes**:  
   

**Arguments:**

*regex*

A regular expression containing one or more parenthesized back
references. The regular expression is anchored, meaning it must match
the entire string (See [Anchored vs. unanchored regular
expressions](#Anchored-vs_002e-unanchored-regular-expressions)).   

*data*

A string to be matched to the regular expression.   

*identifier*

The name of an array which (if there are any back reference matches from
the regular expression) will be populated with the values, in the
manner:

```cf3
          $(identifier[0]) = entire string
          $(identifier[1]) = back reference 1, etc
```

**History**: This function was introduced in CFEngine version 3.0.4
(2010)
