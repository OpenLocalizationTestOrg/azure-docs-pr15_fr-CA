<properties 
   pageTitle="Gérer vos stratégies de sauvegarde StorSimple | Microsoft Azure"
   description="Explique comment vous pouvez utiliser le service Gestionnaire de StorSimple pour créer et gérer des sauvegardes manuelles, plannings et la rétention de sauvegarde."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Le service Gestionnaire de StorSimple permet de gérer les stratégies de sauvegarde (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment utiliser la page de **Stratégies de sauvegarde** du service Gestionnaire de StorSimple pour contrôler le processus de sauvegarde et de conservation des sauvegardes de vos volumes de StorSimple. Il décrit également comment effectuer une sauvegarde manuelle.

Lorsque vous sauvegardez un volume, vous pouvez choisir de créer un instantané local ou un instantané du nuage. Si vous sauvegardez un volume localement épinglé, nous vous recommandons de spécifier un instantané du nuage. En prenant un grand nombre de snapshots locaux d’un volume local épinglé couplé à un ensemble de données qui possède un grand nombre d’évolution du entraîne une situation dans laquelle vous pouvez exécuter rapidement saturé local. Si vous choisissez de prendre des instantanés locaux, nous vous recommandons que vous moins les captures instantanées quotidiennes pour sauvegarder l’état le plus récent, les conserver pour une journée et les supprimez.

Lorsque vous prenez un instantané du nuage d’un volume local épinglé, vous copiez uniquement les données modifiées vers le nuage, où il est dédupliqué et compressé. 

## <a name="the-backup-policies-page"></a>La page de stratégies de sauvegarde

La page de **Stratégies de sauvegarde** vous permet de vous permet de gérer les stratégies de sauvegarde et de planifier des locaux et de snapshots en nuage. (Règles de sauvegarde servent à configurer les plannings de sauvegarde et de conservation d’une collection de volumes). Stratégies de sauvegarde permettent de prendre un instantané de plusieurs volumes simultanément. Cela signifie que les sauvegardes créées par une stratégie de sauvegarde sont des copies cohérentes à l’incident. La page de **Stratégies de sauvegarde** répertorie les stratégies de sauvegarde, leurs types, les volumes associés, le nombre de sauvegardes à conserver et la possibilité d’activer ces stratégies.

La page de **Stratégies de sauvegarde** vous permet également de filtrer les stratégies de sauvegarde existantes par un ou plusieurs des champs suivants :

- **Nom de la stratégie** – le nom associé à la stratégie. Les différents types de stratégies sont les suivantes :

   - Stratégies planifiées, qui sont créés explicitement par l’utilisateur.
   - Stratégies automatiques, qui sont créées lors de la sauvegarde par défaut pour cette option de volume a été activée au moment de la création du volume. Ces stratégies sont nommés _Default *nom_volume*où *nom_volume* correspond au nom du volume StorSimple configuré par l’utilisateur dans le portail classique Azure. Les stratégies automatiques entraînent nuage les captures instantanées quotidiennes commençant à 22:30 heure du périphérique.
   - Stratégies importées, qui ont été créés initialement dans le Gestionnaire de capture instantanée StorSimple. Ils ont une balise décrivant l’hôte de gestionnaire de snapshots StorSimple que les stratégies ont été importées à partir de.

- **Volumes** , les volumes associés à la stratégie. Tous les volumes associés à une stratégie de sauvegarde sont regroupés lorsque les sauvegardes sont créées.

- **Dernière sauvegarde réussie** , la date et l’heure de la dernière sauvegarde réussie qui a été prise avec cette stratégie.

- **Prochaine sauvegarde** – la date et l’heure de la prochaine sauvegarde planifiée qui sera lancée par cette stratégie.

- **Planifications** – le nombre de planifications associées à la stratégie de sauvegarde.

Les opérations fréquemment utilisées que vous pouvez effectuer à partir de cette page sont :

- Ajouter une stratégie de sauvegarde 
- Ajouter ou modifier une planification 
- Supprimer une stratégie de sauvegarde 
- Effectuer une sauvegarde manuelle 
- Créer une stratégie de sauvegarde personnalisée avec plusieurs volumes et les planifications 

## <a name="add-a-backup-policy"></a>Ajouter une stratégie de sauvegarde

Ajouter une stratégie de sauvegarde pour planifier automatiquement vos sauvegardes. Effectuez les opérations suivantes dans le portail Azure classique pour ajouter une stratégie de sauvegarde de votre périphérique StorSimple. Après avoir ajouté la stratégie, vous pouvez définir une planification (voir [Ajouter ou modifier une planification](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Vidéo disponible](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **vidéo disponible**

Pour regarder une vidéo qui montre comment créer une variable locale ou de stratégie de sauvegarde en nuage, cliquez [ici](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Ajouter ou modifier une planification

Vous pouvez ajouter ou modifier une planification qui est associée à une stratégie de sauvegarde existante sur le périphérique StorSimple. Effectuez les opérations suivantes dans le portail Azure classique pour ajouter ou modifier une planification.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Supprimer une stratégie de sauvegarde

Effectuez les opérations suivantes dans le portail Azure classique pour supprimer une stratégie de sauvegarde de votre périphérique StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Effectuer une sauvegarde manuelle

Effectuez les opérations suivantes dans le portail Azure classique pour créer une sauvegarde de (manuelle) à la demande pour un seul volume.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Créer une stratégie de sauvegarde personnalisée avec plusieurs volumes et les planifications

Effectuez les opérations suivantes dans le portail Azure classique pour créer une stratégie de sauvegarde personnalisée qui a plusieurs volumes et les planifications.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
