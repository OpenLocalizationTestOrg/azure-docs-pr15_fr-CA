<properties
    pageTitle="inscription d’application V2.0 | Microsoft Azure"
    description="Comment faire pour enregistrer une application auprès de Microsoft pour l’activation de connexion et l’accès aux services de Microsoft en utilisant le point de terminaison v2.0"
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

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Comment inscrire une application avec le point de terminaison v2.0

Pour générer une application qui accepte à la fois MSA & Azure AD sign-in, vous êtes d’abord nécessaire enregistrer une application auprès de Microsoft.  À ce stade, vous ne pourrez pas utiliser les applications existantes peuvent avoir avec Azure AD ou MSA - vous devez en créer un nouveau.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="visit-the-microsoft-app-registration-portal"></a>Visitez le portail d’inscription Microsoft app
Premièrement d’abord - accéder à [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Il s’agit d’un nouveau portail de l’enregistrement de app dans lequel vous pouvez gérer vos applications Microsoft.

Ouvrir une session soit personnelle ou travailler ou école compte Microsoft.  Si vous n’avez pas soit, inscrivez-vous à un compte personnel. Allez-y, occupations - nous vous attendons ici.

Fait ? Vous devriez maintenant voir votre liste d’applications de Microsoft, qui est sans doute vide.  Activez-la.

Cliquez sur **Ajouter une application**et donnez-lui un nom.  Le portail affectera votre application un Id globalement unique de l’Application que vous utiliserez ultérieurement dans votre code.  Si votre application inclut un composant côté serveur qui a besoin de jetons d’accès pour appeler des API (pensez : Office, Azure ou votre propre API web), vous voudrez créer ici un **Secret de l’Application** ainsi.
<!-- TODO: Link for app secrets -->

Ensuite, ajoutez les plates-formes que votre application utilisera.

- Pour les applications web de base, fournir un **URI de redirection** dans lequel les messages de connexion peuvent être envoyés.
- Pour les applications mobiles, copiez vers le bas de l’uri de redirection par défaut créé automatiquement pour vous.

Si vous le souhaitez, vous pouvez personnaliser l’apparence de votre page de connexion dans la section de profil.  Assurez-vous de cliquer sur **Enregistrer** avant de continuer.

> [AZURE.NOTE] Lorsque vous créez une application à l’aide de [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), l’application sera inscrite dans le locataire domestique du compte que vous utilisez pour vous connecter au portail.  Cela signifie que vous ne pouvez pas enregistrer une application dans vos clients AD Azure à l’aide d’un compte personnel de Microsoft.  Si vous souhaitez explicitement l’inscription d’une application dans un client particulier, connectez-vous avec un compte créé à l’origine de ce client.

## <a name="build-a-quick-start-app"></a>Créez une application de démarrage rapide
Maintenant que vous disposez d’une application Microsoft, vous pouvez effectuer une de nos didacticiels de démarrage rapide v2.0.  Voici quelques recommandations :

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]
