# 11.3.1 Match Query 匹配查询

匹配查询接收 文本/数组/日期 类型的查询条件，然后进行分析，最后组成一个查询动作。举例：

```javascript
{
    "match" : {
        "message" : "this is a test"
    }
}
```

要注意的是上面这个示例中的 `message` 只是被检索的字段的字段名，你可以用任何字段的名字来代替它，包括 `_all`。

有三种类型的匹配查询：`boolean`,`phrase`, 以及 `phrase_prefix`:

## boolean 布尔型

默认的匹配查询是 `boolean` 型的。它会解析输入的查询文本然后用解析结果构建出一个查询动作。`operator`标记可以设置成 `or` 或者 `and` 来控制布尔子句（默认是 `or`）。`should`子句的最小满足数则可以使用 [`minimum_should_match`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-minimum-should-match.html) 参数来设置。

`analyzer`可以用来设置解析查询文本时所用的解析器。默认情况下是使用了字段的映射信息中所写明的解析器或者全局的默认查询解析器。

`lenient`参数也可以用来设置是否需要忽视类型转换错误。比如要查询一个数值型的字段，输入的查询条件文本确是一段文字。默认情况下是`false`,即不忽视，设置成`true`则会忽视掉。

## Fuzziness 模糊查询

`fuzziness` 允许基于被检索的字段的数据类型而进行模糊查询。它的可配置项可以自行查阅 [Fuzzinessedit](https://www.elastic.co/guide/en/elasticsearch/reference/current/common-options.html#fuzziness) 一章，该功能主要用来处理拼写错误。

`prefix_length` 和 `max_expansions` 参数可以控制模糊查询的模糊程度。如果进行模糊查询的话，就会使用 `top_terms_blended_freqs_${max_expansions}` 来作为它的重写方法。而 `fuzzy_rewrite` 参数则允许控制查询是否会被重写。

位置交换型的模糊修正操作默认情况下是被开启的，比如`ab`被修正为`ba`,但你也可以通过把 `fuzzy_transpositions` 设置成 `false` 开禁用它。

下面是一个启用了附加参数的匹配查询（注意结构上的变化）：

```javascript
{
    "match" : {
        "message" : {
            "query" : "this is a test",
            "operator" : "and"
        }
    }
}
```

## Zero terms query 零 terms 型查询

如果解析器移除了一个查询体中的所有词（就像`stop`过滤器就会删除查询文本中的它认为不重要的词），那就会导致最终没有查到任何相关的文档。为了改变这种状况，Elasticsearch 就提供了名为 `zero_terms_query` 配置项，它可以接收 `none`（这同时也是默认值） 或者 `all` （相当于 `match_all` 查询）

```bash
{
    "match" : {
        "message" : {
            "query" : "to be or not to be",
            "operator" : "and",
            "zero_terms_query": "all"
        }
    }
}
```

## Cutoff frequency

The match query supports a `cutoff_frequency` that allows specifying an absolute or relative document frequency where high frequency terms are moved into an optional subquery and are only scored if one of the low frequency (below the cutoff) terms in the case of an `or` operator or all of the low frequency terms in the case of an `and` operator match.

This query allows handling `stopwords` dynamically at runtime, is domain independent and doesn’t require a stopword file. It prevents scoring / iterating high frequency terms and only takes the terms into account if a more significant / lower frequency term matches a document. Yet, if all of the query terms are above the given `cutoff_frequency` the query is automatically transformed into a pure conjunction (`and`) query to ensure fast execution.

The `cutoff_frequency` can either be relative to the total number of documents if in the range `[0..1)` or absolute if greater or equal to `1.0`.

Here is an example showing a query composed of stopwords exclusively:

```bash
{
    "match" : {
        "message" : {
            "query" : "to be or not to be",
            "cutoff_frequency" : 0.001
        }
    }
}
```

> **Important**
> 
> The `cutoff_frequency` option operates on a per-shard-level. This means that when trying it out on test indexes with low document numbers you should follow the advice in [Relevance is broken](https://www.elastic.co/guide/en/elasticsearch/guide/2.x/relevance-is-broken.html).

## phrase

The `match_phrase` query analyzes the text and creates a `phrase` query out of the analyzed text. For example:

```bash
{
    "match_phrase" : {
        "message" : "this is a test"
    }
}
```

Since `match_phrase` is only a `type` of a `match` query, it can also be used in the following manner:

```bash
{
    "match" : {
        "message" : {
            "query" : "this is a test",
            "type" : "phrase"
        }
    }
}
```

A phrase query matches terms up to a configurable `slop` (which defaults to 0) in any order. Transposed terms have a slop of 2.

The `analyzer` can be set to control which analyzer will perform the analysis process on the text. It defaults to the field explicit mapping definition, or the default search analyzer, for example:

```bash
{
    "match_phrase" : {
        "message" : {
            "query" : "this is a test",
            "analyzer" : "my_analyzer"
        }
    }
}
```

## match_phrase_prefix

The `match_phrase_prefix` is the same as `match_phrase`, except that it allows for prefix matches on the last term in the text. For example:

```bash
{
    "match_phrase_prefix" : {
        "message" : "quick brown f"
    }
}
```

It accepts the same parameters as the phrase type. In addition, it also accepts a `max_expansions` parameter (default `50`) that can control to how many prefixes the last term will be expanded. It is highly recommended to set it to an acceptable value to control the execution time of the query. For example:

```bash
{
    "match_phrase_prefix" : {
        "message" : {
            "query" : "quick brown f",
            "max_expansions" : 10
        }
    }
}
```

> **Important**
> 
> The `match_phrase_prefix` query is a poor-man’s autocomplete. It is very easy to use, which let’s you get started quickly with *search-as-you-type* but it’s results, which usually are good enough, can sometimes be confusing.
>
> Consider the query string `quick brown f`. This query works by creating a phrase query out of `quick` and `brown` (i.e. the term `quick` must exist and must be followed by the term `brown`). Then it looks at the sorted term dictionary to find the first 50 terms that begin with `f`, and adds these terms to the phrase query.
>
> The problem is that the first 50 terms may not include the term `fox` so the phase `quick brown fox` will not be found. This usually isn’t a problem as the user will continue to type more letters until the word they are looking for appears.
>
> For better solutions for *search-as-you-type* see the [completion suggester](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html) and


