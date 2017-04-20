<properties
    pageTitle="Que faire en cas d’interruption du stockage Azure | Microsoft Azure"
    description="Que faire en cas d’interruption du stockage Azure"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Que faire en cas d’interruption du stockage Azure

Chez Microsoft, nous nous efforçons de vous assurer que nos services sont toujours disponibles. Parfois, au-delà de notre impact contrôle nous oblige de façons qui entraînent des interruptions de service non programmées dans une ou plusieurs régions. Pour vous aider à gérer ces rares occurrences, nous fournissons les recommandations de haut niveau suivante pour les services de stockage Azure.

## <a name="how-to-prepare"></a>Comment faire pour préparer 

Il est essentiel pour tous les clients à préparer leur propre plan de récupération d’urgence. L’effort pour récupérer d’une panne de stockage généralement implique à la fois au personnel des opérations et des procédures automatisées pour réactiver vos applications en bon état de fonctionnement. Reportez-vous à la documentation Azure ci-dessous pour créer votre propre plan de récupération d’urgence :

-   [Reprise après sinistre et une haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Guide technique de résilience Azure](../resiliency/resiliency-technical-guidance.md)

-   [Service de récupération de Site Azure](https://azure.microsoft.com/services/site-recovery/)

-   [Réplication du stockage Azure](storage-redundancy.md)

-   [Service de sauvegarde Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Comment faire pour détecter 

Il est recommandé pour déterminer l’état du service Azure pour vous abonner à l' [État du tableau de bord Service Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Que faire en cas de panne de stockage

Si un ou plusieurs services de stockage sont temporairement indisponibles à une ou plusieurs régions, il existe deux options à prendre en compte. Si vous souhaitez un accès immédiat à vos données, envisagez l’Option 2.

### <a name="option-1-wait-for-recovery"></a>Option 1 : Attente de récupération

Dans ce cas, aucune action de votre part n’est requise. Nous travaillons assidûment pour restaurer la disponibilité des services Azure. Vous pouvez surveiller l’état du service sur le [Tableau de bord de santé Azure Service](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Option 2 : Copier des données depuis le secondaire

Si vous avez choisi de [stockage geo redondant d’accès en lecture (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recommandé) pour les comptes de votre stockage, avoir accès en lecture à vos données à partir de la zone secondaire. Vous pouvez utiliser des outils tels que [ [AzCopy](storage-use-azcopy.md), Azure PowerShell](storage-powershell-guide-full.md)et la [bibliothèque de déplacement de données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) pour copier des données à partir de la zone secondaire dans un autre compte de stockage dans une zone d’unimpacted, puis pointez vos applications à ce compte de stockage à la fois pour lire et écrire la disponibilité.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Ce qui se passe si un basculement de stockage se produit

Si vous avez choisi le [stockage redondant Geo (GRS)](storage-redundancy.md#geo-redundant-storage) ou [stockage geo redondant d’accès en lecture (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recommandé), le stockage Azure conservera vos données durables dans deux régions (principal et secondaire). Dans les deux régions, le stockage Azure conserve en permanence plusieurs réplicas de vos données.

Lorsqu’un sinistre régional affecte votre région principale, nous essaiera d’abord de restaurer le service dans cette région. Varie en fonction de la nature de la catastrophe et ses impacts, dans quelques rares occasions que nous ne soyons pas en mesure de restaurer la région principale. À ce stade, nous exécuterons un géo-basculement. La réplication de données entre-région est un processus asynchrone qui peut impliquer un certain délai, il est donc possible que les modifications qui n’ont pas encore été répliquées sur la zone secondaire peuvent être perdues. Vous pouvez interroger la [« dernière heure de synchronisation » de votre compte de stockage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) pour obtenir des détails sur l’état de réplication.

Quelques points concernant l’expérience géo-basculement de stockage :

-   Geo-basculement de stockage sera uniquement déclenchée par l’équipe de stockage Azure – aucun client action n’est requise.

-   Vos points de terminaison de service stockage existants pour les objets BLOB, les tables, les files d’attente et les fichiers resteront identiques après le basculement ; l’entrée DNS sera doivent être mis à jour pour passer de la région principale de la zone secondaire.

-   Avant et pendant le basculement de géo, vous n’aurez accès en écriture à votre compte de stockage en raison de l’impact de l’incident, mais vous pouvez toujours lire à partir de l’image secondaire si votre compte de stockage a été configuré comme RA-GRS.

-   Lorsque le basculement géographique a été effectuée et les modifications DNS propagées, votre lecture et un accès en écriture à votre compte de stockage va être repris. Vous pouvez interroger la [« dernière Geo basculement temps » de votre compte de stockage](https://msdn.microsoft.com/library/azure/ee460802.aspx) pour obtenir plus de détails.

-   Après le basculement, votre compte de stockage entièrement fonctionnera, mais dans un état « dégradé », tel qu’il est hébergé en réalité dans une région autonome avec aucune possible géo-réplication. Pour atténuer ce risque, nous restaure la zone principale d’origine et puis effectuez un géo-retour arrière pour restaurer l’état d’origine. Si la zone principale d’origine est irrécupérable, nous alloue une autre région secondaire.
Pour plus d’informations sur l’infrastructure de réplication géographique de stockage Azure, reportez-vous à l’article sur le blog de l’équipe de stockage sur [les Options de redondance et de RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

##<a name="best-practices-for-protecting-your-data"></a>Méthodes conseillées pour la protection de vos données

Il existe certaines approches recommandées pour sauvegarder les données stockées sur une base régulière.

-   Les disques VM – permet de sauvegarder les disques de la machine virtuelle utilisées par vos machines virtuelles Azure le [service de sauvegarde d’Azure](https://azure.microsoft.com/services/backup/) .

-   BLOB de bloc – créer une [capture instantanée](https://msdn.microsoft.com/library/azure/hh488361.aspx) de chaque objet blob de bloc, ou copier les objets BLOB pour le stockage d’un autre compte dans une autre région à l’aide de la [bibliothèque de déplacement de données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/), [Azure PowerShell](storage-powershell-guide-full.md)ou [AzCopy](storage-use-azcopy.md).

-   Tables – permet d’exporter les données de la table vers un autre compte de stockage dans une autre région [AzCopy](storage-use-azcopy.md) .

-   Fichiers – utilisez [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) pour copier vos fichiers vers un autre compte de stockage dans une autre région.
