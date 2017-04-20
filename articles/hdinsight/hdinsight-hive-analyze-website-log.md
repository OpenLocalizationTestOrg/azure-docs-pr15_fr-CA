<properties 
    pageTitle="Utilisez la ruche avec Hadoop pour l’analyse des journaux site Web | Microsoft Azure" 
    description="Apprenez à utiliser la ruche avec HDInsight pour analyser les journaux du site Web. Vous allez utiliser un fichier journal en tant qu’entrée dans une table HDInsight et utiliser HiveQL pour interroger les données." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>Utilisez la ruche avec HDInsight pour analyser les journaux à partir de sites Web

Apprenez à utiliser HiveQL avec HDInsight pour analyser les journaux à partir d’un site Web. Analyse de journal de site Web peut être utilisée pour segmenter votre audience en fonction d’activités similaires, classer les visiteurs par secteur démographique et pour découvrir les contenus qu’ils vue, proviennent de sites Web et ainsi de suite.

Dans cet exemple, vous allez utiliser un cluster de HDInsight pour analyser les fichiers journaux de site Web pour obtenir un aperçu de la fréquence des visites sur le site Web à partir des sites Web externes dans un jour. Vous allez également générer un résumé des erreurs de site Web auxquels les utilisateurs confrontés. Vous allez apprendre comment :

- Se connecter à un stockage Azure Blob qui contient les fichiers journaux de site Web.
- Créer des tables de ruche pour interroger ces journaux.
- Créer des requêtes de ruche pour analyser les données.
- Utilisez Microsoft Excel pour se connecter à HDInsight (en utilisant la connectivité de base de données ouverte (ODBC) pour récupérer les données analysées.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>Conditions préalables

- Un cluster Hadoop sur Azure HDInsight vous devez avoir configuré. Pour obtenir des instructions, reportez-vous à la section [Disposition HDInsight Clusters][hdinsight-provision]. 
- Vous devez disposer de Microsoft Excel 2013 ou Excel 2010 est installé.
- Vous devez disposer de [Microsoft le pilote ODBC de la ruche](http://www.microsoft.com/download/details.aspx?id=40886) pour importer des données à partir de la ruche dans Excel.


##<a name="to-run-the-sample"></a>Pour exécuter l’exemple

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la Startboard (si vous épinglé le cluster), cliquez sur la mosaïque du cluster sur lequel vous souhaitez exécuter l’exemple.

2. À partir de la blade de cluster, sous les **Liens rapides**, cliquez sur **Tableau de bord de Cluster**, puis cliquez sur **Tableau de bord de Cluster HDInsight**du **Tableau de bord de Cluster** serveur lame. Vous pouvez également ouvrir directement le tableau de bord à l’aide de l’URL suivante :

        https://<clustername>.azurehdinsight.net
    
    Lorsque vous y êtes invité, s’authentifier en utilisant le nom d’utilisateur administrateur et le mot de passe utilisé lors de la configuration du cluster.
  
2. À partir de la page web qui s’ouvre, cliquez sur l’onglet **Mise en route présentation** et puis dans la catégorie des **Solutions avec des exemples de données** , cliquez sur l’échantillon **d’Analyse de journal de site Web** .

3. Suivez les instructions fournies sur la page web à la fin de l’exemple.

##<a name="next-steps"></a>Étapes suivantes
Essayez l’exemple suivant : [analyse des données de capteur à l’aide de la ruche avec HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 
