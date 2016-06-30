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

## 可选参数

通过 URI 查询的时候，可以传入以下参数：

| 参数名 | 参数作用 |
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


