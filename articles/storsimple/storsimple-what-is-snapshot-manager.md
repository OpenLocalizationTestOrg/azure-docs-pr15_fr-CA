<properties 
   pageTitle="Quel est le Gestionnaire de snapshots StorSimple ? | Microsoft Azure"
   description="Décrit le Gestionnaire de snapshots de StorSimple, son architecture et ses fonctionnalités."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="what-is-storsimple-snapshot-manager"></a>Quel est le Gestionnaire de snapshots StorSimple ?

## <a name="overview"></a>Vue d’ensemble

StorSimple Gestionnaire de snapshots est un composant logiciel enfichable Microsoft Management Console (MMC) qui simplifie la protection des données et la gestion de la sauvegarde dans un environnement Microsoft Azure StorSimple. Avec le Gestionnaire de snapshots de StorSimple, vous pouvez gérer les données de Microsoft Azure StorSimple dans le centre de données et dans le cloud comme une solution de stockage intégrée unique, donc de simplifier les processus de sauvegarde et de réduire les coûts.

Cette vue d’ensemble introduit le Gestionnaire de snapshots StorSimple, décrit ses fonctionnalités et décrit son rôle dans Microsoft Azure StorSimple. 

Pour une vue d’ensemble de l’ensemble du système de Microsoft Azure StorSimple, y compris le périphérique StorSimple, service Gestionnaire de StorSimple, Gestionnaire de snapshots de StorSimple et StorSimple carte pour SharePoint, consultez [série 8000 de StorSimple : une solution de stockage cloud hybride](storsimple-overview.md). 
 
>[AZURE.NOTE] 
>
>- Vous ne pouvez pas utiliser StorSimple Snapshot Manager pour gérer les réseaux virtuels Microsoft Azure StorSimple (également connu sous le nom StorSimple local périphériques virtuels).
>
>- Si vous envisagez d’installer StorSimple mise à jour 2 sur le périphérique StorSimple, veillez à télécharger la dernière version du Gestionnaire de snapshots StorSimple et l’installer **avant d’installer StorSimple mise à jour 2**. La dernière version du Gestionnaire de snapshots StorSimple est à compatibilité descendante et fonctionne avec toutes les versions publiées de Microsoft Azure StorSimple. Si vous utilisez la version précédente du Gestionnaire de snapshots StorSimple, vous devez mettre à jour (vous n’avez pas besoin de désinstaller la version précédente avant d’installer la nouvelle version).

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Architecture et le rôle de gestionnaire de snapshots StorSimple

Gestionnaire de snapshots StorSimple fournit une console de gestion centrale que vous pouvez utiliser pour créer cohérente, des copies de sauvegarde de point-à-temps des locaux et les données de nuage. Par exemple, vous pouvez utiliser la console pour :

- Configurer, sauvegarder et supprimer des volumes.
- Configurer des groupes de volumes afin de garantir que les données sauvegardées est cohérent pour l’application.
- Gérer les stratégies de sauvegarde afin que les données sont sauvegardées selon une planification prédéfinie.
- Créer locaux et cloud des instantanés, qui peuvent être stockées dans le cloud et utilisés pour la reprise après sinistre.

Le Gestionnaire de snapshots StorSimple extrait la liste des applications enregistrées avec le fournisseur VSS sur l’hôte. Ensuite, pour créer des sauvegardes cohérentes avec les applications, il vérifie les volumes utilisés par une application et propose des groupes de volumes à configurer. StorSimple Snapshot Manager utilise ces groupes pour générer des copies de sauvegarde sont cohérentes avec les applications. (La cohérence des applications existe lorsque tous les fichiers concernant et les bases de données sont synchronisées et représentent l’état réel de l’application à un point spécifique dans le temps.) 

Gestionnaire de snapshots StorSimple des sauvegardes prennent la forme de captures instantanées incrémentielles, qui capture uniquement les modifications apportées depuis la dernière sauvegarde. Par conséquent, les sauvegardes nécessitent moins de stockage et peuvent être créées et restaurées rapidement. Gestionnaire de snapshots StorSimple utilise Windows Volume Shadow Copy Service (VSS) pour s’assurer que les snapshots capturer des données cohérentes avec les applications. (Pour plus d’informations, allez à l’intégration avec section de Windows Volume Shadow Copy Service). Avec le Gestionnaire de snapshots de StorSimple, vous pouvez créer des plannings de sauvegarde ou effectuer des sauvegardes immédiates en fonction des besoins. Si vous devez restaurer les données à partir d’une sauvegarde, vous permet de StorSimple Gestionnaire de snapshots vous sélectionnez à partir d’un catalogue local ou des snapshots de nuage. StorSimple Azure restaure uniquement les données nécessaires qu’il est nécessaire, qui empêche les retards dans la disponibilité des données pendant les opérations de restauration.)

![Architecture du Gestionnaire de capture instantanée StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architecture du Gestionnaire de capture instantanée StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Prise en charge de plusieurs types de volume

Vous pouvez utiliser le Gestionnaire de snapshots StorSimple pour configurer et sauvegarder les types de volumes suivants : 

- Les **volumes de base** – un volume de base est une partition unique sur un disque de base. 

- **Les volumes simples** , un volume simple est un volume dynamique qui contient l’espace d’un seul disque dynamique. Un volume simple est constitué d’une seule région d’un disque ou de plusieurs régions liées entre elles sur le même disque. (Vous pouvez créer des volumes simples uniquement sur des disques dynamiques). Les volumes simples ne sont pas tolérants aux pannes.

- **Les volumes dynamiques** – un volume dynamique est un volume créé sur un disque dynamique. Les disques dynamiques utilisent une base de données pour effectuer le suivi des informations sur les volumes qui sont contenues sur les disques dynamiques d’un ordinateur. 

- **Les volumes dynamiques avec la mise en miroir** – la mise en miroir des volumes dynamiques s’appuient sur l’architecture RAID 1. Avec RAID 1, des données identiques sont écrites sur disque de deux ou plusieurs, produisant un jeu en miroir. Une demande de lecture peut ensuite être traitée par n’importe quel disque contenant les données demandées.

- **Les volumes partagés de cluster** – avec des volumes partagés de cluster (CSVs), plusieurs nœuds dans un cluster de basculement peuvent simultanément lire ou écrire sur le même disque. Basculement d’un nœud vers un autre nœud peut se produire rapidement, sans nécessiter une modification de propriété de lecteur ou de montage, démontage et la suppression d’un volume. 

>[AZURE.IMPORTANT] Ne mélangez pas les CSVs et non CSVs dans la même capture instantanée. Mélange de CSVs et non CSVs dans un instantané n’est pas pris en charge. 
 
Gestionnaire de capture instantanée StorSimple vous permet de restaurer les groupes de l’ensemble du volume ou de cloner des volumes et de récupérer des fichiers individuels.

- [Groupes de volumes et des volumes](#volumes-and-volume-groups) 
- [Types de sauvegarde et les stratégies de sauvegarde](#backup-types-and-backup-policies) 

Pour plus d’informations sur les fonctionnalités du Gestionnaire de snapshots de StorSimple et leur utilisation, voir [interface utilisateur du Gestionnaire de capture instantanée StorSimple](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Groupes de volumes et des volumes

Avec le Gestionnaire de capture instantanée StorSimple, créer des volumes, puis les configurer dans les groupes de volumes. 

Gestionnaire de snapshots StorSimple utilise des groupes de volumes pour créer des copies de sauvegarde sont cohérentes avec les applications. Cohérence de l’application existe lorsque tous les fichiers concernant et les bases de données sont synchronisées et représentent l’état réel d’une application à un point spécifique dans le temps. Les groupes de volumes (qui sont également appelés *groupes de cohérence*) constituent la base d’une sauvegarde ou restauration du travail.

Groupes de volumes ne sont pas identiques en tant que conteneurs de volume. Un conteneur de volume contient un ou plusieurs volumes qui partagent un compte de stockage cloud et d’autres attributs, tels que la consommation de la bande passante et de cryptage. Un conteneur de volume unique peut contenir jusqu'à 256 volumes StorSimple provisionnées. Pour plus d’informations sur les conteneurs de volume, accédez à [gérer vos conteneurs de volume](storsimple-manage-volume-containers.md). Groupes de volumes sont des ensembles de volumes que vous configurez pour faciliter les opérations de sauvegarde. Si vous sélectionnez deux volumes appartenant à des conteneurs de volume différent, les placer dans un groupe de volumes unique, et puis créer une stratégie de sauvegarde pour ce groupe de volume, chaque volume est sauvegardé dans le conteneur de volume approprié, en utilisant le compte de stockage approprié.

>[AZURE.NOTE] Tous les volumes dans un groupe de volumes doivent provenir d’un fournisseur de services de cloud unique.

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Intégration avec le Service de cliché instantané de Volume Windows

Gestionnaire de snapshots StorSimple utilise Windows Volume Shadow Copy Service (VSS) pour capturer des données cohérentes avec les applications. VSS facilite la cohérence des applications en communiquant avec des applications prenant en charge VSS pour coordonner la création d’instantanés incrémentiels. VSS permet de s’assurer que les applications sont temporairement inactive ou au repos, lorsque des instantanés. 

L’implémentation de gestionnaire de snapshots StorSimple de VSS fonctionne avec SQL Server et les volumes NTFS génériques. Le processus est le suivant : 

1. Un demandeur, qui est en général une gestion des données et d’une solution de protection (par exemple StorSimple Gestionnaire de snapshots) ou d’une application de sauvegarde, appelle le VSS et lui demande de rassembler des informations à partir du logiciel de writer dans l’application cible.

2. VSS contacte le composant du générateur pour récupérer une description des données. Le writer renvoie la description des données à sauvegarder. 

3. Signale le VSS writer pour préparer l’application de sauvegarde. Le writer prépare les données pour la sauvegarde en effectuant des transactions en cours, mise à jour des journaux de transactions et ainsi de suite et notifie ensuite VSS.

4. VSS indique au writer de temporairement arrêter des banques de données de l’application et vérifiez qu’aucune donnée n’est écrites sur le volume lors de la création du cliché instantané. Cette étape garantit la cohérence des données et pas plus de 60 secondes.

5. VSS indique au fournisseur pour créer le cliché instantané. Gérer les fournisseurs, ce qui peuvent être le logiciel ou matériel basés sur, les volumes qui sont en cours d’exécution et de créer des clichés instantanés à la demande. Le fournisseur crée le cliché et avertit VSS lorsqu’il est terminé.

6. VSS contacte le writer pour avertir l’application que vous pouvez reprendre l’e/s et également de confirmer que les e/s a été interrompu avec succès lors de la création de clichés instantanés. 

7. Si la copie réussit, VSS renvoie l’emplacement de la copie au demandeur. 

8. Si les données ont été écrites pendant le cliché instantané a été créé, puis la sauvegarde seront incohérente. VSS supprime le cliché instantané et le notifie au demandeur. Le demandeur peut répéter le processus de sauvegarde automatiquement ou informer l’administrateur tentative ultérieurement.

Reportez-vous à l’illustration suivante.

![Processus VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processus du Service de cliché instantané de Volume Windows** 

## <a name="backup-types-and-backup-policies"></a>Types de sauvegarde et les stratégies de sauvegarde

Avec le Gestionnaire de snapshots de StorSimple, vous pouvez sauvegarder les données et stocker localement et dans le cloud. Vous pouvez utiliser le Gestionnaire de snapshots de StorSimple pour sauvegarder les données immédiatement, ou vous pouvez utiliser une stratégie de sauvegarde pour créer une planification pour prendre automatiquement des sauvegardes. Stratégies de sauvegarde vous permettent également de spécifier combien de snapshots est conservés. 

### <a name="backup-types"></a>Types de sauvegarde

Vous pouvez utiliser le Gestionnaire de snapshots de StorSimple pour créer les types de sauvegardes suivants :

- **Snapshots locaux** : snapshots locaux sont des copies de données d’un volume qui sont stockées sur le périphérique StorSimple point-à-temps. En général, ce type de sauvegarde peut être créé et restaurer rapidement. Vous pouvez utiliser un instantané local comme vous le feriez avec une copie de sauvegarde locale.

- **Instantanés de cloud** : nuage instantanés sont des copies de point-à-temps des données d’un volume qui sont stockées dans le nuage. Un instantané du nuage est équivalent à une capture instantanée répliquée sur un système de stockage hors site différents. Les instantanés de nuage sont particulièrement utiles dans les scénarios de récupération d’urgence.

### <a name="on-demand-and-scheduled-backups"></a>Sauvegardes planifiées et sur demande.

Avec le Gestionnaire de snapshots de StorSimple, vous pouvez lancer une sauvegarde unique doit être créé immédiatement, ou vous pouvez utiliser une stratégie de sauvegarde pour planifier les opérations de sauvegarde périodiques.

Une stratégie de sauvegarde est un ensemble de règles automatisées que vous pouvez utiliser pour planifier des sauvegardes régulières. Une stratégie de sauvegarde vous permet de définir la fréquence et les paramètres de la prise de clichés d’un groupe de volumes spécifiques. Vous pouvez utiliser des stratégies pour spécifier les dates de début et d’expiration, des heures, des fréquences et des exigences de conservation, des locaux et des snapshots en nuage. Une stratégie est appliquée dès que vous définissez. 

Vous pouvez utiliser le Gestionnaire de snapshots StorSimple pour configurer ou reconfigurer les stratégies de sauvegarde chaque fois que nécessaire. 

Vous configurez les informations suivantes pour chaque stratégie de sauvegarde que vous créez :

- **Nom** : le nom unique de la stratégie de sauvegarde sélectionnée.

- **Type** : le type de stratégie de sauvegarde ; instantané local ou snapshot de nuage.

- **Groupe de volumes** – le groupe de volumes auquel la stratégie de sauvegarde sélectionnée est affectée.

- **Rétention** – le nombre de copies de sauvegarde à conserver. Si vous cochez la case **tous** , toutes les copies de sauvegarde sont conservés jusqu'à ce que le nombre maximal de copies de sauvegarde par volume est atteint, moment auquel la stratégie échoue et génère un message d’erreur. Vous pouvez également spécifier un certain nombre de sauvegardes à conserver (entre 1 et 64).

- **Date** -date de création de la stratégie de sauvegarde.

Pour plus d’informations sur la configuration des stratégies de sauvegarde, accédez à [Gestionnaire de snapshots de StorSimple utilisé pour créer et gérer des stratégies de sauvegarde](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Surveillance de l’opération de sauvegarde et de gestion

Vous pouvez utiliser le Gestionnaire de snapshots StorSimple pour surveiller et gérer les tâches de sauvegarde à venir, planifiées et terminées. En outre, StorSimple Snapshot Manager propose un catalogue des 64 sauvegardes terminées. Vous pouvez utiliser le catalogue pour rechercher et restaurer des fichiers individuels ou volumes. 

Pour plus d’informations sur l’analyse des travaux de sauvegarde, accédez à [Gestionnaire de snapshots utiliser StorSimple pour afficher et gérer les tâches de sauvegarde](storsimple-snapshot-manager-manage-backup-jobs.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur [l’utilisation du Gestionnaire de snapshots StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).

- Télécharger le [Gestionnaire de StorSimple instantané](https://www.microsoft.com/download/details.aspx?id=44220).
