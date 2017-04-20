<properties 
   pageTitle="Remplacer un contrôleur StorSimple EBOD | Microsoft Azure"
   description="Explique comment supprimer et remplacer un ou deux contrôleurs EBOD sur un périphérique StorSimple 8600."
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

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Remplacer un contrôleur EBOD sur votre périphérique de StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment remplacer un module de contrôleur défectueux EBOD sur votre périphérique Microsoft Azure StorSimple. Pour remplacer un module du contrôleur EBOD, vous devez :

- Retirez le contrôleur défectueux de la EBOD
- Installer un nouveau contrôleur EBOD

Avant de commencer, prenez en compte les informations suivantes :

- Modules EBOD vides doivent être insérés dans tous les emplacements inutilisés. Le boîtier ne sera pas refroidir correctement si un emplacement n’est pas ouvert.

- Le contrôleur de EBOD est remplaçables à chaud et peut être supprimé ou remplacé. Ne retirez pas un module défaillant jusqu'à ce que vous ayez un remplacement. Lorsque vous lancez le processus de remplacement, vous devez le terminer dans les 10 minutes.

>[AZURE.IMPORTANT] Avant de tenter de supprimer ou de remplacer tout composant de StorSimple, assurez-vous que vous passez en revue les [conventions des icônes de sécurité](storsimple-safety.md#safety-icon-conventions) et autres [précautions de sécurité](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Supprimer un contrôleur de EBOD

Avant de remplacer le module de contrôleur EBOD Échec de votre périphérique StorSimple, assurez-vous que l’autre module de contrôleur EBOD est actif et en cours d’exécution. La procédure et le tableau suivants expliquent comment faire pour supprimer le module de contrôleur EBOD.

#### <a name="to-remove-an-ebod-module"></a>Pour supprimer un module EBOD

1. Ouvrez le portail classique Azure.

2. Accédez à des **périphériques** > **Maintenance** > **État du matériel**et vérifiez que l’état du voyant du module de contrôleur active EBOD est vert et que le voyant du module de contrôleur EBOD ayant échoué est rouge.

3. Repérez le module de contrôleur EBOD a échoué à l’arrière de l’appareil.

4. Retirez les câbles qui connectent le module de contrôleur EBOD vers le contrôleur avant de prendre le module EBOD du système.

5. Prenez note du port SAS exacte du module contrôleur EBOD qui a été connecté au contrôleur. Vous devez restaurer le système à cette configuration après avoir remplacé le module EBOD. 

    >[AZURE.NOTE] En général, il s’agit d’un Port, qui porte le nom **d’hôte dans** le diagramme suivant.

    ![Contrôleur de fond de panier de EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figure 1** À l’arrière du module EBOD

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|VOYANT de défaillance|
  	|2|VOYANT d’alimentation|
  	|3|Connecteurs SAS|
  	|4|Voyants SAS|
  	|5|Ports série pour utilisation en usine uniquement|
  	|6|Port (hôte dans)|
  	|7|Le port B (hôte out)|
  	|8|Port C (utilisation en usine uniquement)|

## <a name="install-a-new-ebod-controller"></a>Installer un nouveau contrôleur EBOD

La procédure et le tableau suivants expliquent comment installer un module de contrôleur EBOD de votre périphérique StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Pour installer un contrôleur de EBOD

1. Vérifiez le périphérique EBOD ne sont pas endommagés, en particulier le connecteur de l’interface. Si certaines broches sont tordues, n’installez pas le nouveau contrôleur de EBOD.

2. Avec les taquets en position ouverte, faites glisser le module dans le boîtier jusqu'à ce que les loquets s’enclenchent.

    ![L’installation du contrôleur de EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figure 2**  L’installation du module de contrôleur EBOD

3. Fermez le loquet. Vous devez entendre un clic comme le loquet s’engage.

    ![Libération du verrou EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figure 3**  Fermer le verrou du module EBOD

4. Reconnectez les câbles. Utilisez la configuration exacte qui était présente avant le remplacement. Consultez le diagramme et le tableau pour plus d’informations sur la façon de connecter les câbles suivants.

    ![Câble de votre périphérique de 4U d’alimentation](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **La figure 4**. Reconnexion des câbles

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|Boîtier principal|
  	|2|PCM 0|
  	|3|PCM 1|
  	|4|Contrôleur 0|
  	|5|Contrôleur 1|
  	|6|Contrôleur de EBOD 0|
  	|7|Contrôleur de EBOD 1|
  	|8|Boîtier EBOD|
  	|9|Unités de Distribution d’alimentation|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).
