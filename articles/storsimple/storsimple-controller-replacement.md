<properties 
   pageTitle="Remplacement d’un contrôleur de périphérique StorSimple | Microsoft Azure"
   description="Explique comment supprimer et remplacer un ou deux modules de contrôleur sur votre périphérique StorSimple."
   services="storsimple"
   documentationCenter=""
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

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Remplacement d’un module de contrôleur sur votre périphérique de StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment supprimer et remplacer un ou deux modules de contrôleur dans un dispositif de StorSimple. Il aborde également la logique sous-jacente pour les scénarios de remplacement de contrôleur simple et double.

>[AZURE.NOTE] Avant d’effectuer un remplacement de contrôleur, nous recommandons que vous toujours mettre à jour le microprogramme de votre contrôleur à la version la plus récente.
>
>Pour éviter d’endommager votre appareil StorSimple, ne retirez pas le contrôleur jusqu'à ce que les voyants sont affichés sous forme d’une des opérations suivantes :
>
>- Les lumières sont OFF.
>- VOYANT 3, ![icône de coche verte](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), et ![icône de croix rouge](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) clignotent, et le voyant 0 et voyant 7 **ON**.

Le tableau suivant présente les scénarios de remplacement de contrôleur pris en charge.

|Cas|Scénario de remplacement|Procédure applicable|
|:---|:-------------------|:-------------------|
|1|Un contrôleur est en état d’échec, l’autre contrôleur est active et saine.|[Remplacement d’un contrôleur unique](#replace-a-single-controller), qui décrit la [logique derrière un remplacement d’un contrôleur unique](#single-controller-replacement-logic), ainsi que les [étapes de remplacement](#single-controller-replacement-steps).|
|2|Les deux contrôleurs ont échoué et le remplacement. Le châssis, les disques, boîtier de and.disk sont en bon état.|[Remplacement du contrôleur double](#replace-both-controllers), qui décrit la [logique derrière un remplacement de deux contrôleurs](#dual-controller-replacement-logic), ainsi que les [étapes de remplacement](#dual-controller-replacement-steps). |
|3|Contrôleurs du même périphérique ou à partir de différents périphériques sont permutées. Les châssis, les disques et les boîtiers de disques sont en bon état.|Un message d’alerte incompatibilité emplacement s’affiche.|
|4|Un contrôleur est manquant, et l’autre contrôleur échoue.|[Remplacement du contrôleur double](#replace-both-controllers), qui décrit la [logique derrière un remplacement de deux contrôleurs](#dual-controller-replacement-logic), ainsi que les [étapes de remplacement](#dual-controller-replacement-steps).|
|5|Un ou deux contrôleurs ont échoué. Vous ne pouvez pas accéder au périphérique via la console série ou d’accès distant de Windows PowerShell.|[Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour une procédure de remplacement du dispositif de commande manuel.|
|6|Les contrôleurs ont une version de build différent, qui peut être dû à :<ul><li>Contrôleurs ont une version logicielle différente.</li><li>Contrôleurs ont une version de firmware différentes.</li></ul>|Si les versions du logiciel contrôleur sont différentes, la logique de remplacement qui détecte et met à jour de la version du logiciel sur le contrôleur de remplacement.<br><br>Si la version du firmware du contrôleur est différente et que l’ancienne version du microprogramme est **pas** automatiquement mise à niveau, un message d’alerte apparaît dans le portail classique Azure. Vous devez rechercher les mises à jour et installer les mises à jour du firmware.</br></br>Si la version du firmware du contrôleur est différente, et l’ancienne version du firmware est automatiquement mis à niveau, la logique de remplacement du contrôleur permettra de le détecter, et après que le contrôleur démarre, le micrologiciel sera mis à jour automatiquement.|

Vous devez supprimer un module de contrôleur si elle a échoué. Un ou deux modules contrôleur peuvent échouer, ce qui peut entraîner un remplacement d’un contrôleur unique ou le remplacement de deux contrôleurs. Pour les procédures de remplacement et la logique sous-jacente, consultez les rubriques suivantes :

- [Remplacer un seul contrôleur](#replace-a-single-controller)
- [Remplacez les deux contrôleurs](#replace-both-controllers)
- [Supprimer un contrôleur](#remove-a-controller)
- [Insérer un contrôleur](#insert-a-controller)
- [Identifiez le contrôleur actif sur le périphérique](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Avant le retrait et le remplacement d’un contrôleur, passez en revue les informations de sécurité dans le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## <a name="replace-a-single-controller"></a>Remplacer un seul contrôleur

Lorsqu’un des deux contrôleurs sur le périphérique Microsoft Azure StorSimple a échoué, fonctionne mal ou est manquant, vous devez remplacer un seul contrôleur. 

### <a name="single-controller-replacement-logic"></a>Logique de remplacement d’un contrôleur unique

Dans le remplacement d’un contrôleur unique, vous devez tout d’abord supprimer le contrôleur défaillant. (Le contrôleur restant dans le périphérique est le contrôleur actif.) Lorsque vous insérez le contrôleur de remplacement, les actions suivantes se produisent :

1. Le contrôleur de remplacement démarre immédiatement la communication avec le périphérique StorSimple.

2. Une capture instantanée d’un disque dur virtuel (VHD) pour le contrôleur actif est copiée sur le contrôleur de remplacement.

3. La capture instantanée est modifiée afin que lorsque le contrôleur de remplacement démarre à partir de ce disque dur virtuel, il sera reconnu comme contrôleur de secours.

4. Lorsque les modifications sont terminées, le contrôleur de remplacement démarre comme le contrôleur de secours.

5. Lorsque les deux contrôleurs sont en cours d’exécution, le cluster est en ligne.

### <a name="single-controller-replacement-steps"></a>Étapes de remplacement d’un contrôleur unique

Si un des contrôleurs de votre périphérique Microsoft Azure StorSimple échoue, procédez comme suit. (L’autre contrôleur doit être actif et en cours d’exécution. Si les deux contrôleurs de défaillance ou mauvais fonctionnement, passez aux [étapes de remplacement de contrôleur double](#dual-controller-replacement-steps).)

>[AZURE.NOTE] Il peut prendre 30 à 45 minutes le contrôleur redémarrer et pour récupérer complètement la procédure de remplacement d’un contrôleur unique. La durée totale de l’ensemble de la procédure, y compris l’attachement des câbles, est d’environ 2 heures.

#### <a name="to-remove-a-single-failed-controller-module"></a>Pour supprimer un module unique contrôleur défaillant

1. Dans le portail classique Azure, accéder au Gestionnaire de StorSimple, cliquez sur l’onglet **périphériques** et puis cliquez sur le nom du périphérique que vous souhaitez surveiller.

2. Accédez à **Maintenance > état du matériel**. Le statut de contrôleur 0 ou 1 du contrôleur doit être rouge, qui indique un échec.

    >[AZURE.NOTE] Le contrôleur défaillant dans un remplacement d’un contrôleur unique est toujours un contrôleur de secours.

3. Utilisez les Figure 1 et le tableau suivants de localiser le module contrôleur défaillant.  

    ![Fond de panier de modules de boîtier principal de périphérique](./media/storsimple-controller-replacement/IC740994.png)

    **Figure 1** À l’arrière du dispositif de StorSimple

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Contrôleur 0|
  	|4|Contrôleur 1|

4. Sur le contrôleur défectueux, retirez tous les câbles réseau connecté les ports de données. Si vous utilisez un modèle 8600, également supprimer les câbles SAS du contrôleur de se connectent au contrôleur de EBOD.

5. Suivez les étapes de [suppression d’un contrôleur](#remove-a-controller) pour supprimer le contrôleur a échoué. 

6. Installez le remplacement en usine dans le même emplacement que celui à partir duquel le contrôleur défaillant a été supprimé. Cela déclenche la logique de remplacement d’un contrôleur unique. Pour plus d’informations, consultez [logique de remplacement d’un contrôleur unique](#single-controller-replacement-logic).

7. Alors que la logique de remplacement d’un contrôleur unique progresse en arrière-plan, reconnectez les câbles. Veillez à connecter tous les câbles exactement de la même façon qu’ils étaient connectés avant le remplacement.

8. Après le redémarrage, le contrôleur de vérifier l' **état du contrôleur** et l' **état du Cluster** dans le portail classique Azure pour vérifier que le contrôleur est dans un état sain et qu’il est en mode veille.

>[AZURE.NOTE] Si vous analysez le périphérique par l’intermédiaire de la console série, vous pouvez voir plusieurs redémarrages pendant que le contrôleur est de récupérer à partir de la procédure de remplacement. Lorsque le menu de console série est présenté, vous savez que le remplacement est terminé. Si le menu n’apparaît pas dans les deux heures de démarrage le remplacement du contrôleur, veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md).

## <a name="replace-both-controllers"></a>Remplacez les deux contrôleurs

Lorsque les deux contrôleurs sur le périphérique Microsoft Azure StorSimple ont échoué, sont mal ou sont manquantes, vous devez remplacer les deux contrôleurs. 

### <a name="dual-controller-replacement-logic"></a>Logique de remplacement de contrôleur double

Dans un remplacement de deux contrôleurs, vous supprimez d’abord les deux contrôleurs a échoué et insérez des remplacements. Lorsque les contrôleurs de deux remplacement sont insérés, les actions suivantes se produisent :

1. Le contrôleur de remplacement à l’emplacement 0 vérifie les points suivants :
 
   1. Il utilise la version actuelle du firmware et du logiciel ?

   2. Il est une partie du cluster ?

   3. Le contrôleur d’homologue en cours d’exécution et il en cluster ?
                            
    Si aucune de ces conditions sont remplies, le contrôleur recherche la dernière sauvegarde quotidienne (située dans le **nonDOMstorage** sur le lecteur S). Le contrôleur de copie la dernière capture instantanée d’un disque dur virtuel à partir de la sauvegarde.

2. Le contrôleur dans l’emplacement 0 utilise la capture instantanée vers l’image elle-même.

3. Pendant ce temps, le contrôleur dans l’emplacement 1 attend de contrôleur 0 pour terminer l’imagerie et la démarrer.

4. Après le démarrage du contrôleur 0, contrôleur 1 détecte le cluster créé par contrôleur 0, qui déclenche la logique de remplacement d’un contrôleur unique. Pour plus d’informations, consultez [logique de remplacement d’un contrôleur unique](#single-controller-replacement-logic).

5. Par la suite, exécutera les deux contrôleurs et le cluster sera mise en ligne.

>[AZURE.IMPORTANT] Suite à un remplacement à contrôleur double, après avoir configuré le périphérique StorSimple, il est essentiel de prendre un manuel de sauvegarde du périphérique. Sauvegardes quotidiennes de la configuration de périphérique ne sont pas déclenchés qu’après que 24 heures se sont écoulées. Fonctionne avec la [Prise en charge de Microsoft](storsimple-contact-microsoft-support.md) pour rendre un manuel de sauvegarde de votre périphérique.

### <a name="dual-controller-replacement-steps"></a>Procédure de remplacement de contrôleur double

Ce flux de travail est nécessaire lorsque les deux contrôleurs de votre périphérique Microsoft Azure StorSimple ont échoué. Cela peut se produire dans un centre de données dans laquelle le système de refroidissement cesse de fonctionner, et par conséquent, les deux contrôleurs échouent dans un laps de temps. Selon que le périphérique StorSimple est ou non activé, et si vous utilisez un 8600 ou un modèle 8100, un autre ensemble d’étapes est nécessaire.

>[AZURE.IMPORTANT] Il peut prendre 45 minutes à 1 heure par le contrôleur redémarrer et récupérer entièrement à partir d’une procédure de remplacement du contrôleur double. La durée totale de l’ensemble de la procédure, y compris l’attachement des câbles, est d’environ 2,5 heures.

#### <a name="to-replace-both-controller-modules"></a>Pour remplacer les deux modules de contrôleur

1. Si le périphérique est désactivé, ignorez cette étape et passez à l’étape suivante. Si le périphérique est activé, désactivez le périphérique.
                                        
    1. Si vous utilisez un modèle 8600, le boîtier principal préalable désactiver et ensuite désactiver le boîtier EBOD.

    2. Attendez que le périphérique a été arrêté complètement. Tous les voyants à l’arrière de l’appareil est déconnecté.

2. Retirez tous les câbles réseau sont connectés aux ports de données. Si vous utilisez un modèle 8600, également supprimer les câbles SAS qui connectent le boîtier principal pour le boîtier EBOD.

3. Retirez les deux contrôleurs de périphérique StorSimple. Pour plus d’informations, consultez [Supprimer un contrôleur](#remove-a-controller).

4. Insérez d’abord le remplacement de la fabrique de contrôleur 0 et insérez le contrôleur 1. Pour plus d’informations, voir [Insérer un contrôleur](#insert-a-controller). Cela déclenche la logique de remplacement de deux contrôleurs. Pour plus d’informations, consultez [logique de remplacement de deux contrôleurs](#dual-controller-replacement-logic).

5. Alors que la logique de remplacement du contrôleur progresse en arrière-plan, reconnectez les câbles. Veillez à connecter tous les câbles exactement de la même façon qu’ils étaient connectés avant le remplacement. Consultez les instructions détaillées de votre modèle dans le câble de votre section de périphériques [d’installer votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md) ou [d’installer votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md).

6. Activer le périphérique StorSimple. Si vous utilisez un modèle 8600 :

    1. Assurez-vous que le boîtier EBOD est activé sur le premier.

    2. Patientez jusqu'à ce que le boîtier EBOD est en cours d’exécution.

    3. Allumez le boîtier principal.

    4. Une fois le premier contrôleur redémarre et est dans un état sain, le système exécutera.

    >[AZURE.NOTE] Si vous analysez le périphérique par l’intermédiaire de la console série, vous pouvez voir plusieurs redémarrages pendant que le contrôleur est de récupérer à partir de la procédure de remplacement. Lorsque le menu de la console série s’affiche, vous savez que le remplacement est terminé. Si le menu n’apparaît pas dans les 2,5 heures de démarrage le remplacement du contrôleur, veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md).

## <a name="remove-a-controller"></a>Supprimer un contrôleur

Utilisez la procédure suivante pour supprimer un module de contrôleur défectueux de votre périphérique StorSimple.

>[AZURE.NOTE] Les illustrations suivantes sont pour contrôleur 0. Contrôleur 1, ceux-ci seront inversés.

#### <a name="to-remove-a-controller-module"></a>Pour supprimer un module de contrôleur

1. Saisissez le verrou du module entre le pouce et l’index.

2. Appuyez doucement sur le pouce et l’index afin de libérer le verrou de contrôleur.

    ![Libération du verrou de contrôleur](./media/storsimple-controller-replacement/IC741047.png)

    **Figure 2** Libération du verrou de contrôleur

2. Utiliser le loquet en tant que handle sur le contrôleur du châssis de la diapositive.

    ![Contrôleur coulissant de châssis](./media/storsimple-controller-replacement/IC741048.png)

    **Figure 3** Glissement du contrôleur du châssis

## <a name="insert-a-controller"></a>Insérer un contrôleur

Utilisez la procédure suivante pour installer un module fourni par les usines de contrôleur après la suppression d’un module défaillant à partir de votre périphérique StorSimple.

#### <a name="to-install-a-controller-module"></a>Pour installer un module de contrôleur

1. Vérifiez s’il existe des dommages sur les connecteurs d’interface. N’installez pas le module si les broches du connecteur sont ENDOMMAGES ou fausses.

2. Insérez le module de contrôleur dans le châssis, pendant que le verrou est libéré entièrement. 

    ![Contrôleur décalée dans le châssis](./media/storsimple-controller-replacement/IC741053.png)

    **Figure 4** Glisser de contrôleur dans le châssis

3. Avec le module de contrôleur inséré, commencer à fermer le loquet tout en maintenant le vers le module de contrôleur dans le châssis. Le loquet de s’engager guider le contrôleur en place.

    ![Fermeture du loquet du contrôleur](./media/storsimple-controller-replacement/IC741054.png)

    **Figure 5** Fermeture du loquet du contrôleur

4. Vous avez terminé lorsque le loquet s’enclenche. Le voyant **OK** doit apparaître.  

    >[AZURE.NOTE] Il peut prendre jusqu'à 5 minutes pour le contrôleur et le voyant à activer.

5. Pour vérifier que le remplacement est réussi, dans le portail classique Azure, accédez à des **périphériques** > **Maintenance** > **État du matériel**et assurez-vous que les contrôleurs 0 et 1 sont en bon état (état est vert).

## <a name="identify-the-active-controller-on-your-device"></a>Identifiez le contrôleur actif sur le périphérique

Il existe de nombreuses situations, comme le remplacement de l’enregistrement ou de contrôleur de périphérique de la première fois, vous obligeant à localiser le contrôleur actif sur un périphérique StorSimple. Le contrôleur actif traite toutes les opérations du microprogramme et de mise en réseau disque. Vous pouvez utiliser une des méthodes suivantes pour identifier le contrôleur actif :

- [Le portail classique Azure permet d’identifier le contrôleur actif](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Utiliser Windows PowerShell pour StorSimple pour identifier le contrôleur actif](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Vérifiez le périphérique physique pour identifier le contrôleur actif](#check-the-physical-device-to-identify-the-active-controller)

Chacune de ces procédures est décrite ci-après.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Le portail classique Azure permet d’identifier le contrôleur actif

Dans le portail classique Azure, accédez aux **périphériques** > **Maintenance**et faites défiler jusqu'à la section **contrôleurs** . Ici, vous pouvez vérifier quel contrôleur est actif.

![Identifier un contrôleur active dans Azure portal classique](./media/storsimple-controller-replacement/IC752072.png)

**Figure 6** Azure portal classique montrant le contrôleur actif

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utiliser Windows PowerShell pour StorSimple pour identifier le contrôleur actif

Lorsque vous accédez à votre appareil via la console série, un message de bannière est présenté. Le message de bannière contient des informations de base des périphériques tels que le nom, la version du logiciel installée et modèle état du contrôleur, vous accédez à. L’image suivante montre un exemple d’un message de bannière :

![Message de bannière de série](./media/storsimple-controller-replacement/IC741098.png)

**Figure 7** Contrôleur d’affichage de message 0 comme Active la bannière

Vous pouvez utiliser le message de bannière pour déterminer si le contrôleur que vous êtes connecté est actif ou passif.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Vérifiez le périphérique physique pour identifier le contrôleur actif

Pour identifier le contrôleur actif sur votre périphérique, recherchez le voyant bleu au-dessus du port de données 5 à l’arrière du boîtier principal.

Si ce voyant clignote, le contrôleur est actif et l’autre contrôleur est en mode veille. Utilisez le diagramme et le tableau suivants sous la forme d’une aide.

![Fond de panier périphérique principal de boîtier avec dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figure 8** À l’arrière du boîtier principal avec les ports de données et les voyants de surveillance

|Étiquette|Description|
|:----|:----------|
|1-6|DONNÉES de 0 à 5 ports de réseau|
|7|VOYANT bleu|


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).
