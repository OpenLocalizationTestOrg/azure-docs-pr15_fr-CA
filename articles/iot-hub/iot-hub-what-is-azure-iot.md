<properties
 pageTitle="Solutions Azure pour l’Internet des objets | Microsoft Azure"
 description="Une vue d’ensemble de IoT sur Azure, y compris une architecture de la solution échantillon et son rapport Azure IoT concentrateur, kits SDK de périphériques et solutions préconfigurées"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Étapes suivantes

Concentrateur de IoT Azure est un service Azure qui permet de sécuriser et fiable des communications bidirectionnelles entre votre application back-end et des millions de périphériques. Il permet à l’application back-end de :

- Recevoir de télémétrie à grande échelle de vos périphériques.
- Données d’itinéraire à partir de vos périphériques à un processeur d’événements de flux de données.
- Recevoir des téléchargements de fichiers périphériques.
- Permet d’envoyer des commandes de nuage-dispositif pour des périphériques spécifiques.

Vous pouvez utiliser IoT Hub pour implémenter votre propre back-end de solution. En outre, IoT concentrateur inclut un registre d’identité de périphérique utilisé pour configurer des périphériques, de leurs informations d’identification de sécurité et de leurs droits pour se connecter au concentrateur. Pour en savoir plus sur IoT Hub, consultez [Nouveautés IoT concentrateur ?] [lnk-iot-hub].

Pour savoir comment Azure IoT Hub permet la gestion des périphériques basés sur les normes IoT vous permettant de gérer, de configurer et de mettre à jour de vos périphériques à distance, voir [Gestion des périphériques de vue d’ensemble d’Azure IoT concentrateur][lnk-device-management].

Pour mettre en œuvre des applications clientes sur un large éventail de plates-formes de périphériques matériels et les systèmes d’exploitation, vous pouvez utiliser le kits de développement logiciel de périphérique IoT. Le kits de développement logiciel de périphérique IoT incluent les bibliothèques qui facilitent l’envoi de télémétrie et un concentrateur IoT reçoivent les commandes de nuage vers le périphérique. Lorsque vous utilisez le SDK, vous pouvez choisir à partir de plusieurs protocoles réseau pour communiquer avec IoT concentrateur. Pour plus d’informations, consultez les [informations sur les kits de développement logiciel de périphérique][lnk-device-sdks].

Pour commencer à écrire du code et l’exécution des exemples, consultez la [mise en route de concentrateur de IoT] [ lnk-getstarted] didacticiel.

Peut également vous intéresser dans [Azure IoT Suite][lnk-iot-suite], qui est un ensemble de solutions préconfigurées. IoT Suite vous permet de mettre en route rapidement et de faire évoluer les projets IoT pour des scénarios courants IoT--telles que la surveillance à distance, la gestion des ressources et une maintenance PREVENTIVE.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md