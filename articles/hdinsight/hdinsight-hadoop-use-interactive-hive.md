<properties
    pageTitle="Utilisation d’une ruche Interactive dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser Interactive ruche (ruche sur LLAP) dans HDInsight."
    keywords=""
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="use-interactive-hive-in-hdinsight-preview"></a>Utilisation d’une ruche Interactive dans HDInsight (aperçu)

Interactive (également appelé la ruche [Long live et processus]( https://cwiki.apache.org/confluence/display/Hive/LLAP)) est un nouveau [type de cluster]( hdinsight-hadoop-provision-linux-clusters.md#cluster-types)d' HDInsight.  Ruche interactive permet dans la mise en mémoire cache qui effectue des requêtes de ruche beaucoup plus interactives et plus rapide. Cette nouvelle fonctionnalité permet à une HDInsight de la planète les plus performantes, flexible, et ouvrir une solution importante de données sur le nuage en mémoire caches (à l’aide de la ruche et allumage) et advanced analytique grâce à une intégration étroite avec les Services de R. 

Le cluster de la ruche Interactive est différent du cluster Hadoop. Il ne contient que le service de la ruche. 

> [AZURE.NOTE] MapReduce, porc, Sqoop, Oozie et autres services seront de ce type de cluster bientôt supprimées.
Le service de la ruche du cluster Interactive de la ruche n’est accessible par l’intermédiaire de la ruche de Ambari afficher, Beeline et la ruche ODBC. Il ne sont pas accessibles via la console ruche, Templeton, CLI d’Azure et d’Azure PowerShell. 


 


## <a name="create-an-interactive-hive-cluster"></a>Créer un cluster de la ruche Interactive

Interactive ruche de cluster est uniquement pris en charge sur les clusters basés sur Linux. Pour plus d’informations sur la création de clusters de HDInsight, consultez [clusters basés sur Linux de créer les Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="execute-hive-from-interactive-hive"></a>Exécuter la ruche à partir de la ruche Interactive

Il existe différentes options de comment vous pouvez exécuter des requêtes de ruche :

- Exécuter la ruche à l’aide de la vue de la ruche de Ambari

    Pour obtenir les informations sur l’utilisation de la vue de la ruche, voir [utilisation de la vue de la ruche avec Hadoop dans HDInsight]( hdinsight-hadoop-use-hive-ambari-view.md).

- Ruche de l’exécution à l’aide de Beeline

    Pour obtenir les informations sur l’utilisation de Beeline sur HDInsight, voir [Utilisation de la ruche avec Hadoop dans HDInsight avec Beeline](hdinsight-hadoop-use-hive-beeline.md).

    Vous utilisez Beeline à partir de la headnode ou un nœud vide.  Beeline à partir d’un nœud vide est recommandée.  Pour plus d’informations sur la création d’un cluster de HDInsight avec un edgenode vide, consultez [utilisation de nœuds de bord vide dans HDInsight](hdinsight-apps-use-edge-node.md).

- Ruche de l’exécution à l’aide de ODBC de la ruche

    Pour des informations sur l’utilisation de la ruche de ODBC, voir [Excel de se connecter à Hadoop avec le pilote ODBC de Microsoft de la ruche](hdinsight-connect-excel-hive-odbc-driver.md).

**Pour rechercher la chaîne de connexion JDBC :**

1.  Ouvrez une session sur Ambari à l’aide de l’URL suivante : https://<ClusterName>. AzureHDInsight.net.
2.  Dans le menu de gauche, cliquez sur **la ruche** .
3.  Cliquez sur l’icône en surbrillance pour copier l’URL :

    ![HDInsight Hadoop ruche Interactive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Voir aussi
-   [Les clusters basés sur Linux de créer les Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md): Apprenez à créer des Interactive de la ruche de clusters dans HDInsight.
-   [Utilisez la ruche avec Hadoop dans HDInsight avec Beeline](hdinsight-hadoop-use-hive-beeline.md): Apprenez à utiliser Beeline pour envoyer des requêtes de la ruche.
-   [Excel de se connecter à Hadoop avec le pilote ODBC de Microsoft la ruche](hdinsight-connect-excel-hive-odbc-driver.md): Apprenez à Excel de se connecter à la ruche.
