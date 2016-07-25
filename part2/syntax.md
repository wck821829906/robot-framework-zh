# 2.1 测试资料语法

本节整体介绍了Robot框架测试资料的语法，接下来的几节将会具体介绍如何真正地创建测试用例、测试套件等。

* [2.1.1 文件和目录](#2-1-1)
* [2.1.2 支持的文件格式](#2-1-2)
    + [HTML](#2-1-2-1)
    + [TSV](#2-1-2-2)
    + [普通文本](#2-1-2-3)
    + [reStructuredText](#2-1-2-4)
* [2.1.3 测试资料表格](#2-1-3)
* [2.1.4 解析资料的规则](#2-1-4)
    * [忽略的资料](#2-1-4-1)
    * [空白控制](#2-1-4-2)
    * [转义](#2-1-4-3)
    * [将测试资料分成多列](#2-1-4-4)

<h2 id="2-1-1">2.1.1 文件和目录</h2>

测试资料按照如下规则形成层级结构：

+ 测试用例存放在[testcase文件]()中
+ 一个testcase文件中的所有测试用例自动构成一个[测试套件]()
+ 一个包含多个testcase文件的目录形成更高级别的测试套件。这样的[测试套件目录]()中，由多个testcase文件构成的测试套件就有了子套件
+ 一个测试套件的目录也可以有多个子目录，这种层级结构可以根据需要不断嵌套。
+ 多个测试套件目录可以有一个专门的[初始化文件]()

除此之外，还有：

+ 包含最低级别的keyword的[测试库]()
+ 拥有[变量]()和更高级别的[用户keyword]()的[资源文件]()
+ 比资源文件定义变量更灵活的[变量文件]()

<h2 id="2-1-2">2.1.2 支持的文件格式</h2>

Robot框架的测试资料使用表格的形式来定义，可以使用超文本标记语言(HTML)、制表符分割值(TSV)、普通文本或者reStructuredText(reST)格式的任意一种。接下来几节我们将会介绍这几种格式的优缺点，使用哪种格式取决于使用背景，但是如果没有特殊需求我们还是推荐使用普通文本格式。

Robot框架根据文件拓展名来选择相应的解析器。拓展名的大小写敏感的，可以识别的拓展名有这些：HTML支持.html、.htm、.xhtml，TSV支持.tsv，普通文本支持.txt和特殊的.robot，reStructuredText支持.rst和.rest。

<h3 id="2-1-2-1">HTML</h3>

<h3 id="2-1-2-2">TSV</h3>

<h3 id="2-1-2-3">普通文本</h3>

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

因为空格被用来做分隔了，所以所有空格必须用`${EMPTY}`变量或者反斜线(\)[转义]()表示。跟其他测试资料中的[空白处理]()没什么区别，因为前导、尾部、连续的空格都需要被转义表示。

>###### 提示 
推荐用4个空格来分隔关键字和参数

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

用管道符+空格就没必要用转义表示空格了(除了[行尾的空格]()）。只要一个很重要的事情，如果在实际的测试资料中真的要出现管道符，那就需要用反斜线转义(\|）

```
| *** Test Cases *** |                 |                 |                     |
| Escaping Pipe      | ${file count} = | Execute Command | ls -1 *.txt \| wc -l |
|                    | Should Be Equal | ${file count}   | 42  
```

<h4>编辑和编码</h4>

普通文本最大的优点就是可以非常方便用普通的文本编辑器编辑。许多编辑器或者IDE(至少是Eclipse, Emacs, Vim, 还有 TextMate)也有插件支持高亮显示robot框架的语法和关键字自动补全等。[RIDE]()也支持普通文本格式。

<h4>可以识别的格式</h4>
从robot2.7.6开始，除了常规的.txt拓展名，还可以用.robot拓展名的文件来存测试资料。新的拓展名更容易与普通文本区分，以表示这是一个测试文件。


<h4 id="2-1-2-4">reStructuredText</h4>

<h2 id="2-1-3">测试资料表</h2>

测试资料由以下四种表构成。他们由表的一个格里的内容来区分，可以识别的表名有 ```Settings```, ```Variables```, ```Test Cases```和```Keywords```，这些名字是大小写敏感的并且也可以是单数形式的，比如```Setting``` 和```Test Case``` 

不同的测试数据资料表格

| 表格    |  用处 |
|---------|------|
|Settings | 1) 导入[测试库]()、[资源文件]()、[变量文件]() 2) 定义[测试用例]()和[测试套件]()的元数据|
|Variables| 定义测试资料内所有的[变量]()|
|TestCases|利用keyword[构建testcase]()|
|Keywords| 利用低级的keyword构建用户自定义keyword|

<h2 id="2-1-4">解析资料的规则</h2>

<h4 id="2-1-4-1">忽略的数据</h4>

Robot 框架在解析测试资料的生活，会忽略：

* 所有第一格里不是[合法表名]()的表格
* 表格内第一行中除第一列以外的其他列
* 第一张表前面的内容。如果格式允许两个表格之间有内容，那么这些内容也是被忽略
* 两张表之间的空行。这些空行往往是为了代码更可读
* 一行的最后一个空列，除非是这个是空列被转移表示
* 不用来转义的反斜杠
* 每一格中如果第一个字符是井号，那么井号(#)后的所有字符都会被忽略。这意味着，#可以用来做注释
* 在HTML或reST中的表示格式的内容

Robot框架忽略的内容，不会在报告中出现，并且大多数使用robot框架的工具也会这么做。想要在Robot的输出报告中出现一些信息，可以把他们放到documentation或者其他元数据中，或者用Builtin里的keyword Log或者Comment。


<h4 id="2-1-4-2">处理空白</h4>

Robot框架处理空白的方式跟HTML代码中处理空白的方式一样：

* 回车、换行、制表符都被转换成空格
* 每个子里前导和尾部的空白会被忽略
* 多个连续空格会被合并成一个

不换行空格会被替换成正常空格，如果不这样做，可能偶尔会出现很难调试的错误。

如果一定需要前置、尾部、连续的空格，那就[必须转义表示]()。换行、回车、制表符和不换行空格可以分别用[转义序列]()`\n`、`\r`、`\t`和`\xA0`表示

<h4 id="2-1-4-3">转义</h4>

Robot框架使用的转义字符是反斜杠`\`，还有一些[内置变量]()`${EMPTY}`和`${SPACE}`经作为转义字符使用。接下来会介绍几种不同的转义方法。

<h5>转义特殊字符</h5>

反斜杠符可以转义特殊字符，让这些字符表示原来的含义。

|   字符  |  含义   | 例子 |
|---------|--------|-----|
|  `\$` |  美元符，不用来表示[变量]() |  `\${notvar}`   |
|  `\@` |   at符， 不用来表示[列表变量]()|  `\@{notvar}`   |
|  `\%` |   百分号，不用来表示[环境变量]() |  `\%{notvar}`   |
|  `\#` |   井号，不用来表示注释 |  `\# not comment`   |
|  `\=` |   等号，不用于命名参数的语法 |  `not\=named`   |
|  <code>\&#124;</code> |   管道符，不能作为[管道符分隔]各列 |  <code>&#124; Run &#124; ps \&#124; grep xxx &#124;</code>  | 
|  `\\` |   反斜杠，不用于转义 |  `c:\\temp, \\${var}`   |

<h5>形成转义序列</h5>

反斜杠符还能表示转义序列，否则这些字符很难或者不可能在测试资料中出现。

形成转义序列

|   序列  |  含义   | 例子 |
|---------|--------|-----|
|   `\n`    | 换行符  |  `first line\n2nd line` |
|   `\r`    | 回车符  |  `text\rmore text` |
|   `\t`    | 制表符  |  `text\tmore text` |
|  `\xhh`    |  16进制`hh`表示的字符  |   `null byte: \x00, ä: \xE4` |
|  `\uhhhh`  |  16进制`hhhh`表示的字符  | `snowman: \u2603` |
| `\Uhhhhhhhh`  |  16进制`hhhhhhhh`表示的字符  |   `love hotel: \U0001f3e9` |

>###### 注意 
所有出现的字符串包括`\x02`都是Unicode，如果有需要的话必须精确的转换成byte string。这是可以做到的，比如使用[BuiltIn]()里的keyword Convert To Bytes 或 [String]()里的Encode String To Bytes，也可以在python代码里写成`str(value)`或者`value.encode('UTF-8')`

<p></p>
>###### 注意
>如果在使用`\x`、`\u`和`\U`转义的过程中出现了不合法的16进制数值，那最后的结果是就是不好包括反斜杠符的值，比如`\xAX`(不合法)会变成`xAX`，`\U00110000`(数值太大)会变成`U00110000`。但是，这个特性可能以后会变

<p></p>
>###### 注意 
如果需要使用操作系统相关的行终结符(Windows上是`\r\n`，其他系统是`\n`)可以使用[内建变量]()`{\n}`

<p></p>
>###### 注意 
`\n`后边的没有转义的空格会被忽略掉。这意味着，`two lines\nhere`跟`two lines\n here`是一样的。这样做的动机是为了使用HTML格式的时候，即使里头有换行符仍能包裹起来。其他格式也有相同的逻辑。有一个例外，在[拓展的变量语法]()中空白符不会被忽略。

<p></p>
>###### 注意 
`\x`、`\u`和`\U`能够转义序列是Robot2.8.2以后才有的新特性

<h5>避免忽略空白单元格</h5>

如果有必要保留空白单元格，比如作为一个keyword的参数或者其他原因，就需要保留避免空白单元格被[忽略]()。所有格式中的行尾的空白单元格必须要转义表示，在[空格分隔的格式]()里所有的空值都需要转义

空单元格可以用反斜杠转义或者使用内置变量`${EMPTY}`。后者比较推荐，因为它更容易理解。有一个例外的情况，在空格分隔的格式中，用于表示for循环中的缩进。这些情况在下面的HTML格式和普通文本格式中说明。

|Test Case  |   Action  |  Argument   |   Argument    |    Argument  |
|-----------|-----------|-------------|---------------|--------------|
|Using backslash | Do Something    |  first arg  | \         |      |
|Using ${EMPTY}  | Do Something    |  first arg  | ${EMPTY}  |      |
|Non-trailing empty | Do Something |            |  second arg | # No escaping needed in HTML|
|For loop    |   :FOR   |   ${var}     |   IN   |  @{VALUES}    |
|            |          |   Log |  ${var} | # No escaping needed here either|


```
*** Test Cases ***
Using backslash
    Do Something    first arg    \
Using ${EMPTY}
    Do Something    first arg    ${EMPTY}
Non-trailing empty
    Do Something    ${EMPTY}     second arg    # Escaping needed in space separated format
For loop
    :FOR    ${var}    IN    @{VALUES}
    \    Log    ${var}                         # Escaping needed here too
```

<h5>避免忽略空格</h5>

因为前导、尾部、连续的空格都会被忽略，当他们要作为keyword或者其他元素的参数时，就需要转义表示。跟避免忽略空单元格一样，既可以用反斜杠转义也可以用[内建变量]()`${SPACE}`。

转义空格的例子

|  Escaping with backslash  |  Escaping with ${SPACE} |    Notes  |
|-----|-----|-----|
| `\ leading space` |   `${SPACE}leading space`   |    |
| `trailing space \` | `trailing space${SPACE}`  | Backslash must be after the space. |
|       `\ \`       |  `${SPACE}`  |  Backslash needed on both sides.|
|`consecutive \ \ spaces` | `consecutive${SPACE * 3}spaces`  | Using [extended variable syntax.]() |

正如上边的例子所示，`${SPACE}`能够让测试资料更可读，在需要多个空格的时候，跟[拓展的变量语法]()结合会有很好的效果。

<h4 id="2-1-4-4">将测试资料分到多行</h4>

如果一行有太多列以至于印象可读性，可以试着用省略号(`...`)来表示连续上一行(的内容)。在TestCase和keyword表格中，省略号前面必须放至少一个空单元格。在Settings表格和Variable表格中，省略号可以直接放在某项settings或者variable的名字下边。在所有表格中，省略号前面的空单元格都会被忽略掉。

还有，值只有一个的settings（主要是documentation）可以将值分为好几列。在TestData被解析的时候，这些列会用空格连起来。从robot2.7开始，documentation和其他元数据如果是多列的值，会[用换行连接起来]()

没有分割的测试资料

| Settings | Value |  Value |  Value |  Value |  Value |  Value | 
|----------|-------|--------|--------|--------|--------|--------|
|Default Tags | tag-1 | tag-2 | tag-3 | tag-4 |  tag-5 |  tag-6 |

| Variable | Value |  Value |  Value |  Value |  Value |  Value | 
|----------|-------|--------|--------|--------|--------|--------|
| @{LIST}  |  this | list   |  has   | quite  |  many  |  items |

|Test Case  | Action | Argument |   Arg | Arg | Arg | Arg | Arg | Arg |
|-----------|--------|----------|-------|-----|----|-------|-----|-----|
|  Example  | [Documentation] | Documentation for this test case.\n This can get quite long...     | | | | | | |
|             | [Tags] | t-1| t-2| t-3 |t-4 |t-5 | | |
|        |   Do X  |  one | two | three |  four  |  five |  six|  
| |    ${var} =  |  Get X |  1  | 2 |  3  | 4  | 5  | 6 |

分割过的测试资料


| Settings | Value |  Value |  Value | 
|----------|-------|--------|--------|
|Default Tags | tag-1 | tag-2 | tag-3 | 
| ...         | tag-4 | tag-5 | tag-6 |

| Variable | Value |  Value |  Value |  
|----------|-------|--------|--------|
| @{LIST}  |  this | list   |  has   |
| ...      | quite | many   |  items |

|Test Case  | Action | Argument |   Arg | Arg | 
|-----------|--------|----------|-------|-----|
|  Example  | [Documentation] | Documentation |for this |test case.|
|           |  ...    |        This can get  |  quite  |  long... |   
|           | [Tags]  |   t-1   |   t-2   |   t-3   |
|           |  ...    |   t-4   |   t-5   |         |
|           |  Do X   |   one   |   two   |  three  |
|           |  ...    |   four  |   five  |   six   |  
|           | ${var} = |  Get X |    1    |   2     |  
|           |         |   ...   |    3    |   4     |
|           |         |   ...   |    5    |   6     |



