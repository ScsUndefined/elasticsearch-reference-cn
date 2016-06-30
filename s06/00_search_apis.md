# Search APIs 查询 API

Most search APIs are [multi-index, multi-type](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-search.html#search-multi-index-type), with the exception of the *[Explain API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-explain.html)* endpoints.

## Routing

When executing a search, it will be broadcast to all the index/indices shards (round robin between replicas). Which shards will be searched on can be controlled by providing the *routing* parameter. For example, when indexing tweets, the routing value can be the user name:

```bash
$ curl -XPOST 'http://localhost:9200/twitter/tweet?routing=kimchy' -d '{
    "user" : "kimchy",
    "postDate" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

In such a case, if we want to search only on the tweets for a specific user, we can specify it as the routing, resulting in the search hitting only the relevant shard:

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search?routing=kimchy' -d '{
    "query": {
        "bool" : {
            "must" : {
                "query_string" : {
                    "query" : "some query string here"
                }
            },
            "filter" : {
                "term" : { "user" : "kimchy" }
            }
        }
    }
}
'
```

The routing parameter can be multi valued represented as a comma separated string. This will result in hitting the relevant shards where the routing values match to.

## Stats Groups

A search can be associated with stats groups, which maintains a statistics aggregation per group. It can later be retrieved using the [indices stats](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html) API specifically. For example, here is a search body request that associate the request with two different groups:

```javascript
{
    "query" : {
        "match_all" : {}
    },
    "stats" : ["group1", "group2"]
}
```

## Global Search Timeout

Individual searches can have a timeout as part of the *[Request Body Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)*. Since search requests can originate from many sources, Elasticsearch has a dynamic cluster-level setting for a global search timeout that applies to all search requests that do not set a timeout in the *[Request Body Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)*. The default value is no global timeout. The setting key is `search.default_search_timeout` and can be set using the *[Cluster Update Settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-update-settings.html)* endpoints. Setting this value to `-1` resets the global search timeout to no timeout.

***

Most search APIs are [multi-index, multi-type](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-search.html#search-multi-index-type), with the exception of the *[Explain API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-explain.html)* endpoints.

## Routing

When executing a search, it will be broadcast to all the index/indices shards (round robin between replicas). Which shards will be searched on can be controlled by providing the *routing* parameter. For example, when indexing tweets, the routing value can be the user name:

```bash
$ curl -XPOST 'http://localhost:9200/twitter/tweet?routing=kimchy' -d '{
    "user" : "kimchy",
    "postDate" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

In such a case, if we want to search only on the tweets for a specific user, we can specify it as the routing, resulting in the search hitting only the relevant shard:

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search?routing=kimchy' -d '{
    "query": {
        "bool" : {
            "must" : {
                "query_string" : {
                    "query" : "some query string here"
                }
            },
            "filter" : {
                "term" : { "user" : "kimchy" }
            }
        }
    }
}
'
```

The routing parameter can be multi valued represented as a comma separated string. This will result in hitting the relevant shards where the routing values match to.

## Stats Groups

A search can be associated with stats groups, which maintains a statistics aggregation per group. It can later be retrieved using the [indices stats](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html) API specifically. For example, here is a search body request that associate the request with two different groups:

```javascript
{
    "query" : {
        "match_all" : {}
    },
    "stats" : ["group1", "group2"]
}
```

## Global Search Timeout

Individual searches can have a timeout as part of the *[Request Body Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)*. Since search requests can originate from many sources, Elasticsearch has a dynamic cluster-level setting for a global search timeout that applies to all search requests that do not set a timeout in the *[Request Body Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)*. The default value is no global timeout. The setting key is `search.default_search_timeout` and can be set using the *[Cluster Update Settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-update-settings.html)* endpoints. Setting this value to `-1` resets the global search timeout to no timeout.