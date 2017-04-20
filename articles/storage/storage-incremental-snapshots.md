<properties
    pageTitle="Utiliser des instantanés incrémentiels pour la sauvegarde et la restauration des machines virtuelles Azure | Microsoft Azure"
    description="Créer une solution personnalisée pour la sauvegarde et la récupération de vos disques d’Azure VM à l’aide d’instantanés incrémentiels."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>Sauvegarder disques Azure VM avec instantanés incrémentiels

## <a name="overview"></a>Vue d’ensemble

Le stockage Azure fournit la possibilité de prendre des instantanés de BLOB. Instantanés de capturent l’état de blob à ce point dans le temps. Dans cet article, nous avons décrit un scénario de comment vous pouvez gérer des sauvegardes de disques de machines virtuelles à l’aide de captures instantanées. Vous pouvez utiliser cette méthode lorsque vous choisissez de ne pas utiliser le Service de récupération et de sauvegarde Azure et pour créer une stratégie de sauvegarde personnalisée pour vos disques de l’ordinateur virtuel.

Les disques des machines virtuelles Azure sont stockées sous forme de BLOB de page dans le stockage Azure. Puisque nous parlons de stratégie de sauvegarde pour les disques de l’ordinateur virtuel dans cet article, nous doit se référer à des captures instantanées dans le contexte des objets BLOB de page. Pour en savoir plus sur les instantanés, reportez-vous à [Création d’un instantané d’un objet Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## <a name="what-is-a-snapshot"></a>Ce qu’est une capture instantanée ?

Un instantané de l’objet blob est une version en lecture seule d’un objet blob qui est capturée à un point dans le temps. Une fois un instantané a été créé, il peut être lue, copié, ou supprimé, mais pas modifié. Les snapshots permettent de sauvegarder un blob tel qu’il apparaît à un moment donné. Jusqu'à reste version 2015-04-05, vous avez la possibilité de copier des snapshots complets. Avec le reste de la version 2015-08-07 et versions ultérieures, vous pouvez également copier des instantanés incrémentiels.

## <a name="full-snapshot-copy"></a>Copie de la capture instantanée complète

Les snapshots peuvent être copiées vers un autre compte de stockage sous la forme d’un objet blob à conserver des sauvegardes de l’objet blob de base. Vous pouvez également copier une capture instantanée sur sa base blob, qui est similaire à la restauration de l’objet blob à une version antérieure. Lorsqu’un instantané est copié à partir d’un compte de stockage à un autre, il occupe le même espace que le blob de page de base. Par conséquent, les instantanés toute copies d’un compte de stockage à un autre seront lents et consomment également beaucoup d’espace dans le compte de stockage cible.

>[AZURE.NOTE] Si vous copiez le blob de base vers une autre destination, les captures instantanées de l’objet blob ne sont pas copiées en même temps. De même, si vous remplacez un blob de base avec une copie, clichés associés à l’objet blob de base ne sont pas affectés et restent intactes, sous nom de la base de blob.

### <a name="back-up-disks-using-snapshots"></a>Sauvegarder des disques à l’aide de captures instantanées

Sous la forme d’une stratégie de sauvegarde de vos disques de l’ordinateur virtuel, vous pouvez prendre des instantanés périodiques du blob de disque ou de la page et les copier vers un autre compte de stockage à l’aide d’outils tels que l’opération de [Copie Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou [AzCopy](storage-use-azcopy.md). Vous pouvez copier un instantané à un blob de page de destination avec un nom différent. Le blob de page de destination qui en résulte est un blob page accessible en écriture et pas d’un instantané. Plus loin dans cet article, nous décrirons les étapes pour effectuer des sauvegardes de disques de machines virtuelles à l’aide de captures instantanées.

### <a name="restore-disks-using-snapshots"></a>Restaurer des disques à l’aide de captures instantanées

Lorsqu’il est temps de restauration de votre disque à une version précédente de stable capturée dans un des snapshots de sauvegarde, vous pouvez copier une capture instantanée via le blob de page de base. Une fois la capture instantanée est promue à la page de base de tache, le reste de la capture instantanée, mais sa source est remplacée par une copie qui peut être à la fois lire et écrire. Plus loin dans cet article, nous décrirons les étapes pour restaurer une version précédente de votre disque à partir de sa capture instantanée.

### <a name="implementing-full-snapshot-copy"></a>Mise en œuvre de copie instantanée complète

Vous pouvez implémenter une copie complète de la manière suivante,

-   Tout d’abord, prendre un instantané de la base blob à l’aide de l’opération de [Capture instantanée Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) .
-   Ensuite, copiez la capture instantanée à un compte de stockage cible à l’aide du [Blob de la copie](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Répétez cette procédure pour mettre à jour les copies de sauvegarde de votre base blob.

## <a name="incremental-snapshot-copy"></a>Copie instantanée incrémentielle

La nouvelle fonctionnalité de [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API offre un bien meilleur moyen de sauvegarder les snapshots de vos objets BLOB de page ou de disques. L’API retourne la liste des modifications entre le blob de base et les instantanés. Cela réduit la quantité d’espace de stockage utilisé sur le compte de sauvegarde. L’API prend en charge les objets BLOB de page sur le stockage de la prime ainsi que le stockage Standard. À l’aide de cette API, vous pouvez à présent générer des solutions de sauvegarde plus rapides et efficaces pour les machines virtuelles d’Azure. Ce sera disponible avec la version reste 08-07-2015 et ultérieures.

Copie incrémentielle vous permet de copier à partir d’un compte de stockage à un autre, la différence entre,

-   Blob de base et de son ou de capture instantanée
-   Les deux instantanés du blob de base

Fournie que les conditions suivantes sont remplies,

- Le blob a été créé sur le 1-Jan-2016 ou version ultérieure.
- L’objet blob n’a pas été remplacé avec [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) ou [Blob de copie](https://msdn.microsoft.com/library/azure/dd894037.aspx) entre deux instantanés.


**Remarque**: cette fonctionnalité est disponible de prime et de Blobs de Page Standard Azure.

Lorsque vous avez une stratégie de sauvegarde personnalisée qui utilise des captures instantanées, copie les captures instantanées d’un compte de stockage à un autre peut être très lente et consomme beaucoup d’espace de stockage. Au lieu de copier la capture instantanée à un compte de stockage de sauvegarde, vous pouvez écrire la différence entre deux instantanés consécutifs à un blob de la page de sauvegarde. De cette façon, le temps à copier et l’espace pour stocker des sauvegardes est sensiblement réduit.

### <a name="implementing-incremental-snapshot-copy"></a>Mise en œuvre de copie instantanée incrémentielle

Vous pouvez implémenter la copie instantanée incrémentielle de la manière suivante,

-   Prendre un instantané de la base blob à l’aide du [Blob de la capture instantanée](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copier la capture instantanée pour le compte de stockage de sauvegarde cible à l’aide du [Blob de la copie](https://msdn.microsoft.com/library/azure/dd894037.aspx). Ce sera le blob de la page de sauvegarde. Prendre un instantané de ce blob page sauvegarde et stockez dans compte de sauvegarde.
-   Prenez un nouvel instantané de la base blob à l’aide du Blob de la capture instantanée.
-   Obtenir la différence entre la première et la deuxième instantanés de base blob à l’aide de [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Permet de spécifier la valeur DateTime de la capture instantanée que vous souhaitez obtenir la différence avec le nouveau paramètre **prevsnapshot** . Lorsque ce paramètre est présent, la réponse reste inclut uniquement les pages qui ont été modifiées entre l’instantané de la cible et l’instantané précédent, y compris les pages claires.
-   Utilisez [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) pour appliquer ces modifications à l’objet blob de page sauvegarde.
-   Enfin, prendre un instantané de l’objet blob de page sauvegarde et stockez-le dans le compte de stockage de sauvegarde.

Dans la section suivante, nous décrirons en détail comment vous pouvez gérer des sauvegardes de disques à l’aide de la copie incrémentielle

## <a name="scenario"></a>Scénario

Dans cette section, nous décrirons un scénario qui implique une stratégie de sauvegarde personnalisée pour des disques des machines virtuelles à l’aide de captures instantanées.

Envisagez une série DS Azure VM avec un disque de P30 de stockage premium connecté. Le disque P30 appelé *mypremiumdisk* est stocké dans un compte de stockage premium appelé *mypremiumaccount*. Un compte de stockage standard appelé *mybackupstdaccount* sera utilisé pour le stockage de la sauvegarde de *mypremiumdisk*. Nous souhaitons conserver une capture instantanée de *mypremiumdisk* toutes les 12 heures.

Pour en savoir plus sur la création de disques et compte de stockage, reportez-vous à [comptes de stockage sur Azure](storage-create-storage-account.md).

Pour en savoir plus sur la sauvegarde des machines virtuelles d’Azure, reportez-vous à [planification Azure VM des sauvegardes](../backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Étapes à suivre pour mettre à jour les sauvegardes d’un disque à l’aide d’instantanés incrémentiels

La procédure décrite ci-dessous vous prendre des instantanés de *mypremiumdisk* et conserver les sauvegardes dans *mybackupstdaccount*. La sauvegarde est un blob de page standard appelé *mybackupstdpageblob*. Le blob de la page de sauvegarde reflète toujours le même état que la dernière capture instantanée de *mypremiumdisk*.

1.  Commencez par créer le blob de la page de sauvegarde de votre disque de stockage de prime. Pour ce faire, prendre un instantané de *mypremiumdisk* appelé *mypremiumdisk_ss1*.
2.  Copiez cet instantané de mybackupstdaccount sous la forme d’un blob de page appelé *mybackupstdpageblob*.
3.  Prendre un instantané de *mybackupstdpageblob* appelé *mybackupstdpageblob_ss1*, à l’aide de la [Capture instantanée Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) et le stocker dans *mybackupstdaccount*.
4.  Au cours de la fenêtre de sauvegarde, créer un autre snapshot de *mypremiumdisk*, par exemple *mypremiumdisk_ss2*et stocker dans *mypremiumaccount*.
5.  Obtenir les modifications incrémentielles entre deux instantanés, *mypremiumdisk_ss2* et *mypremiumdisk_ss1*, à l’aide de [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) sur *mypremiumdisk_ss2* avec le paramètre **prevsnapshot** l’estampille de *mypremiumdisk_ss1*. Écrire les modifications incrémentielles dans le blob de page sauvegarde *mybackupstdpageblob* dans *mybackupstdaccount*. S’il existe des plages supprimés dans les modifications incrémentielles, elles doivent être effacées à partir du blob de la page de sauvegarde. Utilisez [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) pour écrire les modifications incrémentielles dans le blob de la page de sauvegarde.
6.  Prendre un instantané de la page de sauvegarde blob *mybackupstdpageblob*, appelée *mybackupstdpageblob_ss2*. Supprimer l' instantané précédent *mypremiumdisk_ss1* de compte de stockage premium.
7.  Répétez les étapes 4 à 6 chaque fenêtre de sauvegarde. De cette façon, vous pouvez gérer des sauvegardes de *mypremiumdisk* dans un compte de stockage standard.

![Sauvegarde de disque à l’aide d’instantanés incrémentiels](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Procédure de restauration d’un disque à partir de snapshots

La procédure décrite ci-dessous permet de restaurer disque premium, *mypremiumdisk* vers un instantané antérieur à partir du compte de stockage de sauvegarde *mybackupstdaccount*.

1.  Identifier le point dans le temps que vous souhaitez restaurer le disque premium. Supposons que c’est instantané *mybackupstdpageblob_ss2*, qui est stocké dans le compte de stockage de sauvegarde *mybackupstdaccount*.
2.  Dans mybackupstdaccount, promouvoir l' instantané *mybackupstdpageblob_ss2* comme le blob de nouvelle page de base de sauvegarde *mybackupstdpageblobrestored*.
3.  Prendre un instantané de ce blob de la page de sauvegarde restauré, appelé *mybackupstdpageblobrestored_ss1*.
4.  Copier le blob de page restaurée *mybackupstdpageblobrestored* à partir de *mybackupstdaccount* à *mypremiumaccount* , comme le disque de nouveau premium *mypremiumdiskrestored*.
5.  Prendre un instantané de *mypremiumdiskrestored*, appelée *mypremiumdiskrestored_ss1* pour les futures sauvegardes incrémentielles.
6.  Point DS series VM à la restauration de disque *mypremiumdiskrestored* et détacher l' ancien *mypremiumdisk* à partir de la machine virtuelle.
7.  Commencer le processus de sauvegarde décrit dans la section précédente pour le disque de restauration *mypremiumdiskrestored*, à l’aide de la *mybackupstdpageblobrestored* comme le blob de la page de sauvegarde.

![Restaurer le disque à partir de snapshots](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la création d’instantanés d’un objet blob et de planification de votre infrastructure de sauvegarde de machine virtuelle à l’aide des liens ci-dessous.

- [Création d’un instantané d’un objet Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planifier votre Infrastructure de sauvegarde de machine virtuelle](../backup/backup-azure-vms-introduction.md)
