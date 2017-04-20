<properties
    pageTitle="Analyser des données de capteur à l’aide de la ruche et Hadoop | Microsoft Azure"
    description="Découvrez comment analyser les données de capteur avec HDInsight (Hadoop) à l’aide de la Console de requête de la ruche, puis visualiser les données dans Microsoft Excel avec PowerView."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="larryfr"/>

#<a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analyser des données de capteur à l’aide de la Console de la ruche de requête sur Hadoop dans HDInsight

Découvrez comment analyser les données de capteur avec HDInsight (Hadoop) à l’aide de la Console de requête de la ruche, puis visualiser les données dans Microsoft Excel à l’aide de Power View.

> [AZURE.NOTE] Les étapes décrites dans ce document fonctionnent uniquement avec les clusters basés sur Windows de HDInsight.

Dans cet exemple, vous utiliserez ruche pour traiter les données historiques produites par chauffage, de ventilation et de systèmes de climatisation (HVAC) pour identifier les systèmes qui ne sont pas en mesure de manière fiable à maintenir une température de jeu. Vous allez apprendre comment :

- Créer des tables de ruche pour interroger les données stockées dans des fichiers séparés par des virgules (CSV) de valeur.
- Créer des requêtes de ruche pour analyser les données.
- Utilisez Microsoft Excel pour se connecter à HDInsight (à l’aide de la connectivité de base de données ouverte (ODBC) pour récupérer les données analysées.
- Power View permet de visualiser les données.

![Un diagramme de l’architecture de la solution](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##<a name="prerequisites"></a>Conditions préalables

* Un cluster de HDInsight (Hadoop) : voir [clusters de fourniture Hadoop dans HDInsight](hdinsight-provision-clusters.md) pour plus d’informations sur la création d’un cluster.

* Microsoft Excel 2013

    > [AZURE.NOTE] Microsoft Excel est utilisée pour la visualisation des données avec [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Pilote ODBC de Microsoft ruche](http://www.microsoft.com/download/details.aspx?id=40886)

##<a name="to-run-the-sample"></a>Pour exécuter l’exemple

1. À partir de votre navigateur web, naviguez jusqu'à l’URL suivante. Remplacer `<clustername>` avec le nom de votre cluster de HDInsight.

        https://<clustername>.azurehdinsight.net

    Lorsque vous y êtes invité, s’authentifier en utilisant le nom d’utilisateur administrateur et le mot de passe utilisé lors de la mise en service de ce cluster.

2. À partir de la page web qui s’ouvre, cliquez sur l’onglet **Mise en route présentation** et puis dans la catégorie des **Solutions avec des exemples de données** , cliquez sur l’échantillon de **données** de capteur.

    ![Mise en route de la photo de la galerie démarré](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Suivez les instructions fournies sur la page web à la fin de l’exemple.
