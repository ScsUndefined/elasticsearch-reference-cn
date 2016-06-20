# Analysis

索引解析模块可以看作是一个解析器的登记处，它是可配置的。而解析器则是用来，当一个文档被索引后，用来分割被索引（被索引）的字段的，它也可以用来处理查询字符串。它映射到 Lucene 的`解析器`。

解析器由单个 [分词器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 和零或多个[标记过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)（TokenFilters）。在分词器的前面可能还有一个或多个 [字符过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)。

Analyzers are composed of a single [Tokenizer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenizers.html) and zero or more [TokenFilters](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenfilters.html). The tokenizer may be preceded by one or more [CharFilters](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-charfilters.html). The analysis module allows one to register `TokenFilters`, `Tokenizers` and `Analyzers` under logical names that can then be referenced either in mapping definitions or in certain APIs. The Analysis module automatically registers (**if not explicitly defined**) built in analyzers, token filters, and tokenizers.

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

All analyzers, tokenizers, and token filters can be configured with a version parameter to control which Lucene version behavior they should use. Possible values are: 3.0 - 3.6, 4.0 - 4.3 (the highest version number is the default option).