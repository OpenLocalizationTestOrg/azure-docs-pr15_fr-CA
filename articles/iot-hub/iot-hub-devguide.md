<properties
 pageTitle="Rubriques de guide de développeur pour IoT concentrateur | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure qui inclut des points de terminaison IoT concentrateur, la sécurité, Registre d’identité de périphérique, gestion des périphériques et de messagerie"
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

# <a name="azure-iot-hub-developer-guide"></a>Guide de développeur de concentrateur de IoT Azure

Concentrateur de IoT Azure est un service entièrement géré qui vous aide à activer fiables et sécurisées des communications bidirectionnelles entre millions d’appareils de IoT et mettre fin à une demande de retour.

Concentrateur de IoT Azure vous offre :

* Sécuriser les communications à l’aide des informations d’identification de sécurité de chaque équipement et contrôle d’accès.
* Périphérique-nuage et nuage-DISPOSITIF une évolutivité messagerie fiable.
* Connectivité de périphérique facile avec les bibliothèques de périphérique pour les plates-formes et les langues les plus populaires.

Ce guide du développeur IoT concentrateur comprend les articles suivants :

- [Envoyer et recevoir des messages avec concentrateur de IoT] [ devguide-messaging] décrit les fonctionnalités de messagerie (périphérique-nuage et nuage-DISPOSITIF) qui expose les IoT concentrateur. Cet article comporte également des informations sur des sujets tels que les formats de message et les protocoles de communication pris en charge et les numéros de ports qu’ils utilisent.
- [Télécharger des fichiers à partir d’un périphérique] [ devguide-upload] décrit comment vous pouvez télécharger des fichiers à partir d’un périphérique. Cet article comporte également des informations sur des sujets tels que les notifications le processus uplaod peut envoyer.
- [Gérer les identités de dispositif concentrateur de IoT] [ devguide-identities] décrit ce qu’il stocke les informations de Registre d’identité de chaque concentrateur IoT périphérique, et comment vous pouvez accéder et le modifier.
- [Contrôler l’accès à un Hub de IoT] [ devguide-security] décrit le modèle de sécurité utilisé pour autoriser l’accès à la fonctionnalité de concentrateur de IoT les deux périphériques et composants de nuage. L’article contient des informations sur l’utilisation des jetons et des certificats X.509 et des informations sur les autorisations que vous pouvez accorder.
- [Des jumeaux de périphérique permet de synchroniser l’état et les configurations] [ devguide-device-twins] décrit le concept de *double de périphérique* et les fonctionnalités qu’elle expose par exemple la synchronisation d’un périphérique avec son double. L’article contient des informations sur les données stockées dans un double dispositif.
- [Appeler une méthode directe sur un périphérique] [ devguide-directmethods] décrit le cycle de vie d’une méthode directe, informations sur la façon d’appeler des méthodes sur un périphérique à partir de votre application back-end et de gérer la méthode directe sur votre périphérique.
- [Planifier des tâches sur plusieurs périphériques] [ devguide-jobs] décrit comment vous pouvez planifier des tâches sur plusieurs périphériques. L’article explique comment envoyer des tâches qui effectuent des tâches telles que l’exécution d’une méthode directe, mise à jour d’un devcie à l’aide d’un double devcie. Il décrit également comment interroger le statut d’une tâche.
- [Référence - points de terminaison IoT concentrateur] [ devguide-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution. Cet article décrit également comment vous pouvez utiliser une passerelle de champ permettant d’activer certains périphériques pour se connecter à vos points de terminaison IoT concentrateur.
- [Référence - langage de requête pour jumeaux, procédés et travaux] [ devguide-query] décrit ce langage de requête qui permet de récupérer les informations de votre concentrateur sur vos jumeaux de périphérique et les tâches.
- [Référence - des quotas et des limitations] [ devguide-quotas] résume les quotas définis dans le service IoT Hub et le comportement la limitation, vous pouvez vous attendre à voir lorsque vous dépassez un quota.
- [Référence - périphérique et service SDK] [ devguide-sdks] listes de kits de développement logiciel vous permet de développement des applications à la fois des services et des périphériques qui interagissent avec votre concentrateur IoT. L’article contient des liens vers la documentation en ligne de l’API.
- [Référence - prise en charge de IoT concentrateur MQTT] [ devguide-mqtt] fournit des informations détaillées sur comment IoT Hub prend en charge le protocole MQTT. L’article décrit la prise en charge du protocole MQTT intégré pour les kits de développement et fournit des informations sur l’utilisation du protocole MQTT directement.
- [Glossaire] [ devguide-glossary] une liste de termes IoT concentrateur communs.



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

