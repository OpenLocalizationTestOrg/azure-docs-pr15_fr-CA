<properties 
   pageTitle="Déployer le Gestionnaire de snapshots StorSimple | Microsoft Azure"
   description="Découvrez comment télécharger et installer le Gestionnaire de snapshots StorSimple, un composant logiciel enfichable MMC pour la gestion des fonctionnalités de sauvegarde et de protection de données StorSimple."
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

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Déployer le composant logiciel enfichable MMC du Gestionnaire de snapshots de StorSimple,

## <a name="overview"></a>Vue d’ensemble

Le Gestionnaire de snapshots StorSimple est un composant logiciel enfichable Microsoft Management Console (MMC) qui simplifie la protection des données et la gestion de la sauvegarde dans un environnement Microsoft Azure StorSimple. Avec le Gestionnaire de snapshots de StorSimple, vous pouvez gérer Microsoft Azure StorSimple sur site et stockage en nuage comme s’il s’agissait d’un système de stockage totalement intégré, donc de simplifier les processus de sauvegarde et de restauration et de réduire les coûts. 

Ce didacticiel décrit la configuration requise, ainsi que des procédures pour l’installation, la suppression et la mise à niveau de StorSimple Gestionnaire de snapshots.

>[AZURE.NOTE] 
>
>- Vous ne pouvez pas utiliser StorSimple Snapshot Manager pour gérer les réseaux virtuels Microsoft Azure StorSimple (également connu sous le nom StorSimple local périphériques virtuels).
>
>- Si vous envisagez d’installer StorSimple mise à jour 2 sur le périphérique StorSimple, veillez à télécharger la dernière version du Gestionnaire de snapshots StorSimple et l’installer **avant d’installer StorSimple mise à jour 2**. La dernière version du Gestionnaire de snapshots StorSimple est à compatibilité descendante et fonctionne avec toutes les versions publiées de Microsoft Azure StorSimple. Si vous utilisez la version précédente du Gestionnaire de snapshots StorSimple, vous devez mettre à jour (vous n’avez pas besoin de désinstaller la version précédente avant d’installer la nouvelle version).

## <a name="storsimple-snapshot-manager-installation"></a>Installation du Gestionnaire de capture instantanée StorSimple

StorSimple Snapshot Manager peut être installé sur les ordinateurs qui exécutent le Service Pack 1 de Windows Server 2008 R2, Windows Server 2012 ou système d’exploitation Windows Server 2012 R2. Sur les serveurs exécutant Windows 2008 R2, vous devez également installer le Service Pack 1 de Windows Server 2008 et de Windows Management Framework 3.0. 

Avant d’installer ou de mettre à niveau le composant logiciel enfichable Gestionnaire de snapshots StorSimple pour Microsoft Management Console (MMC), assurez-vous que le serveur Microsoft Azure StorSimple de périphérique et de l’hôte sont correctement configurés. 

## <a name="configure-prerequisites"></a>Configuration des composants requis

Les étapes suivantes fournissent une vue d’ensemble des tâches de configuration que vous devez effectuer avant d’installer le Gestionnaire de capture instantanée StorSimple. Pour terminer la configuration de Microsoft Azure StorSimple et les informations de configuration, y compris la configuration système requise et les instructions détaillées, voir [déploiement votre périphérique de StorSimple local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Avant de commencer, passez en revue l' [aide-mémoire de configuration de déploiement](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) et et les [conditions préalables au déploiement](storsimple-deployment-walkthrough.md#deployment-prerequisites) dans [déployer votre périphérique de StorSimple local](storsimple-deployment-walkthrough.md).
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>Avant d’installer le Gestionnaire de snapshots de StorSimple

1. Déballage, de montage et connecter le périphérique Microsoft Azure StorSimple comme décrit dans [installer votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md) ou [installer votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Assurez-vous que votre ordinateur hôte exécute l’un des systèmes d’exploitation suivants :

    - Windows Server 2008 R2 (sur les serveurs exécutant Windows 2008 R2, vous devez également installer le Service Pack 1 de Windows Server 2008 et de Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Pour un périphérique virtuel de StorSimple, l’hôte doit être une Machine virtuelle de Microsoft Azure. 

3. Vérifiez que toutes les exigences de configuration de Microsoft Azure StorSimple. Pour plus d’informations, consultez [conditions préalables au déploiement](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Connectez le périphérique à l’hôte et effectuer la configuration initiale. Pour plus d’informations, passez aux [étapes de déploiement](storsimple-deployment-walkthrough.md#deployment-steps).

5. Créer des volumes sur le périphérique, les affecter à l’hôte et vérifiez que l’hôte peut monter et les utiliser. Gestionnaire de snapshots StorSimple prend en charge les types de volumes suivants : 

    - Volumes de base
    - Volumes simples
    - Volumes dynamiques
    - Volumes dynamiques en miroir (RAID 1)
    - Volumes partagés de cluster
 
    Pour plus d’informations sur la création de volumes sur le dispositif de StorSimple ou d’un périphérique virtuel de StorSimple, accédez à [étape 6 : créer un volume de](storsimple-deployment-walkthrough.md#step-6-create-a-volume), de [déployer votre périphérique de StorSimple local](storsimple-deployment-walkthrough.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installer un nouveau gestionnaire de capture instantanée StorSimple

Avant d’installer le Gestionnaire de snapshots StorSimple, assurez-vous que les volumes que vous avez créé sur le périphérique de la StorSimple ou StorSimple de périphérique virtuel monté, initialisées et mis en forme comme indiqué dans les [conditions de configuration requises](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Pour un périphérique virtuel de StorSimple, l’hôte doit être une Machine virtuelle de Microsoft Azure. 
>
>- L’hôte doit être en cours d’exécution pour Windows 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Si votre serveur exécute Windows Server 2008 R2, vous devez également installer le Service Pack 1 de Windows Server 2008 et de Windows Management Framework 3.0.
>
>- Vous devez configurer une connexion iSCSI de l’hôte vers le périphérique StorSimple avant de vous connecter le périphérique au Gestionnaire de capture instantanée StorSimple.

Suivez ces étapes pour effectuer une nouvelle installation du Gestionnaire de capture instantanée StorSimple. Si vous installez une mise à niveau, passez à le [de mise à niveau ou réinstallez le Gestionnaire de snapshots de StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Étape 1 : Installer le Gestionnaire de snapshots de StorSimple 
- Étape 2 : Se connecter StorSimple Gestionnaire de snapshots à un périphérique 
- Étape 3 : Vérifiez la connexion au périphérique 

###<a name="step-1-install-storsimple-snapshot-manager"></a>Étape 1 : Installer le Gestionnaire de snapshots de StorSimple

Utilisez les étapes suivantes pour installer le Gestionnaire de capture instantanée StorSimple.

#### <a name="to-install-storsimple-snapshot-manager"></a>Pour installer le Gestionnaire de capture instantanée StorSimple

1. Télécharger le logiciel Gestionnaire de snapshots StorSimple (accédez à [Gestionnaire de snapshots StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) dans du Microsoft Download Center) et de l’enregistrer en local sur l’hôte.

2. Dans l’Explorateur de fichiers, cliquez sur le dossier compressé, puis cliquez sur **extraire tous les**.

3. Dans la fenêtre **extraire (compressés) compressé** , dans la zone **Sélectionnez une destination et extraire les fichiers** , tapez ou recherchez le chemin d’accès où vous souhaitez extraire le fichier. 

       >[AZURE.IMPORTANT] Vous devez installer le Gestionnaire de snapshots de StorSimple sur le lecteur C:.
 
4. Activez la case à cocher **Afficher les fichiers extraits lorsque vous avez terminé** , puis cliquez sur **Extraire**.

    ![Extraire la boîte de dialogue de fichiers](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. Une fois l’extraction terminée, le dossier de destination s’ouvre. Double-cliquez sur l’icône de programme d’installation d’application qui apparaît dans le dossier de destination.

5. Lorsque le message **D’installation réussie** s’affiche, cliquez sur **Fermer**. Vous devriez voir l’icône du Gestionnaire de snapshots StorSimple sur votre bureau.

    ![icône du bureau](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Étape 2 : Se connecter StorSimple Gestionnaire de snapshots à un périphérique

Utilisez les étapes suivantes pour connecter des StorSimple Gestionnaire de snapshots à un périphérique StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Se connecter StorSimple Gestionnaire de snapshots à un périphérique

1. Cliquez sur l’icône Gestionnaire de snapshots StorSimple sur votre bureau. La fenêtre Gestionnaire de snapshots de StorSimple s’affiche. La fenêtre contient un volet **d’étendue** , un volet de **résultats** et un volet **d’Actions** . 

    ![Interface utilisateur du Gestionnaire de capture instantanée StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - Le volet de **l’arborescence** (volet gauche) contient une liste de nœuds organisés dans une structure arborescente. Vous pouvez développer certains noeuds pour sélectionner une vue ou les données spécifiques à ce nœud. Cliquez sur la flèche pour développer ou réduire un nœud. Cliquez sur un élément dans le volet de **l’arborescence** pour afficher la liste des actions disponibles pour cet élément. 

    - Le volet de **résultats** (le volet central) contient des informations d’état détaillées sur le nœud, vue ou que vous avez sélectionné dans le volet de **portée** .

    - Le volet **Actions** répertorie les opérations que vous pouvez effectuer sur le nœud, afficher ou les données que vous avez sélectionné dans le volet de **portée** .

    Pour une description complète de l’interface utilisateur de gestionnaire de snapshots StorSimple, voir [interface utilisateur du Gestionnaire de capture instantanée StorSimple](storsimple-use-snapshot-manager.md).

2. Dans le volet de **portée** , cliquez sur le nœud de **périphériques** et puis cliquez sur **configurer un périphérique**. La boîte de dialogue **configurer un périphérique** s’affiche.

    ![Configuration d’un périphérique](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. Dans la zone de liste **périphérique** , sélectionnez l’adresse IP du périphérique de Microsoft Azure StorSimple ou du périphérique virtuel. Dans la zone de texte **mot de passe** , tapez le mot de passe de gestionnaire de snapshots de StorSimple que vous avez créé pour le périphérique dans le portail classique Azure. Cliquez sur **OK**.

4. Gestionnaire de snapshots StorSimple recherche le périphérique que vous avez identifié. Si le périphérique est disponible, le Gestionnaire de snapshots StorSimple ajoute une connexion. Vous pouvez [vérifier la connexion au périphérique](#to-verify-the-connection) pour vérifier que la connexion a été ajoutée avec succès.

    Si le périphérique n’est pas disponible pour une raison quelconque, le Gestionnaire de snapshots StorSimple renvoie un message d’erreur. Cliquez sur **OK** pour fermer le message d’erreur, puis cliquez sur **Annuler** pour fermer la boîte de dialogue **configurer un périphérique** .

5. Lorsqu’il se connecte à un périphérique, Gestionnaire de snapshots StorSimple importe chaque groupe de volumes configuré pour ce périphérique, à condition que le groupe de volumes est associé à des sauvegardes. Les groupes de volumes qui n’ont pas de sauvegardes associées ne sont pas importés. En outre, les règles de sauvegarde qui ont été créés pour un groupe de volumes ne sont pas importées. Pour visualiser les groupes importés, droit sur le nœud **Groupes de volumes** de plus en haut dans le volet de **portée** , puis cliquez sur **Activer/désactiver importé des groupes**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Étape 3 : Vérifiez la connexion au périphérique

Utilisez les étapes suivantes pour vérifier que le Gestionnaire de snapshots StorSimple est connecté au périphérique StorSimple.

#### <a name="to-verify-the-connection"></a>Pour vérifier la connexion

1. Dans le volet de **l’arborescence** , cliquez sur le nœud **périphériques** .

    ![État du périphérique StorSimple Gestionnaire de snapshots](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. Vérifiez dans le volet de **résultats** : 

   - Si un indicateur vert s’affiche sur l’icône du périphérique et **disponible** s’affiche dans la colonne **statut** , le périphérique est connecté. 

   - Si un indicateur rouge apparaît sur l’icône du périphérique et non disponible s’affiche dans la colonne **statut** , le périphérique n’est pas connecté. 

   - Si **l’actualisation** s’affiche dans la colonne **état** , puis StorSimple Snapshot Manager récupère des groupes de volumes et les sauvegardes associées pour un périphérique connecté.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Mise à niveau ou réinstallez le Gestionnaire de capture instantanée StorSimple

Vous devez désinstaller StorSimple Gestionnaire de snapshots complètement avant de vous mettre à niveau ou réinstallez le logiciel. 

Avant de réinstaller le Gestionnaire de snapshots StorSimple, sauvegardez la base de données StorSimple Snapshot Manager existant sur l’ordinateur hôte. Il enregistre les informations de configuration et les stratégies de sauvegarde afin que vous pouvez facilement les restaurer ces données à partir de la sauvegarde.

Si vous êtes une mise à niveau ou réinstaller le Gestionnaire de snapshots StorSimple, procédez comme suit :

- Étape 1 : Désinstaller le Gestionnaire de snapshots de StorSimple 
- Étape 2 : Sauvegarder la base de données du Gestionnaire de snapshots de StorSimple 
- Étape 3 : Réinstallez le Gestionnaire de StorSimple instantané et restaurer la base de données 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Étape 1 : Désinstaller le Gestionnaire de snapshots de StorSimple

Utilisez les étapes suivantes pour désinstaller le Gestionnaire de capture instantanée StorSimple.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Pour désinstaller le Gestionnaire de capture instantanée StorSimple

1. Sur l’ordinateur hôte, ouvrez le **Panneau de configuration**, cliquez sur **programmes**, puis cliquez sur **programmes et fonctionnalités**.

2. Dans le volet gauche, cliquez sur **Désinstaller ou modifier un programme**.

3. Cliquez sur **Gestionnaire de snapshots StorSimple**, puis cliquez sur **désinstaller**.

4. Ceci démarre le programme d’installation du Gestionnaire de snapshots de StorSimple. Cliquez sur **Modifier le programme d’installation**, puis cliquez sur **désinstaller**.

    >[AZURE.NOTE] Si il n’y a aucun processus MMC ne fonctionne en arrière-plan, tels que le Gestionnaire de snapshots StorSimple ou gestion des disques, la désinstallation échoue et vous recevez un message pour fermer toutes les instances de MMC avant d’essayer de désinstaller le programme. Sélectionnez **Fermer automatiquement les applications et tenter de les redémarrer une fois l’installation terminée**, puis cliquez sur **OK**.
 
5. Lorsque la désinstallation est terminée, un message **D’installation réussie** s’affiche. Cliquez sur **Fermer**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Étape 2 : Sauvegarder la base de données du Gestionnaire de snapshots de StorSimple

Utilisez les étapes suivantes pour créer et enregistrer une copie de la base de données du Gestionnaire de snapshots de StorSimple.

#### <a name="to-back-up-the-database"></a>Pour sauvegarder la base de données

1. Arrêtez le Service de gestion de Microsoft StorSimple :

   1. Démarrez le Gestionnaire de serveur.

   2. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

   3. Dans la page **Services** , sélectionnez **Service de gestion de StorSimple de Microsoft**.

   4. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **Arrêter le service**.

        ![Arrêter le service Gestionnaire de StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData est un dossier masqué.

3. Rechercher le fichier de catalogue XML, copiez le fichier et stocker la copie dans un endroit sûr, ou dans le nuage.

    ![Fichier de catalogue de sauvegarde StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Redémarrez le Service de gestion de Microsoft StorSimple : 

    1. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    2. Dans la page **Services** , sélectionnez le **Service gestion de Microsoft StorSimple**e.

    3. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **redémarrer le service**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Étape 3 : Réinstallez le Gestionnaire de StorSimple instantané et restaurer la base de données

Pour réinstaller StorSimple Gestionnaire de snapshots, suivez les étapes de [l’installation d’un nouveau gestionnaire de capture instantanée StorSimple](#install-a-new-storsimple-snapshot-manager). Ensuite, utilisez la procédure suivante pour restaurer la base de données du Gestionnaire de snapshots de StorSimple.

#### <a name="to-restore-the-database"></a>Pour restaurer la base de données

1. Arrêtez le Service de gestion de Microsoft StorSimple :

    1. Démarrez le Gestionnaire de serveur.

    2. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    3. Dans la page **Services** , sélectionnez **Service de gestion de StorSimple de Microsoft**.

    4. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **Arrêter le service**.

2. Accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

     >[AZURE.NOTE] ProgramData est un dossier masqué.

3. Supprimer le fichier de catalogue XML et la remplacer par la version que vous avez enregistré précédemment.

4. Redémarrez le Service de gestion de Microsoft StorSimple : 

    1. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    2. Dans la page **Services** , sélectionnez **Service de gestion de StorSimple de Microsoft**.

    3. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **redémarrer le service**.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le Gestionnaire de snapshots StorSimple, accédez à [qu’est le Gestionnaire de snapshots StorSimple ?](storsimple-what-is-snapshot-manager.md).

- Pour en savoir plus sur l’interface utilisateur de gestionnaire de snapshots StorSimple, passez à [l’interface utilisateur du Gestionnaire de capture instantanée StorSimple](storsimple-use-snapshot-manager.md).

- Pour en savoir plus sur l’utilisation du Gestionnaire de snapshots StorSimple, accédez à [Gestionnaire de snapshots StorSimple utiliser pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
