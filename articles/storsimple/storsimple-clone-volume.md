<properties
   pageTitle="Cloner le volume de votre StorSimple | Microsoft Azure"
   description="Décrit les types différents de clone et quand les utiliser et explique comment vous pouvez utiliser une jeu de sauvegarde à cloner un volume individuel."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Le service Gestionnaire de StorSimple permet de clonage d’un volume

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Vue d’ensemble

La page **Catalogue de sauvegarde** du service Gestionnaire de StorSimple affiche tous les jeux de sauvegarde créés lors des sauvegardes manuelles ou automatisées. Vous pouvez utiliser cette page pour répertorier toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionner ou supprimer les sauvegardes, ou utiliser une sauvegarde pour la restauration ou le clonage d’un volume.

![Page du catalogue de sauvegarde](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Ce didacticiel explique comment vous pouvez utiliser une jeu de sauvegarde à cloner un volume individuel. Il explique également la différence entre les clones *transitoires* et *permanentes* . 

## <a name="create-a-clone-of-a-volume"></a>Créer un clone d’un volume

Vous pouvez créer un clone sur le même périphérique, un autre périphérique ou même un ordinateur virtuel à l’aide d’un local ou un instantané du nuage.

#### <a name="to-clone-a-volume"></a>Pour cloner un volume

1. Sur la page du service Gestionnaire de StorSimple, cliquez sur l’onglet **catalogue de sauvegarde** et sélectionnez un jeu de sauvegarde.

2. Développez la jeu de sauvegarde pour afficher les volumes associés. Cliquez sur et sélectionnez un volume dans le jeu de sauvegarde.

     ![Clonage d’un volume](./media/storsimple-clone-volume/HCS_Clone.png) 

3. Cliquez sur le **Clone** pour cloner le volume sélectionné.

4. Dans l’Assistant de Volume de clonage, sous **emplacement et spécifier un nom**:

  1. Identifier un périphérique cible. Il s’agit de l’emplacement où sera créé le clone. Vous pouvez choisir le même périphérique ou spécifier un autre périphérique. Si vous choisissez un volume associé avec d’autres fournisseurs de service cloud (pas d’Azure), la liste déroulante pour le périphérique cible affiche uniquement les périphériques physiques. Impossible de cloner un volume associé avec d’autres fournisseurs de service de cloud sur un périphérique virtuel.

        >  [AZURE.NOTE] Assurez-vous que la capacité requise pour le clone est inférieure à la capacité disponible sur le périphérique cible.
  2. Spécifiez un nom de volume unique pour le clone. Le nom doit contenir entre 3 et 127 caractères.
  3. Cliquez sur l’icône de la flèche ![icône de la flèche](./media/storsimple-clone-volume/HCS_ArrowIcon.png) Pour passer à la page suivante.

5. Sous **spécifier les hôtes qui peuvent utiliser ce volume**:

  1. Spécifier un enregistrement de contrôle d’accès (ACR) pour le clone. Vous pouvez ajouter un nouveau blocage ou choisissez dans la liste existante.
  2. Cliquez sur l’icône de vérification ![icône de la vérification](./media/storsimple-clone-volume/HCS_CheckIcon.png)Pour terminer l’opération.

6. Un travail de clone sera lancé et vous serez averti lorsque le clone est créé avec succès. Cliquez sur **Afficher la tâche** pour surveiller le travail de clone dans la page **tâches** .

7. À l’issue de l’opération de clonage :

  1. Accédez à la page des **périphériques** et sélectionnez l’onglet **Conteneurs d’un Volume** . 
  2. Sélectionnez le conteneur de volume associé avec le volume source que vous le cloné. Dans la liste des volumes, vous devez voir le clone qui vient d’être créé.

>[AZURE.NOTE] Par défaut et surveillance de sauvegarde sont automatiquement désactivés dans un volume cloné.

Un clone est créé de cette manière est un clone en régime transitoire. Pour plus d’informations sur les types de clone, consultez [transitoire et clones permanentes](#transient-vs.-permanent-clones).

Ce clone est un volume standard, et toute opération est possible sur un volume sera disponible pour le clone. Vous devez configurer ce volume pour les sauvegardes.

## <a name="transient-vs-permanent-clones"></a>Transitoires et permanentes clones

Les clones transitoires et permanentes sont créés uniquement lorsque vous effectuez un clonage sur un périphérique différent. Vous pouvez cloner un volume spécifique à partir d’un jeu de sauvegarde à un autre périphérique. Un clone créé de cette manière est un clone *en régime transitoire* . Le clone transitoire aura des références au volume d’origine et utilise ce volume pour lire lors de l’écriture localement. 

Une fois que vous prenez un instantané du nuage d’un clone en régime transitoire, le clone résultant sera un clone *permanente* . Le clone permanent est indépendant et n’a pas toutes les références au volume d’origine qu’il a été cloné à partir de.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scénarios pour les clones transitoires et permanentes

Les sections suivantes décrivent des situations d’exemple dans lequel les clones de transitoires et permanentes peuvent être utilisés.

### <a name="item-level-recovery-with-a-transient-clone"></a>Restauration au niveau élément avec un clone en régime transitoire

Vous devez restaurer un fichier de présentation Microsoft PowerPoint une ans. Votre administrateur identifie la sauvegarde spécifique à partir de cette période et filtre ensuite le volume. L’administrateur puis clone du volume, localise le fichier que vous recherchez et qu’il fournit pour vous. Dans ce scénario, un clone transitoire est utilisé. 
 
![Vidéo disponible](./media/storsimple-clone-volume/Video_icon.png) **vidéo disponible**

Pour regarder une vidéo qui montre comment vous pouvez utiliser le clone et restaurer les fonctions de StorSimple pour récupérer les fichiers supprimés, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Test dans l’environnement de production avec un clone permanent

Vous devez vérifier si un bogue de test dans l’environnement de production. Vous créez un clone du volume dans l’environnement de production en prenant un instantané de nuage de ce clone. Le volume de clonage est désormais indépendant. Dans ce scénario, un clone permanent est utilisé.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment faire pour [restaurer un volume de StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

 
