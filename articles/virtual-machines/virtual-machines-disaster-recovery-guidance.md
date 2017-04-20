<properties
    pageTitle="Que faire dans le cas où une interruption de service Azure a une incidence sur les machines virtuelles Azure | Microsoft Azure"
    description="Comment procéder dans le cas où une interruption de service Azure a une incidence sur les machines virtuelles Azure."
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>Que faire dans le cas où une interruption de service Azure a une incidence sur les machines virtuelles Azure

Chez Microsoft, nous travaillons dur afin de vous assurer que nos services sont toujours disponibles lorsque vous en avez besoin. Forces de notre volonté parfois avoir un impact sur nous de façons qui entraînent des interruptions de service non programmées.

Microsoft fournit un contrat de niveau de Service (SLA) pour ses services comme un engagement de disponibilité et de connectivité. Le contrat SLA pour les différents services Azure, consultez [Les contrats de niveau de Service Azure](https://azure.microsoft.com/support/legal/sla/).

Azure a déjà de nombreuses fonctionnalités de la plate-forme intégrée qui prennent en charge des applications hautement disponibles. Pour plus d’informations sur ces services, lisez la [reprise après sinistre et une haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Cet article décrit un scénario de récupération après sinistre true, lorsqu’une région entière rencontre une panne en raison d’une catastrophe naturelle majeure ou d’interruption de service étendu. Il s’agit de rares occurrences, mais vous devez préparer la possibilité qu’il y a une panne d’une région entière. Si une région entière est confronté à une interruption de service, les copies redondantes localement de vos données est temporairement indisponibles. Si vous avez activé la réplication géographique, trois copies supplémentaires de votre stockage Azure BLOB et les tables sont stockées dans une région différente. Dans le cas d’une panne régionale complète ou un sinistre dans lequel la région principale n’est pas récupérable, Azure remappe toutes les entrées DNS pour la zone géo-répliquées.

>[AZURE.NOTE]Sachez que vous n’avez pas de n’importe quel contrôle sur ce processus, et il se produit uniquement pour les interruptions de service à l’échelle de la région. Pour cette raison, vous devez également compter sur les autres stratégies de sauvegarde spécifiques à l’application d’atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section sur les [stratégies de données pour la reprise après sinistre](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

Pour vous aider à gérer ces rares occurrences, nous fournir les informations suivantes pour les ordinateurs virtuels Azure dans le cas d’une interruption de service de l’ensemble de la région où est déployée votre application Azure VM.

##<a name="option-1-wait-for-recovery"></a>Option 1 : Attente de récupération
Dans ce cas, aucune action de votre part n’est requise. Savoir que nous travaillons assidûment pour restaurer la disponibilité du service. Vous pouvez voir l’état du service actuel sur notre [Tableau de bord de santé Azure Service](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Il s’agit de la meilleure option si vous n’avez pas défini de récupération de Site Azure, sauvegarde des machines virtuelles, stockage redondant geo d’accès en lecture ou un stockage redondant geo avant l’interruption. Si vous avez paramétré stockage redondant geo ou stockage geo redondant d’accès en lecture pour le compte de stockage où se trouvent vos disques durs virtuels de l’ordinateur virtuel (VHD), vous pouvez rechercher pour restaurer l’image disque dur virtuel de base et essayez de mettre en service une nouvelle machine virtuelle à partir de celui-ci. Car il n’existe aucune garantie de synchronisation des données, sauf si la sauvegarde d’Azure VM ou de récupération de Site Azure sont utilisés, il ne s’agit pas d’une option. Par conséquent, cette option n’est pas garantie pour fonctionner.

Pour les clients qui souhaitent un accès immédiat à des machines virtuelles, les deux options suivantes sont disponibles.  

>[AZURE.NOTE]N’oubliez pas que les deux options suivantes ont la possibilité d’une perte de données.     

##<a name="option-2-restore-a-vm-from-a-backup"></a>Option 2 : Restauration d’un ordinateur virtuel à partir d’une sauvegarde
Pour les clients qui ont configuré une sauvegarde de l’ordinateur virtuel, vous pouvez restaurer l’ordinateur virtuel à partir de son point de sauvegarde et de restauration.

Pour restaurer une machine virtuelle à partir de la sauvegarde d’Azure, consultez [restauration d’ordinateurs virtuels dans Azure](../backup/backup-azure-restore-vms.md).

Pour vous aider à planifier votre infrastructure de sauvegarde des machines virtuelles Azure, consultez le [Plan de votre infrastructure de sauvegarde de machine virtuelle dans Azure](../backup/backup-azure-vms-introduction.md).

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Option 3 : Initier un basculement sur incident à l’aide de récupération de Site Azure
Si vous avez configuré la récupération de Site Azure pour travailler avec vos ordinateurs virtuels de Azure concernés, vous pouvez restaurer vos ordinateurs virtuels à partir de leurs duplications. Ces duplications peuvent résider sur Azure ou dans les locaux. Dans ce cas, vous pouvez créer une nouvelle machine virtuelle à partir de son réplica existant. Pour restaurer vos ordinateurs virtuels à partir d’un réplica de récupération de Site d’Azure, consultez [machines virtuelles de migrer le IaaS Azure entre régions Azure avec récupération de Site Azure](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Bien que le système d’exploitation de Azure VM et les disques de données seront répliqués sur un disque dur virtuel secondaire, si elles sont dans un stockage redondant géographique ou un compte de stockage redondant geo d’accès en lecture, chaque VHD est répliqué séparément. Ce niveau de réplication ne garantit pas la cohérence entre les VHD répliquées. Si votre application ou les bases de données qui utilisent ces disques de données ont des dépendances sur l’autre, elle que tous les disques durs virtuels avec sont répliquées comme un instantané n’est pas garanti. Il n’est également pas garanti qu’un instantané cohérent d’application pour démarrer l’ordinateur virtuel entraîne la duplication de disque dur virtuel de stockage redondant geo ou stockage redondant geo d’accès en lecture.

##<a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la mise en œuvre d’une reprise après sinistre et une stratégie de disponibilité élevée, voir [récupération d’urgence et de haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Pour développer une compréhension technique détaillée des fonctionnalités d’une plate-forme nuage, consultez [les conseils techniques résilience Azure](../resiliency/resiliency-technical-guidance.md).

Pour savoir comment sauvegarder des ordinateurs virtuels, reportez-vous à la section [sauvegarder des ordinateurs virtuels Azure](../backup/backup-azure-vms.md).

Apprenez à utiliser la récupération de Site Azure d’orchestrer et d’automatiser la protection de vos machines Windows et Linux physiques (et virtuels) qui s’exécutent sur VMWare et des ordinateurs virtuels Hyper-V, consultez [Récupération de Site Azure](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Si les instructions ne sont ne pas clairs, ou si vous souhaitez que Microsoft pour effectuer les opérations de votre part, contactez le [Service clientèle](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
