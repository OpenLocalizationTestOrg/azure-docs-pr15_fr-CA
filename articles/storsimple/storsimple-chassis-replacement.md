<properties 
   pageTitle="Remplacez le châssis sur un périphérique StorSimple | Microsoft Azure"
   description="Décrit comment supprimer et remplacer le châssis de votre boîtier principal de StorSimple ou d’un boîtier EBOD."
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

# <a name="replace-the-chassis-on-your-storsimple-device"></a>Remplacez le châssis de votre périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment supprimer et remplacer un châssis dans un dispositif de série 8000 de StorSimple. Le modèle StorSimple 8100 est un périphérique de boîtier unique (un seul châssis), alors que la 8600 est un périphérique de boîtier double (deux châssis). Pour un modèle 8600, il existe potentiellement deux châssis susceptibles d’échouer dans l’unité : le châssis du boîtier principal ou le châssis pour le boîtier EBOD.

Dans les deux cas, le châssis de remplacement est fourni par Microsoft est vide. Aucun alimentation et Modules de ventilation (PCM) pour, modules de contrôleur, lecteurs de disque à l’état solide (SSD), des disques durs (HDD) ou des modules EBOD ne seront inclus.

>[AZURE.IMPORTANT] Avant le retrait et le remplacement du châssis, passez en revue les informations de sécurité dans le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-chassis"></a>Supprimer le châssis

Procédez comme suit pour supprimer le châssis sur le périphérique StorSimple.

#### <a name="to-remove-a-chassis"></a>Pour supprimer un châssis

1. Assurez-vous que le périphérique StorSimple est fermée et déconnecté de toutes les sources d’alimentation.

2. Retirez tout le réseau et les câbles SAS, le cas échéant.

3. Retirez l’unité de rack.

4. Supprimez tous les lecteurs et notez les emplacements à partir desquels ils sont supprimés. Pour plus d’informations, reportez-vous à la section [Supprimer le lecteur de disque](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. Sur le boîtier EBOD (si c’est le châssis qui a échoué), supprimez les modules de contrôleur EBOD. Pour plus d’informations, consultez [Supprimer un contrôleur de EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 

    Sur le boîtier principal (si c’est le châssis qui a échoué), supprimez les contrôleurs et notez les emplacements à partir desquels ils sont supprimés. Pour plus d’informations, consultez [Supprimer un contrôleur](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installer le châssis

Procédez comme suit pour installer le châssis sur le périphérique StorSimple.

#### <a name="to-install-a-chassis"></a>Pour installer un châssis

1. Montez le châssis sur le rack. Pour plus d’informations, consultez [votre périphérique StorSimple 8100 montable en Rack](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [votre périphérique StorSimple 8600 montable en Rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Une fois que le châssis est monté dans le rack, installez les modules de contrôleur dans les mêmes positions qu’elles ont été précédemment installés dans.

3. Installer les disques dans les mêmes positions et qu’ils ont été précédemment installés dans des emplacements.

    >[AZURE.NOTE] Nous vous recommandons que vous installez d’abord les SSDs dans les emplacements, puis installez les disques durs.

2. Avec le dispositif monté dans le rack et les composants sont installés, votre périphérique de se connecter aux sources d’alimentation et allumez le périphérique. Pour plus d’informations, consultez le [câble de votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).

