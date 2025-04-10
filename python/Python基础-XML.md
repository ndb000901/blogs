# Python基础-XML

## 1 **解析 XML**

Python 提供了 `xml.etree.ElementTree` 模块来解析 XML 数据。这个模块允许我们读取、修改和写入 XML 文件。

### 1.1 **解析字符串 XML**

通过 `ElementTree` 模块的 `fromstring()` 方法，我们可以直接从字符串解析 XML 数据。
```python
import xml.etree.ElementTree as ET

xml_data = '''<?xml version="1.0" encoding="UTF-8"?>
<library>
    <book id="1">
        <title>Python Programming</title>
        <author>John Doe</author>
        <price>29.99</price>
    </book>
    <book id="2">
        <title>Learning XML</title>
        <author>Jane Smith</author>
        <price>19.99</price>
    </book>
</library>'''

root = ET.fromstring(xml_data)

# 遍历并输出根元素的标签
print(root.tag)  # 输出：library

# 获取子元素
for book in root.findall('book'):
    title = book.find('title').text
    author = book.find('author').text
    price = book.find('price').text
    print(f"Title: {title}, Author: {author}, Price: {price}")
```
输出：
```
library
Title: Python Programming, Author: John Doe, Price: 29.99
Title: Learning XML, Author: Jane Smith, Price: 19.99
```

### 1.2 **解析 XML 文件**

如果 XML 数据存储在文件中，我们可以使用 `ElementTree` 的 `parse()` 方法来加载文件内容。
```python
import xml.etree.ElementTree as ET

tree = ET.parse('example.xml')
root = tree.getroot()

# 获取根元素
print(root.tag)
```

## 2 **修改 XML**

### 2.1 **修改元素的文本内容**
```python
import xml.etree.ElementTree as ET

tree = ET.parse('example.xml')
root = tree.getroot()

# 修改元素的文本内容
for book in root.findall('book'):
    title = book.find('title')
    if title.text == 'Python Programming':
        title.text = 'Advanced Python Programming'

# 保存修改后的 XML 文件
tree.write('modified_example.xml')
```

### 2.2 **修改元素的属性**
```python
import xml.etree.ElementTree as ET

tree = ET.parse('example.xml')
root = tree.getroot()

# 修改元素的属性
for book in root.findall('book'):
    book.set('id', '999')

# 保存修改后的 XML 文件
tree.write('modified_example.xml')
```

## 3 **创建新的 XML**

还可以用 `ElementTree` 模块创建新的 XML 文件：
```python
import xml.etree.ElementTree as ET

# 创建根元素
root = ET.Element("library")

# 创建子元素
book1 = ET.SubElement(root, "book", id="1")
ET.SubElement(book1, "title").text = "Python for Beginners"
ET.SubElement(book1, "author").text = "Alice"
ET.SubElement(book1, "price").text = "25.99"

book2 = ET.SubElement(root, "book", id="2")
ET.SubElement(book2, "title").text = "Mastering XML"
ET.SubElement(book2, "author").text = "Bob"
ET.SubElement(book2, "price").text = "35.99"

# 生成 XML 文件
tree = ET.ElementTree(root)
tree.write("new_library.xml")
```

生成的 `new_library.xml` 文件内容如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<library>
    <book id="1">
        <title>Python for Beginners</title>
        <author>Alice</author>
        <price>25.99</price>
    </book>
    <book id="2">
        <title>Mastering XML</title>
        <author>Bob</author>
        <price>35.99</price>
    </book>
</library>
```

## 4 **XPath 查询**

XPath 是用于在 XML 文档中查找信息的一种语言。`xml.etree.ElementTree` 模块提供了 `find()` 和 `findall()` 方法来支持 XPath 查询。

```python
import xml.etree.ElementTree as ET

tree = ET.parse('example.xml')
root = tree.getroot()

# 使用 XPath 查找元素
book = root.find(".//book[title='Python Programming']")
print(book.find('author').text)  # 输出：John Doe
```

## 5 **命名空间支持**

XML 文件中可能会有命名空间，在处理这类文件时，需要显式指定命名空间。`ElementTree` 模块提供了对命名空间的支持。
```python
import xml.etree.ElementTree as ET

xml_data = '''<ns:library xmlns:ns="http://example.com">
    <ns:book id="1">
        <ns:title>Python Programming</ns:title>
        <ns:author>John Doe</ns:author>
    </ns:book>
</ns:library>'''

namespaces = {'ns': 'http://example.com'}
root = ET.fromstring(xml_data)

# 使用命名空间查找元素
title = root.find('.//ns:title', namespaces).text
print(title)  # 输出：Python Programming
```

---

