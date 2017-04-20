<properties
    pageTitle="FAQ - Services de domaine ActiveDirectory Azure | Microsoft Azure"
    description="Forum aux questions sur Azure des Services de domaine Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Les Services de domaine ActiveDirectory Azure : Forum aux Questions (FAQ)

Cette page répond aux questions fréquemment posées sur les Services de domaine Active Directory Azure. Conserver la vérification des mises à jour.

### <a name="troubleshooting-guide"></a>Guide de dépannage
Consultez notre [guide de dépannage](active-directory-ds-troubleshooting.md) des solutions aux problèmes courants rencontrés lors de la configuration ou de l’administration des Services de domaine Active Directory de Azure.


### <a name="configuration"></a>Configuration de

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>Est-il possible de créer plusieurs domaines pour un seul répertoire de publicité Azure ?
N° Vous ne pouvez créer qu’un seul domaine pris en charge par les Services de domaine Active Directory de Azure pour un seul répertoire d’Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Puis-je activer les Services de domaine Active Directory de Azure dans un réseau virtuel du Gestionnaire de ressources Azure ?
N° Les Services de domaine Active Directory Azure ne peuvent être activées que dans un réseau virtuel Azure classique. Vous pouvez vous connecter au réseau virtuel classique à un réseau virtuel du Gestionnaire de ressources à l’aide d’homologation pour utiliser votre domaine géré dans un réseau virtuel du Gestionnaire de ressources de réseau virtuel.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Puis-je effectuer des Services de domaine Active Directory de Azure disponible dans plusieurs réseaux virtuels au sein de mon abonnement ?
Le service lui-même ne gère pas directement ce scénario. Les Services de domaine Active Directory Azure est disponible dans un seul réseau virtuel à la fois. Toutefois, vous pouvez configurer la connectivité entre plusieurs réseaux virtuels pour exposer des Services de domaine Active Directory de Azure à d’autres réseaux virtuels. Cet article décrit comment vous pouvez [connecter des réseaux virtuels dans Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Puis-je activer les Services de domaine Active Directory de Azure à l’aide de PowerShell ?
PowerShell/automatisé de déploiement des Services de domaine Active Directory de Azure n’est pas disponible actuellement.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Les Services de domaine Active Directory de Azure n’est disponible dans le nouveau portail Azure ?
N° Les Services de domaine Active Directory Azure peuvent être configurés uniquement dans [Azure portal classique](https://manage.windowsazure.com). Nous prévoyons d’étendre la prise en charge pour le [portail Azure](https://portal.azure.com) dans le futur.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Puis-je ajouter des contrôleurs de domaine d’un domaine géré de Services de domaine Active Directory de Azure ?
N° Le domaine fourni par les Services de domaine Active Directory de Azure est un domaine géré. Vous n’avez pas besoin de déployer, de configurer ou de gérer les contrôleurs de domaine pour ce domaine - ces activités de gestion sont fournies en tant que service de Microsoft. Par conséquent, vous ne pouvez pas ajouter de contrôleurs de domaine supplémentaires (en lecture-écriture ou en lecture seule) pour le domaine géré.

### <a name="administration-and-operations"></a>Administration et opérations

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Puis-je connecter au contrôleur de domaine de mon domaine géré à l’aide du Bureau à distance ?
N° Vous ne disposez pas des autorisations pour se connecter à des contrôleurs de domaine pour le domaine géré via Bureau à distance. Les membres du groupe « Administrateurs de contrôleur de domaine DAS » peuvent administrer le domaine géré à l’aide des outils d’administration Active Directory tels que le centre de Administration Active Directory (ADAC) ou Active Directory PowerShell. Ces outils sont installés à l’aide de la fonctionnalité « Outils d’Administration de serveur distant » sur un serveur Windows joint au domaine géré.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>J’ai activé les Services de domaine Active Directory de Azure. Le compte d’utilisateur à utiliser sur les machines de jointure de domaine pour ce domaine ?
Les utilisateurs que vous avez ajoutés au groupe d’administration (par exemple, « DAS DC administrateurs') pourront machines d’adjonction au domaine. En outre, les utilisateurs de ce groupe sont accordées accès Bureau à distance pour les ordinateurs qui ont été liés au domaine.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>Puis-je exercer strict des privilèges administrateur de domaine pour le domaine fourni par les Services de domaine Active Directory de Azure ?
N° Des privilèges d’administrateur sur le domaine géré n’est pas accordé. Des privilèges administrateur du domaine et d’administrateur de l’entreprise ne sont pas disponibles, vous pouvez utiliser dans le domaine. Administrateur de domaine existant ou des groupes d’administrateurs entreprise au sein de votre annuaire AD Azure ne sont également pas accordées des privilèges d’administrateur sur le domaine domaine/entreprise.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>Puis-je modifier les appartenances aux groupes à l’aide de LDAP ou autres outils d’administration Active Directory sur les domaines fournies par les Services de domaine Active Directory de Azure ?
N° Impossible de modifier les appartenances au groupe sur les domaines pris en charge par les Services de domaine Active Directory de Azure. Il en va de même pour les attributs de l’utilisateur. Vous pouvez toutefois modifier des attributs utilisateur ou appartenance aux groupes dans AD Azure ou sur votre domaine local. Ces modifications sont automatiquement synchronisées avec les Services de domaine Active Directory de Azure.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>Puis-je prolonger le schéma du domaine fourni par les Services de domaine Active Directory de Azure ?
N° Le schéma est géré par Microsoft pour le domaine géré. Extensions de schéma ne sont pas pris en charge par les Services de domaine Active Directory de Azure.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Puis-je modifier ou ajouter des enregistrements DNS dans mon domaine géré ?
Oui. Les utilisateurs qui appartiennent au groupe « Administrateurs du contrôleur de domaine DAS » sont des privilèges « administrateur DNS », pour modifier les enregistrements DNS dans le domaine géré. Ces utilisateurs peuvent utiliser la console du Gestionnaire de DNS sur un ordinateur exécutant Windows Server est joint au domaine géré, à gérer le système DNS. Pour utiliser la console du Gestionnaire DNS, installer 'Outils du serveur DNS », qui fait partie de la fonctionnalité facultative de « Outils d’Administration de serveur distant » sur le serveur. Plus d’informations sur les [utilitaires d’administration, la surveillance et la résolution des problèmes DNS](https://technet.microsoft.com/library/cc753579.aspx) sont disponibles sur le site TechNet.


### <a name="billing-and-availability"></a>Facturation et disponibilité

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Est qu'un service payant des Services de domaine Active Directory de Azure ?
Oui. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Existe-t-il une version d’évaluation gratuite pour le service ?
Ce service est inclus dans la version d’évaluation gratuite pour Azure. Vous pouvez vous inscrire à une [version d’évaluation gratuite d’un mois d’Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>Puis-je obtenir les Services de domaine Active Directory de Azure dans le cadre de la Suite Enterprise mobilité paginée (EMS) ?
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Prime de publicité Azure pour utiliser les Services de domaine Active Directory de Azure ai-je besoin ?
N° Les Services de domaine Active Directory Azure est un paiement à l’utilisation des services Azure et ne fait pas partie d’EMS. Les Services de domaine Active Directory Azure sont disponibles pour toutes les éditions de publicité Azure (libre, de base et, Premium) et sont facturés sur une base horaire, selon l’utilisation.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Quelles régions Azure est le service disponible dans ?
Reportez-vous à la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) pour afficher la liste des régions Azure où les Services de domaine Active Directory de Azure est disponible.
