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

~~没理解。。。~~

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

## Cutoff frequency 基于频率进行截断

匹配查询允许你根据频率来进行截断处理，该特性通过 `cutoff_frequency` 参数来设置,你可以指定一个绝对的文档频数，也可以指定一个相对的文档频数，然后高于这个值的高频词就会被转移到一个子查询中单独处理，这个子查询的处理步骤是可选的。如果 operator 是 `or` ，那么只要有一个 term 的频数低于这个值，该文档就会计算相关度评分，如果是 `and`，那么只有当所有的 term 的出现频数都低于指定的值时，这个文档才会计算相关度评分。

~~不是很理解。。。~~

这个特性允许你在运行时动态地处理 `stopwords`（无用词），这是与领域无关的，也不需要一个无用词文件。该特性使得在计算相关度评分的时候可以忽略掉高频率出现的词汇而更专注于低频率的词（低频率通常也就意味着它更有意义）。当所有的查询 term 都高于指定的 `cutoff_frequency`，那这个查询就会被自动转换成一个单纯的联合查询（`and`），以保证查询动作能被快速执行。

`cutoff_frequency` 的值如果在 `[0, 1)` 范围内，则表示它是一个相对的值，相对于文档的总数。值如果在 `[1, +∞)`范围内，则表示它是一个绝对值。

下面是一个用法示例：

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

> **重要提醒**
> 
> `cutoff_frequency` 选项是一个 per-shard 级别的操作。这也就是说如果你的测试索引文档数过少，就会导致相关度评分计算不准确。你应该遵循 [Relevance is broken](https://www.elastic.co/guide/en/elasticsearch/guide/2.x/relevance-is-broken.html) 一文中的相关建议。

## phrase 专业术语查询

`match_phrase` 查询会解析查询文本，然后在被解析的文本之外再创建一个 `phrase`，举例：

The `match_phrase` query analyzes the text and creates a `phrase` query out of the analyzed text. For example:

```bash
{
    "match_phrase" : {
        "message" : "this is a test"
    }
}
```

尽管 `match_phrase` 只是 `match` 查询下的一种 `type` (类型), 但它其实还可以这么来用：

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

一个 phrase 查询会根据一个可配置的 `slop` 的值（默认是 0 ）来匹配 term，并与顺序无关。被转置过的 term （Transposed terms） 的 `slop` 值为 2 。

`analyzer`参数用来指定查询过程中使用的解析器，如果不指明的话，就会采用默认的解析器，默认情况下会使用字段映射信息中指定的解析器，或者全局指定的查询解析器。下面是示例：

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

`match_phrase_prefix` 类似于 `match_phrase`，不同之处在于它允许对文本的最后一个 term 进行前缀匹配，举例：

```bash
{
    "match_phrase_prefix" : {
        "message" : "quick brown f"
    }
}
```

它接收一些可以作为 phrase 类型的参数。另外它也接收一个叫做 `max_expansions` 的参数（默认值是 `50`），该参数可以控制最后一个 term 会被扩展成多少个前缀。gang 真，强烈推荐你把它设置成一个可接受的值来控制查询操作的执行时间，示例代码如下：

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

> **重要特性**
> 
> `match_phrase_prefix` 查询灰常得易用，你可以轻而易举地使用该功能来达到`自动补全`的目的，但相对的，尽管通常情况下，这种查询方式的查询结果会很人满意，但有时候也会查出一些令人摸不着头脑的结果。
>
> 以 `quick brown f` 为例。这个查询就会在 `quick` 和 `brown` 之外再创建一个 phase 查询（即 term `quick` 必须存在，而且必须紧跟在 term `brown` 之后），然后它会在存储好的 term 字典里查找前 50 个以 `f` 开头的 term，把它们都加到 phrase 查询中。
> 
> 问题是用户可能想查找 `quick brown fox` 而 `fox` 又不在前 50 个里，所以用户想要找的就没找到，但其实这通常而言也不是什么问题，因为用户会接着输入，知道他们想要找的东西出现为止。
>
> 更好的有关 `自动补全` 的解决方案，参见 [completion suggester](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html)。

***

A family of match queries that accepts text/numerics/dates, analyzes them, and constructs a query. For example:

```bash
{
    "match" : {
        "message" : "this is a test"
    }
}
```

Note, message is the name of a field, you can substitute the name of any field (including `_all`) instead.

There are three types of match query: boolean, phrase, and phrase_prefix:

## boolean

The default match query is of type boolean. It means that the text provided is analyzed and the analysis process constructs a boolean query from the provided text. The operator flag can be set to or or and to control the boolean clauses (defaults to or). The minimum number of optional should clauses to match can be set using the minimum_should_match parameter.

The analyzer can be set to control which analyzer will perform the analysis process on the text. It defaults to the field explicit mapping definition, or the default search analyzer.

The lenient parameter can be set to true to ignore exceptions caused by data-type mismatches, such as trying to query a numeric field with a text query string. Defaults to false.

## Fuzziness

fuzziness allows fuzzy matching based on the type of field being queried. See the section called “Fuzzinessedit” for allowed settings.

The `prefix_length` and `max_expansions` can be set in this case to control the fuzzy process. If the fuzzy option is set the query will use `top_terms_blended_freqs_${max_expansions}` as its rewrite method the fuzzy_rewrite parameter allows to control how the query will get rewritten.

Fuzzy transpositions (ab → ba) are allowed by default but can be disabled by setting fuzzy_transpositions to false.

Here is an example when providing additional parameters (note the slight change in structure, message is the field name):

```bash
{
    "match" : {
        "message" : {
            "query" : "this is a test",
            "operator" : "and"
        }
    }
}
```

## Zero terms query

If the analyzer used removes all tokens in a query like a stop filter does, the default behavior is to match no documents at all. In order to change that the zero_terms_query option can be used, which accepts none (default) and all which corresponds to a match_all query.

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

The match query supports a cutoff_frequency that allows specifying an absolute or relative document frequency where high frequency terms are moved into an optional subquery and are only scored if one of the low frequency (below the cutoff) terms in the case of an or operator or all of the low frequency terms in the case of an and operator match.

This query allows handling stopwords dynamically at runtime, is domain independent and doesn’t require a stopword file. It prevents scoring / iterating high frequency terms and only takes the terms into account if a more significant / lower frequency term matches a document. Yet, if all of the query terms are above the given cutoff_frequency the query is automatically transformed into a pure conjunction (and) query to ensure fast execution.

The cutoff_frequency can either be relative to the total number of documents if in the range [0..1) or absolute if greater or equal to 1.0.

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
> The cutoff_frequency option operates on a per-shard-level. This means that when trying it out on test indexes with low document numbers you should follow the advice in Relevance is broken.

## phrase

The match_phrase query analyzes the text and creates a phrase query out of the analyzed text. For example:

```bash
{
    "match_phrase" : {
        "message" : "this is a test"
    }
}
```

Since match_phrase is only a type of a match query, it can also be used in the following manner:

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

A phrase query matches terms up to a configurable slop (which defaults to 0) in any order. Transposed terms have a slop of 2.

The analyzer can be set to control which analyzer will perform the analysis process on the text. It defaults to the field explicit mapping definition, or the default search analyzer, for example:

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

It accepts the same parameters as the phrase type. In addition, it also accepts a max_expansions parameter (default 50) that can control to how many prefixes the last term will be expanded. It is highly recommended to set it to an acceptable value to control the execution time of the query. For example:

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
>  The match_phrase_prefix query is a poor-man’s autocomplete. It is very easy to use, which let’s you get started quickly with search-as-you-type but it’s results, which usually are good enough, can sometimes be confusing.
> 
> Consider the query string quick brown f. This query works by creating a phrase query out of quick and brown (i.e. the term quick must exist and must be followed by the term brown). Then it looks at the sorted term dictionary to find the first 50 terms that begin with f, and adds these terms to the phrase query.
> 
> The problem is that the first 50 terms may not include the term fox so the phase quick brown fox will not be found. This usually isn’t a problem as the user will continue to type more letters until the word they are looking for appears.
> 
> For better solutions for search-as-you-type see the completion suggester and


