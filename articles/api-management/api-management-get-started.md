<properties
    pageTitle="Gérer votre première API dans la gestion des API Azure | Microsoft Azure"
    description="Apprenez à créer des API et ajouter des opérations de mise en route de la gestion de l’API."
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
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="manage-your-first-api-in-azure-api-management"></a>Gérer votre première API dans la gestion des API Azure

## <a name="overview"> </a>Vue d’ensemble

Ce guide vous montre comment rapidement mise en route à l’aide de la gestion des API Azure et effectuer votre premier appel d’API.

## <a name="concepts"> </a>Quoi consiste la gestion des API Azure ?

Vous pouvez utiliser Gestion des API Azure de prendre un back-end et de lancer un programme à part entière de API basé sur ce.

Les scénarios courants incluent :

* Les attaques d' **infrastructure mobile de sécurisation** par déclenchement d’accès avec des clés de l’API, prévention du déni de service à l’aide de la limitation, ou à l’aide de stratégies de sécurité avancées comme validation de jeton JWT.
* **Écosystèmes de partenaires ISV de l’activation** en offrant une intégration rapide de partenaire via le portail développeur et en créant une façade de l’API de dissocier les implémentations internes qui ne sont pas de la consommation du partenaire.
* **Exécution d’un programme API interne** en offrant un emplacement centralisé pour l’organisation de communiquer sur la disponibilité et les dernières modifications apportées à l’API, de passerelles d’accès basées sur les comptes de l’organisation, basé sur un canal sécurisé entre le serveur principal et de la passerelle API.


Le système est constitué des composants suivants :

* La **passerelle API** est le point de terminaison qui :
  * Accepte les API appelle et les achemine vers votre les serveurs principaux.
  * Vérifie les API clés JWT jetons, certificats et autres informations d’identification.
  * Applique les quotas d’utilisation et d’évaluer les limites.
  * Transforme votre API à la volée sans modification de code.
  * Met en cache les réponses de back-end dans lequel paramétrer.
  * Journaux d’appels métadonnées à des fins d’analytique.

* Le **portail de publisher** est l’interface d’administration où vous configurez votre programme de l’API. Utiliser pour :
    * Définir ou importer un schéma de l’API.
    * Package API dans les produits.
    * Définir des stratégies telles que les quotas ou les transformations sur les API.
    * Obtenir des informations à partir d’analytique.
    * Gérer les utilisateurs.

* Le **portail des développeurs** sert de la présence du site web principal pour les développeurs, où ils peuvent :
    * Documentation sur l’API de lecture.
    * Essayez une API via la console interactive.
    * Créer un compte et vous abonner pour obtenir les clés de l’API.
    * Analytique de l’accès à leur propre utilisation.


## <a name="create-service-instance"> </a>De créer une instance de gestion de l’API

>[AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure][].

La première étape dans l’utilisation des API de gestion consiste à créer une instance de service. Connectez-vous au [Portail classique d’Azure][] et cliquez sur **Nouveau**, **Services de l’application**, **Gestion de l’API**, **créer**.

![Nouvelle instance de gestion de l’API][api-management-create-instance-menu]

Pour l' **URL**, spécifiez un nom de sous-domaine unique à utiliser pour l’URL du service.

Cliquez sur **l’abonnement** et la **région** souhaitée pour votre instance de service. Après avoir effectué vos sélections, cliquez sur le bouton **suivant** .

![Nouveau service de gestion de l’API][api-management-create-instance-step1]

Entrez **Contoso Ltd.** pour le **Nom de l’organisation**, puis entrez votre adresse e-mail dans le champ **E-Mail de l’administrateur** .

>[AZURE.NOTE] Cette adresse e-mail est utilisée pour les notifications du système de gestion de l’API. Pour plus d’informations, consultez [comment configurer les notifications et les modèles d’e-mail dans Gestion des API Azure][].

![Nouveau service de gestion de l’API][api-management-create-instance-step2]

Les instances de service de gestion de l’API sont disponibles en trois versions : Developer, Standard et Premium. Par défaut, les nouvelles instances de service de gestion de l’API sont créés dans la couche de développement. Pour sélectionner la couche Standard ou Premium, case à cocher **Paramètres avancés** et sélectionnez le niveau souhaité sur l’écran suivant.

>[AZURE.NOTE] La couche de développement est de développement, de test et les programmes pilotes API où la haute disponibilité n’est pas un problème. Dans les couches Standard et Premium, vous pouvez mettre à l’échelle le nombre d’unités réservées pour gérer davantage de trafic. Les couches Standard et Premium fournissent à votre service de gestion de l’API avec le plus de puissance de traitement et les performances. Vous pouvez suivre ce didacticiel à l’aide de n’importe quel niveau. Pour plus d’informations sur les niveaux de gestion de l’API, voir [gestion de l’API de tarification][].

Cliquez sur la case à cocher pour créer l’instance de service.

![Nouveau service de gestion de l’API][api-management-instance-created]

Une fois que l’instance de service est créé, l’étape suivante consiste à créer ou importer une API.

## <a name="create-api"> </a>Importer une API

Une API se compose d’un ensemble d’opérations qui peuvent être appelées à partir d’une application cliente. Opérations de l’API sont transmises aux services web existants.

API peut être créés (et les opérations peuvent être ajoutées) manuellement, ou ils peuvent être importés. Dans ce didacticiel, nous allons importer l’API pour un service web de calculatrice exemple fourni par Microsoft et hébergée sur Azure.

>[AZURE.NOTE] Pour obtenir des instructions sur la création d’une API et l’ajout manuel des opérations, consultez [comment créer des API](api-management-howto-create-apis.md) et [Ajouter des opérations à une API](api-management-howto-add-operations.md).

API est configurés à partir du portail de publisher, qui est accessible via le portail classique d’Azure. Pour atteindre le portail de publisher, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

Pour importer la API de la Calculatrice, cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Importer les API**.

![Bouton Importer API][api-management-import-api]

Effectuez les étapes suivantes pour configurer la calculatrice API :

1. Cliquez sur **L’URL à partir de**, entrez **http://calcapi.cloudapp.net/calcapi.json** dans la zone de texte **URL du document spécification** et cliquez sur le bouton d’option **Swagger** .
2. Dans la zone de texte **suffixe d’URL d’API Web** , tapez **calc** .
3. Cliquez dans la zone **produits (facultatifs)** et cliquez sur **démarrage**.
4. Cliquez sur **Enregistrer** pour importer l’API.

![Ajouter la nouvelle API][api-management-import-new-api]

>[AZURE.NOTE] **API de gestion** prend actuellement en charge la version 1.2 et 2.0 de Swagger document d’importation. Veillez à ce que, même si la [spécification 2.0 de Swagger](http://swagger.io/specification) déclare que `host`, `basePath`, et `schemes` propriétés sont facultatives, votre document 2.0 de Swagger **doit** contenir les propriétés ; sinon il ne sont pas importé. 

Une fois que l’API est importé, la page Résumé de l’API est affichée dans le portail de publisher.

![Résumé de l’API][api-management-imported-api-summary]

La section API comporte plusieurs onglets. L’onglet **Résumé** affiche les mesures de base et des informations sur l’API. L’onglet [paramètres](api-management-howto-create-apis.md#configure-api-settings) est utilisé pour afficher et modifier la configuration d’une API. L’onglet [opérations](api-management-howto-add-operations.md) permet de gérer les opérations de l’API. L’onglet **sécurité** peut être utilisé pour configurer l’authentification de la passerelle pour le serveur principal à l’aide de l’authentification de base ou [l’authentification de certificat mutual](api-management-howto-mutual-certificates.md)et de configurer [l’autorisation de l’utilisateur à l’aide de OAuth 2.0](api-management-howto-oauth2.md).  L’onglet **problèmes** est utilisé pour afficher les problèmes signalés par les développeurs qui utilisent votre API. L’onglet **produits** permet de configurer les produits qui contiennent cette API.

Par défaut, chaque instance de gestion de l’API est livré avec deux exemples de produits :

-   **Starter**
-   **Un nombre illimité**

Dans ce didacticiel, l’API de calculatrice de base a été ajouté au produit Starter lorsque l’API a été importé.

Pour effectuer des appels à une API, les développeurs Abonnez-vous d’abord à un produit qui lui permet d’accéder à celui-ci. Les développeurs peuvent s’abonner aux produits dans le portail des développeurs ou administrateurs peuvent s’abonner aux développeurs de produits dans le portail de publisher. Vous êtes un administrateur depuis la création de l’instance de gestion de l’API dans les étapes précédentes dans le didacticiel, afin que vous êtes déjà abonné à chaque produit par défaut.

## <a name="call-operation"> </a>Appeler une opération à partir du portail de développement

Les opérations peuvent être appelées directement à partir du portail de développement, ce qui offre un moyen pratique d’afficher et de tester les opérations d’une API. Dans cette étape du didacticiel, vous allez appeler opération **ajouter deux entiers** de l’API calculatrice de base. Cliquez sur **portail destiné aux développeurs** dans le menu situé en haut à droite du portail publisher.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis cliquez sur **Calculatrice de base** pour afficher les opérations disponibles.

![Portail des développeurs][api-management-developer-portal-calc-api]

Notez les paramètres qui ont été importés avec les API et les opérations, en fournissant une documentation pour les développeurs qui utiliseront cette opération et les descriptions d’exemple. Ces descriptions peuvent également être ajoutées lorsque des opérations sont ajoutées manuellement.

Pour appeler l’opération **ajouter deux entiers** , cliquez sur **essayer**.

![Essaie][api-management-developer-portal-calc-api-console]

Vous pouvez entrer des valeurs pour les paramètres ou conserver les valeurs par défaut, puis cliquez sur **Envoyer**.

![HTTP Get][api-management-invoke-get]

Une fois qu’une opération est appelée, le portail de développement affiche tout le **contenu de la réponse**, le **statut de la réponse**et les **en-têtes de réponse**.

![Réponse][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Afficher analytique

Pour afficher les analytique de calculatrice de base, basculez vers le portail de publisher en sélectionnant **Gérer** dans le menu en haut à droite du portail développeur.

![Gérer les][api-management-manage-menu]

La vue par défaut pour le portail de l’éditeur est le **tableau de bord**, qui fournit une vue d’ensemble de votre instance de gestion de l’API.

![Tableau de bord][api-management-dashboard]

Placez la souris sur le graphique de **Calculatrice de base** afficher les mesures spécifiques pour l’utilisation de l’API pour une période donnée.

>[AZURE.NOTE] Si vous ne voyez pas toutes les lignes de votre graphique, basculer vers le portail de développement et effectuer des appels à l’API, patientez quelques instants et puis revenez au tableau de bord.

Cliquez sur **Afficher les détails** pour afficher la page Résumé de l’API, y compris une version plus grande de la métrique affichée.

![Analytique][api-management-mouse-over]

![Résumé][api-management-api-summary-metrics]

Pour des mesures détaillées et des rapports, cliquez sur **Analytique** à partir du menu de **Gestion de l’API** sur la gauche.

![Vue d’ensemble][api-management-analytics-overview]

La section **Analytique** comporte les quatre onglets suivants :

-   Fournit des métriques d’utilisation et de la santé globale, ainsi que les meilleurs développeurs, meilleurs produits, API supérieur et opérations supérieure **en un clin de œil** .
-   **Utilisation** offre une présentation détaillée à des appels d’API et de la bande passante, notamment une représentation géographique.
-   Vues de **la santé** sur les codes d’état, taux de réussite, les temps de réponse et les API de cache et des temps de réponse du service.
-   **Activité** fournit des rapports de zoom avant sur l’activité spécifique par le développeur, produit, API et opération.

## <a name="next-steps"> </a>Étapes suivantes

- Découvrez comment [protéger votre API avec les taux limites](api-management-howto-product-with-rules.md).

[Version d’essai Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Comment faire pour configurer des notifications et des modèles d’e-mail dans Azure API de gestion]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Prix de gestion de l’API]: http://azure.microsoft.com/pricing/details/api-management/

[Azure Portal classique]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
