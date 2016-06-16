# Modifying Your Data

Elasticsearch 有着近乎实时的数据操作和搜索数据的能力。默认情况下，当你在索引/更新/删除你的数据后，要经过一秒钟的延迟（刷新间隔）才能在你的搜索结果中显示出影响。这是和其他平台之间的一个重要的区别，比如 SQL，它的事务在执行完毕后，新数据就是立马可用的状态了。

**索引/替换 文档**

我们现在已经看到了，要如何去索引一个文档，现在我们再一次调用下这个命令:

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
```

这条命令又会再一次地把数据存储到 customer 的 external 中去，并且 ID 是 1。如果我们用一个不同的（或者相同的也行）文档来重新运行一遍上面那个命令，那么 Elasticsearch 就会在已有的那个 ID 为 1 的文档上替换上新的文档（也即重新索引）：

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "Jane Doe"
}'
```

上面这段命令把名字从“John Doe”改成了“Jane Doe”。如果我们指明了一个不一样的 ID，那一个新文档就会被索引，而不会动到任何已有的文档：

```bash
curl -XPUT 'localhost:9200/customer/external/2?pretty' -d '
{
  "name": "Jane Doe"
}'
```

上面这段命令会以值为 2 的 ID 来索引一个新文档。

当执行索引文档命令的时候，ID 那部分其实是可选的。如果你没有指明 ID,Elasticsearch 就会随机生成一个 ID 然后用它来索引文档。Elasticsearch 自己生成的 ID 亦或是我们自己指定的 ID 值都会在响应信息中返回过来。

下面是一个使用默认 ID 的代码示例：

```bash
curl -XPOST 'localhost:9200/customer/external?pretty' -d '
{
  "name": "Jane Doe"
}'
```

要注意的是在这个示例中，我们使用了 POST 动作，而非之前一直使用的 PUT。


***

Elasticsearch provides data manipulation and search capabilities in near real time. By default, you can expect a one second delay (refresh interval) from the time you index/update/delete your data until the time that it appears in your search results. This is an important distinction from other platforms like SQL wherein data is immediately available after a transaction is completed.

**Indexing/Replacing Documents**

We’ve previously seen how we can index a single document. Let’s recall that command again:

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
```

Again, the above will index the specified document into the customer index, external type, with the ID of 1. If we then executed the above command again with a different (or same) document, Elasticsearch will replace (i.e. reindex) a new document on top of the existing one with the ID of 1:

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "Jane Doe"
}'
```

The above changes the name of the document with the ID of 1 from "John Doe" to "Jane Doe". If, on the other hand, we use a different ID, a new document will be indexed and the existing document(s) already in the index remains untouched.

```bash
curl -XPUT 'localhost:9200/customer/external/2?pretty' -d '
{
  "name": "Jane Doe"
}'
```

The above indexes a new document with an ID of 2.

When indexing, the ID part is optional. If not specified, Elasticsearch will generate a random ID and then use it to index the document. The actual ID Elasticsearch generates (or whatever we specified explicitly in the previous examples) is returned as part of the index API call.

This example shows how to index a document without an explicit ID:

```bash
curl -XPOST 'localhost:9200/customer/external?pretty' -d '
{
  "name": "Jane Doe"
}'
```

Note that in the above case, we are using the POST verb instead of PUT since we didn’t specify an ID.