
<properties
   pageTitle="Conseils Azure | modèles et pratiques | Microsoft Azure"
   description="Meilleures pratiques et conseils pour Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Conseils Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

L’équipe modèles et méthodes de Microsoft fait partie de l’équipe de conseil clientèle Azure. Notre objectif est d’aider les professionnels de l’informatique réussir sur la plate-forme Microsoft Azure, les architectes et les développeurs. Nous élaborons des conseils qui présente les pratiques recommandées pour la création de solutions de cloud sur Azure.

## <a name="checklists"></a>Listes de vérification

Ces listes sont une référence rapide pour la révision des principaux aspects de la disponibilité et l’évolutivité. 

- [Liste de vérification de disponibilité][AvailabilityChecklist] 

    Un résumé des méthodes recommandées pour garantir la disponibilité.

- [Liste de vérification de l’évolutivité][ScalabilityChecklist]

    Un résumé des pratiques recommandées pour la conception et mise en œuvre de services évolutifs et gestion des données de gestion.

## <a name="best-practices-articles"></a>Meilleures pratiques des articles

Ces articles fournissent une présentation détaillée des concepts importants généralement associé au cloud computing. 

- [Conception de l’API][APIDesign] 

    Étude des problèmes de conception à prendre en compte lors de la conception d’un site web API.

- [Implémentation de l’API][APIImplementation] 

    Un ensemble de pratiques recommandées pour l’implémentation et la publication d’un site web API.

- [Conseils de sécurité API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    Étude des problèmes d’authentification et d’autorisation (par exemple, types de jetons, protocoles d’autorisation, les flux d’autorisation et atténuation des menaces).

- [AutoScaling conseils][AutoscalingGuidance] 

    Un résumé des considérations relatives à la mise à l’échelle des solutions sans la nécessité d’une intervention manuelle.

- [Guide des tâches en arrière-plan][BackgroundJobsGuidance] 

    Une description des options disponibles et des pratiques recommandées pour la mise en oeuvre des tâches qui doivent être effectuées en arrière-plan, indépendamment de tout premier plan ou d’opérations interactives.

- [Contenu Guide de livraison réseau (CDN)][CDNGuidance] 

    Conseils généraux et pratique recommandée pour l’utilisation du CDN pour réduire la charge sur vos applications et optimiser la disponibilité et les performances.

- [Guide de mise en cache][CachingGuidance] 

    Un résumé de l’utilisation de la mise en cache pour améliorer les performances et l’évolutivité d’un système.

- [Conseils de partitionnement de données][DataPartitioningGuidance]

    Les stratégies que vous pouvez utiliser pour partitionner les données afin d’améliorer l’évolutivité, réduisent les conflits et optimiser les performances.

- [Guide de surveillance et de diagnostic][MonitoringandDiagnosticsGuidance] 

    Conseils sur l’utilisant de vos utilisateurs sur votre système, du suivi suivi de l’utilisation des ressources et généralement de surveiller la santé et les performances de votre système.

- [Conventions d’affectation de noms recommandées][naming-conventions] 

    Conventions d’appellation recommandées pour les ressources d’Azure.

- [Réessayer conseils généraux][RetryGeneralGuidance] 

    Discussion sur les principaux concepts de la gestion des défaillances temporaires.

- [Réessayer les instructions spécifiques au Service][RetryServiceSpecificGuidance]

    Un résumé des fonctionnalités de nouvelle tentative pour la plupart des services Azure, y compris des informations pour vous aider à utiliser, adapter ou étendre le mécanisme de reprise de ce service.

## <a name="scenario-guides"></a>Guides du scénario

- [Elasticsearch en cours d’exécution sur Azure][elasticsearch] 
    
    Elasticsearch est un moteur de recherche d’open source hautement évolutive et une base de données. Il est adapté à des situations qui nécessitent une analyse rapide et la détection des informations contenues dans les groupes de données volumineux. Ce guide examine certains aspects essentiels à prendre en compte lors de la création d’un cluster de Elasticsearch.

- [Gestion des identités pour applications mutualisées][identity-multitenant] 
    
    L’architecture multilocataire est une architecture où différents utilisateurs partagent une application mais sont isolés les uns des autres. Ce guide vous explique comment gérer les identités utilisateur dans une application partagée, à l’aide [d’Azure Active Directory] [ AzureAD] pour gérer l’authentification et connexion.
    
- [Développement de solutions de données volumineuses](https://msdn.microsoft.com/library/dn749874.aspx)

    Ce guide explore l’utilisation de HDInsight pour les scénarios tels que l’exploration itérative, comme un entrepôt de données, pour les processus ETL et l’intégration dans des systèmes décisionnels BI existants. Il inclut également des conseils sur la compréhension des concepts de données volumineuses, de planification et de conception de solutions de données volumineuses et de mise en œuvre de ces solutions.
    
## <a name="patterns"></a>Modèles

- [Nuage de motifs de conception : Instructions architecturales des Applications en nuage](https://msdn.microsoft.com/library/dn568099.aspx)

    Les modèles de conception de nuage est une bibliothèque de modèles de conception et les rubriques connexes. Il énonce le bénéfice de l’application de modèles en montrant comment chaque élément peut contenir dans les architectures d’application cloud.
    
- [Optimisation des performances des Applications en nuage](https://github.com/mspnp/performance-optimization)

    Ce guide est une exploration d’anti-modèles courants qui empêchent les applications de mise à l’échelle sous charge. Il comprend des exemples illustrant les huit anti-modèles et d' [amorce d’analyse de performances](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) et d’un guide pour [évaluer les performances par rapport aux critères d’évaluation clés](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Architectures de référence

Nos architectures de référence sont organisés par scénario.
Chaque architecture individuel propose des pratiques recommandées et des mesures normatives et un composant exécutable qui englobe les recommandations.

La bibliothèque actuelle des architectures de référence est disponible à l’adresse [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Conseils de résilience

Ces rubriques décrivent comment concevoir des applications qui sont résilientes à la défaillance dans un environnement de cloud distribués.   

- [Vue d’ensemble de la résilience][ResiliencyOvervew]

     Comment créer des applications sur la plateforme Azure peuvent récupérer de défaillances et continuera à fonctionner. Décrit une approche structurée pour obtenir la résilience, de la conception à l’implémentation, le déploiement et les opérations.

- [Liste de vérification de résilience][resiliency-checklist]

    Une liste des recommandations qui vous aideront à planifier pour une variété de modes de défaillance qui peut se produire.

- [Analyse en mode d’échec][resiliency-fma] 

    Analyse du mode de défaillance (FMA) est un processus pour la création de résilience dans un système, en identifiant les points de défaillance possibles. Comme point de départ pour votre processus FMA, cet article contient un catalogue des modes de défaillance potentiels et leurs mesures d’atténuation. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

