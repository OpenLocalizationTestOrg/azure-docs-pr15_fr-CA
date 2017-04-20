<properties
    pageTitle="Utiliser Hadoop Sqoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser le Kit de développement .NET HDInsight d’exécuter Sqoop d’importation et d’exportation entre un cluster Hadoop et une base de données Azure SQL."
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

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Exécuter des tâches de Sqoop à l’aide du Kit de développement .NET pour Hadoop dans HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser le Kit de développement .NET HDInsight à l’exécution des tâches de Sqoop dans HDInsight pour importer et exporter entre cluster de HDInsight et de la base de données Azure SQL ou de la base de données SQL Server.

> [AZURE.NOTE] Les étapes décrites dans cet article peuvent être utilisés avec un cluster un HDInsight basée sur Linux ou Windows ; Toutefois, ces étapes fonctionnent uniquement à partir d’un client Windows. Utilisez le sélecteur de tabulations en haut de cet article pour choisir d’autres méthodes.

###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Cluster A Hadoop dans HDInsight**. Reportez-vous à la section [créer un cluster et la base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Exécutez Sqoop à l’aide du Kit de développement .NET

Le Kit de développement .NET HDInsight fournit des bibliothèques de client .NET, ce qui facilite la collaboration avec les clusters HDInsight à partir de .NET. Dans cette section, vous allez créer une application de console C# pour exporter la hivesampletable à la table de base de données SQL créé dans cette didacticiels.

**Pour soumettre un travail Sqoop**

1. Créer une application de console C# dans Visual Studio.
2. À partir de la Console de Gestionnaire de package de Visual Studio, exécutez la commande suivante de Nuget pour importer le package.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Utilisez le code suivant dans le fichier Program.cs :

        using System.Collections.Generic;
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
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
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
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
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
        
4. Appuyez sur **F5** pour exécuter le programme. 

##<a name="limitations"></a>Limitations

* Exportation en bloc - des HDInsight de base avec Linux, le connecteur de Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données de SQL Azure ne prend pas en charge insertions en bloc.

* Le traitement par lots - avec HDInsight basé sur Linux, lorsque vous utilisez la `-batch` passer lors de l’exécution des insertions, Sqoop effectue plusieurs insertions plutôt que le traitement par lot des opérations d’insertion.

##<a name="next-steps"></a>Étapes suivantes

Maintenant, vous avez appris comment utiliser Sqoop. Pour plus d’informations, voir :

- [Utilisation des Oozie avec HDInsight](hdinsight-use-oozie.md): action de Sqoop d’utilisation dans un flux de travail Oozie.
- [Analyse des données de retard vol à l’aide de HDInsight](hdinsight-analyze-flight-delay-data.md): utilisez la ruche pour analyser le vol retarder les données et utilisez Sqoop pour exporter des données vers une base de données SQL d’Azure.
- [Télécharger des données à HDInsight](hdinsight-upload-data.md): rechercher d’autres méthodes de chargement des données de stockage des objets Blob HDInsight/Azure.


