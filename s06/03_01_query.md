# 6.3.1 Query query元素的使用

在 `查询体`型的查询方式中，你可以通过 `query` 元素来指明你的查询条件，查询条件的写法采用 [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)

```bash
{
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

***

The query element within the search request body allows to define a query using the [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html).

```bash
{
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```
