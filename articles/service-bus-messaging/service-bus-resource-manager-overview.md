<properties
    pageTitle="Créer des ressources de Bus des services à l’aide des modèles du Gestionnaire de ressources Azure | Microsoft Azure"
    description="Utiliser des modèles d’Azure Resource Manager pour automatiser la création des ressources du Bus des services"
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
    ms.author="sethm"/>

# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Créer des ressources de Bus des services à l’aide des modèles du Gestionnaire de ressources Azure

Cet article décrit comment créer et déployer des ressources de Service Bus et concentrateurs d’événement à l’aide de modèles, PowerShell et le fournisseur de ressources de Bus des services Azure le Gestionnaire de ressources.

Azure modèles du Gestionnaire de ressources vous aident à définir les ressources pour déployer une solution et pour spécifier les paramètres et les variables qui permettent d’entrer des valeurs pour les différents environnements. Le modèle est composé de JSON et des expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. Pour plus d’informations sur l’écriture de modèles du Gestionnaire de ressources Azure et une description du format de modèle, consultez [Création d’un gestionnaire de ressources Azure modèles](../resource-group-authoring-templates.md). 

>[AZURE.NOTE] Les exemples dans cet article montrent comment utiliser le Gestionnaire de ressources Azure pour créer un espace de noms de Service Bus et l’entité messagerie (file d’attente). Pour d’autres exemples de modèle, visitez la [Galerie de modèles de démarrage rapide d’Azure][] et rechercher le « Bus de Service ».

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Modèles de service Bus et le Gestionnaire de ressources événement concentrateurs

Ces modèles de Bus de Service et Gestionnaire de ressources d’événement concentrateurs Azure sont disponibles pour téléchargement et déploiement. Cliquez sur les liens suivants pour plus d’informations sur chacune d’elles, avec des liens vers les modèles sur GitHub : 

- [Créer un espace de noms du Bus des services](service-bus-resource-manager-namespace.md)
- [Créer un espace de noms du Bus de Service avec la file d’attente](service-bus-resource-manager-namespace-queue.md)
- [Créer un espace de noms du Bus de Service avec la rubrique et d’abonnement](service-bus-resource-manager-namespace-topic.md)
- [Créer un espace de noms du Bus de Service avec la règle d’autorisation et de file d’attente](service-bus-resource-manager-namespace-auth-rule.md)
- [Créer un espace de noms de concentrateurs d’événement avec un groupe d’événements Hub et le consommateur](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

La procédure suivante décrit comment utiliser PowerShell pour déployer un modèle de gestionnaire de ressources Azure qui crée un espace de noms du Service Bus couche **Standard** et une file d’attente au sein de cet espace de noms. Cet exemple est basé sur le modèle de [créer un espace de noms du Bus de Service avec la file d’attente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . Le flux de travail approximative est la suivante :

1. Installer PowerShell.
2. Créer le modèle et (éventuellement) d’un fichier de paramètres.
2. Dans PowerShell, connectez-vous à votre compte Azure.
3. Créer un nouveau groupe de ressources s’il n’existe pas.
4. Tester le déploiement.
5. Si vous le souhaitez, définir le mode de déploiement.
6. Déployez le modèle.

Pour des informations complètes sur le déploiement des modèles de Azure le Gestionnaire de ressources, voir [ressources de déploiement avec le Gestionnaire de ressources Azure modèles][].

### <a name="install-powershell"></a>Installer PowerShell

Installer PowerShell d’Azure en suivant les instructions de la [procédure pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

### <a name="create-a-template"></a>Créer un modèle

Cloner ou copier le modèle [201-servicebus-créer-file d’attente](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) à partir de GitHub :

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Créer un fichier de paramètres (facultatif)

Pour utiliser un fichier de paramètres facultatifs, copiez le fichier [201-servicebus-créer-file d’attente](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Remplacez la valeur de `serviceBusNamespaceName` par le nom de l’espace de noms Bus de Service que vous souhaitez créer dans ce déploiement et remplacez la valeur de `serviceBusQueueName` par le nom de la file d’attente à laquelle vous souhaitez créer. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Pour plus d’informations, consultez la rubrique du [fichier de paramètres](../resource-group-template-deploy.md#parameter-file) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Ouvrez une session Azure et définir l’abonnement Azure

À partir d’une invite de commandes PowerShell, exécutez la commande suivante :

```
Login-AzureRmAccount
```

Vous êtes invité à ouvrir une session sur votre compte Azure. Après l’ouverture de session, exécutez la commande suivante pour afficher vos abonnements disponibles.

```
Get-AzureRMSubscription
```

Cette commande renvoie la liste des abonnements d’Azure disponibles. Choisissez un abonnement pour la session en cours en exécutant la commande suivante. Remplacer `<YourSubscriptionId>` avec le GUID pour l’abonnement Azure que vous souhaitez utiliser.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Définir le groupe de ressources

Si vous ne disposez pas d’un groupe de ressources existant, créez un nouveau groupe de ressources avec la commande **New-AzureRmResourceGroup** . Indiquez le nom du groupe de ressources et de l’emplacement que vous souhaitez utiliser. Par exemple :

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Si l’opération réussit, un résumé du nouveau groupe de ressource s’affiche.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Tester le déploiement

Validez votre déploiement en exécutant le `Test-AzureRmResourceGroupDeployment` applet de commande. Lorsque vous testez le déploiement, fournir des paramètres exactement comme vous le feriez lors de l’exécution du déploiement.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Créer le déploiement

Pour créer le nouveau déploiement, exécutez le `New-AzureRmResourceGroupDeployment` de commande et fournir les paramètres nécessaires lorsque vous y êtes invité. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources et le chemin d’accès ou l’URL vers le fichier de modèle. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut de **incrémentielle** est utilisée. Pour plus d’informations, reportez-vous à la section [incrémentielles et déploiements complètes](../resource-group-template-deploy.md#incremental-and-complete-deployments).

La commande suivante vous invite pour les trois paramètres dans la fenêtre PowerShell :

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Pour spécifier un fichier de paramètres à la place, utilisez la commande suivante.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Vous pouvez également utiliser les paramètres en ligne lorsque vous exécutez l’applet de commande de déploiement. La commande est la suivante :

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Pour exécuter un déploiement [complet](../resource-group-template-deploy.md#incremental-and-complete-deployments) , définissez le paramètre de **Mode** **terminé**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Vérifier le déploiement

Si les ressources sont correctement déployés, un résumé du déploiement s’affiche dans la fenêtre PowerShell :

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant vu les commandes pour le déploiement d’un modèle de gestionnaire de ressources Azure et le flux de travail. Pour plus d’informations, consultez les liens suivants :

- [Vue d’ensemble de Gestionnaire des ressources Azure][]
- [Déployer les ressources avec le Gestionnaire de ressources Azure modèles][]
- [Création de modèles](../resource-group-authoring-templates.md)


[Vue d’ensemble de Gestionnaire des ressources Azure]: ../resource-group-overview.md
[Déployer les ressources avec le Gestionnaire de ressources Azure modèles]: ../resource-group-template-deploy.md
[Galerie de modèles de Quickstart Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus