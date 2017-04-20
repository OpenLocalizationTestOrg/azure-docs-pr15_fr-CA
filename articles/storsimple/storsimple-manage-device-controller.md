<properties
   pageTitle="Gérer les contrôleurs de périphérique StorSimple | Microsoft Azure"
   description="Découvrez comment arrêter, redémarrer, arrêter ou réinitialiser vos contrôleurs de périphérique StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>Gérer vos contrôleurs de périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit les différentes opérations qui peuvent être effectuées sur vos contrôleurs de périphérique StorSimple. Les contrôleurs de votre périphérique StorSimple sont les contrôleurs redondants (homologue) dans une configuration active-passive. À un moment donné, un seul contrôleur est actif et traite toutes les opérations de réseau et de disque. L’autre contrôleur est en mode passif. Si le contrôleur actif échoue, le contrôleur passif devient automatiquement actif.

Ce didacticiel comprend des instructions pour gérer les contrôleurs de périphérique en utilisant le :

- Section des **contrôleurs** de la page de **gestion** dans le service Gestionnaire de StorSimple
- Windows PowerShell pour StorSimple.

Nous recommandons de gérer les contrôleurs de périphérique via le service Gestionnaire de StorSimple. Si une action ne peut être effectuée à l’aide de Windows PowerShell pour StorSimple, le didacticiel permet une note de celui-ci.

Après avoir lu ce didacticiel, vous pourrez :

- Redémarrer ou arrêter un contrôleur de matériel StorSimple
- Arrêter un périphérique StorSimple
- Restaurez votre périphérique StorSimple par défaut


## <a name="restart-or-shut-down-a-single-controller"></a>Redémarrer ou arrêter à un seul contrôleur

Un arrêt ou un redémarrage du contrôleur n’est pas nécessaire dans le cadre d’un fonctionnement normal du système. Les opérations d’arrêt pour un contrôleur de périphérique unique sont communes que dans les cas où un composant matériel de périphérique défaillant doit être remplacé. Redémarrage d’un contrôleur peut également être nécessaire dans une situation dans laquelle les performances sont affectées par l’utilisation de mémoire excessive ou un dysfonctionnement du contrôleur. Vous devez également redémarrer un contrôleur après le remplacement d’un contrôleur réussi, si vous souhaitez activer et tester le contrôleur de remplacement.

Le redémarrage d’un périphérique n’est pas perturber les initiateurs connectés, en supposant que le contrôleur passif est disponible. Si un contrôleur passif n’est pas disponible ou activé désactivé, puis redémarrer le contrôleur actif peut entraîner l’interruption de service et des temps d’arrêt.

> [AZURE.IMPORTANT]

> - **Un contrôleur en cours d’exécution ne doit jamais être supprimé physiquement que cela donne lieu à une perte de redondance et un risque accru de temps d’arrêt.**

> - La procédure suivante s’applique uniquement au périphérique physique StorSimple. Pour plus d’informations sur la façon de démarrer, d’arrêter et de redémarrer le périphérique virtuel, voir [travail avec le périphérique virtuel](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).

Vous pouvez redémarrer ou arrêter à un contrôleur de périphérique unique à l’aide du portail Azure classique du service Gestionnaire de StorSimple ou de Windows PowerShell pour StorSimple

Pour gérer vos contrôleurs de périphérique à partir du portail classique Azure, effectuez les opérations suivantes.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Pour redémarrer ou arrêter un contrôleur de portail classique

1. Naviguez jusqu'à **périphériques > Maintenance**.

1. État du **Matériel** , vérifiez que le statut des deux contrôleurs sur votre périphérique est **Sain**.

    ![Vérifiez StorSimple contrôleurs de périphérique sont en bon état](./media/storsimple-manage-device-controller/IC766017.png)

1. À partir du bas de la page de **Maintenance** , cliquez sur **Gérer les contrôleurs**.

    ![Gérer les contrôleurs de périphérique StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] Si vous ne voyez pas **Gérer les contrôleurs**, vous devez installer les mises à jour. Pour plus d’informations, consultez [mise à jour de votre périphérique StorSimple](storsimple-update-device.md).

1. Dans la boîte de dialogue **Modifier les paramètres du contrôleur** , effectuez le des opérations suivantes :
    1. Dans la liste déroulante **Sélectionner un contrôleur** , sélectionnez le contrôleur que vous souhaitez gérer. Les options sont le contrôleur 0 et contrôleur 1. Ces contrôleurs sont également identifiées comme actives ou passives.

        >[AZURE.NOTE] Un contrôleur ne peut être géré si elle est non disponible ou activé désactivé et il n’apparaît pas dans la liste déroulante.

    2. Dans la liste déroulante **Sélectionner l’Action** , choisissez **redémarrer le contrôleur** d’ou **Arrêter le contrôleur**.

        ![Redémarrez le contrôleur de StorSimple dispositif passif](./media/storsimple-manage-device-controller/IC766020.png)
    3. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-device-controller/IC740895.png).

Cela redémarrer ou arrêter le contrôleur. Le tableau ci-dessous résume les détails de ce qui se passe en fonction des sélections effectuées dans la boîte de dialogue **Modifier les paramètres du contrôleur** .  


|Sélection #|Si vous choisissez de...|Cela se produit.|
|---|---|---|
|1.|Redémarrez le contrôleur passif.|Une tâche sera créée pour redémarrer le contrôleur, et vous serez averti lorsque le projet est créé avec succès. Ceci va démarrer le redémarrage du contrôleur. Vous pouvez surveiller le processus de redémarrage en accédant à **Service > tableau de bord > Afficher les journaux d’opération** puis en filtrant par les paramètres spécifiques à votre service.|
|2.|Redémarrez le contrôleur actif.|L’avertissement suivant s’affiche : « Si vous redémarrez le contrôleur actif, le périphérique échouera sur le contrôleur passif. Voulez-vous continuer ? » </br>Si vous choisissez de procéder à cette opération, les étapes suivantes seront identiques à celles utilisées pour redémarrer le contrôleur passif (voir Sélection de 1).|
|3.|Arrêtez le contrôleur passif.|Vous verrez le message suivant : « après l’arrêt est terminée, vous devez appuyez sur le bouton d’alimentation sur votre contrôleur afin de l’activer. Êtes-vous sûr de que vouloir arrêter ce contrôleur ? » </br>Si vous choisissez de procéder à cette opération, les étapes suivantes seront identiques à celles utilisées pour redémarrer le contrôleur passif (voir Sélection de 1).|
|4.|Arrêtez le contrôleur actif.|Vous verrez le message suivant : « après l’arrêt est terminée, vous devez appuyez sur le bouton d’alimentation sur votre contrôleur afin de l’activer. Êtes-vous sûr de que vouloir arrêter ce contrôleur ? » </br>Si vous choisissez de procéder à cette opération, les étapes suivantes seront identiques à celles utilisées pour redémarrer le contrôleur passif (voir Sélection de 1).|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Pour redémarrer ou arrêter un contrôleur de Windows PowerShell pour StorSimple
Procédez comme suit pour arrêter ou redémarrer un contrôleur unique sur votre périphérique de StorSimple à partir du portail classique Azure.


1. Accéder au périphérique à l’aide de la console série ou une session telnet à partir d’un ordinateur distant. Connectez-vous au contrôleur de 0 ou de contrôleur 1 en suivant les étapes de [PuTTY utiliser pour se connecter à la console série du périphérique](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. Dans le menu de la console série, choisissez l’option 1, **Connectez-vous à l’aide d’un accès complet**.

1. Dans le message de bannière, notez le contrôleur que vous êtes connecté (contrôleur 0 ou 1 du contrôleur) et s’il est actif ou le passif contrôleur (en attente).
    - Pour arrêter un contrôleur unique, à l’invite, tapez :

        `Stop-HcsController`

        Cela va arrêter le contrôleur auquel vous êtes connecté. Si vous arrêtez le contrôleur actif, puis il échouera sur le contrôleur passif avant son arrêt.
    - Pour redémarrer un contrôleur, à l’invite, tapez :

        `Restart-HcsController`

        Cela redémarre le contrôleur auquel vous êtes connecté. Si vous redémarrez le contrôleur actif, il échouera sur le contrôleur passif avant le redémarrage.


## <a name="shut-down-a-storsimple-device"></a>Arrêter un périphérique StorSimple

Cette section explique comment arrêter une exécution ou un dispositif de StorSimple a échoué à partir d’un ordinateur distant. Un périphérique est désactivé après l’arrêt à la fois les contrôleurs de périphérique. Un arrêt de périphérique est effectué lorsque le périphérique est physiquement déplacé ou est mis hors service.

> [AZURE.IMPORTANT] Avant d’arrêter le périphérique, vérifiez l’état de santé des composants du périphérique. Naviguez jusqu'à **périphériques > Maintenance > Statut matériel** et vérifiez que l’état de tous les composants du voyant est vert. Un périphérique sain aura l’état vert. Si votre périphérique est en cours d’arrêt vers le bas pour remplacer un composant ne fonctionne pas correctement, vous verrez un échec (rouge) ou un état dégradé (jaune) pour les composants respectifs.

#### <a name="to-shut-down-a-storsimple-device"></a>Pour arrêter un périphérique StorSimple

1. Utilisez la procédure de [redémarrer ou d’arrêter un contrôleur](#restart-or-shut-down-a-single-controller) d’identifier et d’arrêter le contrôleur passif sur votre périphérique. Vous pouvez effectuer cette opération dans le portail classique Azure ou dans Windows PowerShell pour StorSimple.
2. Répétez l’étape ci-dessus pour arrêter le contrôleur actif.
3. Vous devrez maintenant examiner le plan arrière de l’appareil. Après que les deux contrôleurs sont arrêter complètement vers le bas, les voyants d’état sur les deux contrôleurs doit être clignotant rouge. Si vous devez désactiver le périphérique à ce stade, retourner d’alimentation et de ventilation des Modules (PCM pour) les interrupteurs d’alimentation sur la position arrêt. Ceci doit désactiver le périphérique.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Réinitialisation du dispositif aux paramètres par défaut

> [AZURE.IMPORTANT] Si vous souhaitez rétablir les paramètres par défaut votre périphérique, contactez le Support Microsoft. La procédure décrite ci-dessous doit être utilisée qu’en conjonction avec le Support de Microsoft.

Cette procédure explique comment rétablir les paramètres par défaut à l’aide de Windows PowerShell pour StorSimple votre périphérique Microsoft Azure StorSimple.
Réinitialiser un dispositif supprime toutes les données et les paramètres de l’ensemble du cluster par défaut.

Effectuez les opérations suivantes pour rétablir les paramètres par défaut votre périphérique Microsoft Azure StorSimple :

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Pour rétablir le périphérique par défaut dans Windows PowerShell pour StorSimple

1. Accéder au périphérique via la console série. Vérifiez le message de bannière pour vous assurer que vous êtes connecté au contrôleur Active.

1. Dans le menu de la console série, choisissez l’option 1, **Connectez-vous à l’aide d’un accès complet**.

1. À l’invite, tapez la commande suivante pour réinitialiser l’intégralité du cluster, suppression de tous les paramètres du contrôleur, les métadonnées et les données :

    `Reset-HcsFactoryDefault`

    Pour réinitialiser les un seul contrôleur au lieu de cela, utilisez l’applet de commande [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) avec la `-scope` paramètre.)

    Le système va redémarrer plusieurs fois. Vous serez averti lorsque la réinitialisation est réussie. Selon le modèle du système, il peut prendre des 45 à 60 minutes pour un dispositif 8100 et 60 à 90 minutes un 8600 terminer ce processus.

    > [AZURE.TIP]

    > - Si vous utilisez la mise à jour 1.2 ou antérieure utiliser le `–SkipFirmwareVersionCheck` paramètre d’ignorer la vérification de la version du microprogramme (dans le cas contraire, vous verrez une erreur d’incompatibilité du microprogramme : usine par défaut ne peut pas continuer en raison d’une incompatibilité dans les versions de firmware. ).

    > - La procédure de réinitialisation d’usine peut échouer pour les périphériques StorSimple qui n’exécutent pas la mise à jour 1 ou 1.1 dans le portail de l’administration et ont effectué un remplacement des contrôleur simple ou double réussi (avec des contrôleurs de remplacement qui ont été livrés avec le logiciel de mise à Pre-jour 1). Cela se produit lorsque le factory reset image est validé pour la présence d’un fichier de SHA1 sur le contrôleur qui n’existe pas pour le logiciel de 1 avant mise à jour. Si vous voyez que cette fabrique réinitialiser échec, contactez le Support de Microsoft pour vous aider dans les étapes suivantes. Ce problème ne se produit pas avec les contrôleurs de remplacement qui ont été expédiés depuis l’usine avec la mise à jour 1 ou ultérieure du logiciel.


## <a name="questions-and-answers-about-managing-device-controllers"></a>Questions et réponses sur la gestion des contrôleurs de périphérique

Dans cette section, nous avons résumé de certaines des questions plus fréquemment posées concernant la gestion des contrôleurs de périphérique StorSimple.

**Q.** Que se passe-t-il si à la fois les contrôleurs sur mon périphérique sont en bonne santé et activés sur et je redémarrer ou arrêter le contrôleur actif ?

**A.** Si à la fois les contrôleurs sur votre périphérique sont en bonne santé et activés, vous êtes invité à confirmer. Vous pouvez choisir de :

- **Redémarrez le contrôleur actif** – vous serez averti que le redémarrage d’un contrôleur active provoquera le périphérique basculer vers le contrôleur passif. Le contrôleur va redémarrer.

- **Arrêter un contrôleur actif** – vous serez averti qu’arrêter un contrôleur active va entraîner un arrêt. Vous devrez également appuyez sur le bouton d’alimentation sur le périphérique à activer sur le contrôleur.

**Q.** Que se passe-t-il si le contrôleur passif sur mon périphérique est indisponible ou hors tension et je redémarrer ou arrêter le contrôleur actif ?

**A.** Si le contrôleur passif sur votre périphérique est indisponible ou activé désactivé, et vous souhaitez :

- **Redémarrez le contrôleur actif** – vous serez averti que poursuivre l’opération entraîne une interruption temporaire du service, et vous êtes invité à confirmer.

- **Arrêter un contrôleur actif** – vous serez averti que poursuivre l’opération entraînera dans les interruptions de service, et que vous deviez appuyez sur le bouton d’alimentation sur un ou deux contrôleurs pour activer le périphérique. Vous serez invité à confirmer.

**Q.** Lorsque est le contrôleur redémarrage ou l’arrêt échoue à progresser ?

**A.** Redémarrer ou arrêter à un contrôleur peut échouer si :

- Une mise à jour de périphérique est en cours.

- Redémarrage d’un contrôleur est déjà en cours.

- Un arrêt de contrôleur est déjà en cours.

**Q.** Comment pouvez vous déterminer si un contrôleur a été redémarré ou arrêté ?

**A.** Vous pouvez vérifier l’état du contrôleur dans la page Maintenance. L’état du contrôleur indique si un contrôleur a été redémarré ou arrêté. En outre, la page alertes contient une alerte d’information si le contrôleur a été redémarré ou arrêté. Les opérations de redémarrage et d’arrêt du contrôleur sont également enregistrées dans les journaux de l’opération. Pour plus d’informations sur les journaux de l’opération, accédez à [Afficher les journaux de l’opération](storsimple-service-dashboard.md#view-the-operations-logs).

**Q.** Y a-t-il un réel impact sur les e/s à la suite de basculement de contrôleurs ?

**A.** Les connexions TCP entre les initiateurs et contrôleur active sont réinitialisées à la suite de basculement sur incident du contrôleur, mais seront rétablies lorsque le contrôleur passif suppose l’opération. Il existe peut-être une pause temporaire (moins de 30 secondes) dans l’activité d’e/s entre des initiateurs et le périphérique au cours de cette opération.

**Q.** Comment pour retourner mon contrôleur de service après que qu’il a été arrêté et supprimé ?

**A.** Pour restaurer un contrôleur de service, vous devez l’insérer dans le châssis, comme décrit dans le [remplacement d’un module de contrôleur sur votre périphérique StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez des problèmes avec vos contrôleurs de périphérique StorSimple que vous ne pouvez pas résoudre en appliquant les procédures décrites dans ce didacticiel, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md).

- Pour plus d’informations sur l’utilisation du service de gestionnaire de StorSimple, accédez à [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
