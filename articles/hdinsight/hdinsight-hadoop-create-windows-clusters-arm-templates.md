<properties
   pageTitle="Créer des clusters d’Hadoop de basées sur Windows en HDInsight à l’aide des modèles du Gestionnaire de ressources Azure | Microsoft Azure"
    description="Apprenez à créer des clusters pour HDInsight Azure à l’aide des modèles du Gestionnaire de ressources Azure."
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
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Créer des clusters d’Hadoop de basées sur Windows en HDInsight à l’aide des modèles du Gestionnaire de ressources Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters HDInsight à l’aide des modèles du Gestionnaire de ressources Azure. Pour plus d’informations, voir [déploiement d’une application avec le modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md). Pour autre création du cluster les fonctionnalités et outils cliquez sur l’onglet, sélectionnez en haut de cette page ou [les méthodes de création de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Conditions préalables :

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Avant de commencer les instructions de cet article, vous devez disposer des éléments suivants :

- [Abonnement azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI Azure PowerShell ou Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Modèles du Gestionnaire de ressources

Modèle de gestionnaire de ressources facilite la créer des clusters de HDInsight, leurs ressources dépendantes (par exemple, le compte de stockage par défaut) et d’autres ressources (telles que SQL Azure base de données à utiliser Apache Sqoop) pour votre application dans une opération unique et coordonnée. Dans le modèle, vous définissez les ressources qui sont nécessaires à l’application et spécifiez les paramètres de déploiement pour entrer des valeurs pour les différents environnements. Le modèle est composé de JSON et des expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

Un modèle de gestionnaire de ressources pour la création d’un HDInsight cluster et le compte de stockage Azure dépendant se trouvent dans [L’annexe A](#appx-a-arm-template). Utilisez un éditeur de texte pour enregistrer le modèle dans un fichier sur votre station de travail. Vous allez apprendre à appeler le modèle à l’aide de différents outils.

Pour plus d’informations sur le modèle du Gestionnaire de ressources, reportez-vous à la section.

- [Modèles du Gestionnaire de ressources Azure auteur](../resource-group-authoring-templates.md)
- [Déployer une application avec le modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md)


## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

La procédure suivante crée un cluster HDInsight.

**Pour déployer un cluster à l’aide du Gestionnaire de ressources du modèle**

1. Enregistrez le fichier de json dans [l’annexe A](#appx-a-arm-template) à votre station de travail.
2. Définir les paramètres si nécessaire.
3. Exécutez le modèle à l’aide du script PowerShell suivant :

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    Le script PowerShell configure uniquement le nom du cluster et le nom du compte de stockage.  Vous pouvez définir d’autres valeurs dans le modèle de gestionnaire de ressources. 
    
Pour plus d’informations, voir [déploiement avec PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Déployer avec l’interface CLI Azure

L’exemple suivant crée un cluster, un compte de stockage dépendant et son conteneur en appelant un modèle du Gestionnaire de ressources :

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Déployer avec l’API REST

Consultez [déploiement avec l’API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## <a name="deploy-with-visual-studio"></a>Déployer avec Visual Studio

Avec Visual Studio, vous pouvez créer un projet de groupe de ressources et le déployer vers Azure via l’interface utilisateur. Vous sélectionnez le type de ressources à inclure dans votre projet, et ces ressources sont automatiquement ajoutés au modèle du Gestionnaire de ressources. Le projet fournit également un script PowerShell pour déployer le modèle.

Pour une introduction aux groupes de ressources à l’aide de Visual Studio, consultez [Création et le déploiement de groupes de ressources Azure via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##<a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris les différentes façons de créer un cluster de HDInsight. Pour plus d’informations, consultez les articles suivants :


- Pour obtenir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, reportez-vous à la section [ressources de déploiement à l’aide de bibliothèques .NET et un modèle](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour obtenir un exemple détaillé de déploiement d’une application, consultez [disposition et déployer microservices prévisible dans Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Pour obtenir des instructions sur le déploiement de votre solution aux différents environnements, consultez [développement et des environnements de test de Microsoft Azure](../solution-dev-test-environments.md).
- Pour obtenir des informations sur les sections du modèle de gestionnaire de ressources Azure, consultez [modèles de création](../resource-group-authoring-templates.md).
- Pour obtenir une liste des fonctions que vous pouvez utiliser dans un modèle de gestionnaire de ressources Azure, consultez [fonctions de modèle](../resource-group-template-functions.md).



##<a name="appx-a-resource-manager-template"></a>Modèle de gestionnaire de ressources AppX-a :

Le modèle de gestionnaire de ressources Azure suivant crée un cluster de Hadoop de basée sur Windows avec le compte de stockage Azure dépendant.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

