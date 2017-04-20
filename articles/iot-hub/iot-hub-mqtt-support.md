<properties
 pageTitle="Prise en charge de IoT concentrateur MQTT | Microsoft Azure"
 description="Description de MQTT prise en charge dans IoT au niveau du concentrateur"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>Prise en charge de la MQTT de concentrateur IoT

IoT Hub permet aux périphériques de communiquer avec les points de terminaison du périphérique IoT concentrateur à l’aide de la [MQTT v3.1.1] [ lnk-mqtt-org] protocole de port 8883 ou v3.1.1 MQTT via le protocole WebSocket sur le port 443. Concentrateur de IoT nécessite toutes les communications de dispositif sécurisé à l’aide de TLS/SSL (donc IoT concentrateur ne prend en charge les connexions non sécurisées sur le port 1883).

## <a name="connecting-to-iot-hub"></a>Connexion au concentrateur de IoT

Un périphérique peut utiliser le protocole MQTT pour vous connecter à un concentrateur IoT en utilisant les bibliothèques dans les [Kits de développement logiciel Microsoft Azure IoT] [ lnk-device-sdks] ou à l’aide de la MQTT de protocole directement.

## <a name="using-the-device-client-sdks"></a>L’utilisation du client de périphérique SDK

[Kits de développement logiciel client d’appareil] [ lnk-device-sdks] qui prennent en charge le protocole MQTT sont disponibles pour Java, Node.js, C, C# et les Python. Le client de périphérique SDK utiliser la chaîne de connexion IoT concentrateur standard pour établir une connexion à un concentrateur IoT. Pour utiliser le protocole MQTT, le paramètre de protocole du client doit être défini à **MQTT**. Par défaut, les kits de développement logiciel se connectent à un concentrateur IoT avec le **CleanSession** le client d’appareil indicateur est défini sur **0** et utiliser **1 de QoS** pour échanger des messages avec le hub IoT.

Lorsqu’un périphérique est connecté à un concentrateur IoT, le client d’appareil SDK fournissent des méthodes qui permettent de l’appareil envoyer des messages et recevoir des messages à partir d’un concentrateur IoT.

Le tableau suivant contient des liens vers des exemples de code pour chaque langue prise en charge et spécifie le paramètre à utiliser pour établir une connexion à un concentrateur IoT utilisant le protocole MQTT.

| Langue                   | Paramètre de protocole        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | Azure-iot-périphérique-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migration d’une application de périphérique à partir de AMQP à MQTT
Si vous utilisez le [client d’appareil SDK][lnk-device-sdks], passant à l’aide de AMQP à MQTT nécessite de modifier le paramètre de protocole lors de l’initialisation du client, comme indiqué ci-dessus.

Dans ce cas, vérifiez les éléments suivants :

* AMQP renvoie des erreurs pour de nombreuses conditions, tandis que MQTT met fin à la connexion. Par conséquent, votre logique de gestion des exceptions peuvent nécessiter certaines modifications.
* MQTT ne gère pas les opérations de *Rejeter* lors de la réception de [messages de C2D][lnk-messaging]. Si votre serveur principal doit recevoir une réponse de l’application de périphérique, envisagez d’utiliser des [méthodes directes][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>L’utilisation directe de protocole MQTT

Si un périphérique ne peut pas utiliser le client de périphérique SDK, il peut toujours se connecter aux points de terminaison périphérique public en utilisant le protocole MQTT. Dans le paquet de **connexion** , le périphérique doit utiliser les valeurs suivantes :

- Pour le champ de **l’identifiant du client** , utilisez l' **ID de périphérique**. 
- Le champ **nom d’utilisateur** , utilisez `{iothubhostname}/{device_id}`, où {iothubhostname} est le nom complet canonique du concentrateur IoT.

    Par exemple, si le nom de votre concentrateur IoT est **contoso.azure-devices.net** et si le nom de votre périphérique est **MyDevice01**, le champ **nom d’utilisateur** complet doit contenir `contoso.azure-devices.net/MyDevice01`.

- Pour le champ de **mot de passe** , utilisez un jeton SAS. Le format du jeton SAS est la même que pour les protocoles HTTP et le AMQP :<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Pour plus d’informations sur la façon de générer des jetons SAS, reportez-vous à la section de périphérique à l’aide d’un concentrateur IoT de [jetons]de sécurité[lnk-sas-tokens].
    
    Lors du test, vous pouvez également utiliser l' [Explorateur de périphérique] [ lnk-device-explorer] outil permet de générer rapidement un jeton d’associations de sécurité que vous pouvez copier et coller dans votre propre code :
    
    1. Accédez à l’onglet de **gestion** dans l’Explorateur de périphérique.
    2. Cliquez sur **Le jeton SAS** (en haut à droite).
    3. Sur **SASTokenForm**, sélectionnez votre périphérique dans **l’ID de périphérique** de la liste déroulante. Définissez votre **vie**.
    4. Cliquez sur **Générer** pour créer votre jeton.
    
    Le jeton d’associations de sécurité qui est généré a cette structure :   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La partie de ce jeton à utiliser comme le champ du **mot de passe** pour vous connecter à l’aide de MQTT est :   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Pour MQTT connecter et déconnecter des paquets, IoT concentrateur émet un événement sur le canal de **Contrôle des opérations** avec des informations supplémentaires qui peuvent vous aider à résoudre les problèmes de connectivité.

### <a name="sending-messages-to-iot-hub"></a>Envoi de messages à IoT concentrateur

Après une connexion réussie, un périphérique peut envoyer des messages à IoT concentrateur à l’aide de `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` en tant que **Nom de la rubrique**. Le `{property_bag}` élément permet à l’appareil envoyer des messages avec des propriétés supplémentaires dans un format codé en url. Par exemple :

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Cette `{property_bag}` élément utilise le même codage que pour les chaînes de requête dans le protocole HTTP.

L’application de client de périphérique peut également utiliser `devices/{device_id}/messages/events/{property_bag}` comme l' **est le nom de la rubrique** pour définir *ne les messages* à transmettre sous la forme d’un message de télémétrie.

IoT concentrateur ne gère pas les messages de QoS 2. Si un client de périphérique publie un message avec **QoS 2**, IoT concentrateur ferme la connexion réseau.
IoT concentrateur ne persiste pas de conserver les messages. Ajoute de concentrateur de IoT si un périphérique envoie un message avec l’indicateur de **conserver** la valeur 1, le **x-opt-conserver les** propriétés d’application pour le message. Dans ce cas, au lieu de conserver le message conserver, IoT concentrateur transmet à l’application back-end.

### <a name="receiving-messages"></a>Réception de messages

Pour recevoir des messages de IoT Hub, un dispositif doit s’abonner à l’aide de `devices/{device_id}/messages/devicebound/#` en tant que **Filtre de rubrique**. Le caractère générique pour plusieurs niveaux **#** dans le filtre de la rubrique est utilisé uniquement pour autoriser ce périphérique à recevoir des propriétés supplémentaires dans le nom de rubrique. IoT concentrateur ne permet pas l’utilisation de la **#** ou **?** caractères génériques pour le filtrage des sous-rubriques. IoT concentrateur n’étant pas un courtier de messagerie pub-sub généraliste, il prend uniquement en charge les noms de la rubrique documentées et les filtres de la rubrique.

Veuillez Notez, que le périphérique ne recevra pas les messages IoT concentrateur, avant qu’il a ouvert son point de terminaison spécifique du périphérique, représenté par le `devices/{device_id}/messages/devicebound/#` filtre de rubrique. Une fois l’abonnement réussie a été établie, l’ordinateur démarre recevoir uniquement les messages du nuage vers le périphérique qui ont été envoyés vers elle après l’heure de l’abonnement. Si le périphérique se connecte avec l’indicateur **CleanSession** est défini sur **0**, l’abonnement sera rendue persistante entre les différentes sessions. Dans ce cas, la prochaine fois qu’il connecte avec le **CleanSession 0** le périphérique recevront les messages en attente qui ont été envoyés qui lui a été déconnecté. Si le périphérique utilise l’indicateur **CleanSession** défini sur **1** , bien qu’il ne recevra pas les messages IoT concentrateur jusqu'à ce qu’il s’abonne à son point de sortie du périphérique.

Concentrateur de IoT remet les messages avec le **Nom de la rubrique** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` s’il y a des propriétés de message. `{property_bag}`contient des paires clé/valeur codées url des propriétés de message. Seules les propriétés d’application et les propriétés système définissable par l’utilisateur (par exemple, **messageId** ou **correlationId**) sont incluses dans le sac de propriétés. Les noms de propriété de système ont le préfixe **$**, propriétés de l’application utilisent le nom de propriété d’origine sans préfixe.

Lorsqu’un client d’appareil s’abonne à une rubrique avec **QoS 2**, IoT concentrateur accorde maximale QoS de niveau 1 dans le paquet **SUBACK** . Après cela, IoT concentrateur doit remettre des messages sur le périphérique à l’aide de QoS 1.

### <a name="additional-considerations"></a>Considérations supplémentaires

En termes de finale, si vous avez besoin personnaliser le comportement du protocole MQTT sur le côté du nuage, vous devez examiner la [passerelle de protocole Azure IoT] [ lnk-azure-protocol-gateway] qui vous permet de déployer une passerelle de protocole personnalisé de hautes performances qui communique directement avec IoT concentrateur. La passerelle de protocole Azure IoT vous permet de personnaliser le protocole de périphérique pour prendre en charge des déploiements de MQTT brownfield ou autres protocoles personnalisés. Cette approche nécessite, toutefois, que vous exécutez et fonctionner une passerelle de protocole personnalisé.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, reportez-vous aux [Remarques sur MQTT prend en charge les] [ lnk-mqtt-devguide] dans le guide du développeur Azure IoT concentrateur.

Pour en savoir plus sur le protocole MQTT, consultez la [documentation de MQTT][lnk-mqtt-docs].

Pour plus d’informations sur la planification de votre déploiement IoT concentrateur, voir :

- [Azure certifié pour le catalogue du périphérique IoT][lnk-devices]
- [Prise en charge des protocoles supplémentaires][lnk-protocols]
- [Comparer avec les concentrateurs d’événement][lnk-compare]
- [Mise à l’échelle, haute disponibilité et reprise après sinistre][lnk-scaling]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
