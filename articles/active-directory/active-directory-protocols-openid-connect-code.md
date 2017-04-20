<properties
    pageTitle="Vue d’ensemble du protocole AD Azure .NET | Microsoft Azure"
    description="Cet article décrit comment utiliser les messages HTTP pour autoriser l’accès aux applications web et web API dans vos clients à l’aide d’Azure Active Directory et connecter des OpenID."
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


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autoriser l’accès aux applications web à l’aide de OpenID de se connecter et Azure Active Directory

[OpenID connecter](http://openid.net/specs/openid-connect-core-1_0.html) est une couche d’identité simple basée sur le protocole OAuth 2.0. OAuth 2.0 définit des mécanismes permettant d’obtenir et d’utiliser des **jetons d’accès** pour accéder aux ressources protégées, mais ils ne définissent pas les méthodes standard pour fournir des informations d’identité. OpenID connecter implémente l’authentification en tant qu’extension au processus d’autorisation OAuth 2.0, fournissant des informations sur l’utilisateur final sous la forme d’une `id_token` qui vérifie l’identité de l’utilisateur ainsi que fournit des informations de profil de base de l’utilisateur.

OpenID connecter nous recommandons si vous générez une application web qui est hébergée sur un serveur et accessible via un navigateur.

## <a name="authentication-flow-using-openid-connect"></a>Flux d’authentification à l’aide de la connexion de OpenID

Le flux de connexion plus simple contient les étapes suivantes, chacune d’elles est décrite en détail ci-dessous.

![OpenId connecter le flux d’authentification](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>Envoyer la demande de connexion

Lorsque votre application web doit authentifier l’utilisateur, il doit diriger l’utilisateur vers le `/authorize` point de terminaison. Cette requête est similaire à la première branche du [Flux du Code d’autorisation OAuth 2.0](active-directory-protocols-oauth-code.md), à quelques distinctions importantes près :

- La demande doit comprendre la portée `openid` dans les `scope` paramètre.
- Le `response_type` paramètre doit inclure `id_token`.
- La demande doit inclure le `nonce` paramètre.

Par conséquent, une demande d’exemple ressemblerait à ceci :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Obligatoire | Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont des identificateurs de clients, par exemple, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` pour les jetons de clients indépendants |
| identifiant_client | Obligatoire | L’Id d’Application assigné à votre application lorsque vous l’enregistré avec AD Azure. Vous le trouverez dans le portail classique d’Azure. Cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application puis cliquez sur **configurer** |
| response_type | Obligatoire | Doit inclure `id_token` pour la OpenID se connecter connexion.  Il peut également inclure d’autres response_types, tel que `code`. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des étendues.  OpenID de connexion, il doit inclure dans l’étendue `openid`, ce qui correspond à l’autorisation « Connexion vous » dans l’interface utilisateur de consentement.  Vous pouvez également inclure des autres étendues dans cette requête de demande de consentement. |
| valeur à usage unique | Obligatoire | Une valeur incluse dans la demande, générée par l’application, ce qui sera incluse dans la suite `id_token` sous la forme d’une demande d’indemnisation.  L’application peut ainsi vérifier cette valeur pour atténuer les attaques par relecture de jeton.  La valeur est en général une chaîne aléatoire et unique ou un GUID qui peut être utilisé pour identifier l’origine de la demande.  |
| redirect_uri | recommandé | La redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de le redirect_uris que vous avez enregistré dans le portail, mais il doit être codé en url. |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer l’authorization_code résultant dans votre application.  Les valeurs prises en charge sont `form_post` pour la *validation de formulaire HTTP* ou `fragment` pour le *fragment d’URL*.  Pour les applications web, nous recommandons l’utilisation `response_mode=form_post` pour garantir le transfert plus sécurisé de jetons à votre application.  
| état | recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher des attaques de type contrefaçon demande entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |
| invite de commandes | facultatif | Indique le type d’intervention de l’utilisateur qui est requis.  Les seules valeurs valides pour l’instant sont 'connexion', 'none' et 'accord'.  `prompt=login`force l’utilisateur à entrer leurs informations d’identification sur demande, une inversion connexion unique.  `prompt=none`est le contraire : il veillera à ce que l’utilisateur n’est pas présenté avec n’importe quelle invite interactive que ce soit.  Si la demande ne peut pas être effectuée en mode silencieux via une connexion unique, le point de terminaison renvoie une erreur.  `prompt=consent`déclenche la boîte de dialogue de consentement OAuth une fois que l’utilisateur se connecte, demandant à l’utilisateur d’accorder des autorisations à l’application. |
| login_hint | facultatif | Peut être utilisé pour renseigner le champ adresse de nom d’utilisateur/courriel du signe de la page pour l’utilisateur, si vous connaissez leur nom d’utilisateur à l’avance.  Souvent les applications utilisent ce paramètre lors de la ré-authentification, après avoir extrait déjà le nom d’utilisateur à partir d’une précédente connexion à l’aide de la `preferred_username` de revendication. |


À ce stade, l’utilisateur sera invité à entrer leurs informations d’identification et d’authentification.

### <a name="sample-response"></a>Exemple de réponse

Un exemple de réponse, une fois que l’utilisateur a été authentifié, pourrait ressembler à ceci :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id_token | Le `id_token` que l’application est demandée. Vous pouvez utiliser la `id_token` pour vérifier l’identité de l’utilisateur et de commencer une session avec l’utilisateur.  |
| état | Valeur incluse dans la demande est également retournée dans la réponse de jeton. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher des attaques de type contrefaçon demande entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |

### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
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

## <a name="validate-the-idtoken"></a>Valider l’id_token

Seulement recevoir une `id_token` n’est pas suffisant pour authentifier l’utilisateur. Vous devez valider la signature et vérifier les revendications dans le `id_token` par les exigences de votre application. Le point de terminaison AD Azure utilise les jetons de Web JSON (JWTs) et de la cryptographie à clé publique pour signer des jetons et vérifier qu’ils sont valides.

Vous pouvez choisir de valider la `id_token` de client, code, mais une pratique courante consiste à envoyer le `id_token` à un serveur principal et d’effectuer la validation il. Une fois que vous avez validé la signature de la `id_token`, il y a quelques demandes vous devrez vérifier.

Vous souhaiterez peut-être également valider les demandes supplémentaires en fonction de votre scénario. Certains contrôles communs sont les suivants :

- Assurant l’utilisateur/organisation est associée à l’application.
- Assurant l’utilisateur a des privilèges / d’autorisation
- Garantir une certaine force d’authentification s’est produite, comme l’authentification à plusieurs facteurs.

Une fois que vous avez complètement validé le `id_token`, vous pouvez commencer une session avec l’utilisateur et utiliser les revendications dans le `id_token` pour obtenir des informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, des enregistrements, des autorisations, etc.. Pour plus d’informations sur les types de jetons et les revendications, consultez [prise en charge de jeton et les Types de revendications](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Envoyer une demande de déconnexion

Lorsque vous souhaitez vous connecter à l’utilisateur de l’application, il n’est pas suffisante effacer les cookies de votre application ou à la fin de sinon la session avec l’utilisateur.  Vous devez également rediriger l’utilisateur vers le `end_session_endpoint` pour la déconnexion.  Si vous ne parvenez pas à le faire, l’utilisateur sera en mesure de s’authentifier à nouveau à votre application sans entrer leurs informations d’identification, car ils auront une seule ouverture de session session valide avec le point de terminaison Azure AD.

Vous pouvez rediriger tout simplement de l’utilisateur de la `end_session_endpoint` figurant dans le document de métadonnées OpenID se connecter :

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommandé | L’URL sur laquelle l’utilisateur doit être redirigé vers après déconnexion réussie.  Si ne pas, l’utilisateur s’affichera un message générique.  |

## <a name="token-acquisition"></a>Acquisition de jeton

Beaucoup d’applications web doivent non seulement vous connecter l’utilisateur, mais également accéder à un service web pour le compte de cet utilisateur à l’aide de OAuth. Ce scénario combine OpenID se connecter pour l’authentification utilisateur lors de l’acquisition simultanément une `authorization_code` qui peut être utilisé pour obtenir des `access_tokens` en utilisant le flux de Code d’autorisation OAuth.

## <a name="get-access-tokens"></a>Obtenir des jetons d’accès

Pour acquérir les jetons d’accès, vous devrez modifier la demande d’ouverture de session à partir du haut :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

En incluant des autorisations dans la demande et à l’aide de `response_type=code+id_token`, le `authorize` point de terminaison veillera à ce que l’utilisateur a accepté les autorisations indiquées dans le `scope` paramètre de requête et retourner à votre application un code d’autorisation pour l’échange d’un jeton d’accès.

### <a name="successful-response"></a>Réponse correcte

Une réponse correcte à l’aide de `response_mode=form_post` ressemble à :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id_token | Le `id_token` que l’application est demandée. Vous pouvez utiliser la `id_token` pour vérifier l’identité de l’utilisateur et de commencer une session avec l’utilisateur. |
| code | Authorization_code que l’application est demandée. L’application peut utiliser le code d’autorisation de demander un jeton d’accès de la ressource cible. Authorization_codes sont de très courte durée de vie, en général ils expirent après 10 minutes environ. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |

### <a name="error-response"></a>Réponse d’erreur

Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification.  |

Pour une description des codes d’erreur possibles et leur action client recommandé, consultez [codes d’erreur pour les erreurs de point de terminaison de l’autorisation](#error-codes-for-authorization-endpoint-errors).

Une fois que vous avez obtenu une autorisation `code` et une `id_token`, vous pouvez vous connecter l’utilisateur et obtenir des jetons d’accès pour leur compte.  Pour signer l’utilisateur dans, vous devez valider le `id_token` exactement comme décrit ci-dessus. Pour obtenir des jetons d’accès, vous pouvez suivre la procédure décrite dans notre [documentation de protocole OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).
