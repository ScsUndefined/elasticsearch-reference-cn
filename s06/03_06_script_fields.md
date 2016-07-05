# 6.3.6 Script Fields script 字段

script 字段允许你使用脚本去对每个查询结果的各个字段进行运算求知（即 [脚本计算](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting.html) ），举个例子：

```javascript
{
    "query" : {
        ...
    },
    "script_fields" : {
        "test1" : {
            "script" : "doc['my_field_name'].value * 2"
        },
        "test2" : {
            "script" : {
                "inline": "doc['my_field_name'].value * factor",
                "params" : {
                    "factor"  : 2.0
                }
            }
        }
    }
}
```

该特性一样可以适用于 unstored 的字段（比如上面例子中的 `my_field_name`）最终会返回根据脚本定义的运算规则计算出来的值（即自定义的值）。

脚本可以触及到实际被索引的文档，即 `_source` 字段，你可以从中提取出指定的元素（`object` 类型的也可以），下面举个例子：

```javascript
{
    "query" : {
        ...
    },
    "script_fields" : {
        "test1" : {
            "script" : "_source.obj1.obj2"
        }
    }
}
```

需要注意的是，访问 `_source` 关键字的时候采用的是类 json 的调用语法（Note the `_source` keyword here to navigate the json-like model）

然后你需要清楚 `doc['my_field'].value` 和 `_source.my_field` 之间的区别。首先使用 `doc` 关键字会导致那个字段被加载到内存中（被缓存），这是为了更快速地运算，但同时也增加了内存的开销。并且 `doc[...]` 只能用于存放着简单的值的字段，而不能用于存放有 json 对象的那种复杂字段，而且仅在处理 non-analyzed 型的字段或者单一 term 型的字段时才有意义。

而 `_source` 则会导致源数据被加载，被解析，然后返回相关的部分。

***

Allows to return a [script evaluation](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting.html) (based on different fields) for each hit, for example:

```javascript
{
    "query" : {
        ...
    },
    "script_fields" : {
        "test1" : {
            "script" : "doc['my_field_name'].value * 2"
        },
        "test2" : {
            "script" : {
                "inline": "doc['my_field_name'].value * factor",
                "params" : {
                    "factor"  : 2.0
                }
            }
        }
    }
}
```

Script fields can work on fields that are not stored (`my_field_name` in the above case), and allow to return custom values to be returned (the evaluated value of the script).

Script fields can also access the actual `_source` document indexed and extract specific elements to be returned from it (can be an "object" type). Here is an example:

```javascript
{
    "query" : {
        ...
    },
    "script_fields" : {
        "test1" : {
            "script" : "_source.obj1.obj2"
        }
    }
}
```

Note the `_source` keyword here to navigate the json-like model.

It’s important to understand the difference between `doc['my_field'].value` and `_source.my_field`. The first, using the doc keyword, will cause the terms for that field to be loaded to memory (cached), which will result in faster execution, but more memory consumption. Also, the `doc[...]` notation only allows for simple valued fields (can’t return a json object from it) and make sense only on non-analyzed or single term based fields.

The `_source` on the other hand causes the source to be loaded, parsed, and then only the relevant part of the json is returned.


