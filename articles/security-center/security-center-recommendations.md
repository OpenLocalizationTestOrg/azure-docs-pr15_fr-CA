<properties
   pageTitle="Gestion des recommandations de sécurité dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous guide tout au long de recommandations dans le centre de sécurité Azure comment vous aident à protéger vos ressources d’Azure et rester conforme aux stratégies de sécurité."
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

# <a name="managing-security-recommendations-in-azure-security-center"></a>Gestion des recommandations de sécurité dans le centre de sécurité Azure

Ce document vous guide tout au long de l’utilisation des recommandations dans le centre de sécurité Azure pour vous aider à protéger vos ressources d’Azure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="what-are-security-recommendations"></a>Quelles sont les recommandations de sécurité ?
Centre de sécurité analyse périodiquement l’état de sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il crée des recommandations. Les recommandations vous guident tout au long du processus de configuration des contrôles nécessaires.

## <a name="implementing-security-recommendations"></a>Mise en oeuvre des recommandations de sécurité

### <a name="set-recommendations"></a>Jeu de recommandations

Dans la [définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md), vous apprenez à :

- Configurer des stratégies de sécurité.
- Activer la collecte des données.
- Choisissez les recommandations pour voir dans le cadre de votre stratégie de sécurité.

Stratégie recommandations poste actuel autour des mises à jour du système, les règles de base, les programmes contre les logiciels malveillants, [les groupes de sécurité de réseau](../virtual-network/virtual-networks-nsg.md) sur les sous-réseaux et les interfaces de réseau, l’audit de base de données SQL, chiffrement transparent des données de base de données SQL et pare-feu d’application web.  [Définition des stratégies de sécurité](security-center-policies.md) fournit une description de chaque option de recommandation.

### <a name="monitor-recommendations"></a>Recommandations de moniteur
Après avoir défini une stratégie de sécurité, le centre de sécurité analyse l’état de la sécurité de vos ressources pour identifier les vulnérabilités potentielles. La mosaïque de **recommandations** sur la lame du **Centre de sécurité** vous permet de connaître le nombre total de recommandations formulées par le centre de sécurité.

![Mosaïque de recommandations][1]

Pour afficher les détails de chaque recommandation :

1. Cliquez sur la **Mosaïque de recommandations** sur la lame du **Centre de sécurité** . La lame de **recommandations** s’ouvre.

Les recommandations sont affichées dans un tableau où chaque ligne représente une recommandation particulière. Les colonnes de cette table sont les suivants :

- **DESCRIPTION**: décrit la recommandation et ce qui doit être fait pour corriger le problème.
- **Ressource**: répertorie les ressources auquel s’applique cette recommandation.
- **État**: décrit l’état actuel de la recommandation :
    - **Ouvrir**: la recommandation n’a pas encore été satisfaite.
    - **En cours**: la recommandation est actuellement appliquée aux ressources, et aucune action n’est requise par vous.
    - **Résolu**: la recommandation a déjà été effectuée (dans ce cas, la ligne sera grisée).
- **Gravité**: décrit la gravité de cette recommandation particulière :
    - **Haute**: une vulnérabilité existe avec une ressource explicite (par exemple, une application, un ordinateur virtuel ou un groupe de sécurité de réseau) et requiert de l’attention.
    - **Moyenne**: il existe une vulnérabilité et non critiques ou autres étapes sont nécessaires pour l’éliminer ou pour terminer un processus.
    - **Faible**: il existe une vulnérabilité qui doivent être traités, mais ne nécessite pas une attention immédiate. (Par défaut, les recommandations faibles ne sont pas présentées, mais vous pouvez filtrer sur les recommandations de faibles si vous souhaitez les voir.)

Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations disponibles et que chacun d’eux faire si vous l’appliquez.

> [AZURE.NOTE] Vous souhaitez comprendre [classique et les modèles de déploiement Gestionnaire de ressources](../azure-classic-rm.md) pour les ressources Azure.

|Recommandation|Description|
|-----|-----|
|[Activer la collecte des données pour les abonnements](security-center-enable-data-collection.md)|Vous recommande d’activer la collecte des données de la stratégie de sécurité pour chacun de vos abonnements et toutes les machines virtuelles (VM) dans vos abonnements.|
|[Correction des vulnérabilités du système d’exploitation](security-center-remediate-os-vulnerabilities.md)|Par exemple, recommande que vous alignez vos configurations du système d’exploitation avec les règles de configuration recommandée, ne permettent pas d’enregistrer les mots de passe.|
|[Appliquer les mises à jour du système](security-center-apply-system-updates.md)|Recommande de déployer la sécurité du système manquant et mises à jour critiques aux machines virtuelles.|
|[Redémarrage après les mises à jour du système](security-center-apply-system-updates.md#reboot-after-system-updates)|Vous conseille de redémarrer un ordinateur virtuel pour terminer le processus d’application des mises à jour du système.|
|[Ajouter un pare-feu pour applications web](security-center-add-web-application-firewall.md)|Recommande de déployer un pare-feu pour applications web pour les points de terminaison web (WAF). Vous pouvez protéger plusieurs applications web dans le centre de sécurité en ajoutant ces applications à vos déploiements WAF existants. Les appliances WAF (créés avec le modèle de déploiement du Gestionnaire de ressources) doivent être déployés sur un réseau virtuel distinct. Les appliances WAF (créés avec le modèle de déploiement classique) sont limités à l’utilisation d’un groupe de sécurité du réseau. Cette prise en charge sera étendue à l’avenir à un déploiement personnalisé d’un appareil de WAF (classique). Centre de sécurité vous recommandera que vous mettez en service un WAF pour aider à se défendre contre les attaques qui ciblent vos applications web sur des ordinateurs virtuels et sur application Service environnement (ASE). Pour en savoir plus sur l’équipement de direction auxiliaire, consultez la [Documentation sur le Service environnement App](../app-service/app-service-app-service-environments-readme.md). |
|[Finalisation de la protection d’application](security-center-add-web-application-firewall.md#finalize-application-protection)|Pour terminer la configuration d’un WAF, trafic doit être redirigé vers la solution matérielle-logicielle WAF. Le respect de cette recommandation terminera les modifications de configuration nécessaires.|
|[Ajouter un pare-feu de génération suivant](security-center-add-next-generation-firewall.md)|Recommande que vous ajoutez une prochaine génération de pare-feu Conviction d’un partenaire Microsoft pour augmenter la protection de votre sécurité.|
|[Acheminement du trafic via Conviction uniquement](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recommande de configurer les règles de groupe (NSG) de sécurité de réseau qui force le trafic entrant de votre machine virtuelle par le biais de votre Conviction.|
|[Installer la Protection du point de terminaison](security-center-install-endpoint-protection.md)|Vous recommande de mettre en service les programmes contre les logiciels malveillants pour les ordinateurs virtuels (VM Windows uniquement).|
|[Résoudre les alertes de fonctionnement de la Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recommande que vous résolvez les échecs de protection de point de terminaison.|
|[Activer des groupes de sécurité réseau sur les sous-réseaux ou les machines virtuelles](security-center-enable-network-security-groups.md)|Recommande d’activer NSGs sur les sous-réseaux ou les ordinateurs virtuels.|
|[Restreindre l’accès par le biais de Internet vers le point de terminaison](security-center-restrict-access-through-internet-facing-endpoints.md)|Recommande de configurer les règles de trafic entrant pour NSGs.|
|[Activer le serveur SQL d’audit](security-center-enable-auditing-on-sql-servers.md)|Recommande que vous activez l’audit pour les serveurs SQL d’Azure (service d’Azure SQL uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer l’audit de SQL de la base de données](security-center-enable-auditing-on-sql-databases.md)|Recommande que vous activez l’audit pour les bases de données SQL d’Azure (service d’Azure SQL uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer le chiffrement Transparent des données sur les bases de données SQL](security-center-enable-transparent-data-encryption.md)|Recommande d’activer le cryptage pour les bases de données SQL (service Azure SQL uniquement).|
|[Activer l’Agent de la machine virtuelle](security-center-enable-vm-agent.md)|Vous permet de savoir quels ordinateurs virtuels nécessitent l’Agent de la machine virtuelle. L’Agent de la machine virtuelle doit être installé sur les ordinateurs virtuels pour fournir des correctifs d’analyse, d’analyse de ligne de base et les programmes contre les logiciels malveillants. L’Agent de la machine virtuelle est installé par défaut pour les ordinateurs virtuels qui sont déployés à partir de l’Azure Marketplace. L’article [Agent de la machine virtuelle et Extensions – partie 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’Agent de la machine virtuelle.|
| [Appliquer le cryptage de disque](security-center-apply-disk-encryption.md) |Recommande que vous chiffrez vos disques VM à l’aide du cryptage de disque Azure (Windows et Linux VMs). Le cryptage est recommandé pour les volumes à la fois le système d’exploitation et les données sur votre ordinateur virtuel.|
|[Fournir des détails de contact de sécurité](security-center-provide-security-contact-details.md) | Recommande que vous fournissez une sécurité les informations de contact pour chacun de vos abonnements. Informations de contact soient un numéro de téléphone et adresse e-mail. Les informations serviront à vous contacter si notre équipe de sécurité détecte que vos ressources sont compromis. |
| [Version de mise à jour du système d’exploitation](security-center-update-os-version.md) | Vous recommande de mettre à jour la version du système d’exploitation (OS) pour votre Service Cloud avec la version la plus récente disponible pour votre famille de système d’exploitation.  Pour en savoir plus sur les Services en nuage, consultez la [vue d’ensemble des Services en nuage](../cloud-services/cloud-services-choose-me.md). |
| [Évaluation de la vulnérabilité non installée](security-center-vulnerability-assessment-recommendations.md) | Recommande d’installer une solution d’évaluation de vulnérabilité de votre machine virtuelle. |
| [Correction des vulnérabilités](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Vous pouvez ainsi voir les vulnérabilités système et des applications détectées par la solution d’évaluation de vulnérabilité installée sur votre machine virtuelle. |

Vous pouvez filtrer et rejeter les recommandations.

1. Cliquez sur **le filtre** sur la lame de **recommandations** . La lame de **filtre** s’ouvre et que vous sélectionnez les valeurs de gravité et l’état que vous souhaitez voir.

    ![Recommandations de filtre][2]

2. Si vous déterminez qu’une recommandation ne s’applique pas, vous pouvez faire disparaître la recommandation et filtrer hors de votre vue. Il existe deux façons de faire disparaître une recommandation. La première consiste à cliquer avec le bouton droit de la souris sur un élément, puis sélectionnez **faire disparaître**. L’autre consiste à pointer sur un élément et cliquez sur trois points qui s’affichent à droite, puis sélectionnez **faire disparaître**. Vous pouvez afficher les recommandations ignorées en cliquant sur **filtrer**, puis cliquez sur **Dismissed**.

    ![Faire disparaître de recommandation][3]

### <a name="apply-recommendations"></a>Appliquer les recommandations
Après avoir examiné toutes les recommandations, choisissez celui que vous devez appliquer tout d’abord. Nous vous recommandons d’utiliser l’indice de gravité comme le paramètre principal pour évaluer les recommandations doit être appliqué en premier.

Dans la table des recommandations ci-dessus, sélectionnez une recommandation et le parcourir comme un exemple de la façon d’appliquer la recommandation.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez abordé les recommandations de sécurité dans le centre de sécurité. Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer les stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de contrôle partenaire avec le centre de sécurité Azure](security-center-partner-solutions.md) : Apprenez à surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité d’Azure](http://blogs.msdn.com/b/azuresecurity/) , trouver des billets de blog sur la sécurité Azure et de conformité.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
