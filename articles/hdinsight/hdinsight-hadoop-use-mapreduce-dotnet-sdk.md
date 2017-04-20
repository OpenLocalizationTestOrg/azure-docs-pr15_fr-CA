<properties
    pageTitle="Soumettre des travaux MapReduce à l’aide du Kit de développement .NET HDInsight | Microsoft Azure"
    description="Découvrez comment soumettre des travaux MapReduce à Azure Hadoop de HDInsight à l’aide du Kit de développement .NET HDInsight."
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
   ms.date="10/28/2016"
    ms.author="jgao"/>

# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Exécution de tâches MapReduce à l’aide du Kit de développement .NET HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Découvrez comment soumettre des travaux MapReduce à l’aide du Kit de développement .NET HDInsight. HDInsight clusters sont fournis avec un fichier jar avec certains exemples MapReduce. Le fichier jar est */example/jars/hadoop-mapreduce-examples.jar*.  Un des échantillons est *wordcount*. Vous développez une application de console C# pour soumettre un travail wordcount.  La tâche lit le fichier */example/data/gutenberg/davinci.txt* et renvoie les résultats vers */example/data/davinciwordcount*.  Si vous souhaitez exécuter de nouveau l’application, vous devez nettoyer le dossier de sortie.

> [AZURE.NOTE] Les étapes de cet article doivent être effectuées à partir d’un client Windows. Pour plus d’informations sur l’utilisation d’un Linux, OS X ou client Unix pour travailler avec la ruche, utilisez le sélecteur de tabulations indiqué en haut de l’article.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Cluster A Hadoop dans HDInsight**. Reportez-vous à la section [mise en route de basé sur Linux d’Hadoop dans HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012/2013/2015**.

##<a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Soumettre des travaux MapReduce à l’aide du Kit de développement .NET HDInsight

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

                    SubmitMRJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                    var paras = new MapReduceJobSubmissionParameters
                    {
                        //Content = "wordcount  ",
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the MR job to the cluster...");
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

- Pour créer un cluster et soumettre un travail de ruche, consultez [mise en route de HDInsight d’Azure](hdinsight-hadoop-linux-tutorial-get-started.md).
- Pour la création de clusters de HDInsight, consultez [clusters basés sur Linux de créer les Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
- Pour la gestion des clusters de HDInsight, consultez [Hadoop de gérer des clusters dans HDInsight](hdinsight-administer-use-management-portal.md).
- Pour le Kit de développement .NET HDInsight d’apprentissage, consultez [référence du Kit de développement .NET HDInsight](https://msdn.microsoft.com/library/mt271028.aspx).
- Pour non interactif s’authentifier auprès d’Azure, consultez [Création d’applications .NET HDInsight de l’authentification non interactive](hdinsight-create-non-interactive-authentication-dotnet-applications.md).




