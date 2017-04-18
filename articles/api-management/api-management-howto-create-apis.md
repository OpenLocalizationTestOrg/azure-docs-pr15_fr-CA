<properties 
    pageTitle="Comment créer des API dans la gestion des API Azure" 
    description="Découvrez comment créer et configurer des API dans Azure API de gestion." 
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

# <a name="how-to-create-apis-in-azure-api-management"></a>Comment créer des API dans la gestion des API Azure

Une API dans les API de gestion représente un ensemble d’opérations qui peuvent être appelés par les applications clientes. Nouvelles API est créés dans le portail de l’éditeur, et ensuite les opérations souhaitées sont ajoutées. Une fois que les opérations sont ajoutées, l’API est ajoutée à un produit et peut être publié. Une fois publiée, une API peut être abonné à et utilisée par les développeurs.

Ce guide présente la première étape dans le processus : comment faire pour créer et configurer une nouvelle API de gestion de l’API. Pour plus d’informations sur l’ajout d’opérations et de publication d’un produit, voir [Comment faire pour ajouter des opérations à une API][] et explique [comment créer et publier un produit][].

## <a name="create-new-api"> </a>Créer une nouvelle API

API est créés et configurés dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Ajouter des API**.

![API de création][api-management-create-api]

Utilisez la fenêtre **Ajouter une nouvelle API** pour configurer la nouvelle API.

![Ajouter la nouvelle API][api-management-add-new-api]

Les champs suivants sont utilisés pour configurer la nouvelle API.

-   **Nom de l’API de Web** fournit un nom unique et descriptif pour l’API. Il est affiché dans les portails de développeur et éditeur.
-   Fait référence à **l’URL du service Web** service HTTP de l’implémentation de l’API. Gestion de l’API transfère les demandes à cette adresse.
-   **Suffixe de l’URL de l’API Web** est ajouté à l’URL de base pour le service de gestion d’API. La base de l’URL est commun à toutes les API hébergées par une instance de service de gestion de l’API. API de gestion distingue les API en leur suffixe et par conséquent le suffixe doit être unique pour chaque API pour un éditeur donné.
-   **Modèle d’URL d’API Web** détermine quels protocoles peuvent être utilisés pour accéder à l’API. HTTPs est spécifié par défaut.
-   Pour ajouter éventuellement cette nouvelle API pour un produit, cliquez sur la liste déroulante **produits (facultatifs)** et sélectionnez un produit. Cette étape peut être répétée plusieurs fois pour ajouter de l’API à plusieurs produits.

Une fois que les valeurs souhaitées sont configurés, cliquez sur **Enregistrer**. Une fois la nouvelle API est créée, la page Résumé de l’API est affichée dans le portail de publisher.

![Résumé de l’API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Paramètres de l’API de configuration

Vous pouvez utiliser l’onglet **paramètres** pour vérifier et modifier la configuration d’une API. **Nom de l’API de Web**, **URL du service Web**et **suffixe de l’URL de l’API Web** sont initialement définies lorsque l’API est créé et peut être modifié ici. **Description** fournit une description facultative et **schéma d’URL d’API Web** détermine quels protocoles peuvent être utilisés pour accéder à l’API.

![Paramètres de l’API][api-management-api-settings]

Pour configurer l’authentification de la passerelle pour le service back-end de l’API de mise en œuvre, sélectionnez l’onglet **sécurité** . La **informations d’identification de** liste déroulante peut servir à configurer l’authentification **HTTP de base** ou de **certificats clients** . Pour utiliser l’authentification de base HTTP, entrez simplement les informations d’identification de votre choix. Pour plus d’informations sur l’utilisation de l’authentification de certificat client, reportez-vous à la section [comment sécuriser les services back-end à l’aide de l’authentification par certificat de client dans la gestion des API Azure][].

L’onglet **sécurité** peut également être utilisé pour configurer l' **autorisation de l’utilisateur** à l’aide de OAuth 2.0. Pour plus d’informations, consultez [comment autoriser des comptes de développeur à l’aide de 2.0 OAuth dans Azure API de gestion][].

![Paramètres de l’authentification de base][api-management-api-settings-credentials]

Cliquez sur **Enregistrer** pour enregistrer les modifications que vous apportez aux paramètres de l’API.

## <a name="next-steps"> </a>Étapes suivantes

Une fois une API est créée et les paramètres configurés, les étapes suivantes consistent à ajouter les opérations à l’API, ajouter de l’API à un produit et le publier afin qu’il soit disponible pour les développeurs. Pour plus d’informations, consultez les articles suivants.

-   [Comment faire pour ajouter des opérations à une API][]
-   [Comment faire pour créer et publier un produit][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Comment faire pour ajouter des opérations à une API]: api-management-howto-add-operations.md
[Comment faire pour créer et publier un produit]: api-management-howto-add-products.md

[Mise en route de la gestion des API Azure]: api-management-get-started.md
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Comment sécuriser les services back-end à l’aide du client d’authentification de certificat dans la gestion des API Azure]: api-management-howto-mutual-certificates.md
[Comment autoriser des comptes de développeur à l’aide de OAuth 2.0 dans Azure API de gestion]: api-management-howto-oauth2.md