<properties
   pageTitle="Utiliser Hadoop porc dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser des porcs avec Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Utilisez des porcs avec Hadoop sur HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache porc](http://pig.apache.org/) est une plate-forme pour la création de programmes d’Hadoop à l’aide d’un langage procédural appelé *Latin de porc*. Porc est une alternative à Java pour créer des solutions *MapReduce* , et il est fourni avec les HDInsight d’Azure.

Dans cet article, vous apprendrez comment vous pouvez utiliser des porcs avec HDInsight.

##<a id="why"></a>Pourquoi utiliser des porcs ?

Un des défis en matière de traitement des données à l’aide de MapReduce dans Hadoop est mise en œuvre de votre logique de traitement en utilisant uniquement un mappage et une fonction de réduction. Pour un traitement complexe, il est souvent nécessaire d’arrêter le traitement dans plusieurs opérations de MapReduce qui sont chaînées pour obtenir le résultat souhaité.

Au lieu de vous obliger à utiliser uniquement le mappage et de réduire les fonctions, porc permet de définir un traitement comme une série de transformations qui les données circulent via pour produire la sortie de votre choix.

La langue latine de porc vous permet de décrire le flux de données à partir de l’entrée brute, par une ou plusieurs transformations, pour produire la sortie de votre choix. Programmes de porc Latin suivent ce modèle général :

- **Charge**: lire des données à manipuler le système de fichiers
- **Transformation**: manipulation des données
- **Vidage ou banque**: sortie des données à l’écran ou à les stocker pour traitement

Latin de porc prend également en charge les fonctions définies par l’utilisateur (UDF), qui vous permet d’appeler des composants externes qui implémentent la logique qui est difficile à modéliser en Latin de porc.

Pour plus d’informations sur Latin de porc, consultez le [manuel de référence de porc Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) et le [manuel de référence de porc Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Pour obtenir un exemple de l’utilisation de fichiers UDF avec porc, consultez les documents suivants :

* [DataFu d’utilisation avec des porcs dans la HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu est une collection de FDU utiles géré par Apache

* [Utiliser les Python avec porc et de la ruche dans HDInsight](hdinsight-python.md)

* [Utiliser C# avec la ruche et porc dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>À propos de l’exemple de données

Cet exemple utilise un exemple de fichier *log4j* , qui est stocké à **/example/data/sample.log** dans votre conteneur de stockage blob. Chaque journal dans le fichier se compose d’une ligne de champs contenant un `[LOG LEVEL]` champ afin d’afficher le type et la gravité, par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l’exemple précédent, le niveau de journalisation est erreur.

> [AZURE.NOTE] Vous pouvez également générer un fichier log4j à l’aide de l’outil d’enregistrement de [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) et puis chargez ce fichier vers le blob. Pour obtenir des instructions, voir [Télécharger les données à HDInsight](hdinsight-upload-data.md) . Pour plus d’informations sur l’utilisation des objets BLOB Azure storage avec HDInsight, reportez-vous à la section [Utiliser le stockage Blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

Les exemples de données sont stockées dans le stockage Azure Blob HDInsight utilise comme système de fichiers par défaut pour les clusters d’Hadoop. HDInsight peut accéder aux fichiers stockés dans les blobs en utilisant le préfixe **wasb** . Par exemple, pour accéder au fichier exemple.log, vous utiliseriez la syntaxe suivante :

    wasbs:///example/data/sample.log

WASB étant le stockage par défaut pour les HDInsight, vous pouvez également accéder le fichier à l’aide de **/example/data/sample.log** de porc Latin.

> [AZURE.NOTE] La syntaxe de la **wasbs : / / /**, est utilisé pour accéder aux fichiers stockés dans le conteneur de stockage par défaut pour votre cluster HDInsight. Si vous avez spécifié des comptes de stockage supplémentaires lorsque vous configuré votre cluster, et que vous souhaitez accéder aux fichiers stockés dans ces comptes, vous pouvez accéder aux données en spécifiant l’adresse compte du stockage et le nom de conteneur, par exemple : **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>À propos de la tâche de l’échantillon

Le travail de porc Latin suivant charge le fichier de **exemple.log** à partir du stockage par défaut pour votre cluster HDInsight. Il effectue ensuite une série de transformations qui entraînent un décompte du nombre de fois où chaque niveau du journal s’est produite dans les données d’entrée. Les résultats sont vidées dans STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

L’image suivante montre une répartition de la signification de chaque transformation aux données.

![Représentation graphique des transformations][image-hdi-pig-data-transformation]

##<a id="run"></a>Exécuter le travail de porc Latin

HDInsight pouvez exécuter les tâches de porc Latin à l’aide de diverses méthodes. Utilisez le tableau suivant pour décider quelle méthode vous consiste, puis cliquez sur le lien pour une procédure pas à pas.

| **Utilisez cette option** si vous souhaitez...                                   | .. .an les shell **interactif** | ... traitement **par lots** | .. .avec ce **système d’exploitation en cluster** | ... contre ce **système d’exploitation client** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Curl](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux ou Windows                          | Linux, Unix, Mac OS X ou Windows        |
| [Kit de développement .NET pour Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows (pour l’instant)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Bureau à distance](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Exécution des travaux de porc sur Azure HDInsight à l’aide du serveur local SQL Server Integration Services

Vous pouvez également utiliser SQL Server Integration Services (SSIS) pour exécuter un travail de porc. Le Pack de fonctionnalité Azure pour SSIS fournit les composants suivants qui fonctionnent avec les travaux de porc sur HDInsight.


- [Tâche d’Azure porc de HDInsight][pigtask]
- [Gestionnaire de connexion d’abonnement Azure][connectionmanager]


En savoir plus sur le Pack de fonctionnalités Azure pour SSIS [ici][ssispack].


##<a id="nextsteps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser des porcs avec HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec HDInsight d’Azure.

* [Téléchargement des données vers HDInsight][hdinsight-upload-data]
* [Utilisez la ruche avec HDInsight][hdinsight-use-hive]
* [Utilisez Sqoop avec HDInsight](hdinsight-use-sqoop.md)
* [Utiliser Oozie avec HDInsight](hdinsight-use-oozie.md)
* [Utiliser des travaux de MapReduce avec HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
