<properties
    pageTitle="Gérer les clusters Hadoop à l’aide de la CLI d’Azure | Microsoft Azure"
    description="L’utilisation de l’infrastructure du langage commun Azure pour gérer les clusters Hadoop dans HDIsight"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gérer les clusters Hadoop dans HDInsight à l’aide de la CLI d’Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Apprenez à utiliser l' [Interface de ligne de commande de Azure](../xplat-cli-install.md) pour gérer les clusters Hadoop dans Azure HDInsight. L’infrastructure du langage commun Azure est implémenté dans Node.js. Il peut être utilisé sur n’importe quelle plate-forme qui prend en charge les Node.js, y compris Windows, Mac et Linux.

Cet article traite uniquement à l’aide de la CLI Azure avec HDInsight. Pour un guide général sur l’utilisation de CLI d’Azure, consultez [installer et configurer l’infrastructure du langage commun Azure][azure-command-line-tools].

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI** - voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) pour plus d’informations d’installation et de configuration.
- **Se connecter à Azure**, en utilisant la commande suivante :

        azure login

    Pour plus d’informations sur l’authentification à l’aide d’un compte de travail ou à l’école, voir [se connecter à un abonnement Azure à partir de la CLI d’Azure](xplat-cli-connect.md).
    
- **Basculer vers le mode Azure le Gestionnaire de ressources**à l’aide de la commande suivante :

        azure config mode arm

Pour obtenir de l’aide, utilisez le commutateur **-h** .  Par exemple :

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>Créer des clusters

Consultez [basé sur Linux de créer des clusters dans HDInsight à l’aide de la CLI d’Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

##<a name="list-and-show-cluster-details"></a>Répertorier et afficher les détails du cluster
Pour répertorier et afficher les détails du cluster, utilisez les commandes suivantes :

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI. CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>Supprimer de clusters

Pour supprimer un cluster, utilisez la commande suivante :

    azure hdinsight cluster delete <Cluster Name>

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui contient le cluster. Veuillez noter cette opération va supprimer toutes les ressources dans le groupe, y compris le compte de stockage par défaut.

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>Clusters d’échelle

Pour modifier la taille de cluster Hadoop :

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Activer ou désactiver l’accès HTTP pour un cluster

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Activer ou désactiver l’accès RDP pour un cluster

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à effectuer les différentes tâches d’administration de cluster HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Administrer des HDInsight à l’aide du portail Azure] [hdinsight-admin-portal]
* [Administrer les HDInsight à l’aide de PowerShell d’Azure] [hdinsight-admin-powershell]
* [Mise en route de HDInsight d’Azure] [hdinsight-get-started]
* [L’utilisation de l’infrastructure du langage commun Azure] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Répertorier et afficher des clusters"
