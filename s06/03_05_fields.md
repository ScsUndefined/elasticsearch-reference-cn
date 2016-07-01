# 6.3.5 Fields fields参数

> **警告**
> 
> `fields` 字段可以用来指定需要返回的字段，默认是不启用这一参数的，并且通常情况下也不推荐使用这个参数。你可以使用 [source filtering](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-source-filtering.html) 来替代它，来返回需要的原始数据的子集。

下面是一个使用 `fields` 参数来获取匹配的文档的部分字段的一个示例：

```javascript
{
    "fields" : ["user", "postDate"],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

`fields` 同样也接受通配符。

> **警告**
> 
> 只有 *stored* 的字段才能用通配符来获取。 ~~哈？不懂。。。~~

举例：

```bash
{
    "fields": ["xxx*", "*xxx", "*xxx*", "xxx*yyy", "user", "postDate"],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

`*` 可以用来表示返回所有字段。

而如果你像下面这样把 `fields` 的值指定为一个空的数据的话，那么结果就只会返回 `_id` 和 `_type` 了：

```javascript
{
    "fields" : [],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

为了向后兼容，如果你指定了那些没有保存的字段（`store`映射被设置成`false`的），那它就会加载`_source`然后从中提取该字段。这个功能其实也可以用 source filtering 参数来达到。

从文档中获取的字段值本身永远都会当做一个数组来返回。而诸如 `_routing` 或 `_parent` 之类的元数据字段则绝逼不会被当做一个数组来返回。

因为只有叶子般的子字段可以通过 `field` 操作来返回。所以对象型的字段是不能通过这种方式来返回的，而你非要这么做的话就会导致整个搜索请求失败。

而脚本字段的话则会被自动探测并被当做字段来使用，所以类似 `_source.obj1.field1` 的东西一样可以用，尽管这并不推荐，类似  `obj1.field1`  也一样可以生效。

***


> **WARNNING**
> 
> The `fields` parameter is about fields that are explicitly marked as stored in the mapping, which is off by default and generally not recommended. Use [source filtering](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-source-filtering.html) instead to select subsets of the original source document to be returned.

Allows to selectively load specific stored fields for each document represented by a search hit.

```javascript
{
    "fields" : ["user", "postDate"],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

`fields` also accepts one or more wildcard patterns to control which fields of the document should be returned. WARNING: Only *stored* fields can be retrieved with wildcard patterns.

For example:

```bash
{
    "fields": ["xxx*", "*xxx", "*xxx*", "xxx*yyy", "user", "postDate"],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

`*` can be used to load all stored fields from the document.

An empty array will cause only the `_id` and `_type` for each hit to be returned, for example:

```javascript
{
    "fields" : [],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

For backwards compatibility, if the fields parameter specifies fields which are not stored (`store` mapping set to `false`), it will load the `_source` and extract it from it. This functionality has been replaced by the source filtering parameter.

Field values fetched from the document it self are always returned as an array. Metadata fields like `_routing` and `_parent` fields are never returned as an array.

Also only leaf fields can be returned via the `field` option. So object fields can’t be returned and such requests will fail.

Script fields can also be automatically detected and used as fields, so things like `_source.obj1.field1` can be used, though not recommended, as `obj1.field1` will work as well.
