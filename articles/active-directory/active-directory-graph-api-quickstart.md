<properties
   pageTitle="Démarrage rapide pour le graphique AD Azure API | Microsoft Aure"
   description="L’API de graphique Azure Active Directory fournit un accès par programmation à Azure AD via les points de terminaison OData reste API. Applications peuvent utiliser l’API de graphique pour effectuer créer, lire, mettre à jour et supprimer des opérations sur les objets et les données de l’annuaire."
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Démarrage rapide pour le graphique AD Azure API

L’API de graphique Azure Active Directory (AD) fournit un accès par programmation à Azure AD via les points de terminaison OData reste API. Applications peuvent utiliser l’API de graphique pour effectuer créer, lire, mettre à jour et supprimer des opérations sur les objets et les données de l’annuaire. Par exemple, vous pouvez utiliser l’API de graphique pour créer un nouvel utilisateur, afficher ou mettre à jour les propriétés de l’utilisateur, modifier le mot de passe de l’utilisateur, vérifier l’appartenance au groupe d’accès par rôle, désactiver ou supprimer l’utilisateur. Pour plus d’informations sur les fonctionnalités de l’API de graphique et les scénarios d’application, voir [API d’Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) et les [conditions préalables de API Azure AD graphique](https://msdn.microsoft.com/library/hh974476.aspx). 

> [AZURE.IMPORTANT] Azure AD graphique API fonctionnalité est également disponible par le biais de [Microsoft Graph](https://graph.microsoft.io/), une API unifiée qui inclut des API à partir d’autres services de Microsoft, comme Outlook, OneDrive, OneNote, planificateur et graphique Office, accessible via un point de terminaison unique et avec un jeton d’accès unique.

## <a name="how-to-construct-a-graph-api-url"></a>La construction d’une URL d’API de graphique

Dans l’API de graphique, pour accéder aux données d’annuaire et des objets (en d’autres termes, les ressources ou les entités) par rapport à laquelle vous souhaitez effectuer des opérations CRUD, vous pouvez utiliser des URL basées sur le protocole de données ouvertes (OData). Les URL utilisées dans les API de graphe se composent de quatre parties principales : racine, identificateur du locataire, chemin d’accès de la ressource et des options de chaîne de requête de service : `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Prenons l’exemple de l’URL suivante : `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Racine du service**: dans l’API de graphique Azure AD, la racine du service est toujours https://graph.windows.net.
- **Identificateur du locataire**: cela peut être un nom de domaine de (des) vérifiées, dans l’exemple ci-dessus, contoso.com. Il peut également être un ID d’objet locataire ou la « myorganiztion » ou « me » alias. Pour plus d’informations, reportez-vous à [adressage des entités et des opérations dans l’API de graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
- **Chemin d’accès de la ressource**: cette section de l’URL identifie la ressource pour exploiter (utilisateurs, groupes, un utilisateur donné, ou un groupe particulier, etc.) Dans l’exemple ci-dessus, il est « groupes de niveau supérieur » à l’adresse de la ressource définie. Vous pouvez également adresser une entité spécifique, par exemple « utilisateurs / {objectId} » ou « utilisateurs/userPrincipalName ».
- **Paramètres de la requête**: ? sépare la partie du chemin d’accès de ressource à partir de la section de paramètres de requête. Le paramètre de requête « api-version » est requis sur toutes les requêtes dans l’API de graphique. L’API de graphique prend également en charge les options de requête OData suivantes : **$filter**, **$orderby**, **$**, **$top**et **$format**. Les options de requête suivantes ne sont pas actuellement prises en charge : **$count** **$inlinecount**et **$skip**. Pour plus d’informations, consultez [prise en charge des requêtes, des filtres et des Options de pagination dans Azure AD graphique API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Graphique des versions de l’API

Vous spécifiez la version pour une demande d’API de graphe dans le paramètre de requête « api-version ». Pour la version 1.5 ou ultérieure, vous utilisez une valeur de version numérique ; version de l’API = 1,6. Pour les versions antérieures, vous utilisez une chaîne de date qui respecte le format AAAA-MM-JJ ; par exemple,-version de l’api = 2013-11-08. Pour les fonctions de visualisation, utilisez la chaîne « bêta » ; par exemple,-api version = version bêta. Pour plus d’informations sur les différences entre les versions de l’API de graphique, consultez [Versioning des API Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graphique d’API de métadonnées

Pour renvoyer le fichier de métadonnées de graphique API, ajouter le segment « $metadata » après l’identificateur de client dans l’URL par exemple, l’URL suivante retourne les métadonnées pour la société de démonstration utilisée par l’Explorateur graphique : `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Vous pouvez entrer cette URL dans la barre d’adresses d’un navigateur web pour afficher les métadonnées. Le document de métadonnées CSDL renvoyé décrit les entités et les types complexes, leurs propriétés et les fonctions actions exposées par la version de l’API graphique demandé. L’omission du paramètre de version de l’api retourne les métadonnées pour la version la plus récente.

## <a name="common-queries"></a>Requêtes communes

[API Azure AD graphique des requêtes communes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) répertorie les requêtes communes qui peuvent être utilisées avec le graphique de publicité Azure, y compris les requêtes qui peuvent être utilisés pour accéder aux ressources de niveau supérieur dans votre répertoire et les requêtes pour effectuer des opérations dans votre répertoire.

Par exemple, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` renvoie des informations de répertoire contoso.com de la société.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` répertorie tous les objets utilisateur dans le répertoire de contoso.com.

## <a name="using-the-graph-explorer"></a>À l’aide de l’Explorateur du graphique

Vous pouvez utiliser l’Explorateur de graphique pour l’API Azure AD graphique pour interroger les données de l’annuaire que vous générez votre application.

> [AZURE.IMPORTANT] Le graphique de l’Explorateur ne gère pas l’écriture ou de suppression des données à partir d’un répertoire. Vous ne pouvez effectuer que les opérations de lecture sur votre répertoire d’Azure AD avec l’Explorateur de graphique.

Voici la sortie que vous obtiendriez si vous deviez accéder à l’Explorateur de graphique, sélectionnez la société de démonstration d’utilisation et entrez `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` pour afficher tous les utilisateurs dans l’annuaire de démonstration :

![Explorateur de api Azure AD graphique](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Charger l’Explorateur graphique**: pour charger l’outil, accédez à [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Cliquez sur la **Société de démonstration utilisé** pour exécuter l’Explorateur graphique par rapport à des données à partir d’un client de l’exemple. Vous n’avez pas besoin des informations d’identification à utiliser la société de démonstration. Vous pouvez également cliquez sur **connexion** et connectez-vous avec vos informations d’identification du compte Azure AD pour exécuter l’Explorateur graphique par rapport à vos clients. Si vous exécutez l’Explorateur graphique par rapport à vos propres clients, vous ou votre administrateur devrez consentement lors de l’ouverture de session. Si vous avez un abonnement à Office 365, vous disposez automatiquement d’un locataire AD Azure. Les informations d’identification que vous utilisez pour vous connecter à Office 365 sont, en fait, les comptes Active Directory Azure, et vous pouvez utiliser ces informations d’identification avec graphique de l’Explorateur.

**Exécuter une requête**: pour exécuter une requête, tapez votre requête dans la zone de texte demande et cliquez sur **obtenir** ou cliquez sur la touche **entrée** . Les résultats sont affichés dans la zone réponse. Par exemple, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` répertorie tous les objets de groupe dans le répertoire de la démo.

Notez les fonctionnalités et les limitations de l’Explorateur de graphiques suivantes :
- Fonctionnalité de saisie semi-automatique sur des ensembles de ressources. Pour voir cela, cliquez sur la **Société de démonstration d’utilisation** et puis cliquez sur la zone de texte demande (où apparaît l’URL de la société). Vous pouvez sélectionner une ressource dans la liste déroulante.

- Prend en charge la « me » et « nommée » adressage alias. Par exemple, vous pouvez utiliser `https://graph.windows.net/me?api-version=1.6` pour renvoyer l’objet utilisateur de l’utilisateur connecté ou `https://graph.windows.net/myorganization/users?api-version=1.6` pour renvoyer tous les utilisateurs dans le répertoire en cours. Notez que « me » alias using renvoie une erreur pour la société de démonstration, car il n’y a aucun utilisateur connecté qui effectue la demande.

- Une section d’en-tête de réponse. Cela peut servir à résoudre les problèmes qui se produisent lors de l’exécution des requêtes.

- Une visionneuse JSON de la réponse avec les fonctionnalités de développement et de réduction.

- Aucune prise en charge pour afficher une photo miniature.

## <a name="using-fiddler-to-write-to-the-directory"></a>À l’aide de Fiddler pour écrire dans le répertoire

Pour les besoins de ce guide de démarrage rapide, vous pouvez utiliser le débogueur de Web de Fiddler pour entraîner l’exécution d’opérations « écrire » contre vous répertoire d’Azure AD. Pour plus d’informations et pour installer Fiddler, consultez [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

Dans l’exemple ci-dessous, vous utiliserez le débogueur Web de Fiddler pour créer un nouveau groupe de sécurité 'MyTestGroup' dans le répertoire AD Azure.

**Obtenir un jeton d’accès**: pour accéder à Azure AD graphique, clients doivent s’authentifier avec succès vers Active Directory Azure tout d’abord. Pour plus d’informations, consultez [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

**Composition et exécuter une requête**: suivez les étapes ci-dessous.

1. Ouvrir le débogueur Web de Fiddler et basculez vers l’onglet **Composer** .
2. Dans la mesure où vous souhaitez créer un nouveau groupe de sécurité, sélectionnez **Valider** en tant que la méthode HTTP à partir du menu déroulant. Pour plus d’informations sur les opérations et les autorisations sur un objet de groupe, voir le [groupe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) dans la [référence de l’API Azure AD graphique reste](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Dans le champ **Valider**, tapez les éléments suivants en tant que l’URL de demande : `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] Vous devez remplacer mytenantdomain par le nom de domaine de votre propre répertoire AD Azure.

4. Dans le champ déroulant Post en dessous, tapez ce qui suit :

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Remplacer votre &lt;votre jeton d’accès&gt; avec le jeton d’accès de votre annuaire AD Azure.

5. Dans le champ **corps de requête** , tapez ce qui suit :

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Pour plus d’informations sur la création de groupes, reportez-vous à la section [Créer un groupe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Pour plus d’informations sur les entités AD Azure et les types qui sont exposées par le graphique et les informations sur les opérations qui peuvent être effectuées sur ces derniers avec un graphique, voir [référence de l’API reste Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l' [API d’Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- En savoir plus sur les [autorisations d’API Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
