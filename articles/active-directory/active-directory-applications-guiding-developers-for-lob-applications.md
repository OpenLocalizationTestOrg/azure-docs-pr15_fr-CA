<properties
    pageTitle="Annonce Azure et les Applications : guider les développeurs | Microsoft Azure"
    description="Destiné aux professionnels de l’informatique, cet article fournit des instructions pour l’intégration des applications Azure avec Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Annonce Azure et les applications : développez des applications professionnelles de

Ce guide fournit une vue d’ensemble du développement d’applications (LoB) de line-of-business pour Azure Active Directory (AD). Est destiné aux administrateurs globaux d’Active Directory/Office 365.

## <a name="overview"></a>Vue d’ensemble

Création d’applications intégrées à AD Azure permet aux utilisateurs de votre organisation single sign-on avec Office 365. Demander à l’application dans Azure d’Active Directory vous permet de que contrôler la stratégie d’authentification de l’application. Pour en savoir plus sur conditional access et comment protéger des applications avec une authentification multifacteur (AMF) voir [règles d’accès de configuration](active-directory-conditional-access-azuread-connected-apps.md).

Enregistrer votre application pour utiliser Azure Active Directory. Inscrit l’application signifie que vos développeurs peuvent utiliser AD Azure pour authentifier les utilisateurs et demander l’accès à des ressources telles que le courrier électronique, de calendrier et les documents de l’utilisateur.

Tout membre de votre répertoire (pas invités) pouvez enregistre l’application, également appelée *Création d’un objet application*.

Inscription d’une application permet à tout utilisateur à effectuer les opérations suivantes :

- Obtenir une identité d’application qui reconnaît les annonces Azure
- Obtenir des secrets/clés que l’application peut utiliser pour s’authentifier à Active Directory
- Marque l’application dans Azure portal avec un nom personnalisé, le logo, etc..
- Appliquez les fonctionnalités de l’autorisation de AD Azure pour leur application, y compris :
  - Contrôle d’accès basé sur les rôles (RBAC)
  - Azure Active Directory en tant que serveur d’autorisation oAuth (secure une API exposée par l’application)

- Déclarer les autorisations requises nécessaires à l’application de fonctionner comme prévu, y compris :-autorisations de l’application (pour les administrateurs globaux uniquement). Par exemple : l’appartenance aux rôles dans une autre annonce Azure application ou du rôle d’appartenance par rapport à un Azure ressource, groupe de ressources, ou l’abonnement - autorisations déléguées (n’importe quel utilisateur). Par exemple : AD Azure, ouverture de session et le profil de lecture


> [AZURE.NOTE]Par défaut, tout membre peut enregistrer une application. Pour savoir comment limiter les autorisations de l’enregistrement d’applications à des membres spécifiques, voir [comment les applications sont ajoutées à AD Azure](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Voici ce que vous, l’administrateur global, devez faire pour aider les développeurs à rendre leurs applications prêt pour la production :

- Configurer les règles d’accès (stratégie d’accès/AMF)
- Configurer l’application pour exiger l’affectation de l’utilisateur et affecter des utilisateurs
- Supprimer l’expérience de consentement d’utilisateur par défaut

## <a name="configure-access-rules"></a>Configurer les règles d’accès

Configurer les règles d’accès de l’application à vos applications SaaS. Par exemple, vous pouvez exiger AMF ou autoriser uniquement l’accès aux utilisateurs des réseaux approuvés. Les détails de ce sont disponibles dans le document de [règles d’accès de configuration](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurer l’application pour exiger l’affectation de l’utilisateur et affecter des utilisateurs

Par défaut, les utilisateurs peuvent accéder des applications sans assignée. Toutefois, si l’application expose des rôles ou si vous souhaitez que l’application s’affiche sur le panneau d’accès d’un utilisateur, vous devez demander l’affectation de l’utilisateur.

[Nécessitant l’affectation de l’utilisateur](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Si vous êtes un abonné Azure AD Premium ou Suite de mobilité d’entreprise (EMS), nous recommandons vivement l’utilisation de groupes. Affectation de groupes à l’application vous permet de déléguer la gestion des accès permanent au propriétaire du groupe. Vous pouvez créer un groupe, ou demandez à la personne responsable de votre organisation pour créer un groupe à l’aide du centre de gestion de groupe.

[Affectation d’utilisateurs à une application](active-directory-applications-guiding-developers-assigning-users.md)  
[Affectation de groupes à une application](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Supprimer le consentement de l’utilisateur

Par défaut, chaque utilisateur accède, grâce à un consentement pour vous connecter. L’expérience de consentement, en demandant aux utilisateurs d’accorder des autorisations à une application, peut être déconcertant pour les utilisateurs qui ne sont pas familiarisés avec la prise de telles décisions.

Pour les applications de confiance, vous pouvez simplifier l’expérience utilisateur en autorisez l’application au nom de votre organisation.

Pour plus d’informations sur le consentement de l’utilisateur et de l’expérience préalable dans Azure, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).

##<a name="related-articles"></a>Articles connexes

- [Activer l’accès distant sécurisé à des applications sur site avec le Proxy d’Application Azure AD](active-directory-application-proxy-get-started.md)
- [Aperçu d’Azure accès conditionnel pour les applications SaaS](active-directory-conditional-access-azuread-connected-apps.md)
- [Gestion de l’accès aux applications avec Azure AD](active-directory-managing-access-to-apps.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
