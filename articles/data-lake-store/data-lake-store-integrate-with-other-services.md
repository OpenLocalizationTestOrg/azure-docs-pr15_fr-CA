<properties
   pageTitle="Intégration du magasin de données LAC avec d’autres Services Azure | Microsoft Azure"
   description="Comprendre comment la banque de données lac s’intègre avec d’autres services Azure"
   documentationCenter=""
   services="data-lake-store"
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="integrating-data-lake-store-with-other-azure-services"></a>Intégration du magasin de données LAC avec d’autres Services Azure

Magasin de LAC de données de Azure peut être utilisé en conjonction avec d’autres services Azure pour permettre un plus large éventail de scénarios. L’article suivant répertorie les services que le magasin de données lac peut être intégré à.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Utiliser le magasin de données LAC avec Azure HDInsight

Vous pouvez configurer un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) qui utilise le magasin de données lac le stockage très compatible. Pour cette version, pour les clusters Hadoop et tempête sous Windows et Linux, vous pouvez utiliser magasin lac de données uniquement sous la forme d’un stockage supplémentaire. Ces clusters toujours utiliseront le stockage Azure (WASB) comme l’espace de stockage par défaut. Toutefois, pour les clusters de HBase sous Windows et Linux, vous pouvez utiliser Data Store de LAC comme le stockage par défaut, ou le stockage supplémentaire ou les deux.

Pour obtenir des instructions sur la façon de configurer un cluster HDInsight avec le magasin de données lac, voir :

* [Configurer un cluster HDInsight avec magasin lac de données à l’aide du portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Configurer un cluster HDInsight avec magasin lac de données à l’aide de PowerShell d’Azure](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Préférez des vidéos ?** Suivez les liens ci-dessous pour regarder des vidéos avec des instructions sur l’utilisation du magasin de données LAC avec des clusters de HDInsight.

* [Créer un cluster de HDInsight disposant d’un accès au magasin de données lac](https://mix.office.com/watch/l93xri2yhtp2)
* Une fois que le cluster est configuré, [accéder aux données dans le magasin de LAC de données à l’aide de scripts de ruche et porcines](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Utiliser le magasin de données LAC avec données Azure lac Analytique

[Analytique de LAC de données Azure](../data-lake-analytics/data-lake-analytics-overview.md) vous permet d’utiliser avec données à grande échelle du nuage. Il met les ressources et vous permet de faire analytique sur téraoctets ou même exaoctets de données qui peuvent être stockées dans un certain nombre de sources de données prises en charge, un d’eux est le magasin de données lac dynamiquement. Données lac Analytique est spécialement optimisé pour travailler avec un magasin de LAC de données Azure - offrant le plus haut niveau de performance, de débit et de parallélisation pour vous les charges de travail données volumineuses.

Pour obtenir des instructions sur l’utilisation des données lac Analytique avec le magasin de données lac, consultez [Mise en route de données Analytique de LAC à l’aide du magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Préférez des vidéos ?** Suivez les liens ci-dessous pour regarder des vidéos avec des instructions sur l’utilisation du magasin de données LAC avec des clusters de HDInsight.

* [Connecter des données Azure lac Analytique à la banque de LAC de données Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Magasin de LAC Azure données Access via données lac Analytique](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>Utiliser le magasin de données LAC avec Azure Data Factory

Vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour recevoir des données provenant de tables Azure, base de données de SQL Azure, Data Warehouse de SQL Azure, BLOB de stockage Azure et bases de données sur site. Est un citoyen de première classe dans l’écosystème Azure, Azure Data Factory permet d’orchestrer l’intégration de données à partir de ces sources au magasin de LAC de données Azure.

Pour obtenir des instructions sur l’utilisation d’Azure Data Factory avec le magasin de données lac, voir [déplacer des données vers et à partir du magasin de LAC de données à l’aide de la fabrique de données](../data-factory/data-factory-azure-datalake-connector.md).

**Vidéos à nouveau !** Reportez-vous à [Orchestration de données à l’aide de la fabrique de données Azure Azure magasin de données lac](https://mix.office.com/watch/1oa7le7t2u4ka). 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copier des données d’objets BLOB de stockage Azure dans le magasin de données lac

Magasin de LAC de données Azure fournit un outil de ligne de commande, AdlCopy, ce qui vous permet de copier des données depuis le stockage Blob Azure dans un compte de banque de données lac. Pour plus d’informations, voir [copier des données d’objets BLOB de stockage Azure au magasin de données lac](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copier des données entre la base de données de SQL Azure et le magasin de données lac

Vous pouvez utiliser Apache Sqoop pour importer et exporter des données entre la base de données de SQL Azure et le magasin de données lac. Pour plus d’informations, voir [copier des données entre un magasin de données LAC et base de données Azure SQL à l’aide de Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Regardez cette vidéo** sur [à l’aide de Apache Sqoop pour déplacer des données entre des sources relationnelles et magasin de LAC de données Azure](https://mix.office.com/watch/1butcdjxmu114).

## <a name="use-data-lake-store-with-stream-analytics"></a>Utiliser le magasin de données LAC avec flux Analytique

Vous pouvez utiliser la banque de données lac comme l’une des sorties pour stocker les données transmises à l’aide d’Azure flux Analytique. Pour plus d’informations, reportez-vous [à l’aide de données de flux à partir du Blob de stockage Azure dans le magasin de données lac Azure flux Analytique](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Utiliser le magasin de données LAC avec alimentation BI

Vous pouvez utiliser la BI de puissance pour importer des données à partir d’un compte de banque de données LAC pour analyser et visualiser les données. Pour plus d’informations, consultez [analyse des données dans le magasin de données lac à l’aide de BI de puissance](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Utiliser le magasin de données LAC avec le catalogue de données

Vous pouvez enregistrer les données de magasin de données lac dans le catalogue de données Azure afin de les rendre détectables dans toute l’organisation. Pour plus d’informations, consultez [inscrire les données de magasin de données lac dans le catalogue de données Azure](data-lake-store-with-data-catalog.md).


## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure lac de magasin de données](data-lake-store-overview.md)
- [Mise en route de magasin lac de données à l’aide du portail](data-lake-store-get-started-portal.md)
- [Mise en route de magasin de LAC de données à l’aide de PowerShell](data-lake-store-get-started-powershell.md)  
