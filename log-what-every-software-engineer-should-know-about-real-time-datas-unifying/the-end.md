结束语及参考资料
=============================

1. [学术论文、系统、讨论和博客](#学术论文系统讨论和博客)
1. [值得关注的开源软件](#值得关注的开源软件)

如果你从头一直读到了这，那么我对日志的理解你大部分都知道了。

这里再给一些有意思参考资料，你可以再去看看。

人们会用不同的术语描述同一事物，当你从数据库系统到分布式系统、从各类企业级应用软件到广阔的开源世界查看资料时，
这会让人有一些困惑。无论如何，在大方向上还是有一些共同之处。

### 学术论文、系统、讨论和博客

- 关于[状态机](http://www.cs.cornell.edu/fbs/publications/smsurvey.pdf%E2%80%8E)和[主备份](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.20.5896)复制的概述。
- [`PacificA`](http://research.microsoft.com/apps/pubs/default.aspx?id=66814)是实施微软基于日志的分布式存储系统的通用架构。
- [`Spanner`](http://static.googleusercontent.com/external_content/untrusted_dlcp/research.google.com/en/us/archive/spanner-osdi2012.pdf) ——
    并不是每个人都支持把逻辑时间用于他们的日志，`Google`最新的数据库就尝试使用物理时间，并通过把时间戳直接做为区间来直接建时钟迁移的不确定性。
- [`Datanomic`](http://www.datomic.com/)：[解构数据库](https://www.youtube.com/watch?v=Cym4TZwTCNU)是 _Rich Hickey_ （`Clojure`的创建者）在它的首个数据库产品中的重要陈述之一。
- [在消息传递系统中回滚恢复协议的调查](http://www.cs.utexas.edu/~lorenzo/papers/SurveyFinal.pdf)。
    我发现这是有关容错处理和通过日志在数据库之外完成恢复的实际应用的很不错的介绍。
- [反应式宣言（`Reactive Manifesto`）](http://www.reactivemanifesto.org/) ——
    我其实并不清楚反应式编程（`reactive programming`）的确切涵义，但是我想它和『事件驱动』指的是同一件事。
    这个链接并没有太多的信息，但 _Martin Odersky_ （`Scala`大拿）讲授的[这个课程](https://www.coursera.org/course/reactive)很精彩。
- `Paxos`!
    1. 原论文在[这里](http://research.microsoft.com/en-us/um/people/lamport/pubs/lamport-paxos.pdf)。
        关于作者 _Leslie Lamport_ 发表的这篇论文有个有趣的[历史](http://research.microsoft.com/en-us/um/people/lamport/pubs/pubs.html#lamport-paxos)：他在80年代就发明了这个算法，但直到1998年才发表出论文，原因是评审组不喜欢论文中的希腊寓言，而他又不愿修改。
    2. 甚至于论文发布以后，人们还是不怎么理解。_Lamport_ [再次尝试](http://research.microsoft.com/en-us/um/people/lamport/pubs/paxos-simple.pdf)，这次甚至包含了一些『无趣的细节』，关于如何在新型自动化计算机上应用算法的细节。
        但算法仍然没有得到广泛的理解。
    3. [_Fred Schneider_](http://www.cs.cornell.edu/fbs/publications/SMSurvey.pdf)和[_Butler Lampson_](http://research.microsoft.com/en-us/um/people/blampson/58-consensus/Abstract.html)分别给出了更多细节关于在实时系统中如何应用`Paxos`。
    4. 一些`Google`的工程师总结了他们在`Chubby`中实现`Paxos`的[经验](http://www.cs.utexas.edu/users/lorenzo/corsi/cs380d/papers/paper2-1.pdf)。
    5. 我发现所有关于`Paxos`的论文理解起来很痛苦，但是值得我们费大力气弄懂。你不必忍受这样的痛苦了，因为日志结构的文件系统的大师[_John Ousterhout_](http://www.stanford.edu/~ouster/cgi-bin/papers/lfs.pdf)的[这个视频](https://www.youtube.com/watch?v=JEpsBg0AO6o) 让这一切变得相当的容易。这些一致性算法用展开的通信图表述的更好，而不是在论文中通过静态的描述来说明。颇为讽刺的是，这个视频录制的初衷是告诉人们`Paxos`很难理解。
    6. [使用`Paxos`来构造规模一致的数据存储](http://arxiv.org/pdf/1103.2408.pdf)。这是一篇很棒的介绍使用日志来构造数据存储的文章，_Jun_ 是文章的共同作者之一，他也是`Kafka`最早期的工程师之一。
- `Paxos`有很多的竞争者。如下诸项可以更进一步的映射到日志的实施，更适合于实用性的实施。
    1. 由 _Barbara Liskov_ 提出的[视图戳复现](http://pmg.csail.mit.edu/papers/vr-revisited.pdf)是直接进行日志复现建模的较早的算法。
    2. [`Zab`](http://www.stanford.edu/class/cs347/reading/zab.pdf)是`Zookeeper`所使用的算法。
    3. [`RAFT`](https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf)是易于理解的一致性算法之一。由 _John Ousterhout_ 讲授的这个[视频](https://www.youtube.com/watch?v=YbZ3zDzDnrw)非常的棒。
- 你可以的看到在不同的实时分布式数据库中动作日志角色：
    1. [`PNUTS`](https://www.youtube.com/watch?v=YbZ3zDzDnrw)是探索在大规模的传统的分布式数据库系统中实施以日志为中心设计理念的系统。
    2. [`Hbase`](http://hbase.apache.org/)和`Bigtable`都是在目前的数据库系统中使用日志的样例。
    3. `LinkedIn`自己的分布式数据库[`Espresso`](http://www.slideshare.net/amywtang/espresso-20952131)和`PNUTs`一样，使用日志来复现，但有一个小的差异是它使用自己底层的表做为日志的来源。
- 如果你正在做一致性算法选型，[这篇论文](http://arxiv.org/abs/1309.5671)会对你所有帮助。
- [复现：理论与实践](http://www.amazon.com/Replication-Practice-Lecture-Computer-Theoretical/dp/3642112935)，这是收录了分布式系统一致性的大量论文的一本巨著。网上有该书的诸多章节（[1](http://disi.unitn.it/~montreso/ds/papers/replication.pdf)，[4](http://research.microsoft.com/en-us/people/aguilera/stumbling-chapter.pdf)，[5](http://www.distributed-systems.net/papers/2010.verita.pdf)，[6](http://www.cs.cornell.edu/ken/history.pdf)，[7](http://www.pmg.csail.mit.edu/papers/vr-to-bft.pdf)，[8](http://engineering.linkedin.com/distributed-systems/www.cs.cornell.edu/fbs/publications/TrustSurveyTR.pdf)）。
- 流处理：这个话题要总结的内容过于宽泛，但还是有几件我所关注的要提一下：
    1. [在数据流系统中建模和相关事件](http://infolab.usc.edu/csci599/Fall2002/paper/DML2_streams-issues.pdf)：它可能是研究这一领域的最佳概述之一。
    1. [分布处式流处理的高可用性算法](http://cs.brown.edu/research/aurora/hwang.icde05.ha.pdf)。
    1. 随机系统的一些论文：
        - [`TelegraphCQ`](http://db.cs.berkeley.edu/papers/cidr03-tcq.pdf)
        - [`Aurora`](http://cs.brown.edu/research/aurora/vldb03_journal.pdf)
        - [`NiagaraCQ`](http://research.cs.wisc.edu/niagara/papers/NiagaraCQ.pdf)
        - [离散流](http://www.cs.berkeley.edu/~matei/papers/2012/hotcloud_spark_streaming.pdf)：这篇论文讨论了`Spark`的流式系统。
        - [`MillWheel`](http://research.google.com/pubs/pub41378.html) 它是`Google`的流处理系统之一。
        - [`Naiad`：一个实时数据流系统](http://research.microsoft.com/apps/pubs/?id=201100)

企业级软件存在着同样的问题，只是名称不同，或者规模较小，或者是`XML`格式的。哈哈，开个玩笑。

- [事件驱动](http://cs.brown.edu/research/aurora/hwang.icde05.ha.pdf) ——
    据我所知：它就是企业级应用的工程师们常说的『状态机的复现』。有趣的是同样的理念会用在如此迥异的场景中。事件驱动关注的是小的、内存中的使用场景。
    这种机制在应用开发中看起来是把发生在日志事件中的『流处理』和应用关联起来。因此变得不那么琐碎：
    当处理的规模大到需要数据分片时，我关注的是流处理作为独立的首要的基础设施。
- [变更数据捕获](http://en.wikipedia.org/wiki/Change_data_capture) —— 在数据库之外会有些对于数据的舍入处理，这些处理绝大多数都是日志友好的数据扩展。
- [企业级应用集成](http://en.wikipedia.org/wiki/Enterprise_application_integration)，当你有一些现成的类似客户类系管理`CRM`和供应链管理`SCM`的软件时，它似乎可以解决数据集成的问题。
- [复杂事件处理（`CEP`）](http://en.wikipedia.org/wiki/Complex_event_processing)没有人知道它的确切涵义或者它与流处理有什么不同。这些差异看起来集中在无序流和事件过滤、发现或者聚合上，但是依我之见，差别并不明显。我认为每个系统都有自己的优势。
- [企业服务总线（`ESB`）](http://en.wikipedia.org/wiki/Enterprise_service_bus) —— 我认为企业服务总线的概念类似于我所描述的数据集成。在企业级软件社区中这个理念取得了一定程度的成功，对于从事网络和分布式基础架构的工程师们这个概念还是很陌生的。

### 值得关注的开源软件

- [`Kafka`](http://kafka.apache.org/)是把日志作为服务的一个项目，是后边所列各项的基础。
- [`Bookeeper`](http://zookeeper.apache.org/bookkeeper/) 和[`Hedwig`](http://zookeeper.apache.org/bookkeeper/) 另外的两个开源的『把日志作为服务』的项目。它们更关注的是数据库系统内部构件而不是事件数据。
- [`Databus`](https://github.com/linkedin/databus)是提供类似日志的数据库表的覆盖层的系统。
- [`Akka`](http://akka.io/) 是用于`Scala`的`Actor`框架。它有一个[事件驱动](https://github.com/eligosource/eventsourced)的插件，提供持久化和记录。
- [`Samza`](http://samza.apache.org/)是我们在`LinkedIn`中用到的流处理框架，它用到了本文论述的诸多理念，同时与`Kafka`集成来作为底层的日志。
- [`Storm`](http://storm-project.net/)是广泛使用的可以很好的与`Kafka`集成的流处理框架之一。
- [`Spark Streaming`](http://spark.incubator.apache.org/docs/0.7.3/streaming-programming-guide.html)一个流处理框架，是[`Spark`](http://spark.incubator.apache.org/)的一部分。
- [`Summingbird`](https://blog.twitter.com/2013/streaming-mapreduce-with-summingbird)是在`Storm`或`Hadoop`之上的一层，提供了便利的计算抽象。

对于这一领域，我将持续关注，如果您知道一些我遗漏的内容，请您告知。

最后我留给你的信息是这个： :smile_cat:  
[![The Log Song - Ren & Stimpy (Deadwood HoN)](images/log_song.png)](https://www.youtube.com/watch?v=2C7mNr5WMjA)

-----------------

[« 第四部分：系统建设](part4-system-building.md)　　　　[译跋 »](translation-postscript.md)
