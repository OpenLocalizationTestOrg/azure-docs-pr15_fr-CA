<properties
    pageTitle="Référence de jeton de version 2.0 de publicité Azure | Microsoft Azure"
    description="Les types de jetons et créances émis par le point de terminaison v2.0"
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="v20-token-reference"></a>référence de jeton v2.0

Le point de terminaison v2.0 émet plusieurs types de jetons de sécurité dans le traitement de chaque [flux d’authentification](active-directory-v2-flows.md). Ce document décrit les caractéristiques de sécurité et la valeur de chaque type de jeton.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Types de jetons

Le point de terminaison version 2.0 prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-v2-protocols.md), qui utilise à la fois access_tokens et refresh_tokens.  Il prend également en charge l’authentification et l’authentification à l’aide de [OpenID de se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow), qui introduit un troisième type de jeton, l’id_token.  Chacun de ces jetons est représenté sous la forme d’un « jeton de support ».

Un jeton de support est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce sens, le « porteur » est toute partie qui peut présenter le jeton. Si une partie doit tout d’abord authentifier avec AD Azure pour recevoir le jeton au porteur, si les mesures ne sont pas prises pour sécuriser le jeton dans la transmission et le stockage, peuvent être interceptée et utilisée par une partie involontaire. Des jetons de sécurité ont un mécanisme intégré pour empêchant des tiers de leur utilisation, les jetons de support n’ont pas ce mécanisme et doivent être transportés dans un canal sécurisé, tels que la sécurité de la couche transport (HTTPS). Si un jeton de support est transmis en clair, un homme de l’attaque peut servir par une partie malveillante pour obtenir le jeton et l’utiliser pour un accès non autorisé à une ressource protégée. Les mêmes principes de sécurité s’appliquent lors du stockage ou de la mise en cache des jetons de support pour une utilisation ultérieure. Assurez-vous toujours que votre application transmet et stocke les jetons des porteur de manière sécurisée. Plus en matière de sécurité sur les jetons de support, consultez la [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par le point de terminaison v2.0 sont implémentées comme des jetons de Web Json ou JWTs.  Un JWT est un moyen compact et sécurisés pour les URL de transfert d’informations entre les deux parties.  Les informations contenues dans JWTs sont appelées « affirmations », ou assertions d’informations sur le support et l’objet du jeton.  Les revendications contenues dans JWTs sont des objets JSON codés et sérialisés pour la transmission.  Dans la mesure où les JWTs émis par le point de terminaison v2.0 sont signés mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un JWT pour le débogage. Pour plus d’informations sur JWTs, vous pouvez faire référence à la [spécification de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens est une forme de jeton de sécurité de connexion qui reçoit de votre application lors de l’authentification à l’aide de [OpenID de se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Ils sont représentés sous forme de [JWTs](#types-of-tokens)et contient des déclarations que vous pouvez utiliser pour la signature de l’utilisateur dans votre application.  Vous pouvez utiliser les revendications dans un id_token comme vous le souhaitez - qu’ils sont utilisés pour l’affichage des informations de compte ou de prendre des décisions de contrôle d’accès dans une application.  Le point de terminaison v2.0 émet uniquement un seul type d’id_token, qui a un jeu cohérent de créances, quel que soit le type d’utilisateur qui est connecté.  C’est à dire que le format et le contenu de l’id_tokens sera le même pour les deux utilisateurs Microsoft Account personnels et comptes de travail ou l’école.

Id_tokens sont signés, mais pas crypté à ce moment.  Lorsque votre application reçoit un id_token, il doit [valider la signature](#validating-tokens) pour prouver l’authenticité du jeton et valider plusieurs revendications du jeton pour prouver sa validité.  Les revendications validées par une application varient en fonction des exigences du scénario, mais il existe certaines [validations de créance commune](#validating-tokens) que votre application doit effectuer dans chaque scénario.

Plus d’informations sur les revendications dans id_tokens sont indiquées ci-dessous, ainsi que d’un exemple id_token.  Notez que les revendications contenues dans id_tokens ne sont pas retournés dans un ordre particulier.  De plus, les nouvelles demandes peuvent être introduites dans id_tokens à n’importe quel point dans le temps, votre application ne devrait pas altérer comme de nouvelles demandes sont introduites.  La liste ci-dessous inclut les déclarations que votre application peut interpréter de façon fiable au moment de la rédaction de ce document.  Si nécessaire, vous pouvez trouver encore plus en détail dans la [spécification de OpenID de se connecter](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Exemple id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] Pour les exercices pratiques, essayez d’inspecter les revendications dans l’id_token de l’échantillon en le collant dans [calebb.net](https://calebb.net).

#### <a name="claims-in-idtokens"></a>Demandes d’id_tokens
| Nom | Demande d’indemnisation | Exemple de valeur | Description |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Public | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Identifie le destinataire prévu du jeton.  Dans id_tokens, le public est Id de l’Application de votre application, tel qu’assigné à votre application dans le portail de l’enregistrement d’app.  Votre application doit valider cette valeur et rejeter le jeton si elle ne correspond pas. |
| Émetteur | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Identifie le service de jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le locataire AD Azure dans lequel l’utilisateur a été authentifié.  Votre application doit valider la demande de l’émetteur afin de garantir que le jeton fourni à partir de la version 2.0.  Il doit également utiliser le GUID de la revendication pour limiter l’ensemble de clients qui sont autorisés à se connecter à l’application.  Le guid qui indique l’utilisateur est un utilisateur grand public de Microsoft compte est `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Délivrée à | `iat` | `1452285331` | L’heure à laquelle le jeton a été délivré, représentés dans le temps de l’époque. |
| Délai d’expiration | `exp` | `1452289231` | L’heure à laquelle le jeton est non valide, représentés dans le temps de l’époque.  Votre application doit utiliser cette demande de vérifier la validité de la durée de vie de jeton.  |
| Pas avant | `nbf` | `1452285331` |  L’heure à laquelle le jeton est valide, représentés dans le temps de l’époque. Il est généralement le même que l’heure d’émission.  Votre application doit utiliser cette demande de vérifier la validité de la durée de vie de jeton.  |
| Version | `ver` | `2.0` | La version de l’id_token, tel que défini par AD Azure.  Pour le point de terminaison version 2.0, la valeur sera `2.0`. |
| Id de clients | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Guid représentant l’annonce Azure client qui provient de l’utilisateur.  Pour les comptes de travail et à l’école, le guid sera l’ID du locataire immuable de l’organisation à laquelle appartient l’utilisateur.  Pour les comptes personnels, la valeur sera `9188040d-6c67-4c5b-b112-36a304b66dad`.  Le `profile` étendue est requis pour bénéficier de cette demande. |
| Code hachage | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | La valeur de hachage du code est inclus dans l’id_tokens uniquement lorsque l’id_token est émis avec un code d’autorisation OAuth 2.0.  Il peut être utilisé pour valider l’authenticité d’un code d’autorisation.  Consultez la [spécification de OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus de détails sur l’exécution de cette validation. |
| Hachage Token d’accès | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Le hachage de jeton d’accès est inclus dans l’id_tokens uniquement lorsque l’id_token est émis avec un jeton d’accès OAuth 2.0.  Il peut être utilisé pour valider l’authenticité d’un jeton d’accès.  Consultez la [spécification de OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus de détails sur l’exécution de cette validation. |
| Valeur à usage unique | `nonce` | `12345` | La valeur unique est une stratégie pour atténuer les attaques par relecture de jeton.  Votre application peut spécifier une valeur unique dans une demande d’autorisation à l’aide de la `nonce` paramètre de requête.  La valeur que vous fournissez dans la demande sera émise dans l’id_token `nonce` demande d’indemnisation, non modifié.  Cela permet à votre application vérifier la valeur par rapport à la valeur qu’il est spécifié sur la demande, qui associe la session de l’application avec un id_token donné.  Votre application doit effectuer cette validation au cours du processus de validation d’id_token. |
| Nom | `name` | `Babe Ruth` | La revendication de nom fournit une valeur de lecture humaine qui identifie le sujet du jeton. Cette valeur n’est pas garantie pour être unique est mutable et est conçue pour être utilisé uniquement à des fins d’affichage.  Le `profile` étendue est requis pour bénéficier de cette demande. |
| Messagerie | `email` | `thegreatbambino@nyy.onmicrosoft.com` | L’adresse de messagerie principale associé au compte utilisateur, s’il en existe.  Sa valeur est mutable et peut changer pour un utilisateur donné dans le temps.  Le `email` étendue est requis pour bénéficier de cette demande. |
| Nom d’utilisateur par défaut | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | Le nom d’utilisateur principal qui est utilisé pour représenter l’utilisateur dans le point de terminaison v2.0.  Il peut être une adresse électronique, numéro de téléphone ou un nom d’utilisateur générique sans une mise en forme spécifiée.  Sa valeur est mutable et peut changer pour un utilisateur donné dans le temps.  Le `profile` étendue est requis pour bénéficier de cette demande. |
| Objet | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | L’entité de sécurité sur lequel le jeton déclare des informations, telles que l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réassignée ou réutilisé, par conséquent, il peut être utilisé pour effectuer des vérifications d’autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Car l’objet est toujours présent dans les jetons, les problèmes d’annonce d’Azure, il est recommandé à l’aide de cette valeur dans un système d’autorisation général. |
| ObjectId | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Objet Id du compte de l’école ou de travail dans le système d’Azure.  Cette demande ne sera pas émise pour des comptes personnels Microsoft.  Le `profile` étendue est requis pour bénéficier de cette demande. |


## <a name="access-tokens"></a>Jetons d’accès

Les jetons d’accès émises par le point de terminaison v2.0 ne sont à ce stade consommables par les Services Microsoft.  Vos applications ne devez pas effectuer de validation ni contrôle des jetons d’accès pour tous les scénarios pris en charge.  Vous pouvez traiter les jetons d’accès comme complètement opaque : ils sont seulement les chaînes dont votre application peut passer à Microsoft dans les demandes HTTP.

Dans un avenir proche, le point de terminaison v2.0 présente la capacité de votre application de recevoir des jetons d’accès à partir d’autres clients.  À ce stade, cette information sera mis à jour avec les informations de que votre application doit exécuter une validation de jeton d’accès et d’autres tâches similaires.

Lorsque vous demandez un jeton d’accès à partir de la version 2.0, le point de terminaison v2.0 renvoie également des métadonnées sur le jeton d’accès pour la consommation de votre application.  Ces informations comprennent la date d’expiration du jeton d’accès et les étendues pour lesquels il est valide.  Cela permet à votre application pour exécuter intelligente de la mise en cache des jetons d’accès sans avoir à analyser ouvrir le jeton d’accès lui-même.

## <a name="refresh-tokens"></a>Actualiser les jetons

Actualiser les jetons sont des jetons de sécurité dont votre application peut utiliser pour acquérir de nouveau accéder à jetons dans un flux OAuth 2.0.  Il permet à votre application d’obtenir l’accès aux ressources à long terme pour le compte d’un utilisateur sans nécessiter une interaction par l’utilisateur.

Actualiser les jetons sont des ressources multiples.  C'est-à-dire le qu’un jeton d’actualisation reçu lors d’une demande de jeton pour une ressource peut être échangé pour les jetons d’accès à une ressource complètement différente.

Pour recevoir une actualisation dans une réponse de jeton, votre application doit demander et bénéficier de la `offline_acesss` étendue.   Pour en savoir plus sur les `offline_access` étendue, reportez-vous à l' [article consentement et étendues](active-directory-v2-scopes.md).

Actualiser les jetons sont et sera toujours, complètement opaque à votre application.  Ils sont émis par le point de terminaison v2.0 Azure AD et peuvent uniquement être inspectés & interprétés par le point de terminaison v2.0.  Ils sont durables, mais si votre application ne doit pas être écrites à attendre que dure un jeton d’actualisation pour une période donnée.  Jetons d’actualisation peuvent être invalidées à tout moment dans le temps pour diverses raisons.  Le seul moyen pour votre application, de savoir si un jeton d’actualisation est valide consiste à essayer pour le rembourser en effectuant une demande de jeton pour le point de terminaison v2.0.

Lorsque vous utiliser un jeton d’actualisation pour un nouveau jeton d’accès (et si votre application a été accordée le `offline_access` étendue), vous recevrez un nouveau jeton d’actualisation dans la réponse de jeton.  Vous devez enregistrer le jeton actualisation nouvellement émis, remplaçant celui que vous avez utilisé dans la demande.  Ceci garantit que vos jetons actualisation restent valables aussi longtemps que possible.

## <a name="validating-tokens"></a>Validation des jetons

À ce stade, la validation de jeton seulement que vos applications ne devez exécuter validation id_tokens.  Pour valider un id_token, votre application doit valider les signatures de l’id_token et des revendications dans l’id_token.

<!-- TODO: Link -->
Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer facilement de validation token - les informations ci-dessous est fournie simplement pour ceux qui souhaitent comprendre le processus sous-jacent.  Il existe également plusieurs bibliothèques open source de partie 3 disponibles pour la validation de JWT - au moins une option pour presque chaque plate-forme et la langue des.

#### <a name="validating-the-signature"></a>Validation de la signature
Un JWT contient trois segments, qui sont séparés par le `.` caractère.  Le premier segment est connu comme l' **en-tête**, le second dans le **corps**et la troisième comme **signature**.  Le segment de signature peut être utilisé pour valider l’authenticité de l’id_token afin qu’il peut être approuvé par votre application.

Id_Tokens sont signés à l’aide d’algorithmes de cryptage asymétrique standard, tel que RSA 256. L’en-tête de l’id_token contient des informations sur la méthode de cryptage et la clé utilisée pour signer le jeton :

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Le `alg` demande indique l’algorithme utilisé pour signer le jeton, while la `kid` demande indique la clé publique qui a été utilisée pour signer le jeton.

À un moment donné dans le temps, le point de terminaison v2.0 peut-être signer un id_token à l’aide de l’un d’un certain ensemble de paires de clés publique-privée.  Le point de terminaison v2.0 fait pivoter de l’ensemble de clés sur une base périodique, le possible afin que votre application doit être écrite pour gérer ces modifications clées automatiquement.  Une fréquence raisonnable pour vérifier les mises à jour les clés publiques utilisées par le point de terminaison v2.0 est 24 heures.

Vous pouvez acquérir les données de clé signature nécessaires pour valider la signature en utilisant le document de métadonnées OpenID se connecter à :

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Essayez cette URL dans un navigateur !

Ce document de métadonnées est un objet JSON qui contient plusieurs informations, telles que l’emplacement des différents points de terminaison requis pour effectuer l’authentification de connexion de OpenID utiles.  

Il inclut également une `jwks_uri`, qui donne à l’emplacement de l’ensemble des clés publiques utilisées pour signer les jetons.  Le document JSON situé dans le `jwks_uri` contient toutes les informations de clé publiques utilisé à ce moment particulier.  Votre application peut utiliser la `kid` de revendication dans l’en-tête JWT pour sélectionner la clé publique de ce document a été utilisé pour signer un jeton donné.  Il peut ensuite effectuer la validation de la signature à l’aide de la clé publique appropriée et l’algorithme spécifié.

Validation de signature est en dehors de la portée de ce document, de nombreuses bibliothèques open source sont disponibles pour vous aider à effectuer cette opération si nécessaire.

#### <a name="validating-the-claims"></a>Validation des créances
Lorsque votre application reçoit un id_token lors de la connexion de l’utilisateur, il doit également effectuer quelques vérifications contre les revendications dans l’id_token.  Celles-ci incluent, mais ne sont pas limitées à :

- La demande **d’assistance** - pour vérifier que l’id_token vise à donner à votre application.
- Créances **Non avant** et le **Délai d’Expiration** - afin de vérifier que l’id_token n’a pas expiré.
- La demande de **l’émetteur** - afin de vérifier que le jeton a été effectivement émis à votre application par le point de terminaison v2.0.
- La **valeur à usage unique** - comme une atténuation d’attaque une réexécution de jeton.
- et bien plus encore...

Pour une liste complète de votre application doit effectuer des validations de revendication, reportez-vous à la [spécification de OpenID de se connecter](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Des valeurs attendues pour ces demandes sont détaillées ci-dessus dans la [section d’id_token](#id_tokens).


## <a name="token-lifetimes"></a>Durée de vie des jetons

Les durées de vie de jetons suivantes sont fournies uniquement pour votre présentation, comme ils peuvent vous aider dans le développement et le débogage des applications.  Vos applications ne doivent pas être écrites à attendre une de ces durées de vie reste constant - ils peuvent et changeront à un moment donné dans le temps.

| Jeton | Durée de vie | Description |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens (comptes de l’école ou de travail) | 1 heure | Id_Tokens sont généralement valides pendant une heure.  Votre application web peut utiliser cette même durée de vie dans le maintien de sa propre session de l’utilisateur (recommandé), ou choisissez une durée de vie de session complètement différent.  Si votre application a besoin d’obtenir un nouveau id_token, il doit simplement effectuer une nouvelle demande de connexion à la version 2.0 à autoriser le point de terminaison.  Si l’utilisateur a une session de navigateur valide avec le point de terminaison v2.0, ils ne peuvent pas tenus à entrer de nouveau leurs informations d’identification. |
| Id_Tokens (comptes personnels) | 24 heures | Id_Tokens pour des comptes personnels sont généralement valides pendant 24 heures.  Votre application web peut utiliser cette même durée de vie dans le maintien de sa propre session de l’utilisateur (recommandé), ou choisissez une durée de vie de session complètement différent.  Si votre application a besoin d’obtenir un nouveau id_token, il doit simplement effectuer une nouvelle demande de connexion à la version 2.0 à autoriser le point de terminaison.  Si l’utilisateur a une session de navigateur valide avec le point de terminaison v2.0, ils ne peuvent pas tenus à entrer de nouveau leurs informations d’identification. |
| Jetons d’accès (comptes de l’école ou de travail) | 1 heure | Indiqués dans les réponses de jetons dans le cadre des métadonnées jeton. |
| Jetons d’accès (comptes personnels) | 1 heure | Indiqués dans les réponses de jetons dans le cadre des métadonnées jeton.  Access_tokens émis au nom des comptes personnels peut être configuré pour une durée de vie différente, mais une heure est généralement le cas. |
| Actualiser les jetons (compte de travail ou à l’école) | Jusqu'à 14 jours | Un jeton unique d’actualisation est valide pour un maximum de 14 jours.  Toutefois, le jeton d’actualisation peut devenir invalide à tout moment de n’importe quel nombre de raisons, afin que votre application doit continuer à essayer et d’utiliser un jeton d’actualisation jusqu'à ce qu’il tombe en panne, ou jusqu'à ce que votre application remplace par un nouveau jeton d’actualisation.  Un jeton d’actualiser également devient non valide si elle a été 90 jours dans la mesure où l’utilisateur a entré ses informations d’identification. |
| Actualiser des jetons (des comptes personnels) | Jusqu'à 1 an | Un jeton unique d’actualisation est valide pour un maximum de 1 an.  Toutefois, le jeton d’actualisation peut devenir invalide à tout moment de n’importe quel nombre de raisons, afin que votre application doit continuer à essayer et utiliser un jeton d’actualisation jusqu'à ce qu’il échoue. |
| Codes d’autorisation (comptes de l’école ou de travail) | 10 minutes | Codes d’autorisation sont délibérément de courte durées et doivent être remboursées immédiatement pour access_tokens et refresh_tokens dès qu’ils sont reçus. |
| Codes d’autorisation (comptes personnels) | 5 minutes | Codes d’autorisation sont délibérément de courte durées et doivent être remboursées immédiatement pour access_tokens et refresh_tokens dès qu’ils sont reçus.  Codes d’autorisation émis au nom des comptes personnels sont également une utilisation unique. |
