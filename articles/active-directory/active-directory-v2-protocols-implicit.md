<properties
    pageTitle="Flux implicite de publicité Azure v2.0 | Microsoft Azure"
    description="Création d’applications web à l’aide de la mise en oeuvre de l’annonce Azure v2.0 du flux implicite pour les applications de la même page."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 protocoles - SPAs à l’aide du flux implicite
Avec le point de terminaison version 2.0, vous pouvez signer les utilisateurs dans vos applications de page unique avec des comptes personnels et de travail ou aux études de Microsoft.  Page unique et autres applications JavaScript qui exécutent principalement dans une face de navigateur quelques exemples intéressants défis en matière d’authentification :

- Les caractéristiques de sécurité de ces applications sont considérablement différents de celui des applications web serveur traditionnel.
- De nombreux serveurs d’autorisation et les fournisseurs d’identité ne supportent pas les demandes CORS.
- Navigateur de pleine page redirige en dehors de l’application devenue particulièrement invasive de l’expérience utilisateur.

Pour ces applications (pensez : AngularJS, Ember.js, React.js, etc.) AD Azure prend en charge le flux d’octroi implicite de OAuth 2.0.  Le flux implicite est décrit dans la [Spécification de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2).  Son principal avantage est qu’il permet à l’application obtenir des jetons à partir d’AD Azure sans effectuer un serveur principal exchange d’informations d’identification.  Cela permet à l’application de connexion de l’utilisateur, maintenir la session, et jetons d’autre API web du client de code JavaScript.  Il y a quelques points de sécurité importants à prendre en compte lors de l’utilisation du flux implicite - spécifiquement autour de [client](http://tools.ietf.org/html/rfc6749#section-10.3) et [d’emprunt d’identité de l’utilisateur](http://tools.ietf.org/html/rfc6749#section-10.3).

Si vous souhaitez utiliser le flux implicite et AD Azure pour ajouter l’authentification pour votre application JavaScript, nous vous recommandons de qu'utiliser notre bibliothèque JavaScript open source, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Il existe peu de didacticiels AngularJS [ici](active-directory-appmodel-v2-overview.md#getting-started) pour vous aider à démarrer.  

Toutefois, si vous ne souhaitez plus utiliser une bibliothèque dans votre application de page unique et envoyer des messages de protocole vous-même, suivez les étapes générales ci-dessous.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).
    
## <a name="protocol-diagram"></a>Diagramme de protocole
La connexion implicite toute flux ressemble à ceci : chacune des étapes sont décrites en détail ci-dessous.

![OpenId connecter couloirs](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Envoyer la demande de connexion

Initialement, connecte l’utilisateur dans votre application, vous pouvez envoyer une demande d’autorisation [OpenID se connecter](active-directory-v2-protocols-oidc.md) et obtenir un `id_token` à partir de la version 2.0 :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Cliquez sur le lien ci-dessous pour exécuter cette requête ! Une fois connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec un `id_token` dans la barre d’adresses.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Obligatoire | Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et des identificateurs de client.  Pour plus d’informations, consultez [Concepts de base de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| response_type | Obligatoire | Doit inclure `id_token` pour la OpenID se connecter connexion.  Il peut également inclure la response_type `token`. À l’aide de `token` ici permettra à votre application à recevoir un jeton d’accès immédiatement le point de terminaison Autoriser sans avoir à effectuer une deuxième demande au point de terminaison autoriser.  Si vous utilisez la `token` response_type, le `scope` paramètre doit contenir une étendue indiquant quelle ressource pour émettre le jeton pour. |
| redirect_uri | recommandé | La redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de le redirect_uris que vous avez enregistré dans le portail, mais il doit être codé en url. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des étendues.  OpenID de connexion, il doit inclure dans l’étendue `openid`, ce qui correspond à l’autorisation « Connexion vous » dans l’interface utilisateur de consentement.  Si vous le souhaitez vous pouvez également inclure le `email` ou `profile` [étendues](active-directory-v2-scopes.md) afin d’accéder à des données utilisateurs supplémentaires.  Vous pouvez également inclure des autres étendues dans cette requête de demande de consentement à diverses ressources.  |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer le précédent jeton qui en résulte à votre application.  Doit être `fragment` pour le flux implicite.  |
| état | recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher des attaques de type contrefaçon demande entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |
| valeur à usage unique | Obligatoire | Valeur incluse dans la demande, générée par l’application, ce qui sera incluse dans l’id_token résultante sous la forme d’une demande d’indemnisation.  L’application peut ainsi vérifier cette valeur pour atténuer les attaques par relecture de jeton.  La valeur est en général une chaîne aléatoire unique qui peut être utilisée pour identifier l’origine de la demande.  |
| invite de commandes | facultatif | Indique le type d’intervention de l’utilisateur qui est requis.  Les seules valeurs valides pour l’instant sont 'connexion', 'none' et 'accord'.  `prompt=login`force l’utilisateur à entrer leurs informations d’identification sur demande, une inversion connexion unique.  `prompt=none`est le contraire : il veillera à ce que l’utilisateur n’est pas présenté avec n’importe quelle invite interactive que ce soit.  Si la demande ne peut pas être effectuée en mode silencieux via une connexion unique, le point de terminaison v2.0 renvoie une erreur.  `prompt=consent`déclenche la boîte de dialogue de consentement OAuth une fois que l’utilisateur se connecte, demandant à l’utilisateur d’accorder des autorisations à l’application. |
| login_hint | facultatif | Peut être utilisé pour renseigner le champ adresse de nom d’utilisateur/courriel du signe de la page pour l’utilisateur, si vous connaissez leur nom d’utilisateur à l’avance.  Souvent les applications utilisent ce paramètre lors de la ré-authentification, après avoir extrait déjà le nom d’utilisateur à partir d’une précédente connexion à l’aide de la `preferred_username` de revendication. |
| domain_hint | facultatif | Peut être une des `consumers` ou `organizations`.  Si inclus, il ignore le processus de découverte de messagerie électronique utilisateur traverse dans la version 2.0 de connexion page, conduisant à une expérience utilisateur plus rationalisée.  Souvent les applications utilisent ce paramètre lors de la ré-authentification, en extrayant le `tid` à partir de l’id_token de revendication.  Si le `tid` est de valeur de revendication `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Dans le cas contraire, utilisez `domain_hint=organizations`. |

À ce stade, l’utilisateur sera invité à entrer leurs informations d’identification et d’authentification.  Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le `scope` paramètre de requête.  Si l’utilisateur n’a pas consenti à une de ces autorisations, il demande à l’utilisateur à donner son consentement pour les autorisations requises.  Détails des [autorisations, consentement et les applications mutualisées sont fournies ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur s’authentifie et consentement, le point de terminaison v2.0 renverra une réponse à votre application à la liste indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte à l’aide de `response_mode=fragment` et `response_type=id_token+token` présente l’aspect suivant, avec des sauts de ligne pour une meilleure lisibilité :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Incluse en cas `response_type` comprend `token`. Le jeton d’accès que l’application a demandé, dans ce cas pour le Microsoft Graph.  Le jeton d’accès ne doit pas être décodé ou inspecté dans le cas contraire, elle peut être traitée comme une chaîne opaque. |
| token_type | Incluse en cas `response_type` comprend `token`.  Sera toujours `Bearer`. |
| expires_in | Incluse en cas `response_type` comprend `token`.  Indique le nombre de secondes que le jeton est valide, à des fins de mise en cache. |
| champ d’application | Incluse en cas `response_type` comprend `token`.  Indique l’ou les étendues pour laquelle l’access_token sera valide. |
| id_token | Id_token que l’application est demandée. Vous pouvez utiliser l’id_token pour vérifier l’identité de l’utilisateur et de commencer une session avec l’utilisateur.  Plus de détails sur id_tokens et leur contenu est inclus dans la [référence de jeton de point de terminaison v2.0](active-directory-v2-tokens.md).  |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |


#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée :

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification.  |

## <a name="validate-the-idtoken"></a>Valider l’id_token
Seulement recevoir une id_token n’est pas suffisante pour authentifier l’utilisateur. Vous devez valider les signatures de l’id_token et vérifier les revendications dans le jeton par les exigences de votre application.  Le point de terminaison v2.0 utilise les [Jetons de Web JSON (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et de la cryptographie à clé publique pour signer des jetons et vérifier qu’ils sont valides.

Vous pouvez choisir de valider la `id_token` de client, code, mais une pratique courante consiste à envoyer le `id_token` à un serveur principal et d’effectuer la validation il.  Une fois que vous avez validé la signature de l’id_token, il existe quelques demandes que vous devrez vérifier.  Consultez la [référence de jeton de version 2.0](active-directory-v2-tokens.md) pour plus d’informations, y compris des [Jetons de validation](active-directory-v2-tokens.md#validating-tokens) et [d’Importantes informations sur la signature de clé survol](active-directory-v2-tokens.md#validating-tokens).  Nous vous recommandons de faisant usage d’une bibliothèque pour l’analyse et la validation des jetons - au moins est disponible pour la plupart des langages et des plates-formes.
<!--TODO: Improve the information on this-->

Vous souhaiterez peut-être également valider les demandes supplémentaires en fonction de votre scénario.  Certains contrôles communs sont les suivants :

- Assurant l’utilisateur/organisation est associée à l’application.
- Assurant l’utilisateur a des privilèges / d’autorisation
- Garantir une certaine force d’authentification s’est produite, comme l’authentification à plusieurs facteurs.

Pour plus d’informations sur les revendications dans un id_token, consultez la [référence de jeton de point de terminaison v2.0](active-directory-v2-tokens.md).

Une fois que vous avez complètement validé l’id_token, vous pouvez commencer une session avec l’utilisateur et utiliser les revendications dans l’id_token pour obtenir des informations sur l’utilisateur dans votre application.  Ces informations peuvent être utilisées pour l’affichage, des enregistrements, des autorisations, etc..

## <a name="get-access-tokens"></a>Obtenir des jetons d’accès

Maintenant que vous avez connecté l’utilisateur dans votre application d’une page unique, vous pouvez obtenir des jetons d’accès pour le web appel API sécurisées par AD Azure, tel que le [Microsoft Graph](https://graph.microsoft.io).  Même si vous avez déjà reçu d’un jeton à l’aide du `token` response_type, vous pouvez utiliser cette méthode pour acquérir des jetons vers des ressources supplémentaires sans avoir à rediriger l’utilisateur pour vous connecter à nouveau.

Dans le flux normal de OpenID se connecter/OAuth, vous pour ce faire, effectue une demande à la v2.0 `/token` point de terminaison.  Toutefois, le point de terminaison v2.0 ne gère pas les demandes CORS, afin d’effectuer des appels AJAX pour récupérer et actualiser les jetons est hors de question.  Au lieu de cela, vous pouvez utiliser le flux implicite dans un cadre iframe masqué pour obtenir de nouveaux jetons d’autre API de web : 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Essayez de copier et coller la demande dans un onglet du navigateur ci-dessous ! (N’oubliez pas de remplacer le `domain_hint` et le `login_hint` valeurs avec les valeurs correctes pour votre utilisateur)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Obligatoire | Le `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et des identificateurs de client.  Pour plus d’informations, consultez [Concepts de base de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| response_type | Obligatoire | Doit inclure `id_token` pour la OpenID se connecter connexion.  Il peut également inclure d’autres response_types, tel que `code`. |
| redirect_uri | recommandé | La redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de le redirect_uris que vous avez enregistré dans le portail, mais il doit être codé en url. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des étendues.  Les jetons de mise en route, comprennent toutes les [étendues](active-directory-v2-scopes.md) , vous avez besoin pour la ressource concernée.  |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer le précédent jeton qui en résulte à votre application.  Peut être une des `query`, `form_post`, ou `fragment`.  |
| état | recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher des attaques de type contrefaçon demande entre sites.  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |
| valeur à usage unique | Obligatoire | Valeur incluse dans la demande, générée par l’application, ce qui sera incluse dans l’id_token résultante sous la forme d’une demande d’indemnisation.  L’application peut ainsi vérifier cette valeur pour atténuer les attaques par relecture de jeton.  La valeur est en général une chaîne aléatoire unique qui peut être utilisée pour identifier l’origine de la demande.  |
| invite de commandes | Obligatoire | Pour l’actualisation et l’obtention de jetons dans un cadre iframe masqué, vous devez utiliser `prompt=none` pour que l’iframe ne se bloque pas sur la page de connexion v2.0 et retourne immédiatement. |
| login_hint | Obligatoire | Pour l’actualisation et l’obtention de jetons dans un cadre iframe masqué, vous devez inclure le nom d’utilisateur de l’utilisateur dans cet indicateur afin de faire la distinction entre plusieurs sessions, que l’utilisateur peut avoir à un moment donné dans le temps. Vous pouvez extraire le nom d’utilisateur à partir d’une précédente connexion à l’aide de la `preferred_username` de revendication. |
| domain_hint | Obligatoire | Peut être une des `consumers` ou `organizations`.  Pour l’actualisation et l’obtention de jetons dans un cadre iframe masqué, vous devez inclure la domain_hint dans la demande.  Vous devez extraire le `tid` réclamer à l’id_token d’une précédente sign-in pour déterminer la valeur à utiliser.  Si le `tid` est de valeur de revendication `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Dans le cas contraire, utilisez `domain_hint=organizations`. |

Grâce à la `prompt=none` paramètre, cette demande soit réussisse ou échoue immédiatement et revenir à votre application.  Recevrez une réponse correcte à votre application à la liste indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre.

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte à l’aide de `response_mode=fragment` ressemble à :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton qui a demandé l’application. |
| token_type | Sera toujours `Bearer`. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |
| expires_in | La durée pendant laquelle le jeton d’accès est valide (en secondes). |
| champ d’application | Le jeton d’accès est valide pour les étendues. |

#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée.  Dans le cas de `prompt=none`, une erreur attendue sera :

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification.  |

Si vous recevez cette erreur dans la demande de l’iframe, l’utilisateur doit interactivement reconnectez-vous récupérer un nouveau jeton.  Vous pouvez choisir de gérer ce cas dans la façon de sens pour votre application.

## <a name="refreshing-tokens"></a>L’actualisation des jetons

Les deux `id_token`s et `access_token`s expire après un laps de temps, afin que votre application doit être préparée pour actualiser ces jetons périodiquement.  Pour actualiser le type de jeton, vous pouvez effectuer la même demande de cadre iframe masqué dessus à l’aide de la `prompt=none` paramètre pour contrôler le comportement de l’annonce Azure.  Si vous souhaitez recevoir un nouveau `id_token`, veillez à utiliser `response_type=id_token` et `scope=openid`, ainsi qu’un `nonce` paramètre.


## <a name="send-a-sign-out-request"></a>Envoyer une demande de déconnexion

La OpenIdConnect `end_session_endpoint` n’est pas actuellement pris en charge par le point de terminaison v2.0. Cela signifie que votre application ne peut pas envoyer une requête au point de terminaison v2.0 pour mettre fin à une session utilisateur et effacer les cookies définis par le point de terminaison v2.0.
Pour signer un utilisateur out, votre application peut simplement mettre fin à sa propre session de l’utilisateur et laissez la session de l’utilisateur avec le point de terminaison v2.0-intacts.  La prochaine fois que l’utilisateur essaie de se connecter, ils verront une page « Choisir compte », avec leurs activement signé dans des comptes répertoriés.
Sur cette page, l’utilisateur peut choisir pour vous déconnecter de tous les comptes, fin de la session avec le point de terminaison v2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->