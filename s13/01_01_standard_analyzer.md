# 13.1.1 Standard Analyzer 标准解析器

所谓`标准`类型的解析器，指的是那些由 [标准分词器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 以及 [标准标记过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) ，[小写标记过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 和 停顿标记过滤器组成的解析器。

`标准`类型的解析器都以下几个配置项：

| 配置项名称 | 该配置项的作用 |
| ------- | ----------- |
| `stopwords` | 一个停顿符号的列表，用来初始化停顿过滤器。默认情况下这个列表是空的。更多信息在 [Stop Analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-stop-analyzer.html) 一文中。 |
| `max_token_length` | 最大的分词数量The maximum token length. If a token is seen that exceeds this length then it is split at `max_token_length` intervals. Defaults to `255`. |

***

An analyzer of type `standard` is built using the [Standard Tokenizer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-standard-tokenizer.html) with the [Standard Token Filter](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-standard-tokenfilter.html), [Lower Case Token Filter](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lowercase-tokenfilter.html), and Stop Token Filter.

The following are settings that can be set for a `standard` analyzer type:

| Setting | Description |
| ------- | ----------- |
| `stopwords` | A list of stopwords to initialize the stop filter with. Defaults to an *empty* stopword list Check [Stop Analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-stop-analyzer.html) for more details. |
| `max_token_length` | The maximum token length. If a token is seen that exceeds this length then it is split at `max_token_length` intervals. Defaults to `255`. |