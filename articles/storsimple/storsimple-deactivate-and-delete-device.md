<properties 
   pageTitle="Désactiver et supprimer un périphérique StorSimple | Microsoft Azure"
   description="Décrit comment supprimer des StorSimple dispositif de service par désactiver tout d’abord, puis leur suppression."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>Désactiver et supprimer un périphérique de StorSimple

## <a name="overview"></a>Vue d’ensemble

Vous voudrez peut-être prendre un périphérique StorSimple hors service (par exemple, si vous remplacez ou mise à niveau de votre périphérique ou si vous n’utilisez plus StorSimple). Si c’est le cas, vous devez désactiver le périphérique avant que vous pouvez le supprimer. Désactivation rompt la connexion entre le périphérique et le service de gestionnaire de StorSimple correspondant. Ce didacticiel explique comment supprimer un périphérique StorSimple service en commençant par la désactiver, puis leur suppression. 

Lorsque vous désactivez un périphérique, toutes les données qui ont été stockées localement sur le périphérique ne sera plus accessibles. Uniquement les données associées au périphérique qui ont été stocké dans le nuage peuvent être récupérées.  

>[AZURE.WARNING] La désactivation est définitive et ne peut pas être annulée. Un périphérique désactivé ne peut pas être enregistré avec le service Gestionnaire de StorSimple sauf si elle est tout d’abord réinitialisée par la fabrique. 
>
>La fabrique de réinitialise le processus supprime toutes les données qui a été stockées localement sur votre périphérique. Par conséquent, il est essentiel de prendre un instantané de nuage de toutes vos données avant de désactiver un périphérique. Cela vous permettra de récupérer toutes les données à un stade ultérieur.

Ce didacticiel explique comment :

- Désactiver un périphérique et de supprimer des données
- Désactiver un périphérique et de conserver les données

Il explique également comment la désactivation et suppression fonctionne sur un périphérique virtuel StorSimple.

>[AZURE.NOTE] Avant de désactiver un périphérique physique ou virtuel de StorSimple, assurez-vous d’arrêter ou de supprimer des clients et des hôtes qui dépendent de ce périphérique.

## <a name="deactivate-and-delete-data"></a>Désactiver et supprimer des données

Si vous souhaitez supprimer entièrement le périphérique et que vous ne souhaitez pas conserver les données sur le périphérique, puis procédez comme suit.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Pour désactiver le périphérique et supprimer les données  

1. Avant la désactivation d’un périphérique, vous devez supprimer le volume de tous les conteneurs (et les volumes associés au périphérique). Vous pouvez supprimer des conteneurs de volume uniquement après avoir supprimé les sauvegardes associées.

2. Désactiver le périphérique comme suit :

    1. Sur la page de **périphériques** du service Gestionnaire de StorSimple, sélectionnez le périphérique que vous souhaitez désactiver, puis, en bas de la page, cliquez sur **désactiver**.

    2. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer. Le processus de désactivation démarre et prendre quelques minutes.

3. Après la désactivation, vous pouvez supprimer complètement le périphérique. Suppression d’un périphérique supprime de la liste des périphériques connectés au service. Le service puis ne peut plus gérer le périphérique supprimé. Utilisez les étapes suivantes pour supprimer le périphérique :

    1. Sur la page de **périphériques** du service Gestionnaire de StorSimple, sélectionnez un périphérique désactivé que vous souhaitez supprimer.

    2. En bas de la page, cliquez sur **Supprimer**.

    3. Vous serez invité à confirmer. Cliquez sur **Oui** pour continuer.

    Il peut prendre quelques minutes pour le périphérique à supprimer.

## <a name="deactivate-and-retain-data"></a>Désactiver et conserver les données

Si vous intéressent dans la suppression du périphérique mais que vous souhaitez conserver les données, puis procédez comme suit.

####<a name="to-deactivate-a-device-and-retain-the-data"></a>Pour désactiver un périphérique et de conserver les données 

1. Désactiver le périphérique. Tous les conteneurs de volume et les instantanés du périphérique sont conservés.

    1. Sur la page de **périphériques** du service Gestionnaire de StorSimple, sélectionnez le périphérique que vous souhaitez désactiver, puis, en bas de la page, cliquez sur **désactiver**.

    2. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer. Le processus de désactivation démarre et prendre quelques minutes.

2. Vous pouvez basculer maintenant les conteneurs de volume et les instantanés associés. Pour les procédures, passez à [basculement et reprise après sinistre de votre périphérique StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Après la désactivation et basculement sur incident, vous pouvez supprimer le périphérique complètement. Suppression d’un périphérique supprime de la liste des périphériques connectés au service. Le service puis ne peut plus gérer le périphérique supprimé. Effectuez les étapes suivantes pour supprimer le périphérique :
 
    1. Sur la page de **périphériques** du service Gestionnaire de StorSimple, sélectionnez un périphérique désactivé que vous souhaitez supprimer.

    2. En bas de la page, cliquez sur **Supprimer**.

    3. Vous serez invité à confirmer. Cliquez sur **Oui** pour continuer.

    Il peut prendre quelques minutes pour le périphérique à supprimer.

## <a name="deactivate-and-delete-a-virtual-device"></a>Désactiver et supprimer un périphérique virtuel

Pour un périphérique virtuel StorSimple, désactivation libère la machine virtuelle. Vous pouvez ensuite supprimer l’ordinateur virtuel et les ressources créées lorsqu’il a été mis en service. Une fois le périphérique virtuel est désactivé, elle ne peut pas être restaurée à son état précédent. 

Désactivation des résultats dans les actions suivantes :

- Le périphérique virtuel StorSimple est supprimé.

- Les disques de données créé pour le périphérique virtuel StorSimple et le OSDisk sont supprimés.

- Le Service hébergé et le réseau virtuel qui ont été créés au cours de la mise en service sont conservés. Si vous n’utilisez pas ces entités, vous devez les supprimer manuellement.

- Les snapshots de nuage créés par le périphérique virtuel StorSimple sont conservés.

## <a name="next-steps"></a>Étapes suivantes
- Pour restaurer le périphérique désactivé par défaut, passez à [Réinitialiser le périphérique aux paramètres d’usine par défaut](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Pour obtenir une assistance technique, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md).

- Pour en savoir plus sur la façon d’utiliser le service Gestionnaire de StorSimple, accédez à [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md). 
