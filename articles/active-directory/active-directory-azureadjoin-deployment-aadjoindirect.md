<properties
    pageTitle="Scénarios d’utilisation et les considérations relatives au déploiement pour joindre des annonces d’Azure | Microsoft Azure"
    description="Explique comment les administrateurs peuvent configurer joindre des annonces d’Azure pour leurs utilisateurs (employés, étudiants, autres utilisateurs). Il décrit également les différents scénarios réels pour l’utilisation de joindre des annonces d’Azure."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< balises ms.service= « active directory » ms.workload="identity » ms.tgt_pltfrm="na » ms.devlang="na » ms.topic="article » ms.date="09/27/2016 »

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scénarios d’utilisation et les considérations relatives au déploiement pour joindre des annonces d’Azure

## <a name="usage-scenarios-for-azure-ad-join"></a>Scénarios d’utilisation pour joindre des annonces d’Azure
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scénario 1 : Les entreprises en grande partie dans le nuage

Azure Active Directory Join (jointure de publicité Azure) avantages si vous faire fonctionner et gérer les identités de votre entreprise dans le nuage ou déplacez vers le nuage dès. Vous pouvez utiliser un compte que vous avez créé dans AD Azure pour vous connecter à Windows 10. Par le biais [du processus de la première expérience d’exécution (FRX)](active-directory-azureadjoin-user-frx.md), ou en joignant Azure AD dans [le menu Paramètres](active-directory-azureadjoin-user-upgrade.md), vos utilisateurs peuvent joindre leurs machines à AD Azure.  Vos utilisateurs peuvent également profiter de sign-on (SSO) accès unique aux ressources de cloud à Office 365, dans leur navigateur ou dans les applications Office.

### <a name="scenario-2-educational-institutions"></a>Scénario 2 : Les établissements d’enseignement

Les établissements d’enseignement ont généralement deux types d’utilisateur : faculté et aux étudiants. Membres du corps enseignant sont considérés comme les membres de l’organisation à plus long terme. Il est souhaitable de créer les comptes locaux pour eux. Mais les étudiants sont shorter-term membres de l’organisation et leurs comptes peuvent être gérés dans Active Directory Azure. Cela signifie qu’échelle de répertoire peut être envoyé vers le nuage au lieu d’être stockées en local. Cela signifie également que les stagiaires seront en mesure de vous connecter à Windows avec leurs comptes AD Azure et obtenir l’accès aux ressources d’Office 365 dans les applications Office.

### <a name="scenario-3-retail-businesses"></a>Scénario 3 : Les entreprises de vente au détail

Les entreprises de vente au détail ont des travailleurs saisonniers et les employés à long terme. En général, vous créez des comptes locaux et utilisez des ordinateurs de domaine pour les employés à temps plein à plus long terme. Travailleurs saisonniers sont shorter-term membres de l’organisation mais il est souhaitable de gérer leurs comptes où les licences utilisateur peuvent être plus facilement déplacés. Lorsque vous créez leurs comptes d’utilisateurs dans le nuage avec des licences d’Office 365, ces utilisateurs obtiennent les avantages de l’ouverture de session Windows et Office des applications avec un compte Azure, si vous conservez une plus grande flexibilité avec leurs licences après la formation.

### <a name="scenario-4-additional-scenarios"></a>Scénario 4 : Autres scénarios

Les avantages décrits plus haut, vous bénéficiez d’avoir à vos utilisateurs de joindre leurs périphériques à Azure annonce une jointure simplifiée, gestion des périphériques efficace, d’inscription de gestion de dispositif mobile automatique et ouverture de session unique sur Azure AD et ressources sur site.  


## <a name="deployment-considerations-for-azure-ad-join"></a>Considérations relatives au déploiement pour joindre des annonces d’Azure

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Autoriser les utilisateurs à joindre un périphérique appartenant à une société directement à Active Directory Azure


Les entreprises peuvent fournir nuage uniquement des comptes aux organisations et sociétés partenaires. Ces partenaires facilement aux applications d’entreprise et des ressources à l’aide de l’authentification unique. Ce scénario s’applique aux utilisateurs qui accèdent aux ressources principalement dans le nuage, telles que les applications Office 365 ou SaaS qui s’appuient sur Azure AD pour l’authentification.

### <a name="prerequisites"></a>Conditions préalables
**Au niveau de l’entreprise (administrateur)**

*   Abonnement Azure avec Azure Active Directory  

**Au niveau de l’utilisateur**

*   Windows 10 (éditions Professional et Enterprise)

### <a name="administrator-tasks"></a>Tâches de l’administrateur
* [Configurer l’enregistrement de dispositifs](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tâches de l’utilisateur
* [Configurer un nouveau périphérique Windows 10 avec Azure AD lors de l’installation](active-directory-azureadjoin-user-frx.md)
* [Configurer un périphérique Windows 10 avec AD Azure à partir du menu Paramètres](active-directory-azureadjoin-user-upgrade.md)
* [Participez à un périphérique Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>Activer BYOD dans votre organisation pour Windows 10
Vous pouvez configurer vos utilisateurs et vos employés d’utiliser leurs ressources et périphériques personnels de Windows (BYOD) pour accéder aux applications d’entreprise. Vos utilisateurs peuvent ajouter leur compte Azure AD (comptes de l’école ou de travail) sur un périphérique Windows personnel pour accéder aux ressources d’une façon sécurisée et compatible.

### <a name="prerequisites"></a>Conditions préalables
**Au niveau de l’entreprise (administrateur)**

*   Abonnement de publicité Azure

**Au niveau de l’utilisateur**

*   Windows 10 (éditions Professional et Enterprise)


### <a name="administrator-tasks"></a>Tâches de l’administrateur

* [Configurer l’enregistrement de dispositifs](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tâches de l’utilisateur
* [Participez à un périphérique Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : méthodes d’utilisation de périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mots de passe Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
