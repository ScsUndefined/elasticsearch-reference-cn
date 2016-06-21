# Analysis

索引解析模块可以看作是一个解析器的登记处，它是可配置的。而解析器则是用来，当一个文档被索引后，用来分割被索引（被索引）的字段的，它也可以用来处理查询字符串。它映射到 Lucene 的`解析器`。

解析器由单个 [分词器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 和零或多个[标记过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)（TokenFilters）。在分词器的前面可能还有一个或多个 [字符过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)。解析模块允许你使用合理的名字来命名`标记过滤器`，`分词器`和`解析器`，这些名字会在映射的定义信息或者一些 API 中被引用。解析模块会自动注册（如果你没有额外地进行定义的话）内置的解析器，标记过滤器和分词器。

下面是一个配置信息的范例：

```bash
index :
    analysis :
        analyzer :
            standard :
                type : standard
                stopwords : [stop1, stop2]
            myAnalyzer1 :
                type : standard
                stopwords : [stop1, stop2, stop3]
                max_token_length : 500
            # configure a custom analyzer which is
            # exactly like the default standard analyzer
            myAnalyzer2 :
                tokenizer : standard
                filter : [standard, lowercase, stop]
        tokenizer :
            myTokenizer1 :
                type : standard
                max_token_length : 900
            myTokenizer2 :
                type : keyword
                buffer_size : 512
        filter :
            myTokenFilter1 :
                type : stop
                stopwords : [stop1, stop2, stop3, stop4]
            myTokenFilter2 :
                type : length
                min : 0
                max : 2000
```

## 向后兼容

所有的解析器，分词器和标记过滤器都可以用版本号为参数来进行控制，使得它们拥有对应 Lucene 版本的行为特征。支持的版本有：3.0-3.6，4.0-4.3（默认采用最高的版本）。

***

The index analysis module acts as a configurable registry of Analyzers that can be used in order to both break indexed (analyzed) fields when a document is indexed and process query strings. It maps to the Lucene `Analyzer`.

Analyzers are composed of a single [Tokenizer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenizers.html) and zero or more [TokenFilters](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenizers.html). The tokenizer may be preceded by one or more [CharFilters](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-charfilters.html). The analysis module allows one to register `TokenFilters`, `Tokenizers` and `Analyzers` under logical names that can then be referenced either in mapping definitions or in certain APIs. The Analysis module automatically registers (**if not explicitly defined**) built in analyzers, token filters, and tokenizers.

Here is a sample configuration:

```bash
index :
    analysis :
        analyzer :
            standard :
                type : standard
                stopwords : [stop1, stop2]
            myAnalyzer1 :
                type : standard
                stopwords : [stop1, stop2, stop3]
                max_token_length : 500
            # configure a custom analyzer which is
            # exactly like the default standard analyzer
            myAnalyzer2 :
                tokenizer : standard
                filter : [standard, lowercase, stop]
        tokenizer :
            myTokenizer1 :
                type : standard
                max_token_length : 900
            myTokenizer2 :
                type : keyword
                buffer_size : 512
        filter :
            myTokenFilter1 :
                type : stop
                stopwords : [stop1, stop2, stop3, stop4]
            myTokenFilter2 :
                type : length
                min : 0
                max : 2000
```

## Backwards compatibility

All analyzers, tokenizers, and token filters can be configured with a `version` parameter to control which Lucene version behavior they should use. Possible values are: `3.0` - `3.6`, `4.0` - `4.3` (the highest version number is the default option).