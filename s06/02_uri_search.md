# 6.2 URI Search URI 查询

一个查询请求可以通过一个 URI 来完成，用 URI 的方式来查询的话，查询条件就集成在了 URI 中。这种方式有一定的局限性，但是优点在于轻便，尤其当你在做“curl 测试”的时候，举个栗子：

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search?q=user:kimchy'
```

你大概会得到类似这样一个响应体：

```javascript
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

通过 URI 查询的时候，可以传入以下参数：

| 参数名 | 参数作用 |
| -- | -- |
| `q` | 查询字符串 (类似 `query_string` 查询, 详细请移步 [*Query String Query*](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html) ) |
| `df` | 当查询请求没有指明字段前缀的时候，就会以该值指定的字段为默认字段 |
| `analyzer` | 指明用来解析查询字符串的解析器 |
| `lowercase_expanded_terms` | 设置查询字符串是否应该自动被转成小写格式，默认是会自动小写化的 |
| `analyze_wildcard` | 是否需要进行拼写修正以及 prefix queries。默认是不需要 |
| `default_operator` | 默认的运算符，可以是`AND`或者是`OR`。默认是`OR` |
| `lenient` | 设置是否无视格式匹配错误。（比如用一个文本型的关键字去对一个数值型字段进行搜索）默认是不无视的 |
| `explain` | 每个匹配的结果，都包含一个解释信息，解释了为什么这个结果被认为是匹配的 |
| `source` | 设置是否需要返回 `_source` 字段。默认会返回的，改成`false`就不返回了。但你可以使用 `_source_include` & `_source_exclude` 来返回 `_source` 字段的部分内容。（具体查阅 [request body](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-source-filtering.html) 一文） |
| `fields` | 指定要返回的字段，不指定任何值的话就会导致没有字段会返回。多个字段用英式逗号隔开 |
| `sort` | 用来设置排序条件，可以是一个字段名，或者字段名后跟 `:asc` 或 `:desc`。也可以是 `_score` 即用相关度评分来排序。这个值也可以设置成多个，这时候顺序就很重要了。 |
| `track_scores` | ~~没看懂，直译了，自己看原文去~~ 当排序的时候，把这个值设置成`true` 就可以追踪评分，并返回追踪到的信息 ~~何为 track scores 追踪评分。。。。~~ |
| `timeout` | 设置本次查询操作的超时时间，默认是没有超时限制的 |
| `terminate_after` | 设置本次查询操作执行后，对每个分片所搜集的文档的数量上限，一旦本次查询操作查过了指定数量的文档就会提前结束。如果你设置了这个值，那么返回结果里就会多出一个布尔型的字段 `terminated_early` 来表示本次查询的结果是否是提前结束的结果。默认是不会提前结束的 |
| `from` | 分页查询时的起始位置，默认是 `0`|
| `size` | 分页查询时的页容量，默认是`10` |
| `search_type` | 查询类型，可以是 `dfs_query_then_fetch`, `query_then_fetch`, `scan` [~~2.1.0~~][^1] 或者 `count` [~~2.0.0-beta1~~][^2]. 默认是 `query_then_fetch`. 具体各个查询类型对应会有什么样的影响，参阅 [*查询类型*](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-search-type.html) |

[^1] 在 2.1.0 版本的时候被弃用. 可以使用一个用 `_doc` 排序的 regular scroll 来代替

[^2] 在 2.0.0-beta1 版本的时候被弃用.可以用 `size: 0` 代替

***


A search request can be executed purely using a URI by providing request parameters. Not all search options are exposed when executing a search using this mode, but it can be handy for quick "curl tests". Here is an example:

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search?q=user:kimchy'
```

And here is a sample response:

```javascript
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

The parameters allowed in the URI are:

| Name | Description |
| -- | -- |
| `q` | The query string (maps to the `query_string` query, see [*Query String Query*](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html) for more details). |
| `df` | The default field to use when no field prefix is defined within the query. |
| `analyzer` | The analyzer name to be used when analyzing the query string. |
| `lowercase_expanded_terms` | Should terms be automatically lowercased or not. Defaults to `true`. |
| `analyze_wildcard` | Should wildcard and prefix queries be analyzed or not. Defaults to `false`. |
| `default_operator` | The default operator to be used, can be `AND` or `OR`. Defaults to `OR`. |
| `lenient` | If set to true will cause format based failures (like providing text to a numeric field) to be ignored. Defaults to false. |
| `explain` | For each hit, contain an explanation of how scoring of the hits was computed. |
| `source` | Set to `false` to disable retrieval of the `_source` field. You can also retrieve part of the document by using `_source_include` & `_source_exclude` (see the [request body](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-source-filtering.html) documentation for more details) |
| `fields` | The selective stored fields of the document to return for each hit, comma delimited. Not specifying any value will cause no fields to return. |
| `sort` | Sorting to perform. Can either be in the form of `fieldName`, or `fieldName:asc`/`fieldName:desc`. The fieldName can either be an actual field within the document, or the special `_score` name to indicate sorting based on scores. There can be several `sort` parameters (order is important). |
| `track_scores` | When sorting, set to `true` in order to still track scores and return them as part of each hit. |
| `timeout` | A search timeout, bounding the search request to be executed within the specified time value and bail with the hits accumulated up to that point when expired. Defaults to no timeout. |
| `terminate_after` | The maximum number of documents to collect for each shard, upon reaching which the query execution will terminate early. If set, the response will have a boolean field `terminated_early` to indicate whether the query execution has actually terminated_early. Defaults to no terminate_after. |
| `from` | The starting from index of the hits to return. Defaults to `0`. |
| `size` | The number of hits to return. Defaults to `10`. |
| `search_type` | The type of the search operation to perform. Can be `dfs_query_then_fetch`, `query_then_fetch`, `scan` [~~2.1.0~~][^1] or `count` [~~2.0.0-beta1~~][^2]. Defaults to `query_then_fetch`. [*See Search*](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-search-type.html) Type for more details on the different types of search that can be performed. |

[^1] Deprecated in 2.1.0. Replaced by a regular scroll sorted by `_doc`.

[^2] Deprecated in 2.0.0-beta1. Replaced by `size: 0`.


