> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [gitbook.cn](https://gitbook.cn/gitchat/column/5ce4ff9a308dd66813d92799/topic/5ce512d2308dd66813d929bd)

> GitChat 是一款基于微信平台的知识分享产品。通过这款产品我们希望改变 IT 知识的学习方式。

Elasticsearch 开箱即用，上手十分容易。安装、启动、创建索引、索引数据、查询结果，整个过程，无需修改任何配置，无需了解 mapping，运作起来，一切都很容易。

这种容易是建立在 Elasticsearch 在幕后悄悄为你设置了很多默认值，但正是这种容易、这种默认的设置可能会给以后带来痛苦。

例如不但想对 field 做精确查询，还想对同一字段进行全文检索怎么办？shard 数不合理导致无法水平扩展怎么办？出现这些状况，大部分情况下需要通过修改默认的 mapping，然后 reindex 你的所有数据。

这是一个很重的操作，需要很多的资源。索引设计是否合理，会影响以后集群运行的效率和稳定性。

### 1 分析业务

当我们决定引入 Elasticsearch 技术到业务中时，根据其本身的技术特点和应用的经验，梳理出需要预先明确的需求，包括物理需求、性能需求。

在初期应用时，由于对这两方面的需求比较模糊，导致后期性能和扩展性方面无法满足业务需求，浪费了很多资源进行调整。

希望我总结的需求方面的经验能给将要使用 Elasticsearch 的同学提供一些帮助，少走一些弯路。下面分别详细描述。

#### 1.1 物理需求

根据我们的经验，在设计 Elasticsearch 索引之前，首先要合理地估算自己的物理需求，物理需求指数据本身的物理特性，包括如下几方面。

*   数据总量

业务所涉及的领域对象预期有多少条记录，对 Elasticsearch 来说就是有多少 documents 需要索引到集群中。

*   单条数据大小

每条数据的各个属性的物理大小是多少，比如 1k 还是 10k。

*   长文本

明确数据集中是否有长文本，明确长文本是否需要检索，是否可以启用压缩。Elasticsearch 建索引的过程是极其消耗 CPU 的，尤其对长文本更是如此。

明确了长文本的用途并合理地进行相关设置可以提高 CPU、磁盘、内存利用率。我们曾遇见过不合理的长文本处理方式导致的问题，此处在 mapping 设计时会专门讨论。

*   物理总大小

根据上面估算的数据总量和单条数据大小，就可以估算出预期的存储空间大小。

*   数据增量方式

这里主要明确数据是以何种方式纳入 Elasticsearch 的管理，比如平稳增加、定期全量索引、周期性批量导入。针对不同的数据增量方式，结合 Elasticsearch 提供的灵活设置，可以最大化地提高系统的性能。

*   数据生命周期

数据生命周期指进入到系统的数据保留周期，是永久保留、还是随着时间推移进行老化处理？老化的周期是多久？既有数据是否会更新？更新率是多少？根据不同的生命周期，合理地组织索引，会达到更好的性能和资源利用率。

#### 1.2 性能需求

使用任何一种技术，都要确保性能能够满足业务的需求，根据上面提到的业务场景，对于 Elasticssearch 来说，核心的两个性能指标就是索引性能和查询性能。

##### **索引性能需求**

Elasticsearch 索引过程需要对待索引数据进行文本分析，之后建立倒排索引，是个十分消耗 CPU 资源的过程。

对于索引性能来说，我们认为需要明确两个指标，一个是吞吐量，即单位时间内索引的数据记录数；另一个关键的指标是延时，即索引完的数据多久能够被检索到。

Elasticsearch 在索引过程中，数据是先写入 buffer 的，需要 refresh 操作后才能被检索到，所以从数据被索引到能被检索到之间有一个延迟时间，这个时间是可配置的，默认值是 1s。这两个指标互相影响：减少延迟，会降低索引的吞吐量；反之会增加索引的吞吐量。

##### **查询性能需求**

数据索引存储后的最终目的是查询，对于查询性能需求。Elasticsearch 支持几种类型的查询，包括：

*   1. 结构化查询

结构查询主要是回答 yes/no，结构化查询不会对结果进行相关性排序。如 terms 查询、bool 查询、range 查询等。

*   2. 全文检索

全文检索查询主要回答数据与查询的相关程度。如 match 查询、query_string 查询。

*   3. 聚合查询

无论结构化查询和全文检索查询，目的都是找到某些满足条件的结果，聚合查询则不然，主要是对满足条件的查询结果进行统计分析，例如平均年龄是多少、两个 IP 之间的通信情况是什么样的。

对不同的查询来说，底层的查询过程和对资源的消耗是不同的，我们建议根据不同的查询设定不同的性能需求。

### 2 索引设计

**此处索引设计指宏观方面的索引组织方式，即怎样把数据组织到不同的索引，需要以什么粒度建立索引，不涉及如何设计索引的 mapping。（mapping 后文单独讲）**

#### 2.1 按照时间周期组织索引

如果查询中有大量的关于时间范围的查询，分析下自己的查询时间周期，尽量按照周期（小时、日、周、月）去组织索引，一般的日志系统和监控系统都符合此场景。

按照日期组织索引，不但可以减少查询时参与的 shard 数量，而且对于按照周期的**数据老化**、**备份**、**删除**的处理也很方便，基本上相当于文件级的操作性能。

这里有必要提一下 `delete_by_query`，这种数据老化方式性能慢，而且执行后，底层并不一定会释放磁盘空间，后期 merge 也会有很大的性能损耗，对正常业务影响巨大。

#### 2.2 拆分索引

检查查询语句的 filter 情况，如果业务上有大量的查询是基于一个字段 filter，比如 protocol，而该字段的值是有限的几个值，比如 HTTP、DNS、TCP、UDP 等，最好把这个索引拆成多个索引。

这样每次查询语句中就可以去掉 filter 条件，只针对相对较小的索引，查询性能会有很大提高。同时，如果需要查询跨协议的数据，也可以在查询中指定多个索引来实现。

#### 2.3 使用 routing

如果查询语句中有比较固定的 filter 字段，但是该字段的值又不是固定的，我们建议在创建索引时，启用 routing 功能。这样，数据就可以按照 filter 字段的值分布到集群中不同的 shard，使参与到查询中的 shard 数减少很多，极大提高 CPU 的利用率。

#### 2.4 给索引设置别名

我们强烈建议在任何业务中都使用别名，绝不在业务中直接引用具体索引！

##### **别名是什么**

索引别名就像一个快捷方式，可以指向一个或者多个索引，我个人更愿意把别名理解成一个逻辑名称。

##### **别名的好处**

*   方便扩展

对于无法预估集群规模的场景，在初期可以创建单个分片的索引 index-1，用别名 alias 指向该索引，随着业务的发展，单个分片的性能无法满足业务的需求，可以很容易地创建一个两个分片的索引 index-2，在不停业务的情况下，用 alise 指向 index-2，扩展简单至极。

*   修改 mapping

业务中难免会出现需要修改索引 mapping 的情况，修改 mapping 后历史数据只能进行 reindex 到不同名称的索引，如果业务直接使用具体索引，则不得不在 reindex 完成后修改业务索引的配置，并重启服务。业务端只使用别名，就可以在线无缝将 alias 切换到新的索引。

#### 2.5 使用 Rollover index API 管理索引生命周期

对于像日志等滚动生成索引的数据，业务经常以天为单位创建和删除索引。在早期的版本中，由业务层自己管理索引的生命周期。

在 Rollover index API 出现之后，我们可以更方便更准确地进行管理：索引的创建和删除操作在 Elasticsearch 内部实现，业务层先定义好模板和别名，再定期调用一下 API 即可自动完成，索引的切分可以按时间、或者 DOC 数量来进行。

### 总结

在正式接入业务数据之前进行合理的索引设计是一个必要的环节，如果偷懒图方便用最简单的方式进行业务数据接入，问题就会在后期暴露出来，那时再想解决就困难许多。

下一节我们开始介绍**索引层面之下的分片设计**。

> 为了方便学习和技术交流，创建了高可用 Elasticsearch 集群的读者群，入群方式在第 1-3 课，欢迎已购课程的同学入群交流。
> 
> [点击了解《高可用 Elasticsearch 集群 21 讲》](https://gitbook.cn/gitchat/column/5ce4ff9a308dd66813d92799?columnId=5ce4ff9a308dd66813d92799&utm_source=ESzcsd001)。