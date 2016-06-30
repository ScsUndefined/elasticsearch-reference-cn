# Query DSL 查询 DSL

Elasticsearch 提供了一个功能全面的查询 DSL，这个查询 DSL 采用 JSON 格式来定义查询条件。你可以把查询 DSL 理解为一个查询的 AST,并由下面 2 中子类组成：

* 子叶型
  
  子叶查询可以在特定的字段中查找特定的值，这类的查询有诸如 [`match`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html) 查询，[`term`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-term-query.html) 查询或着 [`range`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html) 查询等。这类查询也支持嵌套查询
  
* 混合型

  ~~这边翻译地有点拗口，推荐看原文~~ 混合型查询会包装多个子叶型查询或者其他混合型查询。这会使得子查询的查询条件被组合（比如 `bool` 查询或者 `dis_max`查询）或者被反转（比如 `not` 或者 `constant_score` 查询）。
   
查询子句的行为会因为它们究竟是被用来 [查询内容还是过滤内容](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-filter-context.html) 而各不相同。

***

Elasticsearch provides a full Query DSL based on JSON to define queries. Think of the Query DSL as an AST of queries, consisting of two types of clauses:

* Leaf query clauses
  
  Leaf query clauses look for a particular value in a particular field, such as the [`match`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html), [`term`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-term-query.html) or [`range`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html) queries. These queries can be used by themselves.
  
* Compound query clauses

  Compound query clauses wrap other leaf **or** compound queries and are used to combine multiple queries in a logical fashion (such as the [`bool`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html) or [`dis_max`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-dis-max-query.html) query), or to alter their behaviour (such as the [`not`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-not-query.html) or [`constant_score`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-constant-score-query.html) query).
  
Query clauses behave differently depending on whether they are used in [query context or filter context](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-filter-context.html).