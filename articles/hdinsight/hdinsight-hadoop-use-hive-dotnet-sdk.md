<properties
    pageTitle="Exécuter des requêtes de ruche à l’aide du Kit de développement .NET HDInsight | Microsoft Azure"
    description="Découvrez comment soumettre des travaux d’Hadoop pour Azure Hadoop de HDInsight à l’aide du Kit de développement .NET HDInsight."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
   ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Exécuter des requêtes de ruche à l’aide du Kit de développement .NET HDInsight

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Découvrez comment soumettre des requêtes de ruche à l’aide du Kit de développement .NET HDInsight.

> [AZURE.NOTE] Les étapes de cet article doivent être effectuées à partir d’un client Windows. Pour plus d’informations sur l’utilisation d’un Linux, OS X ou client Unix pour travailler avec la ruche, utilisez le sélecteur de tabulations indiqué en haut de l’article.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Cluster A Hadoop dans HDInsight**. Reportez-vous à la section [mise en route de basé sur Linux d’Hadoop dans HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012/2013/2015**.

##<a name="submit-hive-queries-using-hdinsight-net-sdk"></a>Soumettre des requêtes de ruche à l’aide du Kit de développement .NET HDInsight

Le Kit de développement .NET HDInsight fournit des bibliothèques de client .NET, ce qui facilite la collaboration avec les clusters HDInsight à partir de .NET. 

**Soumettre des travaux**

1. Créer une application de console C# dans Visual Studio.
2. À partir de la Console du Gestionnaire de package Nuget, exécutez la commande suivante.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

2. Utilisez le code suivant :

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitHiveJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
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

                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
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
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. Appuyez sur **F5** pour exécuter l’application.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris les différentes façons de créer un cluster de HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Mise en route de HDInsight d’Azure][hdinsight-get-started]
* [Créer des clusters d’Hadoop dans HDInsight][hdinsight-provision]
* [Gérer les clusters de Hadoop dans HDInsight en utilisant le portail Azure](hdinsight-administer-use-management-portal.md)
* [Référence du Kit de développement .NET HDInsight](https://msdn.microsoft.com/library/mt271028.aspx)
* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)
* [Utilisez Sqoop avec HDInsight](hdinsight-use-sqoop-mac-linux.md)
* [Créer des applications de HDInsight de .NET de l’authentification non interactive](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md


