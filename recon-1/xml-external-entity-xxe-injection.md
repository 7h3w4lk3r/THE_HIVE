# ⭕ XML External Entity (XXE) Injection

The XML document type definition (DTD) contains declarations that can define the structure of an XML document. The DTD is declared within the optional DOCTYPE element at the start of the XML document

XXE are a type of custom XML entity whose defined values are loaded from outside of the DTD

Allows an attacker to interfere with an application's processing of XML data

<mark style="color:red;">**Examples**</mark>

* Custom entities to be defined within the DTD\
  `<!DOCTYPE foo [ <!ENTITY`` `<mark style="color:orange;">`myentity`</mark>` ``"`<mark style="color:yellow;">`my entity value`</mark>`" > ]>`&#x20;
* XML external entities\
  uses the "SYSTEM" keyword and must specify a URL\
  `<!DOCTYPE foo [ <!ENTITY`` `<mark style="color:orange;">`NAME`</mark>` ``SYSTEM "`<mark style="color:yellow;">`http://normal-website.com`</mark>`" > ]>`\
  `<!DOCTYPE foo [ <!ENTITY`` `<mark style="color:orange;">`NAME`</mark>` ``SYSTEM "`<mark style="color:yellow;">`file:///path/to/file`</mark>`" > ]>`&#x20;
