<properties 
    pageTitle="Mettre en service un Cache Redis | Microsoft Azure" 
    description="Modèle de gestionnaire de ressources Azure permet de déployer un Cache Redis d’Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="sdanie"/>

# <a name="create-a-redis-cache-using-a-template"></a>Créer un Cache de Redis à l’aide d’un modèle

Dans cette rubrique, vous allez apprendre à créer un modèle de gestionnaire de ressources Azure qui déploie un Cache Redis d’Azure. Le cache peut être utilisé avec un compte de stockage existant pour conserver les données de diagnostic. Vous apprendrez également comment définir les ressources qui sont déployés et comment définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements ou personnaliser pour répondre à vos besoins.

Actuellement, les paramètres de diagnostic sont partagés pour tous les caches dans la même région d’un abonnement. Mise à jour un cache dans la région affecte tous les autres caches dans la région.

Pour plus d’informations sur la création de modèles, consultez [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Pour le modèle complet, consultez [modèle Redis le Cache](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Modèles du Gestionnaire de ressources pour le nouveau [niveau de prime](cache-premium-tier-intro.md) sont disponibles. 
>
>-    [Créer un Cache de Redis Premium avec les clusters](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Créer un Cache de prime Redis avec la persistance des données](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Créer un Cache Redis de prime avec VNet et clustering facultatif](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Pour vérifier les derniers modèles, voir [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/) et recherchez `Redis Cache`.

## <a name="what-you-will-deploy"></a>Ce que vous allez déployer

Dans ce modèle, vous allez déployer un Cache Azure Redis qui utilise un compte de stockage pour les données de diagnostic.

Pour exécuter le déploiement automatique, cliquez sur le bouton suivant :

[![Déployer vers Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources d’Azure, vous définissez des paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section appelée de paramètres qui contient toutes les valeurs de paramètre.
Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui doivent restent les mêmes. Chaque valeur de paramètre est utilisé dans le modèle pour définir les ressources qui sont déployés. 


[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation

L’emplacement du Cache Redis. Pour des performances optimales, utiliser le même emplacement que l’application à utiliser avec le cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName

Le nom du compte de stockage existant à utiliser pour les tests de diagnostic. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort

Une valeur de type boolean qui indique s’il faut autoriser l’accès via des ports non-SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus

Une valeur qui indique si les tests de diagnostic est activé. Utilisez ON ou OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

### <a name="redis-cache"></a>Redis de Cache

Crée le Cache Redis Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Commandes à exécuter de déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)] 

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


