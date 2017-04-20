<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Création d’applications web à l’aide de l’implémentation d’Azure Active Directory du protocole d’authentification OpenID de se connecter."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure B2C de répertoire actif : Web reconnectez-vous avec connexion de OpenID

OpenID se connecter est un protocole d’authentification basé sur OAuth 2.0, qui peut être utilisé pour déconnecter des utilisateurs en toute sécurité dans les applications web.  À l’aide de la mise en oeuvre de B2C d’Azure Active Directory (AD Azure) de se connecter de OpenID, vous pouvez sous-traiter d’abonnement, connexion, et autres tâches de gestion d’identité des expériences dans vos applications web vers Azure AD. Ce guide va vous montrer comment faire d’une manière indépendante du langage. Il décrit comment envoyer et recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

[OpenID connecter](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *d’autorisation* OAuth 2.0 pour une utilisation en tant que protocole *d’authentification* . Cela vous permet d’effectuer une ouverture de session unique à l’aide de OAuth. Elle introduit le concept d’une `id_token`, qui est un jeton de sécurité qui permet au client de vérifier l’identité de l’utilisateur et d’obtenir des informations de profil de base de l’utilisateur.

Car elle étend OAuth 2.0, elle permet également d’acquérir en toute sécurité des **access_tokens**des applications. Vous pouvez utiliser access_tokens pour accéder aux ressources qui sont sécurisées par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics). Nous vous recommandons de OpenID se connecter si vous créez une application web qui est hébergée sur un serveur et accessible via un navigateur. Si vous souhaitez ajouter la gestion des identités à vos applications mobiles ou de bureau à l’aide d’Azure AD B2C, vous devez utiliser [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) plutôt qu’une connexion de OpenID.

Azure AD B2C étend le protocole standard de OpenID se connecter pour faire plus que la simple authentification et d’autorisation. Il présente le [**paramètre de stratégie**](active-directory-b2c-reference-policies.md), qui vous permet d’utiliser OpenID vous connecter pour ajouter des expériences utilisateur pour votre application, telles que d’abonnement, connexion et gestion des profils. Ici, nous allons vous montrer comment utiliser OpenID se connecter et stratégies de mise en œuvre de ces expériences dans vos applications web. Nous vous montrerons également comment obtenir access_tokens pour accéder à web API.

Les demandes HTTP exemple ci-dessous utilise notre exemple de répertoire B2C, **fabrikamb2c.onmicrosoft.com**, ainsi que notre exemple application **https://aadb2cplayground.azurewebsites.net** et les stratégies. Vous êtes libre d’essayer les demandes vous-même à l’aide de ces valeurs, ou vous pouvez les remplacer par les vôtres.
Découvrez comment [obtenir votre propre client de B2C, application et les stratégies](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Envoyer les demandes d’authentification
Lorsque votre application web doit authentifier l’utilisateur et exécuter une stratégie, elle peut diriger l’utilisateur vers le `/authorize` point de terminaison. C’est la partie interactive du flux, où l’utilisateur aura en fait l’action, en fonction de la stratégie.

Dans cette demande, le client indique les autorisations dont il a besoin d’acquérir de l’utilisateur dans le `scope` paramètre et la stratégie d’exécution dans le `p` paramètre. Trois exemples sont fournis ci-dessous (avec les sauts de ligne pour une meilleure lisibilité), chacun à l’aide d’une stratégie différente. Pour avoir une idée du fonctionne de chaque demande, essayez de coller la demande dans un navigateur et en l’exécutant.

#### <a name="use-a-sign-in-policy"></a>Utiliser une stratégie d’authentification

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utiliser une stratégie d’inscription

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utiliser une stratégie de modifier le profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| identifiant_client | Obligatoire | L’ID d’application [Azure portal](https://portal.azure.com/) affecté à votre application. |
| response_type | Obligatoire | Le type de réponse, qui doit inclure `id_token` OpenID de connexion. Si votre application web aussi ont besoin de jetons pour l’appel d’une API web, vous pouvez utiliser `code+id_token`, comme nous l’avons fait ici. |
| redirect_uri | Recommandé | La redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application. Il doit correspondre exactement à un de le redirect_uris que vous avez enregistré dans le portail, sauf qu’il doit être codé en URL. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des étendues. Une valeur de portée unique indique à Azure annonces à la fois des autorisations qui sont demandés. Le `openid` étendue indique une autorisation à l’utilisateur ouvrir une session et d’obtenir des données relatives à l’utilisateur sous la forme **id_tokens** (plus d’informations à venir sur ce ultérieurement dans cet article). Le `offline_access` étendue est facultatif pour les applications web. Il indique que votre application doit un **refresh_token** pour l’accès aux ressources à long terme. |
| response_mode | Recommandé | La méthode doit être utilisée pour renvoyer les authorization_code qui en résulte à votre application. Il peut être une des 'query', 'form_post' ou 'fragment'.  'form_post' est recommandé pour une sécurité optimale. |
| état | Recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton. Il peut être une chaîne de tout contenu que vous souhaitez. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher des attaques de type contrefaçon demande entre sites. L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page qu’ils étaient sur. |
| valeur à usage unique | Obligatoire | Valeur incluse dans la demande (générée par l’application) qui est incluse dans l’id_token résultante sous la forme d’une demande d’indemnisation. L’application peut ainsi vérifier cette valeur pour atténuer les attaques par relecture de jeton. La valeur est en général une chaîne aléatoire unique qui peut être utilisée pour identifier l’origine de la demande. |
| p | Obligatoire | La stratégie qui sera exécutée. C’est le nom d’une stratégie qui est créé dans votre client B2C. La valeur de nom de stratégie doit commencer par « b2c\_1\_». Pour en savoir plus sur les stratégies dans le [cadre de politique Extensible](active-directory-b2c-reference-policies.md). |
| invite de commandes | Facultatif | Le type d’intervention de l’utilisateur qui est requis. La seule valeur valide pour l’instant est 'connexion', ce qui oblige l’utilisateur à entrer leurs informations d’identification sur cette requête. L’ouverture de session unique ne prendre effet. |

À ce stade, l’utilisateur sera invité à terminer le flux de travail de la stratégie. Cela peut impliquer l’utilisateur entrer leur nom d’utilisateur et le mot de passe, la signature avec une identité sociale, s’inscrire pour le répertoire, ou tout autre nombre d’étapes, en fonction de la façon dont la stratégie est définie.

Une fois que l’utilisateur termine la stratégie, AD Azure renverra une réponse à votre application à la liste indiquée `redirect_uri`, à l’aide de la méthode qui est spécifiée dans le `response_mode` paramètre. La réponse sera exactement la même pour chacun de ces cas, indépendamment de la stratégie qui a été exécutée.

Une réponse correcte à l’aide de `response_mode=fragment` ressemblerait à :

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id_token | Id_token que l’application est demandée. Vous pouvez utiliser l’id_token pour vérifier l’identité de l’utilisateur et de commencer une session avec l’utilisateur. Plus de détails sur id_tokens et leur contenu sont inclus dans la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| code | L’authorization_code qui a demandé l’application, si vous avez utilisé `response_type=code+id_token`. L’application peut utiliser le code d’autorisation pour demander un access_token pour une ressource cible. Authorization_codes sont de très courte durée de vie. En général, ils expirent après 10 minutes environ. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |

Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée :

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification. |
| état | Voir la description complète dans le tableau précédent. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |


## <a name="validate-the-idtoken"></a>Valider l’id_token
Seulement recevoir une id_token n’est pas suffisant pour authentifier l’utilisateur, vous devez valider les signatures de l’id_token et vérifier les revendications dans le jeton selon les exigences de votre application. Azure AD B2C utilise les [Jetons de Web JSON (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et de la cryptographie à clé publique pour signer des jetons et vérifier qu’ils sont valides.

Il existe de nombreuses bibliothèques open source qui sont disponibles pour la validation de JWTs, en fonction de votre langue de préférence. Nous vous recommandons d’exploration de ces options, plutôt que d’implémenter votre propre logique de validation. Ces informations seront utiles pour savoir comment utiliser correctement ces bibliothèques.

Azure AD B2C a une extrémité de métadonnées OpenID connecter, qui permet à une application d’extraire des informations sur Azure AD B2C lors de l’exécution. Ces informations incluent des points de terminaison, le contenu de jetons et les clés de signature de jetons. Il existe un document de métadonnées pour chaque stratégie dans vos clients B2C JSON. Par exemple le document de métadonnées pour le `b2c_1_sign_in` stratégie de la `fabrikamb2c.onmicrosoft.com` se trouve dans :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

L’une des propriétés de ce document de configuration est la `jwks_uri`, dont la valeur pour la même stratégie serait :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Afin de déterminer la stratégie qui a été utilisée lors de la signature d’un id_token (et où extraire les métadonnées à partir de), vous disposez de deux options. Tout d’abord, le nom de la stratégie est inclus dans le `acr` de revendication dans l’id_token. Pour plus d’informations sur la façon d’analyser les revendications à partir d’un id_token, consultez la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md). L’autre option consiste à coder la stratégie dans la valeur de la `state` paramètre lorsque vous émettez la demande, puis le décoder pour déterminer la stratégie qui a été utilisé. Les deux méthodes sont parfaitement valide.

Une fois que vous avez acquis le document de métadonnées à partir de l’extrémité de métadonnées OpenID se connecter, vous pouvez utiliser les clés publiques RSA 256 (qui se trouvent au niveau de ce point de terminaison) pour valider la signature de l’id_token. Il peut y avoir plusieurs clés répertoriés à ce point de terminaison à un moment donné dans le temps, chacune étant identifiée par une `kid`. L’en-tête de l’id_token contient également un `kid` qui indique laquelle de ces clés a été utilisé pour signer l’id_token de la revendication. Consultez la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md) pour plus d’informations, y compris les sections sur la [validation des jetons](active-directory-b2c-reference-tokens.md#validating-tokens) et [des informations importantes à propos de la substitution de la clé de signature](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Une fois que vous avez validé la signature de l’id_token, il existe plusieurs créances dont vous aurez besoin pour vérifier, par exemple :

- Vous devez valider le `nonce` de revendication empêcher les attaques par relecture de jeton. Sa valeur doit être spécifiée dans la demande d’ouverture de session.
- Vous devez valider le `aud` de revendication pour s’assurer que l’id_token a été émis pour votre application. Sa valeur doit être l’ID d’application de votre application.
- Vous devez valider la `iat` et `exp` de revendications pour s’assurer que l’id_token n’a pas expiré.

Il existe également plusieurs validations plus à exécuter, décrites en détail dans les [Spécifications de base OpenID de se connecter](http://openid.net/specs/openid-connect-core-1_0.html).  Vous pourriez également valider d’autres plaintes, en fonction de votre scénario. Certains contrôles communs sont les suivants :

- S’assurer que l’utilisateur/organisation est associée à l’application.
- S’assurer que l’utilisateur dispose d’autorisations et de privilèges appropriés.
- S’assurer qu’une certaine force d’authentification s’est produite, telles que l’authentification multifacteur Azure.

Pour plus d’informations sur les revendications dans un id_token, consultez la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md).

Après avoir validé l’id_token complètement, vous pouvez commencer une session avec l’utilisateur et utiliser les revendications dans l’id_token pour obtenir des informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, d’autorisation et ainsi de suite.

## <a name="get-a-token"></a>Obtenir un jeton.
Si votre application web doit également faire qu’exécuter des stratégies, vous pouvez ignorer les sections suivantes. Ces sections sont applique uniquement aux applications qui doivent faire authentifié d’appels à une API web et sont également protégées par Azure AD B2C web.

Vous pouvez utiliser l’authorization_code que vous avez acheté (à l’aide de `response_type=code+id_token`) pour obtenir un jeton à la ressource souhaitée par l’envoi d’un `POST` demande à la `/token` point de terminaison. Actuellement, la seule ressource dont vous pouvez demander un jeton est votre site web principal de l’application API. La convention pour demander un jeton à vous-même consiste à utiliser des ID de client de votre application en tant que la portée :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obligatoire | La stratégie qui a été utilisée pour obtenir le code d’autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la **chaîne de requête**, pas dans le corps de la publication. |
| identifiant_client | Obligatoire | L’ID d’application [Azure portal](https://portal.azure.com/) affecté à votre application. |
| grant_type | Obligatoire | Le type de subvention, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| champ d’application | Recommandé | Une liste séparée par des espaces des étendues. Une valeur de portée unique indique à Azure annonces à la fois des autorisations qui sont demandés. Le `openid` étendue indique une autorisation à l’utilisateur ouvrir une session et d’obtenir des données relatives à l’utilisateur sous la forme de **id_tokens**. Elle peut être utilisée pour obtenir des jetons à votre site web principal de l’application API, ce qui est représenté par le même ID que le client. Le `offline_access` étendue indique que votre application doit un **refresh_token** pour l’accès aux ressources à long terme. |
| code | Obligatoire | Authorization_code que vous avez acheté dans la première branche du flux. |
| redirect_uri | Obligatoire | Le redirect_uri de l’application à laquelle vous avez reçu l’authorization_code. |
| client_secret | Obligatoire | Le secret de l’application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Le secret de cette application est un artefact de sécurité important. Vous devez le stocker en toute sécurité sur votre serveur. Vous devez également veiller à faire pivoter ce mot de passe du client sur une base périodique. |

Une réponse correcte de jeton ressemble à :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, dans le temps de l’époque. |
| token_type | La valeur du jeton de type. Le seul type qui prend en charge les annonces Azure est porteur. |
| access_token | Le jeton JWT signé que vous avez demandée. |
| champ d’application | Les étendues que le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée pendant laquelle l’access_token est valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours.  Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès à des ressources sur de longues périodes de temps. Pour plus de détails, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). Notez que vous devez avoir utilisé l’étendue `offline_access` dans l’autorisation et les demandes de jetons pour recevoir un refresh_token. |

Réponses d’erreur ressemblera à :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification. |

## <a name="use-the-token"></a>Utiliser le jeton
Maintenant que vous avez acquis avec succès une `access_token`, vous pouvez utiliser le jeton dans les demandes de votre back-end web API en l’incluant dans les `Authorization` en-tête :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualiser le jeton
Les id_tokens sont de courte durée de vie. Vous devez les actualiser après leur expiration pour continuer à accéder aux ressources. Vous pouvez le faire en envoyant un autre `POST` demande à la `/token` point de terminaison. Cette fois, fournir le `refresh_token` à la place de la `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Obligatoire | Description |
| ----------------------- | ------------------------------- | -------- |
| p | Obligatoire | La stratégie qui a été utilisée pour obtenir le refresh_token d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la **chaîne de requête**, pas dans le corps de la publication. |
| identifiant_client | Obligatoire | L’ID d’application [Azure portal](https://portal.azure.com/) affecté à votre application. |
| grant_type | Obligatoire | Le type de subvention, qui doit être `refresh_token` pour cette branche du flux de code d’autorisation. |
| champ d’application | Recommandé | Une liste séparée par des espaces des étendues. Une valeur de portée unique indique à Azure annonces à la fois des autorisations qui sont demandés. Le `openid` étendue indique une autorisation à l’utilisateur ouvrir une session et d’obtenir des données relatives à l’utilisateur sous la forme de **id_tokens**. Elle peut être utilisée pour obtenir des jetons à votre site web principal de l’application API, ce qui est représenté par le même ID que le client. Le `offline_access` étendue indique que votre application doit un **refresh_token** pour l’accès aux ressources à long terme. |
| redirect_uri | Recommandé | Le redirect_uri de l’application à laquelle vous avez reçu l’authorization_code. |
| refresh_token | Obligatoire | Le refresh_token d’origine que vous avez acheté dans la deuxième branche du flux. Notez que vous devez avoir utilisé l’étendue `offline_access` dans l’autorisation et les demandes de jetons pour recevoir un refresh_token. |
| client_secret | Obligatoire | Le secret de l’application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Le secret de cette application est un artefact de sécurité important. Vous devez le stocker en toute sécurité sur votre serveur. Vous devez également veiller à faire pivoter ce mot de passe du client sur une base périodique. |

Une réponse correcte de jeton ressemble à :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, dans le temps de l’époque. |
| token_type | La valeur du jeton de type. Le seul type qui prend en charge les annonces Azure est porteur. |
| access_token | Le jeton JWT signé que vous avez demandée. |
| champ d’application | Les étendues que le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée pendant laquelle l’access_token est valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours.  Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès à des ressources sur de longues périodes de temps. Pour plus de détails, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). |

Réponses d’erreur ressemblera à :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification. |


## <a name="send-a-sign-out-request"></a>Envoyer une demande de déconnexion

Lorsque vous souhaitez vous connecter à l’utilisateur de l’application, il n’est pas suffisante effacer les cookies de votre application ou à la fin de sinon la session avec l’utilisateur. Vous devez également rediriger l’utilisateur vers Azure AD pour vous déconnecter. Si vous ne parvenez pas à le faire, l’utilisateur peut être en mesure d’authentifier une nouvelle fois à votre application sans entrer de nouveau leurs informations d’identification. C’est parce qu’ils auront une unique session session valide avec AD Azure.

Vous pouvez rediriger tout simplement de l’utilisateur de la `end_session_endpoint` qui figure dans le même document de métadonnées OpenID connecter décrit précédemment dans la section « Valider l’id_token » :

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | ------------ |
| p | Obligatoire | La stratégie que vous souhaitez utiliser pour vous connecter à l’utilisateur de votre application. |
| post_logout_redirect_uri | Recommandé | L’URL vers laquelle l’utilisateur doit être redirigé vers après réussite déconnexion. Si elle n’est pas inclus, l’utilisateur s’affichera un message générique par Azure AD B2C.  |

> [AZURE.NOTE]
    Tout en demandant à l’utilisateur de la `end_session_endpoint` efface certains état l’utilisateur unique de session avec Azure AD B2C, il ne signe pas l’utilisateur de la session d’utilisateur identité sociale fournisseur (IDP). Si l’utilisateur sélectionne la même IDP pendant un signe suivantes, ils seront être authentifiés, sans entrer leurs informations d’identification. Si un utilisateur souhaite vous déconnecter de votre application B2C, il ne signifie pas nécessairement que souhaite se déconnecter de son compte Facebook entièrement. Toutefois, pour les comptes locaux, le nom de session sera terminée correctement.

## <a name="use-your-own-b2c-tenant"></a>Utilisez vos propres clients B2C

Si vous souhaitez essayer ces demandes par vous-même, vous devez tout d’abord effectuer ces trois étapes et ensuite de remplacer les valeurs de l’exemple ci-dessus avec votre propre :

- [Créer un client B2C](active-directory-b2c-get-started.md)et utiliser le nom de vos clients dans les requêtes.
- [Création d’une application](active-directory-b2c-app-registration.md) pour obtenir un ID d’application et un redirect_uri. Vous souhaiterez inclure un **web application/web api** dans votre application et éventuellement créer un **secret de l’application**.
- Pour obtenir les noms de votre stratégie de [créer vos stratégies](active-directory-b2c-reference-policies.md) .
