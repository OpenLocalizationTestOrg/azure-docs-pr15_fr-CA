<properties 
    pageTitle="Lier des ressources dans le Gestionnaire de ressources Azure | Microsoft Azure" 
    description="Créer un lien entre les ressources associées dans différents groupes de ressources dans le Gestionnaire de ressources Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>Lier des ressources dans le Gestionnaire de ressources Azure

Au cours du déploiement, vous pouvez marquer une ressource dépendante d’une autre ressource, mais ce cycle de vie se termine au moment du déploiement. Après le déploiement, il n’existe aucune relation identifiée entre les ressources dépendantes. Le Gestionnaire de ressources fournit une fonctionnalité appelée ressource de liaison pour établir des liens permanents entre les ressources.

Avec la liaison de la ressource, vous pouvez documenter les relations qui s’étendent sur des groupes de ressources. Par exemple, il est courant pour une base de données avec son propre cycle de vie résident dans un groupe de ressources, et d’une application avec un autre cycle de vie dans un groupe de ressources différent. L’application se connecte à la base de données afin que vous souhaitez marquer un lien entre l’application et la base de données. 

Toutes les ressources liées doivent appartenir à la même abonnement. Chaque ressource peut être lié qu’à 50 autres ressources. La seule manière d’interroger les ressources connexes est par le biais de l’API REST. Si les ressources liées sont supprimés ou déplacés, le propriétaire du lien doit nettoyer le lien restant. Vous êtes **pas** averti lorsque la suppression d’une ressource qui est liée à d’autres ressources.

## <a name="linking-in-templates"></a>La liaison dans les modèles

Pour définir une liaison dans un modèle, vous incluez un type de ressource qui associe l’espace de noms de fournisseur de ressources et le type de la ressource de code source avec **/providers/links**. Le nom doit inclure le nom de la ressource de code source. Vous fournissez l’id de ressource de la ressource cible. L’exemple suivant établit un lien entre un site web et un compte de stockage.

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


Pour une description complète du format de modèle, consultez [ressources liens - schéma de modèle](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>La liaison avec l’API REST

Pour définir un lien entre les ressources déployées, exécutez :

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Remplacez {id d’abonnement} avec votre id d’abonnement. Remplacez {-groupe de ressources}, {fournisseur-espace de noms, {-type de ressource} et {nom de ressource du} avec les valeurs qui identifient la première ressource dans le lien. Remplacez {nom du lien} par le nom du lien à créer. Utilisez 2015-01-01 pour la version de l’api.

Dans la demande, incluez un objet qui définit la deuxième ressource dans le lien :

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

L’élément properties contient l’identificateur pour la deuxième ressource.

Vous pouvez interroger des liens dans votre abonnement à :

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Pour plus d’exemples, y compris comment récupérer des informations sur les liaisons, consultez [Les ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez également organiser vos ressources avec des balises. Pour en savoir plus sur les ressources de marquage, reportez-vous à la section [en utilisant des balises pour organiser vos ressources](resource-group-using-tags.md).
- Pour une description de la création de modèles et de définir les ressources à déployer, consultez [modèles de création](resource-group-authoring-templates.md).
