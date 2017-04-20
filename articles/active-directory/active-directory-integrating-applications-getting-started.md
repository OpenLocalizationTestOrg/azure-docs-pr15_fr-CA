<properties
   pageTitle="Guide de démarrage rapide intégrant Azure Active Directory lors de l’obtention des applications |  Microsoft Azure"
   description="Cet article est un guide de mise en route pour l’intégration d’Azure Active Directory (AD) avec des applications sur site et applications en nuage."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guide d’intégration d’Azure Active Directory avec les applications de mise en route
## <a name="overview"></a>Vue d’ensemble
Cette rubrique est destinée à vous donner une feuille de route pour l’intégration des applications avec Azure Active Directory (AD). Chacune des sections ci-dessous contient un bref résumé d’une rubrique plus détaillée pour vous pouvez d’identifier les parties de ce guide de mise en route vous intéressent.  Suivez les liens pour obtenir de plus amples sur chaque sujet.

## <a name="before-you-begin-take-inventory"></a>Avant de commencer, prenez l’inventaire
Avant de passer dans pour intégrer des applications AD Azure, il est important de savoir où vous êtes et où vous souhaitez aller.  Les questions suivantes sont destinées à vous aider à réfléchir à votre projet d’intégration application Azure AD.

### <a name="application-inventory"></a>Inventaire des applications
- Où sont l’ensemble de vos applications ? Leur propriétaire ?
- Quel type d’authentification requiert vos applications ?
- Qui a besoin d’accéder à des applications ?
- Vous souhaitez déployer une nouvelle application ?
  - Vous génériez en interne et le déployer sur une instance d’Azure compute ?
  - Vous utiliserez qui est disponible dans la galerie des applications Azure ?

### <a name="user-and-group-inventory"></a>Stock d’utilisateurs et de groupes
- Où vos comptes utilisateur ne résident pas ?
 - Sur site Active Directory
 - Annonce Azure
 - Dans une base de données d’application distinct dont vous êtes propriétaire
 - Dans les applications non sanctionnées
 - Tous les éléments ci-dessus
- Les autorisations et les attributions de rôle des utilisateurs actuellement ont-ils ? Vous devez passer en revue leur accès ou vous êtes sûr que vos affectations de rôle et de l’accès utilisateur sont appropriées maintenant ?
- Les groupes sont déjà établis dans Active Directory sur site ?
 - Comment sont organisées les vos groupes ?
 - Qui sont les membres du groupe ?
 - Les affectations d’autorisations/rôle les groupes actuellement ont-ils ?
- Vous devez nettoyer les bases de données utilisateur ou un groupe avant d’intégrer ?  (Il s’agit d’une question très importante. Garbage in, garbage out).

### <a name="access-management-inventory"></a>Stock de gestion d’accès
- Comment gérez-vous actuellement l’accès aux applications ? Que doit-il changer ?  Avez-vous d’autres méthodes de gestion des accès, comme avec [RBAC](role-based-access-control-configure.md) par exemple ?
- Qui a besoin d’accéder à quoi ?

Vous ne disposez peut-être les réponses à toutes ces questions dès le départ, mais c’est OK.  Ce guide peut vous aider à répondre à certaines de ces questions et à prendre certaines décisions en toute connaissance de cause.

## <a name="prerequisites"></a>Conditions préalables
- Un abonnement Azure et un répertoire Azure Active Directory.  Si vous ne disposez pas d’un abonnement Azure, vous pouvez essayer Azure gratuitement pendant 30 jours. [Essayez-le !](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Intégration d’applications avec Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Recherche non sanctionnée applications en nuage avec découverte d’application Cloud
Comme mentionné ci-dessus, il existe peut-être des applications qui n’ont pas été gérées par votre organisation jusqu'à présent.  Dans le cadre du processus de stock, il est possible de trouver des applications en nuage non sanctionnée. Consultez [recherche des applications en nuage non sanctionnée avec la découverte de nuage App](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Types d’authentification
Chacune de vos applications peut avoir des exigences d’authentification différents. Avec AD Azure, les certificats de signature peuvent être utilisé avec les applications qui utilisent SAML 2.0, WS-Federation, ou protocoles de connexion OpenID ainsi que mot de passe de session unique. Pour plus d’informations sur l’application les types d’authentification pour une utilisation avec Active Directory Azure voir [Gestion des certificats pour fédéré Single Sign-On dans Azure Active Directory](active-directory-sso-certs.md) et le [mot de passe selon l’authentification unique](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>L’activation de l’authentification unique avec un Proxy d’application AD Azure
Avec Microsoft Azure le Proxy d’Application Active Directory, vous pouvez fournir un accès aux applications que qui se trouve à l’intérieur de votre réseau privé sécurisé, depuis n’importe où et sur n’importe quel périphérique. Après avoir installé un connecteur de proxy d’application au sein de votre environnement, il peut être facilement configuré avec AD Azure.

### <a name="integrating-applications-with-azure-ad"></a>Intégration des applications avec Azure AD
Les articles suivants traitent des différentes façons d’applications intègrent à AD Azure et fournissent des conseils.

- [Détermination de Active Directory à utiliser](active-directory-administer.md)
- [L’utilisation d’applications dans la galerie des applications Azure](active-directory-appssoaccess-whatis.md)
- [Intégration de liste des didacticiels applications SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Gestion de l’accès aux applications
Les articles suivants décrivent les méthodes que vous pouvez gérer l’accès aux applications une fois qu’ils ont été intégrés avec Azure AD à l’aide des connecteurs Active Directory Azure et d’Azure AD.

- [Gestion de l’accès aux applications à l’aide d’Active Directory Azure](active-directory-managing-access-to-apps.md)
- [Automatisation avec les connecteurs Active Directory Azure](active-directory-saas-app-provisioning.md)
- [Affectation d’utilisateurs à une application](active-directory-applications-guiding-developers-assigning-users.md)
- [Affectation de groupes à une application](active-directory-applications-guiding-developers-assigning-groups.md)
- [Partage des comptes](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Intégration d’applications personnalisées
Si vous écrivez une nouvelle application et que vous souhaitez aider les développeurs à l’exploitant la puissance AD Azure, consultez [les développeurs de guidage](active-directory-applications-guiding-developers-for-lob-applications.md).

Si vous souhaitez ajouter votre application personnalisée à la galerie des applications Azure, reportez-vous à la section [« Apportez votre propre application », avec une configuration Azure AD libre-service SAML](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Voir aussi

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
