<properties
   pageTitle="Créer des clusters d’Hadoop de basées sur Windows en HDInsight à l’aide de la CLI d’Azure"
    description="Apprenez à créer des clusters pour Azure HDInsight à l’aide d’Azure CLI."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Créer des clusters d’Hadoop de basées sur Windows en HDInsight à l’aide de la CLI d’Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters HDInsight à l’aide de la CLI d’Azure. Pour autre création du cluster les fonctionnalités et outils cliquez sur l’onglet, sélectionnez en haut de cette page ou [les méthodes de création de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Conditions préalables :

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Avant de commencer les instructions de cet article, vous devez disposer des éléments suivants :

- **Abonnement azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **CLI azure**.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="connect-to-azure"></a>Se connecter à Azure

Utilisez la commande suivante pour vous connecter à Azure :

    azure login

Pour plus d’informations sur l’authentification à l’aide d’un compte de travail ou à l’école, voir [se connecter à un abonnement Azure à partir de la CLI d’Azure](../xplat-cli-connect.md).

Pour basculer vers le mode ARM, utilisez la commande suivante :

    azure config mode arm

Pour obtenir de l’aide, utilisez le commutateur **-h** .  Par exemple :

    azure hdinsight cluster create -h

##<a name="create-clusters"></a>Créer des clusters

Il vous faut une gestion de ressources Azure (ARM) et un compte de stockage Azure Blob avant de pouvoir créer un cluster de HDInsight. Pour créer un cluster de HDInsight, vous devez spécifier les éléments suivants :

- **Groupe de ressources Azure**: compte de A données lac Analytique doit être créé au sein d’un groupe de ressources d’Azure. Azure Resource Manager vous permet de travailler avec les ressources de votre application en tant que groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans une opération unique et coordonnée.

    Pour répertorier les groupes de ressources dans votre abonnement :

        azure group list

    Pour créer un nouveau groupe de ressources :

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nom du cluster HDInsight**

- **Emplacement**: parmi les centres de données Azure qui prend en charge les clusters HDInsight. Pour obtenir la liste des emplacements pris en charge, reportez-vous à la section [HDInsight de tarification](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Compte de stockage par défaut**: HDInsight utilise un conteneur de stockage Azure Blob en tant que le système de fichiers par défaut. Un compte de stockage Azure est nécessaire avant de pouvoir créer un cluster de HDInsight.

    Pour créer un nouveau compte de stockage Azure :

        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

    > [AZURE.NOTE]Le compte de stockage doit être colocalisé avec HDInsight dans le centre de données.
    > Le type de compte de stockage ne peut pas être ZRS, car ZRS ne prend en charge la table.

    Pour plus d’informations sur la création d’un compte de stockage Azure via le portail d’Azure, reportez-vous à la section [créer], de gérer ou supprimer un compte de stockage [azure-créer-storageaccount].

    Si vous avez déjà disposez d’un compte de stockage mais que vous ne connaissez pas le nom de compte et clé de compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

    Pour plus d’informations sur l’obtention des informations via le portail d’Azure, consultez la section « Gestion de votre compte de stockage » de [comptes de stockage sur Azure](../storage/storage-create-storage-account#manage-your-storage-account).

- **Conteneur de Blob de valeur par défaut (facultatif)**: la commande **cluster d’azure hdinsight create** crée le conteneur s’il n’existe pas. Si vous choisissez de créer le conteneur à l’avance, vous pouvez utiliser la commande suivante :

    conteneur de stockage Azure créer--nom de compte «<Storage Account Name>»--clé de compte <Storage Account Key> [ContainerName]

Une fois que vous avez préparé le compte de stockage, vous êtes prêt à créer un cluster :


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##<a name="create-clusters-using-configuration-files"></a>Créer des clusters à l’aide de fichiers de configuration
En règle générale, vous créez un cluster HDInsight exécutez de tâches sur elle et puis supprimez le cluster pour réduire le coût. L’interface de ligne de commande vous donne la possibilité d’enregistrer la configuration dans un fichier, afin que vous pouvez le réutiliser chaque fois que vous créez un cluster.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Exemple : Création d’un fichier de configuration qui contient une action de script à exécuter lors de la création d’un cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##<a name="create-clusters-with-script-action"></a>Créer des clusters avec action de script

Créer un fichier de configuration qui contient une action de script à exécuter lors de la création d’un cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Créer un cluster avec une action de script

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Pour des informations sur l’action script général, voir [clusters de HDInsight de personnaliser à l’aide de l’Action de Script (Linux)](hdinsight-hadoop-customize-cluster.md).


## <a name="create-clusters-using-arm-templates"></a>Créer des clusters à l’aide de modèles ARM

Vous pouvez utiliser la CLI pour créer des clusters en appelant les modèles ARM. Reportez-vous à [déployer avec l’interface CLI Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Voir aussi

- [Mise en route de HDInsight d’Azure](hdinsight-hadoop-linux-tutorial-get-started.md) - Apprenez à commencer à travailler avec votre cluster HDInsight
- [Hadoop de soumettre des travaux par programme](hdinsight-submit-hadoop-jobs-programmatically.md) - Apprenez à soumettre par programme des tâches de HDInsight
- [Gérer les clusters Hadoop dans HDInsight à l’aide de la CLI d’Azure](hdinsight-administer-use-command-line.md)
- [À l’aide de l’interface CLI Azure pour Mac, Linux et Windows avec la gestion des services Azure](../virtual-machines-command-line-tools.md)
