# Search APIs 查询 API

查询 API 都几乎都支持同时查询多个索引，多个类型的，除了 *[Explain API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-explain.html)*。

## 路由

一个查询请求会被广播到所有的索引分片（在各个索引之间李彦宏 ~~robin:李彦宏；round robin:循环~~）。而你也可以通过 *routing* 参数来控制到底哪些分片需要被查询。比如当我们要把微博存到索引中的时候，我们就可以把用户名来当做 routing 的值：

```bash
$ curl -XPOST 'http://localhost:9200/twitter/tweet?routing=kimchy' -d '{
    "user" : "kimchy",
    "postDate" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

然后，在搜索阶段，当我们只要查询某个特定用户的微博的时候，我就可以在指定查询条件的同时也指定 routing 的值，这就会导致查询请求直接被路由到相关的分片上：

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

routing 的值也可以是多个，彼此之间需要用英式逗号分割开。

## 分组统计

~~好像是用来统计每类查询的查询次数的，不过没弄懂，就直译了~~

每个查询动作都可以被关联进多个统计分组里，这样就可以维护每个分组的统计学信息了。这些信息可以随后用 [indices stats API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html) 来查看。下面这个示例会把查询动作同时关联入两个统计分组中。

```javascript
{
    "query" : {
        "match_all" : {}
    },
    "stats" : ["group1", "group2"]
}
```

##  全局的超时时限

每个 *[Request Body Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)* 型的查询操作都可以有一个超时设定。然而查询动作的发起方五花八门，也因此 Elasticsearch 有一个全局性的动态的集群级的超时设置，当查询操作本身没有设置超时信息的时候，集群级的超时设置就会生效。默认是不会超时的。超时的设置 key 是 `search.default_search_timeout` 然后可以通过 *[Cluster Update Settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-update-settings.html)* 一章中介绍的方法来设置，把它设置成 `-1` 就表示不会超时。 ~~但是没有说明单位诶...可能是毫秒吧...~~

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