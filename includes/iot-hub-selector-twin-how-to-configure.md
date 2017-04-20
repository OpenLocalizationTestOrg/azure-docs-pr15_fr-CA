> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)

## <a name="introduction"></a>Introduction

Dans [mise en route de concentrateur de IoT jumeaux][lnk-twin-tutorial], vous avez appris à définir des meta-données de périphérique à partir de votre solution back-end à l’aide de *balises*, signaler des conditions de périphérique à partir d’une application de périphérique à l’aide des *propriétés déclarées*et ces informations à l’aide d’un langage de type SQL de la requête.

Dans ce didacticiel, vous allez apprendre à utiliser le de la double *propriétés souhaitées* conjointement *a signalé des propriétés*, à configurer à distance les applications de périphérique. Plus précisément, ce didacticiel montre comment double signalés et propriétés souhaitées activer une configuration à plusieurs étapes d’un paramètre d’application de périphérique et fournissent de la visibilité à la solution back-end de l’état de cette opération sur tous les périphériques.

À un niveau élevé, ce didacticiel suit le *modèle d’état souhaité* pour la gestion des périphériques. L’idée fondamentale de ce modèle est d’avoir le back-end de solution spécifier l’état souhaité de périphériques gérés, au lieu d’envoyer des commandes spécifiques. Cela place le périphérique chargé d’établir la meilleure façon d’atteindre l’état souhaité (très important dans les scénarios IoT où les conditions de périphérique spécifique affectent la possibilité de procéder immédiatement à des commandes spécifiques), tout en permanence reporting pour le back-end pour l’état actuel et les conditions d’erreur potentielles. Le modèle d’état souhaité est déterminant pour la gestion de grands ensembles de périphériques, car elle permet le back-end d’avoir une visibilité totale de l’état du processus de configuration sur tous les périphériques.
Vous trouverez plus d’informations concernant le rôle du modèle d’état souhaité dans la gestion des périphériques dans la [Gestion des périphériques de vue d’ensemble d’Azure IoT concentrateur][lnk-dm-overview].

> [AZURE.NOTE] Dans les scénarios où les périphériques sont contrôlées de façon plus interactive (activer un ventilateur à partir d’une application contrôlées par l’utilisateur), envisagez d’utiliser des [méthodes de nuage-DISPOSITIF][lnk-methods].

Dans ce didacticiel, les modifications de back-end d’application la configuration de télémétrie d’un équipement cible et, qui, de l’application de périphérique suit un processus en plusieurs étapes pour appliquer une mise à jour de configuration (par exemple, nécessitant un redémarrage du module logiciel), qui simule de ce didacticiel avec un simple délai).

Le serveur principal stocke la configuration dans les propriétés de votre choix de la double dispositif de la manière suivante :

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [AZURE.NOTE] Dans la mesure où les configurations peuvent être des objets complexes, elles sont généralement affectés de numéros d’identification uniques ( [GUID]ou hachages[lnk-guid]) pour simplifier les comparaisons.

L’application de périphérique signale sa configuration actuelle, la mise en miroir de la propriété souhaitée **telemetryConfig** dans les propriétés déclarées :

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Notez comment le signalée **telemetryConfig** possède une propriété supplémentaire **état**, utilisé pour signaler l’état du processus de mise à jour de configuration.

Lorsqu’une nouvelle configuration souhaitée est reçue, l’application de périphérique indique une configuration en attente en modifiant les informations :

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Puis, à un moment ultérieur, l’application de périphérique signalera la réussite de l’échec de cette opération en mettant à jour la propriété ci-dessus.
Notez comment le back-end est capable, à tout moment, pour interroger l’état du processus de configuration sur tous les périphériques.

Ce didacticiel vous montre comment procéder pour :

- Créer un périphérique simulé qui reçoit les mises à jour de la configuration de la fin et signale plusieurs mises à jour en tant que *propriétés déclarées* sur le processus de mise à jour de configuration.
- Créer une application back-end qui met à jour de la configuration souhaitée d’un périphérique et interroge ensuite le processus de mise à jour de configuration.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier