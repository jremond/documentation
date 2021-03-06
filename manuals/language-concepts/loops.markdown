---
layout: default
title: Loops
categories: [Manuals, Language Concepts, Loops]
published: true
alias: manuals-language-concepts-loops.html
tags: [manuals, language, syntax, concepts, loops]
---

There are no explicit loops in CFEngine, instead there are lists. To make a 
loop, you simply refer to a list as a scalar and CFEngine will assume a loop 
over all items in the list.

In the examples below the list component has three elements. The list as a 
whole may be referred to as @(component), in order to pass the whole list to a 
promise where a list is expected. However, if we write $(component), i.e. the 
scalar variable, then CFEngine assumes that it should substitute each scalar 
from the list in turn, and thus iterate over the list elements using a loop.

```cf3
    body common control
    {
        bundlesequence  => { "example" };
    }

    bundle agent example
    {
        vars:
            "component" slist => { "cf-monitord", "cf-serverd", "cf-execd" };

            "array[cf-monitord]" string => "The monitor";
            "array[cf-serverd]" string => "The server";
            "array[cf-execd]" string => "The executor, not executionist";

        reports:
            cfengine_3::
                "$(component) is $(array[$(component)])";
    }
```

The output looks something like this:
 
     /usr/local/sbin/cf-agent -f ./unit_loops.cf -K
     
     cf-monitord is The monitor
     cf-serverd is The server
     cf-execd is The executor, not executionist

You see from this that, if we refer to a list variable using the scalar 
reference operator `$()`, CFEngine interprets this to mean “please iterate 
over all values of the list”. Thus, we have effectively a `foreach' loop, 
without the attendant syntax.

If a variable is repeated, its value is tied throughout the expression; so the 
output of:

```cf3
    body common control
    {
        bundlesequence  => { "example" };
    }

    bundle agent example    
    {
    vars:
      "component" slist => { "cf-monitord", "cf-serverd", "cf-execd" };
    
      "array[cf-monitord]" string => "The monitor";
      "array[cf-serverd]" string => "The server";
      "array[cf-execd]" string => "The executor, not executioner";
    
    commands:
       "/bin/echo $(component) is"    
                args => "$(array[$(component)])";
    }
```

is as follows **TODO: update with new output format**:

    Q ".../bin/echo cf-mo": cf-monitord is The monitor
     -> Last 1 QUOTEed lines were generated by "/bin/echo cf-monitord is The monitor"
    Q ".../bin/echo cf-se": cf-serverd is The server
     -> Last 1 QUOTEed lines were generated by "/bin/echo cf-serverd is The server"
    Q ".../bin/echo cf-ex": cf-execd is The executor, not executioner
     -> Last 1 QUOTEed lines were generated by "/bin/echo cf-execd is The executor, not executioner"
