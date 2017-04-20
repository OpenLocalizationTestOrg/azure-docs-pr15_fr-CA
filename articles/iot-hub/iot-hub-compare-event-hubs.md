<properties
 pageTitle="Comparaison du concentrateur IoT Azure Azure événement concentrateurs | Microsoft Azure"
 description="Comparaison des services Azure IoT Hub et concentrateurs d’événement Azure mise en évidence des différences fonctionnelles et cas d’utilisation."
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
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparaison d’Azure IoT Hub et concentrateurs d’événement Azure

Un des cas d’usage principal IoT concentrateur consiste à rassembler les télémétrie à partir de périphériques. Pour cette raison, IoT concentrateur est souvent comparée à [Azure événement concentrateurs][]. Comme concentrateur de IoT, concentrateurs d’événements est un service qui permet d’événement et télémétrie pénètre dans le nuage à grande échelle, avec une latence faible et haute fiabilité de traitement des événements.

Toutefois, les services ont de nombreuses différences, qui sont détaillées dans le tableau suivant.

| Zone | Concentrateur de IoT | Concentrateurs d’événement |
| ---- | ------- | ---------- |
| Modèles de communication | Permet le périphérique-nuage et nuage-dispositif de messagerie. | Permet uniquement la pénétration d’événement (généralement considérés comme pour les scénarios de périphérique-nuage). |
| Prise en charge du protocole de périphérique | Prend en charge MQTT, AMQP, AMQP via le protocole WebSocket et HTTP. En outre, IoT concentrateur fonctionne avec la [passerelle de protocole Azure IoT][lnk-azure-protocol-gateway], une implémentation de passerelle de protocole personnalisable pour prendre en charge les protocoles personnalisés. | Prend en charge AMQP, AMQP via le protocole WebSocket et HTTP. |
| Sécurité | Fournit l’identité de chaque équipement et contrôle d’accès révocable. Reportez-vous à la [section sécurité du guide du développeur de IoT concentrateur]. | Fournit des [stratégies d’accès partagés]à l’échelle concentrateurs événement[Event Hubs - security], révocation limitée prend en charge grâce à [des règles de l’éditeur][Event Hubs publisher policies]. IoT solutions sont souvent requis pour implémenter une solution personnalisée pour prendre en charge les informations d’identification de périphérique et de la fonction anti-usurpation des mesures. |
| Surveillance des opérations | Permet des solutions IoT pour vous abonner à un large éventail d’identité connectivité et gestion des événements des périphériques tels que des erreurs d’authentification de périphérique, la limitation et les exceptions de format incorrect. Ces événements vous permettent d’identifier rapidement les problèmes de connectivité au niveau du périphérique. | Expose uniquement les métriques rassemblées. |
| Échelle | Est optimisé pour la prise en charge des millions de périphériques connectés simultanément. | Peut prendre en charge d’un nombre plus limité de connexions simultanées--jusqu'à 5 000 connexions AMQP, comme [les quotas de Bus des services Azure][]. D’autre part, concentrateurs d’événements vous permet de spécifier la partition pour chaque message envoyé. |
| Kits SDK de périphériques | Fournit des [Kits de développement logiciel de périphérique] [ Azure IoT Hub SDKs] pour une grande variété de plates-formes et des langages. | Est pris en charge sur .NET et C. Fournit également des interfaces envoient des AMQP et HTTP. |
| Téléchargement de fichiers | Permet des solutions IoT télécharger des fichiers à partir de périphériques vers le nuage. Inclut un point de terminaison de notifications de fichier pour l’intégration de flux de travail et une catégorie pour le support de débogage de contrôle des opérations. | Utilise un motif de contrôle demande manuellement la requête de fichiers à partir de périphériques et fournir des périphériques avec une clé de stockage pour la transaction. |

En résumé, même si le seul cas d’utilisation est entrée de télémétrie de périphérique-nuage, IoT concentrateur fournit un service qui est spécifiquement conçu pour une connectivité IoT périphérique. Il continuera à développer les propositions de valeur pour ces scénarios avec des fonctionnalités spécifiques IoT. Concentrateurs d’événement est conçu pour l’entrée d’événement à une grande échelle, à la fois dans le cadre de scénarios de centre de données inter et intra-centre de données.

Il n’est pas rare d’utiliser IoT concentrateur et concentrateurs d’événements dans la même solution--où IoT Hub gère la communication de périphérique-nuage, et concentrateurs d’événement entrée d’événement d’étape ultérieure dans les moteurs de traitement en temps réel.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la planification de votre déploiement IoT concentrateur, reportez-vous à la section [mise à l’échelle, de haute disponibilité et de reprise après sinistre][lnk-scaling].

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

[Événement Azure concentrateurs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Section sécurité du guide du développeur IoT concentrateur]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quotas de Bus des services Azure]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
