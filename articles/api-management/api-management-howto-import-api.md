<properties 
    pageTitle="Concepts clés de la gestion de l’API" 
    description="Obtenir des informations sur les API, les produits, les rôles, les groupes et les autres concepts clés de gestion de l’API." 
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

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Comment faire pour importer la définition d’une API avec Azure API de gestion des opérations

API de gestion des nouvelles API peut être créés et les opérations ajoutées manuellement, ou l’API peut être importés avec les opérations en une seule étape.

API et leurs opérations peuvent être importées à l’aide des formats suivants.

-   WADL
-   Swagger

Ce guide montre comment créer une nouvelle API et de ses opérations en une seule étape d’importation. Pour plus d’informations sur la création manuelle d’une API et l’ajout d’opérations, consultez [comment créer des API][] et [Ajouter des opérations à une API][].

## <a name="import-api"> </a>Importer une API

API est créés et configurés dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

![Portail de Publisher][api-management-management-console]

Cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Importer des API**.

![API d’importation][api-management-import-apis]

La fenêtre de **l’API d’importation** comporte trois onglets qui correspondent aux trois façons pour fournir la spécification de l’API.

-   **À partir du Presse-papiers** vous permet de coller la spécification API dans la zone de texte désigné.
-   **À partir du fichier** vous permet de rechercher et sélectionner le fichier qui contient la spécification de l’API.
-   **À partir de l’URL** vous permet de fournir l’URL de la spécification de l’API.

![Format d’importation API][api-management-import-api-clipboard]

Après avoir fourni la spécification API, utilisez les boutons radio sur la droite pour indiquer le format de spécification. Les formats suivants sont pris en charge.

-   WADL
-   Swagger

Ensuite, entrez un **suffixe d’URL de l’API Web**. Il est ajouté à l’URL de base pour votre service de gestion d’API. La base de l’URL est commun à toutes les API hébergées sur chaque instance d’un service de gestion de l’API. API de gestion distingue les API en leur suffixe et par conséquent le suffixe doit être unique pour chaque API dans une instance du service de gestion des API spécifique.

Une fois que toutes les valeurs sont entrées, cliquez sur **Enregistrer** pour créer l’API et les opérations associées. 

>[AZURE.NOTE] Pour voir un didacticiel de l’importation d’une API de calculatrice de base sous forme de Swagger, [Gérer votre première API dans Azure API de gestion](api-management-get-started.md).

## <a name="export-api"></a> Exporter une API

Outre l’importation de nouvelles API, vous pouvez exporter les définitions de votre API à partir du portail de publisher. Pour ce faire, cliquez sur **Exporter les API** à partir de l' **onglet Résumé** de votre **API**.

![Exportation des API][api-management-export-api]

API peut être exportés à l’aide de WADL ou Swagger. Sélectionnez le format souhaité et cliquez sur **Enregistrer**, sélectionnez l’emplacement dans lequel enregistrer le fichier.

![Format d’exportation API][api-management-export-api-format]

## <a name="next-steps"> </a>Étapes suivantes

Une fois une API est créée et les opérations importées, vous pouvez vérifier et configurer des paramètres supplémentaires, ajouter de l’API à un produit et le publier afin qu’il soit disponible pour les développeurs. Pour plus d’informations, consultez les guides suivants.

-   [Comment faire pour configurer les paramètres de l’API][]
-   [Comment faire pour créer et publier un produit][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Mise en route de la gestion des API Azure]: api-management-get-started.md
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[Comment faire pour ajouter des opérations à une API]: api-management-howto-add-operations.md
[Comment faire pour créer et publier un produit]: api-management-howto-add-products.md
[Comment créer des API]: api-management-howto-create-apis.md
[Comment faire pour configurer les paramètres de l’API]: api-management-howto-create-apis.md#configure-api-settings
