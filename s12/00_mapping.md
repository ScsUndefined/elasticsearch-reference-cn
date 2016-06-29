# Mapping 映射

映射，它定义了一个文档以及它所包含的字段该如何存储，如何被索引。比如你可以使用映射来指明：

* 哪些字符串型字段需要被设置成全文字段
* 哪些字段含有数字，日期或者经纬度
* 文档的哪些字段应当被索引到 `_all` 字段中
* 日期型数据的 [格式](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html)
* 设定一些规则用来控制 [自增型字段](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-mapping.html) 的映射规则

## 映射类型

每个索引都有一个或多个 *映射类型*，根据类型就可以对文档进行逻辑上的区分。比如用户文档应该以一个 `user` 类型来存储，而博文则应该以 `blopost` 类型来存。

每种映射类型都有：

* 元字段

  元字段是用来配置文档的元数据的。包括有 [`_index`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-index-field.html), [`_type`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-type-field.html), [`_id`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-id-field.html), 以及 [`_source`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html)
  
* [字段](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html) or *属性*
  
  每个映射类型都有许多与之相关联的字段或**`属性`**。一个 **`用户`** 类型可能包含有 **`称呼`**, **`姓名`**, 以及 **`年龄`** 字段，而一个 **`博文`** 类型则可能包含有 **`标题`**, **`正文`**, **`用户 ID`** and **`创建日期`** 等字段。同一个索引中的不同映射类型中的同名字段的映射必须是一致的。
  
## 字段的数据类型

每个字段都有一个数据 `类型`，它可以是：

* 一个简单的类型，比如`string`, `data`, `long`, `double`, `boolean` 或者 `ip`
* 支持 JSON 的分层特性的类型，比如 `object` 或 `nested`
* 或者一个专业化的类型，比如 [`geo_point`](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-point.html), [`geo_shape`](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-shape.html), 或者 [`completion`](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html)

把同一个字段以不同的方式来索引以达到不同目的的做法是非常可取的。举个例子，一个 `string` 字段可以被 [索引](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-index.html) 成一个 `analyzed` 字段以应对全文检索，并可以同时被映射成一个 `not_analyzed` 字段来处理排序或者聚合方面的需求。又或者，你可以分别用 [`标准` 解析器](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-standard-analyzer.html)，[`英文` 解析器](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html#english-analyzer) 还有 [`法语` 解析器](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html#french-analyzer) 来处理一个字符串型的字段。

这也即是 *多字段* 的目的。大多数数据类型都通过 [fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/multi-fields.html) 参数来支持多字段特性。

## 自动映射

字段和映射类型可以在要用的时候再定义。*动态映射* 特性允许你在索引一个文档的时候，相应的映射类型和新字段名也将会自动地被加到 elasticsearch 中。新字段可以同时被新增入最高级映射类型和内部的 `object` 和 `nested` 字段中。

[动态映射](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-mapping.html) 规则可以在为新类型以及新字段定义映射规则的时候被配置。

## 手动映射

Elasticsearch 可以根据你的数据来推测你的数据类型，但你自己肯定更了解你的数据，所以尽管在你刚接触 Elasticsearch 的时候，动态映射机制可以大幅度减少你的配置量，从而使你更轻松地上手，但早晚有一天你会想要自己明确地来定义你的映射关系。

你可以在创建索引的时候创建映射类型以及字段映射，然后你就可以用 [PUT mapping API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html) 把添加映射的类型和字段到一个已有的索引中。

## 更新映射

Other than where documented, **已有的类型和字段映射不可以被更新**。改变映射会破坏已经索引好的文档。所以你不能改变映射而是应该用正确的映射来创建一个新索引，然后重新把你的数据索引到这个新的索引中。

## 各映射类型的字段都是共享的

映射类型被用来对字段分组，但是每个映射类型中的字段却不是相互独立的，有着：

* 相同名字的
* 在 *同一个索引* 里
* 在 *不同的映射类型* 中
* 内部被映射到 *相同的字段* 
* 就 **必须使用相同的映射**.

如果 `用户` 映射类型和 `博文` 映射类型都有一个 `标题` 字段，那两种类型中的 `标题` 字段都必须要有一致的映射规则。这一原则的例外情况是 [`copy_to`](https://www.elastic.co/guide/en/elasticsearch/reference/current/copy-to.html), [`dynamic`](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic.html), [`enabled`](https://www.elastic.co/guide/en/elasticsearch/reference/current/enabled.html), [`ignore_above`](https://www.elastic.co/guide/en/elasticsearch/reference/current/ignore-above.html), [`include_in_all`](https://www.elastic.co/guide/en/elasticsearch/reference/current/include-in-all.html), 和 [`properties`](https://www.elastic.co/guide/en/elasticsearch/reference/current/properties.html) 参数，这些参数的每个字段都可能有不同的配置。

但通常情况下，同名的字段就应该拥有相同的数据类型，然后相同的映射也就不是问题了。当的确发生冲突的时候，可以通过选择一个更具体的名字来重命名字段来解决冲突，比如 `用户的称呼`和`博文的标题`。~~title 既可以指人的称呼亦可指文章的标题~~

## 示例

在创建一个索引的时候就可以指定它的映射规则，如下例：

```bash
PUT my_index ①
{
  "mappings": {
    "user": { ②
      "_all":       { "enabled": false  }, ③
      "properties": { ④
        "title":    { "type": "string"  }, ⑤
        "name":     { "type": "string"  }, ⑥
        "age":      { "type": "integer" }  ⑦
      }
    },
    "blogpost": { ⑧
      "properties": { ⑨
        "title":    { "type": "string"  }, ⑩
        "body":     { "type": "string"  }, ⑪
        "user_id":  {
          "type":   "string", ⑫
          "index":  "not_analyzed"
        },
        "created":  {
          "type":   "date", ⑬
          "format": "strict_date_optional_time||epoch_millis"
        }
      }
    }
  }
}
```
[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/mapping/1.json) 

① 创建一个叫做 `my_index` 的索引

②⑧ 新增叫做 `user` and `blogpost` 的映射类型.

③ 禁用 user 映射类型的叫做 `_all` 的 [元字段](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-fields.html)
 
④⑨ 指定每种映射类型的字段或*属性*

⑤⑥⑦⑩⑪⑫⑬ 指明数据*类型*以及为每个字段的映射

***


Mapping is the process of defining how a document, and the fields it contains, are stored and indexed. For instance, use mappings to define:

* which string fields should be treated as full text fields.
* which fields contain numbers, dates, or geolocations.
* whether the values of all fields in the document should be indexed into the catch-all [`_all`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html) field.
* the [format](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html) of date values.
* custom rules to control the mapping for [dynamically added fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-mapping.html).

## Mapping Types

Each index has one or more *mapping types*, which are used to divide the documents in an index into logical groups. User documents might be stored in a `user` type, and blog posts in a `blogpost` type.

Each mapping type has:

* [Meta-fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-fields.html) 

  Meta-fields are used to customize how a document’s metadata associated is treated. Examples of meta-fields include the document’s [`_index`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-index-field.html), [`_type`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-type-field.html), [`_id`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-id-field.html), and [`_source`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html) fields.
  
* [Fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html) or *properties*
  
  Each mapping type contains a list of fields or **`properties`** pertinent to that type. A **`user`** type might contain **`title`**, **`name`**, and **`age`** fields, while a **`blogpost`** type might contain **`title`**, **`body`**, **`user_id`** and **`created`** fields. Fields with the same name in different mapping types in the same index [must have the same mapping](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html#field-conflicts).
  
## Field datatypes

Each field has a data `type` which can be:

* a simple type like [`string`](https://www.elastic.co/guide/en/elasticsearch/reference/current/string.html), [`date`](https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html), [`long`](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html), [`double`](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html), [`boolean`](https://www.elastic.co/guide/en/elasticsearch/reference/current/boolean.html) or [`ip`](https://www.elastic.co/guide/en/elasticsearch/reference/current/ip.html).
* a type which supports the hierarchical nature of JSON such as [`object`](https://www.elastic.co/guide/en/elasticsearch/reference/current/object.html) or [`nested`](https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html).
* or a specialised type like [`geo_point`](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-point.html), [`geo_shape`](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-shape.html), or [`completion`](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html).

It is often useful to index the same field in different ways for different purposes. For instance, a `string` field could be [indexed](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-index.html) as an `analyzed` field for full-text search, and as a `not_analyzed` field for sorting or aggregations. Alternatively, you could index a string field with the [`standard` analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-standard-analyzer.html), the `[english](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html#english-analyzer)` analyzer, and the [`french` analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html#french-analyzer).

This is the purpose of *multi-fields*. Most datatypes support multi-fields via the [fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/multi-fields.html) parameter.

## Dynamic mapping
Fields and mapping types do not need to be defined before being used. Thanks to *dynamic mapping*, new mapping types and new field names will be added automatically, just by indexing a document. New fields can be added both to the top-level mapping type, and to inner `[object](https://www.elastic.co/guide/en/elasticsearch/reference/current/object.html)` and `[nested](https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html)` fields.

The [dynamic mapping](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-mapping.html) rules can be configured to customise the mapping that is used for new types and new fields.

## Explicit mappings

You know more about your data than Elasticsearch can guess, so while dynamic mapping can be useful to get started, at some point you will want to specify your own explicit mappings.

You can create mapping types and field mappings when you [create an index](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html), and you can add mapping types and fields to an existing index with the [PUT mapping API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html).

## Updating existing mappings

Other than where documented, **existing type and field mappings cannot be updated**. Changing the mapping would mean invalidating already indexed documents. Instead, you should create a new index with the correct mappings and reindex your data into that index.

## Fields are shared across mapping types

Mapping types are used to group fields, but the fields in each mapping type are not independent of each other. Fields with:

* the same name
* in the *same index*
* in *different mapping types*
* map to the *same field* internally,
* and **must have the same mapping**.

If a `title` field exists in both the `user` and `blogpost` mapping types, the `title` fields must have exactly the same mapping in each type. The only exceptions to this rule are the [`copy_to`](https://www.elastic.co/guide/en/elasticsearch/reference/current/copy-to.html), [`dynamic`](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic.html), [`enabled`](https://www.elastic.co/guide/en/elasticsearch/reference/current/enabled.html), [`ignore_above`](https://www.elastic.co/guide/en/elasticsearch/reference/current/ignore-above.html), [`include_in_all`](https://www.elastic.co/guide/en/elasticsearch/reference/current/include-in-all.html), and [`properties`](https://www.elastic.co/guide/en/elasticsearch/reference/current/properties.html) parameters, which may have different settings per field.

Usually, fields with the same name also contain the same type of data, so having the same mapping is not a problem. When conflicts do arise, these can be solved by choosing more descriptive names, such as `user_title` and `blog_title`.

## Example mapping

A mapping for the example described above could be specified when creating the index, as follows:

```bash
PUT my_index ①
{
  "mappings": {
    "user": { ②
      "_all":       { "enabled": false  }, ③
      "properties": { ④
        "title":    { "type": "string"  }, ⑤
        "name":     { "type": "string"  }, ⑥
        "age":      { "type": "integer" }  ⑦
      }
    },
    "blogpost": { ⑧
      "properties": { ⑨
        "title":    { "type": "string"  }, ⑩
        "body":     { "type": "string"  }, ⑪
        "user_id":  {
          "type":   "string", ⑫
          "index":  "not_analyzed"
        },
        "created":  {
          "type":   "date", ⑬
          "format": "strict_date_optional_time||epoch_millis"
        }
      }
    }
  }
}
```
[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/mapping/1.json) 

① Create an index called `my_index`.

②⑧ Add mapping types called `user` and `blogpost`.

③ Disable the `_all` [meta field](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-fields.html) for the user mapping type.
 
④⑨ Specify fields or *properties* in each mapping type.

⑤⑥⑦⑩⑪⑫⑬ Specify the data `type` and mapping for each field.