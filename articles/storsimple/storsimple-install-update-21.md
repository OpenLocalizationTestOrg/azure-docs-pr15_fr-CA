<properties
   pageTitle="Installer la mise à jour 2.2 sur votre périphérique StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple 8000 série de mise à jour 2.2 sur votre périphérique de la gamme 8000 de StorSimple."
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
   ms.workload="TBD"
   ms.date="08/02/2016"
   ms.author="alkohli" />

# <a name="install-update-22-on-your-storsimple-device"></a>Installer la mise à jour 2.2 sur votre périphérique de StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment installer la mise à jour 2.2 sur un dispositif de StorSimple une version antérieure du logiciel en cours d’exécution via le portail classique Azure et à l’aide de la méthode de correctif. La méthode de correctif est utilisée lorsqu’une passerelle est configurée sur une interface réseau autres que les données 0 du périphérique StorSimple et que vous essayez de mettre à jour à partir d’une version préliminaire de mise à jour du logiciel 1.

Mise à jour 2.2 inclut le logiciel de périphérique, WMI et mises à jour de l’iSCSI. Mise à jour à partir de la version 2.1, uniquement la mise à jour de logiciel de périphérique sera doivent être appliquées. Si la mise à jour à partir d’une version 2 avant mise à jour, vous aussi devront appliquer le pilote LSI, Spaceport, Storport et mises à jour du microprogramme de disque. Le logiciel de périphérique, WMI, iSCSI, pilote, Spaceport et LSI Storport correctifs sont des mises à jour sans interruption de service et peuvent être appliquées via le portail classique Azure. Les mises à jour du microprogramme de disque sont mises à jour sans interruption de service et peuvent être appliquées uniquement via l’interface Windows PowerShell du périphérique. 

> [AZURE.IMPORTANT]

> - Un ensemble de contrôles avant de manuels et automatiques sont effectuées avant l’installation pour déterminer l’état de santé en termes de matériel état et la connectivité réseau. Ces contrôles préalables sont effectués uniquement si vous appliquez les mises à jour à partir du portail classique Azure.
> - Nous vous conseillons d’installer les mises à jour du pilote et le logiciel via le portail classique Azure. Vous ne devez pas dépasser à l’interface Windows PowerShell de l’équipement (à installer les mises à jour) en cas d’échec de la vérification de la mise à jour préalable passerelle dans le portail. En fonction de la version que vous mettez à jour à partir de, les mises à jour peuvent prendre des heures de 1,5 à 2,5 pour installer. Les mises à jour du mode de maintenance doivent être installés via l’interface Windows PowerShell du périphérique. Comme les mises à jour du mode de maintenance sont mises à jour sans interruption de service, il entraîne un temps d’arrêt pour votre périphérique.
> - Si vous exécutez le Gestionnaire de snapshots StorSimple facultatif, assurez-vous que vous avez mis à niveau votre version de gestionnaire de snapshots pour la mise à jour 2.2 avant la mise à jour de l’appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-22-via-the-azure-classic-portal"></a>Installez la mise à jour 2.2 via le portail classique Azure

Effectuez les opérations suivantes pour mettre à jour votre périphérique à [mettre à jour la 2.2](storsimple-update21-release-notes.md).


> [AZURE.NOTE]
Si vous appliquez une mise à jour 2 ou version ultérieure (y compris 2.1 de mise à jour), Microsoft sera en mesure d’extraire les informations de diagnostic supplémentaires à partir du périphérique. Par conséquent, lorsque notre équipe des opérations identifie les périphériques qui posent problème, nous sont mieux équipés pour collecter des informations à partir du périphérique et de diagnostiquer les problèmes. En acceptant la mise à jour 2 ou version ultérieure, vous nous autorisez à cette prise en charge proactive.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre périphérique est en cours d’exécution **StorSimple 8000 série de mise à jour 2.2 (6.3.9600.17708)**. La **dernière mise à jour de la date** doit être modifié. 

    Si vous mettez à jour à partir d’une version antérieure à la mise à jour 2, vous verrez également que les mises à jour du mode de Maintenance sont disponibles (ce message peut continuer à afficher jusqu'à 24 heures après avoir installé les mises à jour).

    Mises à jour du mode de maintenance sont des mises à jour sans interruption de service entraînent un arrêt du périphérique et peuvent être appliquées uniquement via l’interface Windows PowerShell de votre périphérique. Dans certains cas lorsque vous exécutez la mise à jour 1.2, votre microprogramme de disque peut-être déjà à jour, auquel cas vous n’avez pas besoin d’installer les mises à jour du mode de maintenance.

    Si vous mettez à jour à partir de la mise à jour 2, votre périphérique doit maintenant être à jour. Vous pouvez ignorer les étapes restantes.

13. Télécharger les mises à jour du mode de maintenance à l’aide de la procédure indiquée dans la zone [pour télécharger des correctifs](#to-download-hotfixes) pour rechercher et télécharger des KB3121899, les installations de disque mises à jour (les autres mises à jour doivent déjà être installés maintenant).

13. Suivez les étapes répertoriées dans [installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer les mises à jour du mode de maintenance. 

  

## <a name="install-update-22-as-a-hotfix"></a>Installer la mise à jour 2.2 sous la forme d’un correctif

Utilisez cette procédure si vous ne la vérification de la passerelle lorsque vous essayez d’installer les mises à jour via le portail classique Azure. Le contrôle échoue car vous avez une passerelle affectée à une interface réseau 0 de données non- et votre périphérique exécute une version de logiciel avant la mise à jour 1.

Les versions de logiciels qui peuvent être mis à niveau à l’aide de la méthode du correctif logiciel sont les suivants :

- Mise à jour de 0.1, 0.2, 0.3
- Mise à jour 1, 1.1, 1.2
- Mise à jour 2, 2.1 

> [AZURE.IMPORTANT]
>
> - Si votre périphérique exécute la version de publication (GA), veuillez contacter le [Support technique de Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à la mise à jour.

La méthode correctif implique les trois étapes suivantes :

- Télécharger les correctifs à partir du catalogue Microsoft Update.
- Installer et vérifier les correctifs du mode normal.
- Installer et vérifier le correctif en mode de maintenance (uniquement lors de la mise à jour du logiciel de 2 avant mise à jour).

#### <a name="download-updates-for-a-device-running-update-21-software"></a>Télécharger les mises à jour pour un périphérique qui exécute le logiciel de mise à jour 2.1

**Si votre périphérique est en cours d’exécution 2.1 de mise à jour**, vous devez télécharger uniquement la mise à jour des logiciels périphérique KB3179904. Installer uniquement le fichier binaire est précédé de « all-hcsmdssoftwareudpate ». N’installez pas le SID et précédée de la mise à jour de l’agent MDS `all-cismdsagentupdatebundle`. Cela entraînera une erreur. Il s’agit d’une mise à jour sans interruption de service, e/s ne seront pas perturbés et le périphérique n’aura aucune interruption de service.


#### <a name="download-updates-for-a-device-running-update-2-software"></a>Télécharger les mises à jour pour un périphérique qui exécute le logiciel de mise à jour 2

**Si votre périphérique s’exécute Update 2**, vous devez télécharger et installer les correctifs suivants dans l’ordre indiqué :

| Commande  | KO        | Description                    | Type de mise à jour  | Moment de l’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3179904 | Mise à jour logicielle & #42 ;  |  Régulière <br></br>Sans interruption de service     | ~ 45 min |
| 2.      | KB3146621 | package iSCSI | Régulière <br></br>Sans interruption de service  | ~ 20 min |
| 3.      | KB3103616 | Package WMI |  Régulière <br></br>Sans interruption de service      | ~ 12 minutes |


 & #42 ;  Mise à jour de *Remarque, le logiciel se compose de deux fichiers binaires : précédés de mise à jour du logiciel de périphérique `all-hcsmdssoftwareupdate` et l’agent de Cis et Mds précédés de `all-cismdsagentupdatebundle`. La mise à jour du logiciel de périphérique doit être installé avant l’agent Cis et Mds. Vous devez également redémarrer le contrôleur actif via la `Restart-HcsController` mise à jour de l’applet de commande après l’application de l’agent de Cis et MDS (et avant d’appliquer les autres mises à jour).* 

#### <a name="download-updates-for-a-device-running-pre-update-2-software"></a>Télécharger les mises à jour pour un périphérique exécutant le logiciel préliminaire de mise à jour 2

**Si votre périphérique est en cours d’exécution versions 0.2, 0.3, 1.0 et 1.1**, vous devez télécharger et installer le LSI pilote et le micrologiciel à jour le logiciel, iSCSI, et WMI mises à jour. Cette mise à jour est déjà installée si vous exécutez 1,2 de mettre à jour ou 2. 
 
| Commande  | KO        | Description                    | Type de mise à jour  | Moment de l’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3121900 | Microprogramme et pilote LSI             |  Régulière <br></br>Sans interruption de service      | ~ 20 min |


<br></br>
**Si votre périphérique est en cours d’exécution versions 0.2, 0.3, 1.0, 1.1 et 1.2**, vous devez télécharger et installer le Spaceport et le correctif de Storport. Ceux-ci sont déjà installés si vous exécutez la mise à jour 2.

| Commande  | KO        | Description                    | Type de mise à jour  | Moment de l’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 5.      | KB3090322 | Correctif de spaceport </br> Windows Server 2012 R2 |  Régulière <br></br>Sans interruption de service      | ~ 20 min |
| 6.      | KB3080728 | Correctif de Storport </br> Windows Server 2012 R2 |  Régulière <br></br>Sans interruption de service      | ~ 20 min |



<br></br>
Vous devez également installer les mises à jour du microprogramme de disque. Vous pouvez vérifier si vous avez besoin de mises à jour du microprogramme du disque en exécutant la `Get-HcsFirmwareVersion` applet de commande. Si vous exécutez les versions du micrologiciel : `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, vous ne devez pas installer ces mises à jour.


| Commande  | KO        | Description                    | Type de mise à jour  | Moment de l’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 7.      | KB3121899 | Microprogramme de disque              |  Gestion des <br></br>Interruption de l’activité      | ~ 30 min |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Cette procédure doit être effectuée qu’une seule fois pour appliquer la mise à jour 2.2. Vous pouvez utiliser le portail classique Azure pour appliquer les mises à jour ultérieures.
> - Si la mise à jour à partir de la mise à jour 2, le temps total d’installation est proche de 1 heure et demie.
> - Avant d’utiliser cette procédure pour appliquer la mise à jour, assurez-vous que les contrôleurs de périphérique sont en ligne et que tous les composants matériels sont en bon état.

Procédez comme suit pour télécharger et installer les correctifs.

[AZURE.INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la [version de mise à jour 2.1](storsimple-update21-release-notes.md).
