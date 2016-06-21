# 13.3.19 Synonym Token Filter 同义词标记过滤器

同义词标记过滤器使得你在解析过程中处理同义词时将会变得非常轻松。同义词可以通过一个像下面这样的文件来配置：

```bash
{
    "index" : {
        "analysis" : {
            "analyzer" : {
                "synonym" : {
                    "tokenizer" : "whitespace",
                    "filter" : ["synonym"]
                }
            },
            "filter" : {
                "synonym" : {
                    "type" : "synonym",
                    "synonyms_path" : "analysis/synonym.txt"
                }
            }
        }
    }
}
```

这段代码中告诉 elasticsearch 使用 `analysis/synonym.txt` 文件（相对于`config`文件夹）来配置一个叫做`synonym`的过滤器。这个`synonym`解析器又涵盖了`synonym`过滤器。其他的设置还有 `ignore_case` (默认是 `false`), 以及 `expand` (默认为 `true`)。

`tokenizer`参数定义了用对同义词进行分词操作时使用的分词器。默认是`whitespace`分词器，即空白符分词器。

elasticsearch目前支持有两种同义词格式： Solr, WordNet。

### Solr 格式

Solr 格式的同义词配置文件示例：

```bash
# blank lines and lines starting with pound are comments.

#Explicit mappings match any token sequence on the LHS of "=>"
#and replace with all alternatives on the RHS.  These types of mappings
#ignore the expand parameter in the schema.
#Examples:
i-pod, i pod => ipod,
sea biscuit, sea biscit => seabiscuit

#Equivalent synonyms may be separated with commas and give
#no explicit mapping.  In this case the mapping behavior will
#be taken from the expand parameter in the schema.  This allows
#the same synonym file to be used in different synonym handling strategies.
#Examples:
ipod, i-pod, i pod
foozball , foosball
universe , cosmos

# If expand==true, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod, i-pod, i pod
# If expand==false, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod

#multiple synonym mapping entries are merged.
foo => foo bar
foo => baz
#is equivalent to
foo => foo bar, baz
```

你也可以在配置文件中直接对过滤器定义同义词规则（记得把`synonyms_path`改为`synonyms`）:

```bash
{
    "filter" : {
        "synonym" : {
            "type" : "synonym",
            "synonyms" : [
                "i-pod, i pod => ipod",
                "universe, cosmos"
            ]
        }
    }
}
```

但还是推荐把大量的同义词定在一个文件中，然后使用`synonyms_path`去引用。

### WordNet 格式

基于 [WordNet](http://wordnet.princeton.edu/) 格式的同义词定义文件格式如下：

```bash
{
    "filter" : {
        "synonym" : {
            "type" : "synonym",
            "format" : "wordnet",
            "synonyms" : [
                "s(100000001,1,'abstain',v,1,0).",
                "s(100000001,2,'refrain',v,1,0).",
                "s(100000001,3,'desist',v,1,0)."
            ]
        }
    }
}
```

同样，推荐把同义词规则写在一个文件中，然后使用`synonyms_path`来引用。

***

The `synonym` token filter allows to easily handle synonyms during the analysis process. Synonyms are configured using a configuration file. Here is an example:

```bash
{
    "index" : {
        "analysis" : {
            "analyzer" : {
                "synonym" : {
                    "tokenizer" : "whitespace",
                    "filter" : ["synonym"]
                }
            },
            "filter" : {
                "synonym" : {
                    "type" : "synonym",
                    "synonyms_path" : "analysis/synonym.txt"
                }
            }
        }
    }
}
```

The above configures a `synonym` filter, with a path of `analysis/synonym.txt` (relative to the `config` location). The `synonym` analyzer is then configured with the filter. Additional settings are: `ignore_case` (defaults to `false`), and `expand` (defaults to `true`).

The `tokenizer` parameter controls the tokenizers that will be used to tokenize the synonym, and defaults to the `whitespace` tokenizer.

Two synonym formats are supported: Solr, WordNet.

### Solr synonyms

The following is a sample format of the file:

```bash
# blank lines and lines starting with pound are comments.

#Explicit mappings match any token sequence on the LHS of "=>"
#and replace with all alternatives on the RHS.  These types of mappings
#ignore the expand parameter in the schema.
#Examples:
i-pod, i pod => ipod,
sea biscuit, sea biscit => seabiscuit

#Equivalent synonyms may be separated with commas and give
#no explicit mapping.  In this case the mapping behavior will
#be taken from the expand parameter in the schema.  This allows
#the same synonym file to be used in different synonym handling strategies.
#Examples:
ipod, i-pod, i pod
foozball , foosball
universe , cosmos

# If expand==true, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod, i-pod, i pod
# If expand==false, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod

#multiple synonym mapping entries are merged.
foo => foo bar
foo => baz
#is equivalent to
foo => foo bar, baz
```

You can also define synonyms for the filter directly in the configuration file (note use of `synonyms` instead of `synonyms_path`):

```bash
{
    "filter" : {
        "synonym" : {
            "type" : "synonym",
            "synonyms" : [
                "i-pod, i pod => ipod",
                "universe, cosmos"
            ]
        }
    }
}
```

However, it is recommended to define large synonyms set in a file using `synonyms_path`.

### WordNet synonyms

Synonyms based on [WordNet](http://wordnet.princeton.edu/) format can be declared using format:

```bash
{
    "filter" : {
        "synonym" : {
            "type" : "synonym",
            "format" : "wordnet",
            "synonyms" : [
                "s(100000001,1,'abstain',v,1,0).",
                "s(100000001,2,'refrain',v,1,0).",
                "s(100000001,3,'desist',v,1,0)."
            ]
        }
    }
}
```

Using `synonyms_path` to define WordNet synonyms in a file is supported as well.