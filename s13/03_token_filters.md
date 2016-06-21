# 13.3 Token Filters 标记过滤器

标记过滤器从 [分词器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 那获取一个包含有多个标记的数据流，并能够修改标记（比如将所有字母转为全小写格式），删除标记（比如删除停词）或者增加标记（比如添加同义词）等。

Elasticsearch 有许多内置的标记分词器，你可以用它们来构建一个 [自定义的解析器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)。

***

Token filters accept a stream of tokens from a [tokenizer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenizers.html) and can modify tokens (eg lowercasing), delete tokens (eg remove stopwords) or add tokens (eg synonyms).

Elasticsearch has a number of built in token filters which can be used to build [custom analyzers](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-custom-analyzer.html).