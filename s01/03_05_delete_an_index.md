# Delete an Index
现在我们来试试看怎么删除索引。先试着将刚才创建的顾客索引删掉，然后再列出所有的索引清单，看看顾客索引是不是真的被成功删掉了。

```bash
curl -XDELETE 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
```

响应信息：

```bash
curl -XDELETE 'localhost:9200/customer?pretty'
{
  "acknowledged" : true
}
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
```

这个响应信息意味着我们的确已经成功把顾客索引给删了，现在集群又回到了最初的一无所有的样子了（鳖再说你一无所有了。。。你这不是还有病嘛(⊙\_⊙)）。

在我们继续深入学习之前，我们现在简单温习下我们之前所学习过的一些 API 命令：

```bash
curl -XPUT 'localhost:9200/customer'
curl -XPUT 'localhost:9200/customer/external/1' -d '
{
  "name": "John Doe"
}'
curl 'localhost:9200/customer/external/1'
curl -XDELETE 'localhost:9200/customer'
```

如果你够细心，或许你已经发现这些命令其实有一个通用的模式。这个模式可以简单描述成：

```bash
curl -X<REST 动作名> <节点>:<端口号>/<索引名>/<类型名>/<ID>
```

所有的 API 命令都是采用的这种 REST 访问模式，所以如果你早早地就记住它，那你在学习 Elasticsearch 时就已经有了一个良好的开始。

***

Now let’s delete the index that we just created and then list all the indexes again:

```bash
curl -XDELETE 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
```

And the response:

```bash
curl -XDELETE 'localhost:9200/customer?pretty'
{
  "acknowledged" : true
}
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
```

Which means that the index was deleted successfully and we are now back to where we started with nothing in our cluster.

Before we move on, let’s take a closer look again at some of the API commands that we have learned so far:

```bash
curl -XPUT 'localhost:9200/customer'
curl -XPUT 'localhost:9200/customer/external/1' -d '
{
  "name": "John Doe"
}'
curl 'localhost:9200/customer/external/1'
curl -XDELETE 'localhost:9200/customer'
```

If we study the above commands carefully, we can actually see a pattern of how we access data in Elasticsearch. That pattern can be summarized as follows:

```bash
curl -X<REST Verb> <Node>:<Port>/<Index>/<Type>/<ID>
```

This REST access pattern is pervasive throughout all the API commands that if you can simply remember it, you will have a good head start at mastering Elasticsearch.