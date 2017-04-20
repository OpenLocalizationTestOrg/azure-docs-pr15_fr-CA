<properties
   pageTitle="Gestion des identités pour Applications mutualisées | Microsoft Azure"
   description="Méthodes conseillées pour la gestion de l’authentification, l’autorisation et identité dans les applications mutualisées."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Gestion des identités pour applications mutualisées dans Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cette série a décrit les méthodes conseillées pour l’architecture multilocataire, lors de l’utilisation d’AD Azure pour la gestion d’identité et d’authentification.

Lorsque vous générez une application partagée, une des premières difficultés gère l’identité des utilisateurs, dans la mesure où chaque utilisateur appartient à un client. Par exemple :

- Les utilisateurs se connecter avec leurs informations d’identification d’organisation.
- Utilisateurs doivent avoir accès aux données de leur organisation, mais pas les données qui appartiennent aux autres locataires.
- Une organisation peut s’inscrire pour l’application et ensuite affecter les rôles d’application à ses membres.

Azure Active Directory (AD Azure) possède des fonctionnalités qui prennent en charge tous ces scénarios.

Pour accompagner cette série d’articles, nous avons également créé une complète, [mise en œuvre de bout en bout pour] [ tailspin] d’une application partagée. Les articles reflètent ce que nous avons appris lors de la création de l’application. Pour vous familiariser avec l’application, consultez [exécution de l’application d’enquêtes](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

Voici la liste des articles de cette série :

- [Introduction à la gestion des identités pour applications mutualisées](guidance-multitenant-identity-intro.md)
- [À propos de l’application Tailspin enquêtes](guidance-multitenant-identity-tailspin.md)
- [Authentification à l’aide d’Active Directory Azure et OpenID se connecter](guidance-multitenant-identity-authenticate.md)
- [Travail avec des identités basée sur les revendications](guidance-multitenant-identity-claims.md)
- [Inscription et intégration de client](guidance-multitenant-identity-signup.md)
- [Rôles d’application](guidance-multitenant-identity-app-roles.md)
- [Autorisation basée sur les rôles et basée sur les ressources](guidance-multitenant-identity-authorize.md)
- [Sécurisation d’une API de web back-end](guidance-multitenant-identity-web-api.md)
- [La mise en cache des jetons d’accès OAuth2](guidance-multitenant-identity-token-cache.md)
- [Fédération avec AD FS un client pour applications mutualisées dans Azure](guidance-multitenant-identity-adfs.md)
- [À l’aide d’assertion du client afin d’obtenir les jetons d’accès à partir d’AD Azure](guidance-multitenant-identity-client-assertion.md)
- [À l’aide de la clé de stockage en chambre forte pour protéger la confidentialité de l’application](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
