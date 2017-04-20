<properties
    pageTitle="Configurer un nouveau périphérique à Azure annonce lors de l’installation | Microsoft Azure"
    description="Une rubrique qui explique comment les utilisateurs peuvent configurer Azure Participez à AD lors de leur première expérience d’exécution."
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Configurer un nouveau périphérique à Azure annonce lors de l’installation

Dans Windows, 10, les utilisateurs peuvent rejoindre le leurs périphériques vers Azure Active Directory (AD Azure) dans l’expérience de première exécution (FRX). Cela permet aux entreprises de distribuer des périphériques rétractable pour leurs employés ou des étudiants, ou de les laisser à choisir leurs propres périphériques (CYOD).
Si les éditions Windows 10 Professionnel ou entreprise de 10 Windows est installé sur un périphérique, l’expérience par défaut pour le processus d’installation de périphériques appartenant à la société.

## <a name="to-join-a-device-to-azure-ad"></a>Pour joindre un périphérique à AD Azure


1. Lorsque vous allumez votre nouveau périphérique et que vous démarrez le processus d’installation, vous devez voir le message **d’Obtention d’un prêt** . Suivez les instructions pour configurer votre périphérique.
2. Commencez par la personnalisation de votre région et votre langue. Accepter les termes du contrat de licence logiciel Microsoft.
![Personnaliser pour votre région.](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Sélectionnez le réseau que vous souhaitez utiliser pour la connexion à Internet.
4. Indiquez si vous utilisez un périphérique personnel ou un périphérique appartenant à la société. S’il appartient à la société, cliquez sur **ce périphérique appartient à mon organisation**. Cette opération démarre l’expérience joindre des annonces d’Azure. Voici un écran que vous verrez si vous utilisez Windows 10 Professional.
<center>
![Qui est propriétaire de cet écran PC](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  Entrez les informations d’identification qui vous ont été fournies par votre organisation.
<center>
![Écran de connexion](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  Après avoir entré votre nom d’utilisateur, un client correspondant se trouve dans Azure AD. Si vous êtes dans un domaine fédéré, le système vous affichera à votre serveur de Service (jeton de sécurité) sur site--par exemple, Active Directory Federation Services (ADFS).
7. Si vous êtes un utilisateur dans un domaine non fédéré, entrez vos informations d’identification directement sur la page hébergée par AD Azure. Si la marque de l’entreprise a été configuré, vous également voir le logo de votre société et prend en charge le texte.
8.  Vous êtes invité à entrer un défi d’authentification à plusieurs facteurs. Ce défi est configurable par un administrateur.
9.  Annonce Azure vérifie si cet utilisateur/périphérique requiert l’inscription dans la gestion des périphériques mobiles.
10. Windows enregistre le périphérique dans le répertoire de l’organisation dans Active Directory Azure et s’inscrit dans la gestion des périphériques mobiles, le cas échéant.
11. Si vous êtes un utilisateur géré, Windows ouvre le bureau via le processus de connexion automatique.
12. Si vous êtes un utilisateur fédéré, vous êtes dirigé vers l’écran de connexion Windows pour entrer vos informations d’identification.

> [AZURE.NOTE] Adhésion à un domaine de Windows Server Active Directory local dans l’expérience d’out-of-box Windows n’est pas pris en charge. Par conséquent, si vous souhaitez joindre un ordinateur à un domaine, vous devez sélectionner le lien **configurer Windows avec un compte local** à la place. Vous pouvez ensuite joindre le domaine à partir des paramètres sur votre ordinateur comme vous l’avez fait avant.

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : méthodes d’utilisation de périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mots de passe Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
