<properties
    pageTitle="Créer un espace de noms du Bus de Service avec le sujet et l’abonnement à l’aide d’un modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Créer un espace de noms du Bus de Service avec le sujet et l’abonnement à l’aide du modèle de gestionnaire de ressources Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/14/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Créer un espace de noms du Bus de Service avec le sujet et l’abonnement à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article explique comment utiliser un modèle de gestionnaire de ressources Azure qui crée un espace de noms du Bus de Service avec une rubrique et d’abonnement. Vous apprendrez comment définir les ressources qui sont déployés et comment définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou personnaliser pour l’adapter à vos besoins

Pour plus d’informations sur la création de modèles, consultez [modèles de création d’un gestionnaire de ressources Azure][].

Pour le modèle complet, consultez le modèle [d’espace de noms de Bus de Service avec la rubrique et d’abonnement][] .

>[AZURE.NOTE] Les modèles de Azure le Gestionnaire de ressources suivants sont disponibles pour téléchargement et déploiement.
>
>-    [Créer un espace de noms du Bus de Service avec la règle d’autorisation et de file d’attente](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Créer un espace de noms du Bus de Service avec la file d’attente](service-bus-resource-manager-namespace-queue.md)
>-    [Créer un espace de noms du Bus des services](service-bus-resource-manager-namespace.md)
>-    [Créer un espace de noms de concentrateurs d’événement avec un groupe d’événements Hub et le consommateur](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et rechercher le « Bus de Service ».

## <a name="what-will-you-deploy"></a>Ce que vous allez déployer ?

Avec ce modèle, vous allez déployer un espace de noms du Bus de Service avec la rubrique et d’abonnement.

[Les abonnements et les rubriques du Bus de Service](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fournissent un formulaire un-à-plusieurs de la communication, dans un modèle de *publication et d’abonnement* .

Pour exécuter le déploiement automatique, cliquez sur le bouton suivant :

[![Déployer vers Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources d’Azure, vous définissez des paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section intitulée `Parameters` qui contient toutes les valeurs de paramètre. Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui restent toujours la même. Chaque valeur de paramètre est utilisé dans le modèle pour définir les ressources qui sont déployés.

Le modèle définit les paramètres suivants.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Le nom de l’espace de noms du Bus de Service à créer.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Le nom de la rubrique créé dans l’espace de noms du Bus de Service.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Le nom de l’abonnement créé dans l’espace de noms du Bus de Service.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La version de l’API du Bus des services du modèle.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

Crée un espace de noms standard Bus des services de **messagerie**, type de rubrique et d’abonnement.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Commandes à exécuter de déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et déployé des ressources à l’aide du Gestionnaire de ressources Azure, vous apprenez à gérer ces ressources en consultant ces articles :

- [Gérer le Bus de Service avec PowerShell](service-bus-powershell-how-to-provision.md)
- [Gérer les ressources de Service Bus avec l’Explorateur de Bus de Service](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Création de modèles du Gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
  [Modèles d’Azure Quickstart]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Espace de noms de Bus de service avec la rubrique et d’abonnement]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
