# 6.3.4 Source filtering 筛选所需的原始数据

默认情况下 Elasticsearch 会把匹配到的结果，它入索引时候的原始数据原样返回出来，这原始数据会通过 `_source` 字段返回。你也可以通过 `fields` 参数来控制 Elasticsearch 的这一特性或者通过 `_source` 参数来禁止 Elasticsearch 返回源数据。

要禁用的话，只要把 `_source` 设置成 `false` 即可：

```bash
{
    "_source": false,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

`_source` 也支持通配符，你可以通过使用通配符来指明你需要返回源数据的哪部分信息，比如这样：

```bash
{
    "_source": "obj.*",
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

或者这样：

```bash
{
    "_source": [ "obj1.*", "obj2.*" ],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

最后演示的是，怎么通过 `include` 和 `exclude` 来进行更精准地筛选需要返回的源数据

```bash
{
    "_source": {
        "include": [ "obj1.*", "obj2.*" ],
        "exclude": [ "*.description" ]
    },
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

***


Allows to control how the `_source` field is returned with every hit.

By default operations return the contents of the `_source` field unless you have used the `fields` parameter or if the `_source` field is disabled.

You can turn off `_source` retrieval by using the `_source` parameter:

To disable `_source` retrieval set to `false`:

```bash
{
    "_source": false,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

The `_source` also accepts one or more wildcard patterns to control what parts of the `_source` should be returned:

For example:

```bash
{
    "_source": "obj.*",
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

Or

```bash
{
    "_source": [ "obj1.*", "obj2.*" ],
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

Finally, for complete control, you can specify both include and exclude patterns:

```bash
{
    "_source": {
        "include": [ "obj1.*", "obj2.*" ],
        "exclude": [ "*.description" ]
    },
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```