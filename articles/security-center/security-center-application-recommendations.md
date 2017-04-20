<properties
   pageTitle="Protection de vos applications dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document répond recommandations dans le centre de sécurité Azure qui vous aident à protègent vos applications Azure et restent en conformité avec les stratégies de sécurité."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-applications-in-azure-security-center"></a>Protection de vos applications dans le centre de sécurité Azure

Le centre de sécurité Azure analyse l’état de la sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires.  Recommandations s’appliquent à des types de ressources Azure : le machines virtuelles (VM), mise en réseau, les applications et SQL.

Cet article aborde les recommandations qui s’appliquent aux applications.  Centre de recommandations d’application autour de déploiement d’un pare-feu d’application web.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations d’application disponible et que chacun d’eux faire si vous l’appliquez.

## <a name="available-application-recommendations"></a>Recommandations d’application disponible

|Recommandation|Description|
|-----|-----|
|[Ajouter un pare-feu pour applications web](security-center-add-web-application-firewall.md)|Recommande de déployer un pare-feu pour applications web pour les points de terminaison web (WAF). Vous pouvez protéger plusieurs applications web dans le centre de sécurité en ajoutant ces applications à vos déploiements WAF existants. Les appliances WAF (créés avec le modèle de déploiement du Gestionnaire de ressources) doivent être déployés sur un réseau virtuel distinct. Les appliances WAF (créés avec le modèle de déploiement classique) sont limités à l’utilisation d’un groupe de sécurité du réseau. Cette prise en charge sera étendue à l’avenir à un déploiement personnalisé d’un appareil de WAF (classique).|
|[Finalisation de la protection d’application](security-center-add-web-application-firewall.md#finalize-application-protection)|Pour terminer la configuration d’un WAF, trafic doit être redirigé vers la solution matérielle-logicielle WAF. Le respect de cette recommandation terminera les modifications de configuration nécessaires.|

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressource Azure, consultez les rubriques suivantes :

- [Protection de vos machines virtuelles dans le centre de sécurité Azure](security-center-virtual-machine-recommendations.md)
- [Protection de votre réseau dans le centre de sécurité Azure](security-center-network-recommendations.md)
- [Protection de votre service Azure SQL Azure centre de sécurité](security-center-sql-service-recommendations.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
