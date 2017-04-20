<properties
 pageTitle="Concentrateur de IoT Azure mise à l’échelle | Microsoft Azure"
 description="Décrit comment mettre à l’échelle Azure IoT concentrateur."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/19/2016"
 ms.author="elioda"/>

# <a name="scaling-iot-hub"></a>Mise à l’échelle IoT concentrateur

Azure IoT concentrateur peut prendre en charge jusqu'à un millions d’appareils connecté simultanément. Pour plus d’informations, reportez-vous à la section [IoT concentrateur tarification][lnk-pricing]. Chaque unité de concentrateur de IoT permet un certain nombre de messages quotidiens.

Pour faire évoluer votre solution correctement, étudier votre concentrateur de IoT particulier. En particulier, examinez le débit maximal requis pour les catégories suivantes d’opérations :

* Messages de périphérique-nuage
* Messages du nuage vers le périphérique
* Opérations de Registre identité

Outre ces informations de débit, voir [limitations et IoT concentrateur quotas][] et concevoir votre solution en conséquence.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Débit des messages de périphérique-nuage et nuage-appareil

La meilleure manière de dimensionner une solution IoT concentrateur est d’évaluer le trafic sur une base par unité.

Messages de périphérique-nuage suivent les recommandations de débit soutenu.

| Couche | Débit soutenu | Taux d’envoi PROLONGEE |
| ---- | -------------------- | ------------------- |
| S1 | Jusqu'à 1111 Ko par minute par unité<br/>(1,5 Go/jour/unité) | Moyenne des messages/minute 278 par unité<br/>(400 000 messages par jour par unité) |
| S2 | Jusqu'à 16 Mo/minute par unité<br/>(Go/jour/unité 22,8) | Moyenne des messages/minute 4167 par unité<br/>(6 millions de messages par jour par unité) |
| S3 | Jusqu'à 814 Mo/minute par unité<br/>(1144.4 Go/jour/unité) | Moyenne des messages/minute 208,333 par unité<br/>(300 millions de messages par jour par unité) |

## <a name="identity-registry-operation-throughput"></a>Débit d’opération identité du Registre

Les opérations de Registre IoT concentrateur identité ne sont pas censés pour être des opérations d’exécution, comme ils sont essentiellement liés à la configuration de périphériques.

Pour les numéros de performances spécifiques de transmission en rafale, consultez [limitations et IoT concentrateur quotas][].

## <a name="sharding"></a>Ont

Pendant un concentrateur IoT unique peut s’adapter à des millions de périphériques, votre solution nécessite parfois des caractéristiques de performances spécifiques qui ne garantit pas un concentrateur IoT unique. Dans ce cas, il est recommandé que vous partitionnez vos périphériques dans plusieurs concentrateurs IoT. Plusieurs concentrateurs IoT lisser des pics de trafic et obtenir le débit requis ou taux d’opération sont requis.

## <a name="next-steps"></a>Étapes suivantes

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Limitations et quotas de concentrateur de IoT]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
