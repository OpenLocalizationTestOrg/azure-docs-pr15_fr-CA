<properties
    pageTitle="Haute densité d’hébergement de Service d’application Azure | Microsoft Azure"
    description="Haute densité d’hébergement de Service d’application Azure"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>Haute densité d’hébergement de Service d’application Azure

Lorsque vous utilisez le Service d’application, votre application est dissociée de la capacité allouée par deux concepts :

- **L’Application :** Représente l’application et sa configuration de l’exécution. Par exemple, il inclut la version de .NET que le runtime doit charger, les paramètres de l’application, etc..

- **Le Plan de Service de l’application :** Définit les caractéristiques de la capacité, ensemble des fonctionnalités disponibles et la localisation de l’application. Par exemple, les caractéristiques peuvent être gros ordinateur de (quatre cœurs), quatre instances, les fonctions Premium américains Extrême-Orient.

Une application est toujours liée à un plan de Service de l’application, mais un plan de Service d’application peut fournir une capacité pour une ou plusieurs des applications.

Par conséquent, la plate-forme offre la flexibilité nécessaire pour isoler une application unique ou ont plusieurs applications de partager des ressources en un plan de Service de l’application.

Toutefois, lorsque plusieurs applications partagent un plan de Service d’application, une instance de cette application s’exécute sur chaque instance de ce plan de Service de l’application.

## <a name="per-app-scaling"></a>Par la mise à l’échelle de l’application
*Par la mise à l’échelle de l’application* est une fonctionnalité qui peut être activée au niveau du plan de Service de l’application et ensuite utilisée par l’application.

Par application de mise à l’échelle met à l’échelle une application indépendamment du plan de Service de l’application qui l’héberge. De cette façon, un plan de Service d’application peut être configuré pour fournir des 10 instances, mais une application peut être définie à l’échelle pour les seules 5.

Le modèle de gestionnaire de ressources Azure suivant crée un plan de Service de l’application est mise à l’échelle des 10 instances et une application qui est configurée pour utiliser par la mise à l’échelle de l’application et la mise à l’échelle à seulement 5 instances.

Le plan de Service d’application consiste à définir la propriété **mise à l’échelle du site par site** True ( `"perSiteScaling": true`). L’application consiste à définir le **nombre de collaborateurs** à utiliser pour 5 (`"properties": { "numberOfWorkers": "5" }`).

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>Configuration recommandée pour l’hébergement de haute densité

Par la mise à l’échelle de l’application est une fonctionnalité qui est activée dans les régions d’Azure publiques et les environnements de Service d’application. Toutefois, la stratégie recommandée consiste à utiliser des environnements de Service d’application pour tirer parti de leurs fonctionnalités avancées et les pools de plus grandes capacité.  

Suivez ces étapes pour configurer la haute densité d’hébergement pour vos applications :

1. Configurer l’environnement de Service d’application, puis choisissez un pool de travail dédié pour le scénario d’hébergement haute densité.

1. Créer un plan de Service d’application unique et mettre à l’échelle à utiliser toute la capacité disponible dans le pool de travail.

1. L’indicateur de mise à l’échelle du site la valeur true sur le plan de Service de l’application.

1. Nouveaux sites sont créés et assignés à ce plan de Service d’application avec la propriété **numberOfWorkers** la valeur **1**. Cette configuration permet d’obtenir la densité la plus élevée possible sur ce pool de travail.

1. Le nombre de travailleurs peut être configuré indépendamment par site à accorder les ressources supplémentaires si nécessaire. Par exemple, un forte utilisation site peut définir des **numberOfWorkers** **3** pour avoir plus de capacité de traitement pour cette application, alors que les sites peu utilisé définissez **numberOfWorkers** à **1**.
