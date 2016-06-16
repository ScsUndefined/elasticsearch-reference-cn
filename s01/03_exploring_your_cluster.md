# Exploring Your Cluster

**REST API**

现在我们已经有了一个自己的 elasticsearch 节点（同时也是一个集群），接下来的步骤就是要知道该怎么和它进行交互。幸运的是，elasticsearch 提供了一个功能非常全面且强大的 REST API 以便用户与其进行交互。简单举几个这个 API 可以做的是亲哪个：

* 检查你的集群，节点和索引是否健康，状态怎样以及一些其他统计信息
* 管理你的集群，节点和索引数据以及元数据
* 对你的索引进行增删改查操作
* 执行高阶查询操作，比如分页查询，排序，过滤，带脚本搜索，聚合搜索等等

***

**The REST API**

Now that we have our node (and cluster) up and running, the next step is to understand how to communicate with it. Fortunately, Elasticsearch provides a very comprehensive and powerful REST API that you can use to interact with your cluster. Among the few things that can be done with the API are as follows:

* Check your cluster, node, and index health, status, and statistics
* Administer your cluster, node, and index data and metadata
* Perform CRUD (Create, Read, Update, and Delete) and search operations against your indexes
* Execute advanced search operations such as paging, sorting, filtering, scripting, aggregations, and many others

