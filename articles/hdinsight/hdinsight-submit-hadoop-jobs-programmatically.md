---
title: 在 HDInsight 中提交 Hadoop 作业 | Azure
description: 了解如何将 Hadoop 作业提交到 Azure HDInsight Hadoop。
editor: cgronlun
manager: paulettm
services: hdinsight
documentationCenter: 
tags: azure-portal
authors: mumian

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
wacn.date: 01/05/2017
ms.author: jgao
---

# 在 HDInsight 中提交 Hadoop 作业

学习如何使用 Azure PowerShell 提交 MapReduce 和 Hive 作业，以及如何使用 HDInsight .NET SDK 提交 MapReduce、Hadoop 流式处理和 Hive 作业。

> [!NOTE]
> 这篇文章的步骤必须在 Windows 客户端使用。关于在 HDInsight 把 Linux, OS X 或者 Unix 客户端与 MapReduce, Hive 或者 Pig 一起使用，请查看以下文章，并选择 “Curl” 链接。
><p> - [在 HDInsight 使用 Hive](./hdinsight-use-hive.md)
><p> - [在 HDInsight 使用 Pig](./hdinsight-use-pig.md)
><p> - [在 HDInsight 使用 MapReduce](./hdinsight-use-mapreduce.md)

##先决条件

在开始阅读本文前，你必须具有：

* **一个 Azure HDInsight 群集**。有关说明，请参阅 [HDInsight 入门][hdinsight-get-started]或[在 HDInsight 中创建 Hadoop 群集][hdinsight-provision]。
- **配备 Azure PowerShell 的工作站**。请参阅[安装 Azure PowerShell 1.0 或更新](./hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)。

##使用 PowerShell 提交 MapReduce 作业

请参阅[在基于 Windows 的 HDInsight 中运行 Hadoop MapReduce 示例](./hdinsight-run-samples.md)。

##使用 PowerShell 提交 Hive 作业

请参阅[使用 PowerShell 运行 Hive 查询](./hdinsight-hadoop-use-hive-powershell.md)

## 使用 Visual Studio 提交 Hive 作业

请参阅[适用于 Visual Studio 的 HDInsight Hadoop 工具入门][hdinsight-visual-studio-tools]。

##使用 PowerShell 提交 Sqoop 作业

请参阅[将 Sqoop 与 HDInsight 配合使用][hdinsight-use-sqoop]。

##使用 HDInsight .NET SDK 提交 Hive/Pig/Sqoop 作业
HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET 中使用 HDInsight 群集的操作。

**提交作业**

1. 在 Visual Studio 中创建 C# 控制台应用程序。
2. 通过 Nuget 包管理器控制台运行以下命令。

        Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. 使用以下代码：

        using System.Collections.Generic;
        using System.Security;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.cn";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("Running");

                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitHiveJob();
                    SubmitPigJob();
                    SubmitSqoopJob();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                            LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                            FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                            GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                            FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                            RESULT = order FREQUENCIES by COUNT desc;
                            DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }

                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };

                    Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    Console.WriteLine("Validating that the response is as expected...");
                    Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    Console.WriteLine("Validating the response object...");
                    Console.WriteLine("JobId is " + jobId);

                    Console.WriteLine("Waiting for the job completion ...");

                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
                }

                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";

                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";

                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.chinacloudapi.cn;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;

                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };

                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

5. 按 **F5** 运行应用程序。

##使用适用于 Visual Studio 的 HDInsight 工具提交作业

你可以使用适用于 Visual Studio 的 HDInsight 工具来运行 Hive 查询和 Pig 脚本。请参阅[适用于 HDInsight 的 Visual Studio Hadoop 工具入门](./hdinsight-hadoop-visual-studio-tools-get-started.md)。

##后续步骤
在本文中，你已经学习了几种创建 HDInsight 群集的方法。若要了解更多信息，请参阅下列文章：

* [Azure HDInsight 入门][hdinsight-get-started]
* [在 HDInsight 中创建 Hadoop 群集][hdinsight-provision]
* [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [HDInsight Cmdlet 参考文档][hdinsight-powershell-reference]
* [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
* [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]

[azure-certificate]: ../cloud-services/cloud-services-certs-create.md
[azure-management-portal]: https://manage.windowsazure.cn/

[hdinsight-visual-studio-tools]: ./hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: ./hdinsight-use-sqoop.md
[hdinsight-provision]: ./hdinsight-provision-clusters-v1.md
[hdinsight-use-mapreduce]: ./hdinsight-use-mapreduce.md
[hdinsight-use-hive]: ./hdinsight-use-hive.md
[hdinsight-use-pig]: ./hdinsight-use-pig.md
[hdinsight-get-started]: ./hdinsight-hadoop-tutorial-get-started-windows-v1.md
[hdinsight-storage]: ./hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: ./hdinsight-administer-use-powershell.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/zh-cn/library/dn858087.aspx

[powershell-install-configure]: https://docs.microsoft.com/powershell/azureps-cmdlets-docs

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Mooncake_Quality_Review_1118_2016-->