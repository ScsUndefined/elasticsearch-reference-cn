# 1.5.2 Introducing the Query Language

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

curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
The from parameter (0-based) specifies which document index to start from and the size parameter specifies how many documents to return starting at the from parameter. This feature is useful when implementing paging of search results. Note that if from is not specified, it defaults to 0.

This example does a match_all and sorts the results by account balance in descending order and returns the top 10 (default size) documents.

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

curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
The from parameter (0-based) specifies which document index to start from and the size parameter specifies how many documents to return starting at the from parameter. This feature is useful when implementing paging of search results. Note that if from is not specified, it defaults to 0.

This example does a match_all and sorts the results by account balance in descending order and returns the top 10 (default size) documents.

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}'
```
