---
title: 在 HDinsight 上的 Storm 拓扑中使用 Python 组件 | Azure
description: 了解如何在 Azure HDInsight 上的 Apache Storm 中使用 Python 组件。将学习如何通过基于 Java 和 Clojure 的 Storm 拓扑使用 Python 组件。
services: hdinsight
documentationCenter: 
authors: Blackmist
manager: paulettm
editor: cgronlun

ms.service: hdinsight
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
wacn.date: 12/16/2016
ms.author: larryfr
---

#在 HDInsight 上使用 Python 开发 Apache Storm 拓扑

Apache Storm 支持多种语言，甚至可将多种语言的组件合并成一个拓扑。在本文档中，将学习如何在 HDInsight 上基于 Java 和 Clojure 的 Storm 拓扑中使用 Python 组件。

##先决条件

* Python 2.7 或更高版本

* Java JDK 1.7 或更高版本

* [Leiningen](http://leiningen.org/)

##Storm 多语言支持

Storm 在设计上可与使用任何编程语言编写的组件配合使用，但这些组件必须知道如何使用 [Storm 的 Thrift 定义](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)。在 Python 中，Apache Storm 项目随附了一个可轻松与 Strom 交互的模块。可以在 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) 上找到此模块。

由于 Apache Storm 是在 Java 虚拟机 (JVM) 上运行的 Java 进程，以其他语言编写的组件将以子进程的形式执行。JVM 中运行的 Storm 代码使用通过 stdin/stdout 发送的 JSON 消息与这些子进程通信。有关各组件之间通信的详细信息，请参阅[多语言协议](https://storm.apache.org/documentation/Multilang-protocol.html)文档。

###Storm 模块

Storm 模块 (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) 提供创建可与 Storm 配合使用的 Python 组件所需的代码。

例如，`storm.emit` 可以发出 Tuple，`storm.logInfo` 可以写入日志。建议通读本文件，以了解该模块所提供的功能。

##挑战

借助 __storm.py__ 模块可以创建 Python spout 使用数据和创建 Bolt 处理数据，但负责在组件之间建立通信的整个 Storm 拓扑定义仍需使用 Java 或 Clojure 编写。此外，如果使用 Java，还必须创建充当 Python 组件接口的 Java 组件。

此外，由于 Storm 群集以分布方式运行，因此必须确保 Python 组件所需的任何模块可供群集中的所有辅助节点使用。对于多语言资源，Storm 无法轻松实现此目的 - 必须将所有依赖项包含在拓扑的 jar 文件中，或者在群集的每个辅助节点上手动安装依赖项。

###Java 与Clojure 拓扑定义

两种定义拓扑的方法，Clojure 是目前为止最简单直接的方法，因为可以在拓朴定义中直接引用 python 组件。对于基于 Java 的拓朴定义，还必须定义 Java 组件用于处理一些操作，例如在 Python 组件返回的 Tuple 中声明字段。

本文介绍这两种方法，并附上示例项目。

##使用 Java 拓扑的 Python 组件

> [!NOTE]此示例位于 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) 上的 __JavaTopology__ 目录中。这是一个基于 Maven 的项目。如果不熟悉 Maven，请参阅[在 HDInsight 上使用 Apache Storm 开发基于 Java 的拓扑](./hdinsight-storm-develop-java-topology.md)，了解有关如何为 Storm 拓扑创建 Maven 项目的详细信息。

使用 Python（或其他 JVM 语言组件）的基于 Java 的拓朴乍看之下是使用 Java 组件，但如果仔细查看每个 Java Spout/Bolt，将看到类似于以下代码：

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Java 在此处调用 Python，并运行包含实际 Blot 逻辑的脚本。Java Spout/Bolt（对于本示例）只是在基础 Python 组件要发出的 Tuple 中声明字段。

在此示例中，实际 Python 文件存储在 `/multilang/resources` 目录中。`/multilang` 目录在 __pom.xml__ 中引用：

    <resources>
        <resource>
            <!-- Where the Python bits are kept -->
            <directory>${basedir}/multilang</directory>
        </resource>
    </resources>

这会将 `/multilang` 文件夹中的所有文件包含在基于此项目构建的 jar 中。

> [!IMPORTANT]请注意，这只会指定 `/multilang` 目录，而不是 `/multilang/resources`。Storm 预期非 JVM 资源都位于 `resources` 目录中，因此已在内部查找过该目录。将组件放入此文件夹可以在 Java 代码中直接按名称引用。例如，`super("python", "countbolt.py");`。另一种思路是 Storm 在访问多语言资源时会将 `resources` 目录视为根目录 (/)。
><p>
><p> 针对本示例项目，`storm.py` 模块位于 `/multilang/resources` 目录中。

###构建并运行项目

要在本地运行此项目，只需使用以下 Maven 命令构建并以本地模式运行此项目：

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

使用 Ctrl+C 可终止进程。

要将项目部署到运行 Apache Storm 的 HDInsight 群集，请使用以下步骤：

1. 构建 uber jar：

        mvn package

    这会在此项目的 `/target` 目录中创建名为 __WordCount--1.0-SNAPSHOT.jar__ 的文件。

2. 使用以下方法之一将 jar 文件上载到 Hadoop 群集：

    * 对于__基于 Windows__ 的 HDInsight 群集：在浏览器中转到 HTTPS://CLUSTERNAME.azurehdinsight.cn/，以连接到 Storm 仪表板。将 CLUSTERNAME 替换为 HDInsight 群集名称，并在出现提示时输入管理员名称和密码。

        使用窗体执行以下操作：

        * __Jar 文件__：选择“浏览”，然后选择 __WordCount-1.0-SNAPSHOT.jar__ 文件
        * __类名__：输入 `com.microsoft.example.WordCount`
        * __其他参数__：输入一个用于标识拓扑的友好名称，例如 `wordcount`

        最后，选择“提交”以启动拓扑。

> [!NOTE]Storm 拓扑在启动之后将一直运行，直到被停止（终止）。 要停止拓扑，请从命令行使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 选择拓扑，然后选择“终止”按钮。

##使用 Clojure 拓扑的 Python 组件

> [!NOTE]此示例位于 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) 上的 __ClojureTopology__ 目录中。

此拓扑使用 [Leiningen](http://leiningen.org) 创建，用于[创建新 Clojure 项目](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)。之后，对基架项目进行了以下修改：

* __project.clj__：为 Storm 添加的依赖项，以及在部署到 HDInsight 服务器时可能会造成问题的排除项。
* __resources/resources__：Leiningen 创建默认 `resources` 目录，但此处存储的文件似乎已添加到基于此项目创建的 jar 文件所在的根目录，而 Storm 预期这些文件位于名为 `resources` 的子目录中。因此添加了一个子目录，而 Python 文件就存储在 `resources/resources` 中。在运行时，此目录被视为访问 Python 组件时的根目录 (/)。
* __src/wordcount/core.clj__：此文件包含拓扑定义，并通过 __project.clj__ 文件引用。有关使用 Clojure 定义 Storm 拓扑的详细信息，请参阅 [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)。

###构建并运行项目

__要在本地构建并运行项目__，请使用以下命令：

    lein clean, run

要停止拓扑，请使用 __Ctrl+C__。

__要构建 Uberjar 并部署到 HDInsight__，请使用以下步骤：

1. 创建包含拓扑和所需依赖项的 uberjar：

        lein uberjar

    这会在 `target\uberjar+uberjar` 目录中创建一个名为 `wordcount-1.0-SNAPSHOT.jar` 的新文件。
    
2. 使用以下方法之一将拓扑部署到 HDInsight 群集并运行该拓扑：
    
    * __基于 Windows 的 HDInsight__
    
        1. 在浏览器中转到 HTTPS://CLUSTERNAME.azurehdinsight.cn/，以连接到 Storm 仪表板。将 CLUSTERNAME 替换为 HDInsight 群集名称，并在出现提示时输入管理员名称和密码。

        2. 使用窗体执行以下操作：

            * __Jar 文件__：选择“浏览”，然后选择 __wordcount-1.0-SNAPSHOT.jar__ 文件
            * __类名__：输入 `wordcount.core`
            * __其他参数__：输入一个用于标识拓扑的友好名称，例如 `wordcount`

            最后，选择“提交”以启动拓扑。

> [!NOTE]Storm 拓扑在启动之后将一直运行，直到被停止（终止）。 要停止拓扑，请从命令行使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 选择拓扑，然后选择“终止”按钮。

##后续步骤

在本文档中，已学习如何通过 Storm 拓扑使用 Python 组件。请参阅以下文档，了解将 Python 与 HDInsight 配合使用的其他方式：

* [如何在 Pig 和 Hive 中使用 Python 用户定义的函数 (UDF)](./hdinsight-python.md)

<!---HONumber=Mooncake_Quality_Review_1202_2016-->