<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Les types de jetons émis dans l’Active Directory B2C du Azure."
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


# <a name="azure-ad-b2c-token-reference"></a>Azure B2C d’annonce : Référence de jeton

Azure B2C d’Active Directory (AD Azure) émet plusieurs types de jetons de sécurité lorsqu’il traite chaque [flux d’authentification](active-directory-b2c-apps.md). Ce document décrit les caractéristiques de sécurité et la valeur de chaque type de jeton.

## <a name="types-of-tokens"></a>Types de jetons

Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md), qui utilise des jetons d’accès et des jetons de l’actualisation. Il prend également en charge l’authentification et l’authentification à l’aide de [OpenID de se connecter](active-directory-b2c-reference-protocols.md), qui introduit un troisième type de jeton : le jeton d’ID. Chacun de ces jetons est représenté sous la forme d’un jeton de support.

Un jeton de support est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Le porteur est toute partie qui peut présenter le jeton. Annonce Azure doit tout d’abord authentifier une partie avant de pouvoir recevoir un jeton de support. Mais, si les mesures ne sont pas prises pour sécuriser le jeton dans la transmission et le stockage, il peut être interceptée et utilisée par une partie involontaire. Des jetons de sécurité possèdent un mécanisme intégré pour empêchant des tiers de leur utilisation, mais les jetons porteur ne possèdent pas ce mécanisme. Ils doivent être transportés dans un canal sécurisé, tels que la sécurité de couche transport (HTTPS).

Si un jeton de support est transmis à l’extérieur d’un canal sécurisé, une partie malveillante permet une attaque man-in-the-middle acquérir le jeton et l’utiliser pour accéder à une ressource protégée. Les mêmes principes de sécurité s’appliquent lorsque les jetons de support sont stockés ou mis en cache pour une utilisation ultérieure. Assurez-vous toujours que votre application transmet et stocke les jetons des porteur de manière sécurisée.

Supplémentaires en matière de sécurité sur les jetons de support, consultez la [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons qui émet des Azure AD B2C sont implémentées en tant que jetons de web JSON (JWTs). Un JWT est un moyen compact et sécurisés pour les URL de transfert d’informations entre les deux parties. JWTs contiennent des informations connues que les créances. Il s’agit des assertions d’informations sur le support et le sujet du jeton. Les revendications contenues dans JWTs sont des objets JSON qui sont codés et sérialisés pour la transmission. Étant donné que les JWTs émis par Azure AD B2C sont signés mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un JWT pour le déboguer. Plusieurs outils sont disponibles que pour cela, y compris [calebb.net](http://calebb.net). Pour plus d’informations sur JWTs, reportez-vous aux [spécifications de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Jetons d’ID

Un jeton d’ID est une forme de jeton de sécurité que votre application reçoit le AD B2C Azure `authorize` et `token` points de terminaison. Jetons d’ID sont représentés en tant que [JWTs](#types-of-tokens)et ils contiennent des déclarations que vous pouvez utiliser pour identifier les utilisateurs dans votre application. Lorsque des jetons d’ID sont acquis à partir de la `authorize` point de terminaison, ils sont souvent utilisés pour connecter les utilisateurs aux applications web. Lorsque des jetons d’ID sont acquis à partir de la `token` point de terminaison, elles peuvent être envoyées dans les demandes HTTP lors de la communication entre les deux composants de la même application ou service. Vous pouvez utiliser les revendications dans un jeton d’identification comme vous le souhaitez. Elles sont utilisées pour afficher des informations de compte ou de prendre des décisions de contrôle d’accès dans une application.  

Jetons d’ID sont signés, mais ils ne sont pas cryptés pour l’instant. Lorsque votre application ou une API reçoit un jeton d’ID, elle doit [valider la signature](#token-validation) afin de prouver que le jeton est authentique. Votre application ou l’API doit valider également quelques déclarations du jeton de prouver qu’elle est valide. En fonction des exigences du scénario, les revendications validées par une application peuvent varier, mais votre application doit effectuer certaines [validations de demande commun](#token-validation) dans tous les scénarios.

#### <a name="sample-id-token"></a>Jeton de code exemple
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Jetons d’accès

Un jeton d’accès est également une forme de jeton de sécurité que votre application reçoit le AD B2C Azure `authorize` et `token` points de terminaison. Les jetons d’accès sont également représentés en tant que [JWTs](#types-of-tokens), et ils contiennent des déclarations que vous pouvez utiliser pour identifier les utilisateurs dans vos services web et les API.

Les jetons d’accès sont signés, mais ils ne sont pas cryptés en ce moment - et très similaire aux jetons de code.  Les jetons d’accès doivent être utilisés pour accéder aux services web et des API et d’identifier et d’authentifier l’utilisateur dans les services.  Toutefois, elles ne fournissent pas toute assertion d’autorisation à ces services.  C'est-à-dire que les `scp` les revendications dans des jetons d’accès ne limite ni ne représentent l’accès de l’objet du jeton a été accordée.

Lorsque votre API reçoit un jeton d’accès, il doit [valider la signature](#token-validation) afin de prouver que le jeton est authentique. Votre API doit également valider plusieurs revendications du jeton de prouver qu’elle est valide. En fonction des exigences du scénario, les revendications validées par une application peuvent varier, mais votre application doit effectuer certaines [validations de demande commun](#token-validation) dans tous les scénarios.

### <a name="claims-in-id--access-tokens"></a>Revendications dans des jetons d’accès et d’identification

Lorsque vous utilisez Azure AD B2C, vous avez un contrôle précis sur le contenu de vos jetons. Vous pouvez configurer des [stratégies](active-directory-b2c-reference-policies.md) pour envoyer certains jeux de données de l’utilisateur dans les revendications dont votre application a besoin pour ses opérations. Ces demandes peuvent inclure des propriétés standard, telles que l’utilisateur `displayName` et `emailAddress`. Ils peuvent également inclure des [attributs d’utilisateurs personnalisés](active-directory-b2c-reference-custom-attr.md) que vous pouvez définir dans votre répertoire B2C. Chaque ID et l’accès au jeton que vous recevez contient un ensemble de déclarations de liés à la sécurité. Vos applications peuvent utiliser ces déclarations s’authentifier les utilisateurs et les demandes.

Notez que les revendications dans des jetons de code ne sont pas retournées dans un ordre particulier. En outre, des nouvelles demandes peuvent être introduites dans les jetons de l’ID à tout moment. Votre application ne devrait pas altérer comme de nouvelles demandes sont introduites. Voici les demandes que vous attendez dans access et identification des jetons émis par Azure AD B2C. Toute réclamation supplémentaire sera déterminée par les stratégies. Pour les exercices pratiques, essayez d’inspecter les revendications dans le jeton d’ID exemple en la collant dans [calebb.net](http://calebb.net). Vous trouverez plus de détails dans la [spécification de OpenID de se connecter](http://openid.net/specs/openid-connect-core-1_0.html).

| Nom | Demande d’indemnisation | Exemple de valeur | Description |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Public | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Une demande d’assistance identifie le destinataire prévu du jeton. Pour Azure B2C d’Active Directory, le public est ID de l’Application de votre application, affecté à votre application dans le portail de l’enregistrement d’app. Votre application doit valider cette valeur et rejeter le jeton si elle ne correspond pas. |
| Émetteur | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Cet argument identifie le service de jeton de sécurité (STS) qui construit et retourne le jeton. Il identifie également le répertoire AD Azure dans lequel l’utilisateur a été authentifié. Votre application doit valider la demande de l’émetteur afin de garantir que le jeton fourni à partir de la version 2.0. |
| Délivrée à | `iat` | `1438535543` | Cette demande est l’heure à laquelle le jeton a été délivré, représentés dans le temps de l’époque. |
| Délai d’expiration | `exp` | `1438539443` | Le délai d’expiration revendication est l’heure à laquelle le jeton est non valide, représenté dans heure de l’époque. Votre application doit utiliser cette demande de vérifier la validité de la durée de vie de jeton.  |
| Pas avant | `nbf` | `1438535543` | Cette demande est l’heure à laquelle le jeton est valide, représenté dans le temps de l’époque. Cela est généralement le même que l’heure que le jeton a été délivré. Votre application doit utiliser cette demande de vérifier la validité de la durée de vie de jeton.  |
| Version | `ver` | `1.0` | Il s’agit de la version du jeton ID, tel que défini par AD Azure. |
| Code hachage | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hachage du code est inclus dans un jeton d’identification uniquement lorsque le jeton est émis avec un code d’autorisation OAuth 2.0. Un hachage du code peut être utilisé pour valider l’authenticité d’un code d’autorisation. Consultez la [spécification de OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus de détails sur la façon d’effectuer cette validation. |
| Hachage token d’accès | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hachage de jeton d’accès est inclus dans un jeton d’identification uniquement lorsque le jeton est émis avec un jeton d’accès OAuth 2.0. Un hachage de jeton d’accès peut être utilisé pour valider l’authenticité d’un jeton d’accès. Consultez la [spécification de OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus de détails sur la façon d’effectuer cette validation. |
| Valeur à usage unique | `nonce` | `12345` | Une nonce est une stratégie permettant d’atténuer les attaques par relecture de jeton. Votre application peut spécifier une valeur unique dans une demande d’autorisation à l’aide de la `nonce` paramètre de requête. La valeur que vous fournissez dans la demande sera émise non modifiée dans le `nonce` d’un jeton d’ID de revendication. Cela permet à votre application vérifier la valeur par rapport à la valeur qu’il spécifiée sur la demande, qui associe la session de l’application avec un jeton d’ID donné. Votre application doit effectuer cette validation pendant le processus de validation de jeton d’identité. |
| Objet | `sub` | `Not supported currently. Use oid claim.` | Il s’agit d’une entité sur laquelle le jeton déclare des informations, telles que l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réaffectée ou réutilisés. Il peut être utilisé pour effectuer des vérifications d’autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Toutefois, la demande de l’objet n’est pas encore implémentée dans le AD B2C Azure. Vous devez configurer vos stratégies pour inclure l’ID d’objet `oid` de revendication et sa valeur permet d’identifier les utilisateurs, plutôt que d’utiliser la revendication de l’objet d’autorisation. |
| Référence de classe de contexte de l’authentification | `acr` | `b2c_1_sign_in` | C’est le nom de la stratégie qui a été utilisé pour obtenir le jeton d’ID.  |
| Moment de l’authentification | `auth_time` | `1438535543` | Cette demande est l’heure à laquelle un utilisateur dernière saisie d’informations d’identification, représentée dans le temps de l’époque. |


### <a name="refresh-tokens"></a>Actualiser les jetons

Actualiser les jetons sont des jetons de sécurité que votre application peut utiliser pour acquérir de nouveaux jetons de code et accéder aux jetons dans un flux OAuth 2.0. Ils fournissent votre application avec l’accès aux ressources à long terme pour le compte des utilisateurs sans nécessiter une interaction avec les utilisateurs.

Pour recevoir une actualisation de jetons dans une jeton réponse, votre application doit demander le `offline_acesss` étendue. Pour en savoir plus sur les `offline_access` étendue, reportez-vous à la [référence au protocole Azure AD B2C](active-directory-b2c-reference-protocols.md).

Actualiser les jetons sont et sera toujours, complètement opaque à votre application. Ils sont émis par AD Azure et peuvent être inspectés et interprétées que par AD Azure. Ils sont durables, mais si votre application ne doit pas être écrite dans l’attente que dure un jeton d’actualisation pour une période spécifique. Jetons d’actualisation peuvent être invalidées à tout moment pour diverses raisons. Le seul moyen pour votre application, de savoir si un jeton d’actualisation est valide consiste à essayer pour le rembourser en effectuant une demande de jeton à AD Azure.

Lorsque vous utiliser un jeton d’actualisation pour un nouveau jeton (et si votre application a été accordée le `offline_access` étendue), vous recevrez un nouveau jeton d’actualisation dans la réponse de jeton. Vous devez enregistrer le jeton d’actualisation nouvellement émis. Il doit remplacer le jeton d’actualisation que vous avez précédemment utilisé dans la demande. Cela permet de garantir que vos jetons actualisation restent valables aussi longtemps que possible.

## <a name="token-validation"></a>Validation de jeton

Pour valider un jeton, votre application doit vérifier la signature et les revendications du jeton.

De nombreuses bibliothèques open source sont disponibles pour la validation de JWTs, en fonction de la langue de votre choix. Il est recommandé que vous explorez ces options au lieu d’implémentez votre propre logique de validation. Les informations contenues dans ce guide peuvent vous aider à apprendre à utiliser correctement ces bibliothèques.

### <a name="validate-the-signature"></a>Valider la signature
Un JWT contient trois segments, séparés par le `.` caractère. Le premier segment est l' **en-tête**, le second est le **corps**et le troisième est la **signature**. Le segment de la signature peut être utilisé pour valider l’authenticité du jeton de sorte qu’elle peut être approuvé par votre application.

Les jetons AD B2C Azure sont signés à l’aide d’algorithmes standard de cryptage asymétrique, tel que RSA 256. L’en-tête du jeton contient des informations sur la méthode de cryptage et la clé utilisée pour signer le jeton :

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Le `alg` demande indique l’algorithme utilisé pour signer le jeton. Le `kid` demande indique la clé publique qui a été utilisée pour signer le jeton.

À un moment donné, AD Azure peut-être signer un jeton à l’aide de l’un d’un certain ensemble de paires de clés publique-privée. Azure AD fait pivoter le jeu possible de clés périodiquement, afin que votre application doit être écrite pour gérer ces modifications clées automatiquement. Une fréquence raisonnable pour vérifier les mises à jour les clés publiques utilisées par Active Directory Azure est de 24 heures.

Azure AD B2C a un point de terminaison de métadonnées OpenID se connecter. Ceci permet aux applications d’extraire des informations sur Azure AD B2C lors de l’exécution. Ces informations incluent des points de terminaison, le contenu de jetons et les clés de signature de jetons. Votre répertoire B2C contient un document de métadonnées JSON pour chaque stratégie. Par exemple, le document de métadonnées pour le `b2c_1_sign_in` stratégie de la `fabrikamb2c.onmicrosoft.com` se trouve dans :

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`est le répertoire B2C utilisé pour authentifier l’utilisateur, et `b2c_1_sign_in` est la stratégie utilisée pour obtenir le jeton. Pour déterminer la stratégie qui a été utilisé pour signer un jeton (et où aller pour extraire les métadonnées), vous disposez de deux options. Tout d’abord, le nom de la stratégie est inclus dans le `acr` de revendication dans le jeton. Vous pouvez analyser les créances en dehors du corps de la JWT en base-64, le corps de décodage et désérialise la chaîne JSON qui résulte. Le `acr` demande sera le nom de la stratégie qui a été utilisé pour émettre le jeton.  L’autre option consiste à coder la stratégie dans la valeur de la `state` paramètre lorsque vous émettez la demande, puis le décoder pour déterminer la stratégie qui a été utilisé. Les deux méthodes sont valide.

Le document de métadonnées est un objet JSON qui contient plusieurs informations utiles. Citons notamment l’emplacement des points de terminaison requis pour effectuer l’authentification OpenID de se connecter. Ils incluent également des `jwks_uri`, qui indique l’emplacement de l’ensemble des clés publiques qui sont utilisés pour signer les jetons. Qu’emplacement est indiqué ici, mais il est préférable d’extraire dynamiquement de l’emplacement à l’aide du document de métadonnées et de l’analyse des `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Le document JSON situé à cette URL contient toutes les informations de clé publique utilisé à un moment donné. Votre application peut utiliser la `kid` de revendication dans l’en-tête JWT pour sélectionner la clé publique dans le document JSON est utilisé pour signer un jeton donné. Il pouvez ensuite effectuer la validation de la signature à l’aide de la clé publique appropriée et l’algorithme spécifié.

Une description de la façon d’effectuer la validation de signature est en dehors de la portée de ce document. De nombreuses bibliothèques open source sont disponibles pour vous aider si vous en avez besoin.

### <a name="validate-the-claims"></a>Valider les revendications
Lorsque votre application ou une API reçoit un jeton d’identification, il doit également effectuer plusieurs vérifications contre les revendications dans le jeton d’ID. Celles-ci incluent, mais ne sont pas limitées à :

- La demande de **l’audience** : Cela permet de vérifier que le jeton d’ID a été destiné à donner à votre application.
- Les créances **non avant** et le **délai d’expiration** : ces vérifier que le jeton d’ID n’a pas expiré.
- La demande de **l’émetteur** : Cela permet de vérifier que le jeton a été délivré pour votre application par AD Azure.
- La **valeur à usage unique**: il s’agit d’une stratégie d’atténuation d’attaque une réexécution de jeton.

Pour obtenir une liste complète des contrôles de que votre application doit effectuer, reportez-vous à la [spécification de OpenID de se connecter](https://openid.net). Détails des valeurs attendues pour ces revendications sont inclus dans la précédente [section jeton](#types-of-tokens).  

## <a name="token-lifetimes"></a>Durée de vie des jetons

La durée de vie de jeton suivantes est fournies à vos connaissances. Ils peuvent vous aider lorsque vous développez et déboguez des applications. Notez que vos applications ne doivent pas être écrites à attendre une de ces durées de vie reste constante. Ils peuvent et changent.  Vous pouvez en savoir plus sur la personnalisation des durées de vie jetons dans Azure AD B2C [ici](active-directory-b2c-token-session-sso.md).

| Jeton | Durée de vie | Description |
| ----------------------- | ------------------------------- | ------------ |
| Jetons d’ID | Une heure | Jetons d’ID sont généralement valides pendant une heure. Votre application web peut utiliser cette durée de vie pour gérer ses propres sessions avec des utilisateurs (recommandés). Vous pouvez également choisir une durée de vie de session différent. Si votre application a besoin obtenir un nouvel ID de jeton, il doit simplement faire une nouvelle demande de connexion à Active Directory Azure. Si un utilisateur a une session de navigateur valide avec AD Azure, cet utilisateur ne peut pas tenu à entrer à nouveau les informations d’identification. |
| Actualiser les jetons | Jusqu'à 14 jours | Un jeton unique d’actualisation est valide pour un maximum de 14 jours. Toutefois, un jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit continuer à essayer d’utiliser un jeton d’actualisation jusqu'à ce que la demande échoue, ou jusqu'à ce que votre application remplace le jeton de l’actualiser avec un autre.  Un jeton d’actualisation peut devenir non valide si 90 jours s’est écoulé depuis l’utilisateur entré en dernier des informations d’identification. |
| Codes d’autorisation | Cinq minutes | Codes d’autorisation sont intentionnellement de courte durées. Ils doivent être échangés immédiatement pour les jetons d’accès, les jetons de l’ID ou jetons d’actualisation lors de leur réception. |
