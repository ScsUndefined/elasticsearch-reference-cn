# 1.5.3 Executing Searches

我们已经了解了一些基础的搜索参数，现在我们来更深入地学习下 Query DSL。
首先我们来看一下查询请求返回的 document 键值对。默认情况下，完整的 JSON 文档都会作为 source 来返回（hits 中的 `_source` 字段）。如果你不希望整个文档被返回，我们也可以通过特殊的命令来要求 Elasticsearch 返回部分源文档即可。

下面这个示例就演示了怎么告诉 Elasticsearch 只需要返回源文档的`account_number`和`balance`信息：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}'
```

注意一下上面这个命令的运行结果，`_source`字段依然还是会被返回，但是它里面的内容被精简了，现在`_source`中只包含`account_number`和`balance`。

如果你有一定的 SQL 基础，那么上述这个特性从概念上来讲，其实就跟 `SQL SELECT FROM` 类似。

看完了查询请求返回的 document 键值对，我们现在再来看下 query 部分。我们已经知道`match_all`是用来匹配所有的文档的。那现在我们再来介绍一种新的查询方式叫做 [match query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html) 即按条件查询，或者也可以理解成基于字段的查询（即一个查询条件是针对某个或某些字段的）。

这个示例演示的是查出 account number 是 20 的数据：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "account_number": 20 } }
}'
```

这个例子查询的是所有地址中含有“mill”的账户：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill" } }
}'
```

这个例子查询的是所有地址中含有“mill”或者“lane”的账户：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill lane" } }
}'
```

这个示例是一种新的，叫做 match_phrase 的查询方式，它会查询中所有地址信息中包含“mill lane”短语的账户信息：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_phrase": { "address": "mill lane" } }
}'
```

现在我们介绍下 [bool(ean) query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html) 。bool 查询允许我们用布尔逻辑将相近的查询组合成更复杂的查询。

下面这个示例在 bool 查询中整合了2个 match 查询，查出了所有 address 中既含有“mill”又含有“lane”的数据：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

在上一个示例中，`bool must`子句中的各个查询条件只有都被满足的时候，整个 must 查询子句才会被认为是 true。

和上面那个例子不同的是，下面这个例子查出了 address 中含有“mill”或者“lane”的数据：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "should": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

在上面这个示例中，`bool should`子句中指明了 2 个匹配条件，只要满足一条，就可以认为满足整个 `bool should`。

下面这个例子用两个`match`查询组合在一起，查询出了 address 中既没有“mill”也没有“lane”的数据：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

在上面这个示例中，`bool must_not`子句指定了一系列的限制条件，只有当这些条件都不被满足的时候，整个 bool 查询结果才会被认为是满足的。

我们可以任意地把`must`,`should`和`must_not`查询混合在一个`bool`查询中。甚至，我们可以在`bool`查询中再嵌入另一个`bool`查询来构建复杂的多层级的布尔逻辑。

下面这个示例查询出了所有不住在一个叫做 ID （什么鬼）的地方的 40 岁中年人：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ]
    }
  }
}'
```

***

Now that we have seen a few of the basic search parameters, let’s dig in some more into the Query DSL. Let’s first take a look at the returned document fields. By default, the full JSON document is returned as part of all searches. This is referred to as the source (`_source` field in the search hits). If we don’t want the entire source document returned, we have the ability to request only a few fields from within source to be returned.

This example shows how to return two fields, `account_number` and `balance` (inside of `_source`), from the search:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}'
```

Note that the above example simply reduces the `_source` field. It will still only return one field named `_source` but within it, only the fields `account_number` and `balance` are included.

If you come from a SQL background, the above is somewhat similar in concept to the `SQL SELECT FROM` field list.

Now let’s move on to the query part. Previously, we’ve seen how the `match_all` query is used to match all documents. Let’s now introduce a new query called the [match query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html), which can be thought of as a basic fielded search query (i.e. a search done against a specific field or set of fields).

This example returns the account numbered 20:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "account_number": 20 } }
}'
```

This example returns all accounts containing the term "mill" in the address:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill" } }
}'
```

This example returns all accounts containing the term "mill" or "lane" in the address:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill lane" } }
}'
```

This example is a variant of match (match_phrase) that returns all accounts containing the phrase "mill lane" in the address:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_phrase": { "address": "mill lane" } }
}'
```

Let’s now introduce the [bool(ean) query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html). The bool query allows us to compose smaller queries into bigger queries using boolean logic.

This example composes two `match` queries and returns all accounts containing "mill" and "lane" in the address:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

In the above example, the `bool must` clause specifies all the queries that must be true for a document to be considered a match.

In contrast, this example composes two `match` queries and returns all accounts containing "mill" or "lane" in the address:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "should": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

In the above example, the `bool should` clause specifies a list of queries either of which must be true for a document to be considered a match.

This example composes two `match` queries and returns all accounts that contain neither "mill" nor "lane" in the address:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

In the above example, the `bool must_not` clause specifies a list of queries none of which must be true for a document to be considered a match.

We can combine `must`, `should`, and `must_not` clauses simultaneously inside a `bool` query. Furthermore, we can compose `bool` queries inside any of these `bool` clauses to mimic any complex multi-level boolean logic.

This example returns all accounts of anybody who is 40 years old but don’t live in ID(aho):

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ]
    }
  }
}'
```