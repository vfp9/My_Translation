标题：Start using XML and XSLT to create HTML 

作者：Calvin Hsia

时间：2004年六月22日，星期二 4:10 AM

翻译：xinjie
 
    今晚，在西雅图的 Visual Foxpro 使用者聚会上，Richard Stanton 对 VFP9 中报表具有的许多特征做了陈述，现在，这些特性已经可以应用于 beta 版。

 

    在会议上，提出了一个问题，如何使用 XSLT 来从 XML 中创建 HTML 。我认为可以用少量的代码来解决这个问题。

 

    这里有一个用于此问题的示例代码：它使用一个 customer 表的前 4 条记录，并使用 CURSORTOXML() 函数创建 XML 。

 

    然后使用 TEXTMERGE 来创建第 2 个 XML 字符串，也就是 XSLT。

 

    这两个字符串分别载入它们自己的 XMLDOM 实例。

 

    然后，使用 TRANSFORMNODE 方法将 XSL 转换成 SML 用以创建 HTML（一个具有标题和记录的 HTML 表），它将在 IE 中显示出来。

 

    这是一个很大的进步！

 

 
```foxpro
USE customer

cursortoxml(0,'cxml',1,0,4)   && 创建一个字符串变量 cxml，它包含 customer 的前 4 条记录 

TEXT TO cxsl noshow           && 创建一个字符串变量 cxsl: 它是 XSLT ，用来将 XML 转换成 HTML 表

xml version="1.0"?>

<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">

<xsl:output method="html"/>

<xsl:template match="/">

<HTML>

<HEAD>

<TITLE><xsl:value-of select="//VFPData/customer"/>TITLE>

HEAD>

<BODY>

<table frame="box">

<tr>

<th>Customer IDth>

<th>CompanyNameth>

<th>ContactNameth>

tr>

<xsl:for-each select="//VFPData/customer">

<tr>

<td><xsl:value-of select="cust_id"/>td>

<td><xsl:value-of select="company"/>td>

<td><xsl:value-of select="contact"/>td>

tr>

xsl:for-each>

table>

BODY>

HTML>

xsl:template>

xsl:stylesheet>

ENDTEXT

 

LOCAL oxml as msxml.DOMDocument, oxsl as msxml.DOMDocument

oxml=NEWOBJECT('msxml.DOMDocument')

oxsl=NEWOBJECT('msxml.DOMDocument') && SMLDOM 的另一个实例

oxml.loadXML(cxml)                  && 在 SMLDOM 中载入 XML

oxsl.loadXML(cxsl)                  && 载入刚刚创建的 XSLT

cHTML= oxml.transformNode(oxsl)     && 转换 XSL 到 XML 

STRTOFILE(cHTML,"d:\t.htm")         && 将结果放入一个文件

!start d:\t.htm                     && 在 IE 中显示文件

 

*代码结束
```
