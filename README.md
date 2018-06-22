# translations [![知识共享协议（CC协议）](https://img.shields.io/badge/License-Creative%20Commons-DC3D24.svg) ![Attribution-NonCommercial-ShareAlike CC BY-NC-SA](LICENSE.png)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh)

[![Join the chat at https://gitter.im/oldratlee/translations](https://badges.gitter.im/oldratlee/translations.svg)](https://gitter.im/oldratlee/translations?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![GitHub stars](https://img.shields.io/github/stars/oldratlee/translations.svg?style=social&label=Star)](https://github.com/oldratlee/translations/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/oldratlee/translations.svg?style=social&label=Fork)](https://github.com/oldratlee/translations/fork)
[![GitHub watchers](https://img.shields.io/github/watchers/oldratlee/translations.svg?style=social&label=Watch)](https://github.com/oldratlee/translations/watchers)

一些不错英文资料的中文翻译。  
Chinese translations for classic IT resources.

[自己](http://weibo.com/oldratlee)想到去做些翻译，一是促进自己的深入学习，二是能为大家带来便利，三是兴趣。

遵循原则：『信』为本、力求『达』、不妄追『雅』。  
\# 信：译文忠实表达作者思想；达：让读者轻松地阅读；雅：让读者愉悦地阅读。详见[信达雅 - 百度百科](http://baike.baidu.com/view/645992.htm)。

- 🙈 [自己](http://weibo.com/oldratlee)理解粗浅，翻译中不足和不对之处，欢迎 👏
    - 建议，[提交`Issue`](https://github.com/oldratlee/translations/issues/new)
    - 指正，[`Fork`后提通过`Pull Requst`贡献修改](https://github.com/oldratlee/translations/fork)
- 如有文章理解上有疑问 或是 使用过程中碰到些疑惑，请随意:raised_hands:[提交`Issue`](https://github.com/oldratlee/translations/issues/new) ，一起学习交流讨论！

# 文章分类

<img src="icon.png" width="33%" align="right" />

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [思考/思维](#%E6%80%9D%E8%80%83%E6%80%9D%E7%BB%B4)
- [设计原则](#%E8%AE%BE%E8%AE%A1%E5%8E%9F%E5%88%99)
- [系统设计实例](#%E7%B3%BB%E7%BB%9F%E8%AE%BE%E8%AE%A1%E5%AE%9E%E4%BE%8B)
- [分布式系统/大数据](#%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%E5%A4%A7%E6%95%B0%E6%8D%AE)
- [并发](#%E5%B9%B6%E5%8F%91)
- [`Git`](#git)
- [`Erlang`/`Elixir`](#erlangelixir)
- [`Lisp`](#lisp)
- [`Java`](#java)
- [软件测试](#%E8%BD%AF%E4%BB%B6%E6%B5%8B%E8%AF%95)
- [其它](#%E5%85%B6%E5%AE%83)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 思考/思维

1. [提问的智慧](how-to-ask-questions-the-smart-way/README.md)  
    说明了作者所认为一位发问者事前应该要做好什么，而什么又是不该做的。作者认为这样能让问题容易令人理解，而且发问者自己也能学到较多东西。此文在网络上受到欢迎，被广泛转载而广为人知甚至奉为经典。著名的两个缩写`STFW`（`Search the fxxking web`）以及`RTFM`（`Read the fxxking manual`）就是出自本文。

# 设计原则

1. [`Python` Philosophy（`Python`哲学）翻译及简析](python-philosophy/README.md)  
    既有指明大是大非的理念，又有指导细节操作的准则；既有谆谆教导的推荐，也有声色俱厉的禁止。
1. [`Java`的通用`I/O` `API`设计](generic-io-api-in-java-and-api-design/README.md)  
    给出了一个通用`Java` `IO` `API`设计，更重要的是给出了这个`API`设计本身的步骤和过程，这让`API`设计有些条理。文中示范了从 普通简单实现 整理成 正交分解、可复用、可扩展、高性能、无错误的`API`设计 的过程，这个过程是很值得理解和学习！设计偏向是艺术，一个赏心悦目的设计，尤其是`API`设计，旁人看来多是妙手偶得的感觉，如果能有些章可循真是一件美事。给出 _**减少艺术的艺术工作量**_ 的方法的人是 **大师**。
1. [`API`设计原则 - `Qt`官网的设计实践总结](api-design-principles-from-qt/README.md)  
    `Qt`的设计水准在业界很有口碑，一致、易于掌握和强大的`API`是`Qt`最著名的优点之一。此文既是`Qt`官网上的`API`设计指导准则，也是`Qt`在`API`设计上的实践总结。虽然`Qt`用的是`C++`，但其中设计原则和思考是具有普适性的（如果你对`C++`还不精通，可以忽略与`C++`强相关或是过于细节的部分，仍然可以学习或梳理关于`API`设计最有价值的内容）。整个篇幅中有很多示例，是关于`API`设计一篇难得的好文章。
1. [`GUI` & `CLI`原则](gui-and-cli-principles/README.md)  
    文中列出的`GUI`和`CLI`的原则：说明了两种`Interface`适合的场景和优劣；进而引导你去思考，面向用户或作为程序员的你，交互/操作 如何才能是高效的。

# 系统设计实例

1. [重叠实验设施：更多、更好、更快地实验](overlapping-experiment-infrastructure-more-better-faster-experimentation/README.md)  
    `Google`这篇8年前2010年的关于『实验基础设施』设计的论文，现在看来仍然是关于这个领域最有深度和体系的资源。不单说明了，实验设施的系统设计，还包含实验的进阶的主题如：实验可信度、敏感度、围绕实验数据驱动的整体流程。对于了解`Growth Hacking`/`ABTest`的同学，可以有效的学习实验设施的系统设计，尤其是重叠实验设施要考虑多方面的需求、维度，如何建模是很复杂的；对于不了解`Growth Hacking`/`ABTest`这个领域知识的同学，可以通过这篇文章，学习一个复杂系统整体的思考和设计的模式，包含需求、场景、模型设计、产品流程、落地关键。

# 分布式系统/大数据

1. [日志：每个软件工程师都应该知道的有关实时数据的统一抽象](log-what-every-software-engineer-should-know-about-real-time-datas-unifying/README.md)  
    这篇文章是`LinkedIn`的`Kreps`发表的一篇博文，被称为 **_程序员史诗般必读_** 文章。可以作为大数据/分布式系统领域一份导论式的资料。作者对整个领域的理解和实战精深广博，抓出并梳理了这个领域的核心：日志。
1. [Paxos Made Simple](paxos-made-simple/README.rst)  
    该论文给出描述一致性问题的概念、术语、算法，从复杂中抓取出了核心，给出了如此简单的描述。另言简意赅地说明了多实例`Paxos`（`Multi-Paxos`），这是真正实践中使用的`Paxos`。可以说不读这篇论文你就不知道**你还不知道**如何有效地描述和交流一致性算法。
1. [`PaxosLease`：实现租约的无盘`Paxos`算法](paxoslease/README.rst)  
    可以说是最简单且可以实际使用的`Paxos`算法变种。

# 并发

1. [`Java` `Fork/Join`框架](a-java-fork-join-framework/README.md)  
    _Doug Lea_ 大神关于`Java 7`引入的他写的`Fork/Join`框架的论文。[反应式编程](https://www.reactivemanifesto.org/zh-CN)（`Reactive Programming`/`RP`）作为一种范式在整个业界正在逐步受到认可和落地，是对过往系统的业务需求理解梳理之后对系统技术设计/架构模式的提升总结。`Java`作为一个成熟平台，对于趋势一向有些稳健的接纳和跟进能力，有着令人惊叹的生命活力：`Java 7`提供了`ForkJoinPool`，支持了`Java 8`提供的`Stream`，另外`Java 8`还提供了`Lamda`（有效地表达和使用`RP`需要`FP`的语言构件和理念）；有了前面的这些稳健但不失时机的准备，在`Java 9`中提供了面向`RP`的官方[`Flow API`](https://community.oracle.com/docs/DOC-1006738)，实际上是直接把[`Reactive Streams`](http://www.reactive-streams.org/)的接口加在`Java`标准库中，即[`Reactive Streams`规范](https://github.com/reactive-streams/reactive-streams-jvm#specification)转正了，`Reactive Streams`是`RP`的基础核心组件。`Flow API`标志着`RP`由集市式的自由探索阶段 向 教堂式的统一使用的转变。通过上面这些说明，可以看到`ForkJoinPool`的基础重要性。

# `Git`

1. [`Git`工作流指南](git-workflows-and-tutorials/README.md)  
    关于`Git`工作流主题，也许这是目前最全面最深入的说明。这篇指南以大家在`SVN`中已经广为熟悉使用的集中式工作流作为起点，循序渐进地演进到其它高效的分布式工作流，还介绍了如何配合使用便利的`Pull Request`功能，体系地讲解了各种工作流的应用。行文中实践原则和操作示例并重，对于`Git`的资深玩家可以梳理思考提升，而新接触的同学，也可以跟着step-by-step操练学习并在实际工作中上手使用。
1. [`Git` `2.1`有哪些新特性？](whats-new-git-2-1/README.md)

# `Erlang`/`Elixir`

1. [`Erlang`之父学习`Elixir`语言的一周](a-week-with-elixir/README.md)  
    作为`Erlang`之父 _Joe Armstrong_，对`Erlang VM`上的新语言`Elixir`做了很精彩的评论和思考。『特定领域专家的专业直觉』、『编程语言设计的三定律』、『管道操作符避免恶心代码』、『`Elixir`的`sigil`引出的程序语言如何定义/解释字符串』等等问题的讨论，个性鲜明又幽默诙谐的行文风格，都能强烈感受到 _Joe Armstrong_ 深入广博的老黑客风范。

# `Lisp`

1. [**_Successful Lisp_** 中的`Lisp`书籍推荐](recommend-lisp-books/suggestions4further-reading-in-successful-lisp.md)
    - [`Lisp`书籍推荐和点评](recommend-lisp-books/README.md)，由于`Lisp`与其它语言从**基本概念**就开始的差异，已有的语言经验反而是个学习阻碍，深入浅出的巧妙讲解对入门太重要了。
    - 特别提这篇好文[【转】学习`Lisp`的书籍推荐](recommend-lisp-books/recommend-lisp-books.md)

# `Java`

1. [关于`Java`你可能不知道的10件事](10-things-you-didnt-know-about-java/README.md)  
    作者是个`Java`老鸟，行文风趣幽默，娓娓道出`Java`的诡异和难点时不忘着给出用心良苦的提点。

# 软件测试

1. [`Stubs`和`Mocks`的区别](stubs-vs-mocks/README.md)  
    翻译自《Programming Groovy》，讲得言简意赅。

# 其它

1. [如何用`Linux`命令行管理网络：11个你必须知道的命令](how-to-work-with-network-from-linux-terminal/README.md)
1. [为什么`Android`手机会越用越慢，如何提速？](why-android-phones-slow-down-over-time-and-how-to-speed-them-up/README.md)
