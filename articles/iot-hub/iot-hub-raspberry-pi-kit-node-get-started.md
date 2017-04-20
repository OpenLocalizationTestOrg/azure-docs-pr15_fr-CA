<properties
 pageTitle="Mise en route de Raspberry Pi 3 | Microsoft Azure"
 description="Mise en route de framboises Pi 3, créer votre concentrateur de IoT Azure et connecter votre Pi au concentrateur IoT"
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

# <a name="get-started-with-raspberry-pi-3"></a>Mise en route de Raspberry Pi 3

Dans ce didacticiel, vous commencez par apprendre les principes fondamentaux de l’utilisation, de framboises Pi 3, qui Raspbian en cours d’exécution. Puis, vous apprendrez comment connecter vos périphériques de façon transparente vers le nuage avec [Azure IoT concentrateur](iot-hub-what-is-iot-hub.md). Pour des exemples de Windows 10 IoT Core, visitez le site [windowsondevices.com](http://www.windowsondevices.com/).

## <a name="lesson-1-configure-your-device"></a>Leçon 1 : Configurer votre périphérique

![Diagramme de la leçon 1 E2E](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

Dans cette leçon, vous configurez votre périphérique de framboises Pi 3 avec un système d’exploitation, configurez votre environnement de développement et déployez d’une application à la Pi.

### <a name="configure-your-device"></a>Configurer votre périphérique

Configurer votre framboises Pi 3 lors de la première utilisation et installer le système d’exploitation Raspbian, un système d’exploitation libre qui est optimisé pour le matériel de framboises Pi.

*Temps estimé pour terminer : 30 minutes* 

[Accédez à « Configurer votre périphérique »](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)

### <a name="get-the-tools"></a>Obtenez les outils
Téléchargez les outils et les logiciels pour générer et déployer votre première application framboises Pi 3.

*Temps estimé pour terminer : 20 minutes* 

[Obtenir des outils](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

### <a name="create-and-deploy-the-blink-application"></a>Créer et déployer l’application de clignotement

Clone de l’exemple d’application Node.js à partir de Github et gulp pour déployer cette application sur votre carte de framboises Pi 3. Cet exemple d’application se met à clignoter le voyant connecté à la carte toutes les deux secondes.

*Temps estimé pour terminer : 5 minutes* 

[Atteindre ' créer et déployer l’application blink'](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)

## <a name="lesson-2-create-your-iot-hub"></a>Leçon 2 : Créer votre concentrateur IoT

![Leçon 2 E2E diagramme](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

Dans cette leçon, vous créez gratuitement un compte Azure, configurer votre concentrateur Azure IoT et créez votre premier périphérique dans Azure IoT concentrateur.

Terminer la leçon 1 avant de commencer cette leçon.

### <a name="get-the-azure-tools"></a>Obtenez les outils Azure

Installer Azure l’Interface de ligne de commande (CLI Azure).

*Durée estimée : 10 minutes* 

[Cliquez sur « Outils Azure d’obtenir »](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)

### <a name="create-your-iot-hub-and-register-your-raspberry-pi-3"></a>Créer votre concentrateur IoT et enregistrer votre framboises Pi 3

Créer votre groupe de ressources, configurer votre premier concentrateur d’Azure IoT et ajouter votre premier périphérique au concentrateur IoT Azure à l’aide de la CLI d’Azure. 

*Durée estimée : 10 minutes* 

[Accédez à « Créer votre concentrateur IoT et enregistrer votre framboises Pi 3 »](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


## <a name="lesson-3-send-device-to-cloud-messages"></a>Leçon 3 : Envoyer des messages de périphérique-nuage

![Leçon 3 E2E diagramme](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

Dans cette leçon, vous envoyez des messages depuis votre Pi à votre concentrateur IoT. Vous créez également une application Azure fonction qui Récupère les messages entrants à partir de votre concentrateur IoT et les écrit dans le stockage par table Azure.

Effectuer les leçons 1 et 2 avant de commencer cette leçon.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Créer une application de fonction Azure et le compte de stockage Azure

Utiliser un modèle de gestionnaire de ressources Azure pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes* 

[Atteindre 'Créer un application de fonction Azure et d’un compte de stockage Azure'](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)

### <a name="run-sample-application-to-send-device-to-cloud-messages"></a>Exécutez l’exemple d’application pour envoyer des messages de périphérique-nuage

Déployer et exécuter un exemple d’application sur votre périphérique de framboises Pi 3 qui envoie des messages à IoT concentrateur.

*Durée estimée : 10 minutes* 

[Atteindre 'Exécuter l’exemple d’application pour envoyer des messages de périphérique-nuage'](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

### <a name="read-messages-persisted-in-azure-storage"></a>Lire les messages rendues persistantes dans le stockage Azure
Surveiller les messages de périphérique-nuage qu’ils sont écrits dans le stockage Azure.

*Temps estimé pour terminer : 5 minutes* 

[Accédez à « lire les messages rendues persistantes dans le stockage Azure »](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)


## <a name="lesson-4-send-cloud-to-device-messages"></a>Leçon 4 : Envoyer des messages de nuage-appareil

![Leçon 4 E2E diagramme](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

Cette leçon explique comment envoyer des messages à partir de votre concentrateur Azure IoT votre framboises Pi 3. Les messages de contrôle sur et désactiver le comportement de la DEL qui est connectée à votre Pi. Un exemple d’application est préparé pour effectuer cette tâche.

Effectuer les leçons 1, 2 et 3 avant de commencer cette leçon.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Exécutez l’exemple d’application de recevoir des messages du nuage vers le périphérique

L’exemple d’application dans la leçon 4 s’exécute sur votre Pi et surveille les messages entrants à partir de votre concentrateur IoT. Une nouvelle tâche de choses envoie des messages à votre Pi votre concentrateur IoT faire clignoter le voyant.

*Durée estimée : 10 minutes* 

[Atteindre 'Exécuter l’exemple d’application de recevoir des messages de nuage-DISPOSITIF'](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Section facultative : modifier la dans et hors de comportement du voyant

Personnaliser les messages pour modifier le voyant et désactive le comportement.

*Durée estimée : 10 minutes* 

[Accédez à « section facultative : modifier sur et désactiver le comportement de la DEL '](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


## <a name="troubleshooting"></a>Résolution des problèmes

Si vous répondez à n’importe quel problème pendant les leçons, vous pouvez rechercher des solutions dans cette page.

[Accédez à « Résolution des problèmes »](iot-hub-raspberry-pi-kit-node-troubleshooting.md)
