<properties 
   pageTitle="Remplacer un PCM sur le périphérique StorSimple | Microsoft Azure"
   description="Explique comment supprimer et remplacer la puissance et le refroidissement Module (PCM) sur votre périphérique de StorSimple"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Remplacer un Module de refroidissement et de puissance sur le périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

La puissance et le refroidissement Module (PCM) de votre périphérique Microsoft Azure StorSimple se compose d’un bloc d’alimentation et des ventilateurs qui sont contrôlés par le principal et les boîtiers EBOD. Il n'existe qu’un seul modèle de PCM qui est certifiée pour chaque boîtier. Le boîtier principal est certifié pour un PCM W 764 et le boîtier EBOD est certifié pour un PCM W 580. Bien que les PCMs pour le boîtier principal et le boîtier EBOD sont différents, la procédure de remplacement est identique.

Ce didacticiel explique comment :

- Supprimer un PCM
- Installez un remplacement PCM

>[AZURE.IMPORTANT] Avant le retrait et le remplacement d’un PCM, passez en revue les informations de sécurité dans le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## <a name="before-you-replace-a-pcm"></a>Avant de remplacer un PCM

Être conscient des points suivants avant de remplacer votre PCM :

- En cas de défaillance de l’alimentation de la PCM, laissez le module défectueux, mais supprimer le cordon d’alimentation. Le ventilateur continue à alimenter le boîtier et continuer à assurer le refroidissement. Si le ventilateur échoue, le PCM doit être immédiatement remplacés.

- Avant de supprimer le PCM, débranchez l’alimentation du PCM en désactivant le commutateur principal (le cas échéant) ou en retirer le cordon d’alimentation. Cela fournit un avertissement à votre système qu’une mise hors tension est imminente.

- Assurez-vous que l’autre PCM est fonctionnel pour le fonctionnement du système continue avant de remplacer le PCM défectueux. Un PCM défectueux doit être remplacé par un PCM pleinement opérationnel dès que possible.

- Remplacement des modules PCM ne prend que quelques minutes, mais il doit être terminé dans les 10 minutes de la suppression du PCM échec pour empêcher la surchauffe.

- Notez que les modules PCM W 764 remplacement usine ne contiennent pas le module de batterie de secours. Vous devez retirer la batterie à partir de votre GCL défectueux et puis l’insérer dans le module de remplacement avant d’effectuer le remplacement. Pour plus d’informations, consultez Comment [Supprimer et d’insérer un module de batterie de secours](storsimple-battery-replacement.md).


## <a name="remove-a-pcm"></a>Supprimer un PCM

Suivez ces instructions lorsque vous êtes prêt à supprimer une alimentation et un Module de refroidissement (PCM) à partir de votre périphérique Microsoft Azure StorSimple.

>[AZURE.NOTE] Avant de supprimer votre PCM, vérifiez que vous disposez d’un remplacement correcte (764 W pour le boîtier principal) ou 580 W pour le boîtier EBOD.

#### <a name="to-remove-a-pcm"></a>Pour supprimer un PCM

1. Dans le portail Azure classique, cliquez sur **périphériques** > **Maintenance** > **État du matériel**. Vérifier l’état des composants PCM sous **Composants partagés** pour identifier PCM a échoué :

     - Si un bloc d’alimentation dans le PCM, 0 a échoué, l’état de **L’alimentation dans PCM 0** sera rouge.

     - Si un bloc d’alimentation dans le PCM 1 a échoué, l’état de **l’Alimentation dans PCM 1** est rouge.

     - Si le ventilateur dans PCM 1 a échoué, le statut de **refroidissement 0 pour PCM 0** ou **1 de refroidissement pour PCM 0** sera rouge.

2. Recherchez le PCM a échoué à l’arrière du boîtier principal. Si vous exécutez un modèle 8600, identifier le boîtier principal dans le numéro d’Identification système unitaire indiqué sur le panneau avant affichage SCD. L’ID d’unité est affiché sur le boîtier principal par défaut est **00**, alors que l’ID d’unité est affiché sur le boîtier de la EBOD par défaut est **01**. Le diagramme et les tableaux suivants expliquent le panneau avant de l’affichage SCD.

    ![ID de système sur la façade OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **Figure 1** Panneau avant de l’appareil  

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|Bouton Muet|
  	|2|Puissance du système|
  	|3|Défaillance du module|
  	|4|Erreur de logique|
  	|5|Affichage de l’identifiant unitaire|

3. L’indicateur de surveillance voyants arrière du boîtier principal peut également servir à identifier le PCM défectueux. Consultez le diagramme et le tableau de comprendre comment utiliser les voyants pour localiser le PCM défectueux suivants. Par exemple, si le voyant correspondant à **Défaillance du ventilateur** est allumé, le ventilateur a échoué. De même, si le voyant correspondant à un **Échec de CA** est allumé, le bloc d’alimentation a échoué. 

    ![Fond de panier de périphérique PCM surveillance voyants](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **Figure 2** À l’arrière du PCM avec voyants

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|Panne d’alimentation CA|
  	|2|Panne du ventilateur|
  	|3|Panne de batterie|
  	|4|PCM OK|
  	|5|Panne de courant continu|
  	|6|Batterie en bon état|

4. Reportez-vous au diagramme suivant de l’arrière de l’unité StorSimple de localiser le module PCM a échoué. PCM 0 se trouve sur la gauche et PCM 1 se trouve à droite. Le tableau qui suit décrit les modules.

     ![Fond de panier de modules de boîtier principal de périphérique](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **Figure 3** À l’arrière du dispositif avec des modules plug-in 

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Contrôleur 0|
  	|4|Contrôleur 1|

5. Désactiver le PCM défectueux et déconnectez le cordon d’alimentation. Vous pouvez maintenant supprimer le PCM.

6. Saisissez le loquet et le côté de la poignée de PCM entre le pouce et l’index et ajustez les regrouper ensemble pour ouvrir la poignée.

    ![Ouverture du Handle PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **Figure 4** Ouverture de la poignée de PCM

7. Attrapez la poignée et supprimer le PCM.

    ![Suppression d’un périphérique PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **Figure 5** Suppression du PCM

## <a name="install-a-replacement-pcm"></a>Installez un remplacement PCM

Suivez les instructions pour installer un PCM de votre périphérique StorSimple. Vérifiez que vous avez inséré le module de batterie de sauvegarde avant d’installer le remplacement PCM (s’applique à 764 PCMs W uniquement). Pour plus d’informations, consultez Comment [Supprimer et d’insérer un module de batterie de secours](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Pour installer un PCM

1. Vérifiez que vous disposez du PCM de remplacement correcte pour cette pièce jointe. Le boîtier principal doit un PCM W 764 et le boîtier EBOD un PCM W 580. Vous devriez pas à utiliser le PCM W 580 dans le boîtier principal ou le PCM W 764 dans l’enceinte EBOD. L’image suivante montre où identifier cette information sur l’étiquette apposée sur le PCM.

    ![Étiquette du périphérique PCM](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **Figure 6** Étiquette PCM

2. Vérifiez si le boîtier, en accordant une attention particulière aux connecteurs. 
                                        
    >[AZURE.NOTE] **N’installez pas le module si certaines broches du connecteur sont tordues.**

3. Avec la poignée de PCM en position ouverte, faites glisser le module dans le boîtier.

    ![L’installation du périphérique PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **Figure 7** L’installation du PCM

4. Fermer manuellement la poignée PCM. Vous devez entendre un clic comme le verrou handle s’engage. 
                                        
    >[AZURE.NOTE] Pour vous assurer que les broches du connecteur ont engagé, vous pouvez bar doucement sur la poignée de sans libérer le verrou. Si le PCM a coulisse, cela implique que le verrou a été fermé avant des connecteurs.

5. Connectez les câbles d’alimentation de la source d’alimentation et du PCM.

6. Sécuriser la déformation des balles de déchirure. 

7. Allumez le PCM.

8. Vérifiez si le remplacement a réussi : dans le portail Azure classique de votre service de gestionnaire de StorSimple, accédez aux **périphériques** > **Maintenance** > **État du matériel**. Sous **Composants partagés**, l’état de la PCM doit être vert. 
                                        
    >[AZURE.NOTE] Il peut prendre quelques minutes pour le remplacement PCM s’initialiser complètement.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).
