# 6.3.7 Field Data Fields fielddata_fields参数

该参数允许你指定一个键值对，键为字段名，值为字段值，然后在搜索结果返回的时候，每个匹配的数据中都会返回这个字段和值，哪怕事前这个字段并没有 store，下面是简单的示例：

```javascript
{
    "query" : {
        ...
    },
    "fielddata_fields" : ["test1", "test2"]
}
```

需要注意的是，`fielddata_fields`会导致这个字段的 terms 被加载到内存中（被缓存），即会增加内存的开销。


It’s important to understand that using the `fielddata_fields` parameter will cause the terms for that field to be loaded to memory (cached), which will result in more memory consumption.

***

Allows to return the field data representation of a field for each hit, for example:

```javascript
{
    "query" : {
        ...
    },
    "fielddata_fields" : ["test1", "test2"]
}
```

Field data fields can work on fields that are not stored.

It’s important to understand that using the `fielddata_fields` parameter will cause the terms for that field to be loaded to memory (cached), which will result in more memory consumption.