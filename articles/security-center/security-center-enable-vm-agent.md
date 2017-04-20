<properties
   pageTitle="Activer l’Agent de machine virtuelle dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du centre de sécurité Azure **Activer l’Agent de machine virtuelle**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="enable-vm-agent-in-azure-security-center"></a>Activer l’Agent de machine virtuelle dans le centre de sécurité Azure

L’Agent de la machine virtuelle doit être installé sur les machines virtuelles (VM) afin de [permettre la collecte de données](security-center-enable-data-collection.md).  Azure le centre de sécurité vous permet de voir les ordinateurs virtuels nécessitent l’Agent de la machine virtuelle et recommandons d’activer l’Agent de la machine virtuelle sur ces ordinateurs virtuels.

L’Agent de la machine virtuelle est installé par défaut pour les ordinateurs virtuels qui sont déployés à partir de l’Azure Marketplace. L’article [Agent de la machine virtuelle et Extensions – partie 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’Agent de la machine virtuelle.


> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la **lame de recommandations**, sélectionnez **Activer l’Agent de machine virtuelle**.
![Activer l’Agent de la machine virtuelle][1]

2. La lame **VM Agent est manquant ou ne répond pas**s’affiche. Cette blade répertorie les ordinateurs virtuels qui nécessitent l’Agent de la machine virtuelle. Suivez les instructions sur la lame pour installer l’agent de la machine virtuelle.
![Agent de la machine virtuelle est manquant][2]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md): Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md), découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md)--Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md), découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md)--rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/): obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
