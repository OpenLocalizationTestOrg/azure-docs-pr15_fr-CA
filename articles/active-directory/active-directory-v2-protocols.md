<properties
    pageTitle="Annonce Azure v2.0 protocoles | Microsoft Azure"
    description="Un guide sur les protocoles pris en charge par le point de terminaison AD Azure v2.0."
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

# <a name="v20-protocols---oauth-20--openid-connect"></a>Connectent des protocoles - OAuth 2.0 & OpenID v2.0

Le point de terminaison v2.0 pouvez utiliser AD Azure d’identité-comme-a-service avec des protocoles standard, OpenID se connecter et OAuth 2.0.  Pendant que le service est conforme aux normes, il peut y avoir des différences subtiles entre les deux implémentations de ces protocoles.  Ces informations seront utiles si vous choisissez d’écrire votre code en envoyant directement & gestion des requêtes HTTP ou utilisez un 3ème partie ouvrir bibliothèque source plutôt qu’à l’aide d’un de nos bibliothèques open source.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Les notions de base
Dans presque tous les flux OAuth & OpenID de se connecter, il existe quatre parties participant à l’échange :

![Rôles OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- Le **Serveur de l’autorisation** est le point de terminaison v2.0.  Il est chargé de garantir l’identité de l’utilisateur, l’octroi et révocation de l’accès aux ressources qui délivre des jetons.  Il est également connu sous le nom du fournisseur d’identité - il traite de manière sécurisée rien à voir avec les informations d’utilisateur, leur accès et les relations d’approbation entre les parties dans un flux.
- Le **Propriétaire de la ressource** est généralement l’utilisateur final.  Il s’agit de la partie qui détient les données et qui a le pouvoir d’autoriser des tiers à accéder aux données, ou la ressource.
- Le **Client de OAuth** est votre application, identifiée par son ID d’Application.  Il est généralement la partie interagissant avec l’utilisateur final, et qu’elle les jetons à partir du serveur d’autorisation.  Le client doit être autorisé à accéder à la ressource par le propriétaire de la ressource.
- Le **Serveur de ressource** où réside la ressource ou les données.  Il approuve le serveur d’autorisation pour authentifier et autoriser le OAuth Client en toute sécurité et utilise access_tokens de support afin de garantir que l’accès à une ressource peuvent être accordées.


## <a name="app-registration"></a>Inscription de l’application
Chaque application qui utilise le point de terminaison v2.0 devra être enregistré à la [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) avant qu’elle peut interagir à l’aide de OAuth ou OpenID de se connecter.  Le processus d’inscription de app collecter & affecter quelques valeurs à votre application :

- Un **Id d’Application** qui identifie de manière unique votre application
- Un **URI de rediriger** ou un **Identificateur de Package** qui peut servir à diriger les réponses à votre application
- Quelques autres valeurs spécifiques au scénario.

Pour plus d’informations, découvrez comment [Enregistrer une application](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Points de terminaison
Une fois inscrit, l’application communique avec Active Directory Azure en envoyant des requêtes au point de terminaison v2.0 :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Où les `{tenant}` peut prendre une des quatre valeurs différentes :

| Valeur | Description |
| ----------------------- | ------------------------------- |
| `common` | Permet aux utilisateurs dont les comptes personnels de Microsoft et de comptes du travail ou aux études d’Azure Active Directory pour vous connecter à l’application. |
| `organizations` | Autorise uniquement les utilisateurs disposant de comptes de travail ou aux études d’Azure Active Directory pour vous connecter à l’application. |
| `consumers` | Permet uniquement aux utilisateurs avec des comptes personnels Microsoft (MSA) pour vous connecter à l’application. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`ou`contoso.onmicrosoft.com` | Autorise uniquement les utilisateurs disposant de comptes de travail ou aux études à partir d’un fermier Azure Active Directory particulier pour vous connecter à l’application.  Le nom de domaine convivial du locataire Azure AD ou identificateur du guid du locataire peut être utilisé.  |

Pour plus d’informations sur la façon d’interagir avec ces points de terminaison, choisissez un type particulier d’application ci-dessous.

## <a name="tokens"></a>Jetons
L’implémentation de la version 2.0 de OAuth 2.0 et OpenID se connecter utilisent beaucoup de jetons de support, y compris des jetons porteur représentés sous la forme JWTs. Un jeton de support est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce sens, le « porteur » est toute partie qui peut présenter le jeton. Si une partie doit tout d’abord authentifier avec AD Azure pour recevoir le jeton au porteur, si les mesures ne sont pas prises pour sécuriser le jeton dans la transmission et le stockage, peuvent être interceptée et utilisée par une partie involontaire. Des jetons de sécurité ont un mécanisme intégré pour empêchant des tiers de leur utilisation, les jetons de support n’ont pas ce mécanisme et doivent être transportés dans un canal sécurisé, tels que la sécurité de la couche transport (HTTPS). Si un jeton de support est transmis en clair, un homme de l’attaque peut servir par une partie malveillante pour obtenir le jeton et l’utiliser pour un accès non autorisé à une ressource protégée. Les mêmes principes de sécurité s’appliquent lors du stockage ou de la mise en cache des jetons de support pour une utilisation ultérieure. Assurez-vous toujours que votre application transmet et stocke les jetons des porteur de manière sécurisée. Plus en matière de sécurité sur les jetons de support, consultez la [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

En savoir plus sur différents types de jetons utilisés dans le point de terminaison v2.0 est disponible dans [la référence de jeton de point de terminaison v2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protocoles

Si vous êtes prêt à voir certaines demandes d’exemple, mettre en route un de le ci-dessous didacticiels.  Chacun d’eux correspond à un scénario d’authentification particulier.  Si vous avez besoin d’aide pour déterminer qui est le flux de droit pour vous, consultez [les types d’applications, que vous pouvez créer avec la version 2.0](active-directory-v2-flows.md).

- [Génération Mobile et une Application Native avec OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Génération de Web Apps avec l’ID d’ouverture de se connectent](active-directory-v2-protocols-oidc.md)
- [Créez des applications de Page unique avec le flux implicite OAuth 2.0](active-directory-v2-protocols-implicit.md)
- [Processus de génération ou les traitements de côté serveur avec les informations d’identification du Client OAuth 2.0 de flux](active-directory-v2-protocols-oauth-client-creds.md)
- Obtenir des jetons dans une API Web avec le OAuth 2.0 de la part de flux (prochainement)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
