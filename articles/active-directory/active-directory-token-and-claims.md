 <properties
   pageTitle="Référence de jeton de publicité Azure | Microsoft Azure"
   description="Un guide permettant de comprendre et d’évaluer les déclarations dans les jetons SAML 2.0 et JSON Web jetons (JWT) délivrés par Azure Active Directory (DAS)"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Référence de jeton Azure AD

Azure Active Directory (AD Azure) émet plusieurs types de jetons de sécurité dans le traitement de chaque flux d’authentification. Ce document décrit les caractéristiques de sécurité et la valeur de chaque type de jeton.

## <a name="types-of-tokens"></a>Types de jetons

Annonce Azure prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-protocols-oauth-code.md), qui utilise à la fois access_tokens et refresh_tokens.  Il prend également en charge l’authentification et l’authentification à l’aide de [OpenID de se connecter](active-directory-protocols-openid-connect-code.md), qui introduit un troisième type de jeton, l’id_token.  Chacun de ces jetons est représenté sous la forme d’un « jeton de support ».

Un jeton de support est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce sens, le « porteur » est toute partie qui peut présenter le jeton. Bien que l’authentification avec Azure AD est nécessaire pour recevoir un jeton de support, doivent prendre des mesures pour sécuriser le jeton, afin d’empêcher l’interception par une partie involontaire. Étant donné que les jetons porteur ne disposent pas d’un mécanisme intégré pour empêcher des parties non autorisées de leur utilisation, ils doivent être transportés dans un canal sécurisé, tels que la sécurité de la couche transport (HTTPS). Si un jeton de support est transmis en clair, un homme de l’attaque peut servir à acquérir le jeton et accéder à une ressource protégée. Les mêmes principes de sécurité s’appliquent lors du stockage ou de la mise en cache des jetons de support pour une utilisation ultérieure. Assurez-vous toujours que votre application transmet et stocke les jetons des porteur de manière sécurisée. Plus en matière de sécurité sur les jetons de support, consultez la [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par AD Azure sont implémentées comme des jetons de Web JSON ou JWTs.  Un JWT est un moyen compact et sécurisés pour les URL de transfert d’informations entre les deux parties.  Les informations contenues dans JWTs sont appelées « affirmations », ou assertions d’informations sur le support et l’objet du jeton.  Les revendications contenues dans JWTs sont des objets JSON codés et sérialisés pour la transmission.  Dans la mesure où les JWTs émis par AD Azure sont signés mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un JWT pour le débogage.  Il existe plusieurs outils disponibles pour le faire, par exemple [jwt.calebb.net](http://jwt.calebb.net). Pour plus d’informations sur JWTs, vous pouvez faire référence à la [spécification de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens est une forme de jeton de sécurité de connexion qui reçoit de votre application lors de l’authentification à l’aide de [OpenID de se connecter](active-directory-protocols-openid-connect-code.md).  Ils sont représentés sous forme de [JWTs](#types-of-tokens)et contient des déclarations que vous pouvez utiliser pour la signature de l’utilisateur dans votre application.  Vous pouvez utiliser les revendications dans un id_token comme vous le souhaitez - qu’ils sont utilisés pour l’affichage des informations de compte ou de prendre des décisions de contrôle d’accès dans une application.

Id_tokens sont signés, mais pas crypté à ce moment.  Lorsque votre application reçoit un id_token, il doit [valider la signature](#validating-tokens) pour prouver l’authenticité du jeton et valider plusieurs revendications du jeton pour prouver sa validité.  Les revendications validées par une application varient en fonction des exigences du scénario, mais il existe certaines [validations de créance commune](#validating-tokens) que votre application doit effectuer dans chaque scénario.

Consultez la section suivante pour plus d’informations sur les déclarations d’id_tokens, ainsi qu’un id_token de l’exemple.  Notez que les revendications contenues dans id_tokens ne sont pas retournés dans un ordre particulier.  De plus, les nouvelles demandes peuvent être introduites dans id_tokens à n’importe quel point dans le temps, votre application ne devrait pas altérer comme de nouvelles demandes sont introduites.  La liste suivante comprend les créances que votre application peut interpréter de façon fiable au moment de la rédaction de ce document.  Si nécessaire, vous pouvez trouver encore plus en détail dans la [spécification de OpenID de se connecter](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Exemple id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Pour les exercices pratiques, essayez d’inspecter les revendications dans l’id_token de l’échantillon en le collant dans [calebb.net](http://jwt.calebb.net).

#### <a name="claims-in-idtokens"></a>Demandes d’id_tokens

| Demande JWT | Nom | Description |
|-----------|------|-------------|
| `appid`| ID de l’application | Identifie l’application qui utilise le jeton d’accès à une ressource. L’application peut agir en tant que lui-même ou pour le compte d’un utilisateur. L’ID d’application représente généralement un objet application, mais il peut également représenter un objet entité de sécurité de service dans Active Directory Azure. <br><br> **Par exemple la valeur JWT**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Public | Le destinataire prévu du jeton. L’application qui reçoit le jeton doit vérifier que la valeur public est correcte et qu’il rejette les jetons destinés à un public différent. <br><br> **Exemple de valeur SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Par exemple la valeur JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Référence de classe d’application d’authentification contexte | Indique la façon dont le client a été authentifié. Pour un client public, la valeur est 0. Si l’ID de client et le secret du client sont utilisés, la valeur est 1. <br><br> **Par exemple la valeur JWT**: <br> `"appidacr": "0"`|
| `acr`| Référence de classe de contexte de l’authentification | Indique la manière dont l’objet a été authentifié, et non le client dans la demande de référence de classe d’Application d’authentification contexte. La valeur « 0 » indique que l’authentification de l’utilisateur final ne respectait pas les exigences de la norme ISO/CEI 29115. <br><br> **Par exemple la valeur JWT**: <br> `"acr": "0"`|
| | Authentification instantanée | Enregistre la date et l’heure de l’authentification. <br><br> **Exemple de valeur SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Méthode d’authentification | Identifie comment le sujet du jeton a été authentifié. <br><br> **Exemple de valeur SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Par exemple la valeur JWT**:`“amr”: ["pwd"]` |
| `given_name`| Prénom | Fournit le premier ou « » nom de l’utilisateur, comme définis sur l’objet utilisateur Active Directory Azure. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `"given_name": "Frank"` |
| `groups`| Groupes | Fournit des identificateurs d’objets qui représentent des appartenances aux groupes du sujet. Ces valeurs sont unique (voir objet ID) et peuvent être utilisés en toute sécurité pour la gestion des accès, par exemple l’autorisation d’accéder à une ressource. Les groupes inclus dans la demande de groupes sont configurés sur une base par application, par l’intermédiaire de la propriété « groupMembershipClaims » du manifeste d’application. Exclut tous les groupes d’une valeur null, une valeur de « SecurityGroup » inclut uniquement les appartenances aux groupes de sécurité Active Directory et la valeur est « All » incluent les groupes de sécurité et les listes de Distribution Office 365. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Fournisseur d’identité | Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. Cette valeur est identique à la valeur de la créance de l’émetteur, à moins que le compte d’utilisateur est dans un autre client que l’émetteur. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Contient la date à laquelle le jeton a été délivré. Il est souvent utilisé pour mesurer la fraîcheur de jeton. <br><br> **Exemple de valeur SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Par exemple la valeur JWT**: <br> `"iat": 1390234181` |
| `iss` | Émetteur | Identifie le service de jeton de sécurité (STS) qui construit et retourne le jeton. Dans les jetons AD Azure renvoie, l’émetteur est sts.windows.net. Le GUID dans la valeur de revendication de l’émetteur est l’ID de client de l’annuaire Active Directory Azure. L’ID client est un identificateur immuable et fiable du répertoire. <br><br> **Exemple de valeur SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Par exemple la valeur JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Nom de famille | Fournit le dernier nom, prénom ou nom de famille de l’utilisateur, telle que définie dans l’objet utilisateur Active Directory Azure. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `"family_name": "Miller"` |
| `unique_name`| Nom | Fournit une valeur lisible humaine qui identifie le sujet du jeton. Cette valeur n’est pas garantie pour être unique au sein d’un client et est conçue pour être utilisé uniquement à des fins d’affichage. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | ID de l’objet | Contient un identificateur unique d’un objet dans Active Directory Azure. Cette valeur est immuable et ne peut pas être réaffectée ou réutilisés. Utiliser l’ID d’objet pour identifier un objet dans les requêtes à Active Directory Azure. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Rôles | Représente tous les rôles d’application que l’objet a été accordée à la fois directement et indirectement par l’intermédiaire de l’appartenance au groupe et peut être utilisé pour appliquer un contrôle d’accès basé sur les rôles. Les rôles d’application sont définis sur une base par application, par l’intermédiaire de la `appRoles` propriété du manifeste d’application. Le `value` propriétés de chaque rôle d’application sont la valeur qui apparaît dans la demande de rôles. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Champ d’application | Indique les autorisations de l’emprunt d’identité pour l’application cliente. L’autorisation par défaut est `user_impersonation`. Le propriétaire de la ressource sécurisée peut enregistrer des valeurs supplémentaires dans AD Azure. <br><br> **Par exemple la valeur JWT**: <br> `"scp": "user_impersonation"`|
| `sub` |Objet| Identifie l’entité sur laquelle le jeton déclare des informations, telles que l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réassignée ou réutilisé, par conséquent, il peut être utilisé pour effectuer des vérifications d’autorisation en toute sécurité. Car l’objet est toujours présent dans les jetons, les problèmes d’annonce d’Azure, il est recommandé à l’aide de cette valeur dans un système d’autorisation général. <br> `SubjectConfirmation`n’est pas une demande de remboursement. Elle décrit comment le sujet du jeton est vérifié. `Bearer`Indique que le sujet est confirmé en leur possession du jeton. <br><br> **Exemple de valeur SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Par exemple la valeur JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | ID de clients | Un identificateur immuable, non réutilisable qui identifie le locataire du répertoire qui a émis le jeton. Vous pouvez utiliser cette valeur pour accéder aux ressources du répertoire spécifique de clients dans une application partagée. Par exemple, vous pouvez utiliser cette valeur pour identifier le client dans un appel à l’API de graphique. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Par exemple la valeur JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Durée de vie de jeton | Définit l’intervalle de temps pendant lequel un jeton est valide. Le service qui valide le jeton doit vérifier que la date actuelle est dans la jeton durée de vie, autre qu’il est conseillé de refuser le jeton. Le service peut autoriser pendant cinq minutes au-delà de la plage de durée de vie de jeton pour tenir compte des différences éventuelles de l’horloge (« décalage horaire ») entre Azure AD et le service. <br><br> **Exemple de valeur SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Par exemple la valeur JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nom d’utilisateur Principal | Stocke le nom d’utilisateur de l’utilisateur principal.<br><br> **Par exemple la valeur JWT**: <br> `"upn": frankm@contoso.com`|
| `ver`| Version | Stocke le numéro de version du jeton. <br><br> **Par exemple la valeur JWT**: <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>Jetons d’accès

Les jetons d’accès sont uniquement consommables par les Services Microsoft à ce stade.  Vos applications ne devez pas effectuer de validation ni contrôle des jetons d’accès pour tous les scénarios pris en charge.  Vous pouvez traiter les jetons d’accès comme complètement opaque : ils sont seulement les chaînes dont votre application peut passer à Microsoft dans les demandes HTTP.

Lorsque vous demandez un jeton d’accès, AD Azure renvoie également des métadonnées sur le jeton d’accès pour la consommation de votre application.  Ces informations comprennent la date d’expiration du jeton d’accès et les étendues pour lesquels il est valide.  Cela permet à votre application pour exécuter intelligente de la mise en cache des jetons d’accès sans avoir à analyser ouvrir le jeton d’accès lui-même.

## <a name="refresh-tokens"></a>Actualiser les jetons

Actualiser les jetons sont des jetons de sécurité, votre application peut utiliser pour acquérir de nouveaux jetons d’accès dans un flux OAuth 2.0.  Il permet à votre application d’obtenir l’accès aux ressources à long terme pour le compte d’un utilisateur sans nécessiter une interaction par l’utilisateur.

Actualiser les jetons sont des ressources multiples, qui signifie qu’ils peuvent être reçus au cours d’une demande de jeton pour une ressource, mais remboursés pour les jetons d’accès à une ressource complètement différente. Pour spécifier des ressources multiples, définissez la `resource` paramètre dans la requête pour la ressource cible.

Actualiser les jetons sont complètement opaques à votre application. Ils sont durables, mais si votre application ne doit pas être écrites à attendre que dure un jeton d’actualisation pour une période donnée.  Jetons d’actualisation peuvent être invalidées à tout moment dans le temps pour diverses raisons.  Le seul moyen pour votre application, de savoir si un jeton d’actualisation est valide consiste à essayer pour le rembourser en effectuant une demande de jeton au point de terminaison jeton AD Azure.

Lorsque vous utiliser un jeton d’actualisation pour un nouveau jeton d’accès, vous recevrez un nouveau jeton d’actualisation dans la réponse de jeton.  Vous devez enregistrer le jeton actualisation nouvellement émis, remplaçant celui que vous avez utilisé dans la demande.  Ceci garantit que vos jetons actualisation restent valables aussi longtemps que possible.

## <a name="validating-tokens"></a>Validation des jetons

À ce stade, la validation de jeton seulement que votre application cliente ne devez exécuter validation id_tokens.  Pour valider un id_token, votre application doit valider les signatures de l’id_token et des revendications dans l’id_token.

Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer facilement de validation jeton, si vous souhaitez comprendre le processus sous-jacent.  Plusieurs bibliothèques de code source ouvert de tiers sont également disponibles pour la validation de la JWT, au moins une option pour chaque plate-forme et la langue. Pour plus d’informations sur les exemples de code et les bibliothèques de l’authentification AD Azure, consultez [bibliothèques d’authentification AD Azure](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Validation de la signature

Un JWT contient trois segments, qui sont séparés par le `.` caractère.  Le premier segment est connu comme l' **en-tête**, le second dans le **corps**et la troisième comme **signature**.  Le segment de signature peut être utilisé pour valider l’authenticité de l’id_token afin qu’il peut être approuvé par votre application.

Id_Tokens sont signés à l’aide d’algorithmes de cryptage asymétrique standard, tel que RSA 256. L’en-tête de l’id_token contient des informations sur la méthode de cryptage et la clé utilisée pour signer le jeton :

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

Le `alg` demande indique l’algorithme utilisé pour signer le jeton, while la `x5t` demande indique la clé publique qui a été utilisée pour signer le jeton.

À un moment donné dans le temps, AD Azure peut-être signer un id_token à l’aide de l’un d’un certain ensemble de paires de clés publique-privée. Azure AD fait pivoter de l’ensemble de clés sur une base périodique, le possible afin que votre application doit être écrite pour gérer ces modifications clées automatiquement.  Une fréquence raisonnable pour vérifier les mises à jour les clés publiques utilisées par Active Directory Azure est de 24 heures.

Vous pouvez acquérir les données de clé signature nécessaires pour valider la signature en utilisant le document de métadonnées OpenID se connecter à :

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Essayez cette URL dans un navigateur !

Ce document de métadonnées est un objet JSON qui contient plusieurs informations, telles que l’emplacement des différents points de terminaison requis pour effectuer l’authentification de connexion de OpenID utiles.  

Il inclut également une `jwks_uri`, qui donne à l’emplacement de l’ensemble des clés publiques utilisées pour signer les jetons.  Le document JSON situé dans le `jwks_uri` contient toutes les informations de clé publiques utilisé à ce moment particulier.  Votre application peut utiliser la `kid` de revendication dans l’en-tête JWT pour sélectionner la clé publique de ce document a été utilisé pour signer un jeton donné.  Il peut ensuite effectuer la validation de la signature à l’aide de la clé publique appropriée et l’algorithme spécifié.

Validation de signature est en dehors de la portée de ce document, de nombreuses bibliothèques open source sont disponibles pour vous aider à effectuer cette opération si nécessaire.

#### <a name="validating-the-claims"></a>Validation des créances

Lorsque votre application reçoit un id_token lors de la connexion de l’utilisateur, il doit également effectuer quelques vérifications contre les revendications dans l’id_token.  Celles-ci incluent, mais ne sont pas limitées à :

  - La demande **d’assistance** - pour vérifier que l’id_token vise à donner à votre application.
  - Créances **Non avant** et le **Délai d’Expiration** - afin de vérifier que l’id_token n’a pas expiré.
  - La demande de **l’émetteur** - afin de vérifier que le jeton a été effectivement délivré à votre application par AD Azure.
  - La **valeur à usage unique** - pour atténuer une attaque par reconstitution de jeton.
  - et bien plus encore...

Pour obtenir une liste complète des contrôles de demande que votre application doit effectuer, reportez-vous à la [spécification de OpenID de se connecter](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Des valeurs attendues pour ces revendications sont détaillées dans la section précédente de la [section d’id_token](#id-tokens) .

## <a name="sample-tokens"></a>Jetons de l’échantillon

Cette section affiche des exemples de jetons SAML et JWT qui renvoie des annonces Azure. Ces exemples vous permettent de voir les revendications dans le contexte.
Jeton SAML

Il s’agit d’un exemple d’un jeton SAML typique.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT - jeton d’emprunt d’identité de l’utilisateur

Il s’agit d’un exemple de type JSON web jeton (JWT) utilisé dans un flux de licence de code d’autorisation.
En plus des réclamations, le jeton comprend un numéro de version dans le **ver** et **appidacr**, la référence de classe de contexte de l’authentification, qui indique la façon dont le client a été authentifié. Pour un client public, la valeur est 0. Si un ID de client ou le secret du client a été utilisée, la valeur est 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Contenu associé
- Pour en savoir plus sur la gestion de la stratégie de durée de vie de jeton via l’API Azure AD graphique, reportez-vous à la section Azure AD graphique [des opérations de politique](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) et l' [entité de la stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity).
- Pour plus d’informations et exemples sur la gestion de stratégies via les applets de commande PowerShell, y compris des exemples, consultez [durée de vie des jetons Configurable dans Azure AD](active-directory-configurable-token-lifetimes.md). 
