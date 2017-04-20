<properties
    pageTitle="Dépannage des problèmes d’accès Azure Active Directory | Microsoft Azure"
    description="Découvrez les étapes que vous pouvez prendre pour résoudre les problèmes d’accès avec les ressources de votre organisation en ligne."
    services="active-directory"
    keywords="accès conditionnel basé sur le périphérique, l’enregistrement de dispositifs, activer l’enregistrement du périphérique, l’enregistrement de dispositifs et MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Dépannage des problèmes d’accès Azure Active Directory

Vous essayez d’accéder à l’intranet de votre entreprise SharePoint Online, et vous obtenez un message d’erreur « accès refusé ». Que fais-tu ?

Cet article décrit les étapes de conversion qui peuvent vous aider à résoudre les problèmes d’accès avec les ressources de votre organisation en ligne.

Pour tenter de résoudre Azure Active Directory (AD Azure) accéder aux problèmes, passez à la section de l’article qui traite de la plate-forme de périphérique :

-   Périphérique Windows
-   périphérique d’e/s (Vérifiez arrière pour obtenir de l’aide avec l’iPhone et iPads.)
-   Appareil Android (consultez plus rapidement pour obtenir de l’aide Android téléphones et des tablettes.)

## <a name="access-from-a-windows-device"></a>Accès à partir d’un périphérique Windows

Si votre périphérique utilise une des plates-formes suivantes, rechercher dans les sections suivantes pour le message d’erreur qui s’affiche lorsque vous essayez d’accéder à une application ou un service :

- Windows 10
- 8.1 de Windows
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>DISPOSITIF n’est pas enregistré.

Si votre périphérique n’est pas enregistré avec AD Azure et que l’application est protégée par une stratégie basée sur le périphérique, vous pouvez voir une page qui affiche l’un de ces messages d’erreur :

![« Vous ne pouvez pas y accéder à partir d’ici » des messages pour des dispositifs non enregistrés] (./media/active-directory-conditional-access-device-remediation/01.png "Scénario")

Si votre périphérique est joint au domaine d’Active Directory dans votre organisation, procédez comme suit :

1.  Assurez-vous que vous vous connectez à Windows à l’aide de votre compte professionnel (votre compte Active Directory).
2.  Se connecter à votre réseau d’entreprise via un réseau privé virtuel (VPN) ou de DirectAccess.
3.  Après que vous être connecté, appuyez sur la touche du logo Windows + la touche L pour verrouiller votre session Windows.
4.  Entrez vos informations d’identification du compte de travail pour déverrouiller votre session Windows.
5.  Attendez une minute et essayez de nouveau d’accéder à l’application ou le service.
6.  Si vous voyez la même page, cliquez sur le lien **plus d’informations** et contactez votre administrateur avec les détails.

Si votre périphérique n’est pas joint au domaine et exécute Windows 10, vous avez deux options :

- Exécution jointure AD Azure
- Ajouter votre compte de travail ou de l’école à Windows

Pour plus d’informations sur la façon dont ces options sont différentes, voir [les périphériques à l’aide de Windows 10 dans votre espace de travail](active-directory-azureadjoin-windows10-devices.md).

Pour exécuter joindre des annonces d’Azure, procédez comme suit pour la plate-forme de que votre périphérique utilise. (Jointure de publicité azure n’est pas disponible sur les téléphones Windows.)

**Mise à jour de Windows anniversaire 10**

1.  Ouvrez **l’application** .
2.  Cliquez sur **comptes** > **travail d’accès ou à l’école**.
3.  Cliquez sur **se connecter**.
4.  Cliquez sur **joindre ce périphérique pour Azure AD**.
5.  S’authentifier auprès de votre organisation, de fournir une authentification multifactorielle si vous y êtes invité, puis suivez les étapes qui sont affichent.
6.  Se déconnecter et puis connectez-vous avec votre compte professionnel.
7.  Réessayez d’accéder à l’application.


**Mise à jour de Windows 10 novembre 2015**

1.  Ouvrez **l’application** .
2.  Cliquez sur **système de** > **sur**.
3.  Cliquez sur **joindre AD Azure**.
4.  S’authentifier auprès de votre organisation, de fournir une authentification multifactorielle si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Se déconnecter et puis connectez-vous à votre compte de travail (votre compte Azure AD).
6.  Réessayez d’accéder à l’application.

Pour ajouter votre compte de travail ou à l’école, procédez comme suit :

**Mise à jour de Windows anniversaire 10**

1.  Ouvrez **l’application** .
2.  Cliquez sur **comptes** > **travail d’accès ou à l’école**.
3.  Cliquez sur **se connecter**.
4.  S’authentifier auprès de votre organisation, de fournir une authentification multifactorielle si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Réessayez d’accéder à l’application.


**Mise à jour de Windows 10 novembre 2015**

1.  Ouvrez **l’application** .
2.  Cliquez sur **comptes** > **vos comptes**.
3.  Cliquez sur **Ajouter un travail ou à l’école de compte**.
4.  S’authentifier auprès de votre organisation, de fournir une authentification multifactorielle si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Réessayez d’accéder à l’application.

Si votre périphérique n’est pas à un domaine et qu’il exécute Windows 8.1, pour faire une jointure d’espace de travail et de s’inscrire à Microsoft Intune, procédez comme suit :

1.  Ouvrir les **Paramètres PC**.
2.  Cliquez sur **le réseau** > **espace de travail**.
3.  Cliquez sur **joindre**.
4.  S’authentifier auprès de votre organisation, de fournir une authentification multifactorielle si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Cliquez sur **Activer**.
6.  Réessayez d’accéder à l’application.


### <a name="browser-is-not-supported"></a>Navigateur n’est pas pris en charge.

Vous pouvez être refusé si vous tentez d’accéder à une application ou un service à l’aide d’un des navigateurs suivants :

- Chrome, Firefox ou tout autre navigateur que Microsoft Edge ou Microsoft Internet Explorer dans Windows 10 ou Windows serveur 2016
- Firefox dans Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2

Vous verrez une page d’erreur qui ressemble à ceci :

![« Vous ne pouvez pas y accéder à partir d’ici » de message pour les navigateurs non pris en charge] (./media/active-directory-conditional-access-device-remediation/02.png "Scénario")

La seule mise à jour est d’utiliser un navigateur qui prend en charge de l’application pour votre plate-forme de périphérique.

## <a name="next-steps"></a>Étapes suivantes

[Azure accès conditionnel de Active Directory](active-directory-conditional-access.md)
