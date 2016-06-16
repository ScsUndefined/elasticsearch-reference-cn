# Cluster Health

让我们来一个基础的健康检查吧，这样我们就可以看到咱的集群的健康状况了。我们会用 curl 命令来演示，但其实你可以随便使用什么工具，只要它能发起 HTTP/REST 请求就行（比如用 java 程序来执行 HTTP 请求或者写个 HTML 页面放到浏览器里访问）。我们先假定你刚运行好你的 elasticsearch 节点，然后打开了另一个 shell 窗口。

要检查集群的健康状况，我们就需要使用 `_cat` API。记得前面我们说过，HTTP 的端口号是 9200：

```bash
curl 'localhost:9200/_cat/health?v'
```

大概你会看到下面这段响应信息

```bash
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign
1394735289 14:28:09  elasticsearch green           1         1      0   0    0    0        0
```

我们可以看到我们的集群被命名成 elasticsearch 并且正在运行中，健康状态是 green 绿色的。

不管我们是在什么样的情况下检查集群的健康状态，我们都只会获取到 3 种状态值，绿，黄或红。绿表示（帽子？）一切都很好（集群是功能完整的状态），黄表示所有的数据都是可获取的，但是某些副本节点还没有被分配（集群依然是功能完整的状态），红表示有一些数据不知道什么原因已经无法获取了。需要注意的是，如果一个集群的状态是红色的，那它依然是有部分功能可用的（比如它依然可以从那些可用的分片上检索数据）但你可能需要尽快修复它了，而且你可能已经丢失了部分数据。

通过上面那个响应信息里，我们可以看到我们的集群中只有一个节点并且没有任何分片，因为我们还没有开始存储信息。要注意的是，我们采用的是默认的集群名（elasticsearh），另外，Elasticsearch 默认采用的是单播网络发现的方式来检索同一台机器上的其他节点，所以你可能偶然地在自己电脑上重复运行了多个节点，然后它们就自动组建成了单个集群。在这种情况下，上面那个响应信息里你就会看到不止一个节点。

我们也可以用下面这个命令来获取我们的节点清单：

```bash
  curl 'localhost:9200/_cat/nodes?v'
```

响应：

```bash
curl 'localhost:9200/_cat/nodes?v'
host         ip        heap.percent ram.percent load node.role master name
mwubuntu1    127.0.1.1            8           4 0.00 d         *      New Goblin
```

在这个响应体里我们可以看到我们有一个“绿魔二世”的节点，自己跟自己组建成了一个集群。（感觉好孤独的样子。。。。）

***

Let’s start with a basic health check, which we can use to see how our cluster is doing. We’ll be using curl to do this but you can use any tool that allows you to make HTTP/REST calls. Let’s assume that we are still on the same node where we started Elasticsearch on and open another command shell window.

To check the cluster health, we will be using the [\_cat API](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html). Remember previously that our node HTTP endpoint is available at port `9200`:

```bash
curl 'localhost:9200/_cat/health?v'
```

And the response:

```bash
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign
1394735289 14:28:09  elasticsearch green           1         1      0   0    0    0        0
```

We can see that our cluster named "elasticsearch" is up with a green status.

Whenever we ask for the cluster health, we either get green, yellow, or red. Green means everything is good (cluster is fully functional), yellow means all data is available but some replicas are not yet allocated (cluster is fully functional), and red means some data is not available for whatever reason. Note that even if a cluster is red, it still is partially functional (i.e. it will continue to serve search requests from the available shards) but you will likely need to fix it ASAP since you have missing data.

Also from the above response, we can see and total of 1 node and that we have 0 shards since we have no data in it yet. Note that since we are using the default cluster name (elasticsearch) and since Elasticsearch uses unicast network discovery by default to find other nodes on the same machine, it is possible that you could accidentally start up more than one node on your computer and have them all join a single cluster. In this scenario, you may see more than 1 node in the above response.

We can also get a list of nodes in our cluster as follows:

```bash
curl 'localhost:9200/_cat/nodes?v'
```

And the response:

```bash
curl 'localhost:9200/_cat/nodes?v'
host         ip        heap.percent ram.percent load node.role master name
mwubuntu1    127.0.1.1            8           4 0.00 d         *      New Goblin
```

Here, we can see our one node named "New Goblin", which is the single node that is currently in our cluster.
