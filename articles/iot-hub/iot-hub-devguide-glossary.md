<properties
 pageTitle="Guide du développeur - glossaire | Microsoft Azure"
 description="Un glossaire de termes courants relatifs à IoT concentrateur"
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

# <a name="glossary-of-iot-hub-terms"></a>Glossaire des termes de concentrateur de IoT

Cet article répertorie certains des termes courants associés IoT concentrateur.

## <a name="advanced-message-queueing-protocol-amqp"></a>Protocole de file d’attente de Message avancés (AMQP)

[AMQP](https://www.amqp.org/) est un des protocoles de messagerie qui prend en charge les IoT Hub pour communiquer avec les périphériques. Pour plus d’informations sur les protocoles de messagerie qui prend en charge le concentrateur de IoT, reportez-vous à [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="cloud-to-device-c2d"></a>Nuage-dispositif (C2D)

Généralement utilisé pour faire référence aux messages envoyés depuis IoT Hub à un périphérique connecté. Souvent, ces messages sont des commandes qui indiquent l’unité à prendre des mesures. Pour plus d’informations, reportez-vous à [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="condition"></a>Condition

Fait référence à des informations d’état de périphérique, telles que la méthode de connectivité en cours d’utilisation, comme indiqué par une application de périphérique. Les périphériques peuvent aussi signaler leurs capacités. Vous pouvez interroger la condition et la capacité à l’aide de la double dispositif.

## <a name="data-point-message"></a>Message de point de données

Un message de point de données est un message de nuage vers le périphérique qui contient les données de télémétrie tels que la vitesse du vent ou de température.

## <a name="desired-properties"></a>Propriétés de votre choix

Dans le contexte des jumeaux de périphérique, propriétés souhaitées sont conjointement avec *signalé des propriétés* pour synchroniser la configuration de périphérique ou de condition. Propriétés souhaitées ne peuvent être définies par l’application back end et sont respectées par l’application de périphérique. 

## <a name="device-to-cloud-d2c"></a>Périphérique-nuage (D2C)

Généralement utilisé pour faire référence aux messages envoyés à partir d’un périphérique connecté au concentrateur de IoT. Ces messages peuvent être des messages [interactifs](#interactive-message) ou [de point de données](#data-point-message) . Pour plus d’informations, reportez-vous à [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="device-identity-registry"></a>Registre d’identité de périphérique

Le [Registre d’identité de périphérique](iot-hub-devguide-identity-registry.md) est le composant intégré d’un concentrateur IoT qui stocke des informations sur les périphériques individuels autorisés à se connecter à un concentrateur.

## <a name="device"></a>DISPOSITIF

Dans le contexte de IoT, un périphérique est généralement un périphérique informatique autonome à petite échelle, qui peut collecter des données ou piloter d’autres périphériques. Par exemple, un périphérique peut être un dispositif de surveillance de l’environnement, ou un contrôleur pour les systèmes d’abreuvement et la ventilation dans une serre.

## <a name="device-twin"></a>Double du périphérique

Un [double de périphérique](iot-hub-devguide-device-twins.md) est une copie de concentrateur IoT les paramètres de condition et la configuration d’un périphérique physique. Vous pouvez utiliser un double dispositif pour gérer la configuration du périphérique physique.

## <a name="direct-method"></a>Méthode directe

Une [méthode directe](iot-hub-devguide-direct-methods.md) est un moyen de déclencher une méthode à exécuter sur un périphérique en appelant une API sur votre concentrateur IoT.

## <a name="event-hub-compatible-endpoint"></a>Point de terminaison compatible avec concentrateur événement

Pour lire des messages de périphérique-nuage envoyés à votre concentrateur IoT, vous pouvez vous connecter à un point de terminaison sur votre concentrateur et utiliser n’importe quelle méthode d’événement compatible avec le concentrateur à lire ces messages. Méthodes compatibles avec le concentrateur d’événements incluent à l’aide de l’événement concentrateurs SDK et Azure flux Analytique.

## <a name="field-gateway"></a>Passerelle de champ

Une passerelle de champ permet de connecter des périphériques qui ne peuvent pas se connecter directement au concentrateur de IoT et est généralement déployé localement avec vos périphériques. Pour plus d’informations, consultez [Nouveautés Azure IoT concentrateur ?](iot-hub-what-is-iot-hub.md).

## <a name="job"></a>Travail

Back-end votre solution peut utiliser les travaux pour planifier et suivre les activités sur un ensemble de périphériques sont enregistrés avec votre concentrateur IoT. Les activités comprennent la mise à jour des propriétés du périphérique double que vous le souhaitez, les balises double périphérique de mise à jour et l’appel des méthodes directes.

## <a name="protocol-gateway"></a>Passerelle de protocole

Une passerelle de protocole est généralement déployée dans le nuage et offre des services de traduction pour les périphériques qui se connectent à IoT concentrateur de protocole. Pour plus d’informations, consultez [Nouveautés Azure IoT concentrateur ?](iot-hub-what-is-iot-hub.md).

## <a name="interactive-message"></a>Message interactif

Un message interactif est un nuage-dispositif qui déclenche une action immédiate dans l’application principale. Par exemple, un périphérique peut envoyer une alerte sur une défaillance qui doit être enregistrée automatiquement dans un système CRM.

## <a name="iot-hub"></a>Concentrateur de IoT

IoT concentrateur est un service de Azure entièrement géré qui permet la communication bidirectionnelle fiables et sécurisées entre des millions de périphériques de IoT et un back-end de solution. Pour plus d’informations, consultez [Nouveautés Azure IoT concentrateur ?](iot-hub-what-is-iot-hub.md).

## <a name="iot-suite"></a>IoT Suite

Azure IoT Suite regroupe ensemble plusieurs services Azure grâce à des solutions préconfigurées. Ces solutions préconfigurées vous permettent de vous familiariser rapidement avec les implémentations de bout en bout pour des scénarios courants de IoT. Pour plus d’informations, consultez [Nouveautés Azure IoT Suite ?](../iot-suite/iot-suite-overview.md).

## <a name="job"></a>Travail

Un [travail](iot-hub-devguide-jobs.md) IoT concentrateur vous permet d’effectuer des opérations telles qu’un microprogramme mise à niveau sur plusieurs périphériques connectés à votre concentrateur.

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/) est un des protocoles de messagerie qui prend en charge les IoT Hub pour communiquer avec les périphériques. Pour plus d’informations sur les protocoles de messagerie qui prend en charge le concentrateur de IoT, reportez-vous à [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="reported-properties"></a>Propriétés déclarées

Dans le contexte des jumeaux de périphérique, les propriétés déclarées sont utilisées conjointement avec les *propriétés souhaitées* pour synchroniser la configuration de périphérique ou de condition. Les propriétés déclarées ne peut être définies par l’application de périphérique et peuvent lire et interrogées par le principal de l’application.

## <a name="tags"></a>Balises

Dans le contexte des jumeaux de devcie, les balises sont périphérique meta-données stockées et récupérées par l’application back-end sous la forme d’un document JSON. Les balises ne sont pas visibles par les applications sur un périphérique.

## <a name="system-properties"></a>Propriétés système

Dans le contexte des jumeaux de périphérique, les propriétés système sont en lecture seule et incluent des informations concernant l’utilisation de périphérique comme dernier état de temps et de la connexion de l’activité.