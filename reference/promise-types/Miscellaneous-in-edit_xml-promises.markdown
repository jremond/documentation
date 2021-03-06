---
layout: default
title: edit_xml
categories: [Reference, Promise Types, files, edit_xml]
published: true
alias: reference-promise-types-edit-xml-promises.html
tags: [reference, bundles, agent, edit_xml, xml, edit_xml promises, files promises]
---

The use of XML documents in systems configuration is widespread. XML
documents represent data that is complex and can be structured in
various ways. The XML based editing offers a powerful environment for
editing hierarchical and structured XML datasets.

-   [build\_xpath in \*](#build_005fxpath-in-_002a)
-   [select\_xpath in \*](#select_005fxpath-in-_002a)

#### `build_xpath`

**Type**: string

**Allowed input range**: (arbitrary string)

**Synopsis**: Build an XPath within the XML file

**Example**:  
   

```cf3
body build_xpath example(s)
  {
  build_xpath => "$(s)";
  }
```

**Notes**:  
   

Please note that when `build_xpath` is defined as an attribute, within
an `edit_xml` promise body, the tree described by the specified XPath
will be verified and built BEFORE other `edit_xml` promises within same
promise body. Therefore, the file will not be empty during the execution
of such promises.

#### `select_xpath`

**Type**: string

**Allowed input range**: (arbitrary string)

**Synopsis**: Select the XPath node in the XML file to edit

**Example**:  
   

```cf3
body select_xpath example(s)
{
select_xpath => "$(s)";
}
```

**Notes**:  
   
 Edits to the XML document take place within the selected node. This
attribute is not used when inserting XML content into an empty file.
