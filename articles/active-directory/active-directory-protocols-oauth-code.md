<properties
    pageTitle="Vue d’ensemble du protocole AD Azure .NET | Microsoft Azure"
    description="Cet article décrit comment utiliser les messages HTTP pour autoriser l’accès aux applications web et web API dans vos clients à l’aide d’Azure Active Directory et OAuth 2.0."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Autoriser l’accès aux applications web à l’aide de OAuth 2.0 et Azure Active Directory

Utilise Active Directory (AD Azure) Azure OAuth 2.0 vous permet d’autoriser l’accès aux applications web et web API dans vos clients AD Azure. Ce guide est indépendante du langage et décrit la façon d’envoyer et de recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

Le flux de code d’autorisation OAuth 2.0 est décrite au [point 4.1 de la spécification OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1) . Il est utilisé pour effectuer l’authentification et l’autorisation dans la plupart des types d’application, y compris les applications web et installé en mode natif les applications.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>Flux d ' autorisation de OAuth 2.0

À un niveau élevé, le flux de toute autorisation d’une application ressemble un peu à cela :

![Flux de Code d’authentification OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>Demander un code d’autorisation

Le flux de code d’autorisation commence avec le client qui redirige l’utilisateur vers le `/authorize` point de terminaison. Dans cette demande, le client indique les autorisations dont il a besoin d’acquérir de l’utilisateur. Vous pouvez obtenir les points de terminaison OAuth 2.0 à partir de la page de votre application dans Azure le portail classique, le bouton **Affichage de points de terminaison** dans le bac d’alimentation inférieur.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Obligatoire | Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont des identificateurs de clients, par exemple, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` pour les jetons de clients indépendants |
| identifiant_client | Obligatoire | L’Id d’Application assigné à votre application lorsque vous l’enregistré avec AD Azure. Vous pouvez le trouver dans le portail de gestion Azure. Cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application puis cliquez sur **configurer** |
| response_type | Obligatoire | Doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri | recommandé | La redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de le redirect_uris que vous avez enregistré dans le portail, mais il doit être codé en url.  Pour les applications natives et mobiles, vous devez utiliser la valeur par défaut de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer le précédent jeton qui en résulte à votre application.  Peut être `query` ou `form_post`.  |
| état | recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher des attaques de type contrefaçon demande entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |
| ressources | facultatif | L’URI d’ID App du web API (ressource sécurisée). Pour trouver l’URI ID App du web API, dans le portail de gestion Azure, cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application puis cliquez sur **configurer**. |
| invite de commandes | facultatif |  Indiquer le type d’intervention de l’utilisateur qui est requis.<p> Les valeurs valides sont : <p> *ouverture de session*: l’utilisateur doit être invité à s’authentifier à nouveau. <p> *consentement*: consentement de l’utilisateur a été accordé, mais il doit être mis à jour. L’utilisateur doit être invité à donner son consentement. <p> *admin_consent*: un administrateur doit être invité à donner son consentement au nom de tous les utilisateurs dans leur organisation. |
| login_hint | facultatif | Peut être utilisé pour renseigner le champ adresse de nom d’utilisateur/courriel du signe de la page pour l’utilisateur, si vous connaissez leur nom d’utilisateur à l’avance.  Souvent les applications utilisent ce paramètre lors de la ré-authentification, après avoir extrait déjà le nom d’utilisateur à partir d’une précédente connexion à l’aide de la `preferred_username` de revendication. |
| domain_hint | facultatif | Fournit une indication sur le client ou le domaine que l’utilisateur doit utiliser pour se connecter. La valeur de le domain_hint est un domaine enregistré pour le client. Si le client est fédéré à un répertoire local, DAS redirige vers le serveur de fédération de clients spécifié. |

> [AZURE.NOTE] Si l’utilisateur fait partie d’une organisation, un administrateur de l’organisation peut consentement ou refuser à l’utilisateur ou autoriser l’utilisateur à donner son consentement. L’utilisateur dispose de l’option de consentement uniquement lorsque l’administrateur le permet.

À ce stade, l’utilisateur sera invité à entrer leurs informations d’identification et acceptez les autorisations indiquées dans le `scope` paramètre de requête. Une fois que l’utilisateur s’authentifie et consentement, AD Azure envoie une réponse à votre application à la `redirect_uri` adresse dans votre demande.

### <a name="successful-response"></a>Réponse correcte

Une réponse réussie pourrait ressembler à ceci :

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Paramètre | Description |
| -----------------------| --------------- |
| admin_consent | La valeur est True si un administrateur a consenti à une demande de consentement.|
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser le code d’autorisation de demander un jeton d’accès de la ressource cible. |
| session_state | Une valeur unique qui identifie la session utilisateur en cours. Cette valeur est un GUID, mais il doit être traitée comme une valeur opaque qui est transmise sans examen. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. Il est conseillé de l’application pour vérifier que les valeurs d’état de la demande et la réponse sont identiques à l’avant à l’aide de la réponse. Cela permet de détecter [les attaques de contrefaçon de requête intersites (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) sur le client.  

### <a name="error-response"></a>Réponse d’erreur

Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
|-----------|-------------|
| erreur | Une valeur de code d’erreur définie au point 5.2 de la [Structure d’autorisation OAuth 2.0](http://tools.ietf.org/html/rfc6749). Le tableau suivant décrit les codes d’erreur AD Azure renvoie. |
| error_description | Une description plus détaillée de l’erreur. Ce message n’est pas destiné à être convivial de l’utilisateur final. |
| état | La valeur d’état est une valeur non réutilisé générée de manière aléatoire qui est envoyée dans la demande et retournée dans la réponse pour empêcher les attaques de falsification (CSRF) demande entre sites. |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Le code d’autorisation permet de demander un jeton d’accès

Maintenant que vous avez acquis un code d’autorisation et l’autorisation avez été accordée par l’utilisateur, vous pouvez utiliser le code pour un jeton d’accès à la ressource souhaitée, en envoyant une demande POST à le `/token` point de terminaison :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| clients | Obligatoire |  Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont des identificateurs de clients, par exemple, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` pour les jetons de clients indépendants |
| identifiant_client | Obligatoire | L’Id d’Application assigné à votre application lorsque vous l’enregistré avec AD Azure. Vous le trouverez dans le portail classique d’Azure. Cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application puis cliquez sur **configurer** |
| grant_type | Obligatoire | Doit être `authorization_code` pour le flux de code d’autorisation. |
| code | Obligatoire | Le `authorization_code` que vous avez acheté dans la section précédente   |
| redirect_uri | Obligatoire | Le même `redirect_uri` valeur qui a été utilisé pour acquérir la `authorization_code`. |
| client_secret | requis pour les applications web | Le secret de l’application que vous avez créée dans le portail de l’enregistrement d’application pour votre application.  Il ne doit pas être utilisé dans une application native, car client_secrets ne peut être stockée de manière fiable sur les périphériques.  Il est requis pour les applications web et web API, qui ont la capacité de stocker le `client_secret` en toute sécurité sur le serveur. |
| ressources | requis si spécifié dans la demande d’autorisation de code, else facultative | L’URI d’ID App du web API (ressource sécurisée).
Pour trouver l’URI ID App, dans le portail de gestion Azure, cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**.

### <a name="successful-response"></a>Réponse correcte

Annonce Azure renvoie un jeton d’accès après une réponse correcte. Pour réduire les appels de réseau à partir de l’application cliente et leur latence associé, l’application cliente doit mettre en cache les jetons d’accès pour la durée de vie de jeton qui est spécifiée dans la réponse OAuth 2.0. Pour déterminer la durée de vie de jeton, utilisez soit le `expires_in` ou `expires_on` les valeurs de paramètre.

Si une ressource de l’API de web renvoie un `invalid_token` code d’erreur, cela peut indiquer que la ressource a déterminé que le jeton a expiré. Si les temps d’horloge client et les ressources sont différent (appelé un « décalage horaire »), la ressource peut prendre en compte le jeton à expiration avant que le jeton est désactivé à partir du cache client. Si cela se produit, désactivez le jeton à partir du cache, même s’il est toujours dans sa durée de vie calculée.

Une réponse réussie pourrait ressembler à ceci :

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandé. L’application peut recourir à ce jeton pour s’authentifier auprès de la ressource sécurisée, tel qu’un site web API. |
| token_type | Indique la valeur du type de jeton. Le seul type qui prend en charge les annonces Azure est porteur. Pour plus d’informations sur les jetons de support, consultez [OAuth2.0 autorisation Framework : utilisation de jeton de support (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | La durée pendant laquelle le jeton d’accès est valide (en secondes). |
| expires_on | L’heure d’expiration du jeton d’accès. Cette date est représentée en tant que le nombre de secondes depuis 1970-01-01T0:0:0Z UTC jusqu'à ce que le délai d’expiration. Cette valeur est utilisée pour déterminer la durée de vie des jetons mis en cache. |
| ressources | L’URI d’ID App du web API (ressource sécurisée).|
| champ d’application | Autorisations d’emprunt d’identité à l’application cliente. L’autorisation par défaut est `user_impersonation`. Le propriétaire de la ressource sécurisée peut enregistrer des valeurs supplémentaires dans AD Azure.|
| refresh_token |  Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons d’accès supplémentaires après l’expiration du jeton d’accès actuel.  Actualiser les jetons sont durables et peut être utilisés pour conserver l’accès à des ressources sur de longues périodes de temps. |
| id_token | Un non signé JSON Web jeton (JWT). Le base64Url peut app décoder les segments de ce jeton pour demander des informations concernant l’utilisateur connecté. L’application peut mettre en cache les valeurs et les afficher, mais il fiez pas les limites de sécurité ou d’autorisation. |

### <a name="jwt-token-claims"></a>Revendications des jetons de JWT
Le jeton JWT dans la valeur de la `id_token` paramètre peut être décodé dans les déclarations suivantes :

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Le `id_token` paramètre comprend les types suivants de la demande. Pour plus d’informations sur les jetons de web JSON, consultez les [spécifications IETF de JWT](http://go.microsoft.com/fwlink/?LinkId=392344). Pour plus d’informations sur les types de jetons et les revendications, consultez [prise en charge de jeton et les Types de revendications](active-directory-token-and-claims.md)

| Type de revendication | Description |
|------------|-------------|
| AUD | Audience du jeton. Lorsque le jeton est délivré pour une application cliente, le public est la `client_id` du client.
| EXP | Délai d’expiration. L’heure d’expiration du jeton. Pour le jeton être valide, la date/heure actuelle doit être inférieure ou égale à la `exp` valeur. L’heure est représentée en tant que le nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment où le jeton a été délivré. |
| family_name | L’utilisateur nom de famille. L’application peut afficher cette valeur. |
| nom_donné | Prénom de l’utilisateur. L’application peut afficher cette valeur. |
| IAT | Délivré au temps. L’heure d’émission du JWT. L’heure est représentée en tant que le nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment où le jeton a été délivré. |
| ISS | Identifie l’émetteur de jeton |
| NBF | Pas avant l’heure. Le temps lorsque le jeton devient effectif. Pour le jeton être valide, la date/heure actuelle doit être supérieure ou égale à la valeur Nbf. L’heure est représentée en tant que le nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment où le jeton a été délivré. |
| OID | Identificateur d’objet (ID) de l’objet utilisateur dans Active Directory Azure. |
| Sub | Identificateur de l’objet de jeton. Il s’agit d’un identificateur persistant et immuable de décrivant le jeton de l’utilisateur. Utilisez cette valeur dans la logique de la mise en cache. |
| TID | Clients identificateur (ID) du locataire Azure AD qui a émis le jeton. |
| nom_unique | Un identificateur unique qui peut être affiché à l’utilisateur. Il s’agit généralement d’un nom d’utilisateur principal (UPN). |
| UPN | Nom d’utilisateur principal de l’utilisateur. |
| ver | Version. La version du jeton JWT, généralement 1.0. |

### <a name="error-response"></a>Réponse d’erreur

Les erreurs de point de terminaison d’émission de jeton sont des codes d’erreur HTTP, parce que le client appelle le point de terminaison d’émission du jeton directement. Outre le code d’état HTTP, le point de terminaison AD Azure d’émission du jeton retourne également un document JSON avec des objets qui décrivent l’erreur.

Un exemple de réponse d’erreur pourrait ressembler à ceci :

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification.  |
| error_codes | Liste des codes d’erreur spécifiques de SharePoint Team Services peut aider dans le diagnostic. |
| horodatage | Heure à laquelle l’erreur s’est produite. |
| argument trace_id | Identificateur unique de la demande qui peut aider dans le diagnostic.  |
| correlation_id | Identificateur unique de la demande qui peut aider dans le diagnostic entre les composants.|

#### <a name="http-status-codes"></a>Codes d’état HTTP

Le tableau suivant répertorie les codes d’état HTTP qui renvoie de l’extrémité d’émission du jeton. Dans certains cas, le code d’erreur est suffisant pour décrire la réponse, mais en cas d’erreurs, vous devez analyser le document d’accompagnement JSON et examiner son code d’erreur.

| Code HTTP | Description |
|-----------|-------------|
| 400       | Code HTTP par défaut. Utilisé dans la plupart des cas et est généralement dû à une requête mal formée. Corriger et les soumettre de nouveau la demande. |
| 401       | L’authentification a échoué. Par exemple, le paramètre client_secret est manquante dans la requête.|
| 403       | Échoué de l’autorisation. Par exemple, l’utilisateur n’a pas d’autorisation pour accéder à la ressource. |
| 500       | Une erreur interne s’est produite au niveau du service. Renouvelez la demande. |

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

## <a name="use-the-access-token-to-access-the-resource"></a>Utiliser le jeton d’accès pour accéder à la ressource.

Maintenant que vous avez acquis avec succès une `access_token`, vous pouvez utiliser le jeton dans les demandes au Web API, en l’incluant dans les `Authorization` en-tête. La spécification [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explique comment utiliser des jetons de support dans les demandes HTTP pour accéder aux ressources protégées.

### <a name="sample-request"></a>Exemple de requête

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Réponse d’erreur

Ressources sécurisées qui implémentent des codes d’état HTTP RFC 6750 problème. Si la demande n’inclut pas les informations d’authentification ou il manque le jeton, la réponse inclut une `WWW-Authenticate` en-tête. Lorsqu’une requête échoue, le serveur de ressources répond avec le code d’état HTTP et un code d’erreur.

Voici un exemple d’une réponse d’échec lors de la demande du client n’inclut pas le jeton de support :

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Paramètres de l’erreur

| Paramètre | Description |
|-----------|-------------|
| authorization_uri | L’URI (point de terminaison physique) du serveur d’autorisation. Cette valeur est également utilisée comme clé de recherche pour obtenir plus d’informations sur le serveur à partir d’un point de terminaison de découverte. <p><p> Le client doit valider que le serveur d’autorisation est approuvé. Lorsque la ressource est protégée par AD Azure, il est suffisant vérifier que l’URL commence par https://login.windows.net ou d’un autre hôte prenant en charge les annonces Azure. Une ressource client spécifique doit toujours retourner une autorisation spécifique au locataire URI. |
| erreur | Une valeur de code d’erreur définie au point 5.2 de la [Structure d’autorisation OAuth 2.0](http://tools.ietf.org/html/rfc6749).|
| error_description | Une description plus détaillée de l’erreur. Ce message n’est pas destiné à être convivial de l’utilisateur final.|
| id_ressource | Renvoie l’identificateur unique de la ressource. L’application cliente peut utiliser cet identificateur en tant que la valeur de la `resource` paramètre lorsqu’il demande un jeton pour la ressource. <p><p> Il est très important de l’application client vérifier cette valeur, dans le cas contraire, un service malveillant peut être en mesure d’induire une attaque **d’élévation de privilèges** <p><p> La stratégie recommandée pour empêcher une attaque consiste à vérifier que les `resource_id` correspond à la base de l’URL de l’API de web utilisés. Par exemple, si https://service.contoso.com/data est accédée, le `resource_id` peut être htttps://service.contoso.com/. L’application cliente doit rejeter un `resource_id` qui ne commence pas avec l’URL de base sauf s’il existe un moyen fiable de remplacement afin de vérifier le code. |

#### <a name="bearer-scheme-error-codes"></a>Codes d’erreur de jeu de support

La spécification de la RFC 6750 définit les erreurs suivantes de ressources faisant appel à l’aide de l’en-tête WWW-Authenticate et au schéma de porteur dans la réponse.

| Code d’état HTTP | Code d’erreur | Description | Action du client |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | La demande n’est pas bien formée. Par exemple, il peut être un paramètre manquant ou à l’aide de deux fois le même paramètre. | Corrigez l’erreur et renouvelez la demande. Ce type d’erreur doit se produire uniquement lors du développement et être détecté lors des tests initiaux. |
| 401 | invalid_token   | Le jeton d’accès est manquant, non valide ou a été révoqué. La valeur du paramètre error_description fournit des détails supplémentaires. |  Demander un nouveau jeton à partir du serveur d’autorisation. Si le nouveau jeton échoue, une erreur inattendue s’est produite. Envoyer un message d’erreur à l’utilisateur et une nouvelle tentative après un retard aléatoire. |
| 403 | insufficient_scope | Le jeton d’accès ne contient-elle pas les autorisations de l’emprunt d’identité requises pour accéder à la ressource. | Envoyer une nouvelle demande d’autorisation pour le point de terminaison de l’autorisation. Si la réponse contient le paramètre de l’étendue, utilisez la valeur de portée dans la requête pour la ressource. |
| 403 | insufficient_access | Le sujet du jeton n’a pas les autorisations requises pour accéder à la ressource. | Invite l’utilisateur à utiliser un compte différent ou demander des autorisations pour la ressource spécifiée. |

## <a name="refreshing-the-access-tokens"></a>Actualiser les jetons d’accès

Les jetons d’accès sont courtes et doivent être actualisées après leur expiration pour continuer à accéder à des ressources. Vous pouvez actualiser le `access_token` en soumettant une autre `POST` demande à la `/token` point de terminaison, mais ce temps fournissant le `refresh_token` à la place de la `code`.

Actualiser les jetons n’ont pas de durée de vie spécifiée. En règle générale, les durées de vie des jetons d’actualisation sont relativement longues. Toutefois, dans certains cas, jetons d’actualisation expirent, sont révoqués ou ne disposent pas de privilèges suffisants pour l’action souhaitée. Votre application doit s’attendre et gérer les erreurs renvoyées par le point de terminaison d’émission du jeton correctement.

Lorsque vous recevez une réponse avec une erreur de jeton d’actualisation, ignorer le jeton d’actualisation en cours et demande un nouveau code d’autorisation ou de jeton d’accès. En particulier, lorsqu’une actualisation à l’aide de jeton dans le flux de Code d’octroi, si vous recevez une réponse avec le `interaction_required` ou `invalid_grant` codes d’erreur, ignorer le jeton d’actualisation et de demander un nouveau code d’autorisation.

Un exemple de requête au point de terminaison **client spécifique** (vous pouvez également utiliser le point de terminaison **commun** ) pour obtenir un nouveau accès jeton en utilisant un jeton d’actualisation se présente comme suit :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Paramètre | Description |
|-----------|-------------|
| access_token | Le nouveau jeton d’accès qui a été demandé.|
| expires_in   | Durée de vie du restante en secondes du jeton. La valeur standard est 3600 (une heure). |
| expires_on   | La date et l’heure à laquelle le jeton expire. Cette date est représentée en tant que le nombre de secondes depuis 1970-01-01T0:0:0Z UTC jusqu'à ce que le délai d’expiration. |
| refresh_token | Un nouveau refresh_token OAuth 2.0 qui peut être utilisé pour demander de nouveaux jetons d’accès lorsque celui dans cette réponse arrive à expiration. |
| ressources     | Identifie les ressources sécurisées que le jeton d’accès peut être utilisé pour accéder à. |
| champ d’application        | Autorisations d’emprunt d’identité à l’application client natif. L’autorisation par défaut est **user_impersonation**. Le propriétaire de la ressource cible peut inscrire les autres valeurs dans Azure AD. |
| token_type   | Le type de jeton. La seule valeur prise en charge est **PORTEUR**. |

### <a name="successful-response"></a>Réponse correcte

Une réponse correcte de jeton ressemble à :

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>Réponse d’erreur

Un exemple de réponse d’erreur pourrait ressembler à ceci :

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification.  |
| error_codes | Liste des codes d’erreur spécifiques de SharePoint Team Services peut aider dans le diagnostic. |
| horodatage | Heure à laquelle l’erreur s’est produite. |
| argument trace_id | Identificateur unique de la demande qui peut aider dans le diagnostic.  |
| correlation_id | Identificateur unique de la demande qui peut aider dans le diagnostic entre les composants.|

Pour une description des codes d’erreur et l’action recommandée client, consultez [codes d’erreur pour les erreurs de jeton de point de terminaison](#error-codes-for-token-endpoint-errors).
