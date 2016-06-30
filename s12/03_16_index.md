# 12.3.16 index

`index` 参数控制着字段值的索引方式进而影响了该值的可搜索性。该参数有 3 种可选的值：

* `no`

  不把这个值放到索引中，因此该字段就可能被查询得到。
  
* `not_analyzed`

  不对该字段的值做任何解析操作而是原封不动地把该值当做一个单一不可再分割的 term 放入到索引中。这也是除 `string` 字段以外其他支持 index 选项的字段的默认值。`not_analyzed` 字段通常与 [term-level 查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/term-level-queries.html) 混合使用来执行一个结构化的查询操作。
  
* `analyzed`

  `analyzed` 只能对 `string` 字段使用，同时这也是 `string` 字段的 `index` 参数的默认值。`string` 型字段的值首先会被解析处理，解析过程会讲字符串处理成多个 term (即一系列独立的词)，然后再把它们存入索引中。而在查询的时候，查询字符串通常情况下也会被相同的解析器处理成多个 term，这些 term 与索引中已有的 term 格式相同。这个过程使得 Elasticsearch 拥有 [全文检索](https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html) 的能力。
  
下面示范了怎么创建一个 `not_analyzed` 的 `string` 型字段：

```bash
PUT /my_index
{
  "mappings": {
    "my_type": {
      "properties": {
        "status_code": {
          "type": "string",
          "index": "not_analyzed"
        }
      }
    }
  }
}
```

[VIEW IN SENSE ](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/mapping-index/1.json)

***

The `index` option controls how field values are indexed and, thus, how they are searchable. It accepts three values:

* `no`

  Do not add this field value to the index. With this setting, the field will not be queryable.

* `not_analyzed`

  Add the field value to the index unchanged, as a single term. This is the default for all fields that support this option except for [string](https://www.elastic.co/guide/en/elasticsearch/reference/current/string.html) fields. `not_analyzed` fields are usually used with [term-level queries](https://www.elastic.co/guide/en/elasticsearch/reference/current/term-level-queries.html) for structured search.

* `analyzed`

  This option applies only to `string` fields, for which it is the default. The string field value is first [analyzed](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis.html) to convert the string into terms (e.g. a list of individual words), which are then indexed. At search time, the query string is passed through ([usually](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-analyzer.html)) the same analyzer to generate terms in the same format as those in the index. It is this process that enables [full text search](https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html).

For example, you can create a `not_analyzed` string field with the following:

```bash
PUT /my_index
{
  "mappings": {
    "my_type": {
      "properties": {
        "status_code": {
          "type": "string",
          "index": "not_analyzed"
        }
      }
    }
  }
}
```

[VIEW IN SENSE ](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/mapping-index/1.json)
