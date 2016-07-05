# 11.3 Full text queries 全文检索

Elasticsearch 的高级全文检索功能通常被用来处理针对类似“对电子邮件的正文部分进行全文检索”这样子的需求。该功能自己能识别出被检索的字段是否被解析过，并在执行操作之前使用该字段的解析器 ~~通过 `analyzer` 指定~~（或者查询专用的解析器 ~~通过 `search_analyzer` 指定~~ ）来先对查询文本进行一次解析：

高级全文检索功能可以细分为以下这几个子类：

* [`match` 查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html) 
  
  最常规的全文检索查询方式，包括拼写纠正和特有名词查询或者相关词组查询等

* [`multi_match` 查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-multi-match-query.html)
  
  即同时针对多个字段进行 **`match`** 查询
  
* [`common_terms` 查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-common-terms-query.html)

  一个更专业话的查询方式，当你需要对一些非常规的词汇，一些比较特有的词汇进行查询时，就可以使用 `common_terms` 查询，它提供了更多的配置项给你，让你进一步定义你的查询行为
  
* [`query_string` 查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)

  支持紧凑的 Lucene [query string syntax](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-syntax)，允许你指定在单个查询字符串内指定与|或|非条件以及指定多字段查询。这一查询方式仅适用于有经验的专家级用户
  
* [`simple_query_string`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-simple-query-string-query.html)
  
  一个简化过的，同时也因此变得更健壮的 **`query_string`** 语法，因此可以直接暴露给用户

***

The high-level full text queries are usually used for running full text queries on full text fields like the body of an email. They understand how the field being queried is [analyzed](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis.html) and will apply each field’s `analyzer` (or `search_analyzer`) to the query string before executing.

The queries in this group are:

* [`match` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html)
  
  The standard query for performing full text queries, including fuzzy matching and phrase or proximity queries.

* [`multi_match` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-multi-match-query.html)
  
  The multi-field version of the **`match`** query.
  
* [`common_terms` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-common-terms-query.html)

  A more specialized query which gives more preference to uncommon words.
  
* [`query_string` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)

  Supports the compact Lucene [query string syntax](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-syntax), allowing you to specify AND|OR|NOT conditions and multi-field search within a single query string. For expert users only.
  
* [`simple_query_string`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-simple-query-string-query.html)
  
  A simpler, more robust version of the **`query_string`** syntax suitable for exposing directly to users.
