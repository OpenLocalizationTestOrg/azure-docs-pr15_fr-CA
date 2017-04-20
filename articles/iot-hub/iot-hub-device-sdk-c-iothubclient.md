<properties
    pageTitle="DISPOSITIF IoT Azure SDK pour C - IoTHubClient | Microsoft Azure"
    description="Pour en savoir plus sur l’utilisation de la bibliothèque de IoTHubClient dans le Kit de développement logiciel de périphérique Azure IoT pour C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Dispositif de Microsoft Azure IoT SDK pour C – en savoir plus sur les IoTHubClient

[premier article](iot-hub-device-sdk-c-intro.md) de cette série a introduit le **dispositif de Microsoft Azure IoT SDK pour C**. Cet article a expliqué qu’il existe deux couches architecturales dans le Kit de développement logiciel. À la base est la bibliothèque de **IoTHubClient** qui gère la communication avec le Hub d’IoT directement. Il existe également la bibliothèque de **sérialiseur** qui génère de pour fournir des services de sérialisation. Dans cet article, nous vous fournirons des détails supplémentaires sur la bibliothèque **IoTHubClient** .

L’article précédent décrit comment utiliser la bibliothèque de **IoTHubClient** pour envoyer des événements à IoT Hub et recevoir des messages. Cet article étend cette discussion en expliquant comment gérer plus précisément *lorsque* vous envoyez et recevez des données, présentation de l' **API de niveau inférieur**. Nous expliquerons également comment attacher des propriétés à des événements (et de les récupérer à partir des messages) à l’aide de la propriété gestion des fonctionnalités de la bibliothèque **IoTHubClient** . Enfin, nous vous fournirons une explication supplémentaire de différentes manières pour gérer les messages reçus de IoT concentrateur.

Cet article se termine par couvrant un certain nombre de divers sujets, notamment plus sur les informations d’identification du périphérique et comment modifier le comportement de la **IoTHubClient** via les options de configuration.

Nous utiliserons les exemples du Kit de développement logiciel **IoTHubClient** pour expliquer ces rubriques. Si vous souhaitez suivre l’exemple, consultez la **iothub\_client\_exemple\_http** et **iothub\_client\_exemple\_amqp** les applications qui sont incluses dans le Kit de développement logiciel de périphérique Azure IoT des C. tout ce qui est décrit dans les sections suivantes est présenté dans ces exemples.

Vous pouvez trouver le **périphérique de IoT d’Azure SDK pour C** dans le référentiel de [Kits de développement logiciel Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks) GitHub et afficher les détails de l’API dans la [référence de l’API C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-lower-level-apis"></a>Les API de niveau inférieur

L’article précédent décrit l’opération de base de **IotHubClient** dans le cadre de la **iothub\_client\_exemple\_amqp** application. Par exemple, il a expliqué comment initialiser la bibliothèque à l’aide de ce code.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Il décrit également comment envoyer des événements à l’aide de cet appel de fonction.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

L’article décrit également comment recevoir des messages en enregistrant une fonction de rappel.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

L’article a également montré comment libérer les ressources à l’aide du code suivant.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Il existe cependant des fonctions associé à chacune de ces API :

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_détruire


Ces fonctions tout incluent « LL » dans le nom de l’API. Que, les paramètres de chacune de ces fonctions sont identiques à leurs équivalents non-LL. Toutefois, le comportement de ces fonctions est différent sur un plan important.

Lorsque vous appelez **IoTHubClient\_CreateFromConnectionString**, les bibliothèques de sous-jacent créent un nouveau thread qui s’exécute en arrière-plan. Ce thread envoie les événements et reçoit des messages du Hub de IoT. Aucun thread n’est créé lors de l’utilisation de l’API « LL ». La création de la thread d’arrière-plan est une commodité pour le développeur. Vous n’avez pas à vous soucier d’envoi d’événements explicitement et de recevoir des messages de concentrateur de IoT--il s’effectue automatiquement en arrière-plan. En revanche, API « LL » vous donne un contrôle explicite sur la communication avec le Hub d’IoT, si vous en avez besoin.

Pour mieux comprendre ce point, prenons un exemple :

Lorsque vous appelez **IoTHubClient\_SendEventAsync**, ce que vous faites en fait, l’événement sont mise dans une mémoire tampon. Le thread d’arrière-plan créé lorsque vous appelez **IoTHubClient\_CreateFromConnectionString** en permanence surveille ce tampon et IoT concentrateur envoie toutes les données qu’il contient. Cela se produit en arrière-plan en même temps que le thread principal est exécuter un autre travail.

De même, lorsque vous enregistrez une fonction de rappel pour les messages à l’aide de **IoTHubClient\_SetMessageCallback**, vous êtes commandant le Kit de développement pour que le thread d’arrière-plan appelle la fonction de rappel lorsqu’un message est reçu, indépendamment du thread principal.

L’API « LL » ne pas créer un thread d’arrière-plan. Au lieu de cela, une nouvelle API doit être appelée explicitement envoyer et recevoir des données IoT concentrateur. Cela est illustré dans l’exemple suivant.

La **iothub\_client\_exemple\_http** d’application qui est incluse dans le Kit de développement logiciel présente les API de niveau inférieur. Dans cet exemple, nous envoyer des événements à IoT Hub avec le code suivant :

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Les trois premières lignes de créer le message, et la dernière ligne envoie l’événement. Toutefois, comme mentionné précédemment, « envoi » l’événement indique que les données sont simplement placées dans une mémoire tampon. Rien n’est transmis sur le réseau, lorsque nous appelons **IoTHubClient\_LL\_SendEventAsync**. Puisse effectivement entrée données IoT Hub, vous devez appeler **IoTHubClient\_LL\_DoWork**, comme dans cet exemple :

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ce code (à partir de la **iothub\_client\_exemple\_http** application) appelle à plusieurs reprises **IoTHubClient\_LL\_DoWork**. Chaque fois que **IoTHubClient\_LL\_DoWork** est appelée, elle envoie certains événements de la mémoire tampon à IoT concentrateur et il récupère un message en file d’attente envoyé au périphérique. Ce dernier cas signifie que si nous avons enregistré une fonction de rappel pour les messages, puis le rappel est appelé (en supposant que tous les messages sont la file d’attente). Une telle fonction de rappel peut nous ont inscrit avec le code suivant :

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

La raison qui **IoTHubClient\_LL\_DoWork** est souvent appelée dans une boucle est que chaque fois qu’elle est appelée, elle envoie *certaines* mises en mémoire tamponné les événements IoT concentrateur et récupère *le prochain* message la file d’attente pour le périphérique. Chaque appel n’est pas garanti pour envoyer toutes les mises en mémoire tampon des événements ou pour récupérer tous les messages en file d’attente. Si vous souhaitez envoyer tous les événements dans la mémoire tampon, puis poursuivez sur tout autre traitement que vous pouvez remplacer cette boucle avec le code suivant :

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ce code appelle **IoTHubClient\_LL\_DoWork** jusqu'à ce que tous les événements dans la mémoire tampon ont été envoyés à IoT concentrateur. Notez que cela n’implique pas l’également que tous les messages en file d’attente ont été reçus. Partie de la raison à cela est que vérifier les messages « tout » n’est pas déterministe comme une action. Que se passe-t-il si vous récupérez « tous » les messages, mais ensuite un autre est envoyé au périphérique immédiatement après ? Pour mieux traiter avec un délai d’attente programmée. Par exemple, la fonction de rappel de message peut réinitialiser une minuterie chaque fois il est appelé. Vous pouvez ensuite écrire une logique pour poursuivre le traitement si, par exemple, aucun messages n’ont été reçus pendant les dernières secondes de *X* .

Lorsque vous les événements de la pénétration de molécules terminé et recevoir des messages, veillez à appeler la fonction correspondante pour nettoyer les ressources.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Est en fait qu’un seul ensemble d’API pour envoyer et recevoir des données avec un thread d’arrière-plan et un autre ensemble d’API qui effectue la même opération sans la thread d’arrière-plan. Beaucoup de développeurs préfèrent les API de LL - non, mais les API de niveau inférieur sont utiles lorsque le développeur souhaite que le contrôle explicite sur les transmissions réseau. Par exemple, certains périphériques de collectent des données dans le temps et les événements d’entrée uniquement à intervalles réguliers (par exemple, une fois toutes les heures ou une fois par jour). Les API de bas niveau vous permettent de contrôler explicitement lorsque vous envoyez et recevez des données IoT concentrateur. D’autres préfèrent tout simplement la simplicité qui fournissent de l’API de niveau inférieur. Tout ce qui se produit sur le thread principal plutôt que certains situation de travail en arrière-plan.

Le modèle que vous choisissez, veillez à être cohérent dans lesquelles API que vous utilisez. Si vous démarrez en appelant **IoTHubClient\_LL\_CreateFromConnectionString**, veillez à utiliser uniquement les API de niveau inférieur correspondants pour tout travail de suivi :

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_détruire

-   IoTHubClient\_LL\_DoWork

L’inverse est vrai également. Si vous démarrez avec **IoTHubClient\_CreateFromConnectionString**, puis utilisez les API de LL - non pour tout traitement supplémentaire.

Dans le Kit de développement logiciel de périphérique Azure IoT pour C, consultez le **iothub\_client\_exemple\_http** application pour obtenir un exemple complet de l’API de niveau inférieur. La **iothub\_client\_exemple\_amqp** application peut être référencée pour obtenir un exemple complet de l’API LL.

## <a name="property-handling"></a>Gestion de propriété

Jusqu’ici nous avons décrit l’envoi de données, nous avons été référence dans le corps du message. Par exemple, considérez ce code :

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Cet exemple envoie un message à IoT Hub avec le texte « Hello World ». Toutefois, IoT Hub autorise également des propriétés doit être jointe à chaque message. Les propriétés sont des paires nom/valeur qui peuvent être joint au message. Par exemple, nous pouvons modifier le code précédent pour associer une propriété au message :

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Nous allons commencer par l’appel de **IoTHubMessage\_propriétés** et en lui passant le handle de notre message. Ce que nous obtenons est un **carte\_gérer** référence qui permet de commencer à ajouter des propriétés. Ce dernier est accompli en appelant **carte\_AddOrUpdate**, qui accepte une référence à un mappage de\_poignée, le nom de propriété et la valeur de la propriété. Cette API nous pouvons ajouter autant de propriétés que nous le souhaitons.

Lorsque l’événement est lu à partir de **Concentrateurs de l’événement**, le récepteur peut énumérer les propriétés et récupérer leurs valeurs correspondantes. Par exemple, dans .NET ce devrait faire en accédant à la [collection Properties de l’objet EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

Dans l’exemple précédent, nous sommes y attacher des propriétés à un événement qui nous envoyer à IoT concentrateur. Propriétés peuvent également être associées aux messages reçus IoT concentrateur. Si nous voulons récupérer les propriétés d’un message, nous pouvons utiliser le code suivant dans notre fonction de rappel de message :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

L’appel à **IoTHubMessage\_propriétés** renvoie le **carte\_gérer** référence. Nous passons ensuite cette référence à **carte\_GetInternals** pour obtenir une référence à un tableau de paires nom/valeur (comme un décompte des propriétés). À ce stade, il est très simple d’énumération des propriétés pour obtenir les valeurs que nous voulons.

Vous n’êtes pas obligé d’utiliser des propriétés dans votre application. Toutefois, si vous devez les définir sur les événements ou les récupérer à partir de messages, la bibliothèque **IoTHubClient** facilite.

## <a name="message-handling"></a>Gestion des messages

Comme indiqué précédemment, lorsque des messages arrivent IoT concentrateur la bibliothèque **IoTHubClient** répond en appelant une fonction de rappel enregistrée. Il existe un paramètre de retour de cette fonction qui mérite quelques explications supplémentaires. Voici un extrait de la fonction de rappel dans le **iothub\_client\_exemple\_http** exemple d’application :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Notez que le type de retour est **IOTHUBMESSAGE\_DISPOSITION\_résultat** et, dans ce cas particulier nous retourner **IOTHUBMESSAGE\_accepté**. Il existe des autres valeurs que nous pouvons retourner à partir de cette fonction qui modifient le comporte de la bibliothèque de **IoTHubClient** pour le rappel de message. Voici les options.

-   **IOTHUBMESSAGE\_accepté** – le message a été traité avec succès. La bibliothèque **IoTHubClient** n’appelle pas la fonction de rappel à nouveau avec le même message.

-   **IOTHUBMESSAGE\_rejeté** : le message n’a pas été traité et qu’il n’y a pas le désir de faire à l’avenir. La bibliothèque **IoTHubClient** ne doit pas appeler la fonction de rappel à nouveau avec le même message.

-   **IOTHUBMESSAGE\_abandonné** – le message n’a pas été traité avec succès, mais la bibliothèque **IoTHubClient** doit appeler la fonction de rappel à nouveau avec le même message.

Pour les deux premiers codes de retour, la bibliothèque **IoTHubClient** envoie un message à IoT concentrateur indiquant que le message doit être supprimé de la file d’attente et n’a pas remis à nouveau. L’effet net est identique (le message est supprimé de la file d’attente), mais si le message a été accepté ou rejeté est toujours enregistré.  L’enregistrement de cette distinction est utile pour les expéditeurs du message qui peuvent écouter vos commentaires et déterminer si un périphérique a accepté ou rejeté un message particulier.

Dans ce dernier cas, un message est également envoyé à IoT concentrateur, mais il indique que le message doit être redistribué. En général, vous allez abandonner un message si vous rencontrez une erreur mais que vous souhaitez tenter de traiter le message à nouveau. En revanche, rejet d’un message est approprié lorsque vous rencontrez une erreur irrécupérable (ou si vous décidez simplement que vous ne souhaitez pas traiter le message).

Dans tous les cas, n’oubliez pas des différents codes de retour afin que vous pouvez obtenir le comportement souhaité à partir de la bibliothèque **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Informations d’identification de périphérique auxiliaire

Comme expliqué précédemment, la première chose à faire lorsque vous travaillez avec la bibliothèque **IoTHubClient** est d’obtenir un **IOTHUB\_CLIENT\_gérer** avec un appel semblable à la suivante :

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Les arguments de **IoTHubClient\_CreateFromConnectionString** sont la chaîne de connexion de notre périphérique et d’un paramètre qui indique le protocole nous permet de communiquer avec IoT concentrateur. La chaîne de connexion a un format qui s’affiche comme suit :

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Il existe quatre éléments d’information de cette chaîne : nom IoT Hub, concentrateur de IoT suffixe, ID de périphérique et clé d’accès partagé. Vous procurer le nom de domaine pleinement qualifié (FQDN) d’un concentrateur IoT lorsque vous créez votre instance de concentrateur IoT dans Azure portal, cela vous donne le nom de concentrateur IoT (la première partie du nom de domaine complet) et le suffixe de concentrateur IoT (le reste du nom de domaine complet). Vous obtenez l’ID de périphérique et la touche d’accès rapide partagée lorsque vous vous inscrivez votre appareil avec IoT Hub (comme décrit dans l' [article précédent](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** vous donne un moyen d’initialiser la bibliothèque. Si vous préférez, vous pouvez créer un nouveau **IOTHUB\_CLIENT\_gérer** à l’aide de ces paramètres individuels plutôt que la chaîne de connexion. Ceci est réalisé par le code suivant :

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ceci fait la même chose que **IoTHubClient\_CreateFromConnectionString**.

Cela peut paraître évident, que vous pouvez utiliser **IoTHubClient\_CreateFromConnectionString** au lieu de cette méthode plus détaillée de l’initialisation. N’oubliez pas, cependant, que lorsque vous enregistrez un périphérique dans IoT concentrateur que vous obtenez est un ID de périphérique et de la clé de périphérique (pas une chaîne de connexion). L’outil de kit de développement logiciel **Gestionnaire de périphériques** introduit dans l' [article précédent](iot-hub-device-sdk-c-intro.md) utilise des bibliothèques dans le **service d’Azure IoT SDK** pour créer la chaîne de connexion à partir de l’ID de périphérique, la clé de périphérique et le nom d’hôte de IoT concentrateur. Par conséquent, l’appel de **IoTHubClient\_LL\_créer** peut être préférable, car elle vous évite l’étape de génération d’une chaîne de connexion. Utilisez la méthode pratique.

## <a name="configuration-options"></a>Options de configuration

Jusqu’ici, tout ce qui est décrit sur le fonctionnement de la bibliothèque **IoTHubClient** reflète son comportement par défaut. Toutefois, il y a quelques options que vous pouvez définir pour modifier le fonctionnement de la bibliothèque. Cela s’effectue grâce à la **IoTHubClient\_LL\_SetOption** API. Considérez cet exemple :

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Il existe deux options couramment utilisées :

-   **SetBatching** (bool) : si **true**, alors les données envoyées à IoT concentrateur est envoyé dans des lots. Si la **valeur false**, puis les messages sont envoyés individuellement. La valeur par défaut est **false**. Notez que l’option **SetBatching** s’applique uniquement au protocole HTTP et pas pour les protocoles MQTT ou AMQP.

-   **Délai d’attente** (unsigned int), cette valeur est représentée en millisecondes. Si vous envoyez une demande HTTP ou la réception d’une réponse est plus longue que cette fois, puis la délai de connexion expire.

L’option de traitement par lots est importante. Par défaut, les événements d’ingresses de bibliothèque individuellement (un seul événement est tout ce que vous passez à **IoTHubClient\_LL\_SendEventAsync**). Si l’option de traitement par lots est **true**, la bibliothèque collecte les événements autant que possible à partir de la mémoire tampon (jusqu'à la taille maximale des messages qui accepte IoT concentrateur).  Le lot d’événements est envoyé au concentrateur de IoT dans un seul appel HTTP (les événements individuels sont regroupés dans un tableau JSON). Activer le traitement par lots en général se traduit par des gains de performances importants puisque vous êtes réduction des allers-retours réseau. Il réduit considérablement la bande passante puisque vous envoyez un jeu d’en-têtes HTTP avec un lot d’événements plutôt qu’un jeu d’en-têtes pour chaque événement. Sauf si vous avez une raison spécifique de le faire dans le cas contraire, en général vous allez vouloir activer le traitement par lots.

## <a name="next-steps"></a>Étapes suivantes

Cet article décrit en détail le comportement de la bibliothèque **IoTHubClient** trouvé dans le **dispositif d’Azure IoT SDK pour C**. Avec ces informations, vous devez disposer d’une bonne compréhension des fonctionnalités de la bibliothèque **IoTHubClient** . L' [article suivant](iot-hub-device-sdk-c-serializer.md) fournit des détails similaires sur la bibliothèque de **sérialiseur** .

Pour en savoir plus sur le développement pour IoT Hub, consultez les [Kits de développement de concentrateur IoT][lnk-sdks].

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
