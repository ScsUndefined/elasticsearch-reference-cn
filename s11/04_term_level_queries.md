# 11.4 Term level queries Term 级查询

在进行  [全文检索](https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html) 的时候，elasticsearch在对索引进行查询之前，会先把查询条件进行一番处理，以期尽可能多地查出用户想要的数据，而 *term 级别的查询* 则只会对倒排索引中存储好的确切的 term 来进行查询，从而只查出精确匹配的结果。  

term 级的查询通常是用来查询结构化的数据的，比如数字啦，日期啦，枚举啦什么的，而不是全文字段。当然，你也可以使用 low 级别的查询来规避掉查询操作前的解析处理过程。

term 级的查询总共有这么多：

* `term` query

  查询出那些指定的字段中含有指定的词的文档
  
* `terms` query

  查询出那些指定字段中含有指定的一系列词中的任意一个的文档
  
* `range` query

  指定一个范围，如果某个文档的某个字段的值刚好在这个范围之中，则认为该文档符合查询条件

* `exists` query

  指定某个文档的某个字段，如果该值不是 null，则认为该文档符合查询条件

* `missing` query

  指定某个文档的某个字段，如果该字段不存在，或者虽然存在，但是其值却是 null，则该文档符合查询条件

* `prefix` query

  指定一个前缀，如果某个文档的某个字段的值刚好是以这个前缀起始的，则该文档符合查询条件

* `wildcard` query
  
  类似 SQL 的通配符查询，在 `wildcard` 中，你可以使用 ? 来表示任何的某个字母或者 * 表示任何的多个字母 来组建一个表达式，然后根据这个表达式去查出匹配符合条件的文档
  
* `regexp` query

  指定一个正则表达式，查询出指定字段的值符合这个则表达式的文档

* `fuzzy` query

  模糊查询会查询出近似于查询条件的结果，近似程度通过 Levenshtein 编辑距离来判断，1 或 2 个距离内的词才会被认为是近似的。
  
* `type` query

  查询出指定类型的文档
  
* `ids` query

  根据类型与 ID 查询出符合条件的文档
  
***

While the [full text queries](https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html) will analyze the query string before executing, the *term-level queries* operate on the exact terms that are stored in the inverted index.

These queries are usually used for structured data like numbers, dates, and enums, rather than full text fields. Alternatively, they allow you to craft low-level queries, foregoing the analysis process.

The queries in this group are:

1. [`term` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-term-query.html)

  Find documents which contain the exact term specified in the field specified.

2. [`terms` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-terms-query.html)
  
  Find documents which contain any of the exact terms specified in the field specified.
  
3. [`range` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html)

  Find documents where the field specified contains values (dates, numbers, or strings) in the range specified.

4. [`exists` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-exists-query.html)
  
  Find documents where the field specified contains any non-null value.
  
5. [`missing` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-missing-query.html)

  Find documents where the field specified does is missing or contains only null values.

6. [`prefix` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-prefix-query.html)

  Find documents where the field specified contains terms which being with the exact prefix specified.
  
7. [`wildcard` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-wildcard-query.html)

  Find documents where the field specified contains terms which match the pattern specified, where the pattern supports single character wildcards (`?`) and multi-character wildcards (`*`)
  
8. [`regexp` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-regexp-query.html)

  Find documents where the field specified contains terms which match the [regular expression](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-regexp-query.html#regexp-syntax) specified.
  
9. [`fuzzy` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-fuzzy-query.html)
  
  Find documents where the field specified contains terms which are fuzzily similar to the specified term. Fuzziness is measured as a [Levenshtein edit distance](http://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) of 1 or 2.
  
10. [`type` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-type-query.html)
  
  Find documents of the specified type.
  
11. [`ids` query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-ids-query.html)

  Find documents with the specified type and IDs.
  
  
  