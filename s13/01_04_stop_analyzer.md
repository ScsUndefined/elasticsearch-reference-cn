# 13.1.4 Stop Analyzer 停词解析器

停词解析器是由一个 [小写分词器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 和一个 [停词标记过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 一起组成的。

下面是停词解析器的一些配置项：

| 配置项 |	相关描述 |
| ----- | ------- |
| `stopwords` | 一个停词列表，用来初始化停词过滤器。默认采用英语中的停词。 |
| `stopwords_path` | 指向停词配置文件的路径（可以是相对于 config 文件夹的相对路径，也可以是绝对路径）|

可以使用 `stopwords: \_none\_` 来指定一个 *空的* 停词列表。

***

An analyzer of type `stop` that is built using a [Lower Case Tokenizer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lowercase-tokenizer.html), with [Stop Token Filter](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-stop-tokenfilter.html).

The following are settings that can be set for a `stop` analyzer type:

| Setting |	Description |
| ------- | ----------- |
| `stopwords` | A list of stopwords to initialize the stop filter with. Defaults to the english stop words. |
| `stopwords_path` | A path (either relative to config location, or absolute) to a stopwords file configuration.|

Use `stopwords: \_none\_` to explicitly specify an *empty* stopword list.