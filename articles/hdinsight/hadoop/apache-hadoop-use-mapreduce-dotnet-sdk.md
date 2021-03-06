---
title: 使用 HDInsight .NET SDK 提交 MapReduce 作业 - Azure
description: 了解如何使用 HDInsight .NET SDK 将 MapReduce 作业提交到 Azure HDInsight Apache Hadoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157043"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK 运行 MapReduce 作业

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

了解如何使用 HDInsight .NET SDK 提交 MapReduce 作业。 HDInsight 群集附带含有一些 MapReduce 示例的 jar 文件。 jar 文件为`/example/jars/hadoop-mapreduce-examples.jar`。  其中一个示例是 wordcount****。 开发 C# 控制台应用程序，提交 wordcount 作业。  作业读取`/example/data/gutenberg/davinci.txt`文件，并将结果输出到`/example/data/davinciwordcount`。  如果想要重新运行该应用程序，必须清理输出文件夹。

> [!NOTE]  
> 必须从 Windows 客户端执行本文中的步骤。 有关使用 Linux、OS X 或 Unix 客户端来使用 Hive 的信息，请使用本文顶部显示的选项卡选择器。

## <a name="prerequisites"></a>先决条件

* HDInsight 中的 Apache Hadoop 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

* [视觉工作室](https://visualstudio.microsoft.com/vs/community/)。

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK 提交 MapReduce 作业

HDInsight .NET SDK 提供 .NET 客户端库，这使得使用 .NET 的 HDInsight 群集变得更加容易。

1. 启动可视化工作室并创建 C# 控制台应用程序。

1. 导航到**工具** > **NuGet 包** > **管理器管理器控制台**并输入以下命令：

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 将下面的代码复制到**Program.cs**。 然后，`existingClusterName`通过设置： 、 `existingClusterPassword` `defaultStorageAccountName`、`defaultStorageAccountKey`和`defaultStorageContainerName`的值来编辑代码。

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);
    
                System.Console.WriteLine("Waiting for the job completion ...");
    
                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }
    
                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. 按**F5**以运行应用程序。

要再次运行作业，必须更改作业输出文件夹名称，在示例中是`/example/data/davinciwordcount`。

作业成功完成后，应用程序将打印输出文件`part-r-00000`的内容。

## <a name="next-steps"></a>后续步骤

在本文中，已经学习了几种创建 HDInsight 群集的方法。 若要了解详细信息，请参阅以下文章：

* 有关如何提交 Hive 作业，请参阅[使用 HDInsight .NET SDK 运行 Apache Hive 查询](apache-hadoop-use-hive-dotnet-sdk.md)。
* 有关如何创建 HDInsight 群集，请参阅[在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)。
* 有关如何管理 HDInsight 群集，请参阅[在 HDInsight 中管理 Apache Hadoop 群集](../hdinsight-administer-use-portal-linux.md)。
* 请参阅 [HDInsight.NET SDK 参考资料](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)，学习 HDInsight.NET SDK。
* 请参阅[创建非交互式身份验证 .NET HDInsight 应用程序](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)，了解对 Azure 的非交互式身份验证。