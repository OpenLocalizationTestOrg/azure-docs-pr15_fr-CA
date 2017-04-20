<properties
 pageTitle="Métadonnées d’informations de périphérique dans la solution de surveillance à distance | Microsoft Azure"
 description="Une description de la IoT Azure préconfiguré de son architecture et solution de surveillance à distance."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Solution préconfigurée de métadonnées d’informations de périphérique dans la zone de surveillance à distance

La Suite de IoT Azure préconfiguré de solution de surveillance à distance illustre une approche pour la gestion des métadonnées de périphérique. Cet article décrit l’approche de cette solution pour vous permettre de comprendre :

- Les métadonnées de périphérique stocke de la solution.
- Comment la solution gère les métadonnées de périphérique.

## <a name="context"></a>Contexte

La solution de surveillance préconfigurée à distance utilise [Azure IoT concentrateur] [ lnk-iot-hub] pour activer vos périphériques envoyer des données vers le nuage. Concentrateur de IoT comprend un [Registre d’identité de périphérique] [ lnk-identity-registry] pour contrôler l’accès à IoT concentrateur. Le Registre d’identité de périphérique IoT concentrateur est séparé de la télécommande de surveillance spécifiques *du Registre du périphérique* qui stocke les métadonnées des informations de périphérique. La solution de surveillance à distance utilise un [DocumentDB] [ lnk-docdb] base de données pour implémenter son Registre de périphérique pour le stockage des métadonnées des informations de périphérique. L' [Architecture de référence Microsoft Azure IoT] [ lnk-ref-arch] décrit le rôle du Registre périphérique dans une solution IoT classique.

> [AZURE.NOTE] La solution de surveillance préconfigurée à distance conserve le Registre d’identité de périphérique synchronisé avec le Registre de périphérique. Les deux utilisent le même id de périphérique pour identifier de façon unique chaque périphérique connecté à votre concentrateur IoT.

L' [Aperçu de gestion de périphérique IoT concentrateur] [ lnk-dm-preview] ajoute des fonctionnalités au concentrateur IoT qui sont similaires aux fonctions de gestion d’informations périphérique décrites dans cet article. Actuellement, la solution de surveillance à distance utilise uniquement les fonctionnalités (GA) disponibles dans IoT concentrateur.

## <a name="device-information-metadata"></a>Métadonnées d’informations de périphérique

Un dispositif d’informations JSON le document de métadonnées stocké dans la base de données DocumentDB de périphérique du Registre a la structure suivante :

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: le périphérique lui-même écrit ces propriétés et le périphérique est l’autorité de ces données. D’autres propriétés de périphérique d’exemple : numéro de série, numéro de modèle et le fabricant. 
- **ID de périphérique**: l’id de périphérique unique. Cette valeur est la même dans le Registre d’identité de périphérique IoT concentrateur.
- **HubEnabledState**: l’état du périphérique dans IoT concentrateur. Cette valeur est initialement définie à **null** jusqu'à ce que le périphérique se connecte d’abord. Dans le portail de la solution, une valeur **null** est représentée comme le périphérique qui est « enregistré mais n’est pas présent. »
- **CreatedTime**: heure de création du périphérique.
- **DeviceState**: l’état signalé par le périphérique.
- **UpdatedTime**: le temps que le dispositif a été mis à jour via le portail de la solution.
- **SystemProperties**: le portail de solution écrit les propriétés système et que le périphérique n’a pas connaissance de ces propriétés. Un exemple de propriété de système est **ICCID** si la solution est autorisée avec et connecté à un service de gestion des périphériques activés par l’Assistant Gestion d’installation.
- **Commandes**: une liste des commandes prend en charge par le périphérique. Le périphérique fournit ces informations à la solution.
- **CommandHistory**: une liste des commandes envoyées par la solution de surveillance à distance pour le périphérique et l’état de ces commandes.
- **IsSimulatedDevice**: un indicateur qui identifie un périphérique comme un périphérique simulé.
- **ID**: l’identificateur unique de la DocumentDB de ce document de périphérique.

> [AZURE.NOTE] Informations sur le périphérique peuvent également inclure des métadonnées afin de décrire la télémétrie que le périphérique envoie à IoT concentrateur. La solution de surveillance à distance utilise ces métadonnées de télémétrie pour personnaliser la façon dont le tableau de bord affiche [télémétrie dynamique][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Cycle de vie

Lorsque vous créez un périphérique dans le portail de la solution, la solution crée une entrée dans son Registre de périphérique comme indiqué précédemment. La plupart des informations est initialement générée et le **HubEnabledState** a la valeur **null**. À ce stade, la solution crée également une entrée pour le périphérique dans le Registre d’identité de périphérique, qui génère les clés que le périphérique utilise pour s’authentifier avec IoT concentrateur.

Lorsqu’un périphérique se connecte d’abord à la solution, il envoie un message d’informations de périphérique. Ce message d’information de dispositif comprend des propriétés, notamment le fabricant du périphérique, le numéro de modèle et le numéro de série. Un message d’informations de périphérique inclut également une liste des commandes que le périphérique prend en charge, y compris des informations sur les paramètres de la commande. Lorsque la solution reçoit ce message, il met à jour les métadonnées d’informations de périphérique dans le Registre de périphérique.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Permet d’afficher et de modifier des informations sur le périphérique dans le portail de solution

La liste des périphériques dans le portail de solution affiche les propriétés suivantes sous forme de colonnes : **état** **DeviceId**, **fabricant**, **Numéro de modèle**, **numéro de série**, **Firmware**, **plate-forme**, **processeur**et **De RAM installée**. Les propriétés du périphérique **de Latitude** et de **Longitude** lecteur de l’emplacement de la carte Bing sur le tableau de bord. 

![Liste des périphériques][img-device-list]

Si vous cliquez sur **Modifier** dans le volet **d’Informations de périphérique** dans le portail de la solution, vous pouvez modifier toutes ces propriétés. Modification de ces propriétés, l’enregistrement du périphérique dans la base de données DocumentDB à jour. Cependant, si un périphérique envoie un message d’informations de périphérique mis à jour, il remplace toutes les modifications apportées dans le portail de la solution. Vous ne pouvez pas modifier le **DeviceId**, **nom d’hôte**, **HubEnabledState**, **CreatedTime**, **DeviceState**et **UpdatedTime** des propriétés dans le portail de la solution, car seul le périphérique a autorité sur ces propriétés.

![Modification du périphérique][img-device-edit]

Le portail de solution vous permet de supprimer un périphérique de votre solution. Lorsque vous supprimez un périphérique, la solution supprime les métadonnées d’informations de périphérique à partir du Registre du périphérique solution et supprime l’entrée de périphérique dans le Registre d’identité de périphérique IoT concentrateur. Avant de pouvoir supprimer un périphérique, vous devez le désactiver.

![Suppression de périphérique][img-device-remove]

## <a name="device-information-message-processing"></a>Traitement de messages d’informations de périphérique

Les messages d’information de dispositif envoyés par un périphérique diffèrent des messages de télémétrie. Messages d’information de dispositif incluent des informations telles que les propriétés du périphérique et les commandes d’à qu'un périphérique peut répondre tout l’historique des commandes. Concentrateur IoT lui-même n’a aucune connaissance des métadonnées contenues dans un message d’informations de périphérique et traite le message de la même manière qu’il traite les messages de périphérique-nuage. Dans la solution de surveillance à distance, un [Azure flux Analytique] [ lnk-stream-analytics] les travaux (ASA) lit les messages IoT concentrateur. L’analytique de flux **DeviceInfo** du travail pour les messages qui contiennent les filtres **« ObjectType » : « DeviceInfo »** et les transfère vers l’instance de l’hôte **EventProcessorHost** qui s’exécute dans un projet web. La logique dans l’instance de **EventProcessorHost** utilise l’id de périphérique pour rechercher l’enregistrement de la DocumentDB du périphérique et de mettre à jour l’enregistrement. L’enregistrement de Registre du périphérique inclut désormais les informations de l’historique des commandes, les commandes et les propriétés du périphérique.

> [AZURE.NOTE] Un message d’informations de périphérique est un périphérique-nuage de message standard. La solution fait la distinction entre les messages d’informations de périphérique et télémétrie par ASA requêtes.

## <a name="example-device-information-records"></a>Enregistrements d’informations de périphérique exemple

La solution de surveillance préconfigurée à distance utilise deux types d’enregistrements d’informations de périphérique : les enregistrements pour les simulations de périphériques déployés avec la solution et les enregistrements pour les périphériques personnalisés que vous vous connectez à la solution.

### <a name="simulated-device"></a>Périphérique simulé

L’exemple suivant illustre l’enregistrement d’informations du périphérique pour un périphérique simulé JSON. Cet enregistrement a une valeur définie pour **UpdatedTime**, qui indique le périphérique a envoyé un message **DeviceInfo** IoT concentrateur. L’enregistrement inclut certaines propriétés communes de périphérique, définit six commandes de prise en charge par les périphériques simulés, a l’indicateur **IsSimulatedDevice** défini sur **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Périphérique personnalisé

L’exemple suivant illustre l’enregistrement d’informations du périphérique pour un périphérique personnalisé JSON et a **l’IsSimulatedDevice** indicateur défini à **0**. Vous pouvez voir que ce périphérique personnalisé prend en charge deux commandes et que le portail de solution a envoyé une commande **SetTemperature** sur le périphérique :

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

L’exemple suivant montre le message JSON **DeviceInfo** du périphérique envoyé à jour les métadonnées d’informations de périphérique :

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez fini d’apprendre comment vous pouvez personnaliser les solutions préconfigurées, vous pouvez explorer les capacités des solutions Suite IoT préconfiguré et certaines autres fonctionnalités :

- [Présentation de la solution préconfigurée de maintenance PREVENTIVE][lnk-predictive-overview]
- [Forum aux questions sur la Suite de IoT][lnk-faq]
- [Sécurité IoT de bas en haut][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
