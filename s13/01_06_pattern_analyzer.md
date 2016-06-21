# 13.1.6 Pattern Analyzer 正则解析器

正则解析器可以通过一个正则表达式来切分文本。它有以下的配置项：

| 配置项 | 描述 |
| -------- | ----------- |
| `lowercase` | 是否需要被转成小写，默认是要的。 |
| `pattern` | 用来切分文本的正则表达式。默认是 `\W+`。 |
| `flags` | 正则表达式的标识。 |
| `stopwords` | 一个用来初始化停词过滤器的停词表。默认是*空的*。详情查阅 [Stop Analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-stop-analyzer.html)。|

> **郑重声明**
> 
> 正则表达式是用来匹配 **分隔符** 的，而不是用来匹配被分割的字符串本身。

多个标识应该相互隔离，类似`"CASE_INSENSITIVE|COMMENTS"`。有关`flags`的更多细节请直接查看 [Java Pattern API](http://download.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary)。

### 正则解析器示例

要试验这些示例，你得先删掉原有的测试用索引。

### 空格分词器

```bash
DELETE test

PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "whitespace": {
          "type": "pattern",
          "pattern": "\\s+"
        }
      }
    }
  }
}

GET /test/_analyze?analyzer=whitespace&text=foo,bar baz

# "foo,bar", "baz"
```
[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/analysis-pattern-analyzer/1.json) 

### 非文字分析器

```bash
DELETE test

PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "nonword": {
          "type": "pattern",
          "pattern": "[^\\w]+" 
        }
      }
    }
  }
}

GET /test/_analyze?analyzer=nonword&text=foo,bar baz
# "foo,bar baz" becomes "foo", "bar", "baz"

GET /test/_analyze?analyzer=nonword&text=type_1-type_4
# "type_1","type_4"
```

[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/analysis-pattern-analyzer/2.json)

### 驼峰分析器

```bash
DELETE test

PUT /test?pretty=1
{
  "settings": {
    "analysis": {
      "analyzer": {
        "camel": {
          "type": "pattern",
          "pattern": "([^\\p{L}\\d]+)|(?<=\\D)(?=\\d)|(?<=\\d)(?=\\D)|(?<=[\\p{L}&&[^\\p{Lu}]])(?=\\p{Lu})|(?<=\\p{Lu})(?=\\p{Lu}[\\p{L}&&[^\\p{Lu}]])"
        }
      }
    }
  }
}

GET /test/_analyze?analyzer=camel&text=MooseX::FTPClass2_beta
# "moose","x","ftp","class","2","beta"
```

[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/analysis-pattern-analyzer/3.json)

这面那个正且正则表达式可以理解为：

```bash
  ([^\p{L}\d]+)                 # swallow non letters and numbers,
| (?<=\D)(?=\d)                 # or non-number followed by number,
| (?<=\d)(?=\D)                 # or number followed by non-number,
| (?<=[ \p{L} && [^\p{Lu}]])    # or lower case
  (?=\p{Lu})                    #   followed by upper case,
| (?<=\p{Lu})                   # or upper case
  (?=\p{Lu}                     #   followed by upper case
    [\p{L}&&[^\p{Lu}]]          #   then lower case
  )
```  

***


An analyzer of type `pattern` that can flexibly separate text into terms via a regular expression. Accepts the following settings:

The following are settings that can be set for a `pattern` analyzer type:

| Settings | Description |
| -------- | ----------- |
| `lowercase` | Should terms be lowercased or not. Defaults to `true`. |
| `pattern` | The regular expression pattern, defaults to `\W+`. |
| `flags` | The regular expression flags. |
| `stopwords` | A list of stopwords to initialize the stop filter with. Defaults to an *empty* stopword list Check [Stop Analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-stop-analyzer.html) for more details. |

**IMPORTANT**: The regular expression should match the **token separators**, not the tokens themselves.

Flags should be pipe-separated, eg `"CASE_INSENSITIVE|COMMENTS"`. Check [Java Pattern API](http://download.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) for more details about `flags` options.

### Pattern Analyzer Examples

In order to try out these examples, you should delete the test index before running each example.

### Whitespace tokenizer

```bash
DELETE test

PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "whitespace": {
          "type": "pattern",
          "pattern": "\\s+"
        }
      }
    }
  }
}

GET /test/_analyze?analyzer=whitespace&text=foo,bar baz

# "foo,bar", "baz"
```
[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/analysis-pattern-analyzer/1.json) 

### Non-word character tokenizer

```bash
DELETE test

PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "nonword": {
          "type": "pattern",
          "pattern": "[^\\w]+" 
        }
      }
    }
  }
}

GET /test/_analyze?analyzer=nonword&text=foo,bar baz
# "foo,bar baz" becomes "foo", "bar", "baz"

GET /test/_analyze?analyzer=nonword&text=type_1-type_4
# "type_1","type_4"
```

[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/analysis-pattern-analyzer/2.json)

### CamelCase tokenizer

```bash
DELETE test

PUT /test?pretty=1
{
  "settings": {
    "analysis": {
      "analyzer": {
        "camel": {
          "type": "pattern",
          "pattern": "([^\\p{L}\\d]+)|(?<=\\D)(?=\\d)|(?<=\\d)(?=\\D)|(?<=[\\p{L}&&[^\\p{Lu}]])(?=\\p{Lu})|(?<=\\p{Lu})(?=\\p{Lu}[\\p{L}&&[^\\p{Lu}]])"
        }
      }
    }
  }
}

GET /test/_analyze?analyzer=camel&text=MooseX::FTPClass2_beta
# "moose","x","ftp","class","2","beta"
```

[VIEW IN SENSE](http://localhost:5601/app/sense/?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/analysis-pattern-analyzer/3.json)

The regex above is easier to understand as:

```bash
  ([^\p{L}\d]+)                 # swallow non letters and numbers,
| (?<=\D)(?=\d)                 # or non-number followed by number,
| (?<=\d)(?=\D)                 # or number followed by non-number,
| (?<=[ \p{L} && [^\p{Lu}]])    # or lower case
  (?=\p{Lu})                    #   followed by upper case,
| (?<=\p{Lu})                   # or upper case
  (?=\p{Lu}                     #   followed by upper case
    [\p{L}&&[^\p{Lu}]]          #   then lower case
  )
```  
