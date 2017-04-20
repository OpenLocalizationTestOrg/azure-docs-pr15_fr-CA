<properties
     pageTitle="Le portail Azure permet de configurer le téléchargement du fichier | Microsoft Azure"
     description="Une vue d’ensemble de la configuration du téléchargement du fichier via le portail Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurer les transferts de fichiers via le portail Azure

## <a name="file-upload"></a>Téléchargement de fichiers

Pour utiliser la [fonctionnalité de concentrateur de IoT de téléchargement de fichiers][lnk-upload], vous devez d’abord associer un compte de stockage Azure avec votre concentrateur. Sélectionnez les paramètres de **téléchargement de fichier** pour afficher la liste des propriétés de téléchargement de fichier pour le moyeu IoT qui est en cours de modification.

![][13]

**Conteneur de stockage**: permet de sélectionner un conteneur d’objet blob dans un compte de stockage Azure dans votre abonnement Azure à associer à votre concentrateur IoT le portail Azure. Si nécessaire, vous pouvez créer un compte de stockage Azure sur le conteneur de lame et blob des **comptes de stockage** sur la lame de **conteneurs** . IoT concentrateur génère automatiquement des URI de SAS avec des autorisations d’écriture pour ce conteneur blob pour les périphériques à utiliser pour télécharger des fichiers.

![][14]

**Recevoir des notifications pour les fichiers téléchargés**: activer ou désactiver les notifications de téléchargement de fichier via le bouton.

**Sa durée de vie**: ce paramètre est le time-to-live des URI SAS renvoyé au périphérique par IoT concentrateur. Définir une heure par défaut mais peut être personnalisée à d’autres valeurs à l’aide du curseur.

**Notification de fichier de paramètres par défaut de durée de vie**: notification la durée de vie d’un fichier de téléchargement avant qu’il est arrivé à expiration. La valeur par défaut à un seul jour, mais peuvent être personnalisés à d’autres valeurs à l’aide du curseur.

**Nombre de fichiers notification livraison maximale**: notification de téléchargement le nombre de fois que le concentrateur IoT tente de remettre un fichier. La valeur 10 par défaut mais peut être personnalisée à d’autres valeurs à l’aide du curseur.

![][15]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les capacités de téléchargement de fichier IoT moyeu, voir [télécharger des fichiers à partir d’un périphérique] [ lnk-upload] dans le guide du développeur.

Cliquez sur ces liens pour en savoir plus sur la gestion d’Azure IoT Hub :

- [En vrac gérer les périphériques IoT][lnk-bulk]
- [Mesures de l’utilisation][lnk-metrics]
- [Surveillance des opérations][lnk-monitor]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]
- [Sécuriser votre solution IoT dès le départ des][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md