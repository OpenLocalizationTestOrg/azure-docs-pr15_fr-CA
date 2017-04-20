<properties
   pageTitle="Installez votre périphérique 8100 de StorSimple | Microsoft Azure"
   description="Explique comment décompresser, montage en rack et câble de votre périphérique StorSimple 8100 avant de déployer et de configurer le logiciel."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Décompresser, montable en rack et le câble de votre périphérique StorSimple 8100

## <a name="overview"></a>Vue d’ensemble

Votre 8100 de StorSimple Microsoft Azure est un boîtier unique, monté en rack de périphérique. Ce didacticiel explique comment décompresser, montable en rack et le câble le 8100 StorSimple périphérique matériel avant de configurer et de déployer le périphérique StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Décompression de votre périphérique StorSimple 8100

Les étapes suivantes fournissent des instructions claires et détaillées sur la façon de décompresser votre périphérique de stockage StorSimple 8100. Ce périphérique est fourni dans un coffret unique.

### <a name="prepare-to-unpack-your-device"></a>Préparer décompresser votre périphérique

Avant de décompresser votre périphérique, consultez les informations suivantes.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône de poids lourds](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

1. Assurez-vous que vous disposez de deux personnes disponibles pour gérer le poids du boîtier si elle gérée manuellement. Un boîtier entièrement configuré peut peser jusqu'à 32 kg (70 lb.).
1. Placez la zone sur une surface plane et niveau.

Ensuite, procédez comme suit pour décompresser votre périphérique.

#### <a name="to-unpack-your-device"></a>Pour décompresser votre périphérique

1. Vérifiez que la zone et la mousse d’emballage pour crushes, découpes, l’eau ou tout autre dommage évident. Si la zone ou l’emballage est gravement endommagé, n’ouvrez pas la zone. Veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à déterminer si le périphérique est en bon état de marche.

2. Déballer le carton. L’image suivante montre la vue décompressée de votre périphérique StorSimple.

     ![Décompresser votre périphérique de stockage](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Mode de décompression de votre périphérique de stockage**

     Étiquette | Description
     ----- | -------------
     1     | Boîte d’emballage
     2     | MOUSSE de bas
     3     | DISPOSITIF
     4     | MOUSSE supérieure
     5     | Boîte d’accessoires


3. Après la décompression de la zone, assurez-vous que vous avez :

   - 1 périphérique boîtier unique
   - 2 cordons d’alimentation
   - 1 câble Ethernet de rattachement
   - 2 câbles de console série
   - 1 convertisseur série-USB pour un accès série
   - 1 tournevis de T10 inviolable
   - QSFP 4-à-SFP + adaptateurs à utiliser avec les interfaces de réseau 10 Gigabit Ethernet
   - montage en rack 1 kit (2 rails de côté avec le matériel de montage)
   - Obtention de documentation de route

    Si vous n’avez pas reçu un des éléments ci-dessus, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md).

L’étape suivante consiste à votre périphérique montable en rack.

## <a name="rack-mount-your-storsimple-8100-device"></a>Votre périphérique StorSimple 8100 montable en rack

Suivez les étapes suivantes pour installer votre périphérique de stockage StorSimple 8100 dans un rack standard de 19 pouces avec avant et arrière publications. Le périphérique StorSimple 8100 a un seul boîtier principal.

L’installation se compose de plusieurs étapes, dont chacune est décrite dans les procédures suivantes.

> [AZURE.IMPORTANT]
Dispositifs de StorSimple doivent être monté en rack pour un fonctionnement correct.

### <a name="prepare-the-site"></a>Préparez le site

Le périphérique doit être installé dans un rack 19 pouces standard qui possède à la fois avant et arrière publications. Utilisez la procédure suivante pour vous préparer à l’installation du rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Pour préparer le site pour montage en rack

1. Assurez-vous que le périphérique est placé en toute sécurité sur un travail plat, stable et niveau de surface (ou similaire).

2. Vérifiez que le site dans lequel vous souhaitez configurer une alimentation CA standard à partir d’une source indépendante ou une unité de distribution d’alimentation (PDU) en rack avec une alimentation sans coupure (UPS).

3. Assurez-vous qu’un connecteur de 2U est disponible sur le rack dans lequel vous souhaitez monter le périphérique.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône de poids lourds](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

Assurez-vous que vous disposez de deux personnes disponibles pour gérer le poids si vous gérez manuellement le programme d’installation de périphérique. Un boîtier entièrement configuré peut peser jusqu'à 32 kg (70 lb.).

### <a name="rack-prerequisites"></a>Conditions préalables du rack

Le boîtier 8100 est conçu pour une installation dans un rack standard de 19 pouces armoire avec :

- Profondeur minimale de 27.84 pouces rack valider pour enregistrer.
- Poids maximal de 32 kg pour le périphérique
- Contre-pression maximale de 5 Pascal (0,5 mm de colonne d’eau).

### <a name="rack-mounting-rail-kit"></a>Kit de rails de montage en rack

Un ensemble de rails de montage est fourni pour une utilisation avec le rack 19 pouces. Les rails ont été testés pour gérer le poids maximal de boîtier. Ces rails permet également l’installation de plusieurs boîtiers sans aucune perte d’espace dans le rack.


#### <a name="to-install-the-device-on-the-rails"></a>Pour installer le périphérique sur les rails.

2. Effectuez cette étape uniquement si internes ne sont pas installés sur votre périphérique. En règle générale, les glissières internes sont installés en usine. Si les rails ne sont pas installés, puis installez les diapositives rail de gauche et de droite sur les côtés de l’enceinte du châssis. Qu’ils attachent à l’aide de six vis métriques de chaque côté. Pour faciliter l’orientation, les diapositives de chemin de fer sont marqués **LH – avant** et **RH – avant**et la fin qui est placée à l’arrière du boîtier a une extrémité conique.<br/>

    ![Attachement des diapositives de chemin de fer sur le châssis de boîtier](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
    **diapositives de rail interne d’attachement aux côtés de l’enceinte**

       Étiquette | Description
       ----- | -----------
       1     | M 3 x 4 vis-bouton
       2     | Diapositives du châssis

3. Joindre le rail de gauche externe et les assemblys externes droite aux membres vertical armoire rack. Les crochets sont marqués **LH**, **RH**et **ce face visible** pour vous guider tout au long de l’orientation correcte.

4. Recherchez les broches de rail à l’avant et l’arrière de l’assembly de chemin de fer. Étendez la glissière pour ajuster entre les publications en rack et insérez les chevilles dans l’avant et l’arrière post membre vertical trous. Assurez-vous que l’assembly de chemin de fer est de niveau.

5. Deux vis métriques fournis permet de sécuriser l’ensemble de rails dans le rack verticales membres. Utilisez une vis à l’avant et un à l’arrière.

6. Répétez ces étapes pour l’autre assembly de chemin de fer.<br/>

     ![Attachement des diapositives de chemin de fer dans rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Joindre des assemblys de rail externe au rack**

     Étiquette | Description
     ----- | -----------
     1     | Vis de serrage
     2     | Vis de post rack avant du carré-trou
     3     | Broches d’emplacement avant gauche de chemin de fer
     4     | Vis de serrage
     5     | Broches d’emplacement arrière gauche du chemin de fer


### <a name="mounting-the-device-in-the-rack"></a>Montage de l’unité dans le rack

À l’aide des rails du rack qui viennent d’être installés, effectuez les étapes suivantes pour monter le périphérique dans le rack.

#### <a name="to-mount-the-device"></a>Pour monter le périphérique

1. Avec un assistant, soulevez le boîtier et alignez-le sur les rails de rack.

2. Soigneusement Insérez le périphérique dans les rails et puis poussez le périphérique entièrement dans le rack armoire.<br/>

    ![Dispositif d’insertion dans le rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montage de l’unité dans le rack**


3. Supprimer les CAP de la bride avant droit et gauche en tirant sur les points d’accès clients libres. Les embouts de bride simplement l’alignement sur les brides.

5. Sécurisez le boîtier dans le rack en installant une vis cruciforme fournie par le biais de chaque épaulement, gauche et droite.

4. Installez les embouts de bride en appuyant sur leur position et leur alignement en place.<br/>

     ![L’installation de majuscules de bride](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)

    **L’installation de points d’accès clients bride**

     Étiquette | Description
     ----- | -----------
     1     | Vis de fixation de boîtier

L’étape suivante consiste à câble de votre périphérique d’alimentation, de réseau et d’accès série.

## <a name="cable-your-storsimple-8100-device"></a>Câble de votre périphérique StorSimple 8100

Les procédures suivantes expliquent comment le câble de votre périphérique StorSimple 8100 pour alimentation, réseau et connexions série.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer le câblage de votre périphérique, vous devez :

- Votre périphérique de stockage, complètement décompressé et le monté en rack.

- 2 câbles d’alimentation fournie avec votre périphérique

- Accès à 2 unités de Distribution d’alimentation (recommandé).

- Câbles réseau

- Fourni des câbles série

- Convertisseur USB de série avec le pilote approprié est installé sur votre ordinateur (si nécessaire)

- Condition QSFP 4-à-SFP + adaptateurs à utiliser avec les interfaces de réseau 10 Gigabit Ethernet

- [Matériel pris en charge pour les interfaces de réseau 10 Gigabit Ethernet de votre périphérique StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### <a name="power-cabling"></a>Câblage d’alimentation

Votre périphérique inclut redondants alimentation et Modules de refroidissement (PCM pour). Les PCMs doivent être installés et connectés à plusieurs sources d’alimentation pour garantir une haute disponibilité.

Effectuez les opérations suivantes pour le câble de votre périphérique d’alimentation.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Câblage réseau

Votre périphérique est une configuration active-de réserve : à un moment donné, un seul module de contrôleur est actif et le traitement de toutes les opérations de réseau et de disque lors de l’autre module de contrôleur est en veille. En cas de défaillance d’un contrôleur, le contrôleur de secours est activé immédiatement et continue de tous les disques et les opérations de réseau.

Pour prendre en charge ce basculement contrôleur redondant, vous avez besoin connecter votre réseau périphérique, comme décrit dans les étapes suivantes.

#### <a name="to-cable-for-network-connection"></a>Câble de connexion réseau

1. Votre périphérique a six interfaces réseau sur chaque contrôleur : quatre 1 Gbit/s, et les ports de deux 10 Gbit/s Ethernet. Identifier les ports de données différentes sur le fond de panier de votre périphérique.

    ![Fond de panier de périphérique 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Arrière de l’appareil, montrant les ports de données**

     Étiquette   | Description
     ------- | -----------
     0,1,4,5 |  Interfaces de réseau 1 Gigabit Ethernet
     2, 3     | Interfaces de réseau 10 Gigabit Ethernet
     6       | Ports série

2. Consultez le diagramme suivant pour le câblage réseau. (La configuration réseau minimale est indiquée par des lignes bleues continues. Configuration supplémentaire requise pour les performances et la disponibilité est indiquée par des lignes pointillées.)


    ![Câble de votre périphérique de 2U pour réseau](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Câblage pour votre périphérique réseau**


  	|Étiquette | Description |
  	|----- | ----------- |
  	| A    | Réseau local avec accès à Internet |
  	| B    | Contrôleur 0 |
  	| C    | PCM 0 |
  	| D    | Contrôleur 1 |
  	| E    | PCM 1 |
  	| F, G | Hôtes |
  	| 0-5  | Interfaces réseau |



Lorsque le dispositif de câblage, la configuration minimale requiert :


- Au moins deux interfaces réseau connecté sur chaque contrôleur avec un accès de cloud et un pour iSCSI. Les données 0 port est automatiquement activé et configuré via la console série du périphérique. Outre les données 0, un autre port de données doit également être configuré via le portail classique Azure. Dans ce cas, se connecter aux données 0 port vers le principal (réseau LAN avec accès à Internet). Les autres ports de données peuvent être connectés au segment iSCSI du SAN/LAN (VLAN) du réseau, en fonction du rôle prévu.

- Des interfaces identiques sur chaque contrôleur connecté au même réseau afin de garantir la disponibilité en cas de basculement du contrôleur. Par exemple, si vous choisissez de vous connecter de données 0 et 3 de données pour un des contrôleurs, vous devez connecter les données correspondantes 0 et 3 de données sur l’autre contrôleur.

Gardez à l’esprit pour la haute disponibilité et performances :


- Lorsque cela est possible, vous devez configurer une paire de l’interface réseau pour l’accès de nuage (1 Gigabit Ethernet) et une autre paire pour iSCSI (10 GbE recommandé) sur chaque contrôleur.

- Lorsque cela est possible, connecter les interfaces réseau de chaque contrôleur à deux commutateurs différents afin de garantir la disponibilité par rapport à une défaillance du commutateur. La figure illustre le deux 10 interfaces réseau du Gigabit Ethernet, données 2 et données 3, à partir de chaque contrôleur connectée à deux commutateurs différents.

Pour plus d’informations, reportez-vous aux **interfaces réseau** selon les [exigences de haute disponibilité pour votre périphérique StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Si vous utilisez les émetteurs-récepteurs SFP + avec vos interfaces de réseau 10 Gigabit Ethernet, utiliser le QSFP fourni-SFP + cartes. Pour plus d’informations, accédez au [matériel pris en charge pour les interfaces de réseau 10 Gigabit Ethernet sur le périphérique StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).



### <a name="serial-port-cabling"></a>Câblage du port série

Effectuez les étapes suivantes pour votre port série du câble.

#### <a name="to-cable-for-serial-connection"></a>Câble de connexion série

1. Votre périphérique a un port série sur chaque contrôleur qui est identifiée par une icône représentant une clé. Reportez-vous à l’illustration dans la section de [câblage réseau](#network-cabling) pour localiser des ports série sur le fond de panier de votre périphérique.

2. Identifiez le contrôleur actif sur votre dispositif du fond de panier. Un voyant bleu clignotant indique que le contrôleur est actif.

3. Utilisez les câbles séries fournis (si nécessaire, le convertisseur USB / série pour votre ordinateur portable) et connecter votre console ou (à l’aide de l’émulation de terminal sur le périphérique) à la voie série du contrôleur active.

4. Installer les pilotes USB-série (fournis avec le périphérique) sur votre ordinateur.

5. Configurer la connexion série comme suit : 115 200 bauds, 8 bits de données, 1 bit d’arrêt, pas de parité et de contrôle de flux la valeur None.

6. Vérifiez que la connexion fonctionne en appuyant sur entrée sur la console. Un menu de console série doit apparaître.

>[AZURE.NOTE] **Gestion des pannes**: lorsque le périphérique est installé dans un centre de données distant ou dans une salle d’ordinateurs avec un accès limité, assurez-vous que les connexions séries sur les deux contrôleurs sont toujours connectées à un commutateur de console série ou d’un équipement similaire. Ainsi, contrôle d’out-of-band à distance et de prise en charge des opérations s’il existe des ruptures de réseau ou de défaillances inattendues.

Votre périphérique est maintenant câblé pour alimentation, accès réseau et connectivité en série. L’étape suivante consiste à configurer le logiciel et le déploiement de votre périphérique.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer et configurer votre périphérique de StorSimple local](storsimple-deployment-walkthrough.md).
