原文链接： [The Log: What every software engineer should know about real-time data's unifying abstraction](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) - [Jay Kreps](http://www.linkedin.com/in/jaykreps)   
译文原稿： [日志：每个软件工程师都应该知道的有关实时数据的统一概念](http://www.oschina.net/translate/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) @ [开源中国社区](http://www.oschina.net/) 2015-02

日志：每个软件工程师都应该知道的有关实时数据的统一概念
=====================================================================

我在六年前的一个令人兴奋的时刻加入到LinkedIn公司。从那个时候开始我们就破解单一的、集中式数据库的限制，并且启动到特殊的分布式系统套件的转换。这是一件令人兴奋的事情：我们构建、部署，而且直到今天仍然在运行的分布式图形数据库、分布式搜索后端、Hadoop安装以及第一代和第二代键值数据存储。

从这一切里我们体会到的最有益的事情是我们构建的许多东西的核心里都包含一个简单的理念：日志。有时候也称作预先写入日志或者提交日志或者事务日志，日志几乎在计算机产生的时候就存在，同时它还是许多分布式数据系统和实时应用结构的核心。

不懂得日志，你就不可能完全懂得数据库，NoSQL存储，键值存储，复制，paxos,Hadoop,版本控制以及几乎所有的软件系统；然而大多数软件工程师对它们不是很熟悉。我愿意改变这种现状。在这篇博客文章里，我将带你浏览你必须了解的有关日志的所有的东西，包括日志是什么，如何在数据集成、实时处理和系统构建中使用日志等。

- [第一部分：日志是什么？](part1.md)
- [第二部分：数据集成](part2.md)
- [第三部分：日志和实时流处理](part3.md)
- [第四部分：系统建设](part4.md)
- [结束语](the-end.md)
