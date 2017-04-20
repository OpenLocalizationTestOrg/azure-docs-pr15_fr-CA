<properties 
   pageTitle="Restauration d’un volume de StorSimple à partir de la sauvegarde | Microsoft Azure"
   description="Explique comment utiliser la page de catalogue de sauvegarde du service Gestionnaire de StorSimple pour restaurer un volume de StorSimple à partir d’un jeu de sauvegarde."
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
   ms.date="04/26/2016"
   ms.author="v-sharos" />

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restauration d’un volume de StorSimple à partir d’un jeu de sauvegarde (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Vue d’ensemble

La page **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lors des sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer les sauvegardes, ou utiliser une sauvegarde pour la restauration ou le clonage d’un volume.

 ![Page de catalogue de sauvegarde](./media/storsimple-restore-from-backup-set-u2/restore.png)

Ce didacticiel explique comment utiliser la page de **Catalogue de sauvegarde** pour restaurer votre appareil à partir d’un jeu de sauvegarde.

Vous pouvez restaurer une sauvegarde de nuage ou le volume à partir d’une variable locale. Dans les deux cas, l’opération de restauration apporte le volume en ligne immédiatement, alors que les données sont téléchargées en arrière-plan. 

Avant de lancer une opération de restauration, vous devez être conscient des éléments suivants :

- **Que vous devez prendre le volume hors connexion** – prenez le volume hors connexion sur l’hôte et le périphérique avant de lancer l’opération de restauration. Bien que l’opération de restauration entraîne automatiquement le volume en ligne sur le périphérique, vous devez mettre manuellement le périphérique en ligne sur l’hôte. Dès que le volume est en ligne sur le périphérique, vous pouvez mettre le volume en ligne sur l’hôte. (Vous n’avez pas besoin d’attendre que l’opération de restauration terminée.) Pour les procédures, passez à [prendre un volume hors connexion](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **Après la restauration du type de volume** , les volumes supprimés sont restaurés en fonction du type de l’instantané ; c'est-à-dire, les volumes qui se trouvaient localement sont restaurées en tant que volumes ajoutés localement et volumes qui ont été assemblés sont restaurées en tant que volumes hiérarchisés.

    Pour les volumes existants, le type d’utilisation actuel du volume remplace le type qui est stocké dans l’instantané. Par exemple, si vous restaurez un volume à partir d’un instantané qui a été pris lorsque le type de volume a été monté en cascade et du type de volume maintenant épinglé localement (en raison d’une opération de conversion qui a été exécutée), le volume restauré comme un volume localement épinglé. De même, si un volume localement épinglé existant a été développé et restauré par la suite à partir d’un instantané plus ancien pris lorsque le volume est plus petit, le volume restauré conserve la taille actuelle de développé.

    Vous ne pouvez pas convertir un volume d’un volume monté en cascade à un volume épinglé localement ou à partir d’un volume local épinglé à un volume monté en cascade tandis que le volume est en cours de restauration. Attendez que l’opération de restauration est terminée, puis vous pouvez convertir le volume à un autre type. Pour plus d’informations sur la conversion d’un volume, allez à [changer le type de volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 

- **La taille du volume est reflétée dans le volume restauré** – il s’agit d’un aspect important si vous restaurez un volume localement épinglé qui a été supprimé (volumes ajoutés localement sont entièrement mis en service). Assurez-vous que vous disposez d’un espace suffisant avant d’essayer de restaurer un volume localement épinglé précédemment supprimé. 

- **Que vous ne pouvez pas étendre un volume pendant qu’il est en cours de restauration** , attendez que l’opération de restauration est terminée avant d’essayer d’étendre le volume. Pour plus d’informations sur l’extension d’un volume, accédez à [Modifier un volume](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Vous pouvez effectuer une sauvegarde tandis que vous restaurez un volume local** – procédures atteindre [utilisent le service de gestionnaire de StorSimple pour gérer les stratégies de sauvegarde](storsimple-manage-backup-policies.md).

- **Que vous pouvez annuler une opération de restauration** – si vous annulez l’opération de restauration, puis le volume sera restaurée dans l’état où il se trouvait avant le lancement de l’opération de restauration. Pour les procédures, passez à [Annuler un travail](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-to-use-the-backup-catalog"></a>Comment faire pour utiliser le catalogue de sauvegarde

La page **Catalogue de sauvegarde** fournit une requête qui vous aide à affiner votre sauvegarde définie la sélection. Vous pouvez filtrer les jeux de sauvegarde qui sont récupérés en fonction des paramètres suivants :

- **Périphérique** – le périphérique sur lequel le jeu de sauvegarde a été créé.
- **Stratégie de sauvegarde** ou un **volume** – la stratégie de sauvegarde ou le volume associé à ce jeu de sauvegarde.
- **À partir** d’et **à** , la plage de date et d’heure lors de la création du jeu de sauvegarde.

Les jeux de sauvegarde filtrés sont ensuite sous forme de tableau basé sur les attributs suivants :

- **Nom** : le nom de la stratégie de sauvegarde ou le volume associé avec le jeu de sauvegarde.
- **Taille** -la taille réelle du jeu de sauvegarde.
- **Créé le** – date et heure auxquelles les sauvegardes ont été créés. 
- **Type** – les jeux de sauvegarde peuvent être des snapshots locaux ou des snapshots en nuage. Un instantané local est une sauvegarde de toutes vos données de volume stocké localement sur le périphérique, qu’un instantané du nuage fait référence à la sauvegarde des données d’un volume résidant dans le nuage. Snapshots locaux fournissent un accès plus rapide, des instantanés de nuage ont été choisis pour la résilience des données.
- **Initié par** : les sauvegardes peuvent être lancées automatiquement selon une planification ou manuellement par un utilisateur. (Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Sinon, vous pouvez utiliser l’option **prendre la sauvegarde** pour effectuer une sauvegarde interactive.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Comment faire pour restaurer le volume de votre StorSimple à partir d’une sauvegarde

Vous pouvez utiliser la page **Catalogue de sauvegarde** pour restaurer le volume de votre StorSimple à partir d’une sauvegarde spécifique. N’oubliez pas, toutefois, que la restauration d’un volume sera rétabli le volume à l’état que qui était le sien lors de la sauvegarde a été effectuée. Toutes les données qui a été ajoutées après que l’opération de sauvegarde seront perdue.

> [AZURE.WARNING] Restauration à partir d’une sauvegarde remplacera les volumes existants à partir de la sauvegarde. Cela peut entraîner la perte de toutes les données ont été écrites après que la sauvegarde a été effectuée.

### <a name="to-restore-your-volume"></a>Pour restaurer le volume

1. Dans la page du service Gestionnaire de StorSimple, cliquez sur l’onglet **catalogue de sauvegarde** .

    ![Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. Sélectionnez une sauvegarde comme suite :
  1. Sélectionnez le périphérique approprié.
  2. Dans la liste déroulante, choisissez la stratégie de sauvegarde ou le volume pour la sauvegarde que vous souhaitez sélectionner.
  3. Spécifiez la plage de temps.
  4. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) Pour exécuter cette requête.
 
    Les sauvegardes associées le volume sélectionné ou la stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde.

3. Développez la jeu de sauvegarde pour afficher les volumes associés. Ces volumes doivent être prises en mode hors connexion sur l’hôte et le périphérique avant de pouvoir les restaurer. Accéder aux volumes sur la page de **Conteneurs de Volume** , puis suivez les étapes de [prendre un volume hors connexion](storsimple-manage-volumes-u2.md#take-a-volume-offline) pour les utiliser hors connexion.

    > [AZURE.IMPORTANT] Assurez-vous que vous avez pris les volumes en mode hors connexion sur l’hôte en premier lieu, avant de prendre les volumes hors ligne sur le périphérique. Si vous ne prenez pas les volumes en mode hors connexion sur l’hôte, il peut potentiellement entraîner une corruption des données.

4. Accédez à l’onglet **Catalogue de sauvegarde** , puis sélectionnez un jeu de sauvegarde.

5. Cliquez sur **restaurer** en bas de la page.

6. Vous serez invité à confirmer. Passez en revue les informations de restauration, puis activez la case à cocher de confirmation.

    ![Page de confirmation](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Cliquez sur l’icône de vérification ![Vérifiez l’icône de](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Ceci va démarrer un travail de restauration que vous pouvez afficher en accédant à la page **tâches** . 

8. Une fois la restauration terminée, vous pouvez vérifier que le contenu de vos volumes est remplacé par les volumes à partir de la sauvegarde.

![Vidéo disponible](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **vidéo disponible**

Pour regarder une vidéo qui montre comment vous pouvez utiliser le clone et restaurer les fonctions de StorSimple pour récupérer les fichiers supprimés, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>En cas d’échec de la restauration

Vous recevez une alerte si l’opération de restauration échoue pour une raison quelconque. Dans ce cas, actualisez la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide et que vous restaurez à partir du cloud, puis des problèmes de connectivité peuvent être à l’origine du problème. 

Pour terminer l’opération de restauration, prendre le volume hors connexion sur l’hôte et recommencez l’opération de restauration. Notez que les données du volume qui ont été effectuées au cours du processus de restauration de toutes les modifications seront perdues.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Gérer les StorSimple volumes](storsimple-manage-volumes-u2.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
