# 使用 Python 解析 XML

[![Bright Data 推广](https://github.com/bright-cn/Awesome-Web-Scraping/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner%20CN.png)](https://www.bright.cn/)

了解如何使用 ElementTree、lxml 和 SAX 等库在 Python 中解析 XML，从而增强你的数据处理项目。

- [XML 文件的关键概念](#xml-文件的关键概念)
- [在 Python 中解析 XML 的多种方法](#在-python-中解析-xml-的多种方法)
- [ElementTree](#elementtree)
- [lxml](#lxml)
- [minidom](#minidom)
- [SAX 解析器](#sax-解析器)

## XML 文件的关键概念

在深入了解如何使用 Python 解析 XML 之前，首先需要理解什么是 XML 模式定义（XSD），以及构成 XML 文件的基本元素。这些基础知识将帮助你根据解析需求选择合适的 Python 库。

[XSD](https://en.wikipedia.org/wiki/XML_Schema_(W3C)) 是一种模式规范，用于定义 XML 文档允许的结构、内容和数据类型。它相当于一组验证规则，可确保 XML 文件遵循一致的格式。

XML 文件通常包含 `Namespace`、`root`、`attributes`、`elements` 和 `text content` 等元素，它们共同表示结构化数据。

- **[`Namespace`](https://www.w3schools.com/xml/xml_namespaces.asp)** 用于唯一标识 XML 文档中的元素和属性。它有助于避免命名冲突，并支持不同 XML 数据集之间的互操作性。
- **[`root`](https://en.wikipedia.org/wiki/Root_element)** 是 XML 文档的顶层元素。它是 XML 结构的入口点，并包含所有其他元素。
- **[`attributes`](https://www.w3schools.com/xml/xml_attributes.asp)** 提供有关元素的额外上下文。它们定义在元素的开始标签中，由名称-值对组成。
- **[`elements`](https://www.w3schools.com/xml/xml_elements.asp)** 是 XML 文件的核心单元，表示实际数据或结构标签。元素可以相互嵌套，从而构建层次结构。
- **[`text content`](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent)** 指元素开始标签与结束标签之间的实际文本数据。其中可以包含纯文本、数值或其他字符。

例如，[Bright Data 站点地图](https://www.bright.cn/post-sitemap.xml)采用以下 XML 结构：

- **`urlset`** 充当 `root` 元素。
- **`<urlset xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd">`** 是 `urlset` 元素的命名空间声明。这表示模式规则适用于 `urlset` 及其所有嵌套元素。
- **`url`** 是 `root` 元素的直接子元素。
- **`loc`** 是 `url` 元素中的子元素。

现在，你已经对 XSD 和 XML 结构有了更清晰的了解，接下来可以使用几个实用的 Python 库解析 XML 文件，将这些知识付诸实践。

## 在 Python 中解析 XML 的多种方法

我们以 Bright Data 站点地图为例。在以下示例中，将使用 Python `requests` 库获取 Bright Data 站点地图的内容。

Python requests 库并非内置库，因此你需要先安装它。可以使用以下命令进行安装：

```sh
pip install requests
```

## ElementTree

[ElementTree XML API](https://docs.python.org/3/library/xml.etree.elementtree.html) 提供了一种简单易用的方式，可在 Python 中解析和生成 XML 数据。由于它是 Python 标准库的一部分，因此无需额外安装。

例如，你可以使用 [`findall()`](https://docs.python.org/3/library/xml.etree.elementtree.html#xml.etree.ElementTree.Element.findall) 方法从根元素中检索所有 `url` 元素，并打印每个 `loc` 元素的文本内容，如下所示：

```python
import xml.etree.ElementTree as ET
import requests

url = 'https://brightdata.com/post-sitemap.xml'

response = requests.get(url)
if response.status_code == 200:
   
    root = ET.fromstring(response.content)

    for url_element in root.findall('.//{http://www.sitemaps.org/schemas/sitemap/0.9}url'):
        loc_element = url_element.find('{http://www.sitemaps.org/schemas/sitemap/0.9}loc')
        if loc_element is not None:
            print(loc_element.text)
else:
    print("Failed to retrieve XML file from the URL.")

```

站点地图中的所有 URL 都会打印在输出中：

```
https://brightdata.com/case-studies/powerdrop-case-study
https://brightdata.com/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.com/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.com/case-studies/the-seo-transformation
https://brightdata.com/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.com/case-studies/highly-targeted-influencer-marketing
https://brightdata.com/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.com/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.com/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.com/case-studies/data-intensive-analytical-solutions
https://brightdata.com/case-studies/canopy-advantage-solutions
https://brightdata.com/case-studies/seamless-digital-automations
```

ElementTree 是 Python 中一种简单直观的 XML 解析器，非常适合读取 RSS 源等小型脚本。不过，它缺乏强大的模式验证功能，可能不适合复杂或大规模的 XML 解析，而 `lxml` 等库更适合这些场景。

## lxml

[lxml](https://lxml.de/) 是一个快速、易用且功能丰富的 API，可用于在 Python 中解析 XML 文件。你可以使用 `pip` [安装 `lxml`](https://lxml.de/installation.html#installation)：

```sh
pip install lxml
```

安装完成后，你可以通过[多种 API](https://lxml.de/apidoc/lxml.html) 方法使用 `lxml` 解析 XML 文件，例如 `find()`、`findall()`、`findtext()`、`get()` 和 `get_element_by_id()`。

例如，你可以使用 `findall()` 方法遍历 `url` 元素，查找其中的 `loc` 元素（即 `url` 元素的子元素），然后使用以下代码打印位置文本：

```python
from lxml import etree
import requests

url = "https://brightdata.com/post-sitemap.xml"

response = requests.get(url)
if response.status_code == 200:

    root = etree.fromstring(response.content)
    

    for url in root.findall(".//{http://www.sitemaps.org/schemas/sitemap/0.9}url"):
        loc = url.find("{http://www.sitemaps.org/schemas/sitemap/0.9}loc").text.strip()
        print(loc)
else:
    print("Failed to retrieve XML file from the URL.")
```

输出会显示站点地图中找到的所有 URL：

```
https://brightdata.com/case-studies/powerdrop-case-study
https://brightdata.com/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.com/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.com/case-studies/the-seo-transformation
https://brightdata.com/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.com/case-studies/highly-targeted-influencer-marketing
https://brightdata.com/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.com/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.com/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.com/case-studies/data-intensive-analytical-solutions
https://brightdata.com/case-studies/canopy-advantage-solutions
https://brightdata.com/case-studies/seamless-digital-automations
```

到目前为止，你已经了解了如何定位元素并显示其值。接下来，我们来看看如何在解析 XML 文件之前根据其模式进行验证。此步骤可确认文件是否遵循 XSD 中定义的结构。

以下是站点地图的 XSD：

```xml
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.sitemaps.org/schemas/sitemap/0.9"
           xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
           elementFormDefault="qualified"
           xmlns:xhtml="http://www.w3.org/1999/xhtml">

  
  <xs:element name="urlset">
    <xs:complexType>
      <xs:sequence>
        <xs:element ref="url" minOccurs="0" maxOccurs="unbounded"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
  
  <xs:element name="url">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="loc" type="xs:anyURI"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>

</xs:schema>
```

要使用该站点地图进行模式验证，请务必手动复制它，并创建一个名为 `schema.xsd` 的文件。

现在，使用此 XSD 验证 XML 文件：

```python
from lxml import etree

import requests

url = "https://brightdata.com/post-sitemap.xml"

response = requests.get(url)

if response.status_code == 200:

    root = etree.fromstring(response.content)

    try:
        print("Schema Validation:")
        schema_doc = etree.parse("schema.xsd")  
        schema = etree.XMLSchema(schema_doc)  
        schema.assertValid(root)  
        print("XML is valid according to the schema.")
    except etree.DocumentInvalid as e:
        print("XML validation error:", e)
```

在此步骤中，你先使用 [`etree.parse()`](https://lxml.de/tutorial.html#the-parse-function) 方法解析 XSD 文件，然后根据解析后的内容构建 XML Schema。最后，使用 `assertValid()` 根据该模式验证 XML 根元素。如果 XML 通过验证，你将看到类似 `XML is valid according to the schema` 的消息；否则会抛出 [`DocumentInvalid`](https://lxml.de/api/lxml.etree.DocumentInvalid-class.html) 异常。

输出应如下所示：

```
 Schema Validation:
    XML is valid according to the schema.
```

现在，我们来读取一个使用 `xpath` 方法按路径查找元素的 XML 文件。

要使用 `xpath()` 方法读取元素，请使用以下代码：

```python
from lxml import etree

import requests

url = "https://brightdata.com/post-sitemap.xml"
response = requests.get(url)

if response.status_code == 200:
   
    root = etree.fromstring(response.content)
    
    print("XPath Support:")
    root = etree.fromstring(response.content)

    namespaces = {"ns": "http://www.sitemaps.org/schemas/sitemap/0.9"}
    for url in root.xpath(".//ns:url/ns:loc", namespaces=namespaces):
        print(url.text.strip())

```

在此代码片段中，你注册了 `ns` 前缀，并将其关联到命名空间 URI `http://www.sitemaps.org/schemas/sitemap/0.9`。随后，`XPath` 表达式使用此前缀定位命名空间中的元素。具体而言，`.//ns:url/ns:loc` 会选择该命名空间内作为 `url` 元素子元素的所有 `loc` 元素。

输出如下所示：

```
XPath Support:

https://brightdata.com/case-studies/powerdrop-case-study
https://brightdata.com/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.com/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.com/case-studies/the-seo-transformation
https://brightdata.com/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.com/case-studies/highly-targeted-influencer-marketing
https://brightdata.com/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.com/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.com/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.com/case-studies/data-intensive-analytical-solutions
https://brightdata.com/case-studies/canopy-advantage-solutions
https://brightdata.com/case-studies/seamless-digital-automations
```

`find()` 和 `findall()` 方法比 `xpath()` 更快，因为 `xpath()` 会将所有结果加载到内存中。除非需要更复杂的查询，否则请使用 `find()`。

`lxml` 是一个功能强大的 XML 和 HTML 解析库，支持 XPath、模式验证和 XSLT 等高级功能。它非常适合高性能或复杂任务，但需要单独安装。

如果你要处理大型或复杂的 XML 数据（例如金融数据源），`lxml` 是高效查询、验证和转换数据的理想选择。

## minidom

[`minidom`](https://docs.python.org/3/library/xml.dom.minidom.html) 是 Python 标准库中一个简单、轻量级的 XML 解析库。虽然它的功能和效率不如 `lxml`，但提供了一种解析和操作 XML 数据的简便方式。

你可以使用多种 DOM 方法访问元素。例如，[`getElementsByTagName()` 方法](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementsByTagName)可让你按标签名称检索元素。

以下是使用 `minidom` 库解析 XML 文件并按标签名称获取元素的示例：

```python
import requests
import xml.dom.minidom

url = "https://brightdata.com/post-sitemap.xml"

response = requests.get(url)
if response.status_code == 200:
    dom = xml.dom.minidom.parseString(response.content)
    
    urlset = dom.getElementsByTagName("urlset")[0]
    for url in urlset.getElementsByTagName("url"):
        loc = url.getElementsByTagName("loc")[0].firstChild.nodeValue.strip()
        print(loc)
else:
    print("Failed to retrieve XML file from the URL.")
```

输出如下所示：

```
https://brightdata.com/case-studies/powerdrop-case-study
https://brightdata.com/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.com/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.com/case-studies/the-seo-transformation
https://brightdata.com/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.com/case-studies/highly-targeted-influencer-marketing
https://brightdata.com/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.com/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.com/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.com/case-studies/data-intensive-analytical-solutions
https://brightdata.com/case-studies/canopy-advantage-solutions
https://brightdata.com/case-studies/seamless-digital-automations
```

`minidom` 将 XML 数据表示为 DOM 树，便于导航和操作。它非常适合读取、修改或创建简单 XML 结构等基本任务。

如果你的程序需要从 XML 文件中读取设置，`minidom` 可让你轻松访问特定元素，例如查找子节点或属性。比如，你可以快速检索 `font-size` 节点，并在应用程序中使用其值。

## SAX 解析器

[SAX 解析器](https://docs.python.org/3/library/xml.sax.html)是一种事件驱动型 XML 解析器，它会按顺序处理文档，并触发开始标签、结束标签和文本内容等事件。与 DOM 解析器不同，SAX 不会将整个文档加载到内存中，因此非常适合注重内存效率的大型 XML 文件。

要使用 SAX，你需要为特定的 XML 事件定义事件处理程序，例如 `startElement` 和 `endElement`，并可对其进行自定义，以处理文档的结构和内容。

以下是使用 SAX 解析器处理 XML 文件的示例，其中定义了从站点地图中提取 URL 信息的事件处理程序：

```python
import requests
import xml.sax.handler
from io import BytesIO

class MyContentHandler(xml.sax.handler.ContentHandler):
    def __init__(self):
        self.in_url = False
        self.in_loc = False
        self.url = ""

    def startElement(self, name, attrs):
        if name == "url":
            self.in_url = True
        elif name == "loc" and self.in_url:
            self.in_loc = True

    def characters(self, content):
        if self.in_loc:
            self.url += content

    def endElement(self, name):
        if name == "url":
            print(self.url.strip())
            self.url = ""
            self.in_url = False
        elif name == "loc":
            self.in_loc = False

url = "https://brightdata.com/post-sitemap.xml"

response = requests.get(url)
if response.status_code == 200:

    xml_content = BytesIO(response.content)
    
    content_handler = MyContentHandler()
    parser = xml.sax.make_parser()
    parser.setContentHandler(content_handler)
    parser.parse(xml_content)
else:
    print("Failed to retrieve XML file from the URL.")
```

输出如下所示：

```
https://brightdata.com/case-studies/powerdrop-case-study
https://brightdata.com/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.com/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.com/case-studies/the-seo-transformation
https://brightdata.com/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.com/case-studies/highly-targeted-influencer-marketing
https://brightdata.com/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.com/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.com/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.com/case-studies/data-intensive-analytical-solutions
https://brightdata.com/case-studies/canopy-advantage-solutions
https://brightdata.com/case-studies/seamless-digital-automations
```

与将整个文件加载到内存中的其他解析器不同，SAX 会以增量方式处理文件，从而节省内存并提高性能。不过，它需要编写更多代码来处理每个数据片段，而且无法返回之前的数据部分进行后续分析。

SAX 非常适合高效扫描大型 XML 文件（例如日志文件），以提取特定信息（例如错误消息）。不过，如果你的分析需要研究不同数据片段之间的关系，SAX 可能不是最佳选择。

## 总结

Python 提供了多种灵活的库，可简化 XML 解析。不过，使用 requests 库访问在线文件时，你可能会遇到配额和限流问题。[Bright Data](https://www.bright.cn/) 提供可靠的代理解决方案，帮助绕过这些限制。 

如果你不想进行抓取和解析，不妨免费查看我们的[数据集市场](https://www.bright.cn/products/datasets)！
