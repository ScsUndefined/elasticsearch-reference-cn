# 6.1 Search 查询

查询分两种，一种把查询条件写在一个字符串里，即 [query string as a parameter](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html) ，一种则把查询条件写在一个查询文本中，即 [request body](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)。

## 多索引，多类型

所有的查询 API 都可以针对一个索引中的多个类型进行查询，也可以使用 [多索引语法](https://www.elastic.co/guide/en/elasticsearch/reference/current/multi-index.html) 来同时对多个索引进行查询。假设我们现在有一个存储了微博信息的索引，我们如果需要针对它的所有文档的所有类型进行查询的话，就可以这么来写：

```bash
$ curl -XGET 'http://localhost:9200/twitter/_search?q=user:kimchy'
```

我们针对某些特定的类型进行查询：

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet,user/_search?q=user:kimchy'
```

你也可以在多个索引中通过标签来查询所有相关的博文（在此，我们假设每个用户都有它们自己的索引）：

```bash
$ curl -XGET 'http://localhost:9200/kimchy,elasticsearch/tweet/_search?q=tag:wow'
```

或者我们可以通过 `_all` 占位符来查询所有索引中的 tweet 类型的数据：

```bash
$ curl -XGET 'http://localhost:9200/_all/tweet/_search?q=tag:wow'
```

或者，更宽泛点，我们直接查询所有索引的所有类型：

```bash
$ curl -XGET 'http://localhost:9200/_search?q=tag:wow'
```

***

The search API allows you to execute a search query and get back search hits that match the query. The query can either be provided using a simple [query string as a parameter](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html), or using a [request body](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html).

## Multi-Index, Multi-Type

All search APIs can be applied across multiple types within an index, and across multiple indices with support for the [multi index syntax](https://www.elastic.co/guide/en/elasticsearch/reference/current/multi-index.html). For example, we can search on all documents across all types within the twitter index:

```bash
$ curl -XGET 'http://localhost:9200/twitter/_search?q=user:kimchy'
```

We can also search within specific types:

```bash
$ curl -XGET 'http://localhost:9200/twitter/tweet,user/_search?q=user:kimchy'
```

We can also search all tweets with a certain tag across several indices (for example, when each user has his own index):

```bash
$ curl -XGET 'http://localhost:9200/kimchy,elasticsearch/tweet/_search?q=tag:wow'
```

Or we can search all tweets across all available indices using `_all` placeholder:

```bash
$ curl -XGET 'http://localhost:9200/_all/tweet/_search?q=tag:wow'
```

Or even search across all indices and all types:

```bash
$ curl -XGET 'http://localhost:9200/_search?q=tag:wow'
```