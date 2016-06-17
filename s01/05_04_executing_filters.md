# 1.5.4 Executing Filters

在上一个章节，我们刻意忽略了一个叫做 document score 的东西（搜索结果中的那个`_score`值），直译过来就是文档的得分值，意译一下就是文档的相关性评分值。score 是一个表示文档与查询条件的相关性的数值体现，分值越高表示该文档越接近查询条件，反之依然。

但有时候查询操作并不需要提供相关性信息，尤其是当我们只是“筛选”文档的时候。Elasticsearch会自动侦测你的查询请求是否需要计算相关性，如果它认为不需要计算搜索结果的相关性的时候，它就是自动优化搜索的过程，自动跳过计算相关性的步骤。

我们上个章节介绍的 [bool query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html) 同样支持`filter`子句，filter 会筛选出符合条件的文档然后再去匹配其他查询条件，并且不会影响文档的相关性评分。我们会拿 [range query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html) 来举例，它允许我们筛选出特定范围的值。这个功能通常用来筛选数值类数据或者日期类数据。

下面这个例子使用一个 bool 查询来返回所有 balance 在 20k 到 30k 之间的数据（含 20k 和 30k）。换句话说的话，就是查询出 balance 值大于等于 20k 并且小于等于 30k 的数据。

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}'
```

分析下上面那个示例，bool查询包含一个 `match_all`（查询的部分）查询和一个`range`查询（筛选的部分）。查询和筛选的部分我们都可以替换成任何其他的查询。在上面这个示例中，range 查询完美诠释了为啥筛选操作不会影响查询结果的相关性，因为在这个示例中所有满足筛选条件的文档都是相等的，即，没有哪个文档比其他文档更相关。

除了 `match_all`, `match`, `bool`, and `range` 查询之外，还有很多其他可用的查询方式，而我们也不再会去在这深究它们。我们已经对他们有了个基础的认识了，所以以后学习和实践其他的查询方式的时候，应该也不会太难，我是说应该。

***

In the previous section, we skipped over a little detail called the document score (`_score` field in the search results). The score is a numeric value that is a relative measure of how well the document matches the search query that we specified. The higher the score, the more relevant the document is, the lower the score, the less relevant the document is.

But queries do not always need to produce scores, in particular when they are only used for "filtering" the document set. Elasticsearch detects these situations and automatically optimizes query execution in order not to compute useless scores.

The [bool query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html) that we introduced in the previous section also supports `filter` clauses which allow to use a query to restrict the documents that will be matched by other clauses, without changing how scores are computed. As an example, let’s introduce the [range query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html), which allows us to filter documents by a range of values. This is generally used for numeric or date filtering.

This example uses a bool query to return all accounts with balances between 20000 and 30000, inclusive. In other words, we want to find accounts with a balance that is greater than or equal to 20000 and less than or equal to 30000.

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}'
```

Dissecting the above, the bool query contains a `match_all` query (the query part) and a `range` query (the filter part). We can substitute any other queries into the query and the filter parts. In the above case, the range query makes perfect sense since documents falling into the range all match "equally", i.e., no document is more relevant than another.

In addition to the `match_all`, `match`, `bool`, and `range` queries, there are a lot of other query types that are available and we won’t go into them here. Since we already have a basic understanding of how they work, it shouldn’t be too difficult to apply this knowledge in learning and experimenting with the other query types.