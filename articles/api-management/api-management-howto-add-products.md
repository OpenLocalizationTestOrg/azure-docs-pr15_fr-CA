<properties 
    pageTitle="Comment faire pour créer et publier un produit dans la gestion des API Azure" 
    description="Apprenez à créer et publier des produits dans la gestion des API Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Comment faire pour créer et publier un produit dans la gestion des API Azure

Dans Gestion de l’API Azure, un produit contient un ou plusieurs des API comme un quota d’utilisation et les conditions d’utilisation. Une fois qu’un produit est publié, les développeurs peuvent s’abonner au produit et commencer à utiliser les API de produits. La rubrique fournit un guide pour la création d’un produit, l’ajout d’une API et la publication pour les développeurs.

## <a name="create-product"> </a>Créer un produit

Les opérations sont ajoutées et configurées à une API dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Dans le menu de gauche pour afficher la page **produits** , cliquez sur **produits** , puis cliquez sur **Ajouter un produit**.

![Produits][api-management-products]

![Nouveau produit][api-management-add-new-product]

Dans le champ **nom** et une description du produit dans le champ **Description** , entrez un nom descriptif pour le produit.

Les produits de gestion de l’API peuvent être **ouverte** ou **protégé**. Produits protégés doivent être souscrite avant de pouvoir être utilisés, lors de l’ouverture produits peuvent être utilisés sans abonnement. Vérifiez **l’abonnement** pour créer un produit protégé qui requiert un abonnement. Il s’agit du paramètre par défaut.

Cochez la case **Exiger l’approbation de l’abonnement** administrateur pour réviser et accepter ou refuser des tentatives de l’abonnement à ce produit. Si la case est désactivée, les tentatives d’abonnement seront acceptés automatiquement. Pour plus d’informations sur les abonnements, voir [Afficher les abonnés à un produit][].

Pour autoriser les comptes développeur s’abonner plusieurs fois pour le produit, cochez la case **Autoriser plusieurs abonnements** . Si cette case n’est pas cochée, chaque compte de développeur peut s’abonner qu’une seule fois pour le produit.

![Illimité plusieurs abonnements][api-management-unlimited-multiple-subscriptions]

Pour limiter le nombre d’abonnements simultanées multiples, la case à cocher **limiter le nombre d’abonnements simultanées à** et entrez la limite de l’abonnement. Dans l’exemple suivant, les abonnements simultanées sont limités à quatre par compte de développeur.

![Quatre plusieurs abonnements][api-management-four-multiple-subscriptions]

Une fois que toutes les nouvelles options de produit sont configurées, cliquez sur **Enregistrer** pour créer le nouveau produit.

![Produits][api-management-products-page]

>Par défaut, nouveaux produits non publiés et sont visibles uniquement au groupe **administrateurs** .

Pour configurer un produit, cliquez sur le nom du produit dans l’onglet **produits** .

## <a name="add-apis"> </a>Ajouter des API à un produit

La page **produits** contient quatre liens de configuration : **Résumé**, **paramètres**, **visibilité**et **les abonnés**. L’onglet **Résumé** est où vous pouvez ajouter des API et publier ou annuler la publication d’un produit.

![Résumé][api-management-new-product-summary]

Avant de publier votre produit, vous devez ajouter un ou plusieurs des API. Pour ce faire, cliquez sur **Ajouter une API pour le produit**.

![Ajouter des API][api-management-add-apis-to-product]

Les API de votre choix et cliquez sur **Enregistrer**.

## <a name="add-description"> </a>Ajouter des informations descriptives à un produit

L’onglet **paramètres** vous permet de fournir des informations détaillées sur le produit tel que son objectif, il fournit l’accès aux API et d’autres informations utiles. Le contenu est ciblé sur les développeurs qui en appelant l’API et peuvent être écrites en format texte brut ou un balisage HTML.

![Paramètres de produit][api-management-product-settings]

**Abonnement** pour créer un produit protégé qui requiert un abonnement à utiliser, ou désactivez la case à cocher pour créer un produit en cours qui peut être appelé sans abonnement.

Si vous souhaitez approuver manuellement toutes les demandes d’inscription de produit, sélectionnez **Exiger l’approbation de l’abonnement** . Par défaut, tous les abonnements de produit sont accordées automatiquement.

Pour autoriser les comptes développeur s’abonner plusieurs fois pour le produit, la case à cocher **Autoriser plusieurs abonnements** et éventuellement spécifier une limite. Si cette case n’est pas cochée, chaque compte de développeur peut s’abonner qu’une seule fois pour le produit.

Vous pouvez également remplir le champ de **conditions d’utilisation** décrivant les conditions d’utilisation du produit, les abonnés doivent accepter pour pouvoir utiliser le produit.

## <a name="publish-product"> </a>Publie un produit

Avant que les API dans un produit peuvent être appelées, le produit doit être publié. Sous l’onglet **Résumé** pour le produit, cliquez sur **Publier**, puis cliquez sur **Oui, publiez-le** à confirmer. Pour rendre un produit précédemment publié privé, cliquez sur **Annuler la publication**.

![Publier le produit][api-management-publish-product]

## <a name="make-visible"> </a>Afficher un produit pour les développeurs

L’onglet **visibilité** vous permet de choisir les rôles sont en mesure de voir le produit sur le portail des développeurs et de s’abonner au produit.

![Visibilité du produit][api-management-product-visiblity]

Pour activer ou désactiver la visibilité d’un produit pour les développeurs dans un groupe, ou désactivez la case à cocher à côté du groupe et cliquez sur **Enregistrer**.

>Pour plus d’informations, consultez [comment créer et utiliser des groupes pour gérer les comptes de développeur dans Azure API de gestion][].

## <a name="view-subscribers"> </a>Afficher les abonnés à un produit

L’onglet **abonnés** répertorie les développeurs qui ont souscrit au produit. Les détails et les paramètres pour chaque développeur peuvent être affichées en cliquant sur nom du développeur. Dans cet exemple, aucuns les développeurs n’ont encore abonnés au produit.

![Développeurs][api-management-developer-list]

## <a name="next-steps"> </a>Étapes suivantes

Une fois que l’API de votre choix sont ajoutés et le produit est publié, les développeurs peuvent s’abonner au produit et commencer à appeler les API. Pour consulter le didacticiel décrivant ces éléments ainsi que la configuration du produit, consultez [comment créer et configurer les paramètres de produit dans la gestion des API Azure][].

Pour plus d’informations sur l’utilisation des produits, consultez la vidéo suivante.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Abonnés d’affichage à un produit]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Mise en route de la gestion des API Azure]: api-management-get-started.md
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Comment créer et utiliser des groupes pour gérer les comptes de développeur dans Azure API de gestion]: api-management-howto-create-groups.md
[Comment créer et configurer les paramètres avancés de produit dans Azure API de gestion]: api-management-howto-product-with-rules.md 