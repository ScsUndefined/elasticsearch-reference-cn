# 13.1 Analyzers 解析器

解析器是由单个 [分词器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 和零或多个 [标记过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 组成的。数据在经由分词器处理前，可能还要先被一个或多个 [字符过滤器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 预处理一下。解析模块允许你自行命名`解析器`，你的命名将会在映射定义信息以及某些 API 中被引用。

Elasticsearch 已经构建好了一些备用的解析器供用户使用，当然你也可以自行组合字符过滤器，分词器和标记过滤器来组成一个 [自定义的解析器](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)。

## 默认的解析器

一个解析器需要以一个逻辑名来被注册，随后，它就可以在映射定义信息或者某些 API 中被引用。当没有解析器被定义的时候，elasticsearch 就会采用默认配置。你可以指定，当找不到派生的解析器时，系统究竟采用哪个默认的解析器。

`default`项允许你设置用户索引和检索 API 的默认解析器。`default_search`则可以被用来设置仅在搜索过程中使用的默认解析器（此时索引过程中依然使用的是`default`项指明的解析器）。

举个例子，下面的设置使得默认情况下，查询动作都是精确查询：

```bash
index :
  analysis :
    analyzer :
      default :
        tokenizer : keyword
```

## 解析器的别名

你也可以使用多个被注册的，用于查找的名称来关联解析器，即别名。举个例子，下面这段代码会使得`standard`解析器同时被别名为`alias1`和`alias2`。

```bash
index :
  analysis :
    analyzer :
      standard :
        alias: [alias1, alias2]
        type : standard
        stopwords : [test1, test2, test3]
```

我们将在后续章节逐步介绍一些内置的解析器。

***

Analyzers are composed of a single [Tokenizer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenizers.html) and zero or more [TokenFilters](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-tokenfilters.html). The tokenizer may be preceded by one or more [CharFilters](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-charfilters.html). The analysis module allows you to register `Analyzers` under logical names which can then be referenced either in mapping definitions or in certain APIs.

Elasticsearch comes with a number of prebuilt analyzers which are ready to use. Alternatively, you can combine the built in character filters, tokenizers and token filters to create [custom analyzers](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-custom-analyzer.html).

## Default Analyzers

An analyzer is registered under a logical name. It can then be referenced from mapping definitions or certain APIs. When none are defined, defaults are used. There is an option to define which analyzers will be used by default when none can be derived.

The `default` logical name allows one to configure an analyzer that will be used both for indexing and for searching APIs. The `default_search` logical name can be used to configure a default analyzer that will be used just when searching (the `default` analyzer would still be used for indexing).

For instance, the following settings could be used to perform exact matching only by default:

```bash
index :
  analysis :
    analyzer :
      default :
        tokenizer : keyword
```

## Aliasing Analyzers

Analyzers can be aliased to have several registered lookup names associated with them. For example, the following will allow the `standard` analyzer to also be referenced with `alias1` and `alias2` values.

```bash
index :
  analysis :
    analyzer :
      standard :
        alias: [alias1, alias2]
        type : standard
        stopwords : [test1, test2, test3]
```

Below is a list of the built in analyzers.
