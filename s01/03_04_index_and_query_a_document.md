# Index and Query a Document

现在让咱们来在咱的顾客索引里塞一个什么数据吧。记得我们之前说过的，如果我们想对一个文档进行创建索引操作的话，我们得告诉 Elasticsearch 我们需要把这个文档的索引设置成什么类型。

让我们把下面这个文档索引到我们的顾客索引中，把它指定成“外部”类型，并且 ID 设置成 1：

我们的 JSON 格式的文档： { "name": "John Doe" }

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
```
响应信息：

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "created" : true
}
```

通过上面的响应信息，我们就可以看到一条新的顾客文档就已经被成功创建到了顾客索引的“外部”类型中。这条文档同时也有一个我们在索引它时指定的，值为 1 的 ID。

需要着重注明的是，Elasticsearch 并不强制要求你在往某个索引的某个类型中存储数据前事前创建好这个索引。在上面这个示例中，如果没有事先“顾客”索引没有被创建好的话，Elasticsearch 也会自动帮你创建。（区别于传统的关系数据库，需要你先创建好数据库，再创建数据库表，最后才能再插入数据）

现在让我们试着取回我们刚才存储的数据：

```bash
curl -XGET 'localhost:9200/customer/external/1?pretty'
```

响应信息：

```bash
curl -XGET 'localhost:9200/customer/external/1?pretty'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}
```

在返回信息里出了一个“found”字段和“\_source”字段，其他都没什么好稀奇的。而这个“found”意思是说我们找到了那个我们想要的,ID 是 1 的文档，“\_source”则返回了我们在上个步骤中存储的那个 JSON 格式的文档的整个内容。

***

Let’s now put something into our customer index. Remember previously that in order to index a document, we must tell Elasticsearch which type in the index it should go to.

Let’s index a simple customer document into the customer index, "external" type, with an ID of 1 as follows:

Our JSON document: { "name": "John Doe" }

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
```

And the response:

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "created" : true
}
```

From the above, we can see that a new customer document was successfully created inside the customer index and the external type. The document also has an internal id of 1 which we specified at index time.

It is important to note that Elasticsearch does not require you to explicitly create an index first before you can index documents into it. In the previous example, Elasticsearch will automatically create the customer index if it didn’t already exist beforehand.

Let’s now retrieve that document that we just indexed:

```bash
curl -XGET 'localhost:9200/customer/external/1?pretty'
```

And the response:

```bash
curl -XGET 'localhost:9200/customer/external/1?pretty'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}
```

Nothing out of the ordinary here other than a field, found, stating that we found a document with the requested ID 1 and another field, _source, which returns the full JSON document that we indexed from the previous step.



