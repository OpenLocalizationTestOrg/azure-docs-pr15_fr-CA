<properties
   pageTitle="Déployer le dispositif de StorSimple dans le portail d’administration | Microsoft Azure"
   description="Décrit les étapes et les meilleures pratiques pour déployer le service dans le portail d’administration d’Azure et le périphérique de mise à jour de StorSimple 2."
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
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="v-sharos" />

# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal-update-2"></a>Déployer votre périphérique de StorSimple locaux dans le portail de l’administration (mise à jour 2)

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple déploiement du périphérique. Ces didacticiels de déploiement s’appliquent à la série 8000 StorSimple exécutant le logiciel de mise à jour 2 dans le portail de l’administration d’Azure. Cette série de didacticiels inclut une liste de contrôle de configuration, une liste de configuration préalable et la procédure de configuration détaillée de votre périphérique StorSimple.

Les informations contenues dans ces didacticiels supposent que vous avez examiné les mesures de sécurité et décompressé, montés en rack et relié de votre périphérique StorSimple. Si vous avez besoin effectuer ces tâches, commencez par examiner les [précautions de sécurité](storsimple-safety.md). Suivez les instructions spécifiques au périphérique pour le décompresser, montage en rack et câble de votre périphérique.

- [Décompresser, montage en rack et câblage votre 8100](storsimple-8100-hardware-installation.md)
- [Décompresser, montage en rack et câblage votre 8600](storsimple-8600-hardware-installation.md)

Vous aurez besoin des privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous conseillons d’étudier la liste de vérification de configuration avant de commencer. Le processus de déploiement et de configuration peut prendre un certain temps pour terminer.

> [AZURE.NOTE] Les informations relatives au déploiement StorSimple publiés sur le site Web de Microsoft Azure s’applique aux périphériques série 8000 de StorSimple. Pour plus d’informations sur les périphériques 7000 série, accédez à : [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Pour des informations sur le déploiement 7000 series, consultez le [Guide de démarrage rapide StorSimple système](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Étapes de déploiement

Effectuez ces étapes requises pour configurer votre périphérique StorSimple et le connecter à votre service de gestionnaire de StorSimple. Outre les étapes requises, il existe des étapes facultatives et les procédures que vous devrez peut-être effectuer au cours du déploiement. Instructions de déploiement détaillées indiquent lorsque vous devez effectuer chacune des étapes facultatives suivantes.


| Étape                                                                                   | Description                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CONDITIONS PRÉALABLES**                                                                      | Ceux-ci doivent être réalisées en préparation pour le déploiement à venir.                                                                                        |
|[Aide-mémoire de configuration de déploiement](#deployment-configuration-checklist)                                                     | Cette liste de vérification permet de rassembler et d’enregistrer des informations avant et pendant le déploiement.                                                                       |
| [Conditions préalables au déploiement](#deployment-prerequsites)                                                               | Il valide que l’environnement est prêt pour le déploiement.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **DÉPLOIEMENT ÉTAPE PAR ÉTAPE**                                                                   | Ces étapes sont nécessaires pour déployer votre périphérique StorSimple dans la production.                                                                                      |
| [Étape 1 : Création d’un nouveau service](#step-1-create-a-new-service)                                                         | Permet de paramétrer la gestion de cloud et de stockage de votre périphérique StorSimple. *Ignorez cette étape si vous avez un service existant pour d’autres périphériques StorSimple*.              |
| [Étape 2 : Obtenir la clé d’inscription de service](#step-2-get-the-service-registration-key)                                               | Cette clé permet d’inscrire et de connecter votre dispositif de StorSimple avec le service de gestion.                                                                         |
| [Étape 3 : Configurer et enregistrer le périphérique par le biais de Windows PowerShell pour StorSimple](#step 3-configure-and-register-the-device-through-windows-powershell-for-storsimple) | Connectez le périphérique à votre réseau et l’inscrire avec Azure pour effectuer l’installation à l’aide du service de gestion.                                            |
| [Étape 4 : Terminer l’installation de périphériques](#step-4-complete-the-minimum-device-setup) </br>Facultatif : Mise à jour de votre périphérique StorSimple. | Le service Gestion des services permet de terminer la configuration de périphérique et de l’activer pour fournir le stockage.                                                                      |
| [Étape 5 : Créer un conteneur de volume](#step-5-create-a-volume-container)                                                      | Créer un conteneur pour des volumes de disposition. Un conteneur de volume a compte de stockage, de la bande passante et de paramètres de cryptage pour tous les volumes qu’il contient.    |
| [Étape 6 : Création d’un volume](#step-6-create-a-volume)                                                               | Provisionner les volumes de stockage sur le périphérique StorSimple pour vos serveurs.                                                                                        |
| [Étape 7 : Montage, initialiser et formatage d’un volume](#step-7-mount-initialize-and-format-a-volume) </br>Facultatif : Configuration MPIO.            | Se connecter à vos serveurs pour le stockage iSCSI fourni par le périphérique. Vous pouvez également configurer MPIO pour vous assurer que vos serveurs peuvent tolérer lien et Échec de l’interface réseau.                                                                                                                                                              |
| [Étape 8 : Effectuer une sauvegarde](#step-8-take-a-backup)                                                                  | Définir votre stratégie de sauvegarde pour protéger vos données                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **AUTRES PROCÉDURES**                                                                   | Vous devrez peut-être faire référence à ces procédures lorsque vous déployez votre solution.                                                                                    |
| [Configurer un nouveau compte de stockage pour le service](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [PuTTY permet de se connecter à la console série du périphérique](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [Rechercher et appliquer des mises à jour](#scan-for-and-apply-updates)                                                   |                                                                                                                                                               |
| [Obtenir le nom qualifié d’un hôte Windows Server](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [Créer une sauvegarde manuelle](#create-a-manual-backup)                                                                 |
| [Configuration de MPIO](#configure-mpio)                                                                          |

## <a name="deployment-configuration-checklist"></a>Aide-mémoire de configuration de déploiement

Avant de déployer votre périphérique StorSimple, vous devez collecter des informations afin de configurer le logiciel sur votre périphérique. Certaines de ces informations à l’avance de préparation permettra de rationaliser le processus de déploiement du périphérique StorSimple dans votre environnement. Téléchargez et utilisez cette liste de vérification de noter les détails de configuration lors du déploiement de votre périphérique.  

[Télécharger la liste de contrôle configuration de déploiement StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)  

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

Les sections suivantes expliquent les conditions préalables de configuration de votre service de gestionnaire de StorSimple et de votre périphérique StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Pour le service Gestionnaire de StorSimple

Avant de commencer, assurez-vous que :

- Vous avez votre compte Microsoft avec les informations d’accès.

- Vous avez votre compte de stockage Microsoft Azure avec les informations d’accès.

- Votre abonnement Microsoft Azure est activée pour le service Gestionnaire de StorSimple. Votre abonnement doit être acheté par le biais de l' [Accord de l’entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Vous avez accès au logiciel d’émulation de terminal tel que PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Pour le périphérique dans le centre de données

Avant de configurer le périphérique, vérifiez que :

- Votre périphérique est complètement décompressé, monté sur rack et totalement câblé d’alimentation, de réseau et d’accès série, comme décrit dans :

    -  [Décompresser, montage en rack et câble de votre périphérique 8100](storsimple-8100-hardware-installation.md)
    -  [Décompresser, montage en rack et câble de votre périphérique 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données

Avant de commencer, assurez-vous que :

- Les ports dans le pare-feu de votre centre de données sont ouverts pour autoriser pour iSCSI et le trafic comme décrit dans les [réseau des exigences de votre périphérique StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)en nuage.

## <a name="step-by-step-deployment"></a>Déploiement étape par étape

Utilisez la procédure à suivre pour déployer votre périphérique StorSimple dans le centre de données.

## <a name="step-1-create-a-new-service"></a>Étape 1 : Création d’un nouveau service

Un service StorSimple Manager peut gérer plusieurs périphériques StorSimple. Effectuez les étapes suivantes pour créer une nouvelle instance du service Gestionnaire de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT] Si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service, vous devez créer au moins un compte de stockage, une fois que vous avez créé un service. Ce compte de stockage sera utilisé lorsque vous créez un conteneur de volume.
>
> * Si vous n’avez pas créé un compte de stockage automatiquement, consultez [configurer un nouveau compte de stockage pour le service](#configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
> * Si vous avez activé la création automatique d’un compte de stockage, allez à [étape 2 : obtenir la clé d’inscription du service](#step-2-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Étape 2 : Obtenir la clé d’inscription de service

Une fois que le service Gestionnaire de StorSimple est en cours d’exécution, vous devez obtenir la clé d’inscription du service. Cette clé est utilisée pour inscrire et de connecter le périphérique StorSimple au service.

Effectuez les opérations suivantes dans le portail de l’administration.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Étape 3 : Configurer et enregistrer le périphérique par le biais de Windows PowerShell pour StorSimple

Utiliser Windows PowerShell pour StorSimple pour effectuer la configuration initiale de votre périphérique StorSimple comme expliqué dans la procédure suivante. Vous devez utiliser un logiciel d’émulation de terminal pour terminer cette étape. Pour plus d’informations, consultez [PuTTY utiliser pour se connecter à la console série du périphérique](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Étape 4 : Exécutez le programme d’installation de périphériques

Pour la configuration des périphériques de votre périphérique StorSimple, vous devez :

- Configurer le serveur DNS secondaire.
- Activer iSCSI sur au moins une interface réseau.
- Affecter une adresse IP fixe sur les deux contrôleurs.

Effectuez les opérations suivantes dans le portail de l’administration pour terminer l’installation de périphériques.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Étape 5 : Créer un conteneur de volume

Un conteneur de volume a compte de stockage, de la bande passante et de paramètres de cryptage pour tous les volumes qu’il contient. Vous devez créer un conteneur de volume avant de commencer la mise en service des volumes sur le périphérique StorSimple.

Effectuez les opérations suivantes dans le portail de l’administration pour créer un conteneur de volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Étape 6 : Création d’un volume

Après avoir créé un conteneur de volume, vous pouvez prévoir un volume de stockage sur le périphérique StorSimple pour vos serveurs. Effectuez les opérations suivantes dans le portail de l’administration pour créer un volume.

> [AZURE.IMPORTANT] Azure StorSimple pouvez créer des volumes provisionnées uniquement.  Vous ne pouvez pas créer entièrement configuré ou partiellement configuré des volumes sur un système de StorSimple d’Azure.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Étape 7 : Montage, initialiser et formatage d’un volume

Effectuez ces étapes sur votre hôte Windows.

> [AZURE.IMPORTANT]

> - Pour la haute disponibilité de votre solution de StorSimple, nous vous conseillons de configurer MPIO sur vos serveurs d’hôte (facultatifs) avant la configuration iSCSI. Configuration de MPIO sur les serveurs hôtes permet de garantir que les serveurs peuvent tolérer un lien, un réseau ou une défaillance de l’interface.

> - Pour MPIO et iSCSI configuration instructions d’installation et sur l’hôte Windows Server, accédez à [Configuration de MPIO pour votre périphérique StorSimple](storsimple-configure-mpio-windows-server.md). Il inclut également les étapes pour le montage, initialiser et formater des volumes de StorSimple.

> - Pour MPIO et iSCSI configuration instructions d’installation et sur un hôte Linux, accédez à [Configuration de MPIO pour votre hôte Linux de StorSimple](storsimple-configure-mpio-on-linux.md)

Si vous décidez de ne pas configurer de MPIO, effectuez les opérations suivantes pour monter, initialiser et formater les volumes StorSimple sur un hôte Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Étape 8 : Effectuer une sauvegarde

Les sauvegardes protègent le point-à-temps des volumes et améliorent la capacité de restauration tout en réduisant les délais de restauration. Vous pouvez prendre deux types de sauvegarde de votre périphérique StorSimple : snapshots locaux et des instantanés de nuage. Chacun de ces types de sauvegarde peut être **planifiée** ou **manuelle**.

Effectuez les opérations suivantes dans le portail de l’administration pour créer une sauvegarde planifiée.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Vous pouvez prendre un manuel de sauvegarde à tout moment. Pour les procédures, passez à [Création d’une sauvegarde manuelle](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurer un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative dont vous avez besoin pour effectuer uniquement si vous n’avez pas activé la création automatique d’un compte de stockage à votre service. Un compte de stockage Microsoft Azure est requis pour créer un conteneur de volume StorSimple.

Si vous devez créer un compte de stockage Azure dans une autre région, reportez-vous à la section [Sur les comptes de stockage Azure](../storage/storage-create-storage-account.md) pour obtenir des instructions pas à pas.

Effectuez les opérations suivantes dans le portail de l’administration, sur la page **service de gestionnaire de StorSimple** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>PuTTY permet de se connecter à la console série du périphérique

Pour vous connecter à Windows PowerShell pour StorSimple, vous devez utiliser un logiciel d’émulation de terminal tel que PuTTY. Vous pouvez utiliser PuTTY lorsque vous accédez à l’unité directement par l’intermédiaire de la console série, ou en ouvrant une session telnet à partir d’un ordinateur distant.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Rechercher et appliquer des mises à jour

Mise à jour de votre appareil peut prendre plusieurs heures. Effectuez les opérations suivantes pour rechercher et appliquer des mises à jour sur votre appareil.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Mise à jour de votre périphérique

1.  Sur la page de **Démarrage rapide** de périphérique, cliquez sur **périphériques**. Sélectionnez le périphérique physique et cliquez sur **Maintenance** puis cliquez sur **Rechercher les mises à jour**.  
2.  Un travail d’analyse des mises à jour disponibles est créé. Si les mises à jour sont disponibles, l' **Analyse des mises à jour** se modifie pour **Installer les mises à jour**. Cliquez sur **installer les mises à jour**.
3.  Une tâche de mise à jour sera créée. Surveiller l’état de votre mise à jour en accédant aux **projets**.

    > [AZURE.NOTE] Lors du démarrage de la tâche de mise à jour, il affiche immédiatement le statut de 50 %. Qu’une fois la tâche de mise à jour est terminée, l’état passe à 100 pour cent. Il n’y a aucun état en temps réel pour le processus de mise à jour.

4.  Une fois que le périphérique est correctement mis à jour, activer les interfaces de réseau de données 2 et données 3 si elles ont été désactivées.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtenir le nom qualifié d’un hôte Windows Server

Effectuez les opérations suivantes pour obtenir l’iSCSI nom qualifié (IQN) d’un hôte Windows qui exécute Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Créer une sauvegarde manuelle

Effectuez les opérations suivantes dans le portail de l’administration pour créer une sauvegarde manuelle de la demande pour un seul volume de votre périphérique StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## <a name="configure-mpio"></a>Configuration de MPIO

E/s à chemins multiples (MPIO) est une fonctionnalité facultative et n’est pas installé par défaut sur Windows Server. Il doit être installé en tant que fonction via le Gestionnaire de serveur. Pour des instructions d’installation de MPIO, accédez à [Configuration de MPIO pour votre périphérique StorSimple](storsimple-configure-mpio-windows-server.md).

Pour des instructions d’installation de MPIO pour un périphérique StorSimple connecté à un hôte Linux, accédez à [Configuration de MPIO pour votre hôte Linux](storsimple-configure-mpio-on-linux.md).

> [AZURE.NOTE] MPIO n’est pas pris en charge sur un périphérique virtuel de StorSimple dans Azure.

## <a name="next-steps"></a>Étapes suivantes

- Configuration d’un [périphérique virtuel](storsimple-virtual-device-u2.md).

- Utilisez le [Gestionnaire de StorSimple service](https://msdn.microsoft.com/library/azure/dn772396.aspx) pour gérer le périphérique StorSimple.
