<properties 
   pageTitle="Modifier le mode de périphérique StorSimple | Microsoft Azure"
   description="Décrit les modes de périphérique StorSimple et explique comment utiliser Windows PowerShell pour StorSimple pour changer le mode du périphérique."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>Modification du mode de périphérique sur votre périphérique StorSimple

Cet article fournit une brève description des différents modes dans lesquels votre périphérique StorSimple peut fonctionner. Votre périphérique StorSimple peut fonctionner en trois modes : normal, maintenance et récupération. 

Après avoir lu cet article, vous saurez :

- Quels sont les modes de périphérique StorSimple sont
- Comment déterminer le mode de l’appareil StorSimple est dans
- Comment changer de normal à mode de maintenance et *vice versa*


Les tâches de gestion ci-dessus n’est possible que via l’interface Windows PowerShell de votre périphérique StorSimple.

## <a name="about-storsimple-device-modes"></a>A propos des modes de périphérique StorSimple

Votre périphérique StorSimple peut fonctionner en mode normal, de maintenance ou de récupération. Chacun de ces modes est brièvement décrite ci-dessous.

### <a name="normal-mode"></a>En mode normal

Il s’agit du mode de fonctionnement normal pour un périphérique StorSimple entièrement configuré. Par défaut, votre périphérique doit être en mode normal.

### <a name="maintenance-mode"></a>En mode maintenance

Le périphérique StorSimple devrez parfois être placé en mode maintenance. Ce mode vous permet d’effectuer la maintenance sur le périphérique et d’installer les mises à jour sans interruption de service, tels que ceux liés au microprogramme du disque.

Vous pouvez mettre le système en mode de maintenance uniquement via le Windows PowerShell pour StorSimple. Dans ce mode, toutes les demandes d’e/s sont suspendus. Services de mémoire d’accès aléatoire de non volatile (NVRAM) ou le service de cluster sont également arrêtés. Les deux contrôleurs sont redémarrés lorsque vous entrez ou quittez ce mode. Lorsque vous quittez le mode de maintenance, tous les services va reprendre et doivent être en bon état. Cette opération peut prendre quelques minutes.

>[AZURE.NOTE]Mode de maintenance **est uniquement pris en charge sur un périphérique fonctionne correctement. Il n’est pas pris en charge sur un périphérique dans lequel une ou plusieurs des contrôleurs ne fonctionnent pas.**
</br>

### <a name="recovery-mode"></a>En mode de récupération

En mode de récupération peut être décrit comme « Mode sans échec avec prise en charge de réseau Windows ». Mode de récupération s’engage à l’équipe de Support de Microsoft et leur permet d’effectuer des diagnostics sur le système. L’objectif principal du mode de récupération est de récupérer les journaux système.

Si votre système passe en mode de récupération, vous devez contacter le Support Microsoft pour les étapes suivantes. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

>[AZURE.NOTE] **Vous ne pouvez pas placer le périphérique en mode de récupération. Si le périphérique est en mauvais état, le mode de récupération tente d’obtenir le périphérique dans un état dans lequel le personnel de Support Microsoft puisse l’examiner en.**

## <a name="determine-storsimple-device-mode"></a>Déterminer le mode de périphérique StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Pour déterminer le mode de périphérique en cours

1. Ouvrez une session sur la console série du périphérique en suivant les étapes de [PuTTY utiliser pour se connecter à la console série du périphérique](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Examinez le message de bannière dans le menu de la console série du périphérique. Ce message indique explicitement si le périphérique est en mode de maintenance ou de récupération. Si le message ne contient-elle pas toutes les informations spécifiques relatives au mode du système, le périphérique est en mode normal.

## <a name="change-the-storsimple-device-mode"></a>Modifier le mode de périphérique StorSimple 

Vous pouvez placer le périphérique StorSimple en mode de maintenance (à partir de mode normal) pour effectuer la maintenance ou d’installer des mises à jour du mode de maintenance. Exécutez les procédures suivantes pour entrer ou quitter le mode maintenance.

> [AZURE.IMPORTANT] Avant d’entrer en mode de maintenance, vérifiez que les deux contrôleurs de périphérique sont sains en accédant à l' **État du matériel** , sur la page de **Maintenance** dans Azure portal classique. Si un ou deux contrôleurs ne sont pas intègres, contactez le Support Microsoft pour les étapes suivantes. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

#### <a name="to-enter-maintenance-mode"></a>Pour passer en mode de maintenance

1. Ouvrez une session sur la console série du périphérique en suivant les étapes de [PuTTY utiliser pour se connecter à la console série du périphérique](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Dans le menu de la console série, choisissez l’option 1, **Connectez-vous à l’aide d’un accès complet**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur**. Le mot de passe par défaut est : `Password1`.

3. À l’invite de commandes, tapez : 

    `Enter-HcsMaintenanceMode`

4. Vous verrez un message d’avertissement vous indiquant que le mode maintenance s’interrompre toutes les requêtes d’e/s et le serveur de la connexion au portail Azure classique et vous demandera confirmation. Tapez **Y** pour entrer en mode de maintenance.

5. Les deux contrôleurs va redémarrer. Lorsque le redémarrage est terminé, un autre message s’affiche indiquant que le périphérique est en mode maintenance.


#### <a name="to-exit-maintenance-mode"></a>Pour quitter le mode maintenance

1. Ouvrez une session sur la console série du périphérique. Vérifiez à partir du message de bannière que votre périphérique est en mode maintenance.

2. À l’invite de commandes, tapez :

    `Exit-HcsMaintenanceMode`

3. Un message d’avertissement et un message de confirmation seront affiche. Tapez **Y** pour quitter le mode maintenance.

4. Les deux contrôleurs va redémarrer. Lorsque le redémarrage est terminé, un autre message s’affiche indiquant que le périphérique est en mode normal.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [appliquer les mises à jour d’en mode normal et de la maintenance](storsimple-update-device.md) de votre périphérique StorSimple.

