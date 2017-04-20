<properties
   pageTitle="Le centre de sécurité Azure et les Machines virtuelles Azure | Microsoft Azure"
   description="Ce document vous aide à comprendre comment le centre de sécurité Azure peut vous préserver des ordinateurs virtuels Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Le centre de sécurité Azure et les Machines virtuelles Azure

[Le centre de sécurité Azure](https://azure.microsoft.com/services/security-center/) vous aide à empêcher, détecter et répondre aux menaces. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

Cet article explique comment le centre de sécurité peut vous aider à sécuriser votre Azure Machines virtuelles (VM).

## <a name="why-use-security-center"></a>Pourquoi utiliser le centre de sécurité ?

Centre de sécurité vous aide à protéger les données de machine virtuelle dans Azure en fournissant une visibilité sur les paramètres de sécurité de votre ordinateur virtuel. Lorsque le centre de sécurité protège vos ordinateurs virtuels, les fonctions suivantes seront disponibles :

- Paramètres de sécurité de système d’exploitation (OS) avec les règles de configuration recommandée
- Système de sécurité et des mises à jour critiques qui manquent
- Recommandations de protection de point de terminaison
- Validation de cryptage de disque
- Mise à jour et évaluation des vulnérabilités
- Détection de la menace

En plus de la protection de vos ordinateurs virtuels d’Azure, centre de sécurité fournit également le contrôle de la sécurité et de gestion pour les Services en nuage, les Services d’application, réseaux virtuels et bien plus encore. 

>[AZURE.NOTE] Consultez [Introduction au centre de sécurité Azure](security-center-intro.md) pour en savoir plus sur le centre de sécurité Azure.

## <a name="prerequisites"></a>Conditions préalables

Pour vous familiariser avec le centre de sécurité Azure, vous devez connaître et prendre en compte les éléments suivants :

- Vous devez disposer d’un abonnement à Microsoft Azure. Pour plus d’informations sur les niveaux standard et libres du centre de sécurité, consultez [Tarification des centre de sécurité](https://azure.microsoft.com/pricing/details/security-center/) .
- Envisagez l’adoption de votre centre de sécurité, consultez le [guident des opérations et la planification du centre de sécurité Azure](security-center-planning-and-operations-guide.md) pour en savoir plus sur les considérations de planification et d’opérations.
- Pour plus d’informations concernant la prise en charge du système d’exploitation, consultez le [Centre de sécurité Azure Forum aux questions (FAQ)](security-center-faq.md). 

## <a name="set-security-policy"></a>Définir la stratégie de sécurité

Collecte de données doit être activé afin que le centre de sécurité Azure permet de rassembler les informations dont il a besoin fournir des recommandations et des alertes sont générées en fonction de la stratégie de sécurité que vous configurez. Dans la figure ci-dessous, vous pouvez voir que la **collecte de données** a été **activée**.

Une stratégie de sécurité définit le jeu de contrôles qui sont recommandés pour les ressources au sein de l’abonnement spécifié ou d’un groupe de ressources. Avant d’activer la stratégie de sécurité, vous devez avoir activé la collecte des données, le centre de sécurité collecte les données de vos machines virtuelles pour évaluer l’état de leur sécurité, fournir des recommandations de sécurité et vous avertir des menaces. Dans le centre de sécurité, vous définissez des stratégies pour des groupes de ressources en fonction des besoins de sécurité de votre société et le type des applications ou des données dans chaque abonnement Azure abonnements. 

![Stratégie de sécurité](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] Pour en savoir plus sur chaque article de [définir des stratégies de sécurité](security-center-policies.md) , **stratégie de prévention** disponible, voir.

## <a name="manage-security-recommendations"></a>Gérer les recommandations de sécurité

Centre de sécurité analyse l’état de la sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il crée des recommandations. Les recommandations vous guident tout au long du processus de configuration des contrôles nécessaires.

Après avoir défini une stratégie de sécurité, le centre de sécurité analyse l’état de la sécurité de vos ressources pour identifier les vulnérabilités potentielles. Les recommandations sont affichées dans un tableau où chaque ligne représente une recommandation particulière. Le tableau ci-dessous fournit quelques exemples de recommandations pour les ordinateurs virtuels d’Azure et que chacun d’eux faire si vous l’appliquez. Lorsque vous sélectionnez une recommandation, vous recevrez les informations qui vous montre comment mettre en oeuvre la recommandation dans le centre de sécurité.

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
| [Évaluation de la vulnérabilité non installée](security-center-vulnerability-assessment-recommendations.md) | Recommande d’installer une solution d’évaluation de vulnérabilité de votre machine virtuelle. |
| [Correction des vulnérabilités](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Vous pouvez ainsi voir les vulnérabilités système et des applications détectées par la solution d’évaluation de vulnérabilité installée sur votre machine virtuelle. |

>[AZURE.NOTE] Pour en savoir plus sur les recommandations, reportez-vous à l’article [Gestion des recommandations de sécurité](security-center-recommendations.md) .

## <a name="monitor-security-health"></a>Surveiller la santé sécurité

Après avoir activé les [stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, le centre de sécurité analyse la sécurité de vos ressources pour identifier les vulnérabilités potentielles.  Vous pouvez afficher l’état de la sécurité de vos ressources, ainsi que les éventuels problèmes relatifs à la **santé des ressources sécurité** lame. Lorsque vous cliquez sur des **machines virtuelles** dans la mosaïque de santé **sécurité des ressources** , la lame de **machines virtuelles** s’ouvre avec des recommandations pour vos ordinateurs virtuels. 

![Fonctionnement de la sécurité](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Gérer et répondre aux alertes de sécurité

Centre de sécurité automatiquement recueille, analyse et intègre les données de journal à partir de vos ressources d’Azure, le réseau et les solutions partenaire connecté (comme les pare-feu et endpoint protection solutions), afin de détecter les menaces réelles et de réduire les faux positifs. Grâce à un groupement de divers des [fonctionnalités de détection](security-center-detection-capabilities.md), le centre de sécurité est en mesure de générer des alertes de sécurité par ordre de priorité pour vous aider à déterminer l’origine du problème et fournissent des recommandations sur la manière de corriger les éventuelles attaques.

![Alertes de sécurité](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Sélectionnez une alerte de sécurité pour en savoir plus sur les événements qui ont déclenché l’alerte et que, si tout, la procédure à suivre pour convertir une attaque. Alertes de sécurité sont groupés par [type](security-center-alerts-type.md) et par date.


## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
