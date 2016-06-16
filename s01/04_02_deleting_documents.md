# 1.4.2 Deleting Documents

删除文档是非常简单的。下面这段示例演示了怎么删除 ID 是 2 的文档

```bash
curl -XDELETE 'localhost:9200/customer/external/2?pretty'
```

`delete-by-query` 插件可以批量删除指定条件的多个文档。

***

Deleting a document is fairly straightforward. This example shows how to delete our previous customer with the ID of 2:

```bash
curl -XDELETE 'localhost:9200/customer/external/2?pretty'
```

The `delete-by-query` plugin can delete all documents matching a specific query.