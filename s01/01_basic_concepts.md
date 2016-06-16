# Basic Concepts

Elascticsearch 有一些核心的概念，在一开始的时候就透彻地理解这些概念可以极大地方便你进一步的学习。

**近乎实时（NRT）**

Elasticsearch 是一个近乎实时的搜索平台。说近乎，是因为从你对一个文档进行创建索引开始到这个文档变得可以被搜索之间可能有一个潜在的传输延迟时间（通常情况下是1秒）。

**集群**

所谓集群就是单个或多个节点（服务器）为一组，持有你完整的数据并联合起来提供创建索引和搜索的功能。

不要在不同的环境下使用相同的集群名，不然你的节点最后可能加入了错误的集群中。比如你可以使用 logging-dev, logging-stage, and logging-prod 对应你的开发环境、测试环境和生产环节的集群名。

要注意的是，一个集群中仅含有单个节点也是有效的，而且一样可以运行得很好。此外，你也可以同时拥有多个有着不同名字的集群。

**节点**

一个节点就是你的集群中的一个单独的服务器，它存储有你的数据，并为整个集群的构建索引功能和查询功能出一份力。和集群一样，节点也是通过节点名来辨识的。默认情况下，节点在启动时会被赋予一个随机的漫威角色名（漫威角色名有限啊，分光了之后会咋办？。。。）。节点名作为集群中各个节点的区分标识，就像人的身份证号一样有着重要的管理意义。

通过修改节点所属的集群名，可以手动地将该节点加入到指定的集群中。默认情况下，节点会加入一个叫做 elasticsearch 的集群中，也就是说如果你开启了多个节点，那它们就会自动地组成一个叫做 elasticsearch 的集群，前提是它们在同一个网络中并且能够发现彼此。

在一个集群中，你想开设多少个节点就可以开设多少个节点。此外，如果当前网络中没有任何集群可以给你加入，而此时你又新开设了一个节点，那这个节点默认情况下就会自己组成一个名叫 elasticsearch 的单节点集群。

**索引**

一个索引就是一批有着相似特征的文档的集合。比如说，你可以有一个索引对应你的顾客信息，另一个索引对应你的产品信息以及还有一个索引对应你的订单信息。索引是通过索引名来区分的（索引名必须是全小写格式），当你需要对一个索引中的某个文档进行索引操作，搜索操作，更新操作或者删除操作时，该操作就会通过索引名来定位到需要在哪个索引上执行该操作。

在一个集群中你想构建多少个索引就可以构建多少个。

**类型**

在一个索引中，你可以定义一个或多个类型。类型就是你索引的一个逻辑分类/分割，它的语义完全由你决定。通常情况下，一个文档可以定义有多个类型。举个栗子，让我们假定你正在运行一个博客系统，然后你把你所有的数据都定义在了一个索引中。在这个索引里面，你可以为用户数据定义一种类型，为你的博客数据定义一种类型，然后为评论信息定义一种类型。

**文档**

所谓文档就是可以被索引的数据信息的一个基本单元。再举个大栗子，你可以有一个对应了单个顾客的文档，一个对应了某个产品的文档以及一个对应了单张订单的文档。这些文档以 JSON (JavaScript Object Notation)化的形式表示。JSON 格式现在也几乎是一个无处不在的互联网数据交换格式。

在一个索引或类型中，你可以存储任意数量的文档。要注意的是，尽管一个文档物理上存在于一个索引中，但其实上它必须被索引/被分配到索引的某个类型上。

**分片&副本**

一个索引可以存储贼多的数据，这可能触及单台设备的性能瓶颈。比如一个存有数十亿条文档的索引需要占据 1 TB 的磁盘空间，可能一台设备上都没有 1 TB 的空间给你塞，或者即使有，也会导致查询速度变慢。

为了解决这个问题，Elasticsearch 允许你将单个索引中的海量数据切分到多个名叫分片的东西中去。当你创建一个索引的时候，你就可以定义你所需要的分片数量。每个分片在它自身内部都是一个有着完整功能的并且独立的“索引”，可以被托管在集群中的任意一个节点中。

有两个原因使得分片非常重要：
* 它使你能够横向地切分/拓展你的内容大小
* 它使你能够在多个分片（通常都分布在不同的节点中）中分发和同步操作，从而提升性能和吞吐量

分片是如何分布的，又是如何在检索请求到来时聚合的，这其中的机制都是完全由 Elasticsearch 来管理的。而对于身为用户的你而言，这一机制是完全透明的。

在一个 网络/云 环境中，任何时候都可能会出故障，所以建立一套故障转移机制将会是非常游泳并且也应该被强烈推荐的做法，以防万一哪天某个分片/节点莫名其妙地掉线或者干脆直接消失了（简直辣鸡）。为此，Elasticsearch 允许你为你索引的分片设置一个或多个副本分片，或者简称副本。

这也有两个使得副本非常重要的两个理由：

* 它提供了一个高可用性，避免了单分片/节点故障所带来的致命影响。也因此有一点至关中欧哪个要，那就是绝逼不要把副本分片和它对应的原始分片或者也叫主分片的东西存放在同一个节点下。
* 它允许你扩展你的搜索量/吞吐量，因为搜索可以在所有副本中并行执行。

总结一下，每个索引都可以被切分为多个分片。一个索引可以被复制零（也就是不进行复制）或多次。一旦被复制，每个索引就将会有多个主分片（被复制的原始分片）和副本分片（原始分片的复制品）。主分片和副本分片的数量可以在每次索引被创建按的时候设置。当索引已经创建好了之后，你任何时候都可以动态地改变副本分片的数量，但是你不能改变事后再改变主分片的数量。

>**提醒**
>
>每个 Elasticsearch 分片都是一个 Lucene 索引。而单个 Lucene 索引中可存储的文档数量是有数量限制的。对于 LUCENE-5843 而言，存储上限是 2,147,483,519 (= Integer.MAX_VALUE - 128)个文档。你可以使用 cat/shards api 来监控分片大小

哔哔完这些枯燥无味的概念之后，让我们开始正式的 elasticsearch 欢乐行吧～～～

***

There are a few concepts that are core to Elasticsearch. Understanding these concepts from the outset will tremendously help ease the learning process.

**Near Realtime (NRT)**

Elasticsearch is a near real time search platform. What this means is there is a slight latency (normally one second) from the time you index a document until the time it becomes searchable.

**Cluster**

A cluster is a collection of one or more nodes (servers) that together holds your entire data and provides federated indexing and search capabilities across all nodes. A cluster is identified by a unique name which by default is "elasticsearch". This name is important because a node can only be part of a cluster if the node is set up to join the cluster by its name.

Make sure that you don’t reuse the same cluster names in different environments, otherwise you might end up with nodes joining the wrong cluster. For instance you could use `logging-dev`, `logging-stage`, and `logging-prod` for the development, staging, and production clusters.

Note that it is valid and perfectly fine to have a cluster with only a single node in it. Furthermore, you may also have multiple independent clusters each with its own unique cluster name.

**Node**

A node is a single server that is part of your cluster, stores your data, and participates in the cluster’s indexing and search capabilities. Just like a cluster, a node is identified by a name which by default is a random Marvel character name that is assigned to the node at startup. You can define any node name you want if you do not want the default. This name is important for administration purposes where you want to identify which servers in your network correspond to which nodes in your Elasticsearch cluster.

A node can be configured to join a specific cluster by the cluster name. By default, each node is set up to join a cluster named `elasticsearch` which means that if you start up a number of nodes on your network and—assuming they can discover each other—they will all automatically form and join a single cluster named `elasticsearch`.

In a single cluster, you can have as many nodes as you want. Furthermore, if there are no other Elasticsearch nodes currently running on your network, starting a single node will by default form a new single-node cluster named `elasticsearch`.

**Index**

An index is a collection of documents that have somewhat similar characteristics. For example, you can have an index for customer data, another index for a product catalog, and yet another index for order data. An index is identified by a name (that must be all lowercase) and this name is used to refer to the index when performing indexing, search, update, and delete operations against the documents in it.

In a single cluster, you can define as many indexes as you want.

**Type**

Within an index, you can define one or more types. A type is a logical category/partition of your index whose semantics is completely up to you. In general, a type is defined for documents that have a set of common fields. For example, let’s assume you run a blogging platform and store all your data in a single index. In this index, you may define a type for user data, another type for blog data, and yet another type for comments data.

**Document**

A document is a basic unit of information that can be indexed. For example, you can have a document for a single customer, another document for a single product, and yet another for a single order. This document is expressed in [JSON](http://json.org/) (JavaScript Object Notation) which is an ubiquitous internet data interchange format.

Within an index/type, you can store as many documents as you want. Note that although a document physically resides in an index, a document actually must be indexed/assigned to a type inside an index.

**Shards & Replicas**

An index can potentially store a large amount of data that can exceed the hardware limits of a single node. For example, a single index of a billion documents taking up 1TB of disk space may not fit on the disk of a single node or may be too slow to serve search requests from a single node alone.

To solve this problem, Elasticsearch provides the ability to subdivide your index into multiple pieces called shards. When you create an index, you can simply define the number of shards that you want. Each shard is in itself a fully-functional and independent "index" that can be hosted on any node in the cluster.

Sharding is important for two primary reasons:

* It allows you to horizontally split/scale your content volume
* It allows you to distribute and parallelize operations across shards (potentially on multiple nodes) thus increasing performance/throughput

The mechanics of how a shard is distributed and also how its documents are aggregated back into search requests are completely managed by Elasticsearch and is transparent to you as the user.

In a network/cloud environment where failures can be expected anytime, it is very useful and highly recommended to have a failover mechanism in case a shard/node somehow goes offline or disappears for whatever reason. To this end, Elasticsearch allows you to make one or more copies of your index’s shards into what are called replica shards, or replicas for short.

Replication is important for two primary reasons:

* It provides high availability in case a shard/node fails. For this reason, it is important to note that a replica shard is never allocated on the same node as the original/primary shard that it was copied from.
* It allows you to scale out your search volume/throughput since searches can be executed on all replicas in parallel.

To summarize, each index can be split into multiple shards. An index can also be replicated zero (meaning no replicas) or more times. Once replicated, each index will have primary shards (the original shards that were replicated from) and replica shards (the copies of the primary shards). The number of shards and replicas can be defined per index at the time the index is created. After the index is created, you may change the number of replicas dynamically anytime but you cannot change the number shards after-the-fact.

By default, each index in Elasticsearch is allocated 5 primary shards and 1 replica which means that if you have at least two nodes in your cluster, your index will have 5 primary shards and another 5 replica shards (1 complete replica) for a total of 10 shards per index.

>Note
>
>Each Elasticsearch shard is a Lucene index. There is a maximum number of documents you can have in a single Lucene index. As of [LUCENE-5843](https://issues.apache.org/jira/browse/LUCENE-5843), the limit is 2,147,483,519 (= Integer.MAX_VALUE - 128) documents. You can monitor shard sizes using the [\_cat/shards](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-shards.html) api.

With that out of the way, let’s get started with the fun part…
