<properties
    pageTitle="Configurer un périphérique Windows 10 avec AD Azure à partir des paramètres | Microsoft Azure"
    description="Explique comment les utilisateurs peuvent participer à Azure AD via le menu Paramètres."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configurer un périphérique Windows 10 avec AD Azure à partir des paramètres
Si vous utilisez déjà Windows 7 ou Windows 8 et que votre ordinateur ou le périphérique a été mis à niveau vers Windows 10, vous pouvez joindre Azure Active Directory (AD Azure) via le menu Paramètres.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Pour joindre à AD Azure à partir du menu Paramètres


1. Dans le menu **Démarrer** , cliquez sur l’icône **paramètres** .
2. Dans **paramètres**, sélectionnez **système de**->**sur**->**Join AD Azure**.
<center>
![Joindre Azure AD dans le menu paramètres](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. Cliquez sur **Continuer** dans la fenêtre de message une jointure AD Azure.
<center>
![Fenêtre de message Join Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. Fournir vos informations d’identification d’ouverture de session. Cette expérience de connexion inclut toutes les étapes nécessaires à l’authentification complète. Si vous appartenez à un locataire fédéré, votre administrateur vous fournira l’expérience de fédération qui est hébergé par votre organisation.
<center>
![Fournir des informations d’identification de connexion à](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Si votre organisation a configuré Azure, plusieurs facteurs d’authentification pour joindre à AD Azure, fournir le second facteur avant de continuer.
6. Cliquez sur **Accepter** sur l’écran **d’Autoriser ce périphérique à gérer** .
7. Vous devriez voir le message « votre périphérique est maintenant joint à votre organisation dans Active Directory Azure ».


## <a name="additional-information"></a>Informations supplémentaires
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
* [Authentification des identités sans mots de passe Microsoft Passport](active-directory-azureadjoin-passport.md)
