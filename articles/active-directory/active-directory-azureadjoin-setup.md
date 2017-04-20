<properties
    pageTitle="Configuration de joindre des annonces d’Azure pour vos utilisateurs | Microsoft Azure"
    description="Explique comment les administrateurs peuvent configurer Azure joindre d’Active Directory pour le répertoire de locaux et d’inscription de périphérique."
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
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>Configuration de joindre des annonces d’Azure dans votre organisation

Avant de configurer Azure Active Directory Join (jointure de publicité Azure), vous devez créer manuellement les comptes gérés dans Azure AD ou synchroniser votre répertoire locaux des utilisateurs vers le nuage.

Pour obtenir des instructions détaillées pour la synchronisation de vos utilisateurs sur site AD Azure est couvert dans [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).


Pour créer et gérer des utilisateurs dans Active Directory Azure manuellement, reportez-vous à [Gestion des utilisateurs dans Active Directory Azure](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Configurer l’enregistrement de dispositifs
1. Ouvrez une session sur le portail Azure en tant qu’administrateur.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet **répertoire** , sélectionnez le répertoire.
4. Sélectionnez l’onglet **configurer** .
5. Passez à la section **Devices** .
6. Sous l’onglet **périphériques** , définissez les éléments suivants :  
   * **Nombre de périphériques par utilisateur maximale**: sélectionnez le nombre maximal de périphériques pour lesquels un utilisateur peut dans Azure AD.  Si un utilisateur atteint ce quota, ils ne seront pas en mesure d’ajouter des périphériques supplémentaires jusqu'à ce qu’un ou plusieurs de leurs périphériques existants sont supprimés.
   * **Exiger plusieurs facteurs AUTH à jointure périphériques**: indiquer si les utilisateurs doivent fournir un second facteur d’authentification pour participer à leur appareil pour Azure AD. Pour plus d’informations sur l’authentification à plusieurs facteurs Azure, consultez [mise en route avec authentification à plusieurs facteurs Azure dans le nuage](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md).
   * **Les utilisateurs risquent de dispositifs de jointure AZURE AD**: sélectionner les utilisateurs et les groupes qui sont autorisés à joindre des périphériques à AD Azure.
   * **Périphériques de joint AD AZURE de sur administrateurs supplémentaires**: avec Azure AD Premium ou à la Suite de mobilité d’entreprise (EMS), vous pouvez choisir les utilisateurs disposent des droits d’administrateur local sur le périphérique. Les administrateurs globaux et propriétaires de dispositifs bénéficient des droits d’administrateur local par défaut.

<center>![Configurer le périphérique enregistrement](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

Après avoir configuré joindre des annonces d’Azure pour vos utilisateurs, ils peuvent se connecter à Active Directory Azure grâce à leurs périphériques d’entreprise ou personnels.

Voici les trois scénarios que vous pouvez utiliser pour autoriser les utilisateurs à configurer de joindre des annonces d’Azure :

- Les utilisateurs rejoindre un périphérique appartenant à une société directement à Active Directory Azure.
- Un périphérique appartenant à l’entreprise dans Active Directory sur site de jonction de domaine utilisateurs puis étendre le périphérique à AD Azure.
- Utilisateurs ajouter les comptes de travail ou de l’école à Windows sur un périphérique personnel

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : méthodes d’utilisation de périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
