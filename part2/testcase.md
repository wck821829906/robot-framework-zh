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
    - [嵌入到keyword名字中的参数](#2-2-2-5)
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

下班的测试用例用了[OperatingSystem]()中的*Create Directory*和*Copy File* keyword，他们的参数分别是`path`和`source`，`destination`，这意味着他们分别有一个和两个参数。最后一个keyword，*No Operation*，不需要参数

```
*** Test Cases ***
Example
    Create Directory    ${TEMPDIR}/stuff
    Copy File    ${CURDIR}/file.txt    ${TEMPDIR}/stuff
    No Operation
```

<h4 id="2-2-2-2">默认值</h4>

参数经常会有默认值，当然也可以不给。在documentation中，默认值用等号跟参数名分隔，比如`name=default value`，但是有些用Java写的的keyword可能有多个不同参数的实现。一个keyword可以全部参数都有默认值，但是有默认值的参数后边不能再有强制性参数。

下边的例子说明了如何使用默认值，keyword *Create File*有这几个参数 `path, content=, encoding=UTF-8`。这个keyword不给参数或者给多于3个参数都会失败。

```
*** Test Cases ***
Example
    Create File    ${TEMPDIR}/empty.txt
    Create File    ${TEMPDIR}/utf-8.txt         Hyvä esimerkki
    Create File    ${TEMPDIR}/iso-8859-1.txt    Hyvä esimerkki    ISO-8859-1
```

<h4 id="2-2-2-3">不定长参数</h4>

有可能一个keyword需要接受任意数量的参数，这些所谓的*varargs*可以是强制性参数和默认值参数联接起来，但是总放在强制性参数和默认值参数后边。文档中，他们参数名前面有一个星号，比如`*varargs`

比如，[OperatingSystem]()中的*Create Directory*和*Copy File* keyword，分别有参数 `*paths` 和 `base, *parts`,前者可以传入任意数量的参数，后者则至少传一个。

```
*** Test Cases ***
Example
    Remove Files    ${TEMPDIR}/f1.txt    ${TEMPDIR}/f2.txt    ${TEMPDIR}/f3.txt
    @{paths} =    Join Paths    ${TEMPDIR}    f1.txt    f2.txt    f3.txt    f4.txt
```

<h4 id="2-2-2-4">命名参数</h4>

命名参数的引入使得参数默认值的用法更加灵活，同时也带来了变量值的含义。命名参数的机制跟python里的[关键字参数]()完全一致。

<h5>基本语法</h5>

可以在值的前面加上参数的名字然后传给keyword，比如`arg=value`。这种做法在你要传很多默认值参数的时候尤其有用，因为你可以只给一些参数取名字，而其余的取默认值。比如，一个keyword接收参数`arg1=a, arg2=b, arg3=c`，然后只传一个参数`arg3=override`就调用,那么`arg1`和`arg2`就是默认值，`arg3`的值为`override`。如果这听起来很复杂，但愿下边[命名参数的例子]()能容易懂一些。

命名参数是对大小写敏感的，同时也是对空格敏感。前者意味着，如果你有一个参数`arg`，你必须用`arg=value`，`Arg=value` 或者 `ARG=value` 都不对，后者意味着，等号前面不能有空格，等号`=`后边的空格可能会被当做是给定的值。

[自定义keyword]()中用到命名参数的时候，记得参数的名字不能用`${}`，比如说，参数是`${arg1}=first, ${arg2}=second`的keyword只能像这样用`arg2=override`

出现在命名参数之后的位置参数，比如`| Keyword | arg=value | positional |`是不对的。Robot框架从2.8开始，这回导致一个错误。而命名参数内部的相互顺序可以任意。

>######注意 
>Robot框架2.8之前的版本 不能对没有默认值的参数起名字

<h5>使用变量的命名参数</h5>

命名参数的名字和值都可以用[变量](),如果值是一个简单的[标量]，那就原样传给keyword，这使得用命名参数的时候，任何对象都可以像字符串一样被当做一个值来用。比如，调一个`arg=${object}`这样的keyword，会传一个`${object}`给keyword，而不是把它转换成一个字符串

如果一个命名参数的名字部分是变量，会先处理变量然后才去用他们匹配参数名。这个是Robot框架2.8.6引入的特性。

调用keyword的时候，命名参数需要有一个写一个显式的等号。就是说仅有变量是不会被当做命名参数的，即使是值像`foo=bar`的变量。记住这点非常重要，尤其是当一个keyword内嵌套另一个keyword的时候，比如，有一个keyword接受一个[不定长的参数]比如`@{args}`,然后又把他们传给另一个同样接受`@{args}`参数的keyword，有可能在调用的时候写成`named=arg`并不会成功。下边的例子介绍了这个问题。

```
*** Test Cases ***
Example
    Run Program    shell=True    # This will not come as a named argument to Run Process

*** Keywords ***
Run Program
    [Arguments]    @{args}
    Run Process    program.py    @{args}    # Named arguments are not recognized from inside @{args}
```

如果keyword想要接收然后船体任何的命名参数，那就必须要改为接收[自由keyword参数]()。请看[kwargs的例子]()中嵌套函数如何传递位置参数和命名参数。

<h5>转义命名参数的语法</h5>

只有当等号前的部分能匹配到一个keyword的参数，他才被当做是一个命名参数。但是也可能会有像`for=quux`只有值的位置参数，它并不是跟`foo`相关的参数。这种情况下，参数`foo`就会得到一个错误的值`quux`,或者更可能会报错。

很少情况下会发生这种偶然的匹配，可以用反斜杠来[转义]()，比如`foo\=quux`。这样这个参数就能得到字面值`foo=quux`。注意，如果没有参数名叫foo，那就没必要用转义，但是因为他让代码更清晰了，所以也不失为一个好办法。

<h5>哪里支持命名参数</h5>
上边已经说过，命名参数跟keyword一起作用。除此之外，[导入库]()也会用到。

[自定义keyword]()和[大多数测试库]()命名参数。只有一个例外就是使用[静态库API]()的java 库。用[Libdoc]()生成的库说明文档会记载库是不是支持命名参数。

>###### 注意
>Robot框架2.8之前的版本中使用[动态库API]()的测试库不支持命名参数

<h5>命名参数的例子</h5>

下边的例子说明了在库keyword和自定义keyword以及导入[Telnet]()的生活如何使用命名参数。
```
*** Settings ***
Library    Telnet    prompt=$    default_log_level=DEBUG

*** Test Cases ***
Example
    Open connection    10.0.0.42    port=${PORT}    alias=example
    List files    options=-lh
    List files    path=/tmp    options=-l

*** Keywords ***
List files
    [Arguments]    ${path}=.    ${options}=
    List files    options=-lh
    Execute command    ls ${options} ${path}
```

<h4 id="2-2-2-4">自由keyword参数</h4>

Robot框架2.8加入了[python风格的自由keyword参数]()`**kwargs`。这意味着keyword可以用`name=value`的语法接受所有的参数并且不匹配其他参数作为kwargs======待推敲

跟[命名参数]一样，自由keyword参数也支持用变量。这意味着名字和值都可以用变量来表示，但是表示变量的符号总是要显式的出现。比如，`foo=${bar}` 和 `${foo}=${bar}` 都是合法的，只要变量存在的。一个额外的限制是自由keyword参数名必须是字符串。参数名可以用变量是Robot框架2.8.6才有的新特性，之前的版本还没解决这个问题 ========带推敲

刚开始只有基于python的库支持自由keyword参数，但是因为Robot2.8.2支持了[动态库API]()和Robot2.8.3又支持了基于java的库和[远程库接口]()，最终Robot2.9中自定义keyword终于[支持kwargs]()。总之，现在所有的keyword都支持kwargs了。

<h5> Kwargs例子</h5>

第一个使用kwargs的例子，我们来看看[Process]()中的keyword*Run Process*。他的签名是`command, *arguments, **configuration`，就是说这个keyword接受一个要执行的命令(`command`)，一个作为命令参数的[不定长参数]()(`*arguments`)，最后是关键字参数是可选的配置参数(`**configuration`)。下边的例子展现了自由关键字参数和使用命名参数真的很像。

```
*** Test Cases ***
Using Kwargs
    Run Process    program.py    arg1    arg2    cwd=/home/user
    Run Process    program.py    argument    shell=True    env=${ENVIRON}
```

可以查看[创建测试libraries]()下边的[自由关键字参数(**kwargs)]()一节来了解更多在自定义库中使用kwargs语法的信息。

第二个例子，我们创建了一个包装了刚刚那个例子里用于运行`program.py`的[自定义keyword]()。keyword*Run Program*接受任意数量的参数和kwargs，然后把这些参数和要执行的命令一起传给*Run Process*

```
*** Test Cases ***
Using Kwargs
    Run Program    arg1    arg2    cwd=/home/user
    Run Program    argument    shell=True    env=${ENVIRON}

*** Keywords ***
Run Program
    [Arguments]    @{arguments}    &{configuration}
    Run Process    program.py    @{arguments}    &{configuration}
```


<h4 id="2-2-2-5">嵌入到keyword名字中的参数</h4>

把参数嵌入到keyword名字里是一种完全不同的用来指定参数的办法。[测试库keyword]()和[自定义keyword]()支持这种语法。







