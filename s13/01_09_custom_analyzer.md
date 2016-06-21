# 13.1.9 Custom Analyzer 自定义解析器

你可以自行选择一种`分词器`以及一个或多个`标记过滤器`和一个或多个`字符过滤器`组合为一个自定义的解析器。你可以自行选择 合理的/被注册过的 名字来明来你的分词器和标记过滤器。但要注意的是`自定义的`分析器名字不可以用“\_”开头。

自定义的配置项如下：

| 配置项 | 描述 |
| ------- | ----------- |
| `tokenizer` | 合理的/登记过的 分词器名 |
| `filter` | 合理的/登记过的 标记过滤器的名称列表，这一项是可选的 |
| `char_filter` | 合理的/登记过的 字符过滤器的名称列表，这一项是可选的 |
| `position_increment_gap` | ~~没懂...~~ An optional number of positions to increment between each value of a field using this analyzer. Defaults to 100. 100 was chosen because it prevents phrase queries with reasonably large slops (less than 100) from matching terms across field values. |

下面给出一个示例：

```bash
index :
    analysis :
        analyzer :
            myAnalyzer2 :
                type : custom
                tokenizer : myTokenizer1
                filter : [myTokenFilter1, myTokenFilter2]
                char_filter : [my_html]
                position_increment_gap: 256
        tokenizer :
            myTokenizer1 :
                type : standard
                max_token_length : 900
        filter :
            myTokenFilter1 :
                type : stop
                stopwords : [stop1, stop2, stop3, stop4]
            myTokenFilter2 :
                type : length
                min : 0
                max : 2000
        char_filter :
              my_html :
                type : html_strip
                escaped_tags : [xxx, yyy]
                read_ahead : 1024
```            

***

An analyzer of type `custom` that allows to combine a `Tokenizer` with zero or more `Token Filters`, and zero or more `Char Filters`. The custom analyzer accepts a logical/registered name of the tokenizer to use, and a list of logical/registered names of token filters. The name of the `custom` analyzer must not start with "_".

The following are settings that can be set for a custom analyzer type:

| Setting | Description |
| ------- | ----------- |
| tokenizer | The logical / registered name of the tokenizer to use.|
| filter | An optional list of logical / registered name of token filters. |
| char_filter | An optional list of logical / registered name of char filters. |
| position_increment_gap | An optional number of positions to increment between each value of a field using this analyzer. Defaults to 100. 100 was chosen because it prevents phrase queries with reasonably large slops (less than 100) from matching terms across field values. |

Here is an example:

```bash
index :
    analysis :
        analyzer :
            myAnalyzer2 :
                type : custom
                tokenizer : myTokenizer1
                filter : [myTokenFilter1, myTokenFilter2]
                char_filter : [my_html]
                position_increment_gap: 256
        tokenizer :
            myTokenizer1 :
                type : standard
                max_token_length : 900
        filter :
            myTokenFilter1 :
                type : stop
                stopwords : [stop1, stop2, stop3, stop4]
            myTokenFilter2 :
                type : length
                min : 0
                max : 2000
        char_filter :
              my_html :
                type : html_strip
                escaped_tags : [xxx, yyy]
                read_ahead : 1024
```            