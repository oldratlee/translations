翻译自《Programming Groovy － Dynamic Productivity for the jdk Developer》的P243。  
译文发在：[`Stubs`和`Mocks`的区别](http://oldratlee.com/76/tech/java/stubs-vs-mocks.html)，2010-09-23 / [`Stubs`和`Mocks`的区别](http://blog.csdn.net/oldrat/archive/2010/03/09/5359198.aspx)，2010-03-09。

--------------

讲得言简意赅，值得翻译出来理解。

`Stubs`和`Mocks`的区别
======================

在文章[『`Mocks`不是`Stubs`』](http://martinfowler.com/articles/mocksArentStubs.html)，马丁·福勒讨论了`stubs`和`mocks`之间的区别。

`stub`代表的是一个真实的对象。在测试代码中被调用时，它简单地按照之前对它训练的设定来应答调用者。对`stub`应答的设定是为了通过测试。

`mock`对象要比`stub`做的事情多得多：

- 帮你确定你的代码和它的依赖（称为合作者`collaborator`）有你期望的交互。
- 跟踪你的在`mock`对象代表的合作者执行调用的序列和次数。
- 保证方法调用传递了合适的参数。

`stubs`只检查的是状态（`state`），而`mocks`检查了行为（`behavior`）。在测试中使用`mock`，不仅检查你测试和其依赖之间和状态，而且还有行为。

> 原文如下：
> 
> Stubs vs. Mocks
> 
> In the article “Mocks Aren’t Stubs,” (<http://martinfowler.com/articles/mocksArentStubs.html>),
> Martin Fowler discusses the difference between stubs and mocks. A stub stands in for a real object.
> It simply reciprocates the coached expected response when called by the code being tested.
> The response is set up to satisfy the needs for the test to pass.
> A mock object does a lot more than a stub.
> It helps you ensure your code is interacting with its dependencies, the collaborators, as expected.
> It can keep track of the sequence and number of calls your code makes on the collaborator it stands in for.
> It ensures proper parameters are passed in to method calls.
> While stubs verify state, mocks verify behavior.
> When you use a mock in your test, it veriﬁes not only the state but also the behavior of the interaction of your code with its dependencies.
> Groovy provides support for creating both stubs and mocks, as you will see in Section 16.10, Mocking Using the Groovy Mock Library, on page 254.

PS
=============

顺便推荐一下[《Programming Groovy 2 － Dynamic Productivity for the jdk Developer》](https://book.douban.com/subject/22170571/)这本书。

这是一本`Groovy`进阶的书，入门可以先看一下[《Groovy Recipes －Greasing the Wheels of jdk》](https://book.douban.com/subject/2584518/)或是[《Groovy Programming －An Introduction for jdk Developers》](https://book.douban.com/subject/1969390/)。`Groovy`的学习资料的介绍可以参见：<http://oldratlee.com/87/groovy-study-info.html>。

《Programming Groovy 2》中对于`Groovy`的 **_元编程_** 应该是讲解最全面的；更难得的是，对动态语言的编程最佳实践也讲了很多。

相关资料
=============

- [Mocks Aren’t Stubs](http://martinfowler.com/articles/mocksArentStubs.html)  
    [Mock并非Stub（翻译）](http://www.predatorray.me/Mock%E5%B9%B6%E9%9D%9EStub-%E7%BF%BB%E8%AF%91/)
- 测试相关书籍
    - [《测试驱动开发》](https://book.douban.com/subject/1230036/)
    - [《JUnit实战(第2版)》](https://book.douban.com/subject/10561424/)
    - [《测试驱动的面向对象软件开发》](https://book.douban.com/subject/4910582/)
