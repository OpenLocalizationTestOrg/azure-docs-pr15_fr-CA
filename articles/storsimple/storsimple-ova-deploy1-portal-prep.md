<properties
   pageTitle="Déployer le tableau virtuel de StorSimple 1 - Préparation du portail"
   description="Premier didacticiel pour déployer StorSimple tableau virtuel implique la préparation du portail"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>Déployer le tableau virtuel de StorSimple - préparer le portail

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>Vue d’ensemble

Cet article s’applique à la version de Microsoft Azure StorSimple tableau virtuel (également connu sous le nom le périphérique virtuel local de StorSimple ou un périphérique virtuel de StorSimple) en cours d’exécution mars 2016 disponibilité générale (GA). Cet article est le premier de la série de didacticiels de déploiement requis pour déployer complètement de votre tableau virtuel comme un serveur de fichiers ou un serveur iSCSI. Cet article décrit la préparation nécessaire pour créer et configurer votre service de gestionnaire de StorSimple avant la mise en service d’un tableau virtuel. Cet article lie également d’un aide-mémoire de configuration de déploiement ainsi que la configuration de conditions préalables.

Vous aurez besoin des privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous conseillons d’étudier la liste de vérification de configuration de déploiement avant de commencer. La préparation de portail prendra moins de 10 minutes.

Les informations publiées dans cet article s’applique au déploiement de réseaux virtuels de StorSimple dans Azure portal classique, ainsi que le gouvernement Microsoft Azure Cloud.

### <a name="get-started"></a>Mise en route

Le flux de travail de déploiement se compose de la préparation du portail, mise en service d’un tableau virtuel de l’environnement virtualisé et fin de l’installation. Pour vous familiariser avec le déploiement de tableau virtuel de StorSimple en tant que serveur de fichiers ou un serveur iSCSI, vous devez faire référence aux ressources suivantes tabulation (articles et vidéos).

#### <a name="deployment-articles"></a>Articles sur le déploiement

Consultez les articles suivants dans la séquence prescrite pour déployer votre tableau virtuel StorSimple.

| **#** | **Dans cette étape**                          | **Vous le ferez...**                                                         | **Utilisez ces documents.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1.   | **Configurer le portail classique Azure**       | Créez et configurez votre service de gestionnaire de StorSimple avant la mise en service d’un périphérique virtuel StorSimple.  |[Préparer le portail](storsimple-ova-deploy1-portal-prep.md)|
|2.   | **Disposition du tableau virtuel**           | Pour Hyper-V, provisionner et se connecter à un périphérique virtuel de StorSimple sur un système hôte exécutant Hyper-V sur Windows Server 2008 R2, Windows Server 2012 R2 ou Windows Server 2012. <br></br> <br></br> Pour VMware, provisionner et connexion à un équipement de locaux virtuel StorSimple sur un système hôte VMware ESXi 5.5 et versions ultérieures.<br></br>| [Mettre en service un tableau virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Mettre en service une baie virtuelle dans VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3.    | **Définir le tableau virtuel**              | Pour votre serveur de fichiers, exécuter l’installation initiale, enregistrer votre serveur de fichiers StorSimple et terminer la configuration du périphérique. Vous pouvez ensuite configurer des partages SMB. <br></br> <br></br> Pour votre serveur iSCSI, effectuer la configuration initiale, enregistrer votre serveur iSCSI de StorSimple et terminer la configuration du périphérique. Vous pouvez ensuite configurer des volumes iSCSI.| [Configurer la baie virtuelle en tant que serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurer la baie virtuelle en tant que serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>Vidéos de déploiement

| **Cette étape...** |  **Regardez cette vidéo.**|
|----------------|-------------|
| Instructions pas à pas pour commencer à utiliser le tableau virtuel StorSimple. | [Mise en route avec le tableau virtuel StorSimple](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Instructions pas à pas pour configurer un tableau virtuel de StorSimple dans Hyper-V.|[Créer un tableau virtuel de StorSimple](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Étape par étape comment configurer et inscrire un tableau virtuel de StorSimple|[Configurer un tableau virtuel de StorSimple](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Des instructions pas à pas pour créer des partages, sauvegardez les partages et restaurer des données sur une baie virtuelle StorSimple configuré comme un serveur de fichiers|[Utiliser le tableau virtuel StorSimple](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Instructions pas à pas pour un basculement sur incident et reprise après sinistre d’un tableau virtuel de StorSimple|[Reprise après sinistre de tableau virtuel StorSimple](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Vous pouvez maintenant commencer à configurer le portail classique Azure.

## <a name="configuration-checklist"></a>Aide-mémoire de configuration

La liste de vérification de configuration décrit les informations que vous devez rassembler avant de configurer le logiciel de votre périphérique StorSimple. Préparation de ces informations à l’avance vous aide à rationaliser le processus de déploiement du périphérique StorSimple dans votre environnement. En fonction de si votre périphérique virtuel StorSimple sera déployé comme serveur de fichiers ou un serveur iSCSI, vous devez une des listes de contrôle suivantes.

-   Télécharger [liste de vérification de la Configuration de serveur de StorSimple tableau virtuel fichier](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Téléchargez [tableau virtuel de StorSimple iSCSI aide-mémoire de Configuration de serveur](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Conditions préalables

Vous trouverez ici la configuration préalable pour votre service de gestionnaire de StorSimple, votre périphérique virtuel StorSimple et le réseau de centre de données.

### <a name="for-the-storsimple-manager-service"></a>Pour le service Gestionnaire de StorSimple

Avant de commencer, assurez-vous que :

-   Vous avez votre compte Microsoft avec les informations d’accès.

-   Vous avez votre compte de stockage Microsoft Azure avec les informations d’accès.

-   Votre abonnement Microsoft Azure doit être activé pour le service de gestionnaire de StorSimple.

### <a name="for-the-storsimple-virtual-device"></a>Pour le périphérique virtuel StorSimple

Avant de déployer un périphérique virtuel, assurez-vous que :

-   Vous avez accès à un système hôte exécutant Hyper-V sur Windows Server 2008 R2 ou version ultérieure ou à VMware (ESXi 5.5 ou version ultérieure) qui peut être utilisé à une disposition d’un périphérique.

-   Le système hôte est en mesure de dédier les ressources suivantes pour configurer votre périphérique virtuel :

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau.

    -   Un disque virtuel de 500 Go pour les données du système.

### <a name="for-the-datacenter-network"></a>Pour le réseau de centre de données

Avant de commencer, assurez-vous que :

-   Le réseau de votre centre de données est configuré en fonction de la configuration réseau requise pour votre périphérique StorSimple. Pour plus d’informations, consultez [StorSimple virtuel tableau requise](storsimple-ova-system-requirements.md).

-   Votre périphérique virtuel StorSimple a une bande passante Internet de Mbits/s 5 dédiée (ou plus) disponibles à tout moment. Cette bande passante ne doit pas être partagée avec d’autres applications.

## <a name="step-by-step-preparation"></a>Préparation étape par étape

Utilisez la procédure à suivre pour préparer votre portail pour le service Gestionnaire de StorSimple.

## <a name="step-1-create-a-new-service"></a>Étape 1 : Création d’un nouveau service

Une seule instance du service Gestionnaire de StorSimple pouvez gérer plusieurs périphériques StorSimple 1200. Effectuez les étapes suivantes pour créer une nouvelle instance du service Gestionnaire de StorSimple. Si vous avez un service StorSimple Manager existant pour gérer vos périphériques 1200, ignorez cette étape et passez à [étape 2 : obtenir la clé d’inscription du service](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service, vous devez créer au moins un compte de stockage, une fois que vous avez créé un service.
>

> - Si vous n’avez pas créé un compte de stockage automatiquement, consultez [configurer un nouveau compte de stockage pour le service](#optional-step-configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
>

> - Si vous avez activé la création automatique d’un compte de stockage, allez à [étape 2 : obtenir la clé d’inscription du service](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Étape 2 : Obtenir la clé d’inscription de service


Une fois que le service Gestionnaire de StorSimple est en cours d’exécution, vous devez obtenir la clé d’inscription du service. Cette clé est utilisée pour inscrire et vous connecter votre dispositif de StorSimple avec le service.

Effectuez les opérations suivantes dans [Azure portal classique](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> La clé d’inscription de service est utilisée pour inscrire tous les périphériques qui doivent s’inscrire auprès de votre service de gestionnaire de StorSimple Gestionnaire de StorSimple.

## <a name="step-3-download-the-virtual-device-image"></a>Étape 3 : Téléchargement de l’image du périphérique virtuel

Une fois que la clé d’inscription du service, vous devez télécharger l’image d’un périphérique virtuel approprié pour configurer un périphérique virtuel sur le système hôte. Les images de périphérique virtuel sont spécifiques au système d’exploitation et peuvent être téléchargées à partir de la page de démarrage rapide dans le portail classique Azure.

> [AZURE.IMPORTANT] Le logiciel s’exécutant sur la baie virtuelle StorSimple est utilisable uniquement conjointement avec le service Gestionnaire de Storsimple.


Effectuez les opérations suivantes dans [Azure portal classique](https://manage.windowsazure.com/).

#### <a name="to-get-the-virtual-device-image"></a>Pour obtenir l’image de périphérique virtuel

1.  Dans la page **Gestionnaire de StorSimple service** , cliquez sur le service que vous avez créé. Vous accédez alors à la page de **Démarrage rapide** . (Vous pouvez cliquer sur l’icône de démarrage rapide ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) pour accéder à la page de **Démarrage rapide** à tout moment.)

1.  Cliquez sur le lien correspondant à l’image que vous souhaitez télécharger à partir du Microsoft Download Center. Les fichiers image sont 4,8 Go environ.

    -   VHDX pour Hyper-V dans Windows Server 2012 et les versions ultérieures

    -   Disque dur virtuel pour Hyper-V sur Windows Server 2008 R2 et versions ultérieures

    -   VMDK de VMWare ESXi 5.5 et versions ultérieures

2.  Téléchargez et décompressez le fichier sur un lecteur local, apporter une note de l’endroit où se trouve le fichier décompressé.

![icône de vidéo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **vidéo disponible**

Regardez la vidéo pour obtenir des instructions pas à pas commencer à utiliser le tableau virtuel StorSimple.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Étape facultative : configurer un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative qui doit être effectuée que si vous n’avez pas activé la création automatique d’un compte de stockage à votre service.

Si vous devez créer un compte de stockage Azure dans une autre région, reportez-vous à la section [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) pour obtenir des instructions pas à pas.

Effectuez les opérations suivantes dans [Azure portal classique](https://manage.windowsazure.com/) sur la page du service Gestionnaire de StorSimple pour ajouter un compte de stockage Microsoft Azure existant.

#### <a name="to-add-a-storage-account"></a>Pour ajouter un compte de stockage

1.  Le service de gestionnaire de StorSimple page d’accueil, sélectionnez votre service et double-cliquez dessus. Vous accédez alors à la page de **Démarrage rapide** . Sélectionnez la page de **configuration** .

2.  Cliquez sur **Ajouter/modifier le compte de stockage**. Dans la boîte de dialogue **Ajouter/modifier compte de stockage** , effectuez le des opérations suivantes :

    1.  Cliquez sur **Nouveau**.

    1.  Fournissez un nom pour votre compte de stockage.

    1.  Fournir la **Touche d’accès rapide** de principal pour votre compte de stockage Microsoft Azure.

    1.  Sélectionnez le **mode d’activer SSL** pour créer un canal sécurisé pour la communication réseau entre votre périphérique et le nuage. Désactivez la case à cocher **Activer SSL mode** uniquement si vous êtes dans un cloud privé.

    1.  Cliquez sur l’icône de vérification ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Vous serez averti une fois le compte de stockage est créé avec succès.

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  Le compte de stockage nouvellement créé sera affiché sur la page **configurer** sous des **comptes de stockage**. Cliquez sur **Enregistrer** pour enregistrer le compte de stockage nouvellement créé. Cliquez sur **OK** à l’invite de confirmation.


## <a name="next-step"></a>Étape suivante

L’étape suivante consiste à configurer une machine virtuelle pour votre périphérique virtuel StorSimple. Selon votre système d’exploitation hôte, consultez les instructions détaillées dans :

-   [Mettre en service un tableau virtuel de StorSimple dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Mettre en service un tableau virtuel de StorSimple dans les environnements VMware](storsimple-ova-deploy2-provision-vmware.md)
