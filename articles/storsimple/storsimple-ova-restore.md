<properties
   pageTitle="Restaurer à partir d’une sauvegarde de votre tableau virtuel StorSimple"
   description="Pour en savoir plus sur la façon de restaurer une sauvegarde de votre tableau virtuel StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Restaurer à partir d’une sauvegarde de votre tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble 

Cet article concerne la version de Microsoft Azure StorSimple tableau virtuel (également connu sous le nom le périphérique virtuel local de StorSimple ou un périphérique virtuel de StorSimple) en cours d’exécution mars 2016 disponibilité générale (GA) ou une version ultérieure. Cet article décrit étape par étape comment restaurer à partir d’un jeu de sauvegarde de vos partages ou des volumes sur votre tableau virtuel StorSimple. L’article décrit également le fonctionne de la récupération au niveau de l’élément sur votre tableau virtuel de StorSimple qui est configuré comme un serveur de fichiers.


## <a name="restore-shares-from-a-backup-set"></a>Restauration des partages à partir d’un jeu de sauvegarde


**Avant d’essayer de restaurer des partages, vérifiez que vous disposez de suffisamment d’espace sur le périphérique pour terminer cette opération.** Pour restaurer à partir d’une sauvegarde, dans [Azure portal classique](https://manage.windowsazure.com/), procédez comme suit.

#### <a name="to-restore-a-share"></a>Pour restaurer un partage

1.  Parcourir le **catalogue de sauvegarde**. Filtrer par plage de temps pour rechercher vos sauvegardes et de périphérique approprié. Cliquez sur l’icône de vérification ![](./media/storsimple-ova-restore/image1.png) pour exécuter la requête.


1.  Dans la liste des jeux de sauvegarde s’affichée, cliquez sur et sélectionnez la sauvegarde spécifique. Développez la sauvegarde pour voir les différents partages sous elle. Cliquez sur et sélectionnez un partage que vous souhaitez restaurer.

2.  En bas de la page, cliquez sur **restaurer en tant que nouvelle**.

3.  Ceci va démarrer l’Assistant **restaurer en tant que nouveau partage** . Dans la page **emplacement et spécifier un nom** :


    1.  Vérifiez le nom du périphérique source. Ce doit être l’unité contenant le partage que vous souhaitez restaurer. La sélection du périphérique est désactivée. Pour sélectionner un volume source différent, vous devez quitter l’Assistant et sélectionnez à nouveau la sauvegarde à nouveau la valeur.

    2.  Fournir un nom de partage. Le nom de partage doit contenir 3 à 127 caractères.

    3.  Passez en revue les taille, type et autorisations associées le partage que vous essayez de restaurer. Vous ne pourrez pas modifier les propriétés du partage via l’Explorateur Windows une fois la restauration terminée.

    4.  Cliquez sur l’icône de vérification ![](./media/storsimple-ova-restore/image1.png).

        ![](./media/storsimple-ova-restore/image9.png)

1.  Une fois l’opération de restauration terminée, la restauration démarre et vous verrez une autre notification. Pour surveiller la progression de la restauration, cliquez sur **Afficher la tâche**. Vous accédez alors à la page **tâches** .

2.  Vous pouvez suivre la progression de l’opération de restauration. Lorsque la restauration est achevée à 100 %, accédez à la page **actions** sur votre périphérique.

3.  Vous pouvez maintenant afficher le nouveau partage restauré dans la liste des partages sur votre périphérique. Notez que la restauration est effectuée vers le même type de partage. Un partage hiérarchisé est restauré comme hiérarchisé et un partage épinglé localement comme un partage local épinglé.

Vous avez maintenant terminé la configuration de périphérique et expliqué comment sauvegarder ou restaurer un partage. 


## <a name="restore-volumes-from-a-backup-set"></a>Restauration des volumes à partir d’un jeu de sauvegarde


Pour restaurer à partir d’une sauvegarde, dans le portail Azure classique, procédez comme suit. L’opération de restauration restaure la sauvegarde vers un autre volume sur le même périphérique virtuel ; Vous ne pouvez pas restaurer sur un autre périphérique.

#### <a name="to-restore-a-volume"></a>Pour restaurer un volume

1.  Parcourir le **catalogue de sauvegarde**. Filtrer par plage de temps pour rechercher vos sauvegardes et de périphérique approprié. Cliquez sur l’icône de vérification ![](./media/storsimple-ova-restore/image1.png) pour exécuter la requête.

2.  Dans la liste des jeux de sauvegarde s’affichée, cliquez sur et sélectionnez la sauvegarde spécifique. Développez la sauvegarde pour afficher les différents volumes qu’il contient. Sélectionnez le volume que vous souhaitez restaurer. 

5.  En bas de la page, cliquez sur **restaurer en tant que nouvelle**. Démarre l’Assistant **restaurer en tant que nouveau volume** .

1.  Dans la page **emplacement et spécifier un nom** :


    1.  Vérifiez le nom du périphérique source. Ce doit être l’unité contenant le volume que vous souhaitez restaurer. La sélection du périphérique n’est pas disponible. Pour sélectionner un volume source différent, vous devez quitter l’Assistant et sélectionnez à nouveau la sauvegarde à nouveau la valeur.

    2.  Fournissez un nom de volume pour le volume restauré en tant que nouvelle. Le nom du volume doit contenir 3 à 127 caractères.

    3.  Cliquez sur l’icône de flèche.

        ![](./media/storsimple-ova-restore/image12.png)

1.  Dans la page **spécifier les hôtes qui peuvent utiliser ce volume** , sélectionnez les ACRs appropriés dans la liste déroulante.

    ![](./media/storsimple-ova-restore/image13.png)

1.  Cliquez sur l’icône de vérification ![](./media/storsimple-ova-restore/image1.png). Ceci va démarrer une opération de restauration, et vous verrez la notification suivante à qui la tâche est en cours.

2.  Une fois l’opération de restauration terminée, la restauration démarre et vous verrez une autre notification. Pour surveiller la progression de la restauration, cliquez sur **Afficher la tâche**. Vous accédez alors à la page **tâches** .

3.  Vous pouvez suivre la progression de l’opération de restauration. Accédez à la page de **Volumes** sur votre périphérique.

4.  Vous pouvez désormais afficher le nouveau volume restauré dans la liste des volumes sur votre périphérique. Notez que la restauration est effectuée pour le même type de volume. Un volume monté en cascade est restauré comme hiérarchisé et un volume localement épinglé est restauré comme un volume localement épinglé.

5.  Une fois que le volume apparaît en ligne sur la liste des volumes, le volume est disponible pour utilisation.  Sur l’hôte d’initiateur iSCSI, actualiser la liste des cibles de la fenêtre de propriétés d’initiateur iSCSI.  Une nouvelle cible qui contient le nom de volume restauré doit apparaître comme 'inactive' sous la colonne État.

6.  Sélectionnez la cible et cliquez sur **se connecter**.   Une fois l’initiateur connecté à la cible, l’état doit passer à **connecté**. 

7.  Dans la fenêtre **Gestion des disques** , les volumes montés seront affiche comme indiqué dans l’illustration suivante. Cliquez sur le volume en découvert (cliquez sur le nom du disque), puis cliquez sur **en ligne**.

> [AZURE.IMPORTANT] Lorsque vous tentez de restaurer un volume ou un partage à partir d’une sauvegarde de la valeur, si l’opération de restauration échoue, un volume cible ou partage peut encore être créé dans le portail. Il est important de supprimer ce volume de cible ou de le partager dans le portail afin de minimiser les problèmes futurs découlant de cet élément.

## <a name="item-level-recovery-ilr"></a>Au niveau de l’élément de récupération (ILR)

Cette version introduit la récupération au niveau de l’élément (ILR) sur un périphérique virtuel StorSimple configuré comme un serveur de fichiers. La fonctionnalité vous permet d’effectuer une restauration granulaire des fichiers et des dossiers à partir d’une sauvegarde de nuage de toutes les actions sur le périphérique StorSimple. Les utilisateurs peuvent récupérer des fichiers supprimés à partir de sauvegardes récentes à l’aide d’un modèle de libre-service.

Chaque action possède un dossier *.backups* contenant les sauvegardes les plus récentes. L’utilisateur peut naviguer vers la sauvegarde désirée, copiez les dossiers et les fichiers appropriés à partir de la sauvegarde et les restaurer. Cela permet d’éliminer les appels aux administrateurs pour restaurer des fichiers à partir de sauvegardes.

1.  Lors de la ILR, vous pouvez afficher les sauvegardes via l’Explorateur Windows. Cliquez sur le partage spécifique que vous souhaitez consulter la sauvegarde pour. Vous verrez un dossier *.backups* créé sous le partage qui stocke toutes les sauvegardes. Développez le dossier *.backups* pour afficher les sauvegardes. Le dossier affiche ensuite la vue éclatée de la hiérarchie de la sauvegarde entière. Cet affichage est créé à la demande et ne prend que quelques secondes pour en créer.

    Les 5 dernières sauvegardes s’affichent de cette manière et peuvent être utilisés pour effectuer une restauration au niveau élément. Les sauvegardes récentes 5 incluent à la fois les sauvegardes manuelles et planifiées par défaut.

    
    -   **Les sauvegardes planifiées** , nommé en tant que &lt;nom de périphérique&gt;DailySchedule-AAAAMMJJ-HHMMSS-UTC.

    -   **Sauvegardes manuelles** nommé comme Ad-hoc-AAAAMMJJ-HHMMSS-UTC.
    
        ![](./media/storsimple-ova-restore/image14.png)

1.  Identifier la sauvegarde qui contient la version la plus récente du fichier supprimé. Bien que le nom du dossier contient un horodatage UTC dans chacun des cas ci-dessus, l’heure à laquelle le dossier a été créé est périphérique réel lors du démarrage de la sauvegarde. L’horodatage du dossier permet de localiser et d’identifier les sauvegardes.

2.  Recherchez le dossier ou le fichier que vous souhaitez restaurer dans la sauvegarde que vous avez identifié à l’étape précédente. Remarque Vous ne pouvez afficher que les fichiers ou les dossiers que vous disposez d’autorisations. Si vous n’êtes pas en mesure d’accéder à certains fichiers ou dossiers, vous devrez contacter un administrateur de partage qui permet à l’Explorateur Windows pour modifier les autorisations de partage et vous permettent d’accéder au fichier ou au dossier. Il est recommandé que l’administrateur de partage soit un groupe d’utilisateurs au lieu d’un seul utilisateur.

3.  Copiez le fichier ou le dossier dans le partage approprié sur votre serveur de fichiers StorSimple.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez créer des partages, sauvegarder des partages et restaurer des données sur une baie virtuelle StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur [l’administration de votre tableau virtuel StorSimple à l’aide de l’interface utilisateur de web local](storsimple-ova-web-ui-admin.md).
