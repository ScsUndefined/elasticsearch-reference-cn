# 1.4.1 Updating Documents

除了索引和替换文档，有时候我们也需要更新文档。需要注意的是，Elasticsearch 并没有在一个遮罩下进行就地更新，而是直接一次性地删除旧有文档并索引新文档。

下面这个栗子示范了怎么把先前那个 ID 为 1 的文档的名字更新成“Jane Doe”：

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe" }
}'
```

下面这个栗子则示范了怎么在更新名字的同时新增一个新的“年龄”字段：

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe", "age": 20 }
}'
```

更新操作也可以通过使用简单的脚本语句来执行。不过需要注意的下面这段示例代码中使用到的动态脚本执行功能在`1.4.3`版本后默认是禁用的，更多信息可以自行查阅 [scripting docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting.html) 。使用脚本来把年龄增加 5 岁的示例代码：

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "script" : "ctx._source.age += 5"
}'
```

在上面这段代码中，`ctx._source`会指向要被更新的源文档。


在本文撰写时，一次更新操作只能针对一个文档。而在未来 Elasticsearch 可能提供批量更新满足指定条件的所有文档的功能（类似 SQL 的 UPDATE-WHERE 语句）。

***

In addition to being able to index and replace documents, we can also update documents. Note though that Elasticsearch does not actually do in-place updates under the hood. Whenever we do an update, Elasticsearch deletes the old document and then indexes a new document with the update applied to it in one shot.

This example shows how to update our previous document (ID of 1) by changing the name field to "Jane Doe":

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe" }
}'
```

This example shows how to update our previous document (ID of 1) by changing the name field to "Jane Doe" and at the same time add an age field to it:

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe", "age": 20 }
}'
```

Updates can also be performed by using simple scripts. Note that dynamic scripts like the following are disabled by default as of `1.4.3`, have a look at the [scripting docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting.html) for more details. This example uses a script to increment the age by 5:

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "script" : "ctx._source.age += 5"
}'
```

In the above example, `ctx._source` refers to the current source document that is about to be updated.

Note that as of this writing, updates can only be performed on a single document at a time. In the future, Elasticsearch might provide the ability to update multiple documents given a query condition (like an SQL UPDATE-WHERE statement).