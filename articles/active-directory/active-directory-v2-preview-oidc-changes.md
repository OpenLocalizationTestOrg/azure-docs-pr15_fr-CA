<properties
    pageTitle="Devient le point de terminaison v2.0 AD Azure | Microsoft Azure"
    description="Une description des modifications apportées aux protocoles de présentation publique app modèle v2.0."
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

# <a name="important-updates-to-the-v20-authentication-protocols"></a>Mises à jour importantes des version 2.0 des protocoles d’authentification
Développeurs d’attention ! Sur les deux prochaines semaines, nous allons apporter quelques mises à jour de nos protocoles d’authentification v2.0 qui peuvent signifier Nouveautés pour toutes les applications que vous avez écrit pendant la période d’aperçu.  

## <a name="who-does-this-affect"></a>Qui cela affecte-t-il ?
Les applications qui ont été écrites pour utiliser la version 2.0 a convergé de point de terminaison de l’authentification,

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Vous trouverez plus d’informations sur le point de terminaison v2.0 [ici](active-directory-appmodel-v2-overview.md).

Si vous avez créé une application en utilisant le point de terminaison v2.0 en codant directement sur le protocole de la version 2.0, à l’aide de notre middlewares web OpenID connecter ou OAuth, ou à l’aide d’autres bibliothèques tierces 3e pour effectuer l’authentification, vous devez savoir pour tester vos projets et apporter des modifications si nécessaire.

## <a name="who-doesnt-this-affect"></a>Qui ce n’affecte ?
Les applications qui ont été écrits sur le point de terminaison de l’authentification AD Azure de production,

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Ce protocole est et ne fait pas l’objet des modifications.

En outre, si de votre application **uniquement** utilise notre bibliothèque ADAL pour effectuer l’authentification, vous aurez pas à modifier quoi que ce soit.  ADAL a blindage de votre application à partir des modifications.  

## <a name="what-are-the-changes"></a>Quelles sont les modifications ?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Suppression de la valeur de x5t à partir des en-têtes JWT
Le point de terminaison v2.0 utilise les jetons JWT largement, qui contiennent une section de paramètres d’en-tête aux métadonnées pertinentes sur le jeton.  Si vous de décoder l’en-tête de l’un de nos JWTs en cours, vous trouveriez quelque chose comme :

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Où les propriétés de la « x5t » et « enfant » identifient la clé publique à utiliser pour valider la signature du jeton récupéré à partir de l’extrémité de métadonnées OpenID se connecter.

Le changement que nous faisons ici est pour supprimer la propriété « x5t ».  Vous pouvez continuer à utiliser les mêmes mécanismes pour valider des jetons, mais que vous devez compter uniquement sur la propriété « enfant » pour récupérer la clé publique appropriée, comme spécifié dans le protocole OpenID de se connecter. 

> [AZURE.IMPORTANT] **Votre travail : Assurez-vous que votre application ne dépend pas de l’existence de la valeur de x5t.**

### <a name="removing-profileinfo"></a>Suppression de profile_info
Auparavant, le point de terminaison v2.0 a été retournant un objet JSON de codées en base64 dans les réponses de jetons appelés `profile_info`.  Lorsqu’elle demande un jeton d’accès à partir de la version 2.0 en envoyant une requête à :

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

La réponse doit ressembler à l’objet JSON suivant :
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Le `profile_info` des informations de valeur contenue sur l’utilisateur qui est connecté à l’application - leur nom complet, prénom, nom, adresse de messagerie, identificateur et ainsi de suite.  Essentiellement, le `profile_info` a été utilisé pour la mise en cache de jeton et à des fins d’affichage.

Nous sommes en train de supprimer le `profile_info` valeur – mais ne vous inquiétez pas, nous vous fournissons toujours ces informations pour les développeurs dans un emplacement légèrement différent.  Au lieu de `profile_info`, le point de terminaison v2.0 va à présent retourner un `id_token` dans chaque réponse de jeton :

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Vous pouvez décoder et analyser les id_token pour extraire les mêmes informations que vous avez reçu à partir de profile_info.  L’id_token est un JSON Web jeton (JWT), avec le contenu spécifié par OpenID de se connecter.  Le code de cette procédure devrait donc être très similaire : il suffit d’extraire le segment central (le corps) de l’id_token et de décodage base64 pour accéder à l’objet JSON dans.

Sur les deux prochaines semaines, vous devez coder votre application pour récupérer les informations utilisateur à partir du `id_token` ou `profile_info`; Si elle est présente.  De cette façon, lorsque la modification est effectuée, votre application peut gérer en toute transparence de la transition à partir de `profile_info` à `id_token` sans interruption.

> [AZURE.IMPORTANT] **Votre travail : Assurez-vous que votre application ne dépend pas de l’existence de la `profile_info` valeur.**

### <a name="removing-idtokenexpiresin"></a>Suppression d’id_token_expires_in
Semblable à `profile_info`, nous supprimons également les `id_token_expires_in` paramètre à partir des réponses.  Auparavant, le point de terminaison v2.0 renverrait la valeur pour `id_token_expires_in` avec chaque réponse id_token, par exemple dans une réponse d’autoriser :

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Ou dans une réponse de jeton :

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Le `id_token_expires_in` valeur indique le nombre de secondes qui restent valides pour l’id_token.  Maintenant, nous supprimons la `id_token_expires_in` complètement la valeur.  Au lieu de cela, vous pouvez utiliser la norme OpenID connecter `nbf` et `exp` de revendications pour examiner la validité d’un id_token.  Reportez-vous à la [référence de jeton de version 2.0](active-directory-v2-tokens.md) pour plus d’informations sur ces revendications.

> [AZURE.IMPORTANT] **Votre travail : Assurez-vous que votre application ne dépend pas de l’existence de la `id_token_expires_in` valeur.**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>Modification des créances renvoyés par scope = openid
Cette modification sera plus importantes – en fait, il affecte presque chaque application qui utilise le point de terminaison v2.0.  De nombreuses applications envoient des demandes pour le point de terminaison de version 2.0 à l’aide de la `openid` étendue, comme :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Aujourd'hui, lorsque l’utilisateur accorde l’autorisation pour le `openid` étendue, votre application reçoit une mine d’informations sur l’utilisateur dans l’id_token qui en résulte.  Ces demandes peuvent inclure leur nom, nom d’utilisateur par défaut, adresse de messagerie, ID de l’objet et bien plus encore.

Cette mise à jour, nous modifions les informations qui le `openid` étendue permet l’accès de l’application, à comform mieux avec la spécification OpenID se connecter.  Le `openid` étendue uniquement signer l’utilisateur dans votre application et de recevoir un identificateur spécifique à l’application de l’utilisateur dans le `sub` de l’id_token de revendication.  Les revendications dans un id_token avec uniquement les `openid` étendue accordée sera dépourvues d’informations personnellement identifiables.  Demandes d’id_token exemple sont les suivants :

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Si vous souhaitez obtenir des informations d’identification personnelle (PII) sur l’utilisateur dans votre application, votre application devez demander des autorisations supplémentaires à partir de l’utilisateur.  Nous vous présentons aujourd'hui la prise en charge de deux nouvelles étendues à partir de la spécification OpenID se connecter à la `email` et `profile` étendues – ce qui vous permet de le faire.

- Le `email` étendue est très simple : il permet l’accès de votre application à l’adresse de messagerie principale de l’utilisateur via la `email` de revendication dans l’id_token.  Notez que la `email` demande pas seront toujours présent dans id_tokens, il sera inclus uniquement s’il est disponible dans le profil utilisateur.
- Le `profile` étendue permet l’accès de l’application à toutes les autres informations de base sur l’utilisateur, le nom, le nom d’utilisateur par défaut, ID d’objet et ainsi de suite.

Cela vous permet de coder votre application de façon minimale-divulgation – vous pouvez demander à l’utilisateur pour l’ensemble des informations dont votre application a besoin pour effectuer son travail.  Si vous souhaitez poursuivre la mise en route de l’ensemble des informations d’utilisateur que votre application reçoit actuellement, vous devez inclure tous les trois étendues dans vos demandes d’autorisation :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Votre application peut commencer à envoyer les `email` et `profile` étendues immédiatement et le point de terminaison v2.0 acceptera ces deux portées et commencer à demander les autorisations d’utilisateurs que nécessaire.  Toutefois, la modification de l’interprétation de la `openid` étendue ne prendront effet que pendant quelques semaines.

> [AZURE.IMPORTANT] **Votre travail : ajouter la `profile` et `email` étendues si votre application requiert des informations sur l’utilisateur.**  Notez que ADAL inclura ces autorisations dans les requêtes par défaut. 

### <a name="removing-the-issuer-trailing-slash"></a>Suppression de l’émetteur de barre oblique de fin.
Auparavant, la valeur de l’émetteur qui apparaît dans les jetons à partir de la version 2.0 a pris la forme

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Dans le cas où le guid a été la tenantId de la publicité Azure client qui a émis le jeton.  Grâce à ces modifications, la valeur d’émetteur devient

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

dans les deux jetons et dans le document de découverte OpenID se connecter.

> [AZURE.IMPORTANT] **Votre travail : Assurez-vous que votre application accepte la valeur d’émetteur avec et sans barre oblique de fin lors de la validation de l’émetteur.**

## <a name="why-change"></a>Pourquoi changer ?
La motivation principale pour l’introduction de ces modifications est conforme à la spécification de la norme OpenID se connecter.  En étant OpenID connecter conforme, nous espérons que réduire les différences entre l’intégration avec les services d’identité de Microsoft et avec d’autres services d’identité de l’industrie.  Nous souhaitons permettent aux développeurs d’utiliser leurs bibliothèques d’authentification OpenSource Favoris sans avoir à modifier les bibliothèques pour tenir compte des différences de Microsoft.

## <a name="what-can-you-do"></a>Que pouvez-vous faire ?
À compter d’aujourd'hui, vous pouvez commencer à apporter toutes les modifications décrites ci-dessus.  Vous devez immédiatement :

1.  **Supprimez toutes les dépendances sur le `x5t` paramètre d’en-tête.**
2.  **Gérer correctement la transition à partir de `profile_info` à `id_token` dans les réponses de jetons.**
3.  **Supprimez toutes les dépendances sur le `id_token_expires_in` paramètre de réponse.**
3.  **Ajouter la `profile` et `email` étendues à votre application, si votre application a besoin d’informations utilisateur de base.**
4.  **Accepter les valeurs de l’émetteur de jetons avec et sans barre oblique de fin.**

Notre [documentation de protocole v2.0](active-directory-v2-protocols.md) a déjà été mis à jour pour refléter ces modifications, donc vous pouvez l’utiliser comme référence pour aider à la mise à jour de votre code.

Si vous avez d’autres questions sur l’étendue des modifications, n’hésitez pas à être en contact avec nous sur Twitter à @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>La fréquence des modifications de protocole se produit ?
Nous ne prévoient pas toute rupture remplace les protocoles d’authentification.  Nous avons intentionnellement regroupement ces modifications dans une version afin que vous n’aurez pas à accéder à ce type de processus de mise à jour de si tôt à nouveau.  Bien entendu, nous continuerons à ajouter des fonctionnalités au service d’authentification v2.0 de convergence que vous pouvez tirer parti de, mais ces modifications doivent être additives et saut pas de code existant.

Enfin, nous souhaitons dire Merci d’essayer des choses au cours de la période d’aperçu.  Les idées et expériences de nos premiers ont été précieuses jusqu’ici, et nous espérons que vous continuerez à partager vos opinions et idées.

Amusez-vous bien !

La Division de l’identité de Microsoft
