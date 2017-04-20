<properties
   pageTitle="À l’aide du connecteur de ressource Azure dans les applications de logique | Service d’application Microsoft Azure"
   description="Comment créer et configurer l’application Azure ressource connecteur ou API et l’utiliser dans une logique d’application dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Mise en route avec le connecteur de ressource Azure et l’ajouter à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu.

Le connecteur de ressource Azure permet de gérer facilement les ressources Azure dans votre application logique.

## <a name="create-the-azure-resource-connector"></a>Créer le connecteur de ressource Azure
Pour utiliser l’application Azure ressource connecteur API, vous devez d’abord créer une instance de celle-ci. Cela inline lors de la création d’une application logique ou en sélectionnant l’application Azure Resource Manager connecteur API à partir de Azure Marketplace.

Pour le configurer, vous devez vous définir d’une entité de Service avec des autorisations pour faire tout ce qu’il vous voulez faire dans Azure. Tous les appels sont effectués sur la part de cette entité de sécurité de Service que vous définissez. Ainsi, vous pouvez définir la portée du connecteur pour utiliser uniquement exactement ce que vous voulez faire et rien de plus.

David Ebbo a écrit [un billet excellent](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sur la manière de configurer ce paramètre. Suivez toutes les instructions et obtenir votre **ID de clients**, un **ID de Client** et un **code Secret**. Ces trois champs, ainsi que l' **ID d’abonnement**, sont ce qui sont requis pour configurer le connecteur.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>À l’aide du connecteur de ressource Azure dans le Concepteur d’applications logique
### <a name="trigger"></a>Déclencheur
Il existe deux déclencheurs qui sont pris en charge dans le connecteur :

Nom | Description
---- | -----------
Événement se produit. | Déclencheur lorsqu’un événement se produit à une ressource dans votre abonnement.
Métrique dépasse le seuil |  Déclencheur lorsqu’une mesure répond à un certain seuil.

### <a name="action"></a>Action

De même, vous pouvez fournir un grand nombre d’actions à l’intérieur de votre abonnement Azure :

Pour les **groupes de ressources** , que vous pouvez :

Nom | Description
---- | -----------
Liste des groupes de ressources | Répertorier tous les groupes de ressources dans l’abonnement.
Obtenir le groupe de ressources | Par son id, obtenir un groupe de ressources.
Créer le groupe de ressources | Créer ou mettre à jour un groupe de ressources.
Supprimer le groupe de ressources | Supprimer un groupe de ressources.

Pour les **ressources** , vous pouvez :

Nom | Description
---- | -----------
Liste des ressources | Liste des ressources de votre abonnement par différents types de filtres.
Obtenir des ressources | Obtenir une seule ressource par son ID de ressource
Créer ou mettre à jour les ressources | Créez une ressource, ou mettre à jour d’une ressource existante. Vous devez fournir toutes les propriétés de cette ressource.
Action de ressource |  Effectuer toute autre action sur une ressource. Vous devez connaître le nom de l’action et la charge utile que cette action prend (le cas échéant).
Supprimer la ressource | Supprimer une ressource.

Pour les **Fournisseurs de ressources** , vous pouvez :

Nom | Description
---- | -----------
Fournisseurs de ressources de liste | Liste de tous les fournisseurs de ressources disponibles dans l’abonnement.

Pour les **Déploiements de groupe de ressources** , vous pouvez :

Nom | Description
---- | -----------
Déploiements de liste | Liste de tous les déploiements dans un groupe de ressources.
Obtenir le déploiement | Par son id, obtenir un déploiement du modèle.
Créer le déploiement | Créer un nouveau déploiement de groupe de ressource en fournissant un modèle.

Pour les **événements** sur les ressources, vous pouvez :

Nom | Description
---- | -----------
Obtenir des événements | Obtenir des événements dans un abonnement ou d’une ressource.

Pour les **mesures** sur les ressources, vous pouvez :

Nom | Description
---- | -----------
Obtenir des métriques | Obtenir une mesure d’une ressource de code.

## <a name="do-more-with-your-connector"></a>Faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un flux d’activité à l’aide d’une application de logique. Consultez [Quelles sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous souhaitez démarrer avec les applications Azure logique avant l’ouverture d’un compte Azure, accédez à [essayez de logique d’application](https://tryappservice.azure.com/?appservice=logic), où vous pouvez créer une application de logique de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

Afficher la référence de l’API de REST Swagger à [les connecteurs et les API applications référence](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md
