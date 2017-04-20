

<properties
    pageTitle="Joindre un périphérique personnel à votre organisation | Microsoft Azure"
    description="Explique comment les utilisateurs peuvent enregistrer leurs périphériques Windows 10 personnels à leur réseau d’entreprise et fournit des étapes de déploiement d’un scénario BYOD."
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

# <a name="join-a-personal-device-to-your-organization"></a>Joindre un périphérique personnel à votre organisation.

## <a name="to-join-a-windows-10-device-to-your-organization"></a>Pour joindre un périphérique Windows 10 à votre organisation.

1.  Dans le menu **Démarrer** , sélectionnez **paramètres**.
2.  Sélectionnez les **comptes**, puis cliquez sur **votre compte**.
3.  Cliquez sur **Ajouter le travail ou le compte de l’établissement**et tapez dans vos comptes.
4.  Sur la page de connexion pour votre organisation, entrez votre nom d’utilisateur et le mot de passe, puis cliquez sur **OK**.
5.  Vous serez invité pour un défi d’authentification à plusieurs facteurs. (Ce défi est configurable par un administrateur).
6.  Azure Active Directory (AD Azure) vérifie si le périphérique nécessite une inscription de gestion de périphérique mobile.
7.  Windows enregistre le périphérique dans le répertoire de l’organisation dans Active Directory Azure et s’inscrit dans la gestion des périphériques mobiles, le cas échéant.
8.  Si vous êtes un utilisateur géré, Windows présente sur le bureau automatique ouverture de session.
9.  Si vous êtes un utilisateur fédéré, vous allez être dirigé vers un écran de connexion Windows pour entrer vos informations d’identification.

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : méthodes d’utilisation de périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mots de passe Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
