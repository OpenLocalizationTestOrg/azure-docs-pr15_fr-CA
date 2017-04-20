<properties
   pageTitle="Copier des données vers et à partir de WASB dans le magasin de LAC de données à l’aide de Distcp | Microsoft Azure"
   description="Utilisez l’outil Distcp pour copier les données vers et à partir d’objets BLOB de stockage Azure au magasin de données lac"
   services="data-lake-store"
   documentationCenter=""
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

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Permet de copier des données entre les objets BLOB de stockage Azure et magasin de données lac Distcp

> [AZURE.SELECTOR]
- [À l’aide de DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [À l’aide de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Une fois que vous avez créé un cluster HDInsight qui a accès à un compte de banque de données lac, vous pouvez utiliser les outils d’écosystème Hadoop comme Distcp pour copier les données **vers et à partir** d’un stockage de cluster HDInsight (WASB) dans un compte de banque de données lac. Cet article fournit des instructions sur la manière d’atteindre cet objectif.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Activer votre abonnement Azure** pour la présentation publique de magasin de données lac. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **HDInsight d’azure cluster** d’accéder à un compte de banque de données lac. Voir [Création d’un cluster de HDInsight avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md). Assurez-vous que vous activez le Bureau à distance pour le cluster.

## <a name="do-you-learn-fast-with-videos"></a>Vous découvrez rapidement avec les vidéos ?

[Regardez cette vidéo](https://mix.office.com/watch/1liuojvdx6sie) sur la copie de données entre objets BLOB de stockage Azure et magasin de LAC de données à l’aide de DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Utilisez Distcp à partir d’un bureau à distance (cluster Windows) ou SSH (cluster Linux)

Un cluster de HDInsight est fourni avec l’utilitaire Distcp, qui peut être utilisé pour copier des données provenant de différentes sources dans un cluster de HDInsight. Si vous avez configuré le cluster HDInsight pour utiliser le magasin de données lac comme un espace de stockage supplémentaire, l’utilitaire Distcp peut être utilisé-de-l’emploi pour copier des données vers et à partir d’un compte de banque de données lac ainsi. Dans cette section, nous examinons comment utiliser l’utilitaire Distcp.

1. Si vous disposez d’un cluster Windows, à distance dans un cluster de HDInsight qui a accès à un compte de banque de données lac. Pour obtenir des instructions, voir [se connecter à des clusters à l’aide de RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Dans le bureau du cluster, ouvrez la ligne de commande Hadoop.

    Si vous avez un cluster Linux, utiliser le protocole SSH pour se connecter au cluster. Voir [se connecter à un cluster basé sur Linux de HDInsight](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Exécutez les commandes à partir de l’invite de commandes SSH.

3. Vérifiez si vous pouvez accéder le BLOB de stockage Azure (WASB). Exécutez la commande suivante :

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Il doit fournir une liste du contenu dans l’objet blob de stockage.

4. De même, vérifiez si vous pouvez accéder le compte de la banque de données lac du cluster. Exécutez la commande suivante :

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Il doit fournir une liste de fichiers/dossiers dans le compte de la banque de données lac.

5. Utilisez Distcp pour copier des données à partir de WASB à un compte de banque de données lac.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Il copie le contenu du **dossier/exemple** données/gutenberg/de WASB à **/myfolder** dans le compte de la banque de données lac.

6. De même, utilisez Distcp pour copier les données de compte de la banque de données lac à WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Il copie le contenu de **/myfolder** dans le compte de la banque de données **lac/exemple** données/gutenberg/dossier dans WASB.

## <a name="see-also"></a>Voir aussi

- [Copier des données d’objets BLOB de stockage Azure au magasin de données lac](data-lake-store-copy-data-azure-storage-blob.md)
- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
