<properties
    pageTitle="Vulnérabilités détectées par la Protection d’identité Azure Active Directory | Microsoft Azure"
    description="Vue d’ensemble des vulnérabilités détectées par la Protection d’identité Azure Active Directory."
    services="active-directory"
    keywords="protection d’identité Azure ActiveDirectory, découverte d’application cloud, gestion des applications, sécurité, risques, le niveau de risque, vulnérabilité, stratégie de sécurité"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnérabilités détectées par la Protection d’identité Azure Active Directory 

Les vulnérabilités sont les points faibles de votre environnement qui peut être exploitée par un attaquant. Nous vous recommandons de résoudre ces problèmes pour améliorer la sécurité de votre organisation et d’empêcher les pirates d’exploiter les.
<br><br>
![vulnérabilités](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

Les sections suivantes fournissent une vue d’ensemble des vulnérabilités signalées par la Protection de l’identité.

## <a name="multi-factor-authentication-registration-not-configured"></a>Enregistrement de l’authentification multicritères non configuré 

Cette vulnérabilité permet de contrôler le déploiement d’Azure, plusieurs facteurs d’authentification dans votre organisation. 

Authentification à plusieurs facteurs Azure fournit une seconde couche de sécurité à l’authentification utilisateur. Il vous aide à protéger l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il fournit une authentification forte via une gamme d’options de vérification facile, appel téléphonique, message texte ou application mobile notification ou la vérification de code et 3e partie serment jetons.

Nous vous recommandons de dont vous avez besoin d’Azure, plusieurs facteurs d’authentification pour les connexions utilisateur. Une authentification multifactorielle joue un rôle essentiel dans les stratégies en fonction du risque d’accès conditionnel disponibles par le biais de la Protection d’identité.

Pour plus d’informations, consultez [Nouveautés Azure une authentification multifactorielle ?](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>Applications non managées de nuage

Cette vulnérabilité permet d’identifier les applications non managées nuage dans votre organisation.
 
Dans les entreprises modernes, les services informatiques sont souvent pas connaissance de toutes les applications de cloud à l’aide d’utilisateurs de leur organisation pour effectuer leur travail. Il est facile de voir la raison pour laquelle les administrateurs auront préoccupations concernant l’accès non autorisé aux données de l’entreprise, les fuites de données possibles et autres risques de sécurité. 

Nous recommandons de déployer découverte d’application Cloud pour découvrir les applications en nuage non managée et pour gérer ces applications Azure Active Directory à l’aide de votre organisation.

Pour plus d’informations, consultez [recherche des applications en nuage non managé avec le nuage App découverte](active-directory-cloudappdiscovery-whatis.md).



##<a name="security-alerts-from-privileged-identity-management"></a>Alertes de sécurité à partir de gestion des identités privilégié

Cette vulnérabilité permet de découvrir et résoudre les alertes sur les identités privilégiées dans votre organisation.  

Pour permettre aux utilisateurs d’effectuer des opérations privilégiées, les entreprises doivent accorder un accès privilégié aux utilisateurs temporaires ou permanents dans Azure AD, ressources Azure ou Office 365, ou autres applications SaaS. Chacun de ces utilisateurs privilégiés augmente la surface d’attaque de votre organisation. Cette vulnérabilité permet d’identifier les utilisateurs avec un accès privilégié inutile et prend les mesures appropriées pour réduire ou éliminer le risque qu’ils représentent. 

Il est recommandé que votre organisation utilise la gestion des identités Azure AD privilégié à gérer, de contrôler et moniteur privilégié identités et leur accès aux ressources dans Active Directory Azure ainsi que les autres services en ligne de Microsoft Office 365 ou Microsoft Intune.

Pour plus d’informations, voir [Gestion des identités Azure AD privilégié](active-directory-privileged-identity-management-configure.md). 



## <a name="see-also"></a>Voir aussi

 - [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)
