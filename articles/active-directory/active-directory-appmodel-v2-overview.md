<properties
    pageTitle="vue d’ensemble du point de terminaison v2.0 | Microsoft Azure"
    description="Introduction à la création d’applications avec Microsoft Account et Azure Active Directory reconnectez-vous."
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
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Connexion à Microsoft Account & utilisateurs AD Azure dans une seule application

Dans le passé, un développeur de l’application qui veut prendre en charge des comptes de Microsoft et Azure Active Directory a été nécessaire d’intégrer les deux systèmes distincts.  Nous avons maintenant créé une nouvelle version de l’API d’authentification qui vous permet de connecter les utilisateurs avec les deux types de comptes à l’aide de la système d’Azure.  Ce système d’authentification de convergence est appelé **le point de terminaison v2.0**.  Avec le point de terminaison v2.0, une intégration simple vous permet d’atteindre un public qui s’étend sur des millions d’utilisateurs avec des comptes personnels et de travail ou aux études.

Les applications qui utilisent le point de terminaison v2.0 peuvent également consommer des API du reste de [Microsoft Graph](https://graph.microsoft.io) et [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) à l’aide d’un type de compte.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Mise en route
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Choisissez votre plate-forme de favori à partir de la liste suivante pour générer une application à l’aide de notre les bibliothèques open source et les infrastructures.  Également, vous pouvez utiliser notre documentation de protocole OAuth 2.0 & OpenID de se connecter à envoyer & recevoir des messages de protocole directement sans recourir à une bibliothèque d’authentification.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Quelles sont les nouveautés
Les informations conceptuelles ici sera utiles dans la compréhension de ce qu’est et ce qui n’est pas possible avec le point de terminaison v2.0.

- Obtenir des informations sur les [types d’applications, que vous pouvez créer avec le point de terminaison v2.0](active-directory-v2-flows.md).
- Comprendre les [limitations, les restrictions et les contraintes](active-directory-v2-limitations.md) avec le point de terminaison v2.0.
- Nous avons récemment ajouté la prise en charge de [l’administration restreint étendues](active-directory-v2-scopes.md) et le [client OAuth2 accordent des informations d’identification](active-directory-v2-protocols-oauth-client-creds.md).  Les essayer !

## <a name="reference"></a>Référence
Ces liaisons seront utiles pour l’exploration de la plate-forme en profondeur :

- 2016 de génération : [mise en route avec des identités de Microsoft : signe de niveau entreprise dans pour vos applications](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Obtenir de l’aide sur le débordement de la pile à l’aide d' [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) les balises.
- [Référence au protocole v2.0](active-directory-v2-protocols.md)
- [Référence de jeton v2.0](active-directory-v2-tokens.md)
- [Référence de la bibliothèque v2.0](active-directory-v2-libraries.md)
- [Étendues et consentement dans le point de terminaison v2.0](active-directory-v2-scopes.md)
- [Le portail d’inscription de Microsoft App](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Référence de l’API reste Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Le graphique de Microsoft](https://graph.microsoft.io)