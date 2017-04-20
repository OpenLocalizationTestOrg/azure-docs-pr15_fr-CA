<properties
    pageTitle="Sécurisation de l’accès privilégié dans Azure annonce | Microsoft Azure"
    description="Une rubrique qui explique les approches de la sécurisation de l’accès privilégié sur Azure, Azure Active Directory et de Microsoft Online Services."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Sécurisation de l’accès privilégié dans Azure AD

Sécurisation de l’accès privilégié est une première étape essentielle pour vous aider à protéger les ressources de l’entreprise dans une entreprise moderne. Comptes privilégiés sont ceux qui les administrer et de gérer des systèmes informatiques. Cyber-pirates ciblent ces comptes pour accéder aux données et les systèmes d’une entreprise. Pour sécuriser l’accès privilégié, vous devez isoler les comptes et les systèmes contre le risque d’exposition à un utilisateur malveillant.

Plus d’utilisateurs commencent à obtenir un accès privilégié par l’intermédiaire de services en nuage. Cela peut inclure les administrateurs globaux d’Office 365, administrateurs d’abonnement Azure et les utilisateurs qui disposent d’un accès administratif dans les ordinateurs virtuels ou sur les applications SaaS.

Microsoft vous recommande de que suivre ces instructions sur la [Sécurisation de l’accès privilégié](https://technet.microsoft.com/library/mt631194.aspx).

Pour les clients à l’aide d’Azure Active Directory pour gérer l’accès à Azure, Office 365, ou autres services de Microsoft et les applications, ces principes s’appliquent si les comptes d’utilisateurs sont gérés et authentifiés par Active Directory ou dans Azure Active Directory. Les sections suivantes fournissent plus d’informations sur les fonctionnalités Active Directory Azure pour prendre en charge la sécurisation de l’accès privilégié.

## <a name="multi-factor-authentication"></a>Plusieurs facteurs d’authentification

Pour augmenter la sécurité de l’authentification de l’administrateur, vous devez exiger une authentification multifactorielle (AMF) avant l’octroi de privilèges. AMF est une méthode de vérification qui vous êtes requiert l’utilisation de plus qu’un nom d’utilisateur et le mot de passe. Il fournit une couche de sécurité pour les connexions utilisateur et des transactions.

Azure plusieurs facteurs d’authentification vous permet de garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il fournit une authentification forte via une gamme d’options de vérification facile, y compris les appels téléphoniques, messages texte, notifications d’une application mobile, ou code de vérification et les jetons de serment 3ème partie.

Pour une vue d’ensemble du fonctionnement de l’authentification à plusieurs facteurs Azure consultez la vidéo suivante.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

Pour plus d’informations, consultez [AMF pour Office 365 et AMF pour Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilèges liés au temps

Certaines organisations peuvent estimer qu’ils ont un trop grand nombre d’utilisateurs dans des rôles disposant de privilèges élevés. Un utilisateur peut ont été ajouté au rôle pour une activité donnée, telles que se pour abonner à un service, mais n’avez pas utilisé ces privilèges fréquemment par la suite.

Pour réduire le temps d’exposition de privilèges et augmentez votre visibilité sur leur utilisation, limiter les utilisateurs à prendre uniquement sur leurs privilèges juste in Time (JIT), lorsqu’ils ont besoin pour effectuer une tâche. Pour Azure Active Directory et de Microsoft Online Services, vous pouvez utiliser [la gestion des identités Azure AD privilégié (GIP)](http://aka.ms/AzurePIM).


![Tableau de bord PIM][2]


## <a name="attack-detection"></a>Détection des attaques

[Protection d’identité Azure Active Directory](../active-directory-identityprotection.md) fournit une vue consolidée des événements de risques et des vulnérabilités potentielles affectant les identités de votre entreprise. Basé sur les événements de risques, Protection d’identité calcule un niveau de risque d’utilisateur pour chaque utilisateur, ce qui vous permet de configurer les stratégies fondées sur le risque pour protéger automatiquement l’identité de votre organisation. Ces stratégies, ainsi que d’autres contrôles d’accès conditionnel fournies par Active Directory de Azure et EMS, peuvent automatiquement bloquer l’utilisateur ou proposer des suggestions qui incluent des réinitialisations de mot de passe et la mise en œuvre de l’authentification à plusieurs facteurs.

![Protection d’identité AD Azure][3]

## <a name="conditional-access"></a>Accès conditionnel

Avec contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification d’un utilisateur, avant d’autoriser l’accès à une application. Lorsque ces conditions sont réunies, l’utilisateur est authentifié et autorisé à accéder à l’application.


![Définition des règles d’accès conditionnel avec AMF][4]


## <a name="related-articles"></a>Articles connexes

- Activer [l’authentification selon plusieurs facteur Azure](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Activer la [Gestion d’identité Azure AD privilégié](../active-directory-privileged-identity-management-configure.md)
- Activer la [Protection de l’identité de publicité Azure](../active-directory-identityprotection.md)
- Activer les [contrôles d’accès conditionnel](../active-directory-conditional-access.md)


Pour plus d’informations sur la création d’un plan de sécurité complète, reportez-vous à la section « responsabilités du client et de la feuille de route » du document [Microsoft Cloud Security for Enterprise Architects](http://aka.ms/securecustomer) . Pour plus d’informations sur l’engagement de services de Microsoft pour vous aider à ces rubriques, contactez votre représentant Microsoft ou visitez notre [page de solutions de sécurité](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
