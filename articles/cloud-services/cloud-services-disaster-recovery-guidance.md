<properties
    pageTitle="Que faire en cas d’un Azure service interruption ayant une incidence sur les Services en nuage Azure | Microsoft Azure"
    description="Apprenez quoi faire en cas d’une interruption de service Azure qui a une incidence sur les Services en nuage Azure."
    services="cloud-services"
    documentationCenter=""
    authors="kmouss"
    manager="drewm"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="cloud-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Que faire en cas d’un Azure service interruption ayant une incidence sur les Services en nuage Azure

Chez Microsoft, nous travaillons dur afin de vous assurer que nos services sont toujours disponibles lorsque vous en avez besoin. Forces de notre volonté parfois avoir un impact sur nous de façons qui entraînent des interruptions de service non programmées.

Microsoft fournit un contrat de niveau de Service (SLA) pour ses services comme un engagement de disponibilité et de connectivité. Le contrat SLA pour les différents services Azure, consultez [Les contrats de niveau de Service Azure](https://azure.microsoft.com/support/legal/sla/).

Azure a déjà de nombreuses fonctionnalités de la plate-forme intégrée qui prennent en charge des applications hautement disponibles. Pour plus d’informations sur ces services, lisez la [reprise après sinistre et une haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Cet article décrit un scénario de récupération après sinistre true, lorsqu’une région entière rencontre une panne en raison d’une catastrophe naturelle majeure ou d’interruption de service étendu. Il s’agit de rares occurrences, mais vous devez préparer la possibilité qu’il y a une panne d’une région entière. Si une région entière est confronté à une interruption de service, les copies redondantes localement de vos données est temporairement indisponibles. Si vous avez activé la réplication géographique, trois copies supplémentaires de votre stockage Azure BLOB et les tables sont stockées dans une région différente. Dans le cas d’une panne régionale complète ou un sinistre dans lequel la région principale n’est pas récupérable, Azure remappe toutes les entrées DNS pour la zone géo-répliquées.

>[AZURE.NOTE]Sachez que vous n’avez pas de n’importe quel contrôle sur ce processus, et il se produit uniquement pour les interruptions de service du centre de données à l’échelle. Pour cette raison, vous devez également compter sur les autres stratégies de sauvegarde spécifiques à l’application d’atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section sur les [stratégies de données de reprise après sinistre](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Si vous souhaitez être en mesure d’affecter votre propre basculement, vous pouvez souhaiter envisager l’utilisation de l' [accès en lecture de stockage redondant geo (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), ce qui crée une copie en lecture seule de vos données dans une autre région.

Pour vous aider à gérer ces rares occurrences, nous fournissons les conseils suivants pour les machines virtuelles (VM) Azure dans le cas d’une interruption de service de l’ensemble de la région où est déployée votre application Azure VM.

##<a name="option-1-wait-for-recovery"></a>Option 1 : Attente de récupération
Dans ce cas, aucune action de votre part n’est requise. Savoir que les équipes Azure travaillent assidûment pour restaurer la disponibilité du service. Vous pouvez voir l’état du service actuel sur notre [Tableau de bord de santé Azure Service](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Il s’agit de la meilleure option si un client n’a pas configuré de récupération de Site Azure ou a un déploiement secondaire dans une région différente.

Pour les clients qui souhaitent un accès immédiat à leurs services de nuage déployés, les options suivantes sont disponibles.

>[AZURE.NOTE]N’oubliez pas que ces options ont la possibilité d’une perte de données.     

##<a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>Option 2 : Déployez de nouveau la configuration de votre service cloud dans une nouvelle région

Si vous avez votre code d’origine, vous pouvez tout simplement redéployer l’application, configuration associée et les ressources associées à un service en nuage dans une nouvelle région.  

Pour plus d’informations sur la façon de créer et de déployer une application de service de cloud, consultez [comment créer et déployer un service cloud](./cloud-services-how-to-create-deploy-portal.md).

En fonction des sources de données de votre application, vous devrez peut-être vérifier les procédures de récupération pour la source de données d’application.
  * Pour les sources de données de stockage Azure, voir [réplication de stockage Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) pour vérifier les options qui sont disponibles selon le modèle de réplication choisi pour votre application.
  * Pour les sources de la base de données SQL, consultez [Overview : Cloud business continuité d’activité et de la base de données de récupération après sinistre de la base de données SQL](../sql-database/sql-database-business-continuity.md) pour vérifier les options disponibles basés sur le modèle de réplication choisi pour votre application.

##<a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>Option 3 : Utiliser un déploiement de sauvegarde via le Gestionnaire de trafic Azure
Cette option suppose que vous avez déjà conçu votre solution d’application avec un sinistre régional à l’esprit. Vous pouvez utiliser cette option si vous disposez déjà d’un déploiement d’application services nuage secondaire qui s’exécute dans une région différente et connecté via un canal de gestionnaire de trafic. Dans ce cas, vérifiez l’état de santé du déploiement secondaire. S’il est sain, vous pouvez rediriger le trafic vers elle via le Gestionnaire de trafic Azure. Avec cette stratégie, vous pouvez tirer parti de la méthode de routage de trafic et les configurations de commande de basculement dans le Gestionnaire de trafic Azure. Pour plus d’informations, consultez [comment configurer les paramètres du Gestionnaire de trafic](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Équilibrage de Services Cloud Azure entre régions avec Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##<a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise en œuvre d’une reprise après sinistre et une stratégie de disponibilité élevée, voir [récupération d’urgence et de haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Pour développer une compréhension technique détaillée des fonctionnalités d’une plate-forme nuage, consultez [les conseils techniques résilience Azure](../resiliency/resiliency-technical-guidance.md).

Si les instructions ne sont ne pas clairs, ou si vous souhaitez que Microsoft pour effectuer les opérations de votre part, contactez le [Service clientèle](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
