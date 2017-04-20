

<properties
    pageTitle="Flux de Code d’autorisation de publicité Azure v2.0 OAuth | Microsoft Azure"
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>Protocoles v2.0 - flux de Code d’autorisation OAuth 2.0

L’octroi de code d’autorisation OAuth 2.0 peut être utilisé dans les applications qui sont installées sur un périphérique d’accéder à des ressources protégées, telles que le web API.  À l’aide de la mise en oeuvre de la v2.0 modèle application de OAuth 2.0, vous pouvez ajouter se connecter et accéder à l’API à vos applications mobiles et de bureau.  Ce guide est indépendant du langage et décrit la façon d’envoyer et de recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

Au [point 4.1 de la spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749), le flux de code d’autorisation OAuth 2.0 est décrite dans.  Il est utilisé pour effectuer l’authentification et l’autorisation dans la plupart des types d’application, y compris les [applications web](active-directory-v2-flows.md#web-apps) et [installé en mode natif les applications](active-directory-v2-flows.md#mobile-and-native-apps).  Il permet d’acquérir en toute sécurité les access_tokens qui peut être utilisé pour accéder aux ressources qui sont sécurisés à l’aide de l’extrémité de la version 2.0 des applications.  

## <a name="protocol-diagram"></a>Diagramme de protocole
À un niveau élevé, le flux d’authentification pour une application native/mobile ressemble un peu à cela :

![Flux de Code d’authentification OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Demander un code d’autorisation
Le flux de code d’autorisation commence avec le client qui redirige l’utilisateur vers le `/authorize` point de terminaison.  Dans cette demande, le client indique les autorisations dont il a besoin d’acquérir de l’utilisateur :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Cliquez sur le lien ci-dessous pour exécuter cette requête ! Une fois connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec un `code` dans la barre d’adresses.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Obligatoire | Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et des identificateurs de client.  Pour plus d’informations, consultez [Concepts de base de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| response_type | Obligatoire | Doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri | recommandé | La redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de le redirect_uris que vous avez enregistré dans le portail, mais il doit être codé en url.  Pour les applications natives et mobiles, vous devez utiliser la valeur par défaut de `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des [étendues](active-directory-v2-scopes.md) que vous voulez que l’utilisateur à donner son consentement à.  |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer le précédent jeton qui en résulte à votre application.  Peut être `query` ou `form_post`.  |
| état | recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher des attaques de type contrefaçon demande entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |
| invite de commandes | facultatif | Indique le type d’intervention de l’utilisateur qui est requis.  Les seules valeurs valides pour l’instant sont 'connexion', 'none' et 'accord'.  `prompt=login`force l’utilisateur à entrer leurs informations d’identification sur demande, une inversion connexion unique.  `prompt=none`est le contraire : il veillera à ce que l’utilisateur n’est pas présenté avec n’importe quelle invite interactive que ce soit.  Si la demande ne peut pas être effectuée en mode silencieux via une connexion unique, le point de terminaison v2.0 renvoie une erreur.  `prompt=consent`déclenche la boîte de dialogue de consentement OAuth une fois que l’utilisateur se connecte, demandant à l’utilisateur d’accorder des autorisations à l’application. |
| login_hint | facultatif | Peut être utilisé pour renseigner le champ adresse de nom d’utilisateur/courriel du signe de la page pour l’utilisateur, si vous connaissez leur nom d’utilisateur à l’avance.  Souvent les applications utilisent ce paramètre lors de la ré-authentification, après avoir extrait déjà le nom d’utilisateur à partir d’une précédente connexion à l’aide de la `preferred_username` de revendication. |
| domain_hint | facultatif | Peut être une des `consumers` ou `organizations`.  Si inclus, il ignore le processus de découverte de messagerie électronique utilisateur traverse dans la version 2.0 de connexion page, conduisant à une expérience utilisateur plus rationalisée.  Souvent les applications utilisent ce paramètre lors de la ré-authentification, en extrayant le `tid` à partir d’une connexion précédente sign-in.  Si le `tid` est de valeur de revendication `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Dans le cas contraire, utilisez `domain_hint=organizations`. |

À ce stade, l’utilisateur sera invité à entrer leurs informations d’identification et d’authentification.  Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le `scope` paramètre de requête.  Si l’utilisateur n’a pas consenti à une de ces autorisations, il demande à l’utilisateur à donner son consentement pour les autorisations requises.  Détails des [autorisations, consentement et les applications mutualisées sont fournies ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur s’authentifie et consentement, le point de terminaison v2.0 renverra une réponse à votre application à la liste indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre.

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte à l’aide de `response_mode=query` ressemble à :

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Authorization_code que l’application est demandée. L’application peut utiliser le code d’autorisation de demander un jeton d’accès de la ressource cible.  Authorization_codes sont de très courte durée de vie, en général ils expirent après 10 minutes environ. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée :

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison d’autorisation

Le tableau suivant décrit les différents codes d’erreur qui peuvent être retournés dans le `error` paramètre de la réponse d’erreur.

| Code d’erreur | Description | Action du client |
|------------|-------------|---------------|
| invalid_request | Erreur de protocole, telles qu’un paramètre requis manquant. | Corriger et les soumettre de nouveau la demande. Il s’agit d’un développement erreur est interceptée en général lors du test initial.|
| unauthorized_client | L’application cliente n’est pas autorisée à demander un code d’autorisation. | Cela se produit généralement lorsque l’application cliente n’est pas enregistrée dans Active Directory Azure ou n’est pas ajoutée au locataire Azure AD de l’utilisateur. L’application peut demander à l’utilisateur avec des instructions pour l’installation de l’application et l’ajouter à Active Directory Azure. |
| ACCESS_DENIED | Propriétaire de la ressource refusé de consentement | L’application cliente peut informer l’utilisateur qu’il ne peut pas continuer à moins que l’utilisateur accepte. |
| unsupported_response_type | Le serveur d’autorisation ne gère pas le type de réponse de la demande. | Corriger et les soumettre de nouveau la demande. Il s’agit d’un développement erreur est interceptée en général lors du test initial.|
|server_error | Le serveur a rencontré une erreur inattendue. | Renouvelez la demande. Ces erreurs peuvent résulter de conditions temporaires. L’application cliente peut expliquer à l’utilisateur que sa réponse est retardée en raison d’une erreur temporaire. |
| temporarily_unavailable | Le serveur est temporairement trop occupé pour traiter la demande. | Renouvelez la demande. L’application cliente peut expliquer à l’utilisateur que sa réponse est retardée en raison d’une condition temporaire. |
| invalid_resource |La ressource cible n’est pas valide car il n’existe pas, Azure AD ne le trouve pas ou il n’est pas configuré correctement.| Cela indique que la ressource, si elle existe, n'a pas été configurée dans le client. L’application peut demander à l’utilisateur avec des instructions pour l’installation de l’application et l’ajouter à Active Directory Azure. |

## <a name="request-an-access-token"></a>Demande un jeton d’accès
Maintenant que vous avez acquis un authorization_code et l’autorisation avez été accordée par l’utilisateur, vous pouvez utiliser la `code` pour une `access_token` à la ressource souhaitée, en envoyant un `POST` demande à la `/token` point de terminaison :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Essayez d’exécuter cette requête dans Postman ! (N’oubliez pas de remplacer le `code`)     [ ![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| clients | Obligatoire | Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et des identificateurs de client.  Pour plus d’informations, consultez [Concepts de base de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| grant_type | Obligatoire | Doit être `authorization_code` pour le flux de code d’autorisation. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des étendues.  Les étendues demandés dans cette branche doivent être équivalent à ou un sous-ensemble des étendues demandé dans la première branche.  Si les portées spécifiées dans cette demande s’étendent sur plusieurs serveurs de ressources, le point de terminaison v2.0 retourne un jeton pour la ressource spécifiée dans la première étendue.  Pour une explication plus détaillée des étendues, consultez [autorisations, autorisation et les étendues](active-directory-v2-scopes.md).  |
| code | Obligatoire | Authorization_code que vous avez acheté dans la première branche du flux.   |
| redirect_uri | Obligatoire | La même valeur redirect_uri qui a été utilisée pour obtenir l’authorization_code. |
| client_secret | requis pour les applications web | Le secret de l’application que vous avez créée dans le portail de l’enregistrement d’application pour votre application.  Il ne doit pas être utilisé dans une application native, car client_secrets ne peut être stockée de manière fiable sur les périphériques.  Il est requis pour les applications web et web API, qui ont la capacité de stocker en toute sécurité de le client_secret côté serveur. |

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte de jeton ressemble à :

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandé. L’application peut recourir à ce jeton pour s’authentifier auprès de la ressource sécurisée, tel qu’un site web API. |
| token_type | Indique la valeur du type de jeton. Le seul type qui prend en charge les annonces Azure est porteur  |
| expires_in | La durée pendant laquelle le jeton d’accès est valide (en secondes). |
| champ d’application | Les étendues de l’access_token n’est valide pour. |
| refresh_token |  Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons d’accès supplémentaires après l’expiration du jeton d’accès actuel.  Refresh_tokens sont durables et peut être utilisé pour conserver l’accès à des ressources sur de longues périodes de temps.  Pour plus de détails, reportez-vous à la [référence de jeton de version 2.0](active-directory-v2-tokens.md).  |
| id_token | Un non signé JSON Web jeton (JWT). Le base64Url peut app décoder les segments de ce jeton pour demander des informations concernant l’utilisateur connecté. L’application peut mettre en cache les valeurs et les afficher, mais il fiez pas les limites de sécurité ou d’autorisation.  Pour plus d’informations sur id_tokens, consultez la [référence de jeton de point de terminaison v2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur ressemblera à :

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

#### <a name="error-codes-for-token-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison de jeton

| Code d’erreur | Description | Action du client |
|------------|-------------|---------------|
| invalid_request | Erreur de protocole, telles qu’un paramètre requis manquant. | Corriger et soumettre de nouveau la demande. |
| invalid_grant | Le code d’autorisation n’est pas valide ou a expiré. | Essayez une nouvelle demande à la `/authorize` point de terminaison |
| unauthorized_client | Le client authentifié n’est pas autorisé à utiliser ce type d’octroi de l’autorisation. | Cela se produit généralement lorsque l’application cliente n’est pas enregistrée dans Active Directory Azure ou n’est pas ajoutée au locataire Azure AD de l’utilisateur. L’application peut demander à l’utilisateur avec des instructions pour l’installation de l’application et l’ajouter à Active Directory Azure. |
| invalid_client | Échoué de l’authentification de client. | Les informations d’identification du client ne sont pas valides. Pour résoudre le problème, l’administrateur d’application met à jour les informations d’identification. |
| unsupported_grant_type | Le serveur d’autorisation ne gère pas le type de licence d’autorisation. | Modifier le type de licence dans la demande. Ce type d’erreur doit se produire uniquement lors du développement et être détecté lors du test initial. |
| invalid_resource | La ressource cible n’est pas valide car il n’existe pas, Azure AD ne le trouve pas ou il n’est pas configuré correctement. | Cela indique que la ressource, si elle existe, n'a pas été configurée dans le client. L’application peut demander à l’utilisateur avec des instructions pour l’installation de l’application et l’ajouter à Active Directory Azure. |
| interaction_required | La demande requiert l’interaction de l’utilisateur. Par exemple, une étape d’authentification supplémentaire est nécessaire. | Renouvelez la demande avec la même ressource. |
| temporarily_unavailable | Le serveur est temporairement trop occupé pour traiter la demande. | Renouvelez la demande. L’application cliente peut expliquer à l’utilisateur que sa réponse est retardée en raison d’une condition temporaire.|

## <a name="use-the-access-token"></a>Utiliser le jeton d’accès
Maintenant que vous avez acquis avec succès une `access_token`, vous pouvez utiliser le jeton dans les demandes au Web API en l’incluant dans les `Authorization` en-tête :

> [AZURE.TIP] Exécuter cette requête dans Postman ! (Remplacez le `Authorization` en-tête première)     [ ![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Actualiser le jeton d’accès
Access_tokens sont courts vécu, et vous devez les actualiser après leur expiration pour continuer à accéder à des ressources.  Vous pouvez le faire en envoyant un autre `POST` demande à la `/token` point de terminaison, cette fois, fournissant le `refresh_token` à la place de la `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Essayez d’exécuter cette requête dans Postman ! (N’oubliez pas de remplacer le `refresh_token`)     [ ![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Paramètre | | Description |
| ----------------------- | ------------------------------- | -------- |
| clients | Obligatoire | Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et des identificateurs de client.  Pour plus d’informations, consultez [Concepts de base de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| grant_type | Obligatoire | Doit être `refresh_token` pour cette branche du flux de code d’autorisation. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des étendues.  Les étendues demandés dans cette branche doivent être équivalent à ou un sous-ensemble des étendues demandée de la jambe de demande authorization_code d’origine.  Si les portées spécifiées dans cette demande s’étendent sur plusieurs serveurs de ressources, le point de terminaison v2.0 retourne un jeton pour la ressource spécifiée dans la première étendue.  Pour une explication plus détaillée des étendues, consultez [autorisations, autorisation et les étendues](active-directory-v2-scopes.md).  |
| refresh_token | Obligatoire | Refresh_token que vous avez acheté dans la deuxième branche du flux.   |
| redirect_uri | Obligatoire | La même valeur redirect_uri qui a été utilisée pour obtenir l’authorization_code. |
| client_secret | requis pour les applications web | Le secret de l’application que vous avez créée dans le portail de l’enregistrement d’application pour votre application.  Il ne doit pas être utilisé dans une application native, car client_secrets ne peut être stockée de manière fiable sur les périphériques.  Il est requis pour les applications web et web API, qui ont la capacité de stocker en toute sécurité de le client_secret côté serveur. |

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte de jeton ressemble à :

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandé. L’application peut recourir à ce jeton pour s’authentifier auprès de la ressource sécurisée, tel qu’un site web API. |
| token_type | Indique la valeur du type de jeton. Le seul type qui prend en charge les annonces Azure est porteur  |
| expires_in | La durée pendant laquelle le jeton d’accès est valide (en secondes). |
| champ d’application | Les étendues de l’access_token n’est valide pour. |
| refresh_token |  Un nouveau jeton d’actualisation OAuth 2.0. Vous devez remplacer l’ancien jeton d’actualisation avec ce jeton rachetée récemment actualiser afin de garantir que votre actualisation de jetons restent valables aussi longtemps que possible.  |
| id_token | Un non signé JSON Web jeton (JWT). Le base64Url peut app décoder les segments de ce jeton pour demander des informations concernant l’utilisateur connecté. L’application peut mettre en cache les valeurs et les afficher, mais il fiez pas les limites de sécurité ou d’autorisation.  Pour plus d’informations sur id_tokens, consultez la [référence de jeton de point de terminaison v2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Réponse d’erreur
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

Pour une description des codes d’erreur et l’action recommandée client, consultez [codes d’erreur pour les erreurs de jeton de point de terminaison](#error-codes-for-token-endpoint-errors).
