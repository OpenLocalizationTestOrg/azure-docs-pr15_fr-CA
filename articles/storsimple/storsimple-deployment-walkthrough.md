<properties
   pageTitle="Déployer un dispositif de StorSimple sur site | Microsoft Azure"
   description="Décrit les étapes et les meilleures pratiques pour déployer le service et le dispositif de StorSimple. (S’applique à Microsoft Azure StorSimple version.3 et versions antérieures.)"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="deploy-your-on-premises-storsimple-device"></a>Déployer votre périphérique de StorSimple local

> [AZURE.SELECTOR]
- [Mise à jour 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Mise à jour 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [Version de GA](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple déploiement du périphérique. Ces didacticiels de déploiement s’appliquent à la Version de Release StorSimple 8000 Series, mise à jour de série StorSimple 8000 0,1, mise à jour de la série StorSimple 8000 0,2 et mise à jour de série StorSimple 8000 0,3. Cette série de didacticiels décrit comment configurer votre périphérique StorSimple et comprend une liste de contrôle de configuration configuration préalable et configuration détaillé suit.


Les informations contenues dans ces didacticiels supposent que vous avez examiné les mesures de sécurité et décompressé, montés en rack et relié de votre périphérique StorSimple. Si vous avez besoin effectuer ces tâches, commencez par examiner les [précautions de sécurité](storsimple-safety.md). Selon le modèle de votre périphérique, vous pouvez ensuite décompresser, montage en rack et câble en suivant les instructions dans :

- [Décompresser, montage en rack et câblage votre 8100](storsimple-8100-hardware-installation.md)
- [Décompresser, montage en rack et câblage votre 8600](storsimple-8600-hardware-installation.md)

Vous aurez besoin des privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous conseillons d’étudier la liste de vérification de configuration avant de commencer. Le processus de déploiement et de configuration peut prendre un certain temps pour terminer.

> [AZURE.NOTE] Les informations relatives au déploiement StorSimple publiés sur le site Web de Microsoft Azure s’applique aux périphériques série 8000 de StorSimple. Pour plus d’informations sur les périphériques série 5000 et 7000, accédez à : [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Pour les informations du déploiement de la série 5000 et 7000, consultez le [Guide de démarrage rapide StorSimple système](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Étapes de déploiement

Effectuez ces étapes requises pour configurer votre périphérique StorSimple et le connecter à votre service de gestionnaire de StorSimple. Outre les étapes requises, il existe des étapes facultatives et les procédures que vous pouvez avoir besoin pendant le déploiement. Instructions de déploiement détaillées indiquent lorsque vous devez effectuer chacune des étapes facultatives suivantes.


| Étape                                                                                   | Description                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CONDITIONS PRÉALABLES**                                                                      | Ceux-ci doivent être réalisées en préparation pour le déploiement à venir.                                                                                        |
| Aide-mémoire de configuration de déploiement.                                                     | Cette liste de vérification permet de rassembler et d’enregistrer des informations avant et pendant le déploiement.                                                                       |
| Conditions préalables au déploiement.                                                               | Ces valider l’environnement est prêt pour le déploiement.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **DÉPLOIEMENT ÉTAPE PAR ÉTAPE**                                                                   | Ces étapes sont nécessaires pour déployer votre périphérique StorSimple dans la production.                                                                                      |
| Étape 1 : Création d’un nouveau service.                                                         | Permet de paramétrer la gestion de cloud et de stockage de votre périphérique StorSimple. Ignorez cette étape si vous avez un service existant pour d’autres périphériques StorSimple.                |
| Étape 2 : Obtenir la clé d’inscription du service.                                               | Cette clé permet d’enregistrer et vous connecter votre dispositif de StorSimple avec le service de gestion.                                                                         |
| Étape 3 : Configurer et enregistrer le périphérique par le biais de Windows PowerShell pour StorSimple.    | Connectez le périphérique à votre réseau et l’inscrire avec Azure pour effectuer l’installation à l’aide du service de gestion.                                            |
| Étape 4 : Exécutez le programme d’installation de périphériques</br>Facultatif : Mise à jour de votre périphérique StorSimple.      | Le service Gestion des services permet de terminer la configuration de périphérique et de l’activer pour fournir le stockage.                                                                      |
| Étape 5 : Créer un conteneur de volume.                                                      | Créer un conteneur pour des volumes de disposition. Un conteneur de volume a compte de stockage, de la bande passante et de paramètres de cryptage pour tous les volumes qu’il contient.    |
| Étape 6 : Création d’un volume.                                                                | Provisionner les volumes de stockage sur le périphérique StorSimple pour vos serveurs.                                                                                        |
| Étape 7 : Montage, initialiser et formater un volume.</br>Facultatif : Configuration MPIO.            | Se connecter à vos serveurs pour le stockage iSCSI fourni par le périphérique. Vous pouvez éventuellement configurer MPIO pour vous assurer que vos serveurs peuvent tolérer lien et Échec de l’interface réseau.                                                                                                                                                              |
| Étape 8 : Effectuer une sauvegarde.                                                                  | Définir votre stratégie de sauvegarde pour protéger vos données                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **AUTRES PROCÉDURES**                                                                   | Vous devrez peut-être faire référence à ces procédures lorsque vous déployez votre solution.                                                                                        |
| Configurer un nouveau compte de stockage pour le service.                                      |                                                                                                                                                               |
| PuTTY permet de se connecter à la console série du périphérique.                                    |                                                                                                                                                               |
| Obtenir le nom qualifié d’un hôte Windows Server.                                                   |                                                                                                                                                               |
| Créer une sauvegarde manuelle.                                                                 |


## <a name="deployment-configuration-checklist"></a>Aide-mémoire de configuration de déploiement

La liste de configuration de déploiement suivante décrit les informations dont vous avez besoin de collecter avant et que vous configurez le logiciel de votre périphérique StorSimple. Certaines de ces informations à l’avance de préparation permettra de rationaliser le processus de déploiement du périphérique StorSimple dans votre environnement. Cette liste de vérification permet de Notez également les détails de configuration lors du déploiement de votre périphérique.

| Étape                                  | Paramètre                                         | Détails                                                                                                                                                                | Valeurs |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Câble de votre périphérique**                      | Accès série                                     | Configuration de périphérique initial                                                                  | Oui/non |
|   |   |  |  |
| **Configurer et inscrire l’appareil**          | Paramètres de réseau de données 0                           | Adresse IP de données 0 :</br>Masque de sous-réseau :</br>Passerelle :</br>Serveur DNS principal :</br>Serveur NTP principal :</br>Serveur de proxy Web IP/nom de domaine complet (facultatif) :</br>Port du proxy Web :|        |
|                                        | Mot de passe administrateur                     | Mot de passe doit comporter entre 8 et 15 caractères contenant des caractères minuscules, majuscules, numériques et spéciaux. |        |
|                                        | Gestionnaire de snapshots de StorSimple de mot de passe              | Mot de passe doit être 14 ou 15 caractères contenant des caractères minuscules, majuscules, numériques et spéciaux.|        |
|                                        | Clé d’enregistrement de service                          | Cette clé est générée à partir du portail classique Azure.    |        |
|                                        | Clé de cryptage de données de service                       | Cette clé est créée lorsque le périphérique est enregistré avec le service de gestion via le Windows PowerShell pour StorSimple. Copiez cette clé et l’enregistrer dans un emplacement sûr.|  |
|   |   |  |  |
| **Programme d’installation de périphériques terminée**          | Nom convivial pour votre périphérique                     | Il s’agit d’un nom descriptif pour le périphérique. |        |
|                                        | Fuseau horaire                                          | Votre périphérique utilise ce fuseau horaire pour toutes les opérations planifiées.  |        |
|                                        | Serveur DNS secondaire                              | Il s’agit d’une configuration requise.                                  |        |
|                                        | Interface réseau : contrôleur de données 0 fixe IPs                                     | Ces IP doit être repositionnable à Internet.</br>Contrôleur 0 fixe IP adresse :</br>Contrôleur 1 fixe IP adresse :|
|   |   |  |  |
| **Paramètres d’interface réseau supplémentaires**  | Interface réseau : données 1</br>Si iSCSI activée, ne configurez pas la passerelle.      | Objectif : Nuage/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
|                                        | Interface réseau : données 2</br>Si iSCSI activée, ne configurez pas la passerelle.      | Objectif : Nuage/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
|                                        | Interface réseau : données 3</br>Si iSCSI activée, ne configurez pas la passerelle.      | Objectif : Nuage/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
|                                        | Interface réseau : données 4</br>Si iSCSI activée, ne configurez pas la passerelle.      | Objectif : Nuage/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
|                                        | Interface réseau : données 5</br>Si iSCSI activée, ne configurez pas la passerelle.      | Objectif : Nuage/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
|   |   |  |  |
| **Créer un conteneur de volume**                      | Nom de conteneur de volume :                            | Nom du conteneur                                                                                                                                                 |        |
|                                        | Compte de stockage Azure :                            | Stockage nom & d’accès clé du compte à associer à ce conteneur de volume                                                                                              |        |
|                                        | Clé de cryptage de stockage cloud :                     | Clé de chiffrement pour le stockage de chaque conteneur                                                                                                                           |        |
|   |   |  |  |
| **Créer un volume**                        | Détails de chaque volume                           | Nom de volume :                                                                                                                                                           |        |
|                                        |                                                   | Taille :                                                                                                                                                                  |        |
|                                        |                                                   | Type d’utilisation :                                                                                                                                                            |        |
|                                        |                                                   | Nom de blocage :                                                                                                                                                              |        |
|                                        |                                                   | Stratégie de sauvegarde par défaut :                                                                                                                                                 |        |
|   |   |  |  |
| **Monter, initialiser et formatage d’un volume** | Détails pour chaque serveur hôte de la connexion au stockage | Nom du serveur de Windows :                                                                                                                                                   |        |
|                                        |                                                   | Windows Server nom qualifié :                                                                                                                                                    |        |
|                                        |                                                   | Nom de volume de Windows Server :                                                                                                                                                   |        |
|                                        |                                                   | Lettre de sur des points de montage NTFS :                                                                                                                                      |        |

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

Les sections suivantes expliquent les conditions préalables de configuration pour votre service de gestionnaire de StorSimple, votre périphérique StorSimple et le réseau de votre centre de données.

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
- Le périphérique de votre centre de données peut se connecter à un réseau externe. Exécuter les applets de commande [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) suivant (sous forme de tableau ci-dessous) pour valider la connectivité au réseau externe. Effectuer cette validation sur un ordinateur qui dispose d’une connectivité vers Azure et où vous déploierez votre périphérique StorSimple (dans le réseau de centre de données).  

| Pour ce paramètre...       | Pour vérifier la validité...                                                                                                                                                                                | Exécutez ces commandes/applets de commande.                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP**</br>**Sous-réseau**</br>**Passerelle** | S’agit-il d’une adresse IPv4 ou IPv6 valide ?</br>S’agit-il d’un sous-réseau valide ?</br>S’agit-il d’une passerelle valide ?</br>S’agit-il d’une adresse IP en double sur le réseau ?                                                                          | `ping ip`</br>`arp -a`</br>La `ping` et `arp` commandes doivent échouer indiquant qu’il n’existe aucun périphérique du réseau de centres de données qui est à l’aide de cette adresse IP.
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **DNS**                       | Ceci est un serveur DNS valide et peut résoudre des URL d’Azure ?                                                                                                                    | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Une autre commande qui peut être utilisée est la suivante :</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com`      |
|                           | Vérifiez si le port 53 est ouvert. Ceci est applicable uniquement si vous utilisez un serveur DNS externe pour votre périphérique. DNS interne doit résoudre automatiquement les URL externes.  | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[Plus d’informations sur cette applet de commande](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/)|
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **NTP**                       | Nous déclenche une synchronisation, dès l’entrée de serveur NTP. Vérifier les port UDP 123 est ouvert lorsque vous entrez `time.windows.com` ou des serveurs de temps publics). | [Télécharger et utiliser ce script](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca).                                                                                                                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Proxy (facultatif)**          | S’agit-il d’un URI de proxy valide et un port ? </br> Est le mode d’authentification correcte ?                                                                                                                                | <code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Cette commande doit être exécutée immédiatement après la configuration de proxy web. Si un code d’état 200 est renvoyé, elle indique que la connexion est réussie.                                                                                                                                 |
|                           | Le trafic est routable via proxy ?                                                                                                                                 | Exécutez la validation de DNS, de NTP vérification ou de vérification HTTP une fois après la configuration du serveur proxy sur votre périphérique. Cela donnera une image claire si le trafic est bloqué à un proxy ou un autre emplacement.                                                                                                                      |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Inscription**              | Vérifiez si les ports TCP sortants 443, 80, 9354 sont ouverts.                                                                                                                |  `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Plus d’informations pour l’applet de commande Test-NetConnection](https://technet.microsoft.com/library/dn372891.aspx)                                                                           |

## <a name="step-by-step-deployment"></a>Déploiement étape par étape

Utilisez la procédure à suivre pour déployer votre périphérique StorSimple dans le centre de données.

## <a name="step-1-create-a-new-service"></a>Étape 1 : Création d’un nouveau service

Un service StorSimple Manager peut gérer plusieurs périphériques StorSimple. Pour le déploiement de votre premier périphérique StorSimple, vous devrez créer un nouveau service de gestionnaire de StorSimple.

> [AZURE.IMPORTANT] Ignorez cette étape si vous avez un gestionnaire de StorSimple de service existant et que vous envisagez de déployer votre périphérique StorSimple avec le service.

Effectuez les étapes suivantes pour créer une nouvelle instance du service Gestionnaire de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service, vous devez créer au moins un compte de stockage, une fois que vous avez créé un service. Ce compte de stockage sera utilisé lorsque vous créez un conteneur de volume.
>
> Si vous n’avez pas créé un compte de stockage automatiquement, consultez [configurer un nouveau compte de stockage pour le service](#configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
> Si vous avez activé la création automatique d’un compte de stockage, allez à [étape 2 : obtenir la clé d’inscription du service](#step-2:-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Étape 2 : Obtenir la clé d’inscription de service

Une fois que le service Gestionnaire de StorSimple est en cours d’exécution, vous devez obtenir la clé d’inscription du service. Cette clé est utilisée pour inscrire et vous connecter votre dispositif de StorSimple avec le service.

Effectuez les opérations suivantes dans le portail classique Azure.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Étape 3 : Configurer et enregistrer le périphérique par le biais de Windows PowerShell pour StorSimple

> [AZURE.IMPORTANT] Avant d’effectuer cette configuration, débranchez toutes les interfaces de réseau autres que les données 0 sur (actifs et passifs) pour les contrôleurs.

Utiliser Windows PowerShell pour StorSimple pour effectuer la configuration initiale de votre périphérique StorSimple comme expliqué dans la procédure suivante. Vous devez utiliser un logiciel d’émulation de terminal pour terminer cette étape. Pour plus d’informations, consultez [PuTTY utiliser pour se connecter à la console série du périphérique](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Étape 4 : Exécutez le programme d’installation de périphériques

Pour la configuration des périphériques de votre périphérique StorSimple, vous devez :

- Configurer le serveur DNS secondaire.
- Activer iSCSI sur au moins une interface réseau.
- Affecter une adresse IP fixe sur les deux contrôleurs.

Effectuez les opérations suivantes dans le portail Azure classique pour terminer l’installation de périphériques.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Une fois la configuration du périphérique terminée, vous devez rechercher les mises à jour et si possible, installez les mises à jour. Les mises à jour peuvent prendre plusieurs heures. Suivez les instructions de [Rechercher et d’appliquer des mises à jour](#scan-for-and-apply-updates).


## <a name="step-5-create-a-volume-container"></a>Étape 5 : Créer un conteneur de volume

Un conteneur de volume a compte de stockage, de la bande passante et de paramètres de cryptage pour tous les volumes qu’il contient. Vous devez créer un conteneur de volume avant de commencer la mise en service des volumes sur le périphérique StorSimple.

Effectuez les opérations suivantes dans le portail classique Azure pour créer un conteneur de volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Étape 6 : Création d’un volume

Après avoir créé un conteneur de volume, vous pouvez prévoir un volume de stockage sur le périphérique StorSimple pour vos serveurs. Effectuez les opérations suivantes dans le portail classique Azure pour créer un volume.

> [AZURE.IMPORTANT] Gestionnaire de StorSimple pouvez créer des volumes de provisionnée uniquement.  Vous ne pouvez pas créer de volumes à provisionnement totalement ou partiellement.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Étape 7 : Montage, initialiser et formatage d’un volume

> [AZURE.IMPORTANT]

> - Pour la haute disponibilité de votre solution de StorSimple, nous vous conseillons de configurer MPIO sur votre hôte Windows Server (facultatif) avant la configuration iSCSI sur votre hôte Windows Server. Configuration de MPIO sur les serveurs hôtes permet de garantir que les serveurs peuvent tolérer un lien, un réseau ou une défaillance de l’interface.

> - Pour des instructions d’installation et de configuration de MPIO et iSCSI, accédez à [Configuration de MPIO pour votre périphérique StorSimple](storsimple-configure-mpio-windows-server.md). Il inclut également les étapes pour le montage, initialiser et formater des volumes de StorSimple.

Si vous décidez de ne pas configurer de MPIO, effectuez les opérations suivantes pour monter, initialiser et formater les volumes StorSimple.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Étape 8 : Effectuer une sauvegarde

Les sauvegardes protègent le point-à-temps des volumes et améliorent la capacité de restauration tout en réduisant les délais de restauration. Vous pouvez prendre deux types de sauvegarde de votre périphérique StorSimple : snapshots locaux et des instantanés de nuage. Chacun de ces types de sauvegarde peut être **planifiée** ou **manuelle**.

Effectuez les opérations suivantes dans le portail Azure classique pour créer une sauvegarde planifiée.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Vous pouvez prendre un manuel de sauvegarde à tout moment. Pour les procédures, passez à [Création d’une sauvegarde manuelle](#Create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurer un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative dont vous avez besoin pour effectuer uniquement si vous n’avez pas activé la création automatique d’un compte de stockage à votre service. Un compte de stockage Microsoft Azure est requis pour créer un conteneur de volume StorSimple.

Si vous devez créer un compte de stockage Azure dans une autre région, reportez-vous à la section [Sur les comptes de stockage Azure](../storage/storage-create-storage-account.md) pour obtenir des instructions pas à pas.

Effectuez les opérations suivantes dans le portail Azure classique, sur la page **service de gestionnaire de StorSimple** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>PuTTY permet de se connecter à la console série du périphérique

Pour vous connecter à Windows PowerShell pour StorSimple, vous devez utiliser un logiciel d’émulation de terminal tel que PuTTY. Vous pouvez utiliser PuTTY lorsque vous accédez à l’unité directement par l’intermédiaire de la console série, ou en ouvrant une session telnet à partir d’un ordinateur distant.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Rechercher et appliquer des mises à jour

Mise à jour de votre périphérique peut durer de 1 à 4 heures. Effectuez les opérations suivantes pour rechercher et appliquer des mises à jour sur votre appareil.

> [AZURE.NOTE] Si vous avez une passerelle configurée sur une interface réseau autres que les données de 0, vous devez désactiver les interfaces de réseau de données 2 et données 3 avant d’installer la mise à jour. Accédez à **périphériques > configurer** et désactiver les interfaces de données 2 et données 3. Vous devez réactiver ces interfaces une fois que le périphérique est mis à jour.

#### <a name="to-update-your-device"></a>Mise à jour de votre périphérique
1.  Sur la page de **Démarrage rapide** de périphérique, cliquez sur **périphériques**. Sélectionnez le périphérique physique et cliquez sur **Maintenance** puis cliquez sur **Rechercher les mises à jour**.  
2.  Un travail d’analyse des mises à jour disponibles est créé. Si les mises à jour sont disponibles, l' **Analyse des mises à jour** se modifie pour **Installer les mises à jour**. Cliquez sur **installer les mises à jour**. Vous pouvez être invité à désactiver les données 2 et données 3 avant d’installer les mises à jour. Vous devez désactiver ces interfaces réseau ou les mises à jour peuvent échouer.
3.  Une tâche de mise à jour sera créée. Surveiller l’état de votre mise à jour en accédant aux **projets**.

    > [AZURE.NOTE] Lors du démarrage de la tâche de mise à jour, il affiche immédiatement le statut de 50 %. L’état passe ensuite à 100 pour cent uniquement après que la tâche de mise à jour est terminée. Il n’y a aucun état en temps réel pour le processus de mises à jour.

4.  Une fois que le périphérique est correctement mis à jour, activer les interfaces de réseau de données 2 et données 3 si elles ont été désactivées.



## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtenir le nom qualifié d’un hôte Windows Server

Effectuez les opérations suivantes pour obtenir l’iSCSI nom qualifié (IQN) d’un hôte Windows qui exécute Windows Server 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]


## <a name="create-a-manual-backup"></a>Créer une sauvegarde manuelle

Effectuez les opérations suivantes dans le portail Azure classique pour créer une sauvegarde manuelle de la demande pour un seul volume de votre périphérique StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## <a name="next-steps"></a>Étapes suivantes

- Configuration d’un [périphérique virtuel](storsimple-virtual-device-u2.md).

- Utilisez le [Gestionnaire de StorSimple service](https://msdn.microsoft.com/library/azure/dn772396.aspx) pour gérer le périphérique StorSimple.
