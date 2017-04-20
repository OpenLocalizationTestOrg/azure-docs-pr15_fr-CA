
<properties
    pageTitle="Flux les informations d’identification Active Directory Azure v2.0 OAuth Client | Microsoft Azure"
    description="Création d’applications web à l’aide de la mise en oeuvre de l’annonce Azure du protocole d’authentification OAuth 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>Flux de protocoles v2.0 - informations d’identification du Client 2.0 OAuth

L' [accordent des informations d’identification du client OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), parfois appelé « OAuth à deux branches », permet d’accéder aux ressources web hébergé à l’aide de l’identité d’une application.  Il est couramment utilisé pour les interactions de serveur à serveur qui doivent s’exécuter en arrière-plan sans que le precense immédiate d’un utilisateur final.  Ces types d’applications sont souvent appelés **démons** ou des **comptes de service**.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

Dans le plus courant trois « à trois branches OAuth, » une application cliente est accordée l’autorisation d’accéder à une ressource pour le compte d’un utilisateur particulier.  L’autorisation est **déléguée** de l’utilisateur à l’application, généralement pendant le processus [d’autorisation](active-directory-v2-scopes.md) .  Toutefois, dans le flux d’informations d’identification de client, autorisations sont accordées **directement** à l’application elle-même.  Lorsque l’application présente un jeton à une ressource, la ressource applique qu’autorisation pour effectuer une action - pas qu’un utilisateur a l’autorisation de l’application elle-même.

## <a name="protocol-diagram"></a>Diagramme de protocole
Le flux d’informations d’identification client entière ressemble à ceci : chacune des étapes sont décrites en détail ci-dessous.

![Flux des informations d’identification des clients](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obtenir l’autorisation directe 
Qu’une application reçoit généralement l’autorisation directe pour accéder à une ressource de deux manières : via une liste de contrôle d’accès à la ressource ou affectation d’autorisation application dans Azure AD.  Il existe plusieurs autres façons qu'une ressource peut choisir d’autoriser ses clients, et chaque serveur de ressources peut choisir la méthode la plus pratique pour son application.  Ces deux méthodes sont les plus courantes dans Azure AD et sont recommandés pour les clients et les ressources que vous souhaitent effectuer le flux d’informations d’identification de client.

### <a name="access-control-lists"></a>Listes de contrôle d’accès
Un fournisseur de ressources donné peut appliquer un contrôle d’autorisation basé sur une liste d’ID d’application qu’il connaît et qu’il accorde un niveau particulier d’accès.  Lorsque la ressource reçoit un jeton à partir de la version 2.0, elle peut décoder le jeton et extraire l’ID de l’Application du client à partir de la `appid` et `iss` revendications.  Il peut alors comparer que l’application par rapport à une liste de contrôle d’accès (ACL) qu’il gère.  Le niveau de granularité et de la méthode de la liste de contrôle d’accès peuvent varier considérablement d’une ressource à.

Un cas d’usage commun pour ces ACL est test de canaux d’injection pour une application web ou web api.  Le web api peut-être accorder uniquement un sous-ensemble de toutes les autorisations à ses clients divers.  Mais pour exécuter les tests de bout en bout sur l’api, un client de test est créé qui acquiert des jetons à partir de la version 2.0 et les envoie à l’api.  L’api peut ensuite les ACL ID d’Application du client de test pour un accès complet aux fonctionnalités d’ensemble de l’api.  Notez que si vous avez une liste de ce type sur votre service, veillez à seulement valider l’appelant `appid`, mais également valider que la `iss` du jeton est approuvé aussi bien.

Ce type d’autorisation est courant pour les processus et les comptes de service qui doivent accéder aux données appartienne à des utilisateurs grand public avec des comptes personnels Microsoft.  Pour les données appartienne à des organisations, il est recommandé d’acquérir les autorisations requises par l’application perimssions.

### <a name="application-permissions"></a>Autorisations de l’application
Au lieu d’utiliser des listes ACL, API peut exposer un jeu d' **autorisations des applications** qui peuvent être accordées à une application.  Une application l’autorisation est accordée à une application par un administrateur d’une organisation et peut uniquement être utilisée pour accéder aux données appartienne à cette organisation et ses employés.  Par exemple, le graphique Microsoft expose plusieurs autorisations d’application :

- Lecture du courrier dans toutes les boîtes aux lettres de transfert
- Lire et écrire des messages dans toutes les boîtes aux lettres de
- Envoi d’un message comme n’importe quel utilisateur
- Lecture des données de l’annuaire
- [+ autres](https://graph.microsoft.io)

Pour acquérir ces autorisations dans votre application, vous pouvez effectuer les étapes suivantes.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail de l’enregistrement d’application

- Si vous ne l’avez pas déjà, accédez à votre application dans [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou [créer une application](active-directory-v2-app-registration.md) .  Vous devez vous assurer que votre application a créé au moins un Secret de l’Application.
- Recherchez la section **Autorisations d’Application directe** et ajouter les autorisations requises par votre application.
- Assurez-vous **d’Enregistrer** l’inscription de l’application

#### <a name="recommended-sign-the-user-into-your-app"></a>Recommandé : connexion de l’utilisateur dans votre application

Lors de la création d’une application qui utilise les autorisations de l’application, l’application devrez généralement une page/vue permet à l’administrateur d’approuver les autorisations de l’application.  Cette page peut être la partie de flux d’inscription de l’application, dans les paramètres de l’application, ou un flux de « se connecter » dédiée.  Dans de nombreux cas, il convient pour l’application à afficher ce « se connecter » afficher uniquement après que l’utilisateur s’est inscrit avec un travail ou à l’école compte Microsoft.

La signature de l’utilisateur dans l’application vous permet d’identifier le visant à laquelle l’utilisateur appartient, avant leur demandant d’approuver les autorisations de l’application.  Si elle est absolument nécessaire, il peut vous aider à créer une expérience plus intuitive pour les utilisateurs de votre organisation.  Pour signer l’utilisateur dans, suivez nos [didacticiels de protocole v2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations d’un administrateur de l’annuaire

Lorsque vous êtes prêt à demander des autorisations de l’administrateur de la société, vous pouvez rediriger l’utilisateur vers version 2.0 **admin consentement de point de terminaison**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Obligatoire | Le locataire du répertoire que vous souhaitez demander l’autorisation à partir de.  Peut être fourni au format guid ou nom convivial.  Si vous ne pas connaître le client auquel appartient l’utilisateur et souhaitez leur permettent de se connecter avec les clients, utilisez `common`. |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| redirect_uri | Obligatoire | Le redirect_uri où vous souhaitez que la réponse à envoyer pour votre application à gérer.  Il doit exactement correspondre à l’une de le redirect_uris que vous avez enregistré dans le portail, mais il doit être codé en url et peut avoir des segments de chemin d’accès supplémentaire. |
| état | recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  L’état est utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |

À ce stade, AD Azure appliquera que seul un administrateur peut se connecter terminer la demande.  L’administrateur devrez approuver toutes les autorisations d’application directe que vous avez demandés pour votre application dans le portail de l’enregistrement. 

##### <a name="successful-response"></a>Réponse correcte
Si l’administrateur approuve les autorisations pour votre application, la réponse correcte est :

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Le locataire du répertoire qui a les autorisations accordées à votre application il a demandé, au format guid. |
| état | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  L’état est utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |
| admin_consent | Est définie sur `True`. |


##### <a name="error-response"></a>Réponse d’erreur
Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse d’échec sera :

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur.  |

Une fois que vous avez reçu une réponse correcte à partir de l’application de point de terminaison de mise en service, votre application a acquis les autorisations d’application directe qu'il demandé.  Vous pouvez désormais déplacer sur demande un jeton pour la ressource de votre choix.

## <a name="get-a-token"></a>Obtenir un jeton.
Une fois que vous avez obtenu l’autorisation nécessaire pour votre application, vous pouvez procéder à l’acquisition des jetons d’accès pour les API.  Pour obtenir un jeton à l’aide du client accorder des informations d’identification, envoyez une demande POST à le `/token` point de terminaison v2.0 :

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| champ d’application | Obligatoire | La valeur passée pour la `scope` paramètre dans cette demande doit être l’identificateur de ressource (URI ID d’application) de la ressource souhaitée, fixée avec de la `.default` suffixe.  Pour l’exemple Microsoft Graph, la valeur doit être `https://graph.microsoft.com/.default`.  Cette valeur indique le point de terminaison v2.0 de toutes les autorisations d’application directe que vous avez configuré pour votre application, il doit émettre un jeton pour ceux se rapportant à la ressource souhaitée. |
| client_secret | Obligatoire | Le Secret de l’Application que vous avez généré dans le portail d’inscription pour votre application. |
| grant_type | Obligatoire | Doit être `client_credentials`. | 

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte prend la forme :

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandé. L’application peut recourir à ce jeton pour s’authentifier auprès de la ressource sécurisée, tel qu’un site web API. |
| token_type | Indique la valeur du type de jeton. Le seul type qui prend en charge d’Azure AD est `Bearer`.  |
| expires_in | La durée pendant laquelle le jeton d’accès est valide (en secondes). |

#### <a name="error-response"></a>Réponse d’erreur
Une réponse d’erreur prend la forme :

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification.  |
| error_codes | Liste des codes d’erreur spécifiques de SharePoint Team Services peut aider dans le diagnostic.  |
| horodatage | Heure à laquelle l’erreur s’est produite. |
| argument trace_id | Identificateur unique de la demande qui peut aider dans le diagnostic.  |
| correlation_id | Identificateur unique de la demande qui peut aider dans le diagnostic entre les composants. |

## <a name="use-a-token"></a>Utiliser un jeton
Maintenant que vous avez acquis un jeton, vous pouvez utiliser ce jeton pour effectuer des demandes pour la ressource.  Le jeton d’expiration, il suffit de répéter la demande à la `/token` point de terminaison pour acquérir un jeton d’accès fraîches.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Exemple de code
Pour voir un exemple d’une application qu’implémente de que la client_credentials accorder à l’aide de l’administrateur consentement de point de terminaison, reportez-vous à notre [exemple de code du démon v2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
