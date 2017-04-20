<properties
   pageTitle="Présentation du contrôle et gestion de la sécurité Azure | Microsoft Azure"
   description=" Azure fournit des mécanismes de sécurité à l’aide de la gestion et de surveillance de services cloud Azure et les machines virtuelles.  Cet article fournit une vue d’ensemble de ces fonctionnalités de sécurité de base et les services. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Présentation du contrôle et gestion de la sécurité Azure

Azure fournit des mécanismes de sécurité à l’aide de la gestion et de surveillance de services cloud Azure et les machines virtuelles. Cet article fournit une vue d’ensemble de ces fonctionnalités de sécurité de base et les services. Des liens sont fournis pour les articles qui vous donne les détails de chaque afin d’en savoir plus.

La sécurité de vos services de cloud de Microsoft est un partenariat et un partage des responsabilités entre vous et Microsoft. Responsabilité partagée signifie que Microsoft est responsable de la Microsoft Azure et centres de sécurité physique de ses données (à l’aide de protections de sécurité tels que les portes d’entrée verrouillée badge, des clôtures et des gardes). En outre, Azure fournit des niveaux élevés de sécurité nuage à la couche logicielle qui répond aux besoins de sécurité, de confidentialité et de la conformité de ses clients les plus exigeantes.

Vous possédez vos données et des identités, la responsabilité de la protection, la sécurité de vos ressources sur site et la sécurité des composants de nuage que vous contrôlez. Microsoft vous fournit des contrôles de sécurité et de fonctionnalités pour vous aider à protéger vos données et applications. Votre responsable de la sécurité est basée sur le type de service en nuage.

Le diagramme suivant récapitule le solde de la responsabilité de Microsoft et le client.

![Responsabilité partagée][1]

Pour approfondir gestion de la sécurité, consultez [gestion de la sécurité dans Azure](azure-security-management.md).

Voici les principales fonctionnalités qui seront traités dans cet article :

- Contrôle d’accès basé sur les rôles
- Contre les logiciels malveillants
- Plusieurs facteurs d’authentification
- ExpressRoute
- Passerelles de réseau virtuel
- Gestion des identités privilégié
- Protection d’identité
- Centre de sécurité

## <a name="role-based-access-control"></a>Contrôle d’accès basé sur les rôles

Contrôle d’accès basée sur les rôles (RBAC) fournit la gestion des accès précis pour les ressources d’Azure. L’utilisation de RBAC, vous pouvez accorder des personnes que la quantité d’accès dont ils ont besoin pour effectuer leur travail.  RBAC peut également vous aider à vous assurer que lorsque les utilisateurs quittent l’organisation ils perdent l’accès aux ressources dans le nuage.

Pour en savoir plus :

- [Blog de l’équipe Active Directory sur RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Contrôle d’accès basé sur rôle de Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Contre les logiciels malveillants

Avec Azure, vous pouvez utiliser des logiciels anti-programmes malveillants à partir des fournisseurs de sécurité majeures telles que Microsoft, Symantec, Trend Micro, McAfee et Kaspersky pour aider à protéger vos ordinateurs virtuels à partir des fichiers malveillants, les logiciels publicitaires et autres menaces.

Microsoft Antimalware vous offre la possibilité d’installer un agent contre les logiciels malveillants pour les rôles de PaaS et les machines virtuelles. Basé sur System Center Endpoint Protection, cette fonctionnalité apporte éprouvée sur site des technologies de sécurité dans le nuage.

Nous proposons également l’intégration en profondeur pour la de tendance [Sécurité](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ et [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produits dans la plateforme Azure. DeepSecurity est une solution Antivirus et SecureCloud est une solution de cryptage. Ordinateurs virtuels à l’aide d’un modèle d’extension sera déployé sur DeepSecurity. Utilisez le portail d’interface utilisateur et PowerShell, vous pouvez choisir d’utiliser des DeepSecurity dans les nouveaux ordinateurs virtuels qui sont en cours lancés ou des machines virtuelles existantes qui ont déjà été déployés.

Protection de Point de terminaison de Symantec (SEP) est également pris en charge sur Azure. Grâce à l’intégration de portail, les clients ont la possibilité de spécifier leur intention d’utiliser SEP au sein d’une machine virtuelle. Indicateur de continuation peut être installé sur un ordinateur de novice virtuel via le portail Azure ou peut être installé sur une VM existante à l’aide de PowerShell.

Pour en savoir plus :

- [Déploiement de Solutions contre les logiciels malveillants sur les ordinateurs virtuels Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Logiciel anti-programme malveillant de Microsoft pour les Services en nuage Azure et les Machines virtuelles](../security/azure-security-antimalware.md)
- [Comment faire pour installer et configurer la sécurité Trend Micro en tant que Service sur un ordinateur virtuel de Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Comment faire pour installer et configurer Symantec Endpoint Protection sur un ordinateur virtuel de Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nouvelles Options de logiciels anti-programmes malveillants pour protéger les ordinateurs virtuels Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Plusieurs facteurs d’authentification

Authentification à plusieurs facteurs Azure (AMF) est une méthode d’authentification qui requiert l’utilisation de plus d’une méthode de vérification et ajoute une couche de sécurité critique pour les connexions utilisateur et des transactions. AMF contribue à garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il fournit une authentification forte via une gamme d’options de vérification, appel téléphonique, message texte ou application mobile notification ou la vérification de code et 3e partie serment jetons.

Pour en savoir plus :

- [Plusieurs facteurs d’authentification](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Quel est l’authentification multifacteur Azure ?](../multi-factor-authentication/multi-factor-authentication.md)
- [Fonctionnement de l’authentification à plusieurs facteurs Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute vous permet d’étendre vos réseaux locaux dans le nuage de Microsoft via une connexion privée dédiée facilitée par un fournisseur de connectivité. ExpressRoute, vous pouvez établir des connexions aux services de cloud de Microsoft, comme Microsoft Azure, Office 365 et CRM en ligne. Connectivité peut provenir d’un réseau (IP VPN) pour tout, un réseau Ethernet de point à point ou une connexion croisée virtuelle via un fournisseur de connectivité dans un centre de colocalisation. ExpressRoute les connexions ne passent pas sur l’Internet public. Cela permet des connexions ExpressRoute offre plus la fiabilité, vitesses plus rapides, des latences inférieurs et une plus grande sécurité que les connexions classiques sur Internet.

Pour en savoir plus :

- [Vue d’ensemble technique de ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Passerelles de réseau virtuel

Les passerelles VPN, également appelé Azure des passerelles de réseau virtuel, sont utilisés pour envoyer le trafic réseau entre les réseaux virtuels et les emplacements des locaux. Ils sont également utilisés pour envoyer du trafic entre plusieurs réseaux virtuels dans Azure (VNet à VNet).  Passerelles VPN offrent une connectivité sécurisée de la coexistence entre Azure et votre infrastructure.

Pour en savoir plus :

- [À propos des passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Vue d’ensemble de la sécurité réseau Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Gestion des identités privilégié

Parfois, les utilisateurs ont besoin effectuer des opérations privilégiées dans Azure ressources ou d’autres applications SaaS. Cela signifie souvent organisations disposent pour leur donner un accès privilégié permanent dans Azure Active Directory (AD Azure). Il s’agit d’un risque croissant pour les ressources de hébergé sur le nuage, car les entreprises ne peuvent pas surveiller suffisamment ce que font les utilisateurs avec l’accès privilégié.
En outre, si un compte d’utilisateur avec accès privilégié est compromis, cette seule brèche peut affecter la sécurité globale de votre nuage. La gestion des identités AD privilégié Azure vous aide à résoudre ce risque en réduisant le temps d’exposition de privilèges et d’augmenter la visibilité de l’utilisation.  

La gestion des identités privilégié introduit le concept d’un administrateur temporaire pour un rôle ou un « juste à temps » administrateur l’accès, qui est l’utilisateur qui doit exécuter un processus d’activation pour ce rôle. Le processus d’activation remplace l’affectation de l’utilisateur à un rôle dans Active Directory Azure période inactif à actif, pour une durée spécifiée par exemple 8 heures.

Pour en savoir plus :

- [Gestion des identités Azure AD privilégié](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Mise en route de la gestion des identités Azure AD privilégié](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Protection d’identité

Protection de l’identité Active Directory (AD) Azure offre une vue consolidée de reconnectez-vous activités suspectes et des vulnérabilités potentielles pour aider à protéger votre entreprise. Protection d’identité détecte les activités suspectes pour les utilisateurs et les identités privilégié (administrateur), basées sur des signaux telles que les attaques de force brute, une fuite des informations d’identification et des connexions à partir des emplacements inhabituels et infectés des périphériques.

En fournissant des notifications et la mise à jour recommandée, Protection d’identité permet de limiter les risques en temps réel. Il calcule la gravité des risques utilisateur, et vous pouvez configurer des stratégies fondées sur le risque pour automatiquement aider l’application de sauvegarde accès contre les menaces futures.

Pour en savoir plus :

- [Protection d’identité Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Channel 9 : Les annonces Azure et afficher d’identité : aperçu de Protection d’identité](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centre de sécurité

Azure le centre de sécurité vous aide à empêcher, détecter et répondre aux menaces et fournit le qu'augmentation de visibilité et de contrôler, de la sécurité de vos ressources d’Azure. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

Centre de sécurité vous aide à optimiser et à contrôler la sécurité de vos ressources Azure par :

- L’activation vous permet de définir des stratégies pour les ressources de votre abonnement Azure en fonction des besoins de sécurité de votre société et le type des applications ou des données dans chaque abonnement.
- Surveillance de l’état de vos machines virtuelles Azure, mise en réseau et des applications.
- Fourniture d’une liste hiérarchisée d’alertes de sécurité, notamment les alertes de solutions de partenaires intégrées, ainsi que les informations que nécessaires pour effectuer des recherches rapides et les recommandations sur la façon de corriger une attaque.

Pour en savoir plus :

- [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
