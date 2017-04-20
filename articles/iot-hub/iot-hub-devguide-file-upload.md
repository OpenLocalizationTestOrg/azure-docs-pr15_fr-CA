<properties
 pageTitle="Téléchargement de fichier - guide du développeur | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub - téléchargement de fichiers à partir d’un périphérique à IoT concentrateur"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="upload-files-from-a-device"></a>Télécharger des fichiers à partir d’un périphérique

## <a name="overview"></a>Vue d’ensemble

Comme détaillé dans les [points de terminaison IoT concentrateur] [ lnk-endpoints] article, périphériques peuvent lancer des téléchargements de fichiers en envoyant une notification via un point de terminaison côté périphérique (**/devices/ {deviceId} / fichiers**).  Lorsqu’un périphérique avertit concentrateur IoT d’un téléchargement terminé, IoT concentrateur génère les notifications de téléchargement de fichier que vous pouvez recevoir via un point de terminaison de service orienté (**/messages/servicebound/filenotifications**) en tant que messages.

Au lieu de courtage de messages via concentrateur IoT lui-même, IoT plutôt les concentrateurs en tant que répartiteur à un compte de stockage Azure associé. Un périphérique demande un jeton de stockage IoT concentrateur spécifique dans le fichier que du périphérique souhaite télécharger. Le périphérique utilise l’URI SAS pour télécharger le fichier pour le stockage, et lorsque le téléchargement est terminé le périphérique envoie une notification d’achèvement IoT concentrateur. Concentrateur de IoT vérifie que le fichier a été téléchargé et ajoute ensuite une notification de téléchargement de fichier pour le nouveau service orienté fichier notification messagerie point de terminaison.

Avant de télécharger un fichier à partir d’un périphérique à IoT concentrateur, vous devez configurer votre concentrateur en [associant un stockage Azure] [ lnk-associate-storage] compte à celui-ci.

Votre appareil peut ensuite [initialiser un téléchargement] [ lnk-initialize] et [notifier IoT concentrateur] [ lnk-notify] lorsque le transfert est terminé. Le cas échéant, lorsqu’un périphérique avertit IoT concentrateur que le téléchargement est terminé, le service peut générer un [message de notification][lnk-service-notification].

### <a name="when-to-use"></a>Quand utiliser

Utilisez cette fonctionnalité IoT concentrateur lorsque vous avez besoin de télécharger un fichier à partir d’un périphérique à votre service principal plutôt que d’envoyer un message via IoT concentrateur.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associer un compte de stockage Azure IoT concentrateur

Pour utiliser la fonctionnalité de téléchargement de fichier, vous devez d’abord lier un compte de stockage Azure au concentrateur IoT. Vous pouvez le faire par le biais du [portail Azure][lnk-management-portal], ou par le biais du [fournisseur de ressources IoT concentrateur reste API][lnk-resource-provider-apis]. Une fois que vous avez associé un compte Azure Storage avec votre concentrateur IoT, le service retourne un URI SAS à un périphérique lorsque le périphérique envoie une demande de téléchargement de fichier.

> [AZURE.NOTE] Les [Kits de développement logiciel Azure IoT concentrateur] [ lnk-sdks] gère automatiquement la récupération de l’URI SAS, téléchargement du fichier et la notification de concentrateur IoT d’un téléchargement terminé.

## <a name="initialize-a-file-upload"></a>Initialiser le chargement d’un fichier

IoT concentrateur a un point de terminaison spécifiquement pour les périphériques à la demande d’un URI SAS pour le stockage de télécharger un fichier. Le périphérique établit le processus de téléchargement de fichiers par l’envoi d’une publication au concentrateur IoT à `{iot hub}.azure-devices.net/devices/{deviceId}/files` avec le corps JSON suivant :

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT concentrateur renvoie la suivante, qui le périphérique utilise pour télécharger le fichier :

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Désapprouvée : initialiser le chargement d’un fichier avec une commande GET

> [AZURE.NOTE] Cette section décrit les fonctionnalités désapprouvées pour la réception d’un URI SAS IoT concentrateur. Utilisez la méthode POST décrite ci-dessus.

IoT concentrateur a deux points de terminaison REST pour prendre en charge le téléchargement de fichiers, un pour obtenir l’URI de SAS pour le stockage et l’autre pour notifier le concentrateur IoT d’un téléchargement terminé. Le périphérique établit le processus de transfert de fichier en envoyant une commande GET au concentrateur IoT à `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Le concentrateur retourne un URI SAS spécifique pour le fichier à télécharger et à utiliser une fois le téléchargement terminé, un ID de corrélation.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notifier le concentrateur IoT de chargement d’un fichier terminé

Le périphérique est responsable du téléchargement du fichier de stockage à l’aide de kits de développement logiciel stockage Azure. Une fois le téléchargement terminé, le périphérique envoie un message au concentrateur IoT à `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` avec le corps JSON suivant :

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

La valeur de `isSuccess` est une valeur Boolean indiquant le fichier a été téléchargé avec succès ou non. Le code d’état de `statusCode` est l’état pour le téléchargement du fichier vers le stockage et de la `statusDescription` correspond à la `statusCode`.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent plus d’informations sur le téléchargement de fichiers à partir d’un périphérique.

## <a name="file-upload-notifications"></a>Notifications de téléchargement de fichier

Lorsqu’un périphérique charge un fichier informe le concentrateur IoT d’avancement du téléchargement, le service génère également un message de notification qui contient l’emplacement de stockage et le nom du fichier.

Comme expliqué dans les [points de terminaison][lnk-endpoints], IoT concentrateur fournit des notifications de téléchargement de fichier via un point de terminaison de service orienté (**/messages/servicebound/fileuploadnotifications**) en tant que messages. La sémantique de réception pour les notifications de téléchargement de fichier sont les mêmes que pour les messages du nuage vers le périphérique et le même [cycle de vie du message][lnk-lifecycle]. Chaque message, extraite de l’extrémité de notification de téléchargement de fichier est un enregistrement JSON avec les propriétés suivantes :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | Horodatage qui indique quand la notification a été créée. |
| ID de périphérique | **ID de périphérique** du périphérique qui a téléchargé le fichier. |
| BlobUri | URI du fichier téléchargé. |
| BlobName | Nom du fichier téléchargé. |
| LastUpdatedTime | Horodatage qui indique quand le fichier a été mis à jour. |
| BlobSizeInBytes | Taille du fichier téléchargé. |

**Exemple**. Il s’agit d’un corps de l’exemple d’un message de notification de téléchargement de fichier.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Options de configuration de notification de téléchargement de fichier

Chaque concentrateur IoT expose les options de configuration suivantes pour les notifications de téléchargement de fichier :

| Propriété | Description | Plage et par défaut |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Contrôle si les notifications de téléchargement de fichier sont écrites vers le point de terminaison de notifications de fichier. | Bool. Par défaut : True. |
| **fileNotifications.ttlAsIso8601** | Durée de vie par défaut des notifications de téléchargement de fichier. | ISO_8601 des intervalle jusqu'à 48 H (1 minute minimum). Par défaut : 1 heure. |
| **fileNotifications.lockDuration** | Durée de verrouillage de la file de notifications de téléchargement de fichiers. | 5 à 300 secondes (5 secondes minimum). Par défaut : 60 secondes. |
| **fileNotifications.maxDeliveryCount** | Nombre maximal de remise pour le fichier de téléchargement file d’attente de la notification. | 1 à 100. Par défaut : 100. |

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

D’autres rubriques de référence dans le Guide du développeur incluent :

- [Les points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution.
- [Les quotas et la régulation] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT Hub et le comportement d’accélération s’attendre lorsque vous utilisez le service.
- [SDK de périphérique et service IoT concentrateur] [ lnk-sdks] répertorie la langue différents kits de développement logiciel vous une utilisation lorsque vous développez des services et des périphériques à la fois les applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, procédés et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations du concentrateur de IoT sur vos jumeaux de périphérique, les méthodes et les tâches.
- [Prise en charge de IoT concentrateur MQTT] [ lnk-devguide-mqtt] fournit plus d’informations sur la prise en charge de IoT Hub pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez appris comment télécharger des fichiers à partir de périphériques à l’aide de IoT concentrateur, peut vous intéresser dans les rubriques du Guide de développement suivantes :

- [Gérer les identités de périphérique IoT moyeu][lnk-devguide-identities]
- [Contrôle de l’accès à IoT concentrateur][lnk-devguide-security]
- [Des jumeaux de périphérique permet de synchroniser l’état et les configurations][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un périphérique][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs périphériques][lnk-devguide-jobs]

Si vous souhaitez essayer certaines des concepts décrits dans cet article, vous intéresser dans ce didacticiel IoT Hub :

- [Comment faire pour télécharger des fichiers à partir de périphériques vers le nuage avec IoT concentrateur][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
