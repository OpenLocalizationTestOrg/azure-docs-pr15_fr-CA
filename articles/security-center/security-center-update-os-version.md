<properties
   pageTitle="Version de mise à jour du système d’exploitation dans le centre de sécurité Azure | Microsoft Azure"
   description="Cet article vous montre comment implémentent la recommandation du centre de sécurité Azure **version de mise à jour du système d’exploitation**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="update-os-version-in-azure-security-center"></a>Mettre à jour la version du système d’exploitation dans le centre de sécurité Azure

Pour les machines virtuelles (VM) dans les services en nuage, centre de sécurité Azure recommandera que le système d’exploitation (OS) mis à jour si une version plus récente est disponible.  Nuage uniquement les services les rôles web et worker en cours d’exécution dans la production d’emplacements surveillés.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez la **version de mise à jour du système d’exploitation**.
![Version de mise à jour du système d’exploitation][1]

2. La **version de mise à jour du système d’exploitation**de la lame s’ouvre. Suivez les étapes de cette lame à mettre à jour la version du système d’exploitation.

## <a name="see-also"></a>Voir aussi

Cet article vous a montré comment implémentent la recommandation du centre de sécurité « Version de mise à jour du système d’exploitation ». Pour plus d’informations sur les services en nuage et de la mise à jour de la version du système d’exploitation pour un service en nuage, consultez :

- [Vue d’ensemble des Services de cloud](../cloud-services/cloud-services-choose-me.md)
- [La mise à jour d’un service en nuage](../cloud-services/cloud-services-update-azure-service.md)
- [Comment faire pour configurer les Services en nuage](../cloud-services/cloud-services-how-to-configure-portal.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) : obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
