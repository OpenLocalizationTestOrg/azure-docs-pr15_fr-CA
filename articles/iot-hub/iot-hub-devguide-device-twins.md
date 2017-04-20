<properties
 pageTitle="Developer guide - comprendre jumeaux de périphérique | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub - utilisation des jumeaux de périphérique pour synchroniser les données d’état et de configuration entre IoT concentrateur et vos périphériques"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>Comprendre les jumeaux périphérique - aperçu

## <a name="overview"></a>Vue d’ensemble

*Des jumeaux de périphérique* sont des documents JSON qui stockent des informations d’état de périphérique (meta-données, configuration et conditions). Concentrateur de IoT persiste un double dispositif pour chaque périphérique que vous connectez à IoT concentrateur. Cet article décrit :

* la structure de la double dispositif : *balises*, *souhaité* et *signalé des propriétés*, et
* les opérations exécutées par les applications de périphérique et back-end peut sur jumeaux de périphérique.

> [AZURE.NOTE] À ce stade, jumeaux de périphérique est accessibles uniquement à partir de périphériques qui se connectent au concentrateur IoT utilisant le protocole MQTT. Veuillez vous reporter à la [prise en charge de la MQTT] [ lnk-devguide-mqtt] l’article pour obtenir des instructions sur la conversion d’applications de périphérique existant à utiliser MQTT.

### <a name="when-to-use"></a>Quand utiliser

Utilisez des jumeaux de périphérique pour :

* Stocker des données spécifiques au périphérique meta-dans le nuage, l’emplacement de déploiement, par exemple d’un distributeur automatique.
* Rapport des informations d’état actuelles telles que les capacités disponibles et des conditions à partir de votre application de périphérique, par exemple, un périphérique de connexion via cellulaire ou Wi-Fi.
* Synchroniser l’état de flux de travail longue entre le périphérique app et back end, par exemple, back-end spécifiant la nouvelle version du firmware à installer et que l’application de périphérique reporting des différents stades du processus de mise à jour.
* Interroger votre périphérique meta-données, configuration ou état.

Utiliser des [messages de périphérique-nuage] [ lnk-d2c] pour les séquences d’événements horodatés comme séries de données des capteurs ou des alarmes. Utilisez [les méthodes de nuage-DISPOSITIF] [ lnk-methods] pour un contrôle interactif de périphériques, tels qu’un ventilateur sous tension.

## <a name="device-twins"></a>Jumeaux de périphérique

Jumeaux de périphérique stockage des informations relatives au périphérique qui :

- Dispositif et back end permet de synchroniser la configuration et des conditions de périphérique.
- L’application principale permet de requête et cible longues opérations.

Le cycle de vie d’un double dispositif est lié à [l’identité de cette unité]correspondante[lnk-identity]. Jumeaux est implicitement créé et supprimées lorsqu’une nouvelle identité de périphérique est créée ou supprimée dans IoT concentrateur.

Un double dispositif est un document JSON qui inclut :

* **Les balises**. Un document JSON, lues et écrites par le back-end. Les balises ne sont pas visibles par les applications de périphérique.
* **Propriétés de souhaité**. Utilisé conjointement avec les propriétés déclarées pour synchroniser la configuration de périphérique ou de condition. Propriétés souhaitées ne peuvent être définies par l’application back end et peuvent être lues par l’application de périphérique. L’application de périphérique peut être avertie en temps réel des modifications sur les propriétés de votre choix.
* **Propriétés de la déclaration**. Utilisé conjointement avec les propriétés souhaitées pour synchroniser la configuration de périphérique ou de condition. Les propriétés déclarées ne peut être définies par l’application de périphérique et peuvent lire et interrogées par le principal de l’application.

En outre, la racine de la double DISPOSITIF contient les propriétés en lecture seule à partir de l’identité correspondante, comme contenus dans le [Registre de périphérique identité][lnk-identity].

![][img-twin]

Voici un exemple d’un document JSON double dispositif :

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

Dans l’objet racine, sont les propriétés système et les objets conteneur pour `tags` et `reported` et `desired properties`. Le `properties` conteneur contient certains éléments en lecture seule (`$metadata`, `$etag`, et `$version`) qui sont respectivement décrits dans les [métadonnées de double] [ lnk-twin-metadata] et [d’accès concurrentiel optimiste] [ lnk-concurrency] sections.

### <a name="reported-property-example"></a>Signalée, propriété-exemple

Dans l’exemple ci-dessus, la double DISPOSITIF contient un `batteryLevel` propriété qui est signalée par l’application de périphérique. Cette propriété permet d’interroger et de fonctionner sur les périphériques basés sur le dernier niveau de la batterie signalée. Un autre exemple aurait les fonctionnalités du périphérique périphérique application rapport ou les options de connectivité.

Remarque Les propriétés des simplifier les scénarios où le back-end n’est intéressé par la dernière valeur connue d’une propriété. Utiliser des [messages de périphérique-nuage] [ lnk-d2c] si le back-end doit traiter de télémétrie de périphérique sous la forme de séquences d’événements horodatés, tels que les séries temporelles.

### <a name="desired-configuration-example"></a>Exemple de configuration souhaitée

Dans l’exemple ci-dessus, le `telemetryConfig` propriétés souhaitées et signalées sont utilisées par l’application de fin et dispositif arrière pour synchroniser la configuration de télémétrie pour ce périphérique. Par exemple :

1. Le principal de l’application définit la propriété désirée avec la valeur de la configuration souhaitée. Voici la partie du document avec la propriété désirée :

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. L’application de périphérique est notifiée de la modification immédiatement si connecté ou premier se reconnecter. L’application de périphérique signale ensuite la configuration mise à jour (ou une condition d’erreur à l’aide du `status` propriété). Voici la partie des propriétés déclarées :

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. Le principal de l’application peut conserver le suivi des résultats de l’opération de configuration sur plusieurs périphériques, en [interrogeant] [ lnk-query] jumeaux.

> [AZURE.NOTE] Les extraits de code ci-dessus sont des exemples, optimisées pour une meilleure lisibilité, d’une façon possible à l’encodage d’une configuration de périphérique et de son état. IoT Hub n’impose pas à un schéma spécifique pour les propriétés souhaitées et signalées dans le jumeaux de périphérique.

Dans de nombreux cas jumeaux permettent de synchroniser les opérations longues telles que des mises à jour du firmware. Reportez-vous à [utiliser les propriétés de votre choix pour configurer les périphériques de] [ lnk-twin-properties] pour plus d’informations sur l’utilisation des propriétés de synchroniser et de suivre le temps des opérations exécutées sur les périphériques.

## <a name="back-end-operations"></a>Opérations de back-end

Le back-end opère sur le double utilisant les opérations atomiques suivantes, exposées par l’intermédiaire de HTTP :

1. **Double par id de récupérer**. Cette opération retourne le contenu de document de la double, y compris les balises et que vous le souhaitez, signalé et les propriétés système.
2. **Partiellement mise à jour de double**. Cette opération permet du mettre à jour partiellement des balises ou des propriétés de votre choix de la double back-end. La mise à jour partielle est exprimée sous la forme d’un document JSON qui ajoute ou met à jour de n’importe quelle propriété mentionnée. Propriétés `null` sont supprimés. Par exemple, suivantes crée une nouvelle propriété de votre choix avec la valeur `{"newProperty": "newValue"}`, remplace la valeur existante de `existingProperty` avec `"otherNewValue"`et supprime entièrement `otherOldProperty`. Aucune modification se produire à d’autres propriétés existantes ou les balises :

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Remplacer les propriétés de votre choix**. Cette opération permet le back-end complètement remplacer toutes les propriétés de votre choix et les remplacer par un nouveau document JSON pour `properties/desired`.
4. **Remplacer des balises**. Comme pour remplacer les propriétés de votre choix, cette opération permet le back end à complètement remplacer toutes les balises existantes et les remplacer par un nouveau document JSON pour `tags`.

Toutes les opérations ci-dessus prennent en charge [l’accès concurrentiel optimiste] [ lnk-concurrency] et requiert l’autorisation **ServiceConnect** , tel que défini dans la [sécurité] [ lnk-security] l’article.

En plus de ces opérations, le back-end peut interroger le jumeaux à l’aide d’un [langage de requête]de type SQL[lnk-query]et effectuer des opérations sur des ensembles volumineux de jumeaux à l’aide de [tâches][lnk-jobs].

## <a name="device-operations"></a>Opérations de périphérique

L’application de périphérique opère sur le double à l’aide d’opérations atomiques suivantes :

1. **Récupérer le double**. Cette opération retourne le contenu de document de la double (y compris les balises et vous le souhaitez, signalé et propriétés système) pour le périphérique actuellement connecté.
2. **Partiellement mise à jour signalée propriétés**. Cette opération permet de la mise à jour partielle des propriétés déclarées de l’appareil actuellement connecté. Il utilise le même format de mise à jour JSON comme serveur principal face à une mise à jour partielle des propriétés souhaitées.
3. **Observer souhaité des propriétés**. L’appareil actuellement connecté peut choisir d’être averti des mises à jour les propriétés de votre choix, dès qu’elles se produisent. Le périphérique reçoit le même formulaire de mise à jour (remplacement partiel ou complet) exécuté par le serveur principal.

Toutes les opérations ci-dessus requièrent l’autorisation **DeviceConnect** , tel que défini dans la [sécurité] [ lnk-security] l’article.

Le [dispositif d’Azure IoT SDK] [ lnk-sdks] permettent d’utiliser les opérations ci-dessus, de nombreux langages et plates-formes. Vous trouverez plus d’informations sur les détails de primitives du concentrateur IoT pour la synchronisation des propriétés de votre choix dans le [flux de la reconnexion de périphérique][lnk-reconnection].

> [AZURE.NOTE] Actuellement, jumeaux de périphérique est accessibles uniquement à partir de périphériques qui se connectent au concentrateur IoT utilisant le protocole MQTT.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent plus d’informations sur le contrôle de l’accès à votre concentrateur IoT.

## <a name="tags-and-properties-format"></a>Format des balises et des propriétés

Les balises, les propriétés souhaitées et signalées sont des objets JSON avec les restrictions suivantes :

* Toutes les clés dans les objets JSON sont des chaînes UNICODE 128 caractères respectant la casse. Autorisé de caractères exclure (segments C0 et C1), les caractères de contrôle UNICODE et `'.'`, `' '`, et `'$'`.
* Toutes les valeurs dans l’objet JSON peuvent être des types JSON suivants : booléen, nombre, chaîne, objet. Les tableaux ne sont pas autorisés.

## <a name="twin-size"></a>Taille de la double

Concentrateur de IoT impose une limite de taille de 8 Ko sur les valeurs de `tags`, `properties/desired`, et `properties/reported`, à l’exclusion des éléments en lecture seule.
La taille est calculée en comptant les caractères (segments C0 et C1) et l’espace de contrôle tous les caractères UNICODE à l’exclusion de `' '` lorsqu’il s’affiche en dehors d’une constante de chaîne.
IoT concentrateur rejettera toutes les opérations qui augmenteront la taille de ces documents au-dessus de la limite avec l’erreur.

## <a name="twin-metadata"></a>Métadonnées de double

IoT Hub gère l’horodatage de la dernière mise à jour pour chaque objet JSON dans propriétés souhaitées et signalées. Les horodatages sont en UTC et encodés dans le format [standard ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Par exemple :

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Ces informations sont conservées à chaque niveau (et pas uniquement les feuilles de la structure JSON) pour conserver les mises à jour de supprimer les clés de l’objet.

## <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

Balises, propriétés souhaitées et signalées tous les prend en charge l’accès concurrentiel optimiste.
Balises ont un etag, comme [RFC7232], qui représente de la balise JSON. Vous pouvez utiliser ce dans les opérations de mise à jour conditionnelle de la fin pour assurer la cohérence.

Les propriétés souhaitées et signalées n’ont pas les etags, mais ont une `$version` valeur qui est garanti être incrémentielle. Comme pour un etag, la version peut être utilisée par la partie mise à jour (une application de périphérique d’une propriété déclarée) ou le back-end pour une propriété de votre choix pour appliquer la cohérence des mises à jour.

Les versions sont également utiles lorsqu’un agent observation (par exemple, l’application de périphérique en observant les propriétés souhaitées) à concilier la concurrences entre le résultat d’une opération de récupération et une notification de mise à jour. La section [flux de reconnexion de périphérique] [ lnk-reconnection] fournit des informations supplémentaires.

## <a name="device-reconnection-flow"></a>Flux de reconnexion de périphérique

IoT concentrateur ne conserve pas les notifications de mise à jour de propriétés de votre choix pour les périphériques déconnectés. Il s’ensuit qu’un périphérique qui se connecte doit récupérer le document complet de propriétés de votre choix, en plus de l’abonnement pour les notifications de mise à jour. Étant donné la possibilité de la concurrences entre les notifications de mise à jour et de récupération complète, le flux suivant doit être assuré :

1. Application de périphérique se connecte à un concentrateur IoT.
2. Application de périphérique s’abonne pour les propriétés souhaitées des notifications de mise à jour.
3. Application de périphérique récupère le document complet pour les propriétés souhaitées.

L’application de périphérique peut ignorer toutes les notifications avec `$version` inférieure ou égale à la version du document récupéré complet. Ceci est possible parce que IoT concentrateur garantit que les versions incrémentent toujours.

> [AZURE.NOTE] Cette logique est déjà implémentée dans le [dispositif d’Azure IoT SDK][lnk-sdks]. Cette description est utile uniquement si l’application de périphérique ne peut pas utiliser de dispositif d’Azure IoT SDK et doit de programmer directement l’interface MQTT.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

D’autres rubriques de référence dans le Guide du développeur incluent :

- [Les points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution.
- [Les quotas et la régulation] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT Hub et le comportement d’accélération s’attendre lorsque vous utilisez le service.
- [SDK de périphérique et service IoT concentrateur] [ lnk-sdks] répertorie la langue différents kits de développement logiciel vous une utilisation lorsque vous développez des services et des périphériques à la fois les applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, procédés et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations du concentrateur de IoT sur vos jumeaux de périphérique, les méthodes et les tâches.
- [Prise en charge de IoT concentrateur MQTT] [ lnk-devguide-mqtt] fournit plus d’informations sur la prise en charge de IoT Hub pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous a permis de découvrir jumeaux de périphérique, vous serez peut-être intéressé dans les rubriques suivantes de Guide de développeur :

- [Appeler une méthode directe sur un périphérique][lnk-methods]
- [Planifier des tâches sur plusieurs périphériques][lnk-jobs]

Si vous souhaitez essayer certaines des concepts décrits dans cet article, vous intéresser dans les didacticiels IoT Hub suivants :

- [Comment utiliser le double de périphérique][lnk-twin-tutorial]
- [Comment utiliser les propriétés double][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png