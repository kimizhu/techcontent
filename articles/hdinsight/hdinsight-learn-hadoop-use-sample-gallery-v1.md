---
title: 使用示例库了解 HDInsight 中的 Hadoop | Azure
description: 通过从 HDInsight 入门库运行示例应用程序快速了解 Hadoop。使用示例数据，或提供自己的数据。
services: hdinsight
documentationCenter: 
authors: mumian
manager: paulettm
editor: cgronlun

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2016
wacn.date: 12/16/2016
ms.author: jgao
---

# 使用 HDInsight 入门库了解 Hadoop

借助 HDInsight 入门库，可以通过在 HDInsight 中运行示例应用程序快速轻松地了解 Hadoop。某些示例随附了示例数据。可以为其余示例提供自己的数据。目前，有下面六个示例（还会有更多）：

- 包含 Azure 数据的解决方案
    - Azure Web 应用日志分析
    - Azure 存储分析
- 包含示例数据的解决方案
    - 传感器数据分析
    - Twitter 趋势分析
    - Web 应用日志分析
    - Mahout 电影推荐

![包括示例数据的 HDInsight Hadoop、Storm 和 HBase 入门库解决方案。][hdinsight.sample.gallery]

**从入门库运行示例**

1.	登录 [Azure 经典管理门户][azure.portal]。
2.	单击左侧菜单中的“HDInsight”。你会看到现有 HDInsight 群集的列表，包括 Hadoop、Storm 和 HBase 群集。
3.	单击要在其中运行示例的群集。
4.	单击该页底部的“查询控制台”。
5.	输入群集的 Hadoop 用户名和密码。
6.	单击该页顶部的**入门库**。
7.	单击其中一个示例。每个示例都提供了运行它的详细步骤。下图显示了 Twitter 趋势分析示例：

    ![HDInsight Twitter 趋势分析示例][hdinsight.twitter.sample]

## 后续步骤
了解 HDInsight 的其他途径包括：

- [HDInsight 信息图][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery-v1/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery-v1/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://manage.windowsazure.cn

<!---HONumber=Mooncake_Quality_Review_1202_2016-->