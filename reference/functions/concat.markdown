---
layout: default
title: concat
categories: [Reference, Functions, concat]
published: true
alias: reference-functions-concat.html
tags: [reference, functions, concat]
---

### Function concat

**Synopsis**: concat(...) returns type **string**

  

Concatenate all arguments into string

**Example**:  
   

```cf3
commands:
  "/usr/bin/generate_config $(config)"
    ifvarclass => concat("have_config_", canonify("$(config)"));
```

**Notes**:  
   
 *History*: Was introduced in 3.2.0, Nova 2.1.0 (2011)
