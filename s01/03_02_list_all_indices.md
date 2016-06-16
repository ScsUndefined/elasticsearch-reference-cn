# List All Indices

现在让我们瞥一眼我们的索引：

```bash
curl 'localhost:9200/_cat/indices?v'
```

得到的响应信息如下：

```bash
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
```

这个响应信息简单来说就是我们集群中还没有索引。

***

Now let’s take a peek at our indices:

```bash
curl 'localhost:9200/_cat/indices?v'
```

And the response:

```bash
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
```

Which simply means we have no indices yet in the cluster.