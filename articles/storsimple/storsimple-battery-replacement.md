<properties 
   pageTitle="Remplacer la batterie d’un appareil de StorSimple | Microsoft Azure"
   description="Décrit comment supprimer et remplacer le module de batterie de secours sur votre appareil de StorSimple de mettre à jour."
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

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Remplacez le module de batterie de secours de votre périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Le boîtier principal d’alimentation et refroidissement Module (PCM) sur votre appareil Microsoft Azure StorSimple a un pack batterie supplémentaire. Ce pack offre la puissance afin que le périphérique StorSimple pouvez enregistrer des données en cas de perte d’alimentation du boîtier principal. Cette batterie est appelé *module de batterie de secours*. Le module de batterie de secours existe uniquement pour le boîtier principal de votre périphérique StorSimple (le boîtier EBOD ne contient-elle pas un module de batterie de secours). 

Ce didacticiel explique comment :

- Retirez le module de batterie de secours 
- Installer un nouveau module de batterie de secours
- Gérer le module de batterie de secours

>[AZURE.IMPORTANT] Avant le retrait et le remplacement d’un module de batterie de secours, passez en revue les informations de sécurité dans l' [Introduction au remplacement du composant matériel StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-backup-battery-module"></a>Retirez le module de batterie de secours

Le module de batterie de secours de votre périphérique StorSimple est une unité remplaçable. Avant son installation dans le PCM, le module de batterie doit être stocké dans son emballage d’origine. Procédez comme suit pour supprimer de la batterie de secours.

#### <a name="to-remove-the-backup-battery-module"></a>Pour supprimer le module de batterie de secours

1. Dans le portail classique Azure, accédez à des **périphériques** > **Maintenance** > **État du matériel**. Sous **Composants partagés**, examinez l’état de la batterie.

2. Identifier le PCM dans lequel la batterie a échoué. La figure 1 illustre l’arrière de l’unité StorSimple.

    ![Fond de panier de Modules de boîtier principal de périphérique](./media/storsimple-battery-replacement/IC740994.png)

    **Figure 1** Verso du périphérique principal montrant les modules PCM et contrôleur

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Contrôleur 0|
  	|4|Contrôleur 1|

    Comme indiqué par le numéro 3 dans la Figure 2, la surveillance voyant sur PCM 0 qui correspond à une **Panne de batterie** doit être allumé.

    ![Fond de panier de périphérique PCM surveillance des voyants](./media/storsimple-battery-replacement/IC740992.png)

    **Figure 2** Retour de PCM affichant l’indicateur de surveillance voyants

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|Panne d’alimentation CA|
  	|2|Panne du ventilateur|
  	|3|Panne de batterie|
  	|4|PCM OK|
  	|5|Panne de courant continu|
  	|6|Batterie en bon état|

3. Pour supprimer le PCM avec une batterie défaillante, suivez les étapes de [suppression d’un PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Le PCM est supprimé, soulever et faire pivoter la poignée du module batterie vers le haut comme indiqué dans la figure suivante et tirez dessus à supprimer de la batterie.

    ![Retrait de la batterie à partir de GCL](./media/storsimple-battery-replacement/IC741019.png)

    **Figure 3** Retrait de la batterie à partir du PCM

5. Insérez le module dans l’unité remplaçable d’emballage.

6. Renvoyer l’unité défectueuse à Microsoft pour la bonne gestion et de maintenance.

## <a name="install-a-new-backup-battery-module"></a>Installer un nouveau module de batterie de secours

Procédez comme suit pour installer le module de batterie de rechange dans le PCM dans le boîtier principal de votre périphérique StorSimple.

#### <a name="to-install-the-battery-module"></a>Pour installer le module de batterie

1. Placez le module de batterie de secours dans l’orientation appropriée dans le PCM.

2. Appuyez sur la poignée de module de batterie sur le connecteur de siège.

3. Remplacez le PCM dans le boîtier principal en suivant les instructions dans la zone [Remplacer une alimentation et un Module de votre périphérique StorSimple](storsimple-power-cooling-module-replacement.md).

4. Une fois le remplacement terminé, accédez à des **périphériques** > **Maintenance** > **État du matériel** dans Azure portal classique. Vérifiez l’état de la batterie pour vous assurer que l’installation a réussi. Un état vert indique que la batterie est en bon état.

## <a name="maintain-the-backup-battery-module"></a>Gérer le module de batterie de secours

De votre périphérique StorSimple, le module de batterie de secours alimente le contrôleur au cours d’un événement de perte d’alimentation. Il permet le périphérique StorSimple enregistrer les données critiques avant d’arrêter d’une façon contrôlée. Avec deux batteries complètement chargées dans les PCMs, le système peut gérer deux événements de pertes consécutives.

Dans le portail Azure classique, l' **État du matériel** , sur la page **Maintenance** indique si la batterie fonctionne mal ou est proche de sa fin de vie. L’état de la batterie est indiqué par la **batterie en PCM 0** ou **batterie dans PCM 1** sous **Composants partagés**. Cette page affiche un état **dégradé** approche de la fin de vie et **Échec** de fin de vie atteinte. 

>[AZURE.NOTE] La batterie peut signaler **l’Échec** lorsqu’il doit simplement être facturés.
 
Si l’état **DEGRADED** s’affiche, nous vous recommandons de cours d’action suivant :

- Le système a peut-être rencontré une perte de puissance récents ou les piles peuvent être en cours de maintenance périodique. Observez le système pendant 12 heures avant de continuer.

    - Si l’état est toujours **dégradé** après 12 heures de connexion continue à courant alternatif avec les contrôleurs et les PCMs en cours d’exécution, puis la batterie doit être remplacée. Veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour un module de batterie de secours de remplacement.

    - Si l’état est OK après les 12 heures, la batterie est opérationnelle et il nécessaire uniquement les frais de maintenance.

- Si aucune n’a été une entraîne une perte de courant et le PCM est allumé et connecté à l’alimentation CA, la batterie doit être remplacée. [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour commander un module de batterie de secours de remplacement.

>[AZURE.IMPORTANT] Dispose de la batterie défaillante conformément aux réglementations nationales et régionales. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).
