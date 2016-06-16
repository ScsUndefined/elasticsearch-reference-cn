# 1.5.1 The Search API

现在我们开始进行一些示例性的查询操作。执行查询操作的方式有两种：一种是通过 [REST request URI](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html) 来发送查询参数而另一种则是通过 [REST request body](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html) 来发送。第二种方法功能更丰富而且允许你使用一种可读性更强的 JSON 格式来表示你的查询条件。我们会演示一次 request URI 方法但是本教程的剩余部分，我们只会使用 request body 方法来执行查询操作。

搜索功能的 REST API 都可以通过以`_search`结尾来调用。下面这个示例会返回 bank 索引中的所有文档：

```bash
curl 'localhost:9200/bank/_search?q=*&pretty'
```

让我们现在讲解一下这个查询请求。我们正在查询 bank 索引，而`q=*`参数告诉 Elasticsearch 去匹配索引中的所有文档。`pretty`参数的作用和之前一样，只是为了告诉 Elasticsearch 把返回的 JSON 数据美化一样，提升下可读性。

返回结果的部分：

```bash
curl 'localhost:9200/bank/_search?q=*&pretty'
{
  "took" : 63,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
```

在这个响应信息中，我们可以看到有以下几个部分：

* `took` — Elasticsearch 执行该操作所花费的时间，单位是毫秒。
* `timed_out` — 告诉我们查询操作是否已经超时
* `_shards` — 告诉我们本次查询操作涉及到了多少个分片，包括查询成功和失败的分片数
* `hits` – 查询结果
* `hits.total` – 满足我们的查询条件的文档数
* `hits.hits` – 搜索结果的数据展示（默认只显示前 10 个匹配的搜索结果）
* `_score` and `max_score` - 暂时不讨论这个

下面是使用第二种方法来执行同样的搜索操作：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
```
和第一种方法的不同之处在于，第二种方法不再在 URI 中传递`q=*`，我们 POST 一个 JSON 格式的查询请求体给`_search`API。我们将在下一个章节来讨论这个 JSON。

然后是响应（只显示了部分）

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
{
  "took" : 26,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "13",
```

有一点必须要清楚，当 Elasticsearch 返回了你的查询结果后，那这个查询请求就完全结束了，Elasticsearch 不会维持任何与你搜索结果相关联的服务端资源或开放型指针。这和许多其他平台都明显不同，比如在 SQL 中，你可以使用某种带状态的服务器端指针，来先获取部分查询结果，然后你可以继续回到服务器上接着获取剩余的搜索结果（或者在分页查询时进行换页操作）

***

Now let’s start with some simple searches. There are two basic ways to run searches: one is by sending search parameters through the [REST request URI](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html) and the other by sending them through the [REST request body](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html). The request body method allows you to be more expressive and also to define your searches in a more readable JSON format. We’ll try one example of the request URI method but for the remainder of this tutorial, we will exclusively be using the request body method.

The REST API for search is accessible from the `_search` endpoint. This example returns all documents in the bank index:

```bash
curl 'localhost:9200/bank/_search?q=*&pretty'
```

Let’s first dissect the search call. We are searching (`_search` endpoint) in the bank index, and the `q=*` parameter instructs Elasticsearch to match all documents in the index. The `pretty` parameter, again, just tells Elasticsearch to return pretty-printed JSON results.

And the response (partially shown):

```bash
curl 'localhost:9200/bank/_search?q=*&pretty'
{
  "took" : 63,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
```

As for the response, we see the following parts:

* `took` – time in milliseconds for Elasticsearch to execute the search
* `timed_out` – tells us if the search timed out or not
* `_shards` – tells us how many shards were searched, as well as a count of the successful/failed searched shards
* `hits` – search results
* `hits.total` – total number of documents matching our search criteria
* `hits.hits` – actual array of search results (defaults to first 10 documents)
* `_score` and `max_score` - ignore these fields for now
Here is the same exact search above using the alternative request body method:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
```

The difference here is that instead of passing `q=*` in the URI, we POST a JSON-style query request body to the `_search` API. We’ll discuss this JSON query in the next section.

And the response (partially shown):

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
{
  "took" : 26,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "13",
```

It is important to understand that once you get your search results back, Elasticsearch is completely done with the request and does not maintain any kind of server-side resources or open cursors into your results. This is in stark contrast to many other platforms such as SQL wherein you may initially get a partial subset of your query results up-front and then you have to continuously go back to the server if you want to fetch (or page through) the rest of the results using some kind of stateful server-side cursor.