<properties
    pageTitle="Les FS AMF et AD Azure | Microsoft Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui décrit la procédure de mise en route avec Azure AMF et AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na" ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Mise en route avec Azure, plusieurs facteurs d’authentification et les Services de fédération Active Directory



<center>![Nuage](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si votre organisation a fédéré sur site Active Directory avec Azure Active Directory à l’aide d’ADFS, il existe deux options pour l’utilisation de l’authentification multifactorielle Azure.

- Sécuriser les ressources de cloud à l’aide de l’authentification multifactorielle Azure ou Active Directory Federation Services
- Sécuriser les ressources de cloud et sur site à l’aide du serveur de l’authentification multifactorielle Azure

Le tableau suivant résume l’expérience de vérification entre sécurisation des ressources avec authentification à plusieurs facteurs Azure et AD FS

|Expérience de vérification - applications basées sur la navigation|Expérience de vérification - les applications Non basés sur un navigateur
:------------- | :------------- | :------------- |
Sécurisation des ressources Azure AD à l’aide de l’authentification multifactorielle Azure |<li>La première étape de vérification est effectuée à l’aide d’ADFS sur site.</li> <li>La deuxième étape est une méthode basée sur le téléphone, effectuée à l’aide de l’authentification de nuage.</li>|Utilisateurs finaux peuvent utiliser des mots de passe app pour ouvrir une session.
Sécurisation des ressources Azure AD à l’aide d’Active Directory Federation Services |<li>La première étape de vérification est effectuée à l’aide d’ADFS sur site.</li><li>La deuxième étape est effectuée sur place par honorer la demande.</li>|Utilisateurs finaux peuvent utiliser des mots de passe app pour ouvrir une session.

Avertissements avec des mots de passe app pour les utilisateurs fédérés :

- Les mots de passe App sont vérifiées à l’aide de l’authentification du nuage, elles contournent la fédération. Fédération est uniquement utilisée lors de la définition d’un mot de passe de l’application.
- Paramètres de contrôle d’accès au Client local ne sont pas respectés par les mots de passe app.
- Vous perdez sur site capacité enregistrement de l’authentification des mots de passe de l’application.
- Désactivation/suppression du compte peut prendre jusqu'à trois heures pour la synchronisation d’annuaire, retarder la désactivation/suppression des mots de passe app dans l’identité du nuage.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la configuration d’authentification à plusieurs facteurs Azure, soit le serveur d’authentification selon plusieurs facteurs Azure avec AD FS, consultez les articles suivants :

- [Sécuriser les ressources de cloud à l’aide d’authentification à plusieurs facteurs Azure et AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Sécuriser les ressources de cloud et sur site à l’aide du serveur de l’authentification multifactorielle Azure avec Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Sécuriser les ressources de cloud et sur site à l’aide du serveur de l’authentification multifactorielle Azure avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
