# 1.4.3 Batch Processing

除了能够分开地进行索引，更新和删除文档操作外，Elasticsearch 也提供一个 [\_bulk API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html) 来让你能够批量地执行这些操作。这个功能非常重要，因为当你需要执行多个操作的时候，它可以减少你的网络请求次数从而加快你操作的执行速度。

一个小栗子，下面这段代码会在一个批处理操作中同时索引出2个文档（ID 1 - John Doe 和 ID 2 - Jane Doe）：

```bash
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'
```

而下面这个栗子会在一个批处理操作中先更新第一个文档（ID 号为 1）然后删除第二个文档（ID 号为 2）:

```bash
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'
```

注意一下上面那个栗子中的删除操作的部分，删除操作并没有紧跟一个相对应的源文档，因为删除操作只需要一个 ID 号就够了。

批处理命令会循序地执行所有的子操作。如果其中有的操作因为什么什么原因而失败了，那它就会继续执行下一个操作。当整个批处理请求返回时，它也会循序地打印出每个子操作的执行状态，你可以以此来检验某个特定的子操作是否执行成功。

***

In addition to being able to index, update, and delete individual documents, Elasticsearch also provides the ability to perform any of the above operations in batches using the [_bulk API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html). This functionality is important in that it provides a very efficient mechanism to do multiple operations as fast as possible with as little network roundtrips as possible.

As a quick example, the following call indexes two documents (ID 1 - John Doe and ID 2 - Jane Doe) in one bulk operation:
```bash
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'
```
This example updates the first document (ID of 1) and then deletes the second document (ID of 2) in one bulk operation:
```bash
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'
```
Note above that for the delete action, there is no corresponding source document after it since deletes only require the ID of the document to be deleted.

The bulk API executes all the actions sequentially and in order. If a single action fails for whatever reason, it will continue to process the remainder of the actions after it. When the bulk API returns, it will provide a status for each action (in the same order it was sent in) so that you can check if a specific action failed or not.