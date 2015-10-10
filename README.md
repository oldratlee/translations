translations [![知识共享协议（CC协议）](https://img.shields.io/badge/License-Creative%20Commons-DC3D24.svg) ![Attribution-NonCommercial-ShareAlike CC BY-NC-SA](LICENSE.png)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh)
=======================

一些不错英文资料的中文翻译。  
Chinese translations for good english resources.

[自己](http://weibo.com/oldratlee)想到去做些翻译，一是促进自己的深入学习，二是能为大家带来便利，三是兴趣。

遵循原则：『信』为本、力求『达』、不妄追『雅』。  
\# 信：忠实表达作者思想；达：让读者看起来轻松；雅：让读者看起来愉悦。详见[信达雅 - 百度百科](http://baike.baidu.com/view/645992.htm)。

- :see_no_evil: [自己](http://weibo.com/oldratlee)理解粗浅，翻译中不足和不对之处，欢迎 :clap:
    - 建议，[提交`Issue`](https://github.com/oldratlee/translations/issues/new)
    - 指正，[`Fork`后提通过`Pull Requst`贡献修改](https://github.com/oldratlee/translations/fork)
- 如有文章理解上有疑问 或是 使用过程中碰到些疑惑，请随意:raised_hands:[提交`Issue`](https://github.com/oldratlee/translations/issues/new) ，一起学习交流讨论！

设计原则
------------------

1. [`Python` Philosophy（`Python`哲学）翻译及简析](python-philosophy/README.md)  
既有指明大是大非的理念，又有指导细节操作的准则；
既有谆谆教导的推荐，也有声色俱厉的禁止。
1. [`Java`的通用`I/O` `API`设计](generic-io-api-in-java-and-api-design/README.md)  
给出了一个通用`Java` `IO` `API`设计，更重要的是，给出了这个`API`设计本身的步骤和过程，这让`API`设计有些条理。
文中示范了从 普通简单实现 整理成 正交分解、可复用、可扩展、高性能、无错误的`API`设计 的过程。这个很值得理解和学习！设计偏向是艺术，一个赏心悦目的设计，尤其是`API`设计，旁人看来多是妙手偶得的感觉，如果能有些章可循真是一件美事。
给出 _**减少艺术的艺术工作量**_ 的方法的人是 **大师**。
1. [`GUI` & `CLI`原则](gui-and-cli-principles/README.md)  
文中列出的`GUI`和`CLI`的原则：说明了两种`Interface`适合的场景和优劣；进而引导你去思考，面向用户或作为程序员的你，交互/操作 如何才能是高效的。

思考/思维
------------------

1. [提问的智慧](how-to-ask-questions-the-smart-way/README.md)  
    说明了作者所认为一位发问者事前应该要做好什么，而什么又是不该做的。作者认为这样能让问题容易令人理解，而且发问者自己也能学到较多东西。此文在网络上受到欢迎，被广泛转载而广为人知甚至奉为经典。
    著名的两个缩写`STFW`（`Search the fxxking web`）以及`RTFM`（`Read the fxxking manual`）就是出自本文。

分布式系统/大数据
------------------

1. [日志：每个软件工程师都应该知道的有关实时数据的统一抽象](log-what-every-software-engineer-should-know-about-real-time-datas-unifying/README.md)  
    这篇文章是`LinkedIn`的`Kreps`发表的一篇博文，被称为**_程序员史诗般必读_**文章。可以作为大数据/分布式系统领域一份导论式的资料。作者对整个领域的理解和实战精深广博，抓出并梳理了这个领域的核心：日志。
1. [Paxos Made Simple](paxos-made-simple/README.rst)  
    该论文给出描述一致性问题的概念、术语、算法，从复杂中抓取出了核心，给出了如此简单的描述。
    另言简意赅地说明了多实例`Paxos`（`Multi-Paxos`），这是真正实践中使用的`Paxos`。可以说不读这篇论文你就不知道**你还不知道**如何有效地描述和交流一致性算法。
1. [`PaxosLease`：实现租约的无盘`Paxos`算法](paxoslease/README.rst)  
    可以说是最简单且可以实际使用的`Paxos`算法变种。

`Git`
------------------

1. [`Git`工作流指南](git-workflows-and-tutorials/README.md)  
这篇指南以大家在`SVN`中已经广为熟悉使用的集中式工作流作为起点，循序渐进地演进到其它高效的分布式工作流，还介绍了如何配合使用便利的`Pull Request`功能，体系地讲解了各种工作流的应用。行文中实践原则和操作示例并重，对于`Git`的资深玩家可以梳理思考提升，而新接触的同学，也可以跟着step-by-step操练学习并在实际工作中上手使用。
1. [`Git` `2.1`有哪些新特性？](whats-new-git-2-1/README.md)

`Java`
------------------

1. [关于`Java`你可能不知道的10件事](10-things-you-didnt-know-about-java/README.md)  
你是不是写`Java`已经有些年头了？还依稀记得这些吧：
那些年，它还叫做`Oak`；那些年，`OO`还是个热门话题；那些年，`C++`同学们觉得`Java`是没有出路的；那些年，`Applet`还风头正劲…… 但我打赌下面的这些事中至少有一半你还不知道。

`Lisp`
------------------

1. [**_Successful Lisp_**中的`Lisp`书籍推荐](recommend-lisp-books/suggestions4further-reading-in-successful-lisp.md)
    - 考虑到`Lisp`入门的难度，整理了[`Lisp`书籍推荐和点评](recommend-lisp-books/README.md)
    - 特别提这篇好文[【转】学习`Lisp`的书籍推荐](recommend-lisp-books/recommend-lisp-books.md)

其它
------------------

1. [如何用`Linux`命令行管理网络：11个你必须知道的命令](how-to-work-with-network-from-linux-terminal/README.md)
1. [为什么`Android`手机会越用越慢，如何提速？](why-android-phones-slow-down-over-time-and-how-to-speed-them-up/README.md)
