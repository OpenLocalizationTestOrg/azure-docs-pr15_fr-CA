<properties 
   pageTitle="Gérer les périphériques avec le Gestionnaire de snapshots StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC du Gestionnaire de snapshots StorSimple, pour vous connecter et gérer les périphériques de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Gestionnaire de capture instantanée StorSimple permet de se connecter et de gérer les périphériques de StorSimple

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser des nœuds dans le volet Gestionnaire de snapshots de StorSimple **étendue** pour vérifier les données importées de périphérique StorSimple et actualiser les périphériques de stockage connectés. En outre, lorsque vous cliquez sur le nœud **périphériques** , vous pouvez voir une liste des périphériques connectés et les informations d’état correspondantes dans le volet de **résultats** .

![Périphériques connectés](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figure 1 : Gestionnaire de snapshots de StorSimple connecté périphérique** 

En fonction de vos sélections dans la **vue** , le volet **résultats** affiche les informations suivantes sur chaque périphérique. (Pour plus d’informations sur la configuration d’une vue, accédez au [menu Affichage](storsimple-use-snapshot-manager.md#view-menu).


| Colonne de résultats  |Description          |
|:----------------|:--------------------| 
| Nom            | Le nom du périphérique tel que configuré dans Azure portal classique|
| Modèle           | Le numéro de modèle du périphérique|
| Version         | La version du logiciel installé sur le périphérique |
| État          | Si le périphérique est disponible |
| Dernière synchronisation     | Date et heure lors de la dernière synchronisation de l’appareil |
| Numéro de série.      | Le numéro de série du périphérique |
 
Si vous cliquez sur le nœud de **périphériques** dans le volet de **portée** , vous pouvez sélectionner parmi les actions suivantes :

- Ajouter ou remplacer un périphérique 
- Connectez un périphérique et vérifier les importations 
- Actualiser les périphériques connectés 

Si vous cliquez sur le nœud de **périphériques** , puis cliquez sur un nom de périphérique dans le volet de **résultats** , vous pouvez sélectionner parmi les actions suivantes :

- Authentifier un périphérique 
- Afficher les détails du périphérique 
- Actualiser un périphérique 
- Supprimer une configuration de périphérique 
- Modifier un mot de passe de périphérique

>[AZURE.NOTE] Toutes ces actions sont également disponibles dans le volet **Actions** .
 
Ce didacticiel explique comment utiliser le Gestionnaire de snapshots StorSimple pour se connecter et gérer les périphériques et effectuer les tâches suivantes :

- Ajouter ou remplacer un périphérique 
- Connectez un périphérique et vérifier les importations 
- Actualiser les périphériques connectés 
- Authentifier un périphérique 
- Afficher les détails du périphérique 
- Actualiser un périphérique individuel 
- Supprimer une configuration de périphérique 
- Modifier un mot de passe expiré
- Remplacer un périphérique défaillant

>[AZURE.NOTE] Pour obtenir des informations générales sur l’utilisation de l’interface du Gestionnaire de snapshots StorSimple, accédez à [l’interface utilisateur du Gestionnaire de capture instantanée StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Ajouter ou remplacer un périphérique

Utilisez la procédure suivante pour ajouter ou remplacer un périphérique StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Pour ajouter ou remplacer un périphérique

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **portée** , cliquez sur le nœud de **périphériques** et puis cliquez sur **configurer un périphérique**. La boîte de dialogue **configurer un périphérique** s’affiche.

    ![Configurer un périphérique de StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. Dans la zone de liste déroulante **unité** , sélectionnez l’adresse IP du périphérique ou du périphérique virtuel. 

4. Dans la zone de texte **mot de passe** , tapez le mot de passe de gestionnaire de snapshots de StorSimple que vous avez créé pour le périphérique dans le portail classique Azure. Cliquez sur **OK**. Gestionnaire de snapshots StorSimple recherche le périphérique que vous avez identifié. 

    - Si le périphérique est disponible, le Gestionnaire de snapshots StorSimple ajoute une connexion. 

    - Si le périphérique n’est pas disponible pour une raison quelconque, le Gestionnaire de snapshots StorSimple renvoie un message d’erreur. Cliquez sur **OK** pour fermer le message d’erreur, puis cliquez sur **Annuler** pour fermer la boîte de dialogue **configurer un périphérique** .

## <a name="connect-a-device-and-verify-imports"></a>Connectez un périphérique et vérifier les importations

Utilisez la procédure suivante pour connecter un dispositif de StorSimple et de vérifier que les groupes de volumes existants associés à des sauvegardes sont importés.

#### <a name="to-connect-a-device-and-verify-imports"></a>Pour connecter un périphérique et vérifier les importations

1. Pour connecter un périphérique à StorSimple Gestionnaire de snapshots, suivez les instructions dans Ajouter ou remplacer un périphérique. Lorsqu’il se connecte à un périphérique, Gestionnaire de snapshots StorSimple répond comme suit :

    - Si le périphérique n’est pas disponible pour une raison quelconque, le Gestionnaire de snapshots StorSimple renvoie un message d’erreur. 

   - Si le périphérique est disponible, le Gestionnaire de snapshots StorSimple ajoute une connexion. Lorsque vous sélectionnez le périphérique, il s’affiche dans le volet de **résultats** et le champ d’état indique que le périphérique est **disponible**. Gestionnaire de snapshots StorSimple importe les groupes de volumes configurés pour le périphérique, à condition que les groupes de volumes ont associé des sauvegardes. Stratégies de sauvegarde ne sont pas importés. Les groupes de volumes qui n’ont pas de sauvegardes associées ne sont pas importés.

2. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

3. Cliquez sur le nœud supérieur dans le volet de **l’arborescence** , puis cliquez sur **Importations bascule l’affichage**.

    ![Affichage des importations de sélection bascule](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. La boîte de dialogue **Importation bascule l’affichage** s’affiche, montrant le statut de vos sauvegardes et les groupes de volumes importés. Cliquez sur **OK**. 

Après que les groupes de volumes et les sauvegardes sont correctement importées, vous pouvez utiliser le Gestionnaire de snapshots StorSimple pour les gérer, comme vous gère les groupes de volumes et de sauvegardes que vous avez créé et configuré avec le Gestionnaire de snapshots StorSimple. 

## <a name="refresh-connected-devices"></a>Actualiser les périphériques connectés

Utilisez la procédure suivante pour synchroniser les périphériques connectés StorSimple avec le Gestionnaire de snapshots StorSimple.

####<a name="to-refresh-connected-devices"></a>Pour actualiser les périphériques connectés

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , cliquez droit sur des **périphériques**, puis cliquez sur **Actualiser les périphériques**. Cette opération synchronise les périphériques connectés avec le Gestionnaire de snapshots StorSimple afin que vous puissiez afficher les groupes de volumes et les sauvegardes, y compris les ajouts récents. 

    ![Actualiser les périphériques StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
L’action **Actualiser les périphériques** récupère les nouveaux groupes de volumes et les sauvegardes associées à partir de périphériques connectés. Contrairement à action **analyser les volumes** disponible pour le nœud de **Volumes** , **Périphériques d’actualisation** ne restaure pas le Registre de sauvegarde.

## <a name="authenticate-a-device"></a>Authentifier un périphérique

Utilisez la procédure suivante pour authentifier un dispositif de StorSimple avec le Gestionnaire de snapshots StorSimple.

#### <a name="to-authenticate-a-device"></a>Pour authentifier un dispositif

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , cliquez sur **périphériques**.

3. Dans le volet de **résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **authentification**.

4. La boîte de dialogue **d’authentification** s’affiche. Tapez le mot de passe, puis cliquez sur **OK**.

    ![Authentifier la boîte de dialogue](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>Afficher les détails du périphérique

La procédure suivante permet d’afficher les détails d’un dispositif de StorSimple et, le cas échéant, resynchroniser le périphérique avec le Gestionnaire de capture instantanée StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Permet d’afficher et de resynchroniser les détails du périphérique

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , cliquez sur **périphériques**.

3. Dans le volet de **résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **Détails**. 

4 **Détails du périphérique de** la boîte de dialogue s’affiche. Cette zone affiche le nom de modèle, version, numéro de série, état, cible iSCSI nom qualifié (IQN) et dernière synchronisation date et l’heure. 

   - Cliquez sur **resynchroniser** pour synchroniser le périphérique.

   - Cliquez sur **OK** ou sur **Annuler** pour fermer la boîte de dialogue.

    ![Détails du périphérique](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>Actualiser un périphérique individuel

La procédure suivante permet de resynchroniser un périphérique avec le Gestionnaire de snapshots StorSimple StorSimple.

#### <a name="to-refresh-a-device"></a>Pour actualiser un périphérique

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , cliquez sur **périphériques**. 

3. Dans le volet de **résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **Actualiser le périphérique**. Cette opération synchronise le périphérique avec le Gestionnaire de snapshots StorSimple. 

## <a name="delete-a-device-configuration"></a>Supprimer une configuration de périphérique

Utilisez la procédure suivante pour supprimer une configuration de périphérique StorSimple individuelle à partir du Gestionnaire de capture instantanée StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Pour supprimer une configuration de périphérique

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , cliquez sur **périphériques**. 

3. Dans le volet de **résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **Supprimer**. 

4. Le message suivant s’affiche. Cliquez sur **Oui** pour supprimer la configuration ou sur **non** pour annuler la suppression.

    ![Supprimer la configuration de périphérique](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Modifier un mot de passe expiré

Vous devez entrer un mot de passe pour authentifier un dispositif de StorSimple avec le Gestionnaire de snapshots StorSimple. Vous configurez ce mot de passe lorsque vous utilisez l’interface de Windows PowerShell pour configurer le périphérique. Toutefois, le mot de passe peut expirer. Dans ce cas, vous pouvez utiliser le portail classique Azure pour modifier le mot de passe. Puis, parce que l’unité a été configurée dans le Gestionnaire de snapshots StorSimple avant l’expiration du mot de passe, vous devez ré-authentifier le périphérique dans le Gestionnaire de snapshots StorSimple. 

#### <a name="to-change-the-expired-password"></a>Pour modifier le mot de passe expiré

1. Dans le portail classique Azure, démarrez le service de gestionnaire de StorSimple.

2. Cliquez sur **périphériques** > **configurer** le périphérique.

3. Faites défiler jusqu'à la section Gestionnaire de snapshots de StorSimple. Entrez un mot de passe de 14 à 15 caractères. Assurez-vous que le mot de passe contienne un mélange de caractères majuscules, minuscules, numériques et spéciaux.

4. Entrez à nouveau le mot de passe pour le confirmer.

5. Cliquez sur **Enregistrer** en bas de la page.

#### <a name="to-re-authenticate-the-device"></a>Pour ré-authentifier le périphérique

1. Démarrez le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , cliquez sur **périphériques**. Une liste des périphériques configurés s’affiche dans le volet de **résultats** . 

3. Sélectionnez le périphérique, cliquez du bouton droit, puis cliquez sur **authentification**.

4. Dans la fenêtre **d’authentification** apparaît, entrez le nouveau mot de passe. 

5. Sélectionnez le périphérique, cliquez du bouton droit et sélectionnez **Actualiser périphérique**. Cette opération synchronise le périphérique avec le Gestionnaire de snapshots StorSimple. 

## <a name="replace-a-failed-device"></a>Remplacer un périphérique défaillant

Si un périphérique StorSimple échoue et est remplacé par un dispositif de secours (basculement), procédez comme suit pour connecter le nouveau périphérique et afficher les sauvegardes associées.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Se connecter à un nouveau périphérique après basculement

1. Reconfigurer la connexion iSCSI vers le nouveau périphérique. Pour obtenir des instructions, consultez « étape 7 : montage, d’initialisation et de format d’un volume » dans [déployer votre périphérique de StorSimple local](storsimple-deployment-walkthrough-u2.md). 

>[AZURE.NOTE] Si le nouveau dispositif de StorSimple a la même adresse IP que l’ancien, vous pourrez peut-être vous connecter à l’ancienne configuration. 

2. Arrêtez le Service de gestion de Microsoft StorSimple :

    1. Démarrez le Gestionnaire de serveur.

    2. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**. 

    3. Dans la fenêtre **Services** , sélectionnez le **Service de gestion de StorSimple de Microsoft**. 

    4. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **Arrêter le service**. 

3. Supprimer les informations de configuration relatives à l’ancien périphérique : 

    1. Dans l’Explorateur de fichiers, accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Supprimez les fichiers dans le dossier BACatalog. 

4. Redémarrez le Service de gestion de Microsoft StorSimple : 

    1. Sur le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**. 

    2. Dans la fenêtre **Services** , sélectionnez le **Service de gestion de StorSimple de Microsoft**. 

    3. Dans le volet droit, sous le **Service de gestion de StorSimple de Microsoft**, cliquez sur **redémarrer le service**. 

5. Démarrez le Gestionnaire de capture instantanée StorSimple. 

6. Pour configurer le nouveau périphérique StorSimple, suivez les étapes dans l’étape 2 : connectez un périphérique StorSimple dans le [Gestionnaire de Snapshot StorSimple de déployer](storsimple-snapshot-manager-deployment.md). 

7. Cliquez sur le nœud de niveau supérieur dans le volet de **portée** (Gestionnaire de snapshots StorSimple dans l’exemple), puis cliquez sur **Importations bascule l’affichage**. 

8. Un message s’affiche lorsque les groupes de volumes importés et les sauvegardes sont visibles dans le Gestionnaire de capture instantanée StorSimple. Cliquez sur **OK**. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire de snapshots StorSimple pour afficher et gérer des volumes](storsimple-snapshot-manager-manage-volumes.md).

