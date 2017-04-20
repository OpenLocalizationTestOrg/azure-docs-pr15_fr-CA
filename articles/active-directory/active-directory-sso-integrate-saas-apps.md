<properties
    pageTitle="Intégrer Azure Active Directory SSO avec des applications de SaaS |  Microsoft Azure"
    description="Activer l’authentification d’ouverture de session unique et l’approvisionnement de gestion centralisée d’accès des applications SaaS dans Azure Active Directory de l’utilisateur. Vue d’ensemble de l’intégration Active Directory de Azure pour les applications SaaS."
    services="active-directory"
      keywords="intégrer les applications SaaS Azure AD"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Intégrer Azure Active Directory SSO avec des applications de SaaS  

> [AZURE.SELECTOR]
- [Azure portal](active-directory-enterprise-apps-manage-sso.md)
- [Azure portal classique](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Pour commencer la configuration de l’authentification unique pour une application que vous importez dans votre organisation, vous utiliserez un répertoire existant dans Azure Active Directory (AD Azure). Vous pouvez utiliser un répertoire AD Azure que vous obtenez par le biais de Microsoft Azure, Office 365 ou Windows Intune. Si vous avez deux ou plusieurs d'entre eux, consultez [administrer votre annuaire AD Azure](active-directory-administer.md) pour déterminer celle à utiliser.

## <a name="authentication"></a>Authentification

Pour les applications qui prennent en charge le SAML 2.0, WS-Federation, ou protocoles OpenID connecter, Azure Active Directory les utilise pour établir des relations d’approbation des certificats de signature. Pour plus d’informations à ce sujet, reportez-vous à la section [Gestion des certificats pour fédéré de l’authentification unique](active-directory-sso-certs.md).

Pour les applications qui prennent en charge uniquement HTML basée sur les formulaires reconnectez-vous, Azure Active Directory utilise le « stockage de mot de passe » pour établir des relations d’approbation. Cela permet aux utilisateurs de votre organisation à être automatiquement connecté à une application SaaS par AD Azure en utilisant les informations de compte d’utilisateur de l’application SaaS. Annonce Azure collecte et stocke en toute sécurité les informations de compte d’utilisateur et le mot de passe associé. Pour plus d’informations, reportez-vous à la section [mot de passe de l’authentification unique](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorisation

Un compte configuré permet à un utilisateur être autorisés à utiliser une application une fois qu’ils sont authentifiés par le biais de l’authentification unique. Approvisionnement de l’utilisateur peut le faire manuellement, ou dans certains cas, vous pouvez ajouter et supprimer des informations utilisateur à partir de l’application de SaaS en fonction des modifications apportées à Active Directory de Azure. Pour plus d’informations sur l’utilisation des connecteurs Active Directory Azure existants pour la mise en service automatique, consultez [automatique utilisateur mise en service et mise hors service pour les applications SaaS](active-directory-saas-app-provisioning.md).

Dans le cas contraire, vous pouvez manuellement ajouter des informations à une application, ou utiliser d’autres solutions de déploiement qui sont disponibles sur le marché.

## <a name="access"></a>Accès

Annonce Azure offre plusieurs moyens de personnalisables pour déployer des applications pour les utilisateurs de votre organisation. Vous ne sont pas verrouillées dans une solution d’accès ou un déploiement particulier. Vous pouvez utiliser [la solution qui correspond le mieux à vos besoins](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Considérations supplémentaires pour les applications déjà en cours d’utilisation

Configuration de l’authentification unique sur pour que votre organisation utilise déjà une application est un processus différent du processus de création de comptes pour une nouvelle application. Il existe quelques étapes préliminaires, y compris : mappage des identités d’utilisateur de l’application pour les identités AD Azure et comprendre comment les utilisateurs connaissent la connexion à une application après son intégration.

> [AZURE.NOTE] Pour configurer l’authentification unique pour une application existante, vous devez disposer des droits d’administrateur global dans les deux AD Azure et les applications SaaS.

### <a name="mapping-user-accounts"></a>Mappage des comptes d’utilisateurs

Identité d’un utilisateur est généralement un identificateur unique qui peut être une adresse de messagerie ou un nom d’utilisateur principal (UPN). Vous devez lier (map) identité de chaque utilisateur de l’application à leur identité d’Azure AD. Il existe deux façons de procéder selon la manière dont la demande d’authentification de votre application.

Pour plus d’informations sur le mappage des identités d’application avec des identités AD Azure, consultez [personnalisation les revendications dans le jeton SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) et les [mappages d’attributs de personnalisation pour la mise en service](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Présentation des journaux de l’utilisateur dans l’expérience

Lorsque vous intégrez l’authentification unique pour une application qui est déjà en cours d’utilisation, il est important de comprendre que l’expérience utilisateur sera affectée. Pour toutes les applications, les utilisateurs démarre pour vous connecter à l’aide de leurs informations d’identification Active Directory Azure. Il peut s’agir également qu’ils doivent utiliser un portail différent pour accéder aux applications.

L’authentification unique pour certaines applications peut être effectuée sur la connexion de l’application dans l’interface, mais pour d’autres applications, l’utilisateur devra passer par un portail central des ([Mes applications](http://myapps.microsoft.com) [Office 365](http://portal.office.com/myapps)) pour vous connecter. Pour en savoir plus sur les différents types d’authentification unique et de leur expérience de l’utilisateur dans [l’accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

Une autre ressource précieuse est le *consentement de l’utilisateur de Suppressing* dans l’article de [développeurs de guidage](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Étapes suivantes


Pour les applications SaaS que vous trouvez dans la bibliothèque de l’application, AD Azure fournit un certain nombre de [didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md).

Si l’application n’est pas dans la galerie de l’application, vous pouvez [l’ajouter à la galerie de App AD Azure sous la forme d’une application personnalisée](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Il existe beaucoup plus de détails sur l’ensemble de ces problèmes dans la bibliothèque Azure.com, à partir de [Quel est l’accès aux applications et à l’ouverture de session unique avec Active Directory de Azure.](active-directory-appssoaccess-whatis.md).

## <a name="see-also"></a>Voir aussi

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
