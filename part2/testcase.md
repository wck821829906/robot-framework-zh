# 建立测试用例
这一节介绍的所有的testcase的语法，接下来几节会讨论如何在[test Suite]()中组织[testcase文件]()和[测试套件目录]()

* [2.2.1 测试用例语法](#2-2-1)
    - [基本语法](#2-2-1-1)
    - [测试用例表格中的设定](#2-2-1-2)
    - [设置表格中跟测试用例相关的设定](#2-2-1-3)
* [2.2.2 使用参数](#2-2-2)
    - [强制性参数](#2-2-2-1)
    - [默认值](#2-2-2-2)
    - [不定长参数](#2-2-2-3)
    - [命名参数](#2-2-2-4)
    - [嵌入到keyword名字中的参数](#2-2-2-)
* [2.2.3 失败](#2-2-3-)
    - [测试用例何时会失败](#2-2-3-1)
    - [错误信息](#2-2-3-2)
* [2.2.4 测试用例名字和说明](#2-2-4)
* [2.2.5 给测试用例打标签](#2-2-5)
    - [保留的标签](#2-2-5-1)
* [2.2.6 setup和teardown](#2-2-6)
* [2.2.7 测试模板](#2-2-7)
    - [基本用法](#2-2-7-1)
    - [内嵌关键字的模板](#2-2-7-2)
    - [for循环的模板](#2-2-7-3)
* [2.2.8 不同的测试用例风格](#2-2-8)
    - [关键字驱动](#2-2-8-1)
    - [数据驱动模板](#2-2-8-2)
    - [行为驱动](#2-2-8-3)

<h2 id="2-2-1">2.2.1 测试用例语法</h2>

<h3 id="2-2-1-1">基本语法</h3>

测试用例是有testcase表格中的keyword构成的，[keyword可以用[测试库]()或者资源文件中导入，也可以在测试用例文件本身的通过[keyword表格]()中创建。

测试用例表的第一列是测试用例的名字，一个测试用例从表名开始一直到下一个表名或者到测试用例表的末尾。如果第一个测试和表头直接有东西，那这是一个错误

第二列通常是keyword的名字，有一个例外的情况：[从keyword的返回值中设置变量]()的时候，第二列或者可能的后续几列都是变量名，最后是一个keyword的名字。这两种情况，keyword名后边可能会有参数列。

```
*** Test Cases ***
Valid Login
    Open Login Page
    Input Username    demo
    Input Password    mode
    Submit Credentials
    Welcome Page Should Be Open

Setting Variables
    Do Something    first argument    second argument
    ${value} =    Get Some Value
    Should Be Equal    ${value}    Expected value
```

<h3 id="2-2-1-2">测试用例表中的设定</h3>

测试用例也可以有他们自己的设定。这些设置的名字总是在第二列，就是正常keyword名字的位置，他们的值紧跟其后。他们的名字用方括号括起来，好跟keyword区分。下边列出一些settings，稍后介绍他们。

*[Documentation]*<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用来指定[测试用例说明]()

*[Tags]*<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用来[给测试用例打标签]()

*[Setup], [Teardown]*<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;指定测试的setup和teardown

*[template]*<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;指定要使用的[模板关键字]().这个测试的内容全部是这个关键字的参数

*[timeout]*<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用来设置[测试用例超时时间]()[超时]()会在今后单独讨论

有settings的testcase举例：

```
*** Test Cases ***
Test With Settings
    [Documentation]    Another dummy test
    [Tags]    dummy    owner-johndoe
    Log    Hello, world!
```

<h3 id="2-2-1-3">settings表中与testcase相关的settings</h3>

settings表可以有这几种跟testcase相关的setting。这些setting是主要是刚刚列举的testcase setting的默认值。

*Force Tags, Default Tags*<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tags的强制默认值

*Test Setup, Test Teardown**<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
test setup and teardown的默认值

*Test Template**<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认使用的template keyword.

*Test Timeout**<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;test case timeout的默认值. Timeouts 会单独拿出一节来讨论.

<h2 id="2-2-2">使用参数</h2>

前面的例子已经展示了使用不同参数的keyword，这一节我们将更下彻底的讨论这个重要的特性。我们将讨论如何实现使用不同参数的自定义keyword和库keyword。

keyword可以不接收或接收多个参数，有的参数还可以有默认值。接收什么样的参数取决于keyword是怎么实现的。查看这些信息最好在keyword的说明中，这一节的例子中的说明全部是有[Libdoc]()工具生成的，用javadoc这样的通用文档工具也可以生成相同的信息。

<h4 id="2-2-2-1">强制性参数</h4>

大多数keyword都有的几个参数必须要给定的参数，在他们的说明文档中，参数名通常用逗号隔开，比如`first, second, third`。强制性参数的参数名并不重要，除了要说明他们的作用，重要的是个数要与说明文档中的完全一致。参数不足或者给太多都会导致错误。

下班的测试用例用了【OperatingSystem]()中的*Create Directory*和*Copy File* keyword，他们的参数分别是`path`和`source`，`destination`，这意味着他们分别有一个和两个参数。最后一个keyword，*No Operation*，不需要参数

```
*** Test Cases ***
Example
    Create Directory    ${TEMPDIR}/stuff
    Copy File    ${CURDIR}/file.txt    ${TEMPDIR}/stuff
    No Operation
```

<h4 id="2-2-1-2">默认值</h4>

参数经常会有默认值，当然也可以不给。在documentation中，默认值用等号跟参数名分隔，比如`name=default value`，但是有些用Java写的的keyword可能有多个不同参数的实现。一个keyword可以全部参数都有默认值，但是有默认值的参数后边不能再有强制性参数。

下边的例子说明了如何使用默认值，keyword *Create File*有这几个参数 `path, content=, encoding=UTF-8`。这个keyword不给参数或者给多于3个参数都会失败。

```
*** Test Cases ***
Example
    Create File    ${TEMPDIR}/empty.txt
    Create File    ${TEMPDIR}/utf-8.txt         Hyvä esimerkki
    Create File    ${TEMPDIR}/iso-8859-1.txt    Hyvä esimerkki    ISO-8859-1
```

<h4 id="2-2-1-3">不定长参数</h4>

有可能一个keyword需要接受任意数量的参数，这些所谓的*varargs*可以是强制性参数和默认值参数联接起来，但是总放在强制性参数和默认值参数后边。文档中，他们参数名前面有一个星号，比如`*varargs`

比如，[OperatingSystem]()中的*Create Directory*和*Copy File* keyword，分别有参数 `*paths` 和 `base, *parts`,前者可以传入任意数量的参数，后者则至少传一个。

```
*** Test Cases ***
Example
    Remove Files    ${TEMPDIR}/f1.txt    ${TEMPDIR}/f2.txt    ${TEMPDIR}/f3.txt
    @{paths} =    Join Paths    ${TEMPDIR}    f1.txt    f2.txt    f3.txt    f4.txt
```









