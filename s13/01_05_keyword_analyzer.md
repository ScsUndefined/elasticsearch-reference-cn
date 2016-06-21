# 13.1.5 Keyword Analyzer 关键字解析器

`关键字`类型的解析器会将整个分词当作一个不可再分割的对象来处理。这在处理类似邮政编码，id号等数据的时候会非常有用。但要注意的是，使用`关键字`解析器来处理不如直接把字段标记为`not_analyzed`来得更直观。

***

An analyzer of type `keyword` that "tokenizes" an entire stream as a single token. This is useful for data like zip codes, ids and so on. Note, when using mapping definitions, it might make more sense to simply mark the field as `not_analyzed`.