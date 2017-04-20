<properties
   pageTitle="Protection de service Azure SQL Azure centre de sécurité | Microsoft Azure"
   description="Ce document répond dans le centre de sécurité Azure des recommandations qui vous aident à protéger les SQL Azure service et restent conforme aux stratégies de sécurité."
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

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>Protection de service Azure SQL Azure centre de sécurité

Le centre de sécurité Azure analyse l’état de la sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires.  Recommandations s’appliquent à des types de ressources Azure : le machines virtuelles (VM), mise en réseau, les applications et SQL.

Cet article aborde les recommandations qui s’appliquent au service d’Azure SQL.  Centre recommandations de service SQL Azure autour de l’activation de l’audit pour les serveurs SQL d’Azure et les bases de données et en activant le cryptage pour les bases de données SQL.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations du service SQL disponibles et que chacun d’eux faire si vous l’appliquez.

## <a name="available-sql-service-recommendations"></a>Recommandations de service SQL disponibles

|Recommandation|Description|
|-----|-----|
|[Activer le serveur SQL d’audit](security-center-enable-auditing-on-sql-servers.md)|Recommande que vous activez l’audit pour les serveurs SQL d’Azure (service d’Azure SQL uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer l’audit de SQL de la base de données](security-center-enable-auditing-on-sql-databases.md)|Recommande que vous activez l’audit pour les bases de données SQL d’Azure (service d’Azure SQL uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer le chiffrement Transparent des données sur les bases de données SQL](security-center-enable-transparent-data-encryption.md)|Recommande d’activer le cryptage pour les bases de données SQL (service Azure SQL uniquement).|

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressource Azure, consultez les rubriques suivantes :

- [Protection de vos machines virtuelles dans le centre de sécurité Azure](security-center-virtual-machine-recommendations.md)
- [Protection de vos applications dans le centre de sécurité Azure](security-center-application-recommendations.md)
- [Protection de votre réseau dans le centre de sécurité Azure](security-center-network-recommendations.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
