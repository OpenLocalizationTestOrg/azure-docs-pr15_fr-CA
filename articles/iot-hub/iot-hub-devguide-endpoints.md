<properties
 pageTitle="Guide du développeur - points de terminaison IoT Hub | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub - informations de référence sur les points de terminaison IoT concentrateur"
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

# <a name="reference---iot-hub-endpoints"></a>Référence - points de terminaison IoT concentrateur

## <a name="list-of-iot-hub-endpoints"></a>Liste des points de terminaison IoT concentrateur

Concentrateur de IoT Azure est un service de mutualisée qui expose ses fonctionnalités à des différents acteurs. Le diagramme suivant illustre les différents points de terminaison que IoT concentrateur expose.

![Les points de terminaison IoT concentrateur][img-endpoints]

Voici une description des points de terminaison :

* **Fournisseur de ressources**. Le fournisseur de ressources IoT concentrateur expose un [Gestionnaire de ressources Azure] [ lnk-arm] interface qui permet aux propriétaires d’abonnement Azure créer et supprimer des concentrateurs de IoT et mettre à jour les propriétés du concentrateur IoT. Propriétés du concentrateur de IoT régissent [les stratégies de sécurité au niveau du concentrateur][lnk-accesscontrol], au lieu de contrôle d’accès au niveau du périphérique et les options fonctionnelles pour la messagerie nuage-dispositif et dispositif-nuage. Le fournisseur de ressources IoT Hub vous permet également [d’Exporter les identités des appareils][lnk-importexport].
* **Gestion des identités**. Chaque concentrateur IoT expose un ensemble de points de terminaison HTTP reste à gérer les identités des appareils (créer, récupérer, mettre à jour et supprimer). [Identités des appareils] [ lnk-device-identities] sont utilisés pour un dispositif d’authentification et contrôle d’accès.
* **Gestion des périphériques double**. Chaque concentrateur IoT expose un ensemble du point de terminaison HTTP reste de service orienté pour interroger et mettre à jour des [jumeaux de périphérique] [ lnk-twins] (mise à jour des balises et des propriétés).
* **Gestion des travaux**. Chaque concentrateur IoT expose un ensemble du point de terminaison côté service HTTP reste d’interroger et de gérer les [travaux de][lnk-jobs].
* **Points de terminaison de périphérique**. Pour chaque périphérique mis en service dans le Registre d’identité de périphérique, IoT concentrateur expose un ensemble de points de terminaison qu’un périphérique peut utiliser pour envoyer et recevoir des messages :
    - *Envoyer des messages de périphérique-nuage*. Utiliser ce point de terminaison pour [Envoyer des messages de périphérique-nuage][lnk-d2c].
    - *Réception des messages du nuage vers le périphérique*. Un périphérique utilise ce point de terminaison pour recevoir ciblée de [messages du nuage vers le périphérique][lnk-c2d].
    - *Lancer des téléchargements de fichiers*. Un périphérique utilise ce point de terminaison pour recevoir une URI de SAS de stockage Azure IoT concentrateur pour [télécharger un fichier][lnk-upload].
    - *Récupérer et les propriétés de mise à jour de double*. Un périphérique utilise cette terminaison pour accéder à son [double de périphérique][lnk-twins]de propriétés.
    - *Recevoir les demandes de méthodes directes*. Un périphérique utilise ce points de terminaison pour écouter les [méthodes directes][lnk-methods]de demandes.

    Ces points de terminaison sont exposées à l’aide de la [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 et [1.0 de AMQP] [ lnk-amqp] protocoles. Notez que AMQP est également disponible via le [protocole WebSocket] [ lnk-websockets] sur le port 443.
    
    Endpoins les méthodes jumeaux ne sont disponibles qu’à l’aide de [MQTT v3.1.1][lnk-mqtt].

* **Points de terminaison de Service**. Chaque concentrateur IoT expose un ensemble de points de terminaison de que votre application principale peut utiliser pour communiquer avec vos périphériques. Ces points de terminaison sont actuellement uniquement exposées à l’aide de [AMQP] [ lnk-amqp] protocole, à l’exception de l’extrémité d’appel de méthode qui est exposée via HTTP 1.1.
    - *Recevoir les messages de périphérique-nuage*. Ce point de terminaison est compatible avec les [Concentrateurs d’événement Azure][lnk-event-hubs]. Un service back-end il permet de lire tous les [messages de périphérique-nuage] [ lnk-d2c] envoyées par vos périphériques.
    - *Nuage-DISPOSITIF envoyez et recevez des accusés de réception de livraison*. Ces points de terminaison activer votre application back-end envoyer des [messages du nuage-DISPOSITIF]de fiable[lnk-c2d]et pour recevoir des accusés de réception d’expiration ou livraison correspondante.
    - *Recevoir des notifications de fichier*. Ce point de terminaison de messagerie vous permet de recevoir des notifications lorsque vos périphériques transférer correctement un fichier. 
    - *Appel de la méthode directe*. Un service back-end appeler une [méthode directe] permet à ce point de terminaison[ lnk-methods] sur un périphérique.

[IoT concentrateur API et les kits de développement logiciel] [ lnk-sdks] décrit les différentes façons d’accéder à ces points de terminaison.

Enfin, il est important de noter que tous les points de terminaison IoT concentrateur utilisent le [TLS] [ lnk-tls] protocole et aucun point de terminaison est jamais exposé sur les canaux non crypté/non sécurisé.

## <a name="field-gateways"></a>Passerelles de champ

Dans une solution IoT, une *passerelle de champ* se trouve entre vos périphériques et vos points de terminaison IoT concentrateur. Il est généralement situé près de vos périphériques. Vos périphériques de communiquent directement avec la passerelle de champ à l’aide d’un protocole pris en charge par les périphériques. La passerelle de champ se connecte à un point de terminaison IoT concentrateur à l’aide d’un protocole pris en charge par le concentrateur de IoT. Une passerelle de champ peut être matériel hautement spécialisé ou un ordinateur de faible puissance exécutant qui accomplit le scénario de bout en bout pour la passerelle auquel est destinée le logiciel.

Vous pouvez utiliser le [Kit de développement logiciel Azure IoT passerelle] [ lnk-gateway-sdk] pour implémenter une passerelle de champ. Ce kit offre des fonctionnalités spécifiques telles que la possibilité de multiplexer la communication à partir de plusieurs périphériques sur la même connexion au concentrateur de IoT.

## <a name="next-steps"></a>Étapes suivantes

D’autres rubriques de référence dans ce guide de développeur IoT concentrateur incluent :

- [Langage de requête pour jumeaux, des méthodes et des tâches][lnk-devguide-query]
- [Quotas et limitation][lnk-devguide-quotas]
- [Prise en charge de la MQTT de concentrateur IoT][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md