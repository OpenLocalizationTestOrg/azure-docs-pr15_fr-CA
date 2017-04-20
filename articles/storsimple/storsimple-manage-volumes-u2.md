<properties
   pageTitle="Gérer vos volumes StorSimple (U2) | Microsoft Azure"
   description="Explique comment ajouter, de modifier, de contrôler et de supprimer des volumes de StorSimple et pour les déconnecter si nécessaire."
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
   ms.workload="NA"
   ms.date="10/28/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Le service Gestionnaire de StorSimple permet de gérer les volumes (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment utiliser le service Gestionnaire de StorSimple pour créer et gérer des volumes sur le périphérique de le StorSimple et d’un périphérique virtuel de StorSimple avec la mise à jour 2 est installé.

Le service Gestionnaire de StorSimple est une extension du portail classique Azure qui vous permet de gérer votre solution de StorSimple à partir d’une interface web unique. En plus de la gestion des volumes, vous pouvez utiliser le service Gestionnaire de StorSimple pour créer et gérer des services de StorSimple, afficher et gérer les périphériques, afficher les alertes et afficher et gérer les stratégies de sauvegarde et le catalogue de sauvegarde.

## <a name="volume-types"></a>Types de volumes

StorSimple volumes peuvent être :

- **Localement épinglé volumes**: dans ces volumes, les données restent sur le périphérique StorSimple local à tout moment.
- **Les volumes hiérarchisé**: données de ces volumes peuvent renverser vers le nuage.

Un volume d’archives est un type de volume monté en cascade. La taille de segment de déduplication plue utilisée pour les volumes de l’archivage permet de transférer des segments de données plus grandes dans le nuage. 

Si nécessaire, vous pouvez modifier le volume type de local à plusieurs niveaux ou à partir de plusieurs niveaux locaux. Pour plus d’informations, allez à [changer le type de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes ajoutés localement

Volumes ajoutés localement sont entièrement mis en service des volumes qui ne pas des données de couche dans le nuage, afin de garantir un local des garanties pour les données principales, indépendantes de la connectivité de nuage. Données sur les volumes ajoutés localement ne sont pas dédupliquées et compressées ; Toutefois, des instantanés de volumes ajoutés localement sont dédupliqués. 

Volumes ajoutés localement sont totalement configurés ; Par conséquent, vous devez disposer de suffisamment d’espace sur votre appareil lorsque vous les créez. Vous pouvez configurer des volumes ajoutés localement jusqu'à une taille maximale de 8 To de données sur le périphérique StorSimple 8100 et 20 To de données sur le périphérique 8600. StorSimple réserve l’espace restant local sur le périphérique pour le traitement de données, les métadonnées et les instantanés. Vous pouvez augmenter la taille d’un volume localement ajouté à l’espace maximal disponible, mais vous ne pouvez pas diminuer la taille d’un volume créé qu’une seule fois.

Lorsque vous créez un volume localement épinglé, l’espace disponible pour la création de volumes hiérarchisés est réduite. L’inverse est également vrai : Si vous avez plusieurs niveaux des volumes existants, l’espace disponible pour créer localement pinned volumes sera inférieur aux limites maximales susmentionnées. Pour plus d’informations sur les volumes locaux, consultez les [questions fréquemment posées sur les volumes ajoutés localement](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Plusieurs niveaux de volumes

Hiérarchisé de volumes sont des volumes provisionnées dans lequel les données fréquemment sollicitées restent locales sur le périphérique et moins de données fréquemment utilisés est automatiquement monté en cascade dans le nuage. Provisionnement fin est une technologie de virtualisation dans lequel stockage disponible semble dépasser les ressources physiques. Au lieu de la réservation d’espace de stockage suffisant à l’avance, StorSimple utilise le provisionnement fin à allouer juste assez d’espace pour répondre aux besoins actuels. La nature élastique de stockage en nuage facilite cette approche car StorSimple peut augmenter ou diminuer le stockage en nuage pour satisfaire aux demandes changeantes.

Si vous utilisez le volume de plusieurs niveaux de données d’archivage, la case à cocher **utiliser ce volume des données les moins souvent consultées archivage** modifie la taille de segment de déduplication pour votre volume à 512 Ko. Si vous ne sélectionnez pas cette option, le volume de plusieurs niveaux correspondant utilise une taille de segment de 64 Ko. Une plus grande taille de fragment de déduplication permet au périphérique afin d’accélérer le transfert de données d’archivage volumineux vers le nuage.

>[AZURE.NOTE] Archivage volumes créés avec une version de 2 avant mise à jour de StorSimple sera importés sous la forme hiérarchisée avec archivage la case à cocher.

### <a name="provisioned-capacity"></a>Capacité de mise en service

Reportez-vous au tableau suivant pour une capacité maximale mis en service pour chaque type de dispositif et de volume. (Notez que les volumes ajoutés localement ne sont pas disponibles sur un périphérique virtuel).

|             | Taille maximale du volume monté en cascade | Maximum localement épinglé la taille du volume |
|-------------|----------------------------|------------------------------------|
| **Périphériques physiques** |       |       |
| 8100                 | 64 TO | 8 TO |
| 8600                 | 64 TO | 20 TO |
| **Périphériques virtuels**  |       |       |
| 8010                | 30 TO | N/A   |
| 8020               | 64 TO | N/A   |

## <a name="the-volumes-page"></a>La page Volumes

La page **Volumes** vous permet de gérer les volumes de stockage qui sont configurés sur le périphérique Microsoft Azure StorSimple pour vos initiateurs (serveurs). Il affiche la liste des volumes sur le périphérique StorSimple.

 ![Page de volumes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Un volume est constitué d’une série d’attributs :

- **Nom de volume** – un nom descriptif qui doit être unique et vous aide à identifier le volume. Ce nom est également utilisé dans les rapports d’analyse lorsque vous filtrez sur un volume spécifique.

- **Statut** – peut être en ligne ou hors connexion. Si un volume est en mode hors connexion, il n’est pas visible aux initiateurs (serveurs) qui sont autorisés à accéder à utiliser le volume.

- **Capacité** – Spécifie le montant total des données qui peuvent être stockés par l’initiateur (serveur). -Pinned localement des volumes sont entièrement mis en service et résident sur le périphérique StorSimple. Volumes hiérarchisés sont exactement provisionnés et les données sont dédupliquées. Avec les volumes provisionnées, votre périphérique ne préallouer de capacité de stockage physique en interne ou sur le nuage en fonction de la capacité du volume configuré. La capacité du volume est allouée et utilisée à la demande.

- **Type** – indique si le volume est **hiérarchisé** (par défaut) ou **localement épinglé**.

- **Sauvegarde** – indique l’existence d’une stratégie de sauvegarde par défaut pour le volume.

- **Accès** – spécifie les initiateurs (serveurs) qui sont autorisés à accéder à ce volume. Les initiateurs ne sont pas membres de l’enregistrement du contrôle d’accès (ACR) qui est associé avec le volume ne verront pas le volume.

- **Analyse** – Spécifie si un volume est en cours d’analyse. Un volume aura de surveillance activé par défaut lors de sa création. Surveillance des est, toutefois, être désactivée pour un clone du volume. Pour activer l’analyse d’un volume, suivez les instructions dans le [Moniteur d’un volume](#monitor-a-volume). 

Suivez les instructions de ce didacticiel pour effectuer les tâches suivantes :

- Ajouter un volume 
- Modifier un volume 
- Modifier le type de volume
- Supprimer un volume 
- Prélever un volume hors connexion 
- Analyser un volume 

## <a name="add-a-volume"></a>Ajouter un volume

Vous avez [créé un volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) lors du déploiement de votre solution de StorSimple. Ajout d’un volume est une procédure similaire.

#### <a name="to-add-a-volume"></a>Pour ajouter un volume

1. Dans la page des **périphériques** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs d’un Volume** .

2. Sélectionnez un conteneur de volume dans la liste et double-cliquez dessus pour accéder aux volumes associés au conteneur.

3. Cliquez sur **Ajouter** au bas de la page. Ajouter un Assistant de volume démarre.

     ![Ajouter l’Assistant paramètres de base d’un volume](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. Dans l’Assistant Ajout d’un volume, sous **Paramètres de base**, effectuez les opérations suivantes :

  1. Fournir un **nom** pour le volume.
  2. Dans la liste déroulante, sélectionnez un **Type d’utilisation** . Pour les charges de travail nécessitant des données soient disponibles localement sur le périphérique à tout moment, sélectionnez **Épinglé localement**. Pour tous les autres types de données, sélectionnez **hiérarchisé**. (**Hiérarchisé** est la valeur par défaut).
  3. Si vous avez sélectionné **hiérarchisé** à l’étape 2, vous pouvez sélectionner la case à cocher **utiliser ce volume des données les moins souvent consultées archivage** pour configurer un volume d’archives.
  4. Permet d’entrer la **Capacité de mise en service** pour le volume en Go ou to. Consultez [Provisioned capacité](#provisioned-capacity) pour des tailles maximales pour chaque type de dispositif et de volume. Examinez la **Capacité disponible** pour déterminer la quantité de stockage est effectivement disponible sur votre appareil.

5. Cliquez sur l’icône de la flèche![Icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Si vous configurez un volume localement épinglé, vous verrez le message suivant.

    ![Modifier le message de type de Volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Cliquez sur l’icône de flèche ![icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)pour accéder à la page **Des paramètres supplémentaires** .

    ![Ajouter des paramètres supplémentaires de l’Assistant Volume](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. Sous **Paramètres supplémentaires**, ajoutez un nouvel enregistrement de contrôle d’accès (ACR) :
  
  1. Dans la liste déroulante, sélectionnez un enregistrement de contrôle d’accès (ACR). Vous pouvez également ajouter un nouveau blocage. ACRs déterminent quels hôtes peuvent accéder à vos volumes en faisant correspondre le nom qualifié de l’hôte avec celle figurant dans l’enregistrement. Si vous ne spécifiez pas un blocage, vous verrez le message suivant.

        ![Spécifiez le blocage](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Nous vous conseillons de sélectionner la case à cocher **activer une sauvegarde par défaut pour ce volume** .
  3. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Pour créer le volume avec les paramètres spécifiés.

Le nouveau volume est désormais prêt à l’emploi.

>[AZURE.NOTE] Si vous créez un volume localement épinglé et puis créez un autre volume local épinglé immédiatement par la suite, la travaux s’exécutent de façon séquentielle la création du volume. La première tâche de création de volume doit se terminer avant que la tâche de création de volume suivante puisse commencer.

## <a name="modify-a-volume"></a>Modifier un volume

Modifier un volume lorsque vous avez besoin développer ou modifier les hôtes à accéder au volume.

> [AZURE.IMPORTANT] 
>
> - Si vous modifiez la taille du volume sur le périphérique, doit être modifié sur l’hôte, ainsi que la taille du volume. 
> - Les étapes du côté hôte décrites ici concernent Windows Server 2012 (2012R2). Procédures pour Linux ou d’autres systèmes d’exploitation seront différents. Reportez-vous aux instructions de votre système d’exploitation hôte lorsque vous modifiez le volume sur un hôte exécutant un autre système d’exploitation. 

#### <a name="to-modify-a-volume"></a>Pour modifier un volume

1. Dans la page des **périphériques** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs d’un Volume** .

2. Sélectionnez un conteneur de volume dans la liste et double-cliquez dessus pour afficher les volumes associés au conteneur.

3. Sélectionnez un volume et en bas de la page, cliquez sur **Modifier**. L’Assistant Modification de volume démarre.

4. Dans l’Assistant Modification de volume, sous **Paramètres de base**, vous pouvez effectuer les opérations suivantes :

  - Modifier le **nom**.
  - Convertir le **Type d’utilisation** de localement épinglé à plusieurs niveaux ou à partir de plusieurs niveaux à localement épinglé (pour plus d’informations, consultez [Modifier le type de volume](#change-the-volume-type) ).
  - Augmentez **capacité de mise en service**. La **Capacité de mise en service** peut uniquement être augmentée. Vous ne pouvez pas réduire un volume après sa création.

5. Sous **Paramètres supplémentaires**, vous pouvez modifier le blocage, sous réserve que le volume est hors connexion. Si le volume est en ligne, vous devez déconnecter tout d’abord. Reportez-vous aux étapes de [prendre un volume hors connexion](#take-a-volume-offline) avant de modifier le blocage.

    > [AZURE.NOTE] Vous ne pouvez pas modifier l’option **d’activer une sauvegarde par défaut** pour le volume.

6. Enregistrez vos modifications en cliquant sur l’icône de vérification ![icône de la vérification](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Le portail classique Azure affichera un message de volume mise à jour. Il affiche un message de réussite lorsque le volume a été mis à jour avec succès.

7. Si vous étendez un volume, procédez comme suit sur l’ordinateur hôte de Windows :

   1. Accédez à **gestion de l’ordinateur** ->**Gestion des disques**.
   2. Cliquez sur **Gestion des disques** et sélectionnez **Analyser les disques**.
   3. Dans la liste des disques, sélectionnez le volume mis à jour, avec le bouton droit et sélectionnez puis **Étendre le Volume**. Démarrage de l’Assistant étendre le Volume. Cliquez sur **suivant**.
   4. Terminez l’Assistant, en acceptant les valeurs par défaut. Une fois l’Assistant terminé, le volume doit afficher l’augmentation de la taille.

    >[AZURE.NOTE] Si vous développez un volume localement épinglé, puis un autre localement épinglé volume immédiatement par la suite, les travaux d’extension de volume exécutent séquentiellement. La première tâche d’extension de volume doit se terminer avant que la tâche d’extension de volume à l’adresse suivante puisse commencer.

![Vidéo disponible](./media/storsimple-manage-volumes-u2/Video_icon.png) **vidéo disponible**

Pour regarder une vidéo qui illustre comment étendre un volume, cliquez [ici](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Modifier le type de volume

Vous pouvez modifier le type de volume à partir de hiérarchisé épinglée localement ou à partir de localement par épinglé à plusieurs niveaux. Toutefois, cette conversion ne doit pas être une fréquence. Parmi les raisons de la conversion d’un volume à partir de plusieurs niveaux de mise en attente localement sont les suivantes :

- Locales garanties en ce qui concerne les performances et la disponibilité des données
- Élimination des latences du nuage et les problèmes de connectivité de nuage.

En général, il s’agit des petits volumes existants que vous voulez accéder régulièrement. Un volume localement épinglé est entièrement configuré lors de sa création. Si vous convertissez un volume monté en cascade sur un volume local épinglé, StorSimple vérifie que vous disposez d’un espace suffisant sur votre périphérique avant de démarrer la conversion. Si vous disposez d’un espace insuffisant, vous recevrez un message d’erreur et l’opération sera annulée. 

> [AZURE.NOTE] Avant de commencer une conversion à partir de plusieurs niveaux de mise en attente localement, vérifiez que l’espace disque de vos autres charges de travail. 

Vous pouvez souhaiter modifier un volume localement ajouté à un volume monté en cascade, si vous avez besoin d’espace supplémentaire pour configurer d’autres volumes. Lorsque vous convertissez le volume localement épinglé à monter en cascade, la capacité disponible sur le périphérique augmente par la taille de la capacité de lancé. Si les problèmes de connectivité empêchent la conversion d’un volume à partir du type local au type hiérarchisé, le volume local présentera les propriétés d’un volume monté en cascade jusqu'à ce que la conversion est terminée. C’est parce que certaines données peuvent avoir répandu dans le nuage. Ces données dispersées continuera occuper l’espace local sur le périphérique qui ne peut pas être libéré jusqu'à ce que l’opération de redémarrage est terminée.

>[AZURE.NOTE] Conversion d’un volume peut prendre du temps et vous ne pouvez pas annuler une conversion après son démarrage. Le volume reste en ligne pendant la conversion et vous pouvez effectuer des sauvegardes, mais vous ne pouvez pas développer ou restaurer le volume pendant la conversion.  

Conversion à partir d’une liste hiérarchisée en un volume localement épinglé peut nuire aux performances du dispositif. En outre, les facteurs suivants peuvent augmenter le temps que nécessaire pour terminer la conversion :

- Il est de la bande passante.

- Il n’y a aucune sauvegarde en cours.

Pour réduire les effets de ces facteurs peuvent :

- Passez en revue vos stratégies de bande passante et assurez-vous qu’un dédié 40 Mbits/s de bande passante est disponible.
- Planifier la conversion pour les heures creuses.
- Prendre un instantané de nuage avant de démarrer la conversion.

Si vous convertissez en plusieurs volumes (prise en charge de différentes charges de travail), puis vous devez considérer comme prioritaires la conversion du volume afin que les volumes de priorité supérieure sont converties en premier. Par exemple, vous devez convertir les volumes qui hébergent les machines virtuelles (VM) ou les volumes avec des charges de travail SQL avant de convertir des volumes avec des charges de travail de partage de fichier.

#### <a name="to-change-the-volume-type"></a>Pour modifier le type de volume

1. Dans la page des **périphériques** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs d’un Volume** .

2. Sélectionnez un conteneur de volume dans la liste et double-cliquez dessus pour afficher les volumes associés au conteneur.

3. Sélectionnez un volume et en bas de la page, cliquez sur **Modifier**. L’Assistant Modification de volume démarre.

4. Dans la page **Paramètres de base** , modifiez le type d’utilisation en sélectionnant le nouveau type à partir de la liste déroulante **Type d’utilisation** .

    - Si vous modifiez le type de mise en attente **localement**, StorSimple vérifie s’il existe une capacité suffisante.
    - Si vous modifiez le type à **hiérarchisé** et ce volume sera utilisé pour les données d’archivage, activez la case à cocher **utiliser ce volume des données les moins souvent consultées archivage** .

        ![Case à cocher de l’archive](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Cliquez sur l’icône de flèche ![icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) pour accéder à la page **Paramètres supplémentaires** . Si vous configurez un volume épinglé localement, le message suivant s’affiche.

    ![Modifier le message de type de Volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Cliquez sur l’icône de la flèche ![icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) Pour continuer.

7. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Pour démarrer le processus de conversion. Le portail Azure affichera un message de volume mise à jour. Il affiche un message de réussite lorsque le volume a été mis à jour avec succès.

## <a name="take-a-volume-offline"></a>Prélever un volume hors connexion

Vous devrez peut-être déconnecter un volume lors de la planification pour la modifier ou la supprimer. Lorsqu’un volume est en mode hors connexion, il n’est pas disponible pour l’accès en lecture-écriture. Vous devrez prendre du volume hors connexion sur l’hôte et sur le périphérique. 

#### <a name="to-take-a-volume-offline"></a>Accéder à un volume hors connexion

1. Assurez-vous que le volume en question n’est pas en cours d’utilisation avant de passer en mode hors connexion il.

2. Prendre le volume hors connexion sur l’hôte du premier. Ce qui élimine tout risque de corruption des données sur le volume. Pour connaître les étapes spécifiques, reportez-vous aux instructions de votre système d’exploitation hôte.

3. Une fois que l’hôte est hors ligne, prendre du volume sur le périphérique en mode hors connexion, effectuez les opérations suivantes :

  1. Dans la page des **périphériques** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs d’un Volume** . L’onglet **Conteneurs d’un Volume** répertorie sous forme de tous les conteneurs de volume qui sont associées avec le périphérique.
  2. Sélectionnez un conteneur de volume et cliquez dessus pour afficher la liste de tous les volumes dans le conteneur.
  3. Sélectionnez un volume, puis cliquez sur **Déconnecter**.
  4. Lorsque vous êtes invité à confirmer une opération, cliquez sur **Oui**. Le volume doit maintenant être hors connexion.

    Une fois un volume est en mode hors connexion, l’option **Mettre en ligne** devient disponible.

> [AZURE.NOTE] La commande **Déconnecter** envoie une demande au périphérique à mettre le volume hors connexion. Si les hôtes utilisent toujours le volume, cela se traduit par des connexions interrompues, mais mettre le volume hors connexion n’échoue pas. 

## <a name="delete-a-volume"></a>Supprimer un volume

> [AZURE.IMPORTANT] Vous pouvez supprimer un volume que s’il est en mode hors connexion.

Effectuez les étapes suivantes pour supprimer un volume.

#### <a name="to-delete-a-volume"></a>Pour supprimer un volume

1. Dans la page des **périphériques** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs d’un Volume** .

2. Sélectionnez le conteneur de volume qui a le volume que vous souhaitez supprimer. Cliquez sur le conteneur du volume pour accéder à la page de **Volumes** .

3. Tous les volumes associés à ce conteneur sont affichés dans un format tabulaire. Vérifiez l’état du volume que vous souhaitez supprimer. Si le volume que vous souhaitez supprimer n’est pas en mode hors connexion, mettez hors connexion tout d’abord, suivant les étapes de [prendre un volume hors connexion](#take-a-volume-offline).

4. Une fois que le volume est en mode hors connexion, cliquez sur **Supprimer** au bas de la page.

5. Lorsque vous êtes invité à confirmer une opération, cliquez sur **Oui**. Le volume va être supprimé, et la page **Volumes** s’affichera la liste mise à jour des volumes au sein du conteneur.

    >[AZURE.NOTE]Si vous supprimez un volume localement épinglé, l’espace disponible pour les nouveaux volumes de ne peut pas être mis à jour immédiatement. Le Service Gestionnaire de StorSimple met à jour périodiquement l’espace local disponible. Nous vous recommandons de qu'attendre quelques minutes avant d’essayer de créer le nouveau volume.<br> En outre, si vous supprimez un volume localement épinglé puis supprimez localement épinglé volume immédiatement par la suite, la suppression de volume que les tâches sont exécutées séquentiellement. La première tâche de suppression de volume doit être terminée avant de commence la tâche de suppression de volume suivante.
 
## <a name="monitor-a-volume"></a>Analyser un volume

Contrôle du volume vous permet de collecter des statistiques d’I/O-connexes pour un volume. Surveillance est activée par défaut pour les 32 premiers volumes que vous créez. Surveillance des volumes supplémentaires est désactivé par défaut. Surveillance des volumes clonés est également désactivé par défaut.

Effectuez les étapes suivantes pour activer ou désactiver l’analyse d’un volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Pour activer ou désactiver le contrôle de volume

1. Dans la page des **périphériques** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs d’un Volume** .

2. Sélectionnez le conteneur du volume dans lequel réside le volume, puis cliquez sur le conteneur du volume pour accéder à la page de **Volumes** .

3. Tous les volumes associés à ce conteneur sont répertoriés dans l’affichage sous forme de tableau. Cliquez sur et sélectionnez le volume ou le clone du volume.

4. En bas de la page, cliquez sur **Modifier**.

5. Dans l’Assistant Modification de Volume, sous **Paramètres de base**, sélectionnez **Activer** ou **désactiver** dans la liste déroulante de **surveillance** .

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment le [clone d’un volume de StorSimple](storsimple-clone-volume.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

 
