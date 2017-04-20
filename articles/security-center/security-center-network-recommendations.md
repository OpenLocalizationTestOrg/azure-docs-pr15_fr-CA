<properties
   pageTitle="Protection de votre réseau dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document répond recommandations dans le centre de sécurité Azure qui vous aident à protéger votre réseau Azure et restent en conformité avec les stratégies de sécurité."
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

# <a name="protecting-your-network-in-azure-security-center"></a>Protection de votre réseau dans le centre de sécurité Azure

Le centre de sécurité Azure analyse l’état de la sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires.  Recommandations s’appliquent à des types de ressources Azure : le machines virtuelles (VM), mise en réseau, les applications et SQL.

Cet article aborde les recommandations qui s’appliquent à votre réseau.  Centre de recommandations de réseau autour de pare-feux de prochaine génération, groupes de sécurité réseau, configuration des règles de trafic entrant et bien plus encore.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations du réseau disponible et que chacun d’eux faire si vous l’appliquez.

## <a name="available-network-recommendations"></a>Recommandations de réseau disponible

|Recommandation|Description|
|-----|-----|
|[Ajouter un pare-feu de génération suivant](security-center-add-next-generation-firewall.md)|Recommande que vous ajoutez une prochaine génération de pare-feu Conviction d’un partenaire Microsoft pour augmenter la protection de votre sécurité.|
|[Acheminement du trafic via Conviction uniquement](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recommande de configurer les règles de groupe (NSG) de sécurité de réseau qui force le trafic entrant de votre machine virtuelle par le biais de votre Conviction.|
|[Activer des groupes de sécurité réseau sur les sous-réseaux ou les machines virtuelles](security-center-enable-network-security-groups.md)|Recommande d’activer NSGs sur les sous-réseaux ou les ordinateurs virtuels.|
|[Restreindre l’accès par le biais de Internet vers le point de terminaison](security-center-restrict-access-through-internet-facing-endpoints.md)|Recommande de configurer les règles de trafic entrant pour NSGs.|

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressource Azure, consultez les rubriques suivantes :

- [Protection de vos machines virtuelles dans le centre de sécurité Azure](security-center-virtual-machine-recommendations.md)
- [Protection de vos applications dans le centre de sécurité Azure](security-center-application-recommendations.md)
- [Protection de votre service Azure SQL Azure centre de sécurité](security-center-sql-service-recommendations.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
