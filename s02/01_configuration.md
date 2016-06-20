# 2.1 Configuration

## 环境变量

在 Elasticsearch 的脚本里，有内置的`JAVA_OPTS`参数，会在 JVM 启动的时候传给 JVM。其中最重要的设置项是`-Xmx`，控制着允许程序持有的最大内存量以及`-Xms`，控制了程序所持有的最小内存量（*通常情况下，内存分配得越多越好*）。

绝大多数情况下，`JAVA_OPTS`采用默认值就可以了，你可以使用`ES_JAVA_OPTS`环境变量来设置/改变 JVM 的设置或者参数。

`ES_HEAP_SIZE`环境变量可以设置分配给 elasticsearch java 程序的堆内存。你也可以通过两个独立的环境变量来分开设置最大最小值（但并不推荐这么做），`ES_MIN_MEM`用来设置最小值（默认是`256m`），`ES_MAX_MEM`可以用来设置最大值（默认是`1g`）。

推荐的做法是把最大最小值设置成相同大小，并打开 [mlockall](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration.html#setup-configuration-memory) 特性。

## 系统配置

### 文件描述符

不清楚文件描述符（file descriptors）是什么的，可以自行 wiki ([文件描述符 - 维基百科](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E6%8F%8F%E8%BF%B0%E7%AC%A6))

机器在运行过程中，被打开的文件描述符会渐渐变多，要保证这个增量不会超限（也为了用户在运行 elasticsearch 过程中不出异常）推荐的做法是把它设置成 32k 或者甚至是 64k。

如果想要测试程序究竟能打开多少个文件的话，那就在启动时，把`-Des.max-open-files`设置成`true`就可以了。这样程序在启动的时候就会把打开的文件数打印出来。

另外你也可以使用 *[Nodes Info](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-nodes-info.html)* API 来获取每个节点的 `max_file_descriptors` 值：

```bash
curl localhost:9200/_nodes/stats/process?pretty
```

### 虚拟内存

默认配置下，elasticsearch 使用一个 [混合了 mmapfs/noifs](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 的目录在存储索引。操作系统默认的 mmap 上限值一般都很低，这可能会导致内存溢出。在 Linux 平台上，你可以以`root`权限来运行下面这段命令来增大上限值：

```bash
sysctl -w vm.max_map_count=262144
```

如果要持久化这个配置，那你可以更新 `/etc/sysctl.conf` 这个配置文件中的 `vm.max_map_count` 值。

> **注意**
>
> 如果你是使用安装包来安装（.deb, .rpm）Elasticsrearch 的，那这个值会被自动优化。你可以运行`sysctl vm.max_map_count`命令来确认。

### 内存配置

多数的操作系统就会尽可能地给文件系统缓存分配内存并积极地交换出未被使用的应用程序内存，这可能会导致 elasticsearch 会交换。这会对 elasticsearch 的性能以及节点的稳定性造成极其恶劣的影响，所以应该不惜一切代价地避免它。

有三种可选方案：

* **禁用交换**
  
  最简单粗暴的方法就是直接禁用掉交换。通常情况下，一个沙盒只专门用来运行 Elasticsearch，而它对内存的使用则是通过`ES_HEAP_SIZE`环境变量来控制的。所以也就应该不需要打开交换功能了。
  
  在 Linux 系统中，你可以运行 `sudo swapoff -a` 来临时性地关掉交换。如果要持久化这一配置则需要编辑 `/etc/fstab` 文件并注释掉所有包含 `swap` 单词的行。
  
  在 Windows 系统中，则可以通过禁用页文件来达到相同的目的，具体操作方式 `System Properties → Advanced → Performance → Advanced → Virtual memory`,即 控制面板 → 高级系统设置 → 高级 → 性能 设置 → 高级 → 虚拟内存 更改。
  
* **配置 `swappiness`**
  
  第二种做法是把 sysctl 值的 `vm.swappiness` 设置为 0 。这会减少操作系统内核执行交换的积极性从而导致正常情况下一般不会进行交换，但依然允许整个系统在紧急情况下进行交换。
  
> **注意**
>
> 从 3.5-rc1 内核版本开始，把 swappiness 值设置成 0 会导致 OOM killer 杀死允许交换的进程。你需要把 swappiness 设置成 1 来允许紧急情况下的交换。

* **mlockall**

  第三种方式是使用 Linux/Unix 系统的 [mlockall](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 或者 Windows 系统的 [VirtualLock](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366895%28v=vs.85%29.aspx) 来试着将程序的地址空间锁在 RAM 中以防止任何 Elasticsearch 内存被换出。具体操作方式是在 `config/elasticsearch.yml` 文件中加上下面这一行代码：

  ```bash
  bootstrap.mlockall: true
  ```

  在启动完 Elasticsearch 后，你可以运行下面这段命令，然后看`mlockall`的值来确认这个设置是否已奏效：

  ```bash
  curl http://localhost:9200/_nodes/process?pretty
  ```
  
  如果你看到 `mlockall` 的值是 `false`的话，这就意味着 `mlockall` 请求执行失败了。最普遍的原因是，在 Linux/Unix 操作系统中，运行 Elasticsearch 的用户并没有锁内存的权限。在启动 Elasticsearch 之前，先使用 `root` 权限运行 `ulimit -l unlimited` 命令就可以授予这个权限。
  
  另一种可能的原因是临时文件夹（通常是 `/tmp`）被设置了 `noexec` 选项。这个原因可以通过指定一个新的临时文件夹来解决：
  
  ```bash
  ./bin/elasticsearch -Djna.tmpdir=/path/to/new/dir
  ```
  
  > **警告**
  > 
  > mlockall 可能会导致 JVM 或者 shell 会话在试图分配超出可用内存大小的内存时异常直接退出。
  
## Elasticsearch 配置

**elasticsearch** 的配置文件在 `ES_HOME/config` 文件夹下。这个文件夹里有两个文件，`elasticsearch.yml`，用来配置 Elasticsrearch 的各个 [模块](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)，`logging.yml`，用来配置 Elasticsearch 的日志。

配置信息的书写形式是 [YAML](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#)。下面是个简单的小示例，这改变了所有基于网络的模块用来绑定和发布的地址：

```bash
network :
    host : 10.0.0.4
```

### 路径

在生产环境中使用的时候，你肯定会想要改变数据以及日志文件的存储路径：

```bash
path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
```

### 集群名

同样的，不要忘记给你的集群设置集群名，这会被用来发现和自动关联其他节点：

```bash
cluster:
  name: <NAME OF YOUR CLUSTER>
```

确保你不会在不同的环境下重用你的集群名，否则你可能会发现你的节点最后竟加入到了错误的集群中。你可以使用 `logging-dev`, `logging-stage` 和 `logging-prod` 来命名开发环境，测试环境和生产环境下的集群。

### 节点名

或许，你也会想要以其他一些东西来命名你的节点，比如主机名，而不采用默认的节点名。默认情况下，Elasticsearch 节点在启动时会随机从 3000 多名漫威角色的名字中挑选一个出来命名节点。

```bash
node:
  name: <NAME OF YOUR NODE>
```

主机名可以通过 `HOSTNAME` 变量来指定。如果你单台物理设备上只运行一个 elasticsearch 节点，那你可以使用 `${...}` 语法来将主机名设置成节点名：

```bash
node:
  name: ${HOSTNAME}
```

### 配置格式

在 elasticsearch 程序内部，各个设置项都被映射在相应的`命名空间`里。比如上面那个节点的配置信息就会被映射在程序内部中一个叫 `node.name` 的设置项中。这个机制使得 elasticsearch 能够很方便地支持其他格式的配置方式。比如，用 JSON 格式的信息来配置 Elasticsearch，只需要简单地把  `elasticsearch.yml` 文件重命名成 `elasticsearch.json` 然后再加上下面这段配置信息就可以了：

```bash
{
    "network" : {
        "host" : "10.0.0.4"
    }
}
```

这个机制同样也方便我们使用 `ES_JAVA_OPTS` 环境变量或者通过传参给  `elasticsearch` 命令来在外部对 elasticsearh 进行配置，就像这样：

```bash
$ elasticsearch -Des.network.host=10.0.0.4
```

除了像上面这样在 `-D` 后加上 `es.` 开头的配置信息，你也可以使用 `es.default.` 而不使用 `es.`。这个区别在于 `es.default.` 开头的配置信息只有当相应的配置在配置文件中没有明确设置的时候，才会生效，如果配置文件中有相应的配置，就会优先采用配置文件中的内容。

你也可以在配置文件中使用`${...}`符号来引用环境变量：

```bash
{
    "network" : {
        "host" : "${ES_NET_HOST}"
    }
}
```

另外，假使你不希望把你的配置内容存储在配置文件中，你也可以使用 `${prompt.text}` 或者 `${prompt.secret}` 并把 Elasticsearch 运行在前台。`${prompt.secret}` 已经禁用了 echo，所以它的值是不会在终端中被显示出来的；`${prompt.text}`则允许你看到你键入的值，就像这样：

```bash
node:
  name: ${prompt.text}
```

当你像上面这样使用 `${prompt.text}` 来执行 `elasticsearch` 命令后，你将会看到类似如下的提示信息，要求你输入实际的节点名：

```bash
Enter value for [node.name]:
```

> **注意**
> 
> 当你使用`${prompt.text}` 或 `${prompt.secret}`时，Elasticsearch 此时就无法再作为后台服务来运行。

## 索引配置

在集群中创建索引时，也可以同时指定它们的配置信息。比如下面这个示例就展示了在创建索引时手动指定它的刷新间隔为 5 秒，而不使用默认值（参数格式也可以是 YAML 或 JSON）:

```bash
$ curl -XPUT http://localhost:9200/kimchy/ -d \
'
index:
    refresh_interval: 5s
'
```

索引级的配置信息也可以应用在节点级别上，比如可以在 `elasticsearch.yml` 文件中进行类似下面这样的配置：

```bash
index :
    refresh_interval: 5s
```

这段配置信息的意思是这个指定节点的上的所有索引都会按照上面这个配置来启动，即都会 5 秒刷新一次，除非索引**被额外显式地设置**了其他的刷新间隔时间。换句话说，任何索引级别的配置操作都可以覆盖节点级别的配置。当然上面这个配置项同样也可以被映射成一个`es.`开头的配置项：

```bash
$ elasticsearch -Des.index.refresh_interval=5s
```

所有的索引级配置项都可以在 [index module](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html) 被查阅到。

## 日志配置

Elasticsearch 使用一个基于 [log4j](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 并且是开箱即用的，内部日志提取和输出系统。它试图采用 [YAML](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 格式来进行配置从而简化 log4j 的配置，配置文件是 `config/logging.yml`。它同样也支持 [JSON](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 和 [properties](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 格式。
多个配置文件可以一起被加载，它们各自的配置信息会被合并，前提是这个配置文件的文件名以`logging.`开头并以任意一种被支持的后缀名结束（不管是`.yml`亦或是`.yaml`，`.json` 或 `.properties`）。日志部分包含 java 报名以及它们对应的日志级别，可能 `org.elasticsearch` 前缀会被省略。appender 部分包含日志的输出地。如果自定义地进行拓展以及更多关于被支持的 appender 都可以在 [log4j documentation](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 中查阅得到。

其他的 appender 和日志级别在 [log4j-extras](https://www.gitbook.com/book/scsundefined/elasticsearch-reference-cn/edit#) 中都有，都是拆箱即用的。

### Deprecation logging

In addition to regular logging, Elasticsearch allows you to enable logging of deprecated actions. For example this allows you to determine early, if you need to migrate certain functionality in the future. By default, deprecation logging is disabled. You can enable it in the `config/logging.yml` file by setting the deprecation log level to `DEBUG`.

```bash
deprecation: DEBUG, deprecation_log_file
```

This will create a daily rolling deprecation log file in your log directory. Check this file regularly, especially when you intend to upgrade to a new major version.

***

## Environment Variables

Within the scripts, Elasticsearch comes with built in `JAVA_OPTS` passed to the JVM started. The most important setting for that is the `-Xmx` to control the maximum allowed memory for the process, and -Xms to control the minimum allocated memory for the process (*in general, the more memory allocated to the process, the better*).

Most times it is better to leave the default `JAVA_OPTS` as they are, and use the `ES_JAVA_OPTS` environment variable in order to set / change JVM settings or arguments.

The `ES_HEAP_SIZE` environment variable allows to set the heap memory that will be allocated to elasticsearch java process. It will allocate the same value to both min and max values, though those can be set explicitly (not recommended) by setting `ES_MIN_MEM` (defaults to `256m`), and `ES_MAX_MEM` (defaults to `1g`).

It is recommended to set the min and max memory to the same value, and enable [mlockall](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration.html#setup-configuration-memory).

## System Configuration

### File Descriptors

Make sure to increase the number of open files descriptors on the machine (or for the user running elasticsearch). Setting it to 32k or even 64k is recommended.

In order to test how many open files the process can open, start it with `-Des.max-open-files` set to `true`. This will print the number of open files the process can open on startup.

Alternatively, you can retrieve the `max_file_descriptors` for each node using the *[Nodes Info](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-nodes-info.html)* API, with:

```bash
curl localhost:9200/_nodes/stats/process?pretty
```

### Virtual memory

Elasticsearch uses a [hybrid mmapfs / niofs](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-store.html#default_fs) directory by default to store its indices. The default operating system limits on mmap counts is likely to be too low, which may result in out of memory exceptions. On Linux, you can increase the limits by running the following command as `root`:

```bash
sysctl -w vm.max_map_count=262144
```

To set this value permanently, update the `vm.max_map_count` setting in `/etc/sysctl.conf`.

>**Note**
>
>If you installed Elasticsearch using a package (.deb, .rpm) this setting will be changed automatically. To verify, run `sysctl vm.max_map_count`.

### Memory Settings

Most operating systems try to use as much memory as possible for file system caches and eagerly swap out unused application memory, possibly resulting in the elasticsearch process being swapped. Swapping is very bad for performance and for node stability, so it should be avoided at all costs.

There are three options:

* **Disable swap**
  
  The simplest option is to completely disable swap. Usually Elasticsearch is the only service running on a box, and its memory usage is controlled by the `ES_HEAP_SIZE` environment variable. There should be no need to have swap enabled.

  On Linux systems, you can disable swap temporarily by running: `sudo swapoff -a`. To disable it permanently, you will need to edit the `/etc/fstab` file and comment out any lines that contain the word `swap`.

  On Windows, the equivalent can be achieved by disabling the paging file entirely via `System Properties → Advanced → Performance → Advanced → Virtual memory`.

* **Configure `swappiness`**

  The second option is to ensure that the sysctl value `vm.swappiness` is set to `0`. This reduces the kernel’s tendency to swap and should not lead to swapping under normal circumstances, while still allowing the whole system to swap in emergency conditions.

> **Note**
> 
> From kernel version 3.5-rc1 and above, a swappiness of 0 will cause the OOM killer to kill the process instead of allowing swapping. You will need to set swappiness to 1 to still allow swapping in emergencies.

* **mlockall**

  The third option is to use [mlockall](http://opengroup.org/onlinepubs/007908799/xsh/mlockall.html) on Linux/Unix systems, or [VirtualLock](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366895%28v=vs.85%29.aspx) on Windows, to try to lock the process address space into RAM, preventing any Elasticsearch memory from being swapped out. This can be done, by adding this line to the `config/elasticsearch.yml` file:

  ```bash
  bootstrap.mlockall: true
  ```

  After starting Elasticsearch, you can see whether this setting was applied successfully by checking the value of `mlockall` in the output from this request:

  ```bash
  curl http://localhost:9200/_nodes/process?pretty
  ```
  
  If you see that `mlockall` is `false`, then it means that the `mlockall` request has failed. The most probable reason, on Linux/Unix systems, is that the user running Elasticsearch doesn’t have permission to lock memory. This can be granted by running `ulimit -l unlimited` as `root` before starting Elasticsearch.

  Another possible reason why `mlockall` can fail is that the temporary directory (usually `/tmp`) is mounted with the `noexec` option. This can be solved by specifying a new temp directory, by starting Elasticsearch with:

  ```bash
  ./bin/elasticsearch -Djna.tmpdir=/path/to/new/dir
  ```
  
  > **Warning**
  > 
  > mlockall might cause the JVM or shell session to exit if it tries to allocate more memory than is available!

## Elasticsearch Settings

**elasticsearch** configuration files can be found under `ES_HOME/config` folder. The folder comes with two files, the elasticsearch.yml for configuring Elasticsearch different [modules](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules.html), and `logging.yml` for configuring the Elasticsearch logging.

The configuration format is [YAML](http://www.yaml.org/). Here is an example of changing the address all network based modules will use to bind and publish to:

```bash
network :
    host : 10.0.0.4
```

### Paths

In production use, you will almost certainly want to change paths for data and log files:

```bash
path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
```

### Cluster name

Also, don’t forget to give your production cluster a name, which is used to discover and auto-join other nodes:

```bash
cluster:
  name: <NAME OF YOUR CLUSTER>
```

Make sure that you don’t reuse the same cluster names in different environments, otherwise you might end up with nodes joining the wrong cluster. For instance you could use `logging-dev`, `logging-stage`, and `logging-prod` for the development, staging, and production clusters.

### Node name

You may also want to change the default node name for each node to something like the display hostname. By default Elasticsearch will randomly pick a Marvel character name from a list of around 3000 names when your node starts up.

```bash
node:
  name: <NAME OF YOUR NODE>
```

The hostname of the machine is provided in the environment variable `HOSTNAME`. If on your machine you only run a single elasticsearch node for that cluster, you can set the node name to the hostname using the `${...}` notation:

```bash
node:
  name: ${HOSTNAME}
```

Internally, all settings are collapsed into "namespaced" settings. For example, the above gets collapsed into `node.name`. This means that its easy to support other configuration formats, for example, [JSON](http://www.json.org/). If JSON is a preferred configuration format, simply rename the `elasticsearch.yml` file to `elasticsearch.json` and add:

### Configuration styles

```bash
{
    "network" : {
        "host" : "10.0.0.4"
    }
}
```

It also means that its easy to provide the settings externally either using the `ES_JAVA_OPTS` or as parameters to the `elasticsearch` command, for example:

```bash
$ elasticsearch -Des.network.host=10.0.0.4
```

Another option is to set `es.default`. prefix instead of `es.` prefix, which means the default setting will be used only if not explicitly set in the configuration file.

Another option is to use the `${...}` notation within the configuration file which will resolve to an environment setting, for example:

```bash
{
    "network" : {
        "host" : "${ES_NET_HOST}"
    }
}
```

Additionally, for settings that you do not wish to store in the configuration file, you can use the value `${prompt.text}` or `${prompt.secret}` and start Elasticsearch in the foreground. `${prompt.secret}` has echoing disabled so that the value entered will not be shown in your terminal; `${prompt.text}` will allow you to see the value as you type it in. For example:

```bash
node:
  name: ${prompt.text}
```

On execution of the `elasticsearch` command, you will be prompted to enter the actual value like so:

```bash
Enter value for [node.name]:
```

> **Note**
> 
> Elasticsearch will not start if `${prompt.text}` or `${prompt.secret}` is used in the settings and the process is run as a service or in the background.

## Index Settings

Indices created within the cluster can provide their own settings. For example, the following creates an index with a refresh interval of 5 seconds instead of the default refresh interval (the format can be either YAML or JSON):

```bash
$ curl -XPUT http://localhost:9200/kimchy/ -d \
'
index:
    refresh_interval: 5s
'
```

Index level settings can be set on the node level as well, for example, within the `elasticsearch.yml` file, the following can be set:

```bash
index :
    refresh_interval: 5s
```

This means that every index that gets created on the specific node started with the mentioned configuration will use a refresh interval of 5 seconds `unless the index explicitly sets it`. In other words, any index level settings override what is set in the node configuration. Of course, the above can also be set as a "collapsed" setting, for example:

```bash
$ elasticsearch -Des.index.refresh_interval=5s
```

All of the index level configuration can be found within each [index module](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html).

## Logging

Elasticsearch uses an internal logging abstraction and comes, out of the box, with [log4j](http://logging.apache.org/log4j/1.2/). It tries to simplify log4j configuration by using [YAML](http://www.yaml.org/) to configure it, and the logging configuration file is `config/logging.yml`. The [JSON](http://en.wikipedia.org/wiki/JSON) and [properties](http://en.wikipedia.org/wiki/.properties) formats are also supported. Multiple configuration files can be loaded, in which case they will get merged, as long as they start with the `logging.` prefix and end with one of the supported suffixes (either `.yml`, `.yaml`, `.json` or `.properties`). The logger section contains the java packages and their corresponding log level, where it is possible to omit the `org.elasticsearch` prefix. The appender section contains the destinations for the logs. Extensive information on how to customize logging and all the supported appenders can be found on the [log4j documentation](http://logging.apache.org/log4j/1.2/manual.html).

Additional Appenders and other logging classes provided by [log4j-extras](http://logging.apache.org/log4j/extras/) are also available, out of the box.

### Deprecation logging

In addition to regular logging, Elasticsearch allows you to enable logging of deprecated actions. For example this allows you to determine early, if you need to migrate certain functionality in the future. By default, deprecation logging is disabled. You can enable it in the `config/logging.yml` file by setting the deprecation log level to `DEBUG`.

```bash
deprecation: DEBUG, deprecation_log_file
```

This will create a daily rolling deprecation log file in your log directory. Check this file regularly, especially when you intend to upgrade to a new major version.
