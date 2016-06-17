# 1.5.5 Executing Aggregations

聚合提供了分组查询和提取统计信息的能力。理解聚合功能的最快途径就是先简单地把它任何就是 SQL GROUP BY 和 SQL aggregate 函数。在 Elasticsearch 中你可以同时返回匹配的数据以及基于这些数据的聚合操作结果。这是个功能非常强大并且高效的特性，你可以在单个网络请求中指明要不要同时(或仅返回任意一个)查询操作结果和多个聚合操作结果，这允许你尽可能地省去不必要的网络开销，而且这个 API 还非常简单明了。

我们以下面这个例子开始，这个示例把所有的文档都以 state 分组，然后以数量递减排序（默认排序）返回了前十个（默认返回 10 个）结果：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      }
    }
  }
}'
```

上面这个聚合操作就和下面这个 SQL 语句的意思易相似：

```SQL
SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC
```

返回的结果（只显示了部分）：

```javascript
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "buckets" : [ {
        "key" : "al",
        "doc_count" : 21
      }, {
        "key" : "tx",
        "doc_count" : 17
      }, {
        "key" : "id",
        "doc_count" : 15
      }, {
        "key" : "ma",
        "doc_count" : 15
      }, {
        "key" : "md",
        "doc_count" : 15
      }, {
        "key" : "pa",
        "doc_count" : 15
      }, {
        "key" : "dc",
        "doc_count" : 14
      }, {
        "key" : "me",
        "doc_count" : 14
      }, {
        "key" : "mo",
        "doc_count" : 14
      }, {
        "key" : "nd",
        "doc_count" : 14
      } ]
    }
  }
}
```

我们可以看到，排在第一位的是，亚拉巴马州，有着 21 个账户（亚拉巴马州 AL Alabama），其次是德克萨斯州，有着 17 个账户（德克萨斯州 TX Texas），然后是爱达荷州（爱达荷州 ID Idaho），15个账户等等。

~~Idaho，BB-42～～～一发入魂，二发入孕～～～~~

要注意的是我们设置了`size=0`，这样就可以不显示搜索匹配到的结果了，因为我们只想看下聚合操作的结果而已。

在上一个示例代码的基础上，这次我们计算出每个州的平均账户余额（依旧是默认地，只显示 10 个并以数量降序排序）：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```

注意一下我们是怎么把`average_balance`聚合操作内嵌到`group_by_state`操作中的。这个也是所有聚合操作的通用模板。你可以任意地嵌套使用聚合操作来提取你想要的结果。

基于上一个例子，这次我们开始以平均账户余额递减排序：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```

下面这个示例演示了怎么以年龄段来分组（20-29,30-39,40-49），然后以性别分组，最后再计算每个年龄段的每种性别的账户平均余额：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender"
          },
          "aggs": {
            "average_balance": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
      }
    }
  }
}'
```

还有许多可用的聚合操作，在这里我们就不再深入展开了，如果你想深入了解的话可以从这篇文档开始：[aggregations reference guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)

***

Aggregations provide the ability to group and extract statistics from your data. The easiest way to think about aggregations is by roughly equating it to the SQL GROUP BY and the SQL aggregate functions. In Elasticsearch, you have the ability to execute searches returning hits and at the same time return aggregated results separate from the hits all in one response. This is very powerful and efficient in the sense that you can run queries and multiple aggregations and get the results back of both (or either) operations in one shot avoiding network roundtrips using a concise and simplified API.

To start with, this example groups all the accounts by state, and then returns the top 10 (default) states sorted by count descending (also default):

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      }
    }
  }
}'
```

In SQL, the above aggregation is similar in concept to:

```SQL
SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC
```

And the response (partially shown):

```javascript
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "buckets" : [ {
        "key" : "al",
        "doc_count" : 21
      }, {
        "key" : "tx",
        "doc_count" : 17
      }, {
        "key" : "id",
        "doc_count" : 15
      }, {
        "key" : "ma",
        "doc_count" : 15
      }, {
        "key" : "md",
        "doc_count" : 15
      }, {
        "key" : "pa",
        "doc_count" : 15
      }, {
        "key" : "dc",
        "doc_count" : 14
      }, {
        "key" : "me",
        "doc_count" : 14
      }, {
        "key" : "mo",
        "doc_count" : 14
      }, {
        "key" : "nd",
        "doc_count" : 14
      } ]
    }
  }
}
```

We can see that there are 21 accounts in AL(abama), followed by 17 accounts in TX, followed by 15 accounts in ID(aho), and so forth.

Note that we set `size=0` to not show search hits because we only want to see the aggregation results in the response.

Building on the previous aggregation, this example calculates the average account balance by state (again only for the top 10 states sorted by count in descending order):

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```

Notice how we nested the `average_balance` aggregation inside the `group_by_state` aggregation. This is a common pattern for all the aggregations. You can nest aggregations inside aggregations arbitrarily to extract pivoted summarizations that you require from your data.

Building on the previous aggregation, let’s now sort on the average balance in descending order:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```

This example demonstrates how we can group by age brackets (ages 20-29, 30-39, and 40-49), then by gender, and then finally get the average account balance, per age bracket, per gender:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender"
          },
          "aggs": {
            "average_balance": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
      }
    }
  }
}'
```

There are a many other aggregations capabilities that we won’t go into detail here. The [aggregations reference guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html) is a great starting point if you want to do further experimentation.