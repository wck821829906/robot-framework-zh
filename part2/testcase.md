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
* [2.2.3 错误](#2-2-3-)
    - [测试用例何时会错误](#2-2-3-1)
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

>注意:<br> Robot框架2.8之前的版本 不能对没有默认值的参数起名字

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



<h3 id="2-2-3">失败</h3>

<h4 id="2-2-3-1">测试用例什么时候会失败</h4>

当一个testcase里的任意一个keyword失败了，那这个testcase也就失败了。正常情况下，这说明testcase的执行终止了，可能会执行完[test TearDown]() ，然后就去执行下一个testcase了，也可能是一个[可以继续执行的失败]() 就算是遇到意外的终止。

<h4 id="2-2-3-2">失败信息</h4>

testcaes的错误信息是直接从失败的keyword中得到的。失败的信息通畅是keyword自己生成的，但也有keyword支持配置错误信息

有些环境下，比如当使用可继续执行的失败的时候，一个testcase可以失败很多次。这种情况下，失败信息由每个错误信息连接起来，大量的错误信息会在中途就去掉，这样可以保值[报告]() 更容易读取。而失败的keyword可以在[日志]() 文件中看到完整的错误信息

默认的错误信息是普通文本，但是从Robot2.8开始，也可以用[包含HTML格式]() 的文本了。这类错误的信息用`*HTML*`的标记开头，这个标记并不会出现在最后的报告和日志文件中，下边第二个例子中自定义的错误信息使用了的HTML。

```
*** Test Cases ***
Normal Error
    Fail    This is a rather boring example...

HTML Error
    ${number} =    Get Number
    Should Be Equal    ${number}    42    *HTML* Number is not my <b>MAGIC</b> number.
```

<h3 id="2-2-4">2.2.4 testcase名称和说明文档</h3>

testcase名字直接来自于testcase表，就是testcase那一列里填的东西。每一个Test Suite里的testcase的名字都唯一，与此相关的是你可以在testcase中用[自动化变量]() `${TEST_NAME}`来引用testcase的名字。只要一项测试开始了，包括所有的自定义keyword、test setup以及Test TearDown，那这个变量就是可用的。

*[Documentation]*设定运行你为这个testcase设定一个自由的说明文档。这段说明会和测试日志测试报告一起在控制台输出行中显示出来。说明文档也可以使用[HTML风格]，还可以用[变量]() 让说明文档动态化。

如果说明文档被分成了好多列，那么每一列的内容将会用空格连接起来，在使用[HTML格式]和列很窄的时候很有用。如果说明文档被[分成了好几行]() ，那他们会[用换行符连接起来]() 。日后一行里已经有了换行符护照[转义的反斜杠]() ，那么最后的说明文档中也不会添加新的换行符

```
*** Test Cases ***
Simple
    [Documentation]    Simple documentation
    No Operation

Formatting
    [Documentation]    *This is bold*, _this is italic_  and here is a link: http://robotframework.org
    No Operation

Variables
    [Documentation]    Executed at ${HOST} by ${USER}
    No Operation

Splitting
    [Documentation]    This documentation    is split    into multiple columns
    No Operation

Many lines
    [Documentation]    Here we have
    ...                an automatic newline
    No Operation
```

testcase有一个简洁准确的名字很重要，这样就不用写说明文档了。如果一个testcase因为内部的逻辑需要添加说明，那可能意味着testcase里的keyword需要更好的名字或者需要改进，而不是添加说明文档。最后，上边例子中的一些元数据，比如环境和用户信息，最后用[标签]() 来指定。

<h3 id="2-2-5">2.2.5 给testcase打标签</h3>

在Robot中使用标签是一个简单而强大的分类testcase的方法。标签是普通文本，如果以下的需求可以使用标签：
- 标签会显示在测试资料的[报告]()、[日志]()中，当然提供了测试用例元数据的测试资料也会显示。
- 测试用例的[统计]()(包括全部的、通过的、失败的)是基于标签自动收集的
- 你可以用标签来选择[包括或者排除]()要执行的测试用例
- 你可以用标签来指定哪些测试用例是[严格的]()

这一节只介绍如何设置测试用例的标签，下边列举了不同的设置方法。这些方法可以同时使用。

Setting表中的*Force Tags*
<br>在有这个setting的测试用例文件中，所有的testcase都会得到一个指定的标签。如果是在测试套件的[初始化文件]()中使用，所有的子测试套件都会有这个标签。

Setting表中的*Default Tags*
<br>没有*[Tag]*设置的testcase会被使用这个标签。测试套件的初始化文件不支持默认标签。

TestCase表中的*[Tag]*
<br>testcase中的标签总是来自于这个值。此外，也可以不用继承*Default Tags*中的值，把*Default Tags*用空值覆盖就就可以，也可以用`NONE`覆盖默认tag。

`--settag`命令行选项
<br>所有没设置tag的testcase，都可以用这个选项设置tag。

*Set Tags, Remove Tags, Fail and Pass Execution*等keyword
<br>这些[内置]()的keyword可以在测试执行过程中动态的操控tag

标签是自有文本，但是他们会被规范化，所以会被转换成小写而且会移除所有的空格。如果一个testcase有被同一个标签设置了好几次，那只会保留第一个。标签可以用变量表示，前提是变量必须存在。

```
*** Settings ***
Force Tags      req-42
Default Tags    owner-john    smoke

*** Variables ***
${HOST}         10.0.1.42

*** Test Cases ***
No own tags
    [Documentation]    This test has tags owner-john, smoke and req-42.
    No Operation

With own tags
    [Documentation]    This test has tags not_ready, owner-mrx and req-42.
    [Tags]    owner-mrx    not_ready
    No Operation

Own tags with variables
    [Documentation]    This test has tags host-10.0.1.42 and req-42.
    [Tags]    host-${HOST}
    No Operation

Empty own tags
    [Documentation]    This test has only tag req-42.
    [Tags]
    No Operation

Set Tags and Remove Tags Keywords
    [Documentation]    This test has tags mytag and owner-john.
    Set Tags    mytag
    Remove Tags    smoke    req-*
```

<h4 id="2-2-5-1">保留的标签</h4>
通常情况下，用户可以使用任何在他们的环境中能使用的标签。但是，有一些确定的标签有一些用来预定义Robot框架自己的标签，如果使用这些标签，可能会有一些意想不到的结果。所有Robot已经指定的和将来会使用的标签都有前缀`robot-`。为了避免这个问题，用户不应该使用任何`robot-`前缀的标签，除非确实要使用(那个标签)的特殊功能。

在写这篇文章的时候，只有一个特殊的标签`robot-exit`在[优雅地停止测试执行]()时，会被自动加入到测试。更多相似的用法，以后会加入的。

<h3 id="2-2-6">setup和teardown</h3>

和其他自动化测试框架一样，robot也有类似的setup和TearDown功能。简单说，Test Setup就是在testcase执行前的事情，TearDown就是testcase执行后要做的事情。在robot中，setup和TearDown就是正常的keyword，如果需要可以传入参数。

SetUp和TearDown总是单个keyword。如果他们需要执行多个独立的任务，就应该为此再创建一个更高级别的[自定义keyword]()。另一种办法是用[内置]()的的keyword *Run Keywords*执行多个keyword。

有两种办法可以指定TearDown。第一种，testcase失败的时候也会执行，所以这种可以用于清理动作，不论testcase的状态如何。此外，所有TearDown中的keyword都会被执行，即使其中一个失败了。这种[错误后继续执行]()的功能也可以在正常keyword中应用，只不过在TearDown中的keyword默认开启的。

最简单指定SetUp和TearDown的方法是在testcase文件的setting表中使用 *Test Setup* 和 *Test TearDown* 设定。独立的testcase也可以有自己的setup和TearDown。通过在TestCase表中的*[SetUp]*和*[TearDown]*，他们将覆盖*Test Setup*和*Test Teardown*设定。如果*[SetUp]*和*[TearDown]*后边没有keyword，那就意味着找个testcase没有keyword，还可以用值`NONE`来指定testcase没有setup/teardown。

```
*** Settings ***
Test Setup       Open Application    App A
Test Teardown    Close Application

*** Test Cases ***
Default values
    [Documentation]    Setup and teardown from setting table
    Do Something

Overridden setup
    [Documentation]    Own setup, teardown from setting table
    [Setup]    Open Application    App B
    Do Something

No teardown
    [Documentation]    Default setup, no teardown at all
    Do Something
    [Teardown]

No teardown 2
    [Documentation]    Setup and teardown can be disabled also with special value NONE
    Do Something
    [Teardown]    NONE

Using variables
    [Documentation]    Setup and teardown specified using variables
    [Setup]    ${SETUP}
    Do Something
    [Teardown]    ${TEARDOWN}
```

setup或者TearDown中keyword的名字可以是个变量，通过在命令行中用变量来指定keyword名有利于在不同的环境中使用不同的setup和TearDown。

>注意:
<br>[测试套件可以用他们自己的setup和teardown]().一个测试套件的setup会在所有testcase或者其子测试套件之前执行，测试套件的TearDown在他们之后执行。

<h3 id="2-2-7">测试模板</h3>

测试模板将正常的[关键字驱动]()测试转换成[数据驱动]()测试。关键字驱动的测试用例是用keyword和他们附带参数构成主体的，然而带模板的testcase只有参数和模板keyword。这样就可以在一个测试或者一个文件中完成多次测试，而不是重复相同的keyword。

模板keyword可以接受正常的位置参数和命名参数，也支持嵌入到keyword名字中的参数。跟其他settings不一样，不能用变量来定义模板。

<h4 id="2-2-7-1">基本用法</h4>

下边的例子介绍了接受正常位置参数的keyword作为模板的用法。这两个testcase在功能上是完全一样的。

```
*** Test Cases **
Normal test case
    Example keyword    first argument    second argument

Templated test case
    [Template]    Example keyword
    first argument    second argument
```

正如上边例子想展示的，可以用*[Template]*设定来为一个单独的testcase指定模板。另一个是利用在Settings表中的*Test Template*，这个模板会应用到这个testcase文件中的所有testcase。*[Template]*setting会覆盖settings表中的模板设置。*[Template]*设置为空值意味着testcase没有模板，即使是*Test Template*已经设置了。除了空值，还可以用`NONE`。

如果一个用模板的testcase包含多行数据，模板会一行一行的读完所有的数据。这意味着，有些keyword要执行很多次，每行数据执行一次。模板测试也比较特殊，所有数据都会执行，不会因为某一行或某几行失败就终止。在正常测试中也可以用这种[可以继续的失败]()模式，只不过模板测试自动开启这个模式。

```
*** Settings ***
Test Template    Example keyword

*** Test Cases ***
Templated test case
    first round 1     first round 2
    second round 1    second round 2
    third round 1     third round 2
```

在模板中用[默认值参数]()或者[不定长参数]()，还有[命名参数]()和[自由keyword参数]()，跟其他地方的用法一样。参数中使用[变量]()也是正常支持。

<h4 id="2-2-7-2">内嵌关键字的模板</h4>

从robot2.8.2开始，模板支持一系列[嵌入参数的语法]()。这种语法使得模板keyword的名字中也可以有变量。他们被看作是参数的占位符，并在执行的时候回替换成实际参数。计算结果的keyword没意义使用位置参数。用一个例子来说明这个特性很好。

```
*** Test Cases ***
Normal test case with embedded arguments
    The result of 1 + 1 should be 2
    The result of 1 + 2 should be 3

Template with embedded arguments
    [Template]    The result of ${calculation} should be ${expected}
    1 + 1    2
    1 + 2    3

*** Keywords ***
The result of ${calculation} should be ${expected}
    ${result} =    Calculate    ${calculation}
    Should Be Equal    ${result}     ${expected}
```

模板使用嵌入式的参数是，模板keyword命中的变量数目必须与传入使用的参数数目一致。参数名不需要跟原来keyword的完全一样，也可以用完全不同的参数

```
*** Test Cases ***
Different argument names
    [Template]    The result of ${foo} should be ${bar}
    1 + 1    2
    1 + 2    3

Only some arguments
    [Template]    The result of ${calculation} should be 3
    1 + 2
    4 - 1

New arguments
    [Template]    The ${meaning} of ${life} should be 42
    result    21 * 2
```

使用嵌入参数的模板最大的好处是可以明确指定参数名。当使用普通参数的时候，也可以用命名参数列的方法来达到相同的效果。下一节中[数据驱动风格]的例子将会说明这一点。

<h4 id="2-2-7-3">for循环的模板</h4>

如果模板使用了[for循环](),模板会被用于每一次循环。可以继续的失败模式依旧是开启的，这意味着，所有循环都都会被执行，就算有的会失败。
``
```
*** Test Cases ***
Template and for
    [Template]    Example keyword
    :FOR    ${item}    IN    @{ITEMS}
    \    ${item}    2nd arg
    :FOR    ${index}    IN RANGE    42
    \    1st arg    ${index}
```

<h3 id="2-2-8">不同的测试用例风格</h3>

有几种不同的方法来编写测试用例。用来描述不同的 *工作流* 的testcase可以用关键字驱动和行为驱动的风格。数据驱动风格用于通过大量数据测试同一个工作流。

<h4 id="2-2-8-1">关键字驱动</h4>

工作流测试，比如之前描述的*Valid Login*测试，有好几个keyword和参数构成。他们正常的结构就是首先让系统进入初始状态（比如*Valid Login*中的*Open Login Page*），然后做一些事情，比如（*Input Name*, *Input Password*, *Submit Credentials*），最后验证系统是否达到预期，（*Welcome Page Should Be Open*）。

<h4 id="2-2-8-2">数据驱动模板</h4>

另一种测试风格是数据驱动的测试，测试用例只用一个高级的隐藏了实际的测试流程的keyword，通常是[自定义keyword]()。这些测试在在测试相同场景下不同的输入或者输出的测试中很有用。每一个测试中keyword可以重复，但是[测试模板]()功能只允许指定一次。

<h4 id="2-2-8-3">行为驱动</h4>


