<properties 
   pageTitle="Périphérique virtuel de StorSimple mise à jour 2 | Microsoft Azure"
   description="Découvrez comment créer, déployer et gérer un périphérique virtuel de StorSimple dans un réseau virtuel Microsoft Azure. (S’applique à la mise à jour de StorSimple 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Déployer et gérer un périphérique virtuel de StorSimple dans Azure


##<a name="overview"></a>Vue d’ensemble
Le périphérique virtuel de série 8000 de StorSimple est une fonction supplémentaire qui est fourni avec votre solution Microsoft Azure StorSimple. Le périphérique virtuel StorSimple s’exécute sur un ordinateur virtuel dans un réseau virtuel Microsoft Azure, et vous pouvez l’utiliser pour sauvegarder et cloner des données à partir de vos hôtes. Ce didacticiel décrit comment déployer et gérer un périphérique virtuel dans Azure et est applicable à tous les périphériques virtuels exécutant la version 2 de la mise à jour du logiciel et inférieur.


#### <a name="virtual-device-model-comparison"></a>Comparaison des modèles de périphériques virtuels

Le périphérique virtuel StorSimple est disponible en deux modèles, un 8010 standard (anciennement appelé la 1100) et une prime 8020 (introduit dans la mise à jour 2). Une comparaison des deux modèles est sous forme de tableau ci-dessous.


| Modèle de périphérique          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacité maximale**      | 30 TO                                                                     | 64 TO                                                                                                                                |
| **Azure VM**              | Standard_A3 (4 cœurs, 7 Go de mémoire)                                                                      | Standard_DS3 (4 cœurs, 14 Go de mémoire)                                                                                                                          |
| **Compatibilité des versions** | Les versions en cours d’exécution avant mise à jour de 2 ou version ultérieure                                             | Versions de mise à jour en cours d’exécution 2 ou version ultérieure                                                                                                  |
| **Disponibilité dans la région**   | Toutes les régions Azure                                                         | Régions Azure qui prennent en charge le stockage de la prime<br></br>Pour obtenir la liste des régions, consultez [prise en charge des régions pour 8020](#supported-regions-for-8020) |
| **Type de stockage**          | Utilise le stockage Azure Standard pour les disques locaux<br></br> Découvrez comment [créer un compte de stockage Standard]() | Utilise le stockage Azure Premium pour disques locaux<sup>2</sup> <br></br>Découvrez comment [créer un compte de stockage de la prime](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **Guide de la charge de travail**     | Élément de récupération au niveau des fichiers à partir de sauvegardes                                              | Développement en nuage et de tester des scénarios, une latence faible, charge de travail performances <br></br>Périphérique secondaire de reprise après sinistre                                                                                            |
 
<sup>1</sup> *anciennement la 1100*.

<sup>2</sup> *à la fois le 8010 et 8020 utilisent le stockage Azure Standard pour la couche de nuage. La différence n’existe que dans le niveau local de l’appareil*.

#### <a name="supported-regions-for-8020"></a>Régions pris en charge pour 8020

Les zones de stockage de prime qui sont actuellement pris en charge pour 8020 sont sous forme de tableau ci-dessous. Cette liste sera continuellement mis à jour comme stockage de prime devient disponible dans plusieurs régions. 

| S. Aucune.                                                  | Actuellement pris en charge dans les régions |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | États-Unis centre                     |
| 2                                                       |  Les États-Unis                       |
| 3                                                       |  Les États-Unis 2                     |
| 4                                                       | États-Unis Ouest                        |
| 5                                                       | Europe du Nord                   |
| 6                                                       | Europe de l’ouest                    |
| 7                                                       | Asie du Sud-est                 |
| 8                                                       | Nord-est du Japon                     |
| 9                                                       | Ouest du Japon                     |
| 10                                                      | Est de l’Australie                 |
| 11                                                      | Sud-est de l’Australie *           |
| 12                                                      | Asie de l’est *                     |
| 13                                                      | Sud-US Central *              |

* Stockage de premium a été lancé récemment dans ces zones géographiques.

Cet article décrit le processus pas à pas du déploiement d’un périphérique virtuel de StorSimple dans Azure. Après avoir lu cet article, vous allez :

- Comprendre comment le périphérique virtuel diffère de l’unité physique.

- Être en mesure de créer et de configurer le périphérique virtuel.

- Se connecter au périphérique virtuel.

- Apprenez à travailler avec le périphérique virtuel.

Ce didacticiel s’applique à tous les périphériques virtuels StorSimple exécute la mise à jour 2 et versions ultérieures. 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Comment le périphérique virtuel diffère de l’unité physique

Le périphérique virtuel StorSimple est une version de logiciel uniquement de StorSimple qui s’exécute sur un seul nœud dans une Machine virtuelle de Microsoft Azure. Le périphérique virtuel prend en charge les scénarios de reprise après sinistre dans lequel votre périphérique physique n’est pas disponible et est approprié pour une utilisation dans la récupération au niveau de l’élément à partir de sauvegardes, de reprise après sinistre sur site et les scénarios de test et développement nuage.

#### <a name="differences-from-the-physical-device"></a>Différences par rapport à l’unité physique

Le tableau suivant montre quelques différences fondamentales entre le périphérique virtuel StorSimple et le périphérique physique StorSimple.

|                             | Périphérique physique                                          | Périphérique virtuel                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Emplacement**                    | Se trouve dans le centre de données.                               | S’exécute dans Azure.                                                                            |
| **Interfaces réseau**          | A des interfaces réseau six : données de 0 à 5 des données.                  | Possède une seule interface de réseau : données 0.                                        |
| **Inscription**                | Enregistré au cours de l’étape de configuration.                | L’inscription est une tâche séparée.                                                          |
| **Clé de cryptage de données de service** | Régénérer sur le périphérique physique, puis mettez à jour le périphérique virtuel avec la nouvelle clé.           | Ne peut pas régénérer à partir du périphérique virtuel. |


## <a name="prerequisites-for-the-virtual-device"></a>Conditions préalables pour le périphérique virtuel

Les sections suivantes expliquent les conditions préalables de configuration pour votre périphérique virtuel StorSimple. Avant de déployer un périphérique virtuel, passez en revue les [Considérations de sécurité pour l’utilisation d’un périphérique virtuel](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Exigences d’Azure

Avant de vous mettre en service le périphérique virtuel, vous devez effectuer les préparations dans votre environnement Azure :

- Pour le périphérique virtuel, [configurer un réseau virtuel dans Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Si l’utilisation du stockage de prime, vous devez créer un réseau virtuel dans une région Azure qui prend en charge le stockage de la prime. Plus d’informations sur les [régions qui sont actuellement prises en charge pour 8020](#supported-regions-for-8020).
- Il est recommandé d’utiliser le serveur DNS par défaut fourni par Azure au lieu de spécifier votre propre nom de serveur DNS. Si le nom de votre serveur DNS n’est pas valide ou si le serveur DNS n’est pas en mesure de résoudre correctement les adresses IP, la création du périphérique virtuel échouera.
- Point-à-site et site-à-site sont facultatifs, mais pas obligatoire. Si vous le souhaitez, vous pouvez configurer ces options pour des scénarios plus avancés. 
- Vous pouvez créer des [ordinateurs virtuels Azure](../virtual-machines/virtual-machines-linux-about.md) (serveurs hôtes) dans le réseau virtuel que vous pouvez utiliser les volumes exposées par le périphérique virtuel. Ces serveurs doivent répondre aux conditions suivantes :                            
    - Être Windows ou des ordinateurs virtuels de Linux avec le logiciel iSCSI Initiator installé.
    - Être en cours d’exécution sur le même réseau virtuel en tant que le périphérique virtuel.
    - Se connecter à la cible iSCSI du périphérique virtuel par le biais de l’adresse IP interne du périphérique virtuel.

- Assurez-vous que vous avez configuré la prise en charge pour le trafic iSCSI et le cloud sur le même réseau virtuel.


#### <a name="storsimple-requirements"></a>Exigences de StorSimple

Vérifiez les mises à jour suivantes à votre service Azure StorSimple avant de créer un périphérique virtuel :


- Ajouter des [enregistrements de contrôle d’accès](storsimple-manage-acrs.md) pour les ordinateurs virtuels qui seront des serveurs hôtes pour votre périphérique virtuel.

- Utilisez un [compte de stockage](storsimple-manage-storage-accounts.md#add-a-storage-account) dans la même région que le périphérique virtuel. Les comptes de stockage dans des régions différentes peuvent entraîner une dégradation des performances. Vous pouvez utiliser un compte Standard ou Premium stockage avec le périphérique virtuel. Plus d’informations sur la création d’un [compte de stockage Standard]() ou un [compte de stockage de la prime](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Utilisez un compte de stockage différentes pour la création d’un périphérique virtuel de celui utilisé pour vos données. En utilisant le même compte de stockage peut entraîner une dégradation des performances.

Assurez-vous que vous disposez des informations suivantes avant de commencer :

- Votre Azure classique compte de portail avec les informations d’accès.

- Une copie de la clé de cryptage de données de service de votre périphérique physique.


## <a name="create-and-configure-the-virtual-device"></a>Créer et configurer le périphérique virtuel

Avant d’effectuer ces procédures, vérifiez que vous remplissez les [conditions requises pour le périphérique virtuel](#prerequisites-for-the-virtual-device). 

Après avoir créé un réseau virtuel, configuré un service de gestionnaire de StorSimple et enregistré votre périphérique physique de la StorSimple avec le service, vous pouvez utiliser les étapes suivantes pour créer et configurer un périphérique virtuel StorSimple. 

### <a name="step-1-create-a-virtual-device"></a>Étape 1 : Créer un périphérique virtuel

Procédez comme suit pour créer le périphérique virtuel StorSimple.

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

En cas d’échec de la création du périphérique virtuel dans cette étape, vous peut-être pas de connectivité à Internet. Pour plus d’informations, consultez pour [résoudre les problèmes de connectivité Internet](#troubleshoot-internet-connectivity-errors) lorsque creatig un périphérique virtuel.


### <a name="step-2-configure-and-register-the-virtual-device"></a>Étape 2 : Configurer et inscrire l’appareil virtuel

Avant de commencer cette procédure, assurez-vous que vous disposez d’une copie de la clé de cryptage de données de service. La clé de cryptage de données de service a été créée lorsque vous avez configuré votre premier périphérique StorSimple vous ont invité à l’enregistrer dans un emplacement sécurisé. Si vous ne disposez pas d’une copie de la clé de cryptage de données de service, vous devez contacter le Support Microsoft pour obtenir de l’aide.

Effectuez les étapes suivantes pour configurer et inscrire votre périphérique virtuel StorSimple.
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Étape 3 : (Facultatif) modifier les paramètres de configuration de périphérique

La section suivante décrit les paramètres de configuration de périphérique nécessaire pour le StorSimple périphérique virtuel si vous souhaitez utiliser CHAP, Gestionnaire de snapshots StorSimple ou modifier le mot de passe administrateur de périphérique.

#### <a name="configure-the-chap-initiator"></a>Configurez l’initiateur CHAP

Ce paramètre contient les informations d’identification que votre périphérique virtuel (cible) s’attend à partir des initiateurs (serveurs) qui tentent d’accéder aux volumes. Les initiateurs fournit un nom d’utilisateur CHAP et un mot de passe CHAP pour s’identifier sur votre appareil au cours de cette authentification. Pour obtenir la procédure détaillée, consultez [Configurer le protocole CHAP pour votre périphérique](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurer la cible CHAP

Ce paramètre contient les informations d’identification que votre périphérique virtuel utilise lorsqu’un initiateur CHAP activé demande l’authentification mutuelle ou bidirectionnelle. Votre périphérique virtuel utilisera inverser CHAP nom d’utilisateur et mot de passe inverser CHAP pour s’identifier au cours du processus d’authentification de l’initiateur. Notez que les paramètres CHAP cible sont des paramètres globaux. Lorsque ces fichiers sont appliqués, tous les volumes connectés au périphérique de stockage virtuel utilisent l’authentification CHAP. Pour obtenir la procédure détaillée, consultez [Configurer le protocole CHAP pour votre périphérique](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurer le mot de passe du Gestionnaire de snapshots de StorSimple

Gestionnaire de snapshots StorSimple logiciel réside sur votre hôte Windows et permet aux administrateurs de gérer des sauvegardes de votre périphérique StorSimple sous la forme d’un local et de snapshots en nuage.

>[AZURE.NOTE] Pour le périphérique virtuel, votre hôte Windows est une machine virtuelle Azure.

Lors de la configuration d’un périphérique dans le Gestionnaire de l’instantané StorSimple, le système vous demandera de fournir l’adresse IP du périphérique StorSimple et le mot de passe pour authentifier votre périphérique de stockage. Pour obtenir la procédure détaillée, consultez [configurer le Gestionnaire de Snapshot StorSimple de mot de passe](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Modifier le mot de passe administrateur 

Lorsque vous utilisez l’interface de Windows PowerShell pour accéder au périphérique virtuel, vous devrez entrer un mot de passe administrateur. Pour la sécurité de vos données, vous devez modifier ce mot de passe avant de pouvoir utiliser le périphérique virtuel. Pour obtenir la procédure détaillée, accédez à [configuration administrateur mot de passe](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Se connecter à distance au périphérique virtuel
Accès à distance à votre périphérique virtuel via l’interface de Windows PowerShell n’est pas activé par défaut. Vous devez tout d’abord activer la gestion à distance sur le périphérique virtuel et puis l’activer sur le client qui sera utilisé pour accéder à votre équipement virtuel.

Le processus en deux étapes pour se connecter à distance est détaillé ci-dessous.

### <a name="step-1-configure-remote-management"></a>Étape 1 : Configurer la gestion à distance

Effectuez les étapes suivantes pour configurer la gestion à distance de votre périphérique virtuel StorSimple.

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Étape 2 : Accéder à distance au périphérique virtuel

Une fois que vous avez activé la gestion à distance sur la page de configuration de périphérique StorSimple, vous pouvez utiliser l’accès distant de Windows PowerShell pour se connecter au périphérique virtuel à partir d’un autre ordinateur virtuel à l’intérieur du même réseau virtuel ; par exemple, vous pouvez vous connecter à partir de la machine virtuelle que vous configuré et utilisé pour la connexion iSCSI de l’hôte. Dans la plupart des déploiements, vous vous avez déjà ouvert un point de terminaison public pour accéder à votre machine virtuelle que vous pouvez utiliser pour accéder à l’unité virtuelle de l’hôte.

>[AZURE.WARNING] **Pour plus de sécurité, nous recommandons vivement que vous utilisez HTTPS pour vous connecter aux points de terminaison et supprimez les points de terminaison après avoir terminé votre session à distance de PowerShell.**

Vous devez suivre les procédures de [connexion à distance à votre périphérique de StorSimple](storsimple-remote-connect.md) pour définir une communication à distance pour votre périphérique virtuel.

## <a name="connect-directly-to-the-virtual-device"></a>Se connecter directement au périphérique virtuel

Vous pouvez également vous connecter directement au périphérique virtuel. Si vous souhaitez vous connecter directement au périphérique virtuel à partir d’un autre ordinateur en dehors du réseau virtuel ou à l’extérieur de l’environnement Microsoft Azure, vous devez créer des points de terminaison supplémentaires comme décrit dans la procédure suivante. 

Procédez comme suit pour créer un point de terminaison public sur le périphérique virtuel.

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Nous vous recommandons de vous connectez à partir d’un autre ordinateur virtuel à l’intérieur du même réseau virtuel car cela réduit le nombre de points de terminaison publics sur votre réseau virtuel. Lorsque vous utilisez cette méthode, vous simplement vous connecter à l’ordinateur virtuel via une session Bureau à distance et configurez cet ordinateur virtuel pour une utilisation comme vous le feriez pour tout autre client Windows sur un réseau local. Vous n’avez pas besoin d’ajouter le numéro de port publics parce que le port sera déjà connu.

## <a name="work-with-the-storsimple-virtual-device"></a>Travailler avec le périphérique virtuel StorSimple

Maintenant que vous avez créé et configuré le périphérique virtuel StorSimple, vous êtes prêt à commencer à travailler avec lui. Vous pouvez travailler avec des conteneurs de volumes, les volumes et les règles de sauvegarde sur un périphérique virtuel comme vous le feriez sur un périphérique physique de la StorSimple ; la seule différence est que vous devez vous assurer que vous sélectionnez le périphérique virtuel à partir de votre liste de périphériques. Pour [utiliser le service de gestionnaire de StorSimple pour gérer un périphérique virtuel](storsimple-manager-service-administration.md) pour obtenir des procédures différentes tâches de gestion pour le périphérique virtuel, reportez-vous à la section.

Les sections suivantes présentent les différences que vous rencontrerez lorsque vous travaillez avec le périphérique virtuel.

### <a name="maintain-a-storsimple-virtual-device"></a>Mettre à jour d’un périphérique virtuel de StorSimple

Car il s’agit d’un périphérique logiciel uniquement, la mise à jour pour le périphérique virtuel est minime par rapport à la mise à jour pour le périphérique physique. Vous disposez des options suivantes :

- **Mises à jour logicielles** , vous pouvez afficher la date à laquelle le logiciel a été mis à jour, ainsi que toute mise à jour de messages d’état. Vous pouvez utiliser le bouton **Rechercher les mises à jour** en bas de la page pour effectuer une analyse manuelle si vous souhaitez vérifier les nouvelles mises à jour. Si les mises à jour sont disponibles, cliquez sur **Installer les mises à jour** à installer. Étant donné seulement une seule interface sur le périphérique virtuel, cela signifie qu’il y aura une interruption de service léger lorsque les mises à jour sont appliquées. Le périphérique virtuel va arrêter et redémarrer les (si nécessaire) pour appliquer les mises à jour qui ont été publiés. Pour une procédure pas à pas, passez à la [mise à jour de votre périphérique](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Prise en charge de package** – vous pouvez créer et télécharger un package de prise en charge du Support Microsoft pour résoudre des problèmes avec votre périphérique virtuel. Pour une procédure pas à pas, consultez pour [créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Comptes de stockage pour un périphérique virtuel

Comptes de stockage sont créés pour une utilisation par le service Gestionnaire de StorSimple, par le périphérique virtuel et par le périphérique physique. Lorsque vous créez vos comptes de stockage, nous vous recommandons d’utiliser un identificateur de région dans le nom convivial pour aider à garantir que la région est cohérente dans l’ensemble de tous les composants du système. Pour un périphérique virtuel, il est important que tous les composants dans la même région afin d’éviter les problèmes de performances.

Pour une procédure pas à pas, consultez [Ajouter un compte de stockage](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Désactiver un périphérique virtuel de StorSimple

La désactivation d’un périphérique virtuel supprime l’ordinateur virtuel et les ressources créées lorsqu’il a été mis en service. Une fois le périphérique virtuel est désactivé, elle ne peut pas être restaurée à son état précédent. Avant de désactiver le périphérique virtuel, assurez-vous d’arrêter ou de supprimer des clients et des hôtes qui en dépendent.

La désactivation d’un périphérique virtuel entraîne les actions suivantes :

- Le périphérique virtuel est supprimé.

- Le disque du système d’exploitation et les disques de données créés pour le périphérique virtuel sont supprimées.

- Le service hébergé et le réseau virtuel créé au cours de la mise en service sont conservés. Si vous ne les utilisez pas, vous devez les supprimer manuellement.

- Instantanés de nuage créés pour le périphérique virtuel sont conservés.

Pour une procédure pas à pas, consultez la rubrique [désactiver et le supprimer de votre périphérique StorSimple](storsimple-deactivate-and-delete-device.md).

Dès que le périphérique virtuel est indiqué comme désactivés dans la page service de gestionnaire de StorSimple, vous pouvez supprimer le périphérique virtuel à partir de la liste des périphériques dans la page **périphériques** .


### <a name="start-stop-and-restart-a-virtual-device"></a>Démarrer, arrêter et redémarrer un périphérique virtuel
À la différence de l’unité physique StorSimple, n’est pas alimenté sur ou mise hors tension de bouton pour exécuter un push sur un périphérique virtuel StorSimple. Toutefois, il peut arriver où vous devez arrêter et redémarrer le périphérique virtuel. Par exemple, certaines mises à jour peuvent nécessiter le redémarrage de la machine virtuelle pour terminer le processus de mise à jour. Le moyen le plus simple vous permet de démarrer, arrêter et redémarrer un périphérique virtuel est d’utiliser la Console de gestion des Machines virtuelles.

Lorsque vous examinez la Console de gestion, l’état du périphérique virtuel est **en cours d’exécution** , car il est démarré par défaut après sa création. Vous pouvez démarrer, arrêter et redémarrer un ordinateur virtuel à tout moment.

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Rétablir les paramètres par défaut

Si vous décidez que vous voulez uniquement commencer sur avec votre périphérique virtuel, simplement désactiver et supprimer et créer un nouveau. Comme lorsque votre périphérique physique est remis, votre nouveau périphérique virtuel n’aura pas les mises à jour installées ; Par conséquent, assurez-vous de vérifier les mises à jour avant de l’utiliser.


## <a name="fail-over-to-the-virtual-device"></a>Basculer vers le périphérique virtuel

De reprise après sinistre (DR) est un des scénarios clés qui a été conçu pour le périphérique virtuel StorSimple. Dans ce scénario, le périphérique physique de StorSimple ou d’un centre de données entier ne peut pas être disponible. Heureusement, vous pouvez utiliser un périphérique virtuel pour les opérations de restauration dans un autre emplacement. Lors de la reprise après sinistre, les conteneurs de volume du périphérique source changent de propriétaire et sont transférées vers le périphérique virtuel. Les conditions requises pour la reprise après sinistre sont que le périphérique virtuel a été créé et configuré, tous les volumes dans le conteneur de volume ont été prises en mode hors connexion, et le conteneur de volume est associé à un instantané du nuage.

>[AZURE.NOTE] 
>
> - Lors de l’utilisation d’un périphérique virtuel comme périphérique secondaire pour la reprise après sinistre, gardez à l’esprit que le 8010 a 30 To de stockage Standard et 8020 a 64 To de stockage de la prime. Le périphérique virtuel du 8020 capacité plus élevé peut être plus adapté à un scénario de reprise après sinistre.
> - Vous ne pouvez pas le basculement sur incident ou un clone d’un dispositif de mise à jour 2 en cours d’exécution sur un périphérique exécutant un logiciel de 1 avant mise à jour. Vous pouvez cependant basculer sur un périphérique exécutant la mise à jour 2 pour un périphérique qui exécute la mise à jour 1 (1.1 ou 1.2)

Pour une procédure pas à pas, accédez au [basculement vers un périphérique virtuel](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## <a name="shut-down-or-delete-the-virtual-device"></a>Arrêter ou de supprimer le périphérique virtuel

Si vous avez précédemment configuré et que vous utilisé une StorSimple périphérique virtuel mais souhaitez plus accumuler de frais de calcul pour son utilisation, vous pouvez arrêter le périphérique virtuel. Arrêter le périphérique virtuel ne supprime pas son système d’exploitation ou les disques de données dans le stockage. Elle n’empêche pas les frais accumulés de votre abonnement, mais continueront de frais de stockage pour les disques du système d’exploitation et des données.

Si vous supprimez ou arrêtez le périphérique virtuel, il apparaît **hors ligne** dans la page de périphériques du service Gestionnaire de StorSimple. Vous pouvez choisir de désactiver ou supprimer le périphérique si vous souhaitez également supprimer les sauvegardes créées par le périphérique virtuel. Pour plus d’informations, consultez [désactiver et supprimer un périphérique StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>Résoudre les problèmes de connectivité Internet 

Lors de la création d’un périphérique virtuel, si il n’y a pas de connectivité à Internet, l’étape de création échoue. Pour résoudre l’échec est en raison de la connectivité Internet, effectuez les opérations suivantes dans le portail classique Azure :

1. Créer un ordinateur virtuel de Windows server 2012 dans Azure. Cet ordinateur virtuel doivent utiliser le même compte de stockage, le VNet et le sous-réseau utilisé par votre périphérique virtuel. Si vous avez déjà un hôte Windows Server existant dans Azure en utilisant le même compte de stockage, Vnet et sous-réseau, vous pouvez également l’utiliser pour résoudre les problèmes liés à la connectivité Internet.
2. Journal distant sur l’ordinateur virtuel créé à l’étape précédente. 
3. Ouvrez une fenêtre de commande à l’intérieur de la machine virtuelle (Win + R, puis tapez `cmd`).
4. Exécutez la commande suivante à l’invite.

    `nslookup windows.net`

5. Si `nslookup` échoue, Échec de connexion Internet empêche le périphérique virtuel à partir de l’inscription au service Gestionnaire de StorSimple. 
6. Apportez les modifications requises à votre réseau virtuel pour vous assurer que le périphérique virtuel est en mesure d’accéder aux sites Azure comme « Certificate Revocation Lists ».

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour gérer un périphérique virtuel](storsimple-manager-service-administration.md).
 
- Comprendre comment [restaurer un volume de StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md). 

