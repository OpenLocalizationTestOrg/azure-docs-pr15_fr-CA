<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Comment créer des applications directement en utilisant les protocoles pris en charge par Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-authentication-protocols"></a>Azure B2C d’annonce : Protocoles d’authentification

Azure B2C d’Active Directory (AD Azure) fournit l’identité en tant que service pour vos applications en prenant en charge les deux protocoles standard : OpenID se connecter et OAuth 2.0. Le service est conforme aux normes, mais les deux implémentations de ces protocoles présentant de légères différences.  Les informations contenues dans ce guide sera utiles si vous écrivez votre code en envoyant directement et en gérant les requêtes HTTP, et non à l’aide d’une bibliothèque open source. Nous vous conseillons de lire cette page avant de vous plonger dans les détails de chaque protocole spécifique. Mais si vous êtes déjà familiarisé avec Azure AD B2C, vous pouvez atteindre directement [les guides de référence du protocole](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Les notions de base
Chaque application qui utilise Azure AD B2C doit être enregistré dans le répertoire B2C dans [Azure portal](https://portal.azure.com). Le processus d’inscription application collecte et assigne de quelques valeurs à votre application :

- Un **ID d’Application** qui identifie de manière unique votre application.
- Un **URI de rediriger** ou un **identificateur de package** qui peut servir à diriger les réponses à votre application.
- Quelques autres valeurs spécifiques au scénario. Pour en savoir plus, Découvrez [comment enregistrer votre application](active-directory-b2c-app-registration.md).

Après avoir inscrit votre application, il communique avec Active Directory Azure en envoyant des requêtes au point de terminaison v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Dans presque tous les flux OAuth et OpenID se connecter, quatre parties sont impliquées dans l’échange :

![Rôles OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- Le **serveur de l’autorisation** est le point de terminaison AD Azure v2.0. Il traite de manière sécurisée tout ce qui concerne les accès et les informations utilisateur. Il gère également les relations d’approbation entre les parties dans un flux. Il est responsable de la vérification de l’identité de l’utilisateur, l’octroi et révocation de l’accès aux ressources et l’émission de jetons. Il est également connu sous le nom du fournisseur d’identité.
- Le **propriétaire de la ressource** est généralement l’utilisateur final. Il s’agit de la partie qui détient les données, et il a le pouvoir d’autoriser des tiers à accéder aux données ou la ressource.
- Le **client de OAuth** est votre application. Il est identifié par son ID d’Application. Il est généralement la partie qui les utilisateurs finaux interagissent avec. Il demande également des jetons à partir du serveur d’autorisation. Le propriétaire de la ressource doit accorder l’autorisation du client pour accéder à la ressource.
- Le **serveur de ressource** où réside la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser le client OAuth en toute sécurité. Il utilise également les jetons d’accès au porteur pour s’assurer que l’accès à une ressource peuvent être accordées.

## <a name="policies"></a>Stratégies de
Sans doute, les stratégies d’Azure AD B2C sont des fonctionnalités les plus importantes du service. Azure AD B2C étend les protocoles standard de OAuth 2.0 et OpenID connecter en introduisant des stratégies. Ces règles autorisent B2C d’AD Azure effectuer beaucoup plus que la simple authentification et d’autorisation. Stratégies de décrivent complètement le consommateur identité des expériences, y compris d’abonnement, connexion et modification de profils. Stratégies peuvent être définies dans une interface utilisateur d’administration. Elles peuvent être exécutées à l’aide d’un paramètre de requête spécial dans les demandes d’authentification HTTP. Stratégies ne sont pas des fonctionnalités standard de OAuth 2.0 et OpenID se connecter, donc vous devez prendre le temps de comprendre les. Pour plus d’informations, consultez le [guide de référence de stratégie Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Jetons
La mise en oeuvre d’Azure AD B2C OAuth 2.0 et OpenID connecter utilise de manière intensive les jetons de support, y compris des jetons au porteur qui sont représentées sous forme de jetons de web JSON (JWTs). Un jeton de support est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Le porteur est toute partie qui peut présenter le jeton. Annonce Azure doit tout d’abord authentifier une partie avant de pouvoir recevoir un jeton de support. Mais, si les mesures ne sont pas prises pour sécuriser le jeton dans la transmission et le stockage, il peut être interceptée et utilisée par une partie involontaire.

Des jetons de sécurité ont un mécanisme intégré qui empêche des parties non autorisées de leur utilisation, mais les jetons porteur ne possèdent pas ce mécanisme. Ils doivent être transportés dans un canal sécurisé, tels que la sécurité de couche transport (HTTPS). Si un jeton de support est transmis à l’extérieur d’un canal sécurisé, une partie malveillante permet une attaque man-in-the-middle acquérir le jeton et l’utiliser pour accéder à une ressource protégée. Les mêmes principes de sécurité s’appliquent lorsque les jetons de support sont stockés ou mis en cache pour une utilisation ultérieure. Assurez-vous toujours que votre application transmet et stocke les jetons des porteur de manière sécurisée.

Support supplémentaire jeton en matière de sécurité, consultez la [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Plus de détails sur les différents types de jetons utilisés dans Azure AD B2C sont disponibles dans [la référence de jeton AD Azure](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protocoles

Lorsque vous êtes prêt à passer en revue certaines demandes d’exemple, vous pouvez démarrer avec l’un des didacticiels suivants. Chacun d’eux correspond à un scénario d’authentification particulier. Si vous avez besoin de vous aider à déterminer le flux vous circule, consultez [les types d’applications que vous pouvez créer à l’aide d’Azure AD B2C](active-directory-b2c-apps.md).

- [Créer des applications mobiles et natives à l’aide de OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Créer des applications web à l’aide de OpenID de se connecter](active-directory-b2c-reference-oidc.md)
