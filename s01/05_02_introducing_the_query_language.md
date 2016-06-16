# 1.5.2 Introducing the Query Language

Elasticsearch 提供一个 JSON 格式的，域指定（field-special）的语言来让你执行查询操作。这个查询语言在 [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html) 中有详细介绍。这个查询语言功能非常宽泛，所以可能刚接触的时候会让人觉得有点骇人，不过不要怕，最好的学习方式就是先喰几个简单的小栗子压压惊。

回到我们最近一个示例代码，我们执行的是下面在这样子的一个查询语句：

```bash
{
  "query": { "match_all": {} }
}
```

让我们来看一看上面这段代码，这个 query 部分就定义了我们的查询条件，match_all 部分指明了查询条件对应的类型信息。这个 match_all 查询意思是查询指定索引中的所有文档。

除了 query 部分外，我们还可以传递其他参数来影响查询结果。比如下面这个栗子查询了所有的文档，但是只获取了所有文档中的第一个：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "size": 1
}'
```

要注意的是，如果你没有指明返回结果的数据，那默认就是 10 条。

下面这个栗子是查询了所有的数据，但是只需要返回第 11 ～ 20 条：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
```

from 参数指明了返回结果的起始位置，而 size 则指明了要返回多少条数据。这个特性在进行分页查询的时候会用得到。需要注意的是如果 from 没有额外指明，那么默认就是 0。

下面这个示例演示的是，查询出所有文档然后以 balance 值递减排序，然后返回前十条结果。

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}'
```

***

Elasticsearch provides a JSON-style domain-specific language that you can use to execute queries. This is referred to as the [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html). The query language is quite comprehensive and can be intimidating at first glance but the best way to actually learn it is to start with a few basic examples.

Going back to our last example, we executed this query:

```bash
{
  "query": { "match_all": {} }
}
```

Dissecting the above, the query part tells us what our query definition is and the match_all part is simply the type of query that we want to run. The match_all query is simply a search for all documents in the specified index.

In addition to the query parameter, we also can pass other parameters to influence the search results. For example, the following does a match_all and returns only the first document:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "size": 1
}'
```

Note that if size is not specified, it defaults to 10.

This example does a match_all and returns documents 11 through 20:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
```

The from parameter (0-based) specifies which document index to start from and the size parameter specifies how many documents to return starting at the from parameter. This feature is useful when implementing paging of search results. Note that if from is not specified, it defaults to 0.

This example does a match_all and sorts the results by account balance in descending order and returns the top 10 (default size) documents.

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}'
```