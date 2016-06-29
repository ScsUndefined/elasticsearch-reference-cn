# Mapping 映射

Mapping is the process of defining how a document, and the fields it contains, are stored and indexed. For instance, use mappings to define:

* which string fields should be treated as full text fields.
* which fields contain numbers, dates, or geolocations.
* whether the values of all fields in the document should be indexed into the catch-all [`\_all`](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html) field.
* the [format](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html) of date values.
* custom rules to control the mapping for [dynamically added fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-mapping.html).

## Mapping Types

Each index has one or more *mapping types*, which are used to divide the documents in an index into logical groups. User documents might be stored in a `user` type, and blog posts in a `blogpost` type.

Each mapping type has:

* [Meta-fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-fields.html) 

  Meta-fields are used to customize how a document’s metadata associated is treated. Examples of meta-fields include the document’s `[_index](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-index-field.html)`, `[_type](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-type-field.html)`, `[_id](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-id-field.html)`, and `[_source](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html)` fields.
  
* [Fields](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html) or *properties*
  
  Each mapping type contains a list of fields or **`properties`** pertinent to that type. A **`user`** type might contain **`title`**, **`name`**, and **`age`** fields, while a **`blogpost`** type might contain **`title`**, **`body`**, **`user_id`** and **`created`** fields. Fields with the same name in different mapping types in the same index [must have the same mapping](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html#field-conflicts).
  
## Field datatypes

Each field has a data `type` which can be:

* a simple type like `[string](https://www.elastic.co/guide/en/elasticsearch/reference/current/string.html)`, `[date](https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html)`, `[long](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html)`, `[double](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html)`, `[boolean](https://www.elastic.co/guide/en/elasticsearch/reference/current/boolean.html)` or `[ip](https://www.elastic.co/guide/en/elasticsearch/reference/current/ip.html)`.
* a type which supports the hierarchical nature of JSON such as `[object](https://www.elastic.co/guide/en/elasticsearch/reference/current/object.html)` or `[nested](https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html)`.
* or a specialised type like `[geo_point](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-point.html)`, `[geo_shape](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-shape.html)`, or `[completion](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html)`.

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

If a `title` field exists in both the `user` and `blogpost` mapping types, the `title` fields must have exactly the same mapping in each type. The only exceptions to this rule are the `[copy_to](https://www.elastic.co/guide/en/elasticsearch/reference/current/copy-to.html)`, `[dynamic](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic.html)`, `[enabled](https://www.elastic.co/guide/en/elasticsearch/reference/current/enabled.html)`, `[ignore_above](https://www.elastic.co/guide/en/elasticsearch/reference/current/ignore-above.html)`, `[include_in_all](https://www.elastic.co/guide/en/elasticsearch/reference/current/include-in-all.html)`, and `[properties](https://www.elastic.co/guide/en/elasticsearch/reference/current/properties.html)` parameters, which may have different settings per field.

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