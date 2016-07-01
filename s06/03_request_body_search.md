# 6.3 Request Body Search 查询体型的请求方式

除了使用 URI 来发起一个查询请求，你也可以使用一个包含着 [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html) 的 DSL 来进行查询，就像下面这样：

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search' -d '{
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
'
```

下面是这个查询请求的结果：

```bash
{
    "_shards":{
        "total" : 5,
        "successful" : 5,
        "failed" : 0
    },
    "hits":{
        "total" : 1,
        "hits" : [
            {
                "_index" : "twitter",
                "_type" : "tweet",
                "_id" : "1",
                "_source" : {
                    "user" : "kimchy",
                    "postDate" : "2009-11-15T14:12:12",
                    "message" : "trying out Elasticsearch"
                }
            }
        ]
    }
}
```

## 配置参数

| 参数名       | 参数作用
| ----------------- | ----------------- |
| `timeout`         | 查询的超时时间，用来限制查询请求的最大耗时，当超时的时候会返回已查询到的部分结果。默认是不会超时的，更过有关超时的知识会在 [Time units](https://www.elastic.co/guide/en/elasticsearch/reference/current/common-options.html#time-units) 这章中讲述 |
| `from`            | 分页查询的起始位置，默认是 `0`|
| `size`            | 分页查询的页容量。默认是`10`。如果你只想知道查询结果的数量而不是具体内容的话，那你可以设置成`0`，这样可以提升性能 |
| `search_type`     | 查询操作的类型，可以是 `dfs_query_then_fetch` 或 `query_then_fetch`。 默认是 `query_then_fetch`。更多有关查询类型的知识会在 [Search Type](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-search-type.html) 一章中讲述 |
| `request_cache`   | 设置是否缓存 `size` 为 `0` 的查询，比如 aggregations 聚合和 suggestions 暗示（没有匹配的结果被返回）更多有关这种缓存的知识会在 [Shard request cache](https://www.elastic.co/guide/en/elasticsearch/reference/current/shard-request-cache.html) 一章中讲述|
| `terminate_after` | 设置查询动作在每个分片上的遍历文档的总数，如果查询请求涉及的文档数达到这个上限，那本次查询请求就会提前结束。当这个值被设置之后，查询结果会多返回一个布尔型的字段 `terminated_early` 用来标识本次查询请求是否是被提前中止的。这个功能默认不不开启。 |

再补充一下，`search_type` 和 `request_cache` 只能通过 query-string 的形式来设置，而其余的参数则应该通过search body(查询主体)来设置。主体内容也可以通过一个名字为 `source` 的 REST 参数来设置。

HTTP GET 或 POST 都可以用来执行这种查询方式，但由于不是所有的 HTTP 终端都允许 GET 请求附带一个 body，当 POST 就都可以，所以 POST 相对更通用些。

***

The search request can be executed with a search DSL, which includes the [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html), within its body. Here is an example:

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search' -d '{
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
'
```

And here is a sample response:

```bash
{
    "_shards":{
        "total" : 5,
        "successful" : 5,
        "failed" : 0
    },
    "hits":{
        "total" : 1,
        "hits" : [
            {
                "_index" : "twitter",
                "_type" : "tweet",
                "_id" : "1",
                "_source" : {
                    "user" : "kimchy",
                    "postDate" : "2009-11-15T14:12:12",
                    "message" : "trying out Elasticsearch"
                }
            }
        ]
    }
}
```

## Parameters

| Name              | Description |
| ----------------- | ----------------- |
| `timeout`         | A search timeout, bounding the search request to be executed within the specified time value and bail with the hits accumulated up to that point when expired. Defaults to no timeout. See [the section called “Time unitsedit”](https://www.elastic.co/guide/en/elasticsearch/reference/current/common-options.html#time-units). |
| `from`            | To retrieve hits from a certain offset. Defaults to 0. |
| `size`            | The number of hits to return. Defaults to `10`. If you do not care about getting some hits back but only about the number of matches and/or aggregations, setting the value to `0` will help performance. |
| `search_type`     | The type of the search operation to perform. Can be `dfs_query_then_fetch` or `query_then_fetch`. Defaults to `query_then_fetch`. See [Search Type](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-search-type.html) for more. |
| `request_cache`   | Set to `true` or `false` to enable or disable the caching of search results for requests where `size` is 0, ie aggregations and suggestions (no top hits returned). See [Shard request cache](https://www.elastic.co/guide/en/elasticsearch/reference/current/shard-request-cache.html). |
| `terminate_after` | The maximum number of documents to collect for each shard, upon reaching which the query execution will terminate early. If set, the response will have a boolean field `terminated_early` to indicate whether the query execution has actually terminated_early. Defaults to no terminate_after. |

Out of the above, the `search_type` and the `request_cache` must be passed as query-string parameters. The rest of the search request should be passed within the body itself. The body content can also be passed as a REST parameter named `source`.

Both HTTP GET and HTTP POST can be used to execute search with body. Since not all clients support GET with body, POST is allowed as well.