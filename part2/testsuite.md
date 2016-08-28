# 2.3 建立测试套件

Robot框架测试用例在testcase文件中建立，testcase文件可以用目录组织。这些文件和目录构成了一个层级的测试套件结构。

* [2.3.1 测试用例文件](#2-3-1)
* [2.3.2 测试套件目录](#2-3-2)
    + [无效文件的警告](#2-3-2-1)
    + [初始化文件](#2-3-2-2)
* [2.3.3 测试套件的名字和文档](#2-3-3)
* [2.3.4 自由的测试套件元数据](#2-3-4)
* [2.3.5 套件的setup和TearDown](#2-3-5)


<h2 id="2-3-1">测试用例文件</h2>

robot框架测试用例在testcase文件中的testcase表中[创建]()。这样的一个文件中的所有testcase自动构成一个测试套件。testcase数目没有上线，但是推荐少于是个，除非用于[数据驱动方法](),一个testcase只包含一个高级keyword。

下列settings可以用于自定义testsuite。

*[Documentation]*
<br>&emsp;&emsp;用来指定[测试套件说明]()

*Metadata*
<br>&emsp;&emsp;用名-值对来设定[自由的测试套件元数据]()

*[Suite Setup, Suite Teardown]*
<br>&emsp;&emsp;指定[Suite Setup和Teardown]()

>注意
<br> 所有的setting名都可以添加一个可选的冒号，比如*Documentation:*。这能使得settings能有可读性，尤其是用普通文本格式的时候。

<h2 id="2-3-2">测试套件目录</h2>
<h3 id="2-3-2-1">无效文件的警告</h3>
<h3 id="2-3-2-2">初始化文件</h3>
<h2 id="2-3-3">测试套件的名字和文档</h2>
<h2 id="2-3-4">自由的测试套件元数据</h2>
<h2 id="2-3-5">套件的setup和TearDown</h2>