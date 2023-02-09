---
layout: post
title: Python爬虫之Beautifulsoup模块的使用
author: "gebaocai"
header-style: text
lang: zh
tags: [Python, Beautifulsoup]
---

Beautifulsoup模块介绍
------
Beautiful Soup 是一个可以从HTML或XML文件中提取数据的Python库.它能够通过你喜欢的转换器实现惯用的文档导航,查找,修改文档的方式.Beautiful Soup会帮你节省数小时甚至数天的工作时间.你可能在寻找 Beautiful Soup3 的文档,Beautiful Soup 3 目前已经停止开发,官网推荐在现在的项目中使用Beautiful Soup 4, 移植到BS4

```
#安装 Beautiful Soup
pip install beautifulsoup4

#安装解析器
Beautiful Soup支持Python标准库中的HTML解析器,还支持一些第三方的解析器,其中一个是 lxml .根据操作系统不同,可以选择下列方法来安装lxml:

$ apt-get install Python-lxml

$ easy_install lxml

$ pip install lxml

另一个可供选择的解析器是纯Python实现的 html5lib , html5lib的解析方式与浏览器相同,可以选择下列方法来安装html5lib:

$ apt-get install Python-html5lib

$ easy_install html5lib

$ pip install html5lib
```

下表列出了主要的解析器,以及它们的优缺点,官网推荐使用lxml作为解析器,因为效率更高. 在Python2.7.3之前的版本和Python3中3.2.2之前的版本,必须安装lxml或html5lib, 因为那些Python版本的标准库中内置的HTML解析方法不够稳定.


|-----------------+---------+-------------+------------+------------|
|         解析器   |  使用方法   | 优势  | 劣势  |
|-----------------|:---------:|:-----------:|:----------:|
| Python标准库 |BeautifulSoup(markup, "html.parser") | Python的内置标准库执行速度适中文档容错能力强      | Python 2.7.3 or 3.2.2)前 的版本中文档容错能力差    |
| lxml HTML 解析器     |BeautifulSoup(markup, "lxml")| 速度快文档容错能力强 | 需要安装C语言库            |
| lxml XML 解析器      |BeautifulSoup(markup, ["lxml", "xml"])``BeautifulSoup(markup, "xml") | 速度快唯一支持XML的解析器 | 需要安装C语言库           |
| html5lib      |BeautifulSoup(markup, "html5lib")        | 最好的容错性以浏览器的方式解析文档生成HTML5格式的文档             | 速度慢不依赖外部扩展            |
|=================+============+=================+================|

注意：
```
注意: 使用bs4，必须先选择 “解析器”

from bs4 import BeautifulSoup

#  markup="": 传入解析文本
# BeautifulSoup('解析文本内容', '解析器')
# python自带的解析器 html.parser
# BeautifulSoup('<a>蔡徐坤打篮球真厉害，还会唱歌，跳舞，Rap!!!!</a>', 'html.parser')

# lxml: pip3 install lxml   第三方的解析库(推荐使用)
# soup = BeautifulSoup('<a>蔡徐坤打篮球真厉害，还会唱歌，跳舞，Rap!!!!</a>', 'lxml')
# print(soup)
# print(type(soup))
```

基本使用
------

```
from bs4 import BeautifulSoup

html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a id="tank" href="http://example.com/tank">蔡徐坤打篮球真厉害，还会唱歌，跳舞，Rap!!!!</a>
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;

and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""

#基本使用：容错处理,文档的容错能力指的是在html代码不完整的情况下,使用该模块可以识别该错误。使用BeautifulSoup解析上述代码,能够得到一个 BeautifulSoup 的对象,并能按照标准的缩进格式的结构输出
soup = BeautifulSoup(html_doc, 'lxml')
print(soup)
# html文本美化   了解
print(soup.prettify())

```

遍历文档树
------
遍历文档树：即直接通过标签名字选择，特点是选择速度快，但如果存在多个相同的标签则只返回第一个
```
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a id="cxk" href="http://example.com/cxk">蔡徐坤打篮球真厉害，还会唱歌，跳舞，Rap!!!!</a>
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;

and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html_doc, 'lxml')


# 基本用法
# 1、直接.标签名 （*******）
print(soup)
print(soup.head)
print(soup.a)  # 返回第一个a标签

# 2、获取标签的名称  了解
print(soup.p.name)

# 3、获取标签的属性 (*********)
print(soup.a.attrs)   # 第一个a标签的所有属性
print(type(soup.a.attrs))  # 返回所有标签属性的类型 dict
print(soup.a.attrs.get('id')) # dict类型可以get取值
print(soup.a.attrs.get('href'))

# 4、获取标签的文本内容
# 获取第一个a标签中的文本内容
print(soup.a.text)

# 5、嵌套选择
# xml
'''
<info>  # 父标签
    <name>cxk</name>
    <gf>刘亦菲</gf>
</info>
'''
print(soup.prettify())
print(type(soup.html))  # 可以嵌套选择。
print(type(soup.html.head.title))
print(soup.html.head.title)

# 6、子节点、子孙节点
print(soup)  # 注意: 换行符也算一个 节点
print(soup.body.contents)  # body下所有子节点，返回的是一个列表
print(len(soup.body.contents))  # body下所有子节点

# 优先使用children
print(soup.body.children)  # 得到一个迭代器,包含p下所有子节点
print(list(soup.body.children))  # 得到一个迭代器,包含p下所有子节点
for children in soup.body.children:
    print(children)
    
# 7、父节点、祖先节点
# print(soup)
# print(soup.a.parent)  # 获取a标签的父节点
print(soup.a.parents)  # 找到a标签所有的祖先节点，父亲的父亲，父亲的父亲的父亲...  它也是一个迭代器
print(list(soup.a.parents))  # 找到a标签所有的祖先节点，父亲的父亲，父亲的父亲的父亲...
print(len(list(soup.a.parents)))  # 4

# 8、兄弟节点
# print(soup)
# print('=====>')
print(soup.a.next_sibling)  # 下一个兄弟
print(soup.a.previous_sibling)  # 上一个兄弟


print(list(soup.a.next_siblings)) # 下面的兄弟们(下面的所有兄弟)=>生成器对象
print(len(list(soup.a.next_siblings)))  # 7 内容也算是
print(soup.a.previous_siblings)  # 上面的兄弟们=>生成器对象
print(list(soup.a.previous_siblings))  # 上面的兄弟们=>生成器对象

```

搜索文档树之五种过滤器
------
#### 标签查找与属性查找:
```
 - 根据标签查找属性:
        # 查找第一个标签
        - soup.find(
            name 属性匹配
            attrs 属性查找匹配
            text 文本匹配
        )
         - 查找第一个a标签
             # 根据name中的字符串，与attrs中class属性值匹配对应的第一个a标签
           soup.find(
                  name='a',
                  attrs={'class': '属性值'}
                )

        # 查找所有标签
        - soup.find_all()

```
#### find_all( name , attrs , recursive , text , **kwargs )
```
#2、find_all( name , attrs , recursive , text , **kwargs )
#2.1、name: 搜索name参数的值可以使任一类型的 过滤器 ,字符窜,正则表达式,列表,方法或是 True .
print(soup.find_all(name=re.compile('^t')))

#2.2、keyword: key=value的形式，value可以是过滤器：字符串 , 正则表达式 , 列表, True .
print(soup.find_all(id=re.compile('my')))
print(soup.find_all(href=re.compile('lacie'),id=re.compile('\d'))) #注意类要用class_
print(soup.find_all(id=True)) #查找有id属性的标签

# 有些tag属性在搜索不能使用,比如HTML5中的 data-* 属性:
data_soup = BeautifulSoup('<div data-foo="value">foo!</div>','lxml')
# data_soup.find_all(data-foo="value") #报错：SyntaxError: keyword can't be an expression
# 但是可以通过 find_all() 方法的 attrs 参数定义一个字典参数来搜索包含特殊属性的tag:
print(data_soup.find_all(attrs={"data-foo": "value"}))
# [<div data-foo="value">foo!</div>]

#2.3、按照类名查找，注意关键字是class_，class_=value,value可以是五种选择器之一
print(soup.find_all('a',class_='sister')) #查找类为sister的a标签
print(soup.find_all('a',class_='sister ssss')) #查找类为sister和sss的a标签，顺序错误也匹配不成功
print(soup.find_all(class_=re.compile('^sis'))) #查找类为sister的所有标签

#2.4、attrs
print(soup.find_all('p',attrs={'class':'story'}))

#2.5、text: 值可以是：字符，列表，True，正则
print(soup.find_all(text='Elsie'))
print(soup.find_all('a',text='Elsie'))

#2.6、limit参数:如果文档树很大那么搜索会很慢.如果我们不需要全部结果,可以使用 limit 参数限制返回结果的数量.效果与SQL中的limit关键字类似,当搜索到的结果数量达到 limit 的限制时,就停止搜索返回结果
print(soup.find_all('a',limit=2))

#2.7、recursive:调用tag的 find_all() 方法时,Beautiful Soup会检索当前tag的所有子孙节点,如果只想搜索tag的直接子节点,可以使用参数 recursive=False .
print(soup.html.find_all('a'))
print(soup.html.find_all('a',recursive=False))

'''
像调用 find_all() 一样调用tag
find_all() 几乎是Beautiful Soup中最常用的搜索方法,所以我们定义了它的简写方法. BeautifulSoup 对象和 tag 对象可以被当作一个方法来使用,这个方法的执行结果与调用这个对象的 find_all() 方法相同,下面两行代码是等价的:
soup.find_all("a")
soup("a")
这两行代码也是等价的:
soup.title.find_all(text=True)
soup.title(text=True)
'''

```

#### **find( name , attrs , recursive , text , **kwargs )
```
#3、find( name , attrs , recursive , text , **kwargs )
find_all() 方法将返回文档中符合条件的所有tag,尽管有时候我们只想得到一个结果.比如文档中只有一个<body>标签,那么使用 find_all() 方法来查找<body>标签就不太合适, 使用 find_all 方法并设置 limit=1 参数不如直接使用 find() 方法.下面两行代码是等价的:

soup.find_all('title', limit=1)
# [<title>The Dormouse's story</title>]
soup.find('title')
# <title>The Dormouse's story</title>

唯一的区别是 find_all() 方法的返回结果是值包含一个元素的列表,而 find() 方法直接返回结果.
find_all() 方法没有找到目标是返回空列表, find() 方法找不到目标时,返回 None .
print(soup.find("nosuchtag"))
# None

soup.head.title 是 tag的名字 方法的简写.这个简写的原理就是多次调用当前tag的 find() 方法:

soup.head.title
# <title>The Dormouse's story</title>
soup.find("head").find("title")
# <title>The Dormouse's story</title>

```

五种过滤器
------

```
- 字符串过滤器   字符串全局匹配
            - name = 'p'
            name 属性匹配
            attrs 属性查找匹配
            text 文本匹配

        - 正则过滤器

            re模块匹配
            - name = re.compile()
            name 属性匹配
            attrs 属性查找匹配
            text 文本匹配

        - 列表过滤器
            列表内的数据匹配

            - name = ['tank', 100]
            name 属性匹配
            attrs 属性查找匹配
            text 文本匹配

        - bool过滤器
            True匹配
            - name = True
            name 属性匹配
            attrs 属性查找匹配
            text 文本匹配

        - 方法过滤器
            用于一些要的属性以及不需要的属性查找。
            - name = func()
            name 属性匹配
            attrs 属性查找匹配
            text 文本匹配

    属性:
        - class_
        - id

```

```
from bs4 import BeautifulSoup

html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a id="cxk" href="http://example.com/cxk">蔡徐坤打篮球真厉害，还会唱歌，跳舞，Rap!!!!</a>
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;

and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""

soup = BeautifulSoup(html_doc, 'lxml')
```

#### 字符串过滤器
```
# 根据name查找节点标签
a_tag = soup.find(name='a')  # 查找的是第一个a标签
print(a_tag)

# 根据attrs属性查找节点标签
a_tag = soup.find(attrs={"id": "cxk"}) # id=cxk
print(a_tag)

# 根据文本找节点
a_tag = soup.find(text="The Dormouse's story")
print(a_tag)

# name与attrs配合使用
res = soup.find(name='a', attrs={'id': 'link3'})
res = soup.find(name='a', attrs={'id': 'link3'}, text='Tillie')
print(res)

```

#### 正则过滤器
```
import re
# # print(soup)
# # 查找class属性值中包含 s的节点  # 选择第一个
res = soup.find(attrs={'class': re.compile('s')})
# # 查找标签名字中包含a的节点  # 选择第一个
res = soup.find(name=re.compile('a'))
print(res)

```

#### 列表过滤器
```
# print(soup)
# 获取第一个a标签 或 者p标签的节点
res = soup.find(name=['a', 'p'])
print(res)

# 获取所有的a标签与p标签的节点
res = soup.find_all(name=['a', 'p'])
print(res)

```

#### bool过滤器（了解）
```
print(soup.find_all(True))  # 获取所有为真的标签
for tag in soup.find_all(True):
    print(tag.name)
```

#### 方法过滤器
```
# 函数用于判断节点中有class没有id的节点，并将该节点返回
def has_class_but_no_id(tag):
    return tag.has_attr('class') and not tag.has_attr('id')

print(soup.find_all(has_class_but_no_id))

```

#### CSS过滤器
```
print(soup.select('#cxk'))  # 查找id为cxk的节点
print(soup.select('.story')) # 查找类为story的节点
```

总结
------
```
# 总结:
#1、推荐使用lxml解析库
#2、讲了三种选择器:标签选择器,find与find_all，css选择器
    1、标签选择器筛选功能弱,但是速度快
    2、建议使用find,find_all查询匹配单个结果或者多个结果
    3、如果对css选择器非常熟悉建议使用select
#3、记住常用的获取属性attrs和文本值get_text()的方法
```