<properties
    pageTitle="Créer des clusters Hadoop, HBase, tempête ou étincelle sur Linux dans HDInsight à l’aide de PowerShell d’Azure | Microsoft Azure"
    description="Apprenez à créer des clusters Hadoop, HBase, tempête ou étincelle sous Linux pour HDInsight à l’aide de PowerShell d’Azure."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>Créer des grappes de serveurs Linux dans HDInsight à l’aide de PowerShell d’Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell est un environnement de script puissant qui vous permet de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Microsoft Azure. Ce document fournit des informations sur la création d’un cluster basé sur Linux de HDInsight à l’aide de PowerShell d’Azure. Il comprend également un exemple de script.

> [AZURE.NOTE] Azure PowerShell est uniquement disponible sur les clients Windows. Si vous utilisez un client Mac OS X, Unix ou Linux, voir [Création d’un cluster basé sur Linux de HDInsight à l’aide de la CLI d’Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) pour plus d’informations sur l’utilisation de l’infrastructure du langage commun Azure pour créer un cluster.

## <a name="prerequisites"></a>Conditions préalables
Vous devez avoir les éléments suivants avant de commencer cette procédure :

- Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- PowerShell Azure.
    Pour plus d’informations sur l’utilisation de PowerShell d’Azure avec HDInsight, reportez-vous à la section [HDInsight d’administrer à l’aide de PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande Windows PowerShell de HDInsight, consultez [référence d’applet de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Créer des clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour créer un cluster de HDInsight à l’aide de PowerShell d’Azure, vous devez effectuer les procédures suivantes :

- Créer un groupe de ressources Azure
- Créer un compte de stockage Azure
- Créer un conteneur d’Azure Blob
- Créer un cluster HDInsight

Les deux paramètres les plus importants que vous devez définir pour créer des clusters de Linux sont ceux qui spécifient le type de système d’exploitation et les détails d’utilisateur SSH :

- Assurez-vous que vous spécifiez le paramètre **- OSType** **Linux**.
- Pour utiliser le protocole SSH pour les sessions à distance sur les clusters, vous pouvez spécifier le mot de passe utilisateur SSH ou la clé publique SSH. Si vous spécifiez à la fois le mot de passe utilisateur SSH et la clé publique SSH, la clé sera ignorée. Si vous souhaitez utiliser le SSH pour les sessions à distance, vous devez spécifier un mot de passe SSH vide lorsque vous y êtes invité pour un. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Le script suivant montre comment créer un nouveau cluster :

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Les valeurs que vous spécifiez pour **$clusterCredentials** sont utilisés pour créer le compte d’utilisateur Hadoop pour le cluster. Utilisez ce compte pour se connecter au cluster.

Les valeurs que vous spécifiez pour **$sshCredentials** sont utilisés pour créer l’utilisateur SSH pour le cluster. Ce compte permet de démarrer une session SSH à distance sur le cluster et l’exécution de tâches.

> [AZURE.IMPORTANT] Dans ce script, vous devez spécifier le nombre de nœuds de traitement qui seront dans le cluster. Si vous prévoyez d’utiliser plus de 32 nœuds de travailleur (soit lors de la création du cluster, soit par la mise à l’échelle de cluster après la création), vous devez également spécifier une taille du nœud de tête au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, reportez-vous à la section [HDInsight de tarification](https://azure.microsoft.com/pricing/details/hdinsight/).

Il peut prendre jusqu'à 20 minutes pour créer un cluster.

L’exemple suivant montre comment ajouter un compte de stockage supplémentaires :

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>Personnaliser des clusters

- Consultez [HDInsight de personnaliser des clusters à l’aide des données d’amorçage](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Voir [clusters basés sur Windows de personnaliser la HDInsight à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="delete-the-cluster"></a>Supprimer du cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un cluster HDInsight, utilisez les ressources suivantes pour apprendre à travailler avec votre cluster.

### <a name="hadoop-clusters"></a>Hadoop clusters

* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)
* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)
* [Utilisez MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters HBase

* [Mise en route de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters de tempête

* [Développer des topologies de Java pour tempête sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation des composants de Python de tempête sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déployer et surveiller des topologies avec tempête sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clusters d’allumage

* [Créez une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance sur un cluster d’allumage à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Allumage avec BI : effectuer l’analyse interactive des données à l’aide d’étincelle dans HDInsight avec les outils d’analyse Décisionnelle](hdinsight-apache-spark-use-bi-tools.md)
* [Allumage avec apprentissage automatique : allumage utilisation de HDInsight pour prédire les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Diffusion en continu de l’allumage : Allumage d’utilisation dans HDInsight pour générer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
