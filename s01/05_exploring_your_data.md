# Exploring Your Data

**模拟数据**

至此我们已经简单了解了一些基础，现在让我们尝试着在更贴近现实环境中的那种复杂的数据结构上进行操作。我已经准备好了一些模拟数据，模拟的是一个银行账户信息。每个文档结构大概都像下面这样：

```bash
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```

你们可能好奇这些值对应的含义是什么，其实压根就没啥含义。。。这些数据都是我通过 [www.json-generator.com/](http://www.json-generator.com/) 自动生成的，所以请直接无视数据的值。

**加载模拟数据**

你可以点击这个超链接来下载这个模拟数据（[accounts.json](https://github.com/bly2k/files/blob/master/accounts.zip?raw=true)）。把它解压到当前目录中，然后用这个命令把它加载到我们的集群中：

```bash
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'
```

响应信息：

```bash
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
yellow bank    5   1       1000            0    424.4kb        424.4kb
```

这表示我们已经成功地把 1000 条文档索引到了一个名叫“bank”的索引中了（类型为“account”）。

***

**Sample Dataset**

Now that we’ve gotten a glimpse of the basics, let’s try to work on a more realistic dataset. I’ve prepared a sample of fictitious JSON documents of customer bank account information. Each document has the following schema:

```bash
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```

For the curious, I generated this data from [www.json-generator.com/](http://www.json-generator.com/) so please ignore the actual values and semantics of the data as these are all randomly generated.

**Loading the Sample Dataset**

You can download the sample dataset (accounts.json) from [here](https://github.com/bly2k/files/blob/master/accounts.zip?raw=true). Extract it to our current directory and let’s load it into our cluster as follows:

```bash
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'
```

And the response:

```bash
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
yellow bank    5   1       1000            0    424.4kb        424.4kb
```

Which means that we just successfully bulk indexed 1000 documents into the bank index (under the account type).

***

**Sample Dataset**

Now that we’ve gotten a glimpse of the basics, let’s try to work on a more realistic dataset. I’ve prepared a sample of fictitious JSON documents of customer bank account information. Each document has the following schema:

```bash
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```

For the curious, I generated this data from [www.json-generator.com/](http://www.json-generator.com/) so please ignore the actual values and semantics of the data as these are all randomly generated.

**Loading the Sample Dataset**

You can download the sample dataset (accounts.json) from [here](https://github.com/bly2k/files/blob/master/accounts.zip?raw=true). Extract it to our current directory and let’s load it into our cluster as follows:

```bash
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'
```

And the response:

```bash
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
yellow bank    5   1       1000            0    424.4kb        424.4kb
```

Which means that we just successfully bulk indexed 1000 documents into the bank index (under the account type).

***

**Sample Dataset**

Now that we’ve gotten a glimpse of the basics, let’s try to work on a more realistic dataset. I’ve prepared a sample of fictitious JSON documents of customer bank account information. Each document has the following schema:

```bash
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```

For the curious, I generated this data from [www.json-generator.com/](http://www.json-generator.com/) so please ignore the actual values and semantics of the data as these are all randomly generated.

**Loading the Sample Dataset**

You can download the sample dataset (accounts.json) from [here](https://github.com/bly2k/files/blob/master/accounts.zip?raw=true). Extract it to our current directory and let’s load it into our cluster as follows:

```bash
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'
```

And the response:

```bash
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
yellow bank    5   1       1000            0    424.4kb        424.4kb
```

Which means that we just successfully bulk indexed 1000 documents into the bank index (under the account type).