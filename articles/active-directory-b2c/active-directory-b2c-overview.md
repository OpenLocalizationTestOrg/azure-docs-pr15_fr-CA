<properties
    pageTitle="B2C d’Azure Active Directory : Vue d’ensemble | Microsoft Azure"
    description="Développement d’applications pour les consommateurs avec Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure B2C de répertoire actif : Inscription et de consommateurs dans vos applications

Azure B2C annuaire Active est une solution de gestion d’identité le nuage complet pour votre consommateur orienté applications web et mobiles. Il s’agit d’un service hautement disponible global qui s’adapte à des centaines de millions d’identités de consommateurs. Bâti sur une plate-forme sécurisée à l’échelle de l’entreprise, Azure Active Directory B2C permet de conserver vos applications, votre entreprise et vos consommateurs protégés.

Dans le passé, les développeurs d’applications qui souhaitaient d’inscription et de consommateurs dans leurs applications écrirait leur propre code. Et qu’ils seraient ont utilisé les systèmes ou les bases de données sur site pour stocker les noms d’utilisateur et mots de passe. Azure B2C répertoire actif offre aux développeurs un meilleur moyen d’intégrer la gestion des identités dans leurs applications à l’aide d’une plate-forme sécurisée et basée sur des normes et un ensemble complet de stratégies extensibles. Lorsque vous utilisez Azure Active Directory B2C, vos clients peuvent vous inscrire pour vos applications à l’aide de leurs comptes sociaux existants (Facebook, Google, Amazon, LinkedIn) ou en créant de nouvelles informations d’identification (adresse de messagerie et mot de passe ou nom d’utilisateur et mot de passe) ; Nous appelons les dernier « comptes locaux. »

## <a name="get-started"></a>Mise en route

Premier besoin pour enregistrer l’application avec un Active Directory B2C du Azure pour générer une application qui accepte le consommateur s’inscrire et à se connecter, vous allez client. Obtenez vos propres clients en suivant les étapes décrites dans [créer un locataire Azure AD B2C](active-directory-b2c-get-started.md).

Vous pouvez écrire votre application par rapport au service Azure Active Directory B2C en choisissant envoyer des messages de protocole directement, à l’aide de [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) ou [Ouvrir connexion d’ID](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), ou à l’aide de nos bibliothèques pour effectuer le travail pour vous. Choisissez votre plate-forme de favori dans le tableau suivant et mise en route.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Quelles sont les nouveautés

Revenez souvent pour obtenir des informations sur les futures modifications apportées à l’Active Directory B2C du Azure. Nous allons également tweet sur les mises à jour à l’aide de @AzureAD.

- Obtenir des informations à propos de notre [infrastructure de stratégie extensible](active-directory-b2c-reference-policies.md) et sur les types de stratégies que vous pouvez créer et utiliser dans vos applications.
- Signet notre [blog de service](https://blogs.msdn.microsoft.com/azureadb2c/) pour les notifications sur les problèmes de service secondaire, mises à jour, état et atténuations. Continuer à surveiller le [tableau de bord état Azure](https://azure.microsoft.com/status/) .
- [Contraintes, restrictions et limites de service](active-directory-b2c-limitations.md).
- Enfin, un [exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) à l’aide d’Azure AD B2C & cœur d’ASP.NET.

## <a name="how-to-articles"></a>Articles de savoir-faire

Apprenez à utiliser les fonctionnalités d’Azure Active Directory B2C spécifiques :

- Configurer les comptes de [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [compte Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md)et [LinkedIn](active-directory-b2c-setup-li-app.md) à utiliser dans vos applications pour les consommateurs.
- [Utilisation des attributs personnalisés pour collecter des informations sur vos clients](active-directory-b2c-reference-custom-attr.md).
- [Activer l’authentification selon plusieurs facteurs Azure dans vos applications pour les consommateurs](active-directory-b2c-reference-mfa.md).
- [Définir mot de passe libre-service, permettant à vos consommateurs de réinitialisation](active-directory-b2c-reference-sspr.md).
- [Personnaliser l’apparence de vous inscrire, inscription et d’autres pages pour les consommateurs](active-directory-b2c-reference-ui-customization.md) qui sont pris en charge par Azure Active Directory B2C.
- [Utiliser l’API graphique Azure Active Directory pour créer par programme, lire, mettre à jour et suppression des consommateurs](active-directory-b2c-devquickstarts-graph-dotnet.md) dans vos clients Azure Active Directory B2C.

## <a name="next-steps"></a>Étapes suivantes

Ces liaisons seront utiles pour l’exploration du service en profondeur :

- Consultez les [informations de tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obtenir de l’aide sur le débordement de la pile à l’aide d' [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) les balises.
- Faites-nous part de vos idées à l’aide de [Voix d’utilisateur](https://feedback.azure.com/forums/169401-azure-active-directory/)--nous voulons les entendre ! Utiliser l’expression « AzureADB2C : » dans le titre de votre annonce afin que nous pouvons trouver.
- Passez en revue la [référence au protocole Azure AD B2C](active-directory-b2c-reference-protocols.md).
- Passez en revue la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md).
- Lire les [questions fréquentes de B2C Azure Active Directory](active-directory-b2c-faqs.md).
- [Fichier de prise en charge des demandes d’Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous encourageons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et de s’abonner à l’alerte de sécurité.
