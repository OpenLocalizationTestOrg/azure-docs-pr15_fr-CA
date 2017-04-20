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

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure B2C de répertoire actif : Flux de code d’autorisation OAuth 2.0

L’octroi de code d’autorisation OAuth 2.0 peut être utilisé dans les applications qui sont installées sur un périphérique d’accéder à des ressources protégées, telles que le web API. À l’aide de l’implémentation d’Azure Active Directory (AD Azure) B2C OAuth 2.0, vous pouvez ajouter des tâches de gestion d’identité d’inscription, inscription et d’autres à vos applications mobiles et de bureau. Ce guide est indépendante du langage. Il décrit comment envoyer et recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

<!-- TODO: Need link to libraries -->

Le flux de code d’autorisation OAuth 2.0 est décrite au [point 4.1 de la spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749). Vous pouvez l’utiliser pour effectuer l’authentification et l’autorisation dans la plupart des types d’application, y compris les [applications web](active-directory-b2c-apps.md#web-apps) et [installé en mode natif les applications](active-directory-b2c-apps.md#mobile-and-native-apps). Il permet d’acquérir en toute sécurité des **access_tokens**, qui peut être utilisé pour accéder aux ressources qui sont sécurisées par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics), les applications.

Ce guide se concentre sur une version particulière d’OAuth 2.0 d’autorisation code flux--**clients publics**. Un client public est toute application cliente qui ne peut pas être approuvée en toute sécurité de préserver l’intégrité du mot de passe secret. Cela inclut les applications mobiles, applications de bureau et pratiquement n’importe quelle application qui s’exécute sur un périphérique et a besoin d’obtenir access_tokens. Si vous souhaitez ajouter la gestion des identités pour une application web à l’aide d’Azure AD B2C, vous devez utiliser [OpenID connecter](active-directory-b2c-reference-oidc.md) plutôt que OAuth 2.0.

Azure AD B2C étend la norme Qu'oauth 2.0 flux pour faire plus que la simple authentification et d’autorisation. Il présente le [**paramètre de stratégie**](active-directory-b2c-reference-policies.md), qui vous permet d’utiliser OAuth 2.0 pour ajouter des expériences utilisateur pour votre application, telles que d’abonnement, connexion et gestion des profils. Ici, nous allons montrer comment utiliser OAuth 2.0 et les stratégies de mise en œuvre de ces expériences dans vos applications natives. Nous allons également montrer comment obtenir access_tokens pour accéder à web API.

Les demandes HTTP exemple ci-dessous utilise notre exemple de répertoire B2C, **fabrikamb2c.onmicrosoft.com**, ainsi que notre exemple d’application et les stratégies. Vous êtes libre d’essayer les demandes vous-même à l’aide de ces valeurs, ou vous pouvez les remplacer par les vôtres.
Découvrez comment [obtenir votre propre répertoire B2C, application et les stratégies](#use-your-own-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. obtenez un code d’autorisation
Le flux de code d’autorisation commence avec le client qui redirige l’utilisateur vers le `/authorize` point de terminaison. Ceci est la partie interactive du flux, où l’utilisateur aura réellement action. Dans cette demande, le client indique les autorisations dont il a besoin d’acquérir de l’utilisateur dans le `scope` paramètre et la stratégie d’exécution dans le `p` paramètre. Trois exemples sont fournis ci-dessous (avec les sauts de ligne pour une meilleure lisibilité), chacun à l’aide d’une stratégie différente.

#### <a name="use-a-sign-in-policy"></a>Utiliser une stratégie d’authentification

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utiliser une stratégie d’inscription

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utiliser une stratégie de modifier le profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| identifiant_client | Obligatoire | L’ID d’application [Azure portal](https://portal.azure.com) affecté à votre application. |
| response_type | Obligatoire | Le type de réponse, qui doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri | Obligatoire | La redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application. Il doit correspondre exactement à un de le redirect_uris que vous avez enregistré dans le portail, sauf qu’il doit être codé en URL. |
| champ d’application | Obligatoire | Une liste séparée par des espaces des étendues. Une valeur de portée unique indique à Azure annonces à la fois des autorisations qui sont demandés. À l’aide de l’ID de client comme la portée indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou les API web, représenté par le même ID de client.  Le `offline_access` étendue indique que votre application doit un **refresh_token** pour l’accès aux ressources à long terme.  Vous pouvez également utiliser le `openid` étendue pour demander une **id_token** d’Azure AD B2C. |
| response_mode | Recommandé | La méthode doit être utilisée pour renvoyer les authorization_code qui en résulte à votre application. Il peut être une des 'query', 'form_post' ou 'fragment'.
| état | Recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton. Il peut être une chaîne de tout contenu que vous souhaitez. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher des attaques de type contrefaçon demande entre sites. L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page qu’ils étaient sur ou la stratégie en cours d’exécution. |
| p | Obligatoire | La stratégie qui sera exécutée. C’est le nom d’une stratégie qui est créé dans votre répertoire B2C. La valeur de nom de stratégie doit commencer par « b2c\_1\_». Pour en savoir plus sur les stratégies dans le [cadre de politique Extensible](active-directory-b2c-reference-policies.md). |
| invite de commandes | Facultatif | Le type d’intervention de l’utilisateur qui est requis. La seule valeur valide pour l’instant est 'connexion', ce qui oblige l’utilisateur à entrer leurs informations d’identification sur cette requête. L’ouverture de session unique ne prendre effet. |

À ce stade, l’utilisateur sera invité à terminer le flux de travail de la stratégie. Cela peut impliquer l’utilisateur entrer leur nom d’utilisateur et le mot de passe, la signature avec une identité sociale, s’inscrire pour le répertoire, ou tout autre nombre d’étapes, en fonction de la façon dont la stratégie est définie.

Une fois que l’utilisateur termine la stratégie, AD Azure renverra une réponse à votre application à la liste indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre. La réponse sera exactement la même pour chacun de ces cas, indépendamment de la stratégie qui a été exécutée.

Une réponse correcte qui utilise `response_mode=query` ressemble à :

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Authorization_code que l’application est demandée. L’application peut utiliser le code d’autorisation pour demander un access_token pour une ressource cible. Authorization_codes sont de très courte durée de vie. En général, ils expirent après 10 minutes environ. |
| état | Voir la description complète dans le tableau précédent. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |

Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application peut traiter de manière appropriée :

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification. |
| état | Voir la description complète de la première table dans cette section. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et la réponse sont identiques. |


## <a name="2-get-a-token"></a>2. Obtention d’un jeton
Maintenant que vous avez acquis un authorization_code, vous pouvez utiliser la `code` pour obtenir un jeton à la ressource souhaitée par l’envoi d’un `POST` demande à la `/token` point de terminaison. Dans Azure B2C d’Active Directory, la seule ressource dont vous pouvez demander un jeton est votre site web principal de l’application API. La convention qui est utilisée pour demander un jeton à vous-même consiste à utiliser des ID de client de votre application en tant que la portée :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obligatoire | La stratégie qui a été utilisée pour obtenir le code d’autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, pas dans le corps de la publication. |
| identifiant_client | Obligatoire | L’ID d’application [Azure portal](https://portal.azure.com) affecté à votre application. |
| grant_type | Obligatoire | Le type de subvention, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| champ d’application | Recommandés | Une liste séparée par des espaces des étendues. Une valeur de portée unique indique à Azure annonces à la fois des autorisations qui sont demandés. À l’aide de l’ID de client comme la portée indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou les API web, représenté par le même ID de client.  Le `offline_access` étendue indique que votre application doit un **refresh_token** pour l’accès aux ressources à long terme.  Vous pouvez également utiliser le `openid` étendue pour demander une **id_token** d’Azure AD B2C. |
| code | Obligatoire | Authorization_code que vous avez acheté dans la première branche du flux. |
| redirect_uri | Obligatoire | Le redirect_uri de l’application à laquelle vous avez reçu l’authorization_code. |

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
| access_token | Le jeton jeton JSON Web (JWT) signé que vous avez demandée. |
| champ d’application | Les étendues que le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée pendant laquelle le jeton est valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours. Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès à des ressources sur de longues périodes de temps. Pour plus de détails, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). |

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

## <a name="3-use-the-token"></a>3. Utilisez le jeton
Maintenant que vous avez acquis avec succès une `access_token`, vous pouvez utiliser le jeton dans les demandes de votre back-end web API en l’incluant dans les `Authorization` en-tête :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Actualisez le jeton
Jetons d’accès & Id sont de courte durée de vie. Vous devez les actualiser après leur expiration pour continuer à accéder aux ressources. Vous pouvez le faire en envoyant un autre `POST` demande à la `/token` point de terminaison. Ce temps fournir le `refresh_token` à la place de la `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | -------- |
| p | Obligatoire | La stratégie qui a été utilisée pour obtenir le refresh_token d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, pas dans le corps de la publication. |
| identifiant_client | Recommandé | L’ID d’application [Azure portal](https://portal.azure.com) affecté à votre application. |
| grant_type | Obligatoire | Le type de subvention, qui doit être `refresh_token` pour cette branche du flux de code d’autorisation. |
| champ d’application | Recommandé | Une liste séparée par des espaces des étendues. Une valeur de portée unique indique à Azure annonces à la fois des autorisations qui sont demandés. À l’aide de l’ID de client comme la portée indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou les API web, représenté par le même ID de client.  Le `offline_access` étendue indique que votre application doit un **refresh_token** pour l’accès aux ressources à long terme.  Vous pouvez également utiliser le `openid` étendue pour demander une **id_token** d’Azure AD B2C. |
| redirect_uri | Facultatif | Le redirect_uri de l’application à laquelle vous avez reçu l’authorization_code. |
| refresh_token | Obligatoire | Le refresh_token d’origine que vous avez acheté dans la deuxième branche du flux. |

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
| access_token | Le jeton jeton JSON Web (JWT) signé que vous avez demandée. |
| champ d’application | Les étendues que le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée pendant laquelle le jeton est valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours. Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès à des ressources sur de longues périodes de temps. Pour plus de détails, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). |

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

## <a name="use-your-own-b2c-directory"></a>Utilisez votre propre répertoire B2C

Si vous souhaitez essayer ces demandes par vous-même, vous devez tout d’abord effectuer ces trois étapes et ensuite de remplacer les valeurs de l’exemple ci-dessus avec votre propre :

- [Créer un répertoire B2C](active-directory-b2c-get-started.md) et utiliser le nom de votre répertoire dans les requêtes.
- [Création d’une application](active-directory-b2c-app-registration.md) pour obtenir un ID d’application et un redirect_uri. Vous devez inclure un **client natif** dans votre application.
- Pour obtenir les noms de votre stratégie de [créer vos stratégies](active-directory-b2c-reference-policies.md) .
