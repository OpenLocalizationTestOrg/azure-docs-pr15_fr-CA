<properties 
    pageTitle="Comment faire pour ajouter des opérations à une API dans la gestion des API Azure | Microsoft Azure" 
    description="Apprenez à ajouter des opérations à une API dans la gestion des API Azure." 
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

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Comment faire pour ajouter des opérations à une API dans la gestion des API Azure

Avant de pouvoir utiliser une API de gestion de l’API, les opérations doivent être ajoutées. Ce guide explique comment ajouter et configurer les différents types d’opérations à une API dans les API de gestion.

## <a name="add-operation"> </a>Ajouter une opération

Les opérations sont ajoutées et configurées à une API dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Sélectionnez l’API souhaité dans le portail de l’éditeur, puis sélectionnez l’onglet **opérations** . 

![Opérations][api-management-operations]

Cliquez sur **Ajouter une opération** pour ajouter une nouvelle opération. **nouvelle opération** s’affichera et la **Signature** est activée par défaut.

![Opération d’ajout][api-management-add-operation]

Spécifier le **verbe HTTP** en choisissant dans la liste déroulante.

![Méthode HTTP][api-management-http-method]

<a name="url-template"></a>

Définissez le modèle de l’URL en tapant dans un fragment d’URL composé d’un ou plusieurs segments de chemin d’accès d’URL et de zéro ou plusieurs paramètres de chaîne de requête. Le modèle d’URL, ajouté à l’URL de base de l’API, identifie une seule opération de HTTP. Il peut contenir un ou plusieurs des parties variables qui sont identifiés par des accolades. Les éléments de variable sont appelées paramètres de modèle et sont attribuées dynamiquement les valeurs extraites à partir de l’URL de la demande lorsque la demande est traitée par la plate-forme de gestion de l’API.

![Modèle d’URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Si vous le souhaitez, spécifiez le **modèle de réécrire les URL**. Cela vous permet d’utiliser le modèle d’URL standard pour le traitement des demandes entrantes sur le serveur frontal, lors de l’appel du back-end via une URL convertie en fonction du modèle de réécriture. Paramètres de modèle à partir du modèle de l’URL doivent être utilisés dans le modèle de réécriture. L’exemple suivant montre comment content type codé comme segment de chemin d’accès dans le service web à partir de l’exemple précédent peut être fournie comme paramètre de requête dans l’API publié via la plate-forme de gestion de l’API à utiliser les modèles d’URL.

![Réécriture d’URL modèle][api-management-url-template-rewrite]

Les appelants à l’opération utilise le format `/customers?customerid=ALFKI` et il sera mappé à `/Customers('ALFKI')` lorsque le service principal est appelé.


Nom **complet** et la **Description de** fournissent une description de l’opération et sont utilisés pour fournir une documentation pour les développeurs utilisant cette API dans le portail des développeurs.

![Description][api-management-description]

La description de l’opération peut être spécifiée en tant que texte brut ou HTML dans la zone de texte **Description** .

## <a name="operation-caching"> </a>La mise en cache de l’opération

Réponse mise en cache réduit la latence perçue par les consommateurs API, permet de réduire la consommation de bande passante et réduit la charge sur le web HTTP de service mise en œuvre de l’API. 

Pour activer la mise en cache pour l’opération plus rapidement et plus facilement, sélectionnez l’onglet **mise en cache** et cochez la case **Activer** .

![La mise en cache][api-management-caching-tab]

**Durée** spécifie la durée pendant laquelle la réponse de l’opération reste dans le cache. La valeur par défaut est 3 600 secondes ou 1 heure.

Clés de cache sont utilisés pour différencier les réponses afin que la réponse correspondant à chaque clé de cache différentes obtiendra sa propre valeur mise en cache séparé. Le cas échéant, entrez les paramètres de chaîne de requête spécifique et/ou les en-têtes HTTP à utiliser dans l’informatique respectivement les valeurs de clé de cache dans les zones de texte **variation par paramètres de chaîne de requête** et de la **variation par les en-têtes** . Lorsque aucun sont les URL de la requête spécifiée, complet et les valeurs d’en-tête HTTP suivantes sont utilisées dans la génération de clés de cache : **Accepter** et **Accept-Charset**.

>Pour plus d’informations sur la mise en cache et la mise en cache des stratégies, voir [comment les résultats des opérations de cache dans Gestion de l’API Azure][].


## <a name="request-parameters"> </a>Paramètres de la demande

Paramètres de l’opération sont gérés sous l’onglet Paramètres. Les paramètres spécifiés dans le **Modèle d’URL** dans l’onglet **Signature** sont ajoutés automatiquement et peuvent être modifiées qu’en modifiant le modèle d’URL. Des paramètres supplémentaires peuvent être entrés manuellement.

Pour ajouter un nouveau paramètre de requête, cliquez sur **Ajouter un paramètre de requête** , puis entrez les informations suivantes :

-   **Nom** - nom de paramètre.
-   **Description** : une brève description du paramètre (facultatif).
-   **Type** : type de paramètre, sélectionnée dans la liste déroulante vers le bas.
-   **Valeurs de** valeurs pouvant être assignée à ce paramètre. Une des valeurs peut être marquée comme valeur par défaut (facultative).
-   **Requis** - rendre le paramètre obligatoire en activant la case à cocher. 

![Paramètres de la demande][api-management-request-parameters]

## <a name="request-body"> </a>Corps de requête

Si l’opération (par exemple, PUT, POST) et nécessite un organisme peut donner un exemple de ce dernier dans tous les formats de représentation pris en charge (par exemple au format json, XML). 

>Le corps de la demande est utilisé uniquement à des fins de documentation et n’est pas validé.

Pour entrer un corps de demande, basculez vers l’onglet **corps** .

Cliquez sur **Ajouter une représentation**, commencez à taper le nom de type de contenu souhaité (par exemple application/json), sélectionnez-le dans la liste déroulante et collez l’exemple de corps de requête souhaitée dans le format sélectionné dans la zone de texte. 

![Corps de la demande][api-management-request-body]

En plus de représentations de, vous pouvez également spécifier une description facultative dans la zone de texte **Description** .

## <a name="responses"> </a>Les réponses

Il est conseillé de fournir des exemples de réponses pour tous les codes d’état que l’opération peut produire. Chaque code d’état peut avoir plus d’un exemple de corps de réponse, un pour chacun des types de contenu pris en charge. 

Pour ajouter une réponse, cliquez sur **Ajouter** et commencez à taper le code d’état de votre choix. Dans cet exemple, le code d’état est **200 OK**. Une fois que le code est affiché dans la liste déroulante, sélectionnez-la, et le code de réponse est créé et ajouté à votre opération.

![Code de réponse][api-management-response-code]

Cliquez sur **Ajouter une représentation**, commencez à taper le nom de type de contenu souhaité (par exemple application/json) et sélectionnez-le dans la liste déroulante vers le bas.

![Type de contenu de corps][api-management-response-body-content-type]

Collez l’exemple de corps de réponse dans le format sélectionné dans la zone de texte. 

![Corps de la réponse][api-management-response-body]

Si vous le souhaitez, ajouter une description facultative dans la zone de texte **Description** .

Une fois l’opération terminée, cliquez sur **Enregistrer**.


## <a name="next-steps"> </a>Étapes suivantes

Une fois que les opérations sont ajoutées à une API, l’étape suivante consiste à associer l’API avec un produit et de le publier pour que les développeurs peuvent appeler ses opérations.

-   [Comment faire pour créer et publier un produit][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Mise en route de la gestion des API Azure]: api-management-get-started.md
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Comment faire pour créer et publier un produit]: api-management-howto-add-products.md
[Comment les résultats des opérations de cache dans Gestion de l’API Azure]: api-management-howto-cache.md