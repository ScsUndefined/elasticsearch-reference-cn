# Getting Started

Elasticsearch 是一个高可用的、可拓展的并且开源的全文检索和分析引擎。它能够让你近乎实时地存储、检索和分析大批量的数据。它通常被用来搭建应用程序的底层引擎/技术以便赋予应用程序复杂的搜索功能。

下面列举一些 Elasticsearch 的使用场景：

* 你有一个电商网站，需要提供搜索功能给用户以便他们检索你的商品。对于这种情况，你就可以使用 Elasticsearch 来存储你所有的商品清单和库存信息然后提供给用户搜索以及自动补全的功能。

* 如果你想要收集日志或者流量，并且想要分析和数据挖掘的操作，以便看出数据的趋势、统计学信息、概要信息或者异常情况等信息。在这种场景下，你就可以使用 Logstash（Elasticsearch/Logstash/Kibana 堆的一部分）来收集、聚合以及解析你的数据，然后让 Logstash 把处理好的数据存储到 Elasticsearch 中。一旦数据被存储在 Elasticsearch 中，你就可以使用检索和聚合功能来挖掘任何你感兴趣的数据。

* 如果你正在运营一个特价商品订阅系统，即一个允许那些关注价格的消费者指定一个订阅规则来订阅商品的变价信息。比如：“我想买个iPhone 9s，如果它下个月的售价跌破多少多少软妹币的时候，请务必要通知我！不管是哪家店跌的！”。在这种情况下，你就可以检索销售商的售价，并把这些售价信息推送到 Elasticsearch 中，然后使用它的反向搜索（Percolator）功能来匹配用户指定的价格变动信息，最后，一旦匹配成功那就可以推送消息给用户。

* 如果你有分析商务情报的需求，即需要快速地从大量数据（这里的大量数据指的是数据量达到上百万甚至数十亿的数据）中进行调查，分析，生成可视化报表以及解决一些其他自定义的数据处理需求。在这种场景下，你可以使用 Elasticsearch 来存储你的数据，然后在 Kibana （Elasticsearch/Logstash/Kibana 堆中的一部分）中构建自定义的仪表盘来可视化地展现你海量数据中对你有价值的方方面面。另外，你也可以使用 Elasticsearch 的聚合功能来对你的数据进行操作，执行复杂的业务信息查询操作。

接下来，本教程会通过指导你安装和运行 Elasticsearch 让你对它有个基础的认识，然后会指引你执行一些基本的操作，比如构建索引，执行搜索操作以及修正你的数据等等。在本教程的结尾，你应该对 Elasticsearch 是什么，怎么工作的，有一个深刻的理解。如果你够机智，那你或许都可以使用它来构建高级别的搜索应用程序或者进行数据挖掘。

***

Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time. It is generally used as the underlying engine/technology that powers applications that have complex search features and requirements.

Here are a few sample use-cases that Elasticsearch could be used for:

* You run an online web store where you allow your customers to search for products that you sell. In this case, you can use Elasticsearch to store your entire product catalog and inventory and provide search and autocomplete suggestions for them.

* You want to collect log or transaction data and you want to analyze and mine this data to look for trends, statistics, summarizations, or anomalies. In this case, you can use Logstash (part of the Elasticsearch/Logstash/Kibana stack) to collect, aggregate, and parse your data, and then have Logstash feed this data into Elasticsearch. Once the data is in Elasticsearch, you can run searches and aggregations to mine any information that is of interest to you.

* You run a price alerting platform which allows price-savvy customers to specify a rule like "I am interested in buying a specific electronic gadget and I want to be notified if the price of gadget falls below $X from any vendor within the next month". In this case you can scrape vendor prices, push them into Elasticsearch and use its reverse-search (Percolator) capability to match price movements against customer queries and eventually push the alerts out to the customer once matches are found.

* You have analytics/business-intelligence needs and want to quickly investigate, analyze, visualize, and ask ad-hoc questions on a lot of data (think millions or billions of records). In this case, you can use Elasticsearch to store your data and then use Kibana (part of the Elasticsearch/Logstash/Kibana stack) to build custom dashboards that can visualize aspects of your data that are important to you. Additionally, you can use the Elasticsearch aggregations functionality to perform complex business intelligence queries against your data.

For the rest of this tutorial, I will guide you through the process of getting Elasticsearch up and running, taking a peek inside it, and performing basic operations like indexing, searching, and modifying your data. At the end of this tutorial, you should have a good idea of what Elasticsearch is, how it works, and hopefully be inspired to see how you can use it to either build sophisticated search applications or to mine intelligence from your data.
















