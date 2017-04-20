<properties
   pageTitle="Mise à jour de votre périphérique StorSimple | Microsoft Azure"
   description="Explique comment utiliser la fonctionnalité de mise à jour de StorSimple pour installer les correctifs et mises à jour d’en mode normal et de la maintenance."
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
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>Mettre à jour votre périphérique série 8000 de StorSimple

## <a name="overview"></a>Vue d’ensemble

Les fonctionnalités de mises à jour de StorSimple vous permettent facilement votre périphérique StorSimple à jour. Selon le type de mise à jour, vous pouvez mettre à jour le périphérique via le portail classique Azure ou via l’interface de Windows PowerShell. Ce didacticiel décrit les types de mise à jour et l’installation de chacun d’eux.

Vous pouvez appliquer les deux types de mises à jour de périphérique : 

- Normal (ou, en mode Normal) mises à jour
- Mises à jour du mode de maintenance

Vous pouvez installer des mises à jour régulières via les Azure portal classique de Windows PowerShell ; Toutefois, vous devez utiliser Windows PowerShell pour installer des mises à jour du mode de Maintenance. 

Chaque type de mise à jour est décrite séparément, ci-dessous.

### <a name="regular-updates"></a>Mises à jour régulières

Mises à jour régulières sont mises à jour sans interruption de service peuvent être installés lorsque le périphérique est en mode Normal. Ces mises à jour sont appliquées à chaque contrôleur de périphérique via le site Web Microsoft Update. 

> [AZURE.IMPORTANT] Un basculement sur incident du contrôleur peut se produire au cours du processus de mise à jour. Toutefois, cela n’affecte pas l’opération ou la disponibilité du système.

- Pour plus d’informations sur la façon d’installer les mises à jour régulières via le portail classique Azure, reportez-vous à la section [installation mises à jour régulières via le portal(#install-regular-updates-via-the-azure-classic-portal) classique Azure.

- Vous pouvez également installer les mises à jour régulières par le biais de Windows PowerShell pour StorSimple. Pour plus d’informations, consultez [installation des mises à jour régulières par le biais de Windows PowerShell pour StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Mises à jour du mode de maintenance

Mises à jour du Mode de maintenance sont mises à jour sans interruption de service, tels que des mises à niveau du microprogramme de disque. Ces mises à jour nécessitent le périphérique pour être placé en mode Maintenance. Pour plus d’informations, consultez [étape 2 : mode de Maintenance d’entrer](#step2). Vous ne pouvez pas utiliser le portail classique Azure pour installer les mises à jour du mode de Maintenance. Au lieu de cela, vous devez utiliser Windows PowerShell pour StorSimple. 

Pour plus d’informations sur la façon d’installer les mises à jour du mode de Maintenance, voir [mises à jour du mode de Maintenance d’installation par le biais de Windows PowerShell pour StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] Mises à jour du mode de maintenance doivent être appliquées séparément à chaque contrôleur. 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installer les mises à jour régulières via le portail classique Azure

Vous pouvez utiliser le portail classique Azure pour appliquer des mises à jour de votre périphérique StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installer les mises à jour régulières par le biais de Windows PowerShell pour StorSimple

Vous pouvez également utiliser Windows PowerShell pour StorSimple pour appliquer les mises à jour du standard (mode Normal).

> [AZURE.IMPORTANT] Vous pouvez installer des mises à jour régulières à l’aide de Windows PowerShell pour StorSimple, nous vous recommandons vivement d’installer les mises à jour régulières via le portail classique Azure. À partir de mise à jour 1, des vérifications sont effectuées avant d’installer les mises à jour à partir du portail. Ces vérifications préalables seront préempter des défaillances et assurer une meilleure expérience. 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installer les mises à jour du mode de Maintenance par le biais de Windows PowerShell pour StorSimple

Windows PowerShell pour StorSimple vous permet d’appliquer des mises à jour du mode de Maintenance sur le périphérique StorSimple. Dans ce mode, toutes les demandes d’e/s sont suspendus. Services de mémoire d’accès aléatoire de non volatile (NVRAM) ou le service de cluster sont également arrêtés. Les deux contrôleurs sont réamorcés lorsque vous entrez ou quittez ce mode. Lorsque vous quittez ce mode, tous les services va reprendre et doivent être en bon état. (Cela peut prendre quelques minutes.)

Si vous devez appliquer les mises à jour du mode de Maintenance, vous recevrez une alerte via le portail classique Azure que vous disposez des mises à jour qui doivent être installés. Cette alerte contiendra les instructions pour installer les mises à jour à l’aide de Windows PowerShell pour StorSimple. Une fois que vous mettez à jour votre périphérique, utilisez la même procédure pour modifier le périphérique en mode normal. Pour obtenir des instructions détaillées, reportez-vous à la section [étape 4 : mode Maintenance de sortie](#step4).

> [AZURE.IMPORTANT] 
> 
> - Avant d’entrer en mode de Maintenance, vérifiez que les deux contrôleurs de périphérique sont en bon état en vérifiant l' **État du matériel** , sur la page de **Maintenance** dans Azure portal classique. Si le contrôleur n’est pas en bon état, contactez le Support Microsoft pour les étapes suivantes. Pour plus d’informations, accédez à contacter le support technique Microsoft. 
> - Lorsque vous êtes en mode de Maintenance, vous devez d’abord appliquer la mise à jour sur un seul contrôleur, puis sur l’autre contrôleur.

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Étape 1 : Se connecter à la console série<a name="step1">

Tout d’abord, utilisez une application telle que PuTTY pour accéder à la console série. La procédure suivante explique comment utiliser PuTTY pour se connecter à la console série.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Étape 2 : Mode de Maintenance<a name="step2">

Après que vous être connecté à la console, déterminer s’il existe des mises à jour à installer et entrer en mode de Maintenance pour les installer.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Étape 3 : Installer vos mises à jour<a name="step3">

Ensuite, installez les mises à jour.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Étape 4 : Mode de gestion de sortie<a name="step4">

Enfin, quitter le mode Maintenance.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installez les correctifs par le biais de Windows PowerShell pour StorSimple

Contrairement aux mises à jour pour Microsoft Azure StorSimple, les correctifs sont installés à partir d’un dossier partagé. Comme avec les mises à jour, il existe deux types de correctifs : 

- Correctifs réguliers 
- Correctifs du mode maintenance  

Les procédures suivantes expliquent comment utiliser Windows PowerShell pour StorSimple pour installer ordinaires et les correctifs du mode Maintenance.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Que se passe-t-il pour les mises à jour si vous exécutez une réinitialisation de l’usine du périphérique ?

Si un périphérique est réinitialisé sur les paramètres d’usine, puis toutes les mises à jour sont perdues. Une fois que le périphérique de la fabrique de réinitialisation est enregistré et configuré, vous devez installer manuellement les mises à jour via l’Azure portal classique et/ou de Windows PowerShell pour StorSimple. Pour plus d’informations sur le factory de réinitialisation, consultez [Réinitialiser les paramètres par défaut du périphérique](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur [l’utilisation de Windows PowerShell pour StorSimple administrer votre périphérique StorSimple](storsimple-windows-powershell-administration.md).
- En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
