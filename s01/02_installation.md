# Installation

Elasticsearch 要求 Java 版本至少得是 7.特别是在撰写本文的时候，哥哥推荐你使用 Oracle 的 1.8.0_73 JDK 版本（很多炫酷新特性啊有木有）。Java 的安装步骤因为平台不同而各不相同，所以我们这边就不哔哔了，反正 windows 一键安装很便捷。。。Orcale 的安装教程在它的官网 [Oracle’s website](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html) 上也有。最后再补充一句，在你安装 Elasticsearch 之前，请首先运行下面这段命令来确认你的 Java 版本（以后如果还有安装或者升级的时候，如果有需要，也应该运行这段代码来确认一遍 Java 版本）：

```bash
java -version
echo $JAVA_HOME
```

Java 装好之后就可以开始下载和运行 Elsticsearch 惹。二进制包在 [www.elastic.co/downloads](http://www.elastic.co/downloads) 上都可以下得到，这上面有所有以前的发布版本。每个发布版本你都可以选择下载 zip 压缩格式还是 tar 亚索格式，或者一个 DEB 安装包或者 RPM 女装包。在这里我们以 tar 格式的发布包为例来做演示：

首先让我们输入下面这段命令来下载 Elasticsearch 2.3.3 的 tar 格式的包（ Windows 用户请自行下载 zip 格式，谢谢）：

```bash
curl -L -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.3/elasticsearch-2.3.3.tar.gz
```

解压命令（Windows 用户直接右键解压缩就行了。。。）：

```bash
tar -xvf elasticsearch-2.3.3.tar.gz
```

压缩包被解开后有许多文件和文件夹。我们接下来先把目录切换到 bin 目录下：

```bash
cd elasticsearch-2.3.3/bin
```

现在我们就可以运行我们的节点了，这同时也是一个单节点的集群（Windows 用户双击 elasticsearch.bat 文件）：

```bash
./elasticsearch
```

没啥问题的话，你的控制台应该就是像下面这样显示：

```bash
./elasticsearch
[2014-03-13 13:42:17,218][INFO ][node           ] [New Goblin] version[2.3.3], pid[2085], build[5c03844/2014-02-25T15:52:53Z]
[2014-03-13 13:42:17,219][INFO ][node           ] [New Goblin] initializing ...
[2014-03-13 13:42:17,223][INFO ][plugins        ] [New Goblin] loaded [], sites []
[2014-03-13 13:42:19,831][INFO ][node           ] [New Goblin] initialized
[2014-03-13 13:42:19,832][INFO ][node           ] [New Goblin] starting ...
[2014-03-13 13:42:19,958][INFO ][transport      ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9300]}, publish_address {inet[/192.168.8.112:9300]}
[2014-03-13 13:42:23,030][INFO ][cluster.service] [New Goblin] new_master [New Goblin][rWMtGj3dQouz2r6ZFL9v4g][mwubuntu1][inet[/192.168.8.112:9300]], reason: zen-disco-join (elected_as_master)
[2014-03-13 13:42:23,100][INFO ][discovery      ] [New Goblin] elasticsearch/rWMtGj3dQouz2r6ZFL9v4g
[2014-03-13 13:42:23,125][INFO ][http           ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9200]}, publish_address {inet[/192.168.8.112:9200]}
[2014-03-13 13:42:23,629][INFO ][gateway        ] [New Goblin] recovered [1] indices into cluster_state
[2014-03-13 13:42:23,630][INFO ][node           ] [New Goblin] started
```

不需要太深究上面那一长串内容的细节，我们可以看到我们的节点被命名成“绿魔二世”（在你实际运行的时候可能是另一个漫威角色名，绿魔二世就是电影蜘蛛侠里的那个富二代，被蜘蛛侠NTR的那个...）并且已经启动成功了还把自己推举成了一个集群中的主节点（忒不要脸...）。先别担心自己不明白主节点是嘛意思，后面会慢慢告诉你。现在所需要知道的是我们已经开启了一个节点，并且它自己跟自己组成了一个集群。

就像前面所提到的那样，我们可以重新定义节点名和集群名。这可以通过在启动 Elasticsearch 的时候在命令行里这么写来实现：

```bash
./elasticsearch --cluster.name my_cluster_name --node.name my_node_name
```

另外要注意的是以“http”标记的那样显示了节点的 HTTP 地址(192.168.8.112)和端口号(9200)，默认情况下 elasticsearch 以 9200 端口来开发它的 REST API，必要的话，这个端口也可以通过配置改掉。

***

Elasticsearch requires at least Java 7. Specifically as of this writing, it is recommended that you use the Oracle JDK version 1.8.0_73. Java installation varies from platform to platform so we won’t go into those details here. Oracle’s recommended installation documentation can be found on [Oracle’s website](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html). Suffice to say, before you install Elasticsearch, please check your Java version first by running (and then install/upgrade accordingly if needed):

```bash
java -version
echo $JAVA_HOME
```

Once we have Java set up, we can then download and run Elasticsearch. The binaries are available from [www.elastic.co/downloads](http://www.elastic.co/downloads) along with all the releases that have been made in the past. For each release, you have a choice among a `zip` or `tar` archive, or a `DEB` or `RPM` package. For simplicity, let’s use the tar file.

Let’s download the Elasticsearch 2.3.3 tar as follows (Windows users should download the zip package):

```bash
curl -L -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.3/elasticsearch-2.3.3.tar.gz
```

Then extract it as follows (Windows users should unzip the zip package):

```bash
tar -xvf elasticsearch-2.3.3.tar.gz
```

It will then create a bunch of files and folders in your current directory. We then go into the bin directory as follows:

```bash
cd elasticsearch-2.3.3/bin
```

And now we are ready to start our node and single cluster (Windows users should run the elasticsearch.bat file):

```bash
./elasticsearch
```

If everything goes well, you should see a bunch of messages that look like below:

```bash
./elasticsearch
[2014-03-13 13:42:17,218][INFO ][node           ] [New Goblin] version[2.3.3], pid[2085], build[5c03844/2014-02-25T15:52:53Z]
[2014-03-13 13:42:17,219][INFO ][node           ] [New Goblin] initializing ...
[2014-03-13 13:42:17,223][INFO ][plugins        ] [New Goblin] loaded [], sites []
[2014-03-13 13:42:19,831][INFO ][node           ] [New Goblin] initialized
[2014-03-13 13:42:19,832][INFO ][node           ] [New Goblin] starting ...
[2014-03-13 13:42:19,958][INFO ][transport      ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9300]}, publish_address {inet[/192.168.8.112:9300]}
[2014-03-13 13:42:23,030][INFO ][cluster.service] [New Goblin] new_master [New Goblin][rWMtGj3dQouz2r6ZFL9v4g][mwubuntu1][inet[/192.168.8.112:9300]], reason: zen-disco-join (elected_as_master)
[2014-03-13 13:42:23,100][INFO ][discovery      ] [New Goblin] elasticsearch/rWMtGj3dQouz2r6ZFL9v4g
[2014-03-13 13:42:23,125][INFO ][http           ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9200]}, publish_address {inet[/192.168.8.112:9200]}
[2014-03-13 13:42:23,629][INFO ][gateway        ] [New Goblin] recovered [1] indices into cluster_state
[2014-03-13 13:42:23,630][INFO ][node           ] [New Goblin] started
```

Without going too much into detail, we can see that our node named "New Goblin" (which will be a different Marvel character in your case) has started and elected itself as a master in a single cluster. Don’t worry yet at the moment what master means. The main thing that is important here is that we have started one node within one cluster.

As mentioned previously, we can override either the cluster or node name. This can be done from the command line when starting Elasticsearch as follows:

```bash
./elasticsearch --cluster.name my_cluster_name --node.name my_node_name
```

Also note the line marked http with information about the HTTP address (`192.168.8.112`) and port (`9200`) that our node is reachable from. By default, Elasticsearch uses port `9200` to provide access to its REST API. This port is configurable if necessary.





