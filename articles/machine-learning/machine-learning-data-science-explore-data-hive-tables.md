<properties
    pageTitle="Explorer des données dans les tables de ruche avec les requêtes de ruche | Microsoft Azure"
    description="Explorer des données dans les tables de ruche à l’aide de requêtes de la ruche."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorer des données dans les tables de ruche avec les requêtes de la ruche

Ce document fournit des exemples de scripts de ruche qui sont utilisés pour explorer des données dans les tables de ruche dans un cluster HDInsight Hadoop.

Les liens de **menu** suivants vers les rubriques qui décrivent comment utiliser les outils pour explorer des données à partir de différents environnements de stockage.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez :

* Créer un compte de stockage Azure. Si vous avez besoin d’instructions, reportez-vous à la section [Création d’un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Mise en service d’un cluster d’Hadoop personnalisée avec le service HDInsight. Si vous avez besoin d’instructions, reportez-vous à la section [Personnaliser Azure HDInsight Hadoop Clusters avancée d’Analytique](machine-learning-data-science-customize-hadoop-cluster.md).
* Les données ont été téléchargées vers les tables de ruche dans Azure HDInsight Hadoop clusters. Si elle n’est pas le cas, suivez les instructions pour télécharger les données vers les tables de la ruche tout d’abord [créer et charger des données à des tables de la ruche](machine-learning-data-science-move-hive-tables.md) .
* Activer l’accès distant au cluster. Si vous avez besoin d’instructions, reportez-vous à la section [accès le nœud de tête de Hadoop Cluster](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Si vous avez besoin de savoir comment soumettre des requêtes de la ruche, voir [Comment faire pour envoyer les requêtes de la ruche](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Scripts exemple ruche de requête d’exploration de données

1. Obtenir le nombre d’observations par partition `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtenir le nombre d’observations par jour `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obtenir les niveaux d’une colonne par catégorie  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtenir le nombre de niveaux dans la combinaison de deux colonnes catégorielles `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtenir la distribution pour des colonnes numériques  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraire des enregistrements à partir de la jointure de deux tables

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de requête supplémentaires pour les scénarios de données de voyage de taxi

Exemples de requêtes qui sont spécifiques à des scénarios de [Données de voyage NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/) sont également fournis dans le [référentiel de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ces requêtes déjà le schéma de données spécifié et sont prêts à être soumis à l’exécution.
