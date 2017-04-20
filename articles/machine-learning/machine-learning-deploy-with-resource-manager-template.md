<properties
    pageTitle="Déployer l’apprentissage à l’aide du modèle de gestionnaire de ressources Azure un espace de travail de l’ordinateur | Microsoft Azure"
    description="Comment déployer un espace de travail pour l’apprentissage de Machine Azure à l’aide du modèle de gestionnaire de ressources Azure"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Déployer l’apprentissage à l’aide du Gestionnaire de ressources Azure un espace de travail de l’ordinateur

## <a name="introduction"></a>Introduction
À l’aide d’un gestionnaire de ressources Azure modèle de déploiement enregistre le temps en vous donnant une méthode évolutive de déployer des composants interconnectés avec une validation et le mécanisme de reprise. Pour définir des espaces de travail formation Azure Machine, par exemple, vous devez tout d’abord configurer un compte de stockage Azure et de déployer votre espace de travail. Imaginez cette opération manuellement pour des centaines d’espaces de travail. Une alternative plus simple est d’utiliser un modèle de gestionnaire de ressources Azure pour déployer un espace de travail formation de Machine Azure et toutes ses dépendances. Cet article passe en revue ce processus pas à pas. Une excellente présentation du Gestionnaire de ressources Azure, consultez [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Pas à pas : créer un espace de travail de formation de Machine
Nous va créer un groupe de ressources Azure, puis déployer un nouveau compte de stockage Azure et d’Azure Machine Learning espace de travail à l’aide d’un modèle de gestionnaire de ressources. Une fois le déploiement terminé, nous imprime des informations importantes sur les espaces de travail qui ont été créées (la clé primaire, la workspaceID et l’URL de l’espace de travail).

### <a name="create-an-azure-resource-manager-template"></a>Créer un modèle de gestionnaire de ressources Azure
Un espace de travail de Machine formation requiert un compte de stockage Azure pour stocker le groupe de données lié.
Le modèle suivant utilise le nom du groupe de ressources pour générer le nom de compte de stockage et le nom de l’espace de travail.  Il utilise également le nom de compte de stockage sous la forme d’une propriété lors de la création de l’espace de travail.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Enregistrez ce modèle en tant que fichier mlworkspace.json sous c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Déployer le groupe de ressources, basé sur le modèle
* PowerShell ouverte
* Installez les modules pour Azure le Gestionnaire de ressources et de la gestion des services Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Ces étapes téléchargement puis installer les modules nécessaires pour effectuer les étapes restantes. Cette opération ne doit être effectuée qu’une fois dans l’environnement où vous exécutez les commandes PowerShell.   

* Authentifier vers Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Cette étape doit être répétée pour chaque session. Une fois authentifié, les informations sur votre abonnement doivent être affichées.

![Compte Azure][1]

Maintenant que nous avons accès à Azure, nous pouvons créer le groupe de ressources.

* Créer un groupe de ressources

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Vérifiez que le groupe de ressources est correctement configuré. **ProvisioningState** doit être « terminé ».
Le nom de groupe de ressources est utilisé par le modèle pour générer le nom de compte de stockage. Le nom de compte de stockage doit être comprise entre 3 et 24 caractères et utiliser des chiffres et des lettres minuscules uniquement.

![Groupe de ressources][2]

* Le déploiement de groupe de ressources, de déployer un nouvel espace de travail de formation de Machine.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Une fois le déploiement terminé, il est simple d’accéder aux propriétés de l’espace de travail que vous avez déployé. Par exemple, vous pouvez accéder le jeton de clé primaire.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Une autre façon de récupérer les jetons de l’espace de travail existant est d’utiliser la commande Invoke-AzureRmResourceAction. Par exemple, vous pouvez répertorier les jetons principales et secondaires de tous les espaces de travail.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Après la mise en service de l’espace de travail, vous pouvez également automatiser de nombreuses tâches de Studio de formation Azure Machine à l’aide du [PowerShell Module pour l’apprentissage de Machine Azure](http://aka.ms/amlps).

## <a name="next-steps"></a>Étapes suivantes 
* Pour en savoir plus sur les [Modèles de gestionnaire de ressources création Azure](../resource-group-authoring-templates.md). 
* Jetez un œil sur le [Référentiel de modèles Quickstart Azure](https://github.com/Azure/azure-quickstart-templates). 
* Regardez cette vidéo sur [Azure le Gestionnaire de ressources](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
