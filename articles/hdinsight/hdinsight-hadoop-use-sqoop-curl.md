---
title: 在 HDInsight 中将 Hadoop Sqoop 与 Curl 配合使用 | Azure
description: 了解如何使用 Curl 向 HDInsight 远程提交 Sqoop 作业。
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/21/2016
wacn.date: 12/26/2016
ms.author: jgao
---

# 使用 Curl 在 HDInsight 中的 Hadoop 上运行 Sqoop 作业
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

学习如何使用 Curl 在 HDInsight 中的 Hadoop 群集上运行 Sqoop 作业。

本文档使用 Curl 演示如何使用原始 HTTP 请求来与 HDInsight 交互，以便运行、监视和检索 Sqoop 作业的结果。要执行这些操作，需要使用 HDInsight 群集提供的 WebHCat REST API（前称 Templeton）。

## 先决条件
要完成本文中的步骤，需要：

* HDInsight 群集上的 Hadoop（基于 Windows）
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## 使用 Curl 提交 Sqoop 作业
> [!NOTE]
使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。此外，还必须使用群集名称作为用来向服务器发送请求的统一资源标识符 (URI) 的一部分。
><p> 
> 对本部分中的所有命令，请将 **USERNAME** 替换为在群集上进行身份验证的用户，并将 **PASSWORD** 替换为用户帐户的密码。将 **CLUSTERNAME** 替换为群集名称。
><p> 
> REST API 通过[基本身份验证](http://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。你始终应该使用安全 HTTP (HTTPS) 来发出请求，以确保安全地将凭据发送到服务器。
> 
> 

1. 在命令行中，使用以下命令验证是否可以连接到 HDInsight 群集。

    ```
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.cn/templeton/v1/status
    ```

    你应会收到类似于下面的响应：

    ```
    {"status":"ok","version":"v1"}
    ```

    此命令中使用的参数如下：

   * **-u** - 用来对请求进行身份验证的用户名和密码。
   * **-G** - 指出这是 GET 请求。

     所有请求的 URL 开头 (**https://CLUSTERNAME.azurehdinsight.cn/templeton/v1**) 都是一样的。路径 **/status** 指示请求将返回服务器的 WebHCat（也称为 Templeton）状态。
2. 使用以下命令 sqoop 作业：

    ```
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.chinacloudapi.cn;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.cn/templeton/v1/sqoop
    ```

    此命令中使用的参数如下：

    * **-d** - 由于未使用 `-G`，请求默认为 POST 方法。`-d` 指定与请求一起发送的数据值。

        * **user.name** - 正在运行命令的用户。

        * **command** - 要执行的 Sqoop 命令。

        * **statusdir** - 此作业的状态要写入到的目录。

    此命令应会返回可用来检查作业状态的作业 ID。

    ```
    {"id":"job_1415651640909_0026"}
    ```

1. 若要检查作业的状态，请使用以下命令。将 **JOBID** 替换为上一步骤返回的值。例如，如果返回值为 `{"id":"job_1415651640909_0026"}`，则 **JOBID** 将是 `job_1415651640909_0026`。

    ```
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.cn/templeton/v1/jobs/JOBID | jq .status.state
    ```

    如果作业已完成，状态将是 **SUCCEEDED**。

   > [!NOTE]
   此 Curl 请求返回具有作业相关信息的 JavaScript 对象表示法 (JSON) 文档；使用 jq 可以仅检索状态值。
   > 
   > 
2. 在作业的状态更改为 **SUCCEEDED** 后，可以从 Azure Blob 存储中检索作业的结果。随查询一起传递的 `statusdir` 参数包含输出文件的位置；在本例中为 **wasbs:///example/curl**。此地址会将作业的输出存储在 HDInsight 群集所用的默认存储容器的 **example/curl** 目录中。

    可以使用 [Azure CLI](../xplat-cli-install.md) 列出并下载这些文件。例如，若要列出 **example/curl** 中的文件，请使用以下命令：

    ```
    azure storage blob list <container-name> example/curl
    ```

    若要下载文件，请使用以下命令：

    ```
    azure storage blob download <container-name> <blob-name> <destination-file>
    ```

   > [!NOTE]
   必须使用 `-a` 和 `-k` 参数指定包含 Blob 的存储帐户名称，或者设置 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS\_KEY** 环境变量。请参阅<a href="./hdinsight-upload-data.md" target="\_blank">在 HDInsight 中上传 Hadoop 作业的数据</a>了解详细信息。
   > 
   > 

## 摘要
如本文档中所示，你可以使用原始 HTTP 请求来运行、监视和查看 HDInsight 群集上的 Sqoop 作业的结果。

有关本文中使用的 REST 接口的详细信息，请参阅 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API guide</a>（Sqoop REST API 指南）。

## 后续步骤
有关将 Hive 与 HDInsight 配合使用的一般信息：

* [将 Sqoop 与 HDInsight 上的 Hadoop 配合使用](./hdinsight-use-sqoop.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Hive 与 HDInsight 上的 Hadoop 配合使用](./hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 上的 Hadoop 配合使用](./hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](./hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/zh-cn/library/dn479185.aspx

[azure-purchase-options]: https://www.azure.cn/pricing/overview/
[azure-member-offers]: https://www.azure.cn/pricing/member-offers/
[azure-trial]: https://www.azure.cn/pricing/1rmb-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://zh.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: ./hdinsight-connect-excel-power-query.md

[hdinsight-use-oozie]: ./hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: ./hdinsight-analyze-flight-delay-data.md

[hdinsight-provision]: ./hdinsight-provision-clusters-v1.md
[hdinsight-submit-jobs]: ./hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ./hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/zh-cn/library/ee692792.aspx

<!---HONumber=Mooncake_Quality_Review_1215_2016-->