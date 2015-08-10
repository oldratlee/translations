原文链接： [Hard commits, soft commits and transaction logs](http://lucidworks.com/blog/understanding-transaction-logs-softcommit-and-commit-in-sorlcloud/)

关于硬提交、软提交和事务
=====================================

![solr](solr-logo.png)

在`Solr` `4.0`中，新增了软提交（`soft commit`）功能和硬提交（`hard commit`）的一个新参数。
然后，软提交和硬提交之间的相互影响有些让人迷惑，*特别*是两者都是用于事务日志的。
储存文件`solrconfig.xml`中解释了这些选项，但由于在示例中的文档中能说明得不多，如果要做_完整_的说明，
这个示例文件可能会有10M，没有人会去通读。本文概述了硬提交和软提交以及用于硬提交新的`openSearcher`选项的作用。
发布说明文档在[`Solr参考指南`](https://cwiki.apache.org/confluence/display/solr/UpdateHandlers+in+SolrConfig)中有，本方是这个主题一个更轻松的介绍。
我咨询过几个提交者，拿到了一个细节。我确保信息的准确性，如有任何转述的错误责任在我。

口诀心法
--------------








请记住，极少情况需要优化索引。

来开心地索引吧！
