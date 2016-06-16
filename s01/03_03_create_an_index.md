# Create an Index

让我们创建一个名叫“customer”，即顾客的意思，的索引，然后再列举出所有的索引。

```bash
curl -XPUT 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
```

第一行命令使用 PUT 动作创建了一个名叫“customer”的索引。我们简单地把`pretty`附加到请求地址末尾以要求 elasticsearch 在返回 JSON 响应信息时美化它一下（如果有的话）。

返回信息如下：

```bash
curl -XPUT 'localhost:9200/customer?pretty'
{
  "acknowledged" : true
}

curl 'localhost:9200/_cat/indices?v'
health index    pri rep docs.count docs.deleted store.size pri.store.size
yellow customer   5   1          0            0       495b           495b
```

第二行命令的结果告诉我们，我们现在已经有了一个叫“customer”的索引，并且它已经有了5个主分片和一个副本（默认配置）并且它内部存储了 0 个文档。

你或许也主要到了这个“customer”索引有一个黄色的健康标识。回想一下，我们之前讨论过了黄色意味着有些副本还没有被分配。之所以这种情况会发生是因为 Elasticsearch 默认情况下只会给索引创建一个副本。而现在我们只有一个节点在运行，而还有一个副本还没有被分配（以便获得高可用性）除非之后某个时间点有其他节点加入到这个集群中。一旦副本在另一个节点上被分配，那么健康标识就会变成绿。

***

Now let’s create an index named "customer" and then list all the indexes again:

```bash
curl -XPUT 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
```

The first command creates the index named "customer" using the PUT verb. We simply append pretty to the end of the call to tell it to pretty-print the JSON response (if any).

And the response:

```bash
curl -XPUT 'localhost:9200/customer?pretty'
{
  "acknowledged" : true
}
```

curl 'localhost:9200/_cat/indices?v'
health index    pri rep docs.count docs.deleted store.size pri.store.size
yellow customer   5   1          0            0       495b           495b
The results of the second command tells us that we now have 1 index named customer and it has 5 primary shards and 1 replica (the defaults) and it contains 0 documents in it.

You might also notice that the customer index has a yellow health tagged to it. Recall from our previous discussion that yellow means that some replicas are not (yet) allocated. The reason this happens for this index is because Elasticsearch by default created one replica for this index. Since we only have one node running at the moment, that one replica cannot yet be allocated (for high availability) until a later point in time when another node joins the cluster. Once that replica gets allocated onto a second node, the health status for this index will turn to green.