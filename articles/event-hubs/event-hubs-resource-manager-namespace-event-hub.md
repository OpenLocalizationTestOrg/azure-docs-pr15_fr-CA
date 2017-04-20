<properties
    pageTitle="Créer un espace de noms de concentrateurs d’événement avec le groupe de concentrateur de l’événement et le consommateur à l’aide d’un modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Créer un espace de noms de concentrateurs d’événement avec le concentrateur d’événements et le groupe de consommateurs à l’aide du modèle de gestionnaire de ressources Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Créer un espace de noms de concentrateurs d’événement avec le groupe de concentrateur de l’événement et le consommateur à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article explique comment utiliser un modèle de gestionnaire de ressources Azure qui crée un espace de noms de concentrateurs d’événement avec un concentrateur d’événements et un groupe de consommateurs. Vous apprendrez comment définir les ressources qui sont déployés et comment définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou personnaliser pour l’adapter à vos besoins

Pour plus d’informations sur la création de modèles, consultez [modèles de création d’un gestionnaire de ressources Azure][].

Pour le modèle complet, consultez [concentrateur d’événements et le modèle de groupe de consommateurs][] sur GitHub.

>[AZURE.NOTE]
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et rechercher les concentrateurs d’événement.

## <a name="what-will-you-deploy"></a>Ce que vous allez déployer ?

Avec ce modèle, vous allez déployer un espace de noms de concentrateurs d’événement avec un concentrateur d’événements et un groupe de consommateurs.

[Concentrateurs d’événement](../event-hubs/event-hubs-what-is-event-hubs.md) est un événement de traitement du service utilisé pour fournir des entrée d’événement et la télémétrie vers Azure à grande échelle, avec une latence faible et haute fiabilité.

Pour exécuter le déploiement automatique, cliquez sur le bouton suivant :

[![Déployer vers Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources d’Azure, vous définissez des paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section intitulée `Parameters` qui contient toutes les valeurs de paramètre. Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui restent toujours la même. Chaque valeur de paramètre est utilisé dans le modèle pour définir les ressources qui sont déployés.

Le modèle définit les paramètres suivants.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Le nom de l’espace de noms de concentrateurs d’événement à créer.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Le nom du concentrateur d’événements créé dans l’événement concentrateurs espace de noms.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Le nom du groupe de consommateurs créé pour le concentrateur de l’événement.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

La version de l’API du modèle.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Ressources pour le déploiement

Crée un espace de noms du type **EventHubs**, avec un concentrateur d’événements et un groupe de consommateurs.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Commandes à exécuter de déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Création de modèles du Gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
[Modèles d’Azure Quickstart]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Modèle groupe Hub et le consommateur d’événements]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
