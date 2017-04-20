<properties
 pageTitle="Guide du développeur - IoT concentrateur SDK | Microsoft Azure"
 description="Azure IoT concentrateur developer guide - informations et des liens vers diverses Azure IoT concentrateur périphérique et service kits de développement logiciel."
 services="iot-hub"
 documentationCenter=""
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

# <a name="iot-hub-sdks"></a>Kits de développement logiciel IoT concentrateur

## <a name="iot-hub-device-sdks"></a>Dispositif de concentrateur de IoT SDK

Le périphérique Microsoft Azure IoT SDK contiennent du code qui facilite la production de périphériques et les applications qui sont gérées par les services Azure IoT concentrateur et de se connectent à.

Le périphérique suivant IoT SDK sont disponibles au téléchargement à partir de GitHub :

- [DISPOSITIF IoT Azure SDK pour C] [ lnk-c-device-sdk] écrit en ANSI C (C99) pour la portabilité et la compatibilité de nombreuses plates-formes.
- [DISPOSITIF IoT Azure SDK pour .NET][lnk-dotnet-device-sdk]
- [DISPOSITIF IoT Azure SDK pour Java][lnk-java-device-sdk]
- [DISPOSITIF IoT Azure SDK pour Node.js][lnk-node-device-sdk]
- [Dispositif de Microsoft Azure IoT SDK pour les Python 2.7][lnk-python-device-sdk]

> [AZURE.NOTE] Consultez les fichiers readme dans les référentiels GitHub pour plus d’informations sur l’utilisation du langage et des responsables du lot de spécifique à la plate-forme pour installer les fichiers binaires et des dépendances sur votre ordinateur de développement.

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilité plateformes de système d’exploitation et du matériel

Pour plus d’informations sur la compatibilité du Kit de développement logiciel avec des périphériques matériels spécifiques, consultez la [Azure certifié pour le catalogue du périphérique IoT][lnk-certified].

## <a name="iot-hub-service-sdks"></a>Concentrateur de IoT service SDK

Les kits de développement de service Microsoft Azure IoT contiennent du code qui facilite la création d’applications qui interagissent directement avec IoT Hub pour gérer les périphériques et la sécurité.

Du service IoT SDK sont disponibles au téléchargement à partir de GitHub :

- [Service IoT Azure SDK pour .NET][lnk-dotnet-service-sdk]
- [Service IoT Azure SDK pour Node.js][lnk-node-service-sdk]
- [Service IoT Azure SDK pour Java][lnk-java-service-sdk]

> [AZURE.NOTE] Consultez les fichiers readme dans les référentiels GitHub pour plus d’informations sur l’utilisation du langage et des responsables du lot de spécifique à la plate-forme pour installer les fichiers binaires et des dépendances sur votre ordinateur de développement.

## <a name="azure-iot-gateway-sdk"></a>Passerelle de IoT Azure SDK

Cette passerelle de IoT Azure SDK contient l’infrastructure et des modules pour créer des solutions de passerelle IoT. Vous pouvez étendre le SDK pour créer des passerelles adaptées à n’importe quel scénario de bout en bout.

Vous pouvez télécharger le [SDK de passerelle Azure IoT] [ lnk-gateway-sdk] de GitHub.

## <a name="online-api-reference-documentation"></a>Documentation de référence API en ligne

Voici une liste de liens vers la documentation en ligne de référence API pour périphérique IoT d’Azure, de service et les bibliothèques de passerelle :

- [Internet des objets (IoT) .NET][lnk-dotnet-ref]
- [Concentrateur IoT reste][lnk-rest-ref]
- [DISPOSITIF IoT Azure SDK pour C][lnk-c-ref]
- [DISPOSITIF IoT Azure SDK pour Java][lnk-java-ref]
- [Service IoT Azure SDK pour Java][lnk-java-service-ref]
- [DISPOSITIF IoT Azure SDK pour Node.js][lnk-node-ref]
- [Service IoT Azure SDK pour Node.js][lnk-node-service-ref]
- [Passerelle de IoT Azure SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Étapes suivantes

D’autres rubriques de référence dans ce guide de développeur IoT concentrateur incluent :

- [Les points de terminaison IoT concentrateur][lnk-devguide-endpoints]
- [Langage de requête pour jumeaux, des méthodes et des tâches][lnk-devguide-query]
- [Quotas et limitation][lnk-devguide-quotas]
- [Prise en charge de la MQTT de concentrateur IoT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md