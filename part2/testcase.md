# 建立测试用例
这一节介绍的所有的testcase的语法，接下来几节会讨论如何在[test Suite]()中组织[testcase文件]()和[测试套件目录]()

* [2.2.1 测试用例语法](#2-2-1)
    - [基本语法](#2-2-1-1)
    - [测试用例表格中的设定](#2-2-1-2)
    - [设置表格中跟测试用例相关的设定](#2-2-1-3)
* [2.2.2 使用参数](#2-2-2)
    - [强制性参数](#2-2-2-)
    - [默认值](#2-2-2-)
    - [不定长参数](#2-2-2-)
    - [命名参数](#2-2-2-)
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
