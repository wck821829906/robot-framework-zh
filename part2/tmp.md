# 2.1 测试数据语法

本节整体介绍了Robot框架测试数据的语法，接下来的几节将会具体介绍如何真正地创建测试用例、测试套件等。

* [2.1.1 文件和目录](#2-1-1)
* [2.1.2 支持的文件格式](#2-1-2)
    + [HTML](#2-1-2-1)
    + [TSV](#2-1-2-2)
    + [普通文本](#2-1-2-3)
    + [reStructuredText](#2-1-2-4)
* [2.1.3 测试数据表格](#2-1-3)
* [2.1.4 解析数据的规则](#2-1-4)
    * [忽略的数据](#2-1-4-1)
    * [空白控制](#2-1-4-2)
    * [转义](#2-1-4-3)
    * [将测试数据分成多列](#2-1-4-4)


<h3 id="2-1-1">文件和目录</h3>
测试数据按照如下规则形成层级结构：
+ 测试用例存放在[testcase文件]()中
+ 一个testcase文件中的所有测试用例自动构成一个[测试套件]()
+ 一个包含多个testcase文件的目录形成更高级别的测试套件。这样的[测试套件目录]()中，由多个testcase文件构成的测试套件就有了子套件
+ 一个测试套件的目录也可以有多个子目录，这种层级结构可以根据需要不断嵌套。
+ 多个测试套件目录可以有一个专门的[初始化文件]()

除此之外，还有：
+ 包含最低级别的keyword的[测试库]()
+ 拥有[变量]()和更高级别的[用户keyword]()的[资源文件]()
+ 比资源文件定义变量更灵活的[变量文件]()

<h3 id="2-1-2"> 支持的文件格式</h3>
Robot框架的测试数据使用表格的形式来定义，可以使用超文本标记语言(HTML)、制表符分割值(TSV)、普通文本或者reStructuredText(reST)格式的任意一种。接下来几节我们将会介绍这几种格式的优缺点，使用哪种格式取决于使用背景，但是如果没有特殊需求我们还是推荐使用普通文本格式。

Robot框架根据文件拓展名来选择相应的解析器。拓展名的大小写敏感的，可以识别的拓展名有这些：HTML支持.html、.htm、.xhtml，TSV支持.tsv，普通文本支持.txt和特殊的.robot，reStructuredText支持.rst和.rest。

<h4 id="2-1-2-1">HTML</h4>
<h4 id="2-1-2-2">TSV</h4>
<h4 id="2-1-2-3">普通文本</h4>

<h3>普通文本</h3>
普通文本易于编辑和版本控制，因此是robot框架使用最多的格式。

普通文本跟TSV在技术上很相似，只不过TSV用tab分割不同列，而在普通文本中你可以用两个或者多个空格来分割，也可以用两边有空格的管道符(|)

跟TSV一样，测试资料表的表头前必须有至少一个星号，表头中其他的星号和空格是可以忽略的，比如*** Settings *** and *Settings 是一样的效果。第一个表之前的所有内容都会被忽略，这个是还是跟TSV一样。

在普通问题中，tab会自动转换成2个空格，这样只用单个tab作为分隔符就跟TSV一样了。需要注意的是，在普通文本中，连续的多个tab被看做是一个分隔符，但是在TSV中，每个tab都是一个分隔符。

<h4>空格分隔的格式</h4>
可以只用几个空格做分隔，也可以用很多个，但至少要两个空格，这样能更好的对齐。因为TSV的对齐是不可控制的，所以能在编辑器里修改TSV格式(的对齐)是一个很不错的事情。

```
*** Settings ***
Library       OperatingSystem

*** Variables ***
${MESSAGE}    Hello, world!

*** Test Cases ***
My Test
    [Documentation]    Example test
    Log    ${MESSAGE}
    My Keyword    /tmp

Another Test
    Should Be Equal    ${MESSAGE}    Hello, world!

*** Keywords ***
My Keyword
    [Arguments]    ${path}
    Directory Should Exist    ${path}
```

因为空格被用来做分隔了，所以所有空格必须用```${EMPTY}```变量或者反斜线(\)[转义]()表示。跟其他测试资料中的[空白处理]()没什么区别，因为前导、尾部、连续的空格都需要被转义表示。
> 提示： 推荐用4个空格来分隔关键字和参数

<h4>管道和空格</h4>
用空格分隔的最大问题是很难从参数中找出关键字来，尤其是遇到一个关键字有很多参数的时候，或者有的参数是空格的时候。这时候，管道和空格分隔就显得效果更好，因为更直观了。

```
| *Setting*  |     *Value*     |
| Library    | OperatingSystem |

| *Variable* |     *Value*     |
| ${MESSAGE} | Hello, world!   |

| *Test Case*  | *Action*        | *Argument*   |
| My Test      | [Documentation] | Example test |
|              | Log             | ${MESSAGE}   |
|              | My Keyword      | /tmp         |
| Another Test | Should Be Equal | ${MESSAGE}   | Hello, world!

| *Keyword*  |
| My Keyword | [Arguments]            | ${path}
|            | Directory Should Exist | ${path}
```
空格分隔和管道符+空格分隔可以同时混用，但是每一行只能用一种分隔方式。规定必须要有前导的管道符，但是结尾的管道符不是强制的。另外管道符两边都必须至少有一个空格(除非是行首和行尾的管道符)，但是没必要为了看起来更清晰明了然后刻意对齐管道符。

用管道符+空格就没必要用转义表示空格了(除了[行尾的空格]()）。只要一个很重要的事情，如果在实际的测试数据中真的要出现管道符，那就需要用反斜线转义(\|）


```
| *** Test Cases *** |                 |                 |                      |
| Escaping Pipe      | ${file count} = | Execute Command | ls -1 *.txt \| wc -l |
|                    | Should Be Equal | ${file count}   | 42  
```

<h4>编辑和编码</h4>
普通文本最大的优点就是可以非常方便用普通的文本编辑器编辑。许多编辑器或者IDE(至少是Eclipse, Emacs, Vim, 还有 TextMate)也有插件支持高亮显示robot框架的语法和关键字自动补全等。[RIDE]()也支持普通文本格式。

<h4>可以识别的格式</h4>
从robot2.7.6开始，除了常规的.txt拓展名，还可以用.robot拓展名的文件来存测试资料。新的拓展名更容易与普通文本区分，以表示这是一个测试文件。


<h4 id="2-1-2-4">reStructuredText</h4>
<h3 id="2-1-3">测试资料表</h3>
测试资料由以下四种表构成。他们由表的一个格里的内容来区分，可以识别的表名有 ```Settings```, ```Variables```, ```Test Cases```和```Keywords```，这些名字是大小写敏感的并且也可以是单数形式的，比如```Setting``` 和```Test Case``` 
<h5></h5>
<h4></h4>