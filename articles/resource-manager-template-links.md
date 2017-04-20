<properties
   pageTitle="Modèle de gestionnaire de ressources pour les liaisons de ressources | Microsoft Azure"
   description="Affiche le schéma du Gestionnaire de ressources pour le déploiement des liens entre les ressources associées à un modèle."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>Schéma de modèle de liens ressources

Crée un lien entre deux ressources. Le lien est appliqué à une ressource connue comme la ressource de code source. La deuxième ressource dans le lien est appelée la ressource cible.

## <a name="schema-format"></a>Format de schéma

Pour créer un lien, ajoutez le schéma suivant à la section de ressources de votre modèle.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- |
| type de | Enum<br />Obligatoire<br />**{namespace} / {type} / fournisseurs/liens**<br /><br />Le type de ressource à créer. Le {namespace} et {type} valeurs font référence au type d’espace de noms et de la ressource fournisseur de la ressource de code source. |
| apiVersion | Enum<br />Obligatoire<br />**01 / 01/2015**<br /><br />La version de l’API à utiliser pour la création de la ressource. |  
| nom | Chaîne<br />Obligatoire<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> jusqu'à 64 caractères et ne peut pas contenir <>, %, &, ?, ou les caractères de contrôle.<br /><br />Valeur qui spécifie le nom de ressource de source et un nom pour le lien. |
| dependsOn | Tableau<br />Facultatif<br />Une liste séparée par des virgules, d’une ressource de noms ou des identificateurs de ressource uniques.<br /><br />La collection de ressources que dépend de ce lien. Si les ressources de la liaison sont déployés dans le même modèle, inclure les noms de ressources dans cet élément afin de garantir qu’ils sont déployés en premier. | 
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Qui identifie la ressource à lier à un objet et des remarques sur le lien. |  

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ------- | ---- |
| targetId | Chaîne<br />Obligatoire<br />**{l’id de ressource}**<br /><br />Identificateur de la ressource cible à lier. |
| Notes | Chaîne<br />Facultatif<br />jusqu'à 512 caractères<br /><br />Description du verrou. |


## <a name="how-to-use-the-link-resource"></a>L’utilisation de la ressource de lien

Vous appliquez un lien entre deux ressources lorsque les ressources ont une dépendance qui se poursuit après le déploiement. Par exemple, une application peut se connecter à une base de données dans un groupe de ressources différent. Vous pouvez définir cette dépendance en créant un lien à partir de l’application à la base de données. Les liens vous permettent de vous permet de documenter la relation entre deux ressources. Par la suite, vous ou quelqu'un de votre organisation peut demander à une ressource pour les liens découvrir comment la ressource fonctionne avec d’autres ressources.

Toutes les ressources liées doivent appartenir à la même abonnement. Chaque ressource peut être lié qu’à 50 autres ressources. Si les ressources liées sont supprimés ou déplacés, le propriétaire du lien doit nettoyer le lien restant.

Pour travailler avec des liaisons restantes, consultez [Les ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Utilisez la commande Azure PowerShell suivante pour voir tous les liens de votre abonnement. Vous pouvez fournir d’autres paramètres pour limiter les résultats.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Exemples

L’exemple suivant applique un verrou en lecture seule à une application web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Les modèles suivants de démarrage rapide pour déploiement des ressources avec un lien.

- [Alerte de la file d’attente de l’application de la logique](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Alerte de marge avec application de la logique](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Mettre en service une application API avec une passerelle existante](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Mettre en service une application API avec une nouvelle passerelle](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Créer une application de logique d’application et des API à l’aide d’un modèle](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Application de logique qui envoie un message texte au déclenchement d’une alerte](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la structure du modèle, consultez [Création d’un gestionnaire de ressources Azure modèles](resource-group-authoring-templates.md).
