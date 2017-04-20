<properties
   pageTitle="Index des conseils techniques de résilience | Microsoft Azure"
   description="Index des articles techniques sur la compréhension et conception résistante et très performante, applications à tolérance de pannes, ainsi que de planification de continuité d’activité et une reprise après sinistre"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance"></a>Guide technique de résilience Azure

##<a name="introduction"></a>Introduction

Haute disponibilité et les besoins de récupération après sinistre de réunion requiert deux types de connaissances :

- Spécifications techniques détaillées des fonctionnalités d’une plate-forme nuage
- Savoir comment l’architecture correctement un service distribué

Cette série d’articles couvre l’ancienne : les capacités et les limitations de la plateforme Azure en ce qui concerne la résilience (parfois appelée la continuité d’activité). Si vous êtes intéressé par ces derniers, veuillez consulter la série de l’article consacrée à la [reprise après sinistre et une haute disponibilité pour les applications Azure](https://aka.ms/drtechguide). Bien que la touche de cette série d’articles sur les modèles d’architecture et de conception, qui n’est pas l’objectif de la série. Pour des conseils de conception, vous pouvez consulter le contenu de la section [ressources supplémentaires](#additional-resources) .

Les informations sont organisées dans les articles suivants :

- [La récupération des défaillances locales](resiliency-technical-guidance-recovery-local-failures.md).
Matériel physique (par exemple, des disques, des serveurs et des équipements réseau) peut échouer. Les ressources peuvent être épuisées lors de pics de charge. Cet article décrit les fonctionnalités Azure fournit pour garantir une haute disponibilité sous ces conditions.

- [Récupération à partir d’une perturbation Azure à l’échelle de la région](resiliency-technical-guidance-recovery-loss-azure-region.md).
Défaillances répandues sont rares, mais ils sont théoriquement possibles. Régions entières peuvent être isolées en raison de défaillances du réseau, ou ils peuvent être endommagés physiquement, par des catastrophes naturelles. Cet article explique comment utiliser Azure pour créer des applications qui s’étendent sur des zones géographiquement.

- [Restauration sur site vers Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
Le nuage modifie considérablement le coût de la reprise après sinistre, permettant aux organisations de Azure permet d’établir un second site pour la récupération. Vous pouvez faire cela à une fraction du coût de la construction et la maintenance d’un centre de données secondaire. Cet article explique les fonctionnalités Azure fournit pour l’extension d’un centre de données local dans le nuage.

- [Récupération à partir d’une suppression accidentelle ou de corruption des données](resiliency-technical-guidance-recovery-data-corruption.md).
Applications peuvent avoir des bogues que les données endommagées. Opérateurs peuvent supprimer correctement les données importantes. Cet article explique ce que Azure fournit pour la sauvegarde des données et restauration à un point antérieur, il est temps.

##<a name="additional-resources"></a>Ressources supplémentaires

- [Reprise après sinistre et une haute disponibilité des applications basées sur Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md).
Cet article est une présentation détaillée de la disponibilité et la reprise après sinistre. Il couvre le défi de la réplication manuelle de données transactionnelles et de référence. Les sections finales fournissent des résumés des différents types de topologies de récupération après sinistre qui couvrent des régions Azure le plus haut niveau de disponibilité.

- [Liste de vérification de disponibilité](resiliency-high-availability-checklist.md).
Cet article est une liste de fonctionnalités, les services, et les modèles qui peuvent vous aider à augmentent la résilience et la disponibilité de votre application.

- [Aide à la résilience des services Microsoft Azure](resiliency-service-guidance-index.md).
Cet article est un index de services Azure et fournit des liens vers les instructions de récupération d’urgence et de la conception.

- [Vue d’ensemble : Cloud business continuité d’activité et de la base de données de récupération après sinistre de la base de données SQL](../sql-database/sql-database-business-continuity.md).
Cet article fournit des techniques de base de données de SQL Azure pour la disponibilité. Il se centre principalement sur les stratégies de sauvegarde et de restauration. Si vous utilisez la base de données de SQL Azure dans votre service cloud, vous devez examiner ce document et ses ressources connexes.

- [Haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
Cet article décrit les options de disponibilité que vous pouvez Explorer lorsque vous utilisez l’infrastructure en tant que service (IaaS) pour héberger vos services de base de données. Elle traite des groupes de disponibilité AlwaysOn, la mise en miroir de base de données, l’envoi de journaux et de sauvegarde/restauration. Plusieurs didacticiels illustrent l’utilisation de ces techniques.

- [Meilleures pratiques de conception des services à grande échelle sur les Services en nuage Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
Cet article se concentre sur le développement d’architectures de nuage hautement évolutive. La plupart des techniques que vous employez pour améliorer l’évolutivité également améliorent la disponibilité. En outre, si votre application ne peut pas mettre à l’échelle sous l’augmentation de la charge, évolutivité devient un problème de disponibilité.

- [Sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
Cet article fournit des conseils techniques sur la procédure de sauvegarde et de restauration de Microsoft SQL Server en cours d’exécution sur Azure Virtual Machines.

- [Failsafe : conseils pour des architectures de cloud souple](https://channel9.msdn.com/Series/FailSafe).
Cet article fournit des conseils pour la création d’architectures de cloud souple, des conseils pour l’implémentation de ces architectures sur les technologies Microsoft et les recettes de mise en oeuvre de ces architectures pour des scénarios spécifiques.

- [Étude de cas technique : à l’aide des technologies de cloud pour améliorer la récupération d’urgence](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
Cette étude de cas montre comment Microsoft IT Azure pour améliorer la reprise après sinistre.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de conseils techniques pour Azure résilience. Si vous êtes intéressé par des autres articles de cette série, vous pouvez démarrer avec [la récupération des défaillances locales](resiliency-technical-guidance-recovery-local-failures.md).
