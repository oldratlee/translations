原文链接： [The Log: What every software engineer should know about real-time data's unifying abstraction](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) - [Jay Kreps](http://www.linkedin.com/in/jaykreps)   
译文原稿： [日志：每个软件工程师都应该知道的有关实时数据的统一概念](http://www.oschina.net/translate/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) @ [开源中国社区](http://www.oschina.net/) 2015-02

译序
-----------------

译文基于[日志：每个软件工程师都应该知道的有关实时数据的统一概念](http://www.oschina.net/translate/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) @ [开源中国社区](http://www.oschina.net/)，感谢 [LitStone](http://my.oschina.net/kaiyuancao), [super0555](http://my.oschina.net/super0555), [几点人](http://my.oschina.net/jidianren), [cmy00cmy](http://my.oschina.net/u/1385461), [tnjin](http://my.oschina.net/tnjin), [928171481](http://my.oschina.net/u/240148), [黄劼](http://my.oschina.net/saintknight) 同学的辛苦付出！

开源中国的译文自己在读的过程，觉得可以有加强：

- 开源中国译文页面包含了影响阅读的杂乱内容（如译者信息、翻译评论……）
- 分页随意，应该按小节分页
- 没有目录
- 图片的排版没有按原文，影响原作者用图心意。
- 多人翻译的审校不足，不少翻译需要改进和改正。

日志：每个软件工程师都应该知道的有关实时数据的统一抽象
=====================================================================

我在六年前加入到`LinkedIn`公司，那是一个令人兴奋的时刻：我们刚开始面临单一庞大的集中式数据库的限制问题，需要过渡到一套专业的分布式系统。
这是一个令人兴奋的经历：我们构建、部署和运行分布式图形数据库（`distributed graph database`）、分布式搜索后端（`distributed search backend`）、
`Hadoop`以及第一代和第二代键值数据存储（`key-value store`），而且这套系统一直运行至今。

这个过程中，我学到的最有益的事情是我们所构建这套系统的许多组件其核心都包含了一个很简单的概念：日志。
日志有时会叫成 预先写入日志（`write-ahead logs`）、提交日志（`commit logs`）或者事务日志（`transaction logs`），几乎和计算机本身形影不离，
是许多分布式数据系统（`distributed data system`）和实时应用架构（`real-time application architecture`）的核心。

不懂得日志，你就不可能真正理解数据库、`NoSQL`存储、键值存储、数据复制（`replication`）、`paxos`、`Hadoop`、版本控制（`version control`），甚至几乎任何一个软件系统；然而大多数软件工程师对日志并不熟悉。我有意于改变这个现状。
本文我将带你浏览有关日志需要了解的一切，包括日志是什么，如何在数据集成（`data integration`）、实时处理（`real time processing`）和系统构建中使用日志。

-----------------

- [第一部分：日志是什么？](part1-what-s-a-log.md)
    - [数据库中的日志](part1-what-s-a-log.md#数据库中的日志)
    - [分布式系统中的日志](part1-what-s-a-log.md#分布式系统中的日志)
    - [变更日志101：表与事件的二相性](part1-what-s-a-log.md#变更日志101表与事件的二相性)
    - [接下来的内容](part1-what-s-a-log.md#接下来的内容)
- [第二部分：数据集成](part2-data-integration.md)
    - [数据集成：两个难题](part2-data-integration.md#数据集成两个难题)
        - [事件数据管道](part2-data-integration.md#事件数据管道)
        - [专用的数据系统（`specialized data systems`）的爆发](part2-data-integration.md#专用的数据系统specialized-data-systems的爆发)
    - [日志结构化的（`log-structured`）数据流](part2-data-integration.md#日志结构化的log-structured数据流)
    - [在`LinkedIn`](part2-data-integration.md#在linkedin)
    - [`ETL`与数据仓库的关系](part2-data-integration.md#etl与数据仓库的关系)
    - [日志文件与事件](part2-data-integration.md#日志文件与事件)
    - [构建可伸缩的日志](part2-data-integration.md#构建可伸缩的日志)
- [第三部分：日志与实时流处理](part3-logs-and-real-time-stream-processing.md)
    - [数据流图（`data flow graphs`）](part3-logs-and-real-time-stream-processing.md#数据流图data-flow-graphs)
    - [有状态的实时流处理](part3-logs-and-real-time-stream-processing.md#有状态的实时流处理)
    - [日志合并（`log compaction`）](part3-logs-and-real-time-stream-processing.md#日志合并log-compaction)
- [第四部分：系统建设](part4-system-building.md)
- [结束语](the-end.md)
