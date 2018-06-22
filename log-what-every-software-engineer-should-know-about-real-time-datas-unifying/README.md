原文链接： [The Log: What every software engineer should know about real-time data's unifying abstraction](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) - [Jay Kreps](http://www.linkedin.com/in/jaykreps)，2013-12-16   
基于开源中国社区的译文稿： [日志：每个软件工程师都应该知道的有关实时数据的统一概念](http://www.oschina.net/translate/log-what-every-software-engineer-should-know-about-real-time-datas-unifying)  
译文发在[伯乐在线](http://blog.jobbole.com/)：[The Log：每个程序员都应该知道有关实时数据的统一抽象](http://blog.jobbole.com/89674/)， 2015-08-21

## 🍎 译序 

这篇文章是`LinkedIn`的`Kreps`发表的一篇博文，虽然很长，但被称为[程序员史诗般必读文章](http://bryanpendleton.blogspot.hk/2014/01/the-log-epic-software-engineering.html)。

[学习笔记：The Log（我所读过的最好的一篇分布式技术文章）](http://www.cnblogs.com/foreach-break/p/notes_about_distributed_system_and_The_log.html)对本文做了很赞的解读和摘要。

但作为一篇 **_经典_** 文章，还是值得去完整地研读和理解：

1. 原文可以作为大数据/分布式系统领域一份导论式的资料。   
    作者对整个领域的理解和实战精深广博，抓出并梳理了这个领域的核心：日志。
1. 原文作为一手资料，有完整的分析过程，能够深入和核对自己的理解。
1. 解读和摘要不能替代自己理解。  
    信息被过滤和传递得越多，丢失和偏差也就越多。

当然，你也可以把这篇译文本身作为英文原文的一种理解，在读原文时有不理解的地方可以参考对比。
如果你能这么做，相信对于学习效果真真是极好的～

[自己](http://weibo.com/oldratlee)理解粗浅且这篇文章又长难度又大，翻译中肯定会有不少不足和不对之处，欢迎建议（[提交Issue](https://github.com/oldratlee/translations/issues)）和指正（[Fork后提交代码](https://github.com/oldratlee/translations/fork)）！

<img src="images/oldratlee-alipay-qr.png" width="128" hspace="10px" align="right" >

PS：

- 为什么要整理和审校翻译 参见 [译跋](translation-postscript.md)
- 如果您觉得这译文对你有帮助，可以用支付宝扫描右边的二维码，请我喝杯可乐啥的？ ^\_^  
    邀捐赠是还头一回……

日志：每个软件工程师都应该知道的有关实时数据的统一抽象
=====================================================================

我在六年前加入到`LinkedIn`公司，那是一个令人兴奋的时刻：我们刚开始面临单一庞大的集中式数据库的限制问题，需要过渡到一套专门的分布式系统。
这是一个令人兴奋的经历：我们构建、部署和运行分布式图数据库（`distributed graph database`）、分布式搜索后端（`distributed search backend`）、
`Hadoop`以及第一代和第二代键值数据存储（`key-value store`），而且这套系统一直运行至今。

这个过程中，我学到的最有益的事情是我们所构建这套系统的许多组件其核心都包含了一个很简单的概念：日志。
日志有时会叫成 预先写入日志（`write-ahead logs`）、提交日志（`commit logs`）或者事务日志（`transaction logs`），几乎和计算机本身形影不离，
是许多分布式数据系统（`distributed data system`）和实时应用架构（`real-time application architecture`）的核心。

不懂得日志，你就不可能真正理解数据库、`NoSQL`存储、键值存储（`key value store`）、数据复制（`replication`）、`paxos`、`Hadoop`、版本控制（`version control`），甚至几乎任何一个软件系统；然而大多数软件工程师对日志并不熟悉。我有意于改变这个现状。
本文我将带你浏览有关日志需要了解的一切，包括日志是什么，如何在数据集成（`data integration`）、实时处理（`real time processing`）和系统构建中使用日志。

-----------------
[« 译跋](translation-postscript.md)　　　　　　　　[第一部分：日志是什么？ »](part1-what-is-a-log.md)

目录
-----------------

- [🍎 译序](#译序)
- [概述](#日志每个软件工程师都应该知道的有关实时数据的统一抽象)
- [第一部分：日志是什么？](part1-what-is-a-log.md)
    1. [数据库中的日志](part1-what-is-a-log.md#%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84%E6%97%A5%E5%BF%97)
    1. [分布式系统中的日志](part1-what-is-a-log.md#%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%E4%B8%AD%E7%9A%84%E6%97%A5%E5%BF%97)
    1. [变更日志（`changelog`）101：表与事件的二象性（`duality`）](part1-what-is-a-log.md#%E5%8F%98%E6%9B%B4%E6%97%A5%E5%BF%97changelog101%E8%A1%A8%E4%B8%8E%E4%BA%8B%E4%BB%B6%E7%9A%84%E4%BA%8C%E8%B1%A1%E6%80%A7duality)
    1. [接下来的内容](part1-what-is-a-log.md#%E6%8E%A5%E4%B8%8B%E6%9D%A5%E7%9A%84%E5%86%85%E5%AE%B9)
- [第二部分：数据集成](part2-data-integration.md)
    1. [数据集成：两个难题](part2-data-integration.md#%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%A4%E4%B8%AA%E9%9A%BE%E9%A2%98)
        - [事件数据管道](part2-data-integration.md#%E4%BA%8B%E4%BB%B6%E6%95%B0%E6%8D%AE%E7%AE%A1%E9%81%93)
        - [专用数据系统（`specialized data systems`）的爆发](part2-data-integration.md#%E4%B8%93%E7%94%A8%E6%95%B0%E6%8D%AE%E7%B3%BB%E7%BB%9Fspecialized-data-systems%E7%9A%84%E7%88%86%E5%8F%91)
    1. [日志结构化的（`log-structured`）数据流](part2-data-integration.md#%E6%97%A5%E5%BF%97%E7%BB%93%E6%9E%84%E5%8C%96%E7%9A%84log-structured%E6%95%B0%E6%8D%AE%E6%B5%81)
    1. [在`LinkedIn`](part2-data-integration.md#%E5%9C%A8linkedin)
    1. [`ETL`与数据仓库的关系](part2-data-integration.md#etl%E4%B8%8E%E6%95%B0%E6%8D%AE%E4%BB%93%E5%BA%93%E7%9A%84%E5%85%B3%E7%B3%BB)
    1. [日志文件与事件](part2-data-integration.md#%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6%E4%B8%8E%E4%BA%8B%E4%BB%B6)
    1. [构建可伸缩的日志](part2-data-integration.md#%E6%9E%84%E5%BB%BA%E5%8F%AF%E4%BC%B8%E7%BC%A9%E7%9A%84%E6%97%A5%E5%BF%97)
- [第三部分：日志与实时流处理](part3-logs-and-real-time-stream-processing.md)
    1. [数据流图（`data flow graphs`）](part3-logs-and-real-time-stream-processing.md#%E6%95%B0%E6%8D%AE%E6%B5%81%E5%9B%BEdata-flow-graphs)
    1. [有状态的实时流处理](part3-logs-and-real-time-stream-processing.md#%E6%9C%89%E7%8A%B6%E6%80%81%E7%9A%84%E5%AE%9E%E6%97%B6%E6%B5%81%E5%A4%84%E7%90%86)
    1. [日志合并（`log compaction`）](part3-logs-and-real-time-stream-processing.md#%E6%97%A5%E5%BF%97%E5%90%88%E5%B9%B6log-compaction)
- [第四部分：系统构建（`system building`）](part4-system-building.md)
    1. [分解单品方式而不是打包套餐方式（`Unbundling`）？](part4-system-building.md#%E5%88%86%E8%A7%A3%E5%8D%95%E5%93%81%E6%96%B9%E5%BC%8F%E8%80%8C%E4%B8%8D%E6%98%AF%E6%89%93%E5%8C%85%E5%A5%97%E9%A4%90%E6%96%B9%E5%BC%8Funbundling)
    1. [日志在系统架构中的地位](part4-system-building.md#%E6%97%A5%E5%BF%97%E5%9C%A8%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84%E4%B8%AD%E7%9A%84%E5%9C%B0%E4%BD%8D)
- [结束语及参考资料](the-end.md)
    1. [学术论文、系统、讨论和博客](the-end.md#%E5%AD%A6%E6%9C%AF%E8%AE%BA%E6%96%87%E7%B3%BB%E7%BB%9F%E8%AE%A8%E8%AE%BA%E5%92%8C%E5%8D%9A%E5%AE%A2)
    1. [值得关注的开源软件](the-end.md#%E5%80%BC%E5%BE%97%E5%85%B3%E6%B3%A8%E7%9A%84%E5%BC%80%E6%BA%90%E8%BD%AF%E4%BB%B6)
- [译跋](translation-postscript.md)
