<properties
 pageTitle="Guide du développeur - Registre d’identité de périphérique | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub - description du Registre d’identité de périphérique et comment l’utiliser pour gérer vos périphériques"
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

# <a name="manage-device-identities-in-iot-hub"></a>Gérer les identités de périphérique IoT moyeu

## <a name="overview"></a>Vue d’ensemble

Chaque concentrateur IoT a un registre d’identité de périphérique qui stocke des informations sur les périphériques qui sont autorisés à se connecter au concentrateur. Avant de connecter un périphérique à un concentrateur, il doit y avoir une entrée pour ce périphérique dans le Registre d’identité de périphérique du concentrateur. Un périphérique doit également s’authentifier avec le hub en fonction des informations d’identification stockées dans le Registre d’identité de périphérique.

À un niveau élevé, le Registre d’identité de périphérique est une collection de reste compatible identité de ressources de périphérique. Lorsque vous ajoutez une entrée au Registre, IoT concentrateur crée un ensemble de ressources de périphérique dans le service de la file d’attente contient des messages en vol de nuage vers le périphérique.

### <a name="when-to-use"></a>Quand utiliser

Utilisez le Registre d’identité de périphérique lorsque vous avez besoin configurer les périphériques qui se connectent à un concentrateur de IoT vous et lorsque vous avez besoin contrôler l’accès par périphérique pour les points de terminaison faisant face à l’appareil dans votre concentrateur.

> [AZURE.NOTE] Le Registre d’identité de périphérique ne contient-elle pas toutes les métadonnées spécifiques à l’application.

## <a name="device-identity-registry-operations"></a>Opérations de périphérique identité du Registre

Le Registre d’identité de périphérique IoT concentrateur expose les opérations suivantes :

* Créer l’identité du périphérique
* Identité de dispositif de mise à jour
* Récupérer l’identité du périphérique par ID
* Supprimer l’identité du périphérique
* Liste d’identités jusqu'à 1000
* Exporter toutes les identités pour le stockage des objets blob Azure
* Importer les identités depuis le stockage blob Azure

Toutes ces opérations peuvent utiliser l’accès concurrentiel optimiste, comme spécifié dans [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] La seule façon de récupérer toutes les identités dans le Registre d’identité d’un concentrateur est d’utiliser l' [exportation] [ lnk-export] fonctionnalité.

Un registre d’identité IoT concentrateur de périphérique :

- Ne contient pas de métadonnées de l’application.
- Est accessible comme un dictionnaire, à l’aide de l' **ID de périphérique** comme clé.
- Ne gère pas les requêtes expressifs.

Une solution IoT a généralement une banque spécifique à la solution distincte qui contient les métadonnées spécifiques à l’application. Par exemple, le magasin de solutions spécifiques dans une solution de création de smart doit enregistrer la pièce dans laquelle un capteur de température est déployé.

> [AZURE.IMPORTANT] N’utilisez le Registre d’identité de périphérique pour la gestion des périphériques et des opérations de provisionnement. Au moment de l’exécution des opérations de haut débit ne doivent pas dépendre de l’exécution d’opérations dans le Registre d’identité de périphérique. Par exemple, la vérification de l’état de la connexion d’un périphérique avant l’envoi d’une commande n’est pas un modèle de prise en charge. Pensez à vérifier le [taux de limitation] [ lnk-quotas] pour le Registre d’identité de périphérique et la [pulsation du périphérique] [ lnk-guidance-heartbeat] modèle.

## <a name="disable-devices"></a>Désactivation de périphériques

Vous pouvez désactiver des périphériques en mettant à jour de la propriété **status** d’une identité dans le Registre. En général, vous utilisez cette propriété dans deux scénarios :

- Au cours d’un processus de configuration d’orchestration. Pour plus d’informations, reportez-vous à la section [Mise en service du dispositif][lnk-guidance-provisioning].
- Si, pour une raison quelconque, vous pensez à un périphérique est compromis ou est devenu non autorisé.

## <a name="import-and-export-device-identities"></a>Importer et exporter les identités des appareils

Vous pouvez exporter les identités des appareils en vrac à partir du Registre d’identité d’un concentrateur IoT, à l’aide des opérations asynchrones sur [point de terminaison fournisseur de ressource IoT concentrateur][lnk-endpoints]. Les exportations sont des tâches de longue qui utilisent un conteneur blob de fourni par le client pour enregistrer les données d’identité de périphérique de lecture à partir du Registre de l’identité.

Vous pouvez importer les identités des appareils en vrac dans le Registre d’identité d’un concentrateur IoT, à l’aide des opérations asynchrones sur [point de terminaison fournisseur de ressource IoT concentrateur][lnk-endpoints]. Les importations sont des travaux de longue qui utilisent des données dans un conteneur de blob de fourni par le client pour écrire des données d’identité de périphérique dans le Registre d’identité de périphérique.

- Pour plus d’informations sur l’importation et l’exportation des API, voir [fournisseur de ressources IoT concentrateur reste API][lnk-resource-provider-apis].
- Pour en savoir plus sur l’exécution d’importation et d’exportation des travaux, voir [gestion d’en bloc des identités de dispositif concentrateur de IoT][lnk-bulk-identity].

## <a name="device-provisioning"></a>Configuration du périphérique

Les données de périphérique contenant une solution IoT donnée dépendant des besoins spécifiques de cette solution. Mais, au minimum, une solution doit stocker des clés d’authentification et les identités des appareils. Concentrateur de IoT Azure comprend un registre d’identité qui peut stocker des valeurs pour chaque périphérique ID, clés d’authentification, des codes d’état. Une solution peut utiliser d’autres services Azure comme le stockage par table Azure, stockage blob Azure ou DocumentDB d’Azure pour stocker des données de périphérique supplémentaire.

*Configuration du périphérique* est le processus d’ajout de données initiale d’un appareil pour les magasins de votre solution. Pour activer un nouveau périphérique pour se connecter à un concentrateur, vous devez ajouter un nouvel ID de périphérique et les clés dans le Registre d’identité IoT concentrateur. Dans le cadre du processus de mise en service, vous devrez peut-être d’initialiser des données spécifiques au périphérique dans d’autres banques de la solution.

## <a name="device-heartbeat"></a>Pulsation de périphérique

Le Registre d’identité IoT Hub contient un champ appelé **connectionState**. Vous ne devez utiliser le champ **connectionState** pendant le développement et le débogage, IoT solutions ne doivent pas interroger le champ au moment de l’exécution (par exemple, pour vérifier si un périphérique est connecté afin de décider s’il faut envoyer un message de nuage-dispositif ou un SMS).

Si votre solution IoT a besoin de savoir si un périphérique est connecté (au moment de l’exécution, ou avec plus de précision que celle offerte par la propriété **connectionState** ), votre solution doit implémenter le *modèle de pulsation*.

Dans le modèle de pulsation, le périphérique envoie des messages de périphérique-nuage au moins une fois chaque montant fixe de temps (par exemple, au moins une fois par heure). Cela signifie que même si un périphérique n’a pas de données à envoyer, il envoie toujours un message périphérique-nuage vide (généralement avec une propriété qui identifie le système comme une pulsation). Sur le côté service, la solution tient à jour une carte avec la dernière pulsation reçue pour chaque périphérique et suppose qu’il existe un problème avec un périphérique si elle ne reçoit pas un message de pulsation dans le délai prévu.

Une implémentation plus complexe pourrait inclure les informations de [l’analyse des opérations de] [ lnk-devguide-opmon] pour identifier les périphériques qui tente de se connecter ou communiquer, mais échoue. Lorsque vous implémentez le modèle de pulsation, pensez à vérifier les [Quotas de concentrateur IoT et ralentit sa][lnk-quotas].

> [AZURE.NOTE] Si l’état de connexion de périphérique uniquement pour déterminer s’il faut envoyer les messages du nuage vers le périphérique a besoin d’une solution IoT et que les messages ne sont pas diffusées aux grands groupes de périphériques, un modèle beaucoup plus simple à prendre en compte est d’utiliser un court délai d’expiration. Cela permet d’obtenir le même résultat que la mise à jour un Registre état de connexion de périphérique à l’aide du modèle de pulsation, tout en étant beaucoup plus efficace. Il est également possible, en demandant à être averti par un concentrateur IoT de quels périphériques sont en mesure de recevoir des messages, et qui ne sont pas en ligne ou qui n’ont pu être les accusés de réception message.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent plus d’informations sur le Registre d’identité devcie.

## <a name="device-identity-properties"></a>Propriétés d’identité de périphérique

Identités des appareils sont représentées en tant que documents JSON avec les propriétés suivantes.

| Propriété | Options | Description |
| -------- | ------- | ----------- |
| ID de périphérique | obligatoire, en lecture seule sur les mises à jour | Une chaîne qui respecte la casse (jusqu'à 128 caractères), les caractères alphanumériques ASCII 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | obligatoire, en lecture seule | Chaîne respectant la casse, généré par le concentrateur jusqu'à 128 caractères. Cela permet de distinguer les périphériques avec le même **ID de périphérique**, lorsqu’ils ont été supprimés et recréés. |
| eTag | obligatoire, en lecture seule | Chaîne représentant un etag faible pour l’identité du périphérique, en fonction de la [RFC7232][lnk-rfc7232].|
| AUTH | facultatif | Un objet composite contenant des éléments de sécurité et les informations d’authentification. |
| AUTH.symkey | facultatif | Un objet composite contenant une principale et une clé secondaire, stockées dans le format base64. |
| état | Obligatoire | Un indicateur d’accès. Peut être **activé** ou **désactivé**. Si **activé**, le périphérique est autorisé à se connecter. Si **désactivé**, ce périphérique ne peut pas accéder à n’importe quel point de terminaison orientés vers le périphérique. |
| statusReason | facultatif | Une chaîne de caractères de longueur 128 qui stocke la raison du statut d’identité de périphérique. Tous les caractères UTF-8 sont autorisées. |
| statusUpdateTime | en lecture seule | Un indicateur temporel, indiquant la date et l’heure de la dernière mise à jour de l’état. |
| connectionState | en lecture seule | Un champ indiquant l’état de la connexion : soit **connecté** ou **déconnecté**. Ce champ représente la vue IoT concentrateur de l’état de connexion du périphérique. **Important**: ce champ doit être utilisé uniquement à des fins de développement/de débogage. L’état de connexion est mis à jour uniquement pour les périphériques à l’aide de MQTT ou AMQP. En outre, il repose sur de niveau protocole Ping (ping MQTT, ou AMQP ping), et elle peut avoir un délai maximal de 5 minutes seulement. Pour ces raisons, il est possible de faux positifs, tels que les périphériques signalés comme étant connecté, mais qui sont réellement déconnecté. |
| connectionStateUpdatedTime | en lecture seule | Un indicateur temporel, indiquant la date et l’heure de la dernière à l’état de la connexion a été mis à jour. |
| lastActivityTime  | en lecture seule | Un indicateur temporel, indiquant la date et l’heure de la dernière le périphérique connecté, reçu ou envoyé un message. |

> [AZURE.NOTE] État de la connexion ne peut représenter que la vue IoT concentrateur de l’état de la connexion. Mises à jour de cet état peuvent être retardées, en fonction des configurations et des conditions réseau.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

D’autres rubriques de référence dans le Guide du développeur incluent :

- [Les points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution.
- [Les quotas et la régulation] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT Hub et le comportement d’accélération s’attendre lorsque vous utilisez le service.
- [SDK de périphérique et service IoT concentrateur] [ lnk-sdks] répertorie la langue différents kits de développement logiciel vous une utilisation lorsque vous développez des services et des périphériques à la fois les applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, procédés et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations du concentrateur de IoT sur vos jumeaux de périphérique, les méthodes et les tâches.
- [Prise en charge de IoT concentrateur MQTT] [ lnk-devguide-mqtt] fournit plus d’informations sur la prise en charge de IoT Hub pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez appris comment utiliser le Registre d’identité de périphérique IoT concentrateur, peut vous intéresser dans les rubriques du Guide de développement suivantes :

- [Contrôle de l’accès à IoT concentrateur][lnk-devguide-security]
- [Des jumeaux de périphérique permet de synchroniser l’état et les configurations][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un périphérique][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs périphériques][lnk-devguide-jobs]

Si vous souhaitez essayer certaines des concepts décrits dans cet article, vous intéresser dans ce didacticiel IoT Hub :

- [Mise en route avec Azure IoT concentrateur][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md