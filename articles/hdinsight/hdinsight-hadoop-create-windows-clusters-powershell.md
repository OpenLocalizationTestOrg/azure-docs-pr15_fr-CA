<properties
   pageTitle="Créer les clusters basés sur Windows de Hadoop dans HDInsight à l’aide de PowerShell d’Azure | Microsoft Azure"
    description="Apprenez à créer des clusters pour Azure HDInsight à l’aide de PowerShell d’Azure."
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
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Créer les clusters basés sur Windows de Hadoop dans HDInsight à l’aide de PowerShell d’Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters HDInsight à l’aide de PowerShell d’Azure. PowerShell Azure est un module qui fournit des applets de commande pour gérer les Azure avec Windows PowerShell. Pour autre création du cluster les fonctionnalités et outils cliquez sur l’onglet, sélectionnez en haut de cette page ou [les méthodes de création de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Conditions préalables :

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer les instructions de cet article, vous devez disposer des éléments suivants :

- Abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- PowerShell Azure.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Créer des clusters
Azure PowerShell est un environnement de script puissant qui vous permet de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur la création d’un cluster de HDInsight à l’aide de PowerShell d’Azure. Pour plus d’informations sur la configuration d’une station de travail pour exécuter des applets de commande Windows PowerShell de HDInsight, consultez [installer et configurer Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation de PowerShell d’Azure avec HDInsight, reportez-vous à la section [HDInsight d’administrer à l’aide de PowerShell](hdinsight-administer-use-powershell.md). Pour une liste des applets de commande de Windows PowerShell HDInsight, consultez [référence d’applet de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Les procédures suivantes sont nécessaires à la création d’un cluster de HDInsight à l’aide de PowerShell d’Azure :

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>Créer des clusters à l’aide du modèle ARM

Vous pouvez utiliser Azure PowerShell pour déployer un modèle ARM qui crée un cluster HDInsight.  Voir [appel de modèles à l’aide de PowerShell d’Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell).

##<a name="customize-clusters"></a>Personnaliser des clusters

- Consultez [HDInsight de personnaliser des clusters à l’aide des données d’amorçage](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Voir [clusters basés sur Windows de personnaliser la HDInsight à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).


##<a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris les différentes façons de créer un cluster de HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Mise en route de HDInsight d’Azure](hdinsight-hadoop-linux-tutorial-get-started.md) - Apprenez à commencer à travailler avec votre cluster HDInsight
* [Hadoop de soumettre des travaux par programme](hdinsight-submit-hadoop-jobs-programmatically.md) - Apprenez à soumettre par programme des tâches de HDInsight
* [Hadoop de gérer des clusters dans HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md) - Apprenez à travailler avec HDInsight à l’aide de PowerShell d’Azure
* [Azure documentation du kit SDK de HDInsight]  [ hdinsight-sdk-documentation] -Découvrez le SDK HDInsight




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
