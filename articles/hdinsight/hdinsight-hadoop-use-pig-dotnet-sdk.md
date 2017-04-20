<properties
   pageTitle="Utiliser Hadoop porc avec .NET dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser le Kit de développement .NET pour Hadoop à soumettre des tâches de porc d’Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Exécuter des travaux de porc à l’aide du Kit de développement .NET pour Hadoop dans HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple de l’utilisation du Kit de développement .NET pour Hadoop à soumettre des tâches de porc à un Hadoop sur cluster de HDInsight.

Le Kit de développement .NET HDInsight fournit les bibliothèques clientes .NET qui facilite la collaboration avec les clusters HDInsight à partir de .NET. Porc vous permet de créer les opérations MapReduce par une série de transformations de données de modélisation. Vous allez apprendre à utiliser une application de base de C# pour soumettre un travail de porc à un cluster d’HDInsight.

## <a name="prerequisites"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants.

* Un cluster Azure HDInsight (Hadoop sur HDInsight) (Windows ou Linux).
* Visual Studio 2012 ou 2013 ou 2015.

## <a name="create-the-application"></a>Création de l’application

Le Kit de développement .NET HDInsight fournit des bibliothèques de client .NET, ce qui facilite la collaboration avec les clusters HDInsight à partir de .NET. 


1. Ouvrez Visual Studio 2012 ou 2013
2. Dans le menu **fichier** , sélectionnez **Nouveau** , puis **projet**.
3. Pour le nouveau projet, tapez ou sélectionnez les valeurs suivantes.

    <table>
    <tr>
    <th>Propriété</th>
    <th>Valeur</th>
    </tr>
    <tr>
    <th>Catégorie</th>
    <th>Modèles/Visual C# / Windows</th>
    </tr>
    <tr>
    <th>Modèle</th>
    <th>Application console</th>
    </tr>
    <tr>
    <th>Nom</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. Cliquez sur **OK** pour créer le projet.
5. Dans le menu **Outils** , sélectionnez **Gestionnaire de package de bibliothèque** ou **De Gestionnaire de package Nuget**, puis sélectionnez **Console du Gestionnaire de package**.
6. Exécutez la commande suivante dans la console pour installer les packages du Kit de développement .NET.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. À partir de l’Explorateur de solutions, double-cliquez sur **Program.cs** pour l’ouvrir. Remplacez le code existant par celui-ci.

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

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
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
            }
        }


7. Appuyez sur **F5** pour démarrer l’application.
8. Appuyez sur **entrée** pour quitter l’application.

## <a name="summary"></a>Résumé

Comme vous pouvez le voir, le Kit de développement .NET pour Hadoop vous permet de créer des applications .NET qui envoient des travaux de porc à un cluster d’HDInsight et de surveiller l’état du travail.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations générales sur les porcs dans HDInsight.

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight.

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [MapReduce utilisation avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md) [Aperçu-portail] : https://portal.azure.com/
