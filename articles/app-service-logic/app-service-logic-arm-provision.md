<properties 
    pageTitle="Créer une application de logique à l’aide des modèles du Gestionnaire de ressources Azure dans le Service d’application Azure | Microsoft Azure" 
    description="Un modèle du Gestionnaire de ressources Azure permet de déployer une application de logique vide pour définir le flux de travail." 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>Créer une application de logique à l’aide d’un modèle

Utiliser un modèle de gestionnaire de ressources Azure pour créer une application vide logique qui peut être utilisée pour définir le flux de travail. Vous pouvez définir les ressources qui sont déployés et comment définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements ou personnaliser pour répondre à vos besoins.

Pour plus d’informations sur les propriétés de l’application logique, voir [API de gestion des flux de travail logique d’application](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Pour obtenir des exemples de la définition elle-même, voir [définitions d’auteur logique d’application](app-service-logic-author-definitions.md). 

Pour plus d’informations sur la création de modèles, consultez [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Pour le modèle complet, consultez [modèle de logique d’application](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Ce que vous allez déployer

Avec ce modèle, vous déployez une application de logique.

Pour exécuter le déploiement automatique, cliquez sur le bouton suivant :  

[![Déployer vers Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

### <a name="logic-app"></a>Application de la logique

Crée l’application logique.

Les modèles utilise une valeur de paramètre pour le nom logique de l’application. Il définit l’emplacement de l’application logique vers le même emplacement que le groupe de ressources. 

Cette définition particulière s’exécute une fois par heure et envoie une commande ping à l’emplacement spécifié dans le paramètre **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Commandes à exécuter de déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
