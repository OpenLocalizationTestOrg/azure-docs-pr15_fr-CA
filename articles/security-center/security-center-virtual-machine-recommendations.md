<properties
   pageTitle="Protection de vos machines virtuelles dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document répond recommandations dans le centre de sécurité Azure qui vous aident à protéger vos ordinateurs virtuels et restent en conformité avec les stratégies de sécurité."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Protection de vos machines virtuelles dans le centre de sécurité Azure

Le centre de sécurité Azure analyse l’état de la sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires.  Recommandations s’appliquent à des types de ressources Azure : le machines virtuelles (VM), mise en réseau, les applications et SQL.

Cet article aborde les recommandations qui s’appliquent aux ordinateurs virtuels.  Machine virtuelle recommandations se concentrent sur la collecte des données, mises à jour système, mise en service de logiciel anti-programme malveillant, crypter vos disques VM et bien plus encore.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations disponibles de la machine virtuelle et que chacun d’eux faire si vous l’appliquez.

## <a name="available-vm-recommendations"></a>Recommandations de mémoire virtuelle disponibles

|Recommandation|Description|
|-----|-----|
|[Activer la collecte des données pour les abonnements](security-center-enable-data-collection.md)|Vous recommande d’activer la collecte des données de la stratégie de sécurité pour chacun de vos abonnements et toutes les machines virtuelles (VM) dans vos abonnements.|
|[Correction des vulnérabilités du système d’exploitation](security-center-remediate-os-vulnerabilities.md)|Par exemple, recommande que vous alignez vos configurations du système d’exploitation avec les règles de configuration recommandée, ne permettent pas d’enregistrer les mots de passe.|
|[Appliquer les mises à jour du système](security-center-apply-system-updates.md)|Recommande de déployer la sécurité du système manquant et mises à jour critiques aux machines virtuelles.|
|[Redémarrage après les mises à jour du système](security-center-apply-system-updates.md#reboot-after-system-updates)|Vous conseille de redémarrer un ordinateur virtuel pour terminer le processus d’application des mises à jour du système.|
|[Installer la Protection du point de terminaison](security-center-install-endpoint-protection.md)|Vous recommande de mettre en service les programmes contre les logiciels malveillants pour les ordinateurs virtuels (VM Windows uniquement).|
|[Résoudre les alertes de fonctionnement de la Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recommande que vous résolvez les échecs de protection de point de terminaison.|
|[Activer l’Agent de la machine virtuelle](security-center-enable-vm-agent.md)|Vous permet de savoir quels ordinateurs virtuels nécessitent l’Agent de la machine virtuelle. L’Agent de la machine virtuelle doit être installé sur les ordinateurs virtuels pour fournir des correctifs d’analyse, d’analyse de ligne de base et les programmes contre les logiciels malveillants. L’Agent de la machine virtuelle est installé par défaut pour les ordinateurs virtuels qui sont déployés à partir de l’Azure Marketplace. L’article [Agent de la machine virtuelle et Extensions – partie 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’Agent de la machine virtuelle.|
| [Appliquer le cryptage de disque](security-center-apply-disk-encryption.md) |Recommande que vous chiffrez vos disques VM à l’aide du cryptage de disque Azure (Windows et Linux VMs). Le cryptage est recommandé pour les volumes à la fois le système d’exploitation et les données sur votre ordinateur virtuel.|
| [Version de mise à jour du système d’exploitation](security-center-update-os-version.md) | Vous recommande de mettre à jour la version du système d’exploitation (OS) pour votre Service Cloud avec la version la plus récente disponible pour votre famille de système d’exploitation.  Pour en savoir plus sur les Services en nuage, consultez la [vue d’ensemble des Services en nuage](../cloud-services/cloud-services-choose-me.md). |
| [Évaluation de la vulnérabilité non installée](security-center-vulnerability-assessment-recommendations.md) | Recommande d’installer une solution d’évaluation de vulnérabilité de votre machine virtuelle. |
| [Correction des vulnérabilités](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Vous pouvez ainsi voir les vulnérabilités système et des applications détectées par la solution d’évaluation de vulnérabilité installée sur votre machine virtuelle. |

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressource Azure, consultez les rubriques suivantes :

- [Protection de vos applications dans le centre de sécurité Azure](security-center-application-recommendations.md)
- [Protection de votre réseau dans le centre de sécurité Azure](security-center-network-recommendations.md)
- [Protection de votre service Azure SQL Azure centre de sécurité](security-center-sql-service-recommendations.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
