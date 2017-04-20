<properties
    pageTitle="Partage des comptes à l’aide d’Active Directory Azure |  Microsoft Azure"
    description="Décrit comment Azure Active Directory permet aux organisations de partager en toute sécurité des comptes pour les applications sur site et services de nuage de consommateur."
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Partage des comptes avec Azure AD

## <a name="overview"></a>Vue d’ensemble
Parfois, les organisations doivent utiliser un nom d’utilisateur unique et un mot de passe pour plusieurs personnes. Cela se produit généralement dans deux cas :

- Lors de l’accès aux applications qui requièrent une connexion unique et un mot de passe pour chaque utilisateur, si les applications sur site ou au consommateur de services en nuage (par exemple, les comptes sociaux d’entreprise).
- Lors de la création des environnements multi-utilisateurs. Avoir un compte local qui dispose de privilèges et peut être utilisé pour le programme d’installation, d’administration et récupération des activités (par exemple, le compte local « administrateur général » pour Office 365) ou le compte de la racine de la force de vente de base.

En règle générale, ces comptes seraient être partagés par distribuer les informations d’identification (nom d’utilisateur/mot de passe) aux personnes appropriées ou de les stocker dans un emplacement partagé où plusieurs agents de confiance peuvent y accéder.

Le modèle traditionnel de partage présente plusieurs inconvénients :

- Activation de l’accès aux nouvelles applications nécessite vous permet de distribuer les informations d’identification à tout le monde a besoin d’accéder.
- Chaque application partagée peut nécessiter son propre jeu d’informations d’identification partagées, obliger les utilisateurs à mémoriser plusieurs jeux d’informations d’identification. Lorsque les utilisateurs doivent mémoriser plusieurs références, le risque augmente qu’ils aura recours à des pratiques risquées. (par exemple, écriture de mots de passe).
- Vous ne pouvez pas savoir qui a accès à une application.
- Vous ne pouvez pas savoir qui a *accès à* une application.
- Lorsque vous avez besoin supprimer l’accès à une application, vous devez mettre à jour les informations d’identification et de les distribuer de nouveau à tout le monde a besoin d’accéder à cette application.

## <a name="azure-active-directory-account-sharing"></a>Le partage de compte Active Directory Azure

Annonce Azure fournit une nouvelle approche à l’aide de comptes partagés qui supprime ces inconvénients.

L’administrateur AD Azure configure les applications un utilisateur peut accéder à l’aide du panneau d’accès et de choisir le type de mieux d’ouverture de session unique adaptée à cette application. Un de ces types, *mot de passe connexion unique*, vous permet d’Azure AD agissent comme une sorte de « courtier » au cours du processus d’ouverture de session pour cette application.

Les utilisateurs se connecteront une fois avec le compte de leur organisation. C’est le même compte que celui qu’ils utilisent régulièrement pour accéder à leur bureau ou un e-mail. Ils peuvent découvrir et que les applications qui leur sont affectés pour. Avec les comptes partagés, cette liste d’applications peut inclure n’importe quel nombre d’informations d’identification partagés. L’utilisateur final n’a pas besoin de vous souvenir ni d’écrire vers le bas les différents comptes, qu'ils peuvent utiliser.

Les comptes partagés non seulement d’augmentent la visibilité et améliorent la facilité d’utilisation, ils améliorent également votre sécurité. Les utilisateurs disposant d’autorisations pour utiliser les informations d’identification ne voyez pas le mot de passe partagé, mais au lieu de cela Obtient l’autorisation d’utiliser le mot de passe dans le cadre d’un flux d’authentification orchestrée. De plus, avec certaines applications d’authentification de mot de passe, vous pouvez avoir Azure AD périodiquement le survol (mise à jour) le mot de passe à l’aide de mots de passe complexe, l’augmentation de la sécurité des comptes. L’administrateur peut facilement accorder ou révoquer l’accès à une application et également de savoir qui a accès au compte et qui a eu accès dans le passé.

Annonce Azure prend en charge les comptes partagés pour toute Suite de mobilité d’entreprise (EMS), Premium ou Basic aux utilisateurs sous licence, pour tous les types de mot de passe unique signer sur les applications. Vous pouvez partager des comptes pour des milliers d’applications déjà intégrées dans la bibliothèque de l’application et que vous pouvez ajouter votre propre application d’authentification de mot de passe avec des [applications d’authentification personnalisées](active-directory-sso-integrate-saas-apps.md).

Les fonctionnalités Active Directory Azure qui permettent le partage de compte :

- [Mot de passe de session unique](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- Mot de passe de session un agent unique
- [Affectation de groupe](active-directory-accessmanagement-self-service-group-management.md)
- Applications de mot de passe personnalisé
- [Application du tableau de bord/rapports d’utilisation](active-directory-passwords-get-insights.md)
- Portails d’accès utilisateur final
- [Proxy de l’application](active-directory-application-proxy-get-started.md)
- [Marketplace de Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Partage d’un compte
Annonce Azure permet de partager un compte, que vous devez :

- Ajouter une application [bibliothèque de l’application](https://azure.microsoft.com/marketplace/active-directory/) ou une [application personnalisée](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Configurer l’application pour le mot de passe Single Sign-On (SSO)
- Utiliser [affectation basées sur les groupes](active-directory-accessmanagement-group-saasapps.md) , puis sélectionnez la possibilité d’entrer des informations d’identification partagées
- Facultatif : dans certaines applications, tels que Facebook, Twitter ou LinkedIn, vous pouvez activer l’option pour le [renversement du mot de passe Active Directory Azure automatisée](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Vous pouvez également sécuriser votre compte partagé avec plusieurs facteurs d’authentification (AMF) (en savoir plus sur la [sécurisation des applications avec Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) et vous pouvez déléguer la capacité de gérer des utilisateurs ayant accès à l’application à l’aide de gestion de groupe [Azure AD libre-service](active-directory-accessmanagement-self-service-group-management.md) .

## <a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Protection des applications à accès conditionnel](active-directory-conditional-access.md)
- [Groupe de libre service gestion/SSA](active-directory-accessmanagement-self-service-group-management.md)
