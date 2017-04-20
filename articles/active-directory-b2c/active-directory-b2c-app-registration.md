<properties
    pageTitle="Azure B2C de répertoire actif : Inscription d’Application | Microsoft Azure"
    description="Comment enregistrer votre application avec Azure Active Directory B2C"
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure B2C de répertoire actif : Enregistrer votre application

## <a name="prerequisite"></a>Condition préalable

Pour générer une application qui accepte d’inscription et de connexion de consommateur, vous devez d’abord enregistrer l’application avec un locataire Azure Active Directory B2C. Obtenez vos propres clients en suivant les étapes décrites dans [créer un locataire Azure AD B2C](active-directory-b2c-get-started.md). Après avoir suivi toutes les étapes de cet article, vous avez la lame de fonctionnalités B2C épinglée à votre Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Accédez à la lame de fonctionnalités B2C

Si vous disposez de la lame de fonctionnalités B2C épinglée à votre Startboard, vous verrez la lame dès que vous vous connectez au [portail Azure](https://portal.azure.com/) comme administrateur Global du locataire B2C.

La lame est également accessible en cliquant sur **Parcourir** , puis sur **Azure AD B2C** dans le volet de navigation de gauche sur le [portail Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT] Vous devez être un administrateur Global de la cliente B2C pour être en mesure d’accéder à la lame de fonctionnalités B2C. Un administrateur Global à partir de n’importe quel autre client ou un utilisateur à partir de n’importe quel client ne peut pas y accéder.  Vous pouvez passer à vos clients B2C à l’aide du sélecteur de clients dans le coin supérieur droit du portail Azure.

## <a name="register-an-application"></a>Inscription d’une application

1. Sur la lame de fonctionnalités B2C sur Azure portal, cliquez sur **Applications**.
2. Cliquez sur **+ Ajouter** en haut de la lame.
3. Entrez le **nom** de l’application qui décrit votre application aux consommateurs. Par exemple, vous pouvez entrer « Contoso B2C application ».
4. Si vous écrivez une application basée sur le web, basculez le **inclure web app ou des API** sur **Oui**. Les **URL de réponse** sont des points de terminaison où Azure AD B2C retournera les jetons de votre demande. Par exemple, entrez `https://localhost:44321/`. Si votre application web sera également appeler certaines web API sécurisée par Azure AD B2C, allez vouloir créer une **Application Secret** ainsi en cliquant sur le bouton **Générer la clé** .

    > [AZURE.NOTE] Un **Secret de l’Application** est une information d’identification de sécurité importante et doit être sécurisée de manière appropriée.

5. Si vous écrivez une application mobile, basculez le **client natif d’inclure** à **Oui**. Copier vers le bas de l' **URI de redirection** qui est automatiquement créé pour vous par défaut.
6. Cliquez sur **créer** pour enregistrer votre application.
7. Cliquez sur l’application que vous venez de créer et de copier l' **Application Client ID** globalement unique que vous utiliserez ultérieurement dans votre code.

> [AZURE.IMPORTANT] Les applications créées dans la lame de fonctionnalités B2C ont géré dans le même emplacement. Si vous modifiez B2C applications à l’aide de PowerShell ou un autre portail, ils deviennent non pris en charge et ne fonctionnera probablement pas avec Azure AD B2C.

## <a name="build-a-quick-start-application"></a>Créer une Application de démarrage rapide

Maintenant que vous avez une application enregistrée avec Azure AD B2C, vous pouvez effectuer une de nos didacticiels de démarrage rapide pour obtenir en cours d’exécution. Voici quelques recommandations :

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
