<properties
   pageTitle="Installez votre périphérique 8600 de StorSimple | Microsoft Azure"
   description="Explique comment décompresser, montage en rack et câble de votre périphérique StorSimple 8600 avant de déployer et de configurer le logiciel."
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
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Décompresser, montable en rack et le câble de votre périphérique StorSimple 8600

## <a name="overview"></a>Vue d’ensemble
Votre 8600 de StorSimple Microsoft Azure est un dispositif de double enceinte et se compose d’un serveur principal et un boîtier EBOD. Ce didacticiel explique comment décompresser, montable en rack et du matériel de périphérique de câble le 8600 StorSimple avant de configurent le logiciel StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Décompresser votre périphérique 8600 de StorSimple

Les étapes suivantes fournissent des instructions claires et détaillées sur la décompression de votre périphérique de stockage StorSimple 8600. Ce périphérique est livré en deux zones, l’une pour le boîtier principal et l’autre pour le boîtier EBOD. Ces deux zones sont ensuite placés dans une zone unique.

### <a name="prepare-to-unpack-your-device"></a>Préparer décompresser votre périphérique

Avant de décompresser votre périphérique, consultez les informations suivantes.


![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône de poids lourds](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

1. Assurez-vous que vous disposez de deux personnes disponibles pour gérer le poids de l’unité si elle gérée manuellement. Un boîtier entièrement configuré peut peser jusqu'à 32 kg (70 lb.).
1. Placez la zone sur une surface plane et niveau.

Ensuite, procédez comme suit pour décompresser votre périphérique.

#### <a name="to-unpack-your-device"></a>Pour décompresser votre périphérique

1. Vérifiez que la zone et la mousse d’emballage pour crushes, découpes, l’eau ou tout autre dommage évident. Si la zone ou l’emballage est gravement endommagé, n’ouvrez pas la zone. Veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à déterminer si le périphérique est en bon état de marche.

2. Ouvrez la boîte externe et puis retirez les deux cases correspondant aux principaux et de boîtiers EBOD. Vous pouvez maintenant décompresser le principal et les boîtiers EBOD. La figure suivante présente la vue décompressée de l’un des boîtiers.

    ![Décompresser votre périphérique de stockage](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Mode de décompression de votre périphérique de stockage**

     Étiquette | Description
     ----- | -------------
     1     | Boîte d’emballage
     2     | Câbles SAS (dans la barre d’état des câbles et les accessoires)
     3     | MOUSSE de bas
     4     | DISPOSITIF
     5     | MOUSSE supérieure
     6     | Boîte d’accessoires

3. Après avoir décompressé les deux cases, assurez-vous que vous avez :

  - 1 boîtier principal (le boîtier principal et un boîtier EBOD se trouvent dans deux zones distinctes)
  - Boîtier EBOD 1
  - 4 câbles d’alimentation, 2 dans chaque zone.
  - 2 câbles SAS (connecter le boîtier principal au boîtier de EBOD)
  - 1 câble Ethernet de rattachement
  - 2 câbles de console série
  - 1 convertisseur série-USB pour un accès série
  - QSFP 4-à-SFP + adaptateurs à utiliser avec les interfaces de réseau 10 Gigabit Ethernet
  - 2 kits de montage (4 rails de côté avec le matériel, 2 pour le boîtier principal et d’un boîtier EBOD de montage), du rack 1 dans chaque zone.
  - Obtention de documentation démarrée

    Si vous n’avez pas reçu un des éléments ci-dessus, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md).  

L’étape suivante consiste à votre périphérique montable en rack.

## <a name="rack-mount-your-storsimple-8600-device"></a>Votre périphérique StorSimple 8600 montable en rack

Suivez les étapes suivantes pour installer votre périphérique de stockage StorSimple 8600 dans un rack standard de 19 pouces avec avant et arrière publications. Ce périphérique est livré avec deux boîtiers : un boîtier principal et un boîtier EBOD. Ces deux éléments doivent être montés-rack.

L’installation se compose de plusieurs étapes, dont chacune est décrite dans les procédures suivantes.

> [AZURE.IMPORTANT]
Dispositifs de StorSimple doivent être monté en rack pour un fonctionnement correct.



### <a name="site-preparation"></a>Préparation du site

Les boîtiers doivent être installés dans un rack 19 pouces standard qui possède à la fois avant et arrière publications. Utilisez la procédure suivante pour vous préparer à l’installation du rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Pour préparer le site pour montage en rack

1. Assurez-vous que le principal et les boîtiers EBOD sont repos en toute sécurité sur une surface plate, stable et niveau de travail (ou similaire).

2. Vérifiez que le site dans lequel vous souhaitez configurer une alimentation CA standard à partir d’une source indépendante ou un unité de Distribution d’alimentation (PDU) de rack avec une alimentation sans coupure (UPS).

3. Assurez-vous que ce slot (2 X 2U) d’un 4U est disponible sur le rack dans lequel vous souhaitez monter les boîtiers.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône de poids lourds](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

 Assurez-vous que vous disposez de deux personnes disponibles pour gérer le poids si vous gérez manuellement le programme d’installation de périphérique. Un boîtier entièrement configuré peut peser jusqu'à 32 kg (70 lb.).

### <a name="rack-prerequisites"></a>Conditions préalables du rack

Les boîtiers sont conçus pour une installation dans un rack standard de 19 pouces armoire avec :

- Profondeur minimale de 27.84 pouces rack valider pour enregistrer
- Poids maximal de 32 kg pour le périphérique
- Contre-pression maximale de 5 Pascal (0,5 mm de colonne d’eau)

### <a name="rack-mounting-rail-kit"></a>Kit de rails de montage en rack

Vous trouverez un ensemble de rails de montage pour une utilisation avec le rack 19 pouces. Les rails ont été testés pour gérer le poids maximal de boîtier. Ces rails permet également l’installation de plusieurs boîtiers sans perte de l’espace dans le rack. Commencez par installer le boîtier EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Pour installer le boîtier de la EBOD sur les rails.

2. Effectuez cette étape uniquement si internes ne sont pas installés sur votre périphérique. En règle générale, les glissières internes sont installés en usine. Si les rails ne sont pas installés, puis installez les diapositives rail de gauche et de droite sur les côtés de l’enceinte du châssis. Qu’ils attachent à l’aide de six vis métriques de chaque côté. Pour faciliter l’orientation, les diapositives de chemin de fer sont marqués **LH – avant** et **RH – avant**et la fin qui est placée à l’arrière du boîtier a une extrémité conique.

    ![Attachement des diapositives de chemin de fer sur le châssis de boîtier](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Attachement des diapositives de chemin de fer sur les côtés de l’enceinte**

    Étiquette | Description
    ----- | -----------
    1     | M 3 x 4 vis-bouton
    2     | Diapositives du châssis

3. Attacher la glissière gauche et des assemblys de droite aux membres vertical armoire rack. Les crochets sont marqués **LH**, **RH**et **ce face visible** pour vous guider tout au long de bon sens.

4. Recherchez les broches de rail à l’avant et l’arrière de l’assembly de chemin de fer. Étendez la glissière pour ajuster entre les publications en rack et insérez les chevilles dans l’avant et à trous d’élément vertical post du rack arrière. Assurez-vous que l’assembly de chemin de fer est de niveau.

5. Sécuriser l’ensemble de rails dans le rack membres verticales à l’aide de deux vis métrique. Utilisez une vis à l’avant et un à l’arrière.

6. Répétez ces étapes pour l’autre assembly de chemin de fer.

     ![Attachement des diapositives de chemin de fer dans rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Joindre des assemblys de chemin de fer dans le rack**

     Étiquette | Description
     ----- | -----------
     1     | Vis de serrage
     2     | Vis de post rack avant du carré-trou
     3     | Broches d’emplacement de glissière avant à gauche
     4     | Vis de serrage
     5     | Broches d’emplacement des rails arrière gauche

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montage du boîtier EBOD dans le rack

À l’aide des rails du rack qui viennent d’être installés, procédez comme suit pour monter le boîtier EBOD dans le rack.

#### <a name="to-mount-the-ebod-enclosure"></a>Pour monter le boîtier EBOD

1. Avec un assistant, soulevez le boîtier et alignez-le sur les rails de rack.

2. Avec soin d’insérer le boîtier dans les rails et retirez-le complètement dans le rack armoire.

    ![Dispositif d’insertion dans le rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Le boîtier en rack de montage**

3. Supprimer les CAP de la bride avant droit et gauche en tirant sur les points d’accès clients libres. Les embouts de bride simplement l’alignement sur les brides.

4. Sécurisez le boîtier dans le rack en installant une vis cruciforme fournie par le biais de chaque épaulement, gauche et droite.

4. Installez les embouts de bride en appuyant sur leur position et leur alignement en place.

     ![L’installation de majuscules de bride](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **L’installation de points d’accès clients bride**

     Étiquette | Description
     ----- | -----------
     1     | Vis de fixation de boîtier


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Le boîtier principal dans le rack de montage

Lorsque vous avez terminé le montage du boîtier EBOD, vous devez monter le boîtier principal suivant les mêmes étapes.

> [AZURE.NOTE]
>
> - Il est possible d’avoir plusieurs emplacements vides dans le rack entre le boîtier principal et le boîtier EBOD.
> - Le câble SAS fourni 2m permet de connecter le boîtier principal pour le boîtier EBOD.
> - Il n’y a pas de contraintes sur le positionnement relatif de l’unité centrale à l’unité EBOD. Par conséquent, le boîtier principal peut être placé dans l’emplacement supérieur et le boîtier EBOD ci-dessous, ou vice versa.

L’étape suivante consiste à câble de votre périphérique d’alimentation, de réseau et d’accès série.

## <a name="cable-your-storsimple-8600-device"></a>Câble de votre périphérique StorSimple 8600

Les procédures suivantes expliquent comment le câble de votre périphérique StorSimple 8600 pour alimentation, réseau et connexions série.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer à le câble de votre périphérique, vous devez :

- Le boîtier principal et le boîtier EBOD, complètement décompressé
- 4 câbles d’alimentation (2 chaque pour le serveur principal et le boîtier EBOD) fourni avec votre périphérique
- 2 câbles SAS fournis avec le périphérique pour connecter le boîtier de la EBOD pour le boîtier principal
- Accès à 2 unités de Distribution d’alimentation (PDU) (recommandé)
- Câbles réseau
- Fourni des câbles série
- Convertisseur série-USB avec le pilote approprié est installé sur votre ordinateur (si nécessaire)
- Condition QSFP 4-à-SFP + adaptateurs à utiliser avec les interfaces de réseau 10 Gigabit Ethernet
- [Matériel pris en charge pour les interfaces de réseau 10 Gigabit Ethernet de votre périphérique StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS et le câble d’alimentation

Votre périphérique possède un boîtier principal et un boîtier EBOD. Cette opération nécessite les unités à être câblés ensemble d’alimentation et de connectivité de Serial Attached SCSI (SAS).

Lors de la configuration de ce périphérique pour la première fois, exécutez les étapes pour SAS câblage tout d’abord et puis suivez les étapes pour le câblage d’alimentation.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Câblage réseau

Votre périphérique est dans une configuration active-de réserve : à un moment donné, un seul module de contrôleur est actif et le traitement de toutes les opérations de réseau et de disque lors de l’autre module de contrôleur est en veille. En cas de défaillance d’un contrôleur, le contrôleur de secours immédiatement active et continue de toutes les opérations de mise en réseau et de disque.

Pour prendre en charge ce basculement contrôleur redondant, vous avez besoin connecter votre réseau périphérique, comme indiqué dans les étapes suivantes.

#### <a name="to-cable-for-network-connection"></a>Câble de connexion réseau

1. Votre périphérique a six interfaces réseau sur chaque contrôleur : quatre ports de 1 Gbit/s et deux 10 Gbit/s Ethernet. Reportez-vous à l’illustration suivante pour identifier les ports de données sur le fond de panier de votre périphérique.

     ![Fond de panier de périphérique 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Retour de votre périphérique, montrant les ports de données**

     Étiquette   | Description
     ------- | -----------
     0,1,4,5 |  Interfaces de réseau 1 Gigabit Ethernet
     2, 3     | Interfaces de réseau 10 Gigabit Ethernet
     6       | Ports série



1. Consultez le diagramme suivant pour le câblage réseau. (La configuration réseau minimale est indiquée par des lignes bleues continues. Haute disponibilité et des performances, une configuration supplémentaire requise est indiquée par des lignes pointillées.)

![Câble de votre périphérique de 4U de réseau](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Câblage pour votre périphérique réseau**

Étiquette | Description
----- | -----------
A    | Réseau local avec accès à Internet
B    | Contrôleur 0
C    | PCM 0
D    | Contrôleur 1
E    | PCM 1
F    | Contrôleur de EBOD 0
G    | Contrôleur de EBOD 1
H, I  | Hôtes (par exemple, les serveurs de fichiers)
0-5  | Interfaces réseau
6    | Boîtier principal
7    | Boîtier EBOD

Lorsque le dispositif de câblage, la configuration minimale requiert :


- Au moins deux interfaces réseau connecté sur chaque contrôleur avec un accès de cloud et un pour iSCSI. Les données 0 port est automatiquement activé et configuré via la console série du périphérique. Outre les données 0, un autre port de données doit également être configuré via le portail classique Azure. Dans ce cas, se connecter aux données 0 port vers le principal (réseau LAN avec accès à Internet). Les autres ports de données peuvent être connectés au segment iSCSI du SAN/LAN (VLAN) du réseau, en fonction du rôle prévu.

- Des interfaces identiques sur chaque contrôleur connecté au même réseau afin de garantir la disponibilité en cas de basculement du contrôleur. Par exemple, si vous choisissez de vous connecter de données 0 et 3 de données pour un des contrôleurs, vous devez connecter les données correspondantes 0 et 3 de données sur l’autre contrôleur.

Gardez à l’esprit pour la haute disponibilité et performances :


- Lorsque cela est possible, vous devez configurer une paire de l’interface réseau pour l’accès de nuage (1 Gigabit Ethernet) et une autre paire pour iSCSI (10 GbE recommandé) sur chaque contrôleur.

- Lorsque cela est possible, connecter les interfaces réseau de chaque contrôleur à deux commutateurs différents afin de garantir la disponibilité par rapport à une défaillance du commutateur. La figure illustre le deux 10 interfaces réseau du Gigabit Ethernet, données 2 et données 3, à partir de chaque contrôleur connectée à deux commutateurs différents. Pour plus d’informations, reportez-vous aux **interfaces réseau** selon les [exigences de haute disponibilité pour votre périphérique StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Si vous utilisez les émetteurs-récepteurs SFP + avec vos interfaces de réseau 10 Gigabit Ethernet, utiliser le QSFP fourni-SFP + cartes. Pour plus d’informations, accédez au [matériel pris en charge pour les interfaces de réseau 10 Gigabit Ethernet sur le périphérique StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### <a name="serial-port-cabling"></a>Câblage du port série

Effectuez les étapes suivantes pour votre port série du câble.

#### <a name="to-cable-for-serial-connection"></a>Câble de connexion série

1. Votre périphérique a un port série sur chaque contrôleur qui est identifiée par une icône représentant une clé. Pour rechercher les ports série, reportez-vous à l’illustration qui présente des ports de données à l’arrière de votre périphérique.

2. Identifiez le contrôleur actif sur votre dispositif du fond de panier. Un voyant bleu clignotant indique que le contrôleur est actif.

3. Utilisez le câble série fourni (si nécessaire, le convertisseur USB / série pour votre ordinateur portable) et connecter votre console ou (à l’aide de l’émulation de terminal sur le périphérique) à la voie série du contrôleur active.

4. Installer les pilotes USB-série (fournis avec le périphérique) sur votre ordinateur.

5. Configurer la connexion série comme suit :
   - 115 200 bauds
   - 8 bits de données
   - 1 bit d’arrêt
   - Pas de parité
   - Contrôle de flux de valeur **Aucun**

6. Vérifiez que la connexion fonctionne en appuyant sur entrée sur la console. Un menu de console série doit apparaître.

> [AZURE.NOTE] **Gestion des pannes :** Lorsque le périphérique est installé dans un centre de données distant ou dans une salle d’ordinateurs avec un accès limité, assurez-vous que les connexions séries sur les deux contrôleurs sont toujours connectées à un commutateur de console série ou d’un équipement similaire. Cela permet au contrôle d’out-of-band à distance et de prise en charge des opérations en cas d’interruption du réseau ou des échecs inattendus.

Vous avez terminé le câblage de votre périphérique d’alimentation, d’accès au réseau et de connexion série. L’étape suivante consiste à configurer le logiciel sur votre périphérique.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à [déployer et à configurer votre périphérique de StorSimple local](storsimple-deployment-walkthrough-u2.md).
