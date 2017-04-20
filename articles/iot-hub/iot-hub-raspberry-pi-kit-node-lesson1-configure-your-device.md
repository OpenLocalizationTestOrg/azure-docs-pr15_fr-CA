<properties
 pageTitle="Configurer votre périphérique | Microsoft Azure"
 description="Configurer votre framboises Pi 3 pour une première utilisation et installer le système d’exploitation Raspbian, un système d’exploitation libre qui est optimisé pour le matériel de framboises Pi."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="11-configure-your-device"></a>1.1 configurer votre périphérique

## <a name="111-what-you-will-do"></a>1.1.1 ce que vous fera

Configurer votre Pi pour la première utilisation et installer le système d’exploitation Raspbian, un système d’exploitation libre qui est optimisé pour le matériel de framboises Pi. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="112-what-you-will-learn"></a>1.1.2 vous apprendrez

Dans cette section, vous apprendrez à :

- L’installation de Raspbian sur votre Pi
- Comment faire pour alimenter votre Pi, à l’aide d’un câble USB
- Comment connecter votre Pi au réseau à l’aide d’un câble Ethernet ou le Wi-Fi
- Comment ajouter un voyant à le breadboard et le connecter à votre Pi

## <a name="113-what-you-need"></a>1.1.3 ce dont vous avez besoin

Pour terminer cette section, vous devez les parties suivantes de votre Starter Kit de framboises Pi 3 :

- Le Conseil de framboises Pi 3
- La carte MicroSD de 16 Go
- La puissance de 2 a 5 v fournir avec le câble USB de micro six pieds
- La breadboard
- Fils de connecteur
- Une résistance de 560 ohms
- Un voyant diffus 10mm
- Le câble Ethernet

![Éléments de votre Starter Kit](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

Vous devez également :

- Une connexion filaire ou sans fil pour votre Pi pour se connecter à
- Une USB-SD carte mini-SD carte ou pour graver l’image du système d’exploitation à la carte MicroSD.
- Un ordinateur exécutant Windows, Mac ou Linux. L’ordinateur est utilisé pour installer le Raspbian sur la carte MicroSD.
- Une connexion Internet pour télécharger les outils nécessaires et le logiciel

## <a name="114-install-raspbian-on-the-microsd-card"></a>1.1.4 installer Raspbian sur la carte MicroSD

Préparer la carte MicroSD pour écrire l’image Raspbian.

1. Télécharger Raspbian.
  1. [Téléchargez](https://www.raspberrypi.org/downloads/raspbian/) le fichier zip pour Raspbian Jessie avec les pixels.
  2. Extraire l’image Raspbian dans un dossier sur votre ordinateur.
2. Installez Raspbian à la carte MicroSD.
  1. [Télécharger](https://www.etcher.io) et installer l’utilitaire de graveur de carte SD de Etcher.
  2. Exécuter Etcher et sélectionnez l’image de Raspbian que vous avez extrait à l’étape 1.
  3. Sélectionnez le lecteur de carte MicroSD.
    Remarque : Etcher peut avoir déjà sélectionné le lecteur approprié.
  4. Cliquez sur Flash pour installer Raspbian à la carte MicroSD.
  5. Retirez la carte de MicroSD à partir de votre ordinateur une fois terminé.
    Remarque : Il est possible sans supprimer la fiche MicroSD directement, car Etcher est automatiquement éjecte ou démonte la carte MicroSD à la fin.
  6. Insérez la carte MicroSD dans votre Pi.

![Insérez la carte SD](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="115-power-on-your-pi"></a>1.1.5 sous tension votre Pi

Mettez sous tension votre Pi en utilisant le câble USB micro et le bloc d’alimentation.

![Sous tension](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [AZURE.NOTE] Il est important d’utiliser le bloc d’alimentation dans le kit est d’au moins 2 a pour vous assurer que votre framboises est chargé avec assez de puissance pour fonctionner correctement.

## <a name="116-connect-your-raspberry-pi-3-to-the-network"></a>1.1.6 votre framboises Pi 3 la connexion au réseau

Vous pouvez connecter votre Pi à un réseau filaire ou à un réseau sans fil. Assurez-vous que votre Pi est connecté au même réseau que votre ordinateur. Par exemple, vous pouvez connecter votre Pi au même commutateur connecté à votre ordinateur.

### <a name="1161-connect-to-a-wired-network"></a>1.1.6.1 connecter à un réseau câblé

Utilisez le câble Ethernet pour connecter votre Pi à votre réseau câblé. Les deux voyants sur votre Pi sous tension si la connexion est établie.

![Se connecter à l’aide du câble Ethernet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="1162-connect-to-a-wireless-network"></a>1.1.6.2 connecter à un réseau sans fil

Suivez les [instructions](https://www.raspberrypi.org/learning/software-guide/wifi/) de la base de Pi framboises à votre Pi de se connecter à votre réseau sans fil. Ces instructions vous obligent à vous connecter un moniteur et un clavier à votre Pi.

## <a name="117-connect-the-led-to-your-pi"></a>1.1.7 se connecter le voyant à votre Pi

Pour effectuer cette tâche, utilisez les [breadboard](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), les fils du connecteur, le voyant et la résistance. Les connecter aux ports [d’entrée/sortie à usage général](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) de votre Pi. 

![Breadboard, DEL et résistance](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Se connecter à la jambe inférieure du voyant à **GPIO GND (6 broches)**.
2. Se connecter à la jambe plue du voyant à une branche de la résistance.
3. Se connecter à l’autre branche de la résistance à **GPIO 4 (broche 7)**.

Notez que la polarité de voyant est importante. Ce paramètre de polarité est communément appelé faible Active.

![Brochage](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

Félicitations ! Vous avez correctement configuré votre Pi.

## <a name="118-summary"></a>1.1.8 résumé de

Dans cette section, vous avez appris comment configurer votre Pi en installant Raspbian, connectez votre Pi à un réseau et connexion d’un voyant et votre Pi. Notez que le voyant ne s’allume encore. Dans la section suivante, vous installez les outils nécessaires et le logiciel en vue d’un exemple d’application en cours d’exécution sur votre Pi.

![Le matériel est prêt](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Étapes suivantes

[1.2 obtenir les outils](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
