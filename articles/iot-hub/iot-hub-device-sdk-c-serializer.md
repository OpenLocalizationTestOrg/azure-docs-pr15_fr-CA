<properties
    pageTitle="DISPOSITIF IoT Azure SDK pour C - sérialiseur | Microsoft Azure"
    description="Pour en savoir plus sur l’utilisation de la bibliothèque de sérialiseur dans le Kit de développement logiciel de périphérique Azure IoT pour C"
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

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-serializer"></a>Dispositif de Microsoft Azure IoT SDK pour C – plus de sérialiseur

[premier article](iot-hub-device-sdk-c-intro.md) de cette série a introduit le **dispositif d’Azure IoT SDK pour C**. Le prochain article fournie une description plus détaillée de [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Cet article complète couverture du Kit de développement en fournissant une description plus détaillée du composant restant : la bibliothèque de **sérialiseur** .

L’article Introduction décrit comment utiliser la bibliothèque de **sérialiseur** pour envoyer des événements et recevoir des messages IoT concentrateur. Dans cet article, nous étendre cette discussion en fournissant une explication plus détaillée comment modéliser vos données avec le langage macro de **sérialiseur** . L’article également comprend plus de détails sur la façon dont la bibliothèque sérialise des messages (et dans certains cas, comment vous pouvez contrôler le comportement de sérialisation). Nous allons également décrire certains que vous pouvez modifier les paramètres qui déterminent la taille des modèles que vous créez.

Enfin, l’article revisite certains sujets abordés dans les articles précédents, tels que le message et de la gestion de propriété. Comme nous allons savoir, ces fonctionnalités fonctionnent de la même manière, à l’aide de la bibliothèque de **sérialiseur** comme ils le font avec la bibliothèque **IoTHubClient** .

Tout ce qui est décrit dans cet article est basé sur les exemples du Kit de développement logiciel **sérialiseur** . Si vous souhaitez suivre l’exemple, consultez la **simplesample\_amqp** et **simplesample\_http** applications incluses dans le Kit de développement logiciel de périphérique Azure IoT pour C.

Vous pouvez trouver le **périphérique de IoT d’Azure SDK pour C** dans le référentiel de [Kits de développement logiciel Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks) GitHub et afficher les détails de l’API dans la [référence de l’API C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-modeling-language"></a>Le langage de modélisation

L' [article de présentation](iot-hub-device-sdk-c-intro.md) de cette série a introduit le **dispositif d’Azure IoT SDK pour C** par l’intermédiaire de l’exemple fourni dans le langage de modélisation du **simplesample\_amqp** application :

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Comme vous pouvez le voir, le langage de modélisation est basé sur les macros de C. Commencez toujours votre définition avec **BEGIN\_espace de noms** et se terminent toujours par **fin\_espace de noms**. Il est courant de nommer l’espace de noms pour votre société ou, comme dans cet exemple, le projet sur lequel vous travaillez.

Ce qui se passe à l’intérieur de l’espace de noms sont des définitions de modèle. Dans ce cas, il existe un seul modèle pour un anémomètre. Une fois de plus, le modèle peut être n’importe quel nom, mais en général il est nommé pour le périphérique ou le type de données que vous souhaitez échanger avec IoT concentrateur.  

Les modèles contiennent une définition des événements que vous pouvez ingress IoT concentrateur (les *données*) ainsi que les messages que vous pouvez recevoir IoT concentrateur ( *actions*). Comme vous pouvez le voir dans l’exemple, les événements ont un type et un nom ; les actions ont un nom et des paramètres facultatifs (chacun avec un type).

N’est pas illustrée dans cet exemple sont les types de données supplémentaires qui sont prises en charge par le Kit de développement logiciel. Nous traiterons suivant.

> [AZURE.NOTE] IoT concentrateur fait référence aux données de qu'un périphérique lui envoie en tant qu' *événements*, tandis que le langage de modélisation désigne sous forme de *données* (définies à l’aide de **WITH_DATA**). De la même manière, IoT concentrateur désigne les données envoyées aux périphériques en tant que *messages*, tandis que le langage de modélisation le désigne comme des *actions* (défini à l’aide de **WITH_ACTION**). N’oubliez pas que ces conditions peuvent être utilisées indifféremment dans cet article.

### <a name="supported-data-types"></a>Types de données pris en charge

Les types de données suivants sont pris en charge dans les modèles créés avec la bibliothèque de **sérialiseur** :

| Type de                    | Description                            |
|-------------------------|----------------------------------------|
| Double                  | double précision à virgule flottante |
| int                     | entier 32 bits                         |
| float                   | nombre à virgule flottante simple précision |
| long                    | entier long                           |
| int8\_t                 | entier de 8 bits                          |
| int16\_t                | entier 16 bits                         |
| Int32\_t                | entier 32 bits                         |
| Int64\_t                | entier 64 bits                         |
| bool                    | valeur booléenne                                |
| ASCII\_char\_ptr        | Chaîne ASCII                           |
| EDM\_DATE\_temps\_de contrepartie | décalage de date                       |
| EDM\_GUID               | GUID                                   |
| EDM\_binaire             | fichier binaire                                 |
| DÉCLARER\_STRUCT         | type de données complexe                      |

Commençons par le dernier type de données. Le **DECLARE\_STRUCT** permet de définir les types de données complexes, qui sont des groupements d’autres types primitifs. Ces regroupements nous permettent de définir un modèle qui ressemble à ceci :

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Notre modèle contient un événement de données unique de type **TestType**. **TestType** est un type complexe qui inclut plusieurs membres, qui, collectivement, démontrent les types primitifs pris en charge par le **sérialiseur** de langage de modélisation.

Avec un modèle de ce type, nous pouvons écrire le code pour envoyer des données au concentrateur IoT qui s’affiche comme suit :

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

En fait, nous assignant une valeur à chaque membre de la structure de **Test** et et appel de **SendAsync** pour envoyer l’événement de données de **Test** dans le nuage. **SendAsync** est une fonction d’assistance qui envoie un événement de données unique à IoT Hub :

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Cette fonction sérialise l’événement donné de données et les envoie au concentrateur IoT à l’aide de **IoTHubClient\_SendEventAsync**. Il s’agit du même code que celui décrit dans les articles précédents (**SendAsync** encapsule la logique dans une fonction pratique).

Une autre fonction d’assistance utilisée dans le code précédent est **GetDateTimeOffset**. Cette fonction transforme le moment donné une valeur de type **EDM\_DATE\_temps\_OFFSET**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Si vous exécutez ce code, le message suivant est envoyé au concentrateur de IoT :

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Notez que la sérialisation JSON, qui est le format généré par la bibliothèque du **sérialiseur** . Notez également que chaque membre de l’objet JSON sérialisé correspond aux membres de la **TestType** que nous avons défini dans notre modèle. Les valeurs correspondent également exactement celles qui sont utilisées dans le code. Toutefois, notez que les données binaires sont codées en base64 : « AQID » est le base64 codage de {0 x 01, 0 x 02, 0 x 03}.

Cet exemple présente l’avantage de l’utilisation de la bibliothèque de **sérialiseur** : il nous permet d’envoyer de JSON vers le nuage, sans avoir à gérer explicitement la sérialisation dans notre application. Il nous soucier consiste à définir les valeurs des événements de données dans notre modèle, puis en appelant une API simple pour envoyer les événements vers le nuage.

Avec ces informations, nous pouvons définir des modèles qui incluent la plage de types de données pris en charge, y compris des types complexes (nous pourrions même inclure des types complexes dans d’autres types complexes). Cependant, il sérialisée JSON généré par l’exemple ci-dessus fait apparaître un point important. *Comment* nous envoyer des données à l’aide de la bibliothèque de **sérialiseur** détermine exactement comment le JSON est formé. Ce point particulier est ce que nous allons ensuite.

## <a name="more-about-serialization"></a>En savoir plus sur la sérialisation

La section précédente présente un exemple de la sortie générée par la bibliothèque du **sérialiseur** . Dans cette section, nous expliquerons comment la bibliothèque sérialise les données et comment vous pouvez contrôler ce comportement à l’aide de l’API de sérialisation.

Pour le faire passer de la discussion sur la sérialisation, nous travaillons en collaboration avec un nouveau modèle basé sur un thermostat. Tout d’abord, nous allons fournir des informations générales sur le scénario que nous essayons d’adresse.

Nous allons modéliser un thermostat qui mesure la température et l’humidité. Chaque élément de données est va être envoyé au concentrateur de IoT différemment. Par défaut, les ingresses de thermostat un événement de température toutes les 2 minutes ; un événement d’humidité est ingressed une fois toutes les 15 minutes. Lorsque l’événement est ingressed, elle doit inclure un horodatage qui indique le temps que la température correspondante ou l’humidité a été mesurée.

Compte tenu de ce scénario, nous illustrerons deux façons de modéliser les données, et de l’effet que la modélisation a sur la sortie sérialisée.

### <a name="model-1"></a>Modèle 1

Voici la première version d’un modèle qui prend en charge le scénario précédent :

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Notez que le modèle contient deux événements de données : **température** et **l’humidité**. Contrairement aux exemples précédents, le type de chaque événement est une structure définie à l’aide de **DECLARE\_structure**. **TemperatureEvent** inclut une mesure de la température et un cachet de date ; **HumidityEvent** contient une mesure de l’humidité et d’un horodatage. Ce modèle nous offre un moyen naturel pour modéliser les données pour le scénario décrit ci-dessus. Lorsque nous envoyer un événement vers le nuage, nous enverrons soit une température/timestamp ou une paire d’humidité/timestamp.

Nous pouvons envoyer un événement de température dans le nuage à l’aide du code suivant :

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Nous allons utiliser des valeurs codées en dur de température et d’humidité dans l’exemple de code, mais imaginez que nous récupérons en fait ces valeurs en échantillonnant les capteurs correspondants sur le thermostat.

Le code ci-dessus utilise l’application d’assistance **GetDateTimeOffset** qui a été introduite précédemment. Pour des raisons qui deviendront désactivez ultérieures, ce code sépare explicitement la tâche de sérialisation et d’envoi de l’événement. Le code ci-dessus sérialise l’événement de température dans une mémoire tampon. Ensuite, **sendMessage** est une fonction d’assistance (inclus dans **simplesample\_amqp**) qui envoie l’événement IoT concentrateur :

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Ce code est un sous-ensemble de l’application d’assistance de **SendAsync** décrit dans la section précédente, afin que nous n’allons pas dessus à nouveau ici.

Lorsque nous exécutons le code précédent pour envoyer l’événement de température, cette forme sérialisée de l’événement est envoyée au concentrateur de IoT :

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Nous allons envoyer une température qui est de type **TemperatureEvent** et struct contient un membre de la **température** et de **durée** . Cela se répercute directement dans les données sérialisées.

De la même façon, nous pouvons envoyer un événement de l’humidité avec ce code :

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

La forme sérialisée qui est envoyée au concentrateur de IoT apparaît comme suit :

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Il s’agit encore une fois, comme prévu.

Avec ce modèle, vous pouvez l’imaginer comment d’autres événements peuvent facilement être ajoutés. Vous définissez à l’aide de structures plus **DECLARE\_STRUCT**et inclure l’événement correspondant dans le modèle à l’aide de **WITH\_données**.

Maintenant, nous allons modifier le modèle de sorte qu’il inclut les mêmes données, mais avec une structure différente.

### <a name="model-2"></a>Modèle 2

Prenez en compte ce modèle alternatif à la précédente :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Dans ce cas, nous avons éliminé la **DECLARE\_STRUCT** macros et définissez simplement les éléments de données à partir de notre scénario à l’aide de types simples à partir du langage de modélisation.

Uniquement pour l’instant, nous allons ignorer l’événement **horaire** . Avec cela mis à part, voici le code pour la **température**d’entrée :

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ce code envoie l’événement sérialisé suivant à IoT concentrateur :

```
{"Temperature":75}
```

Et le code pour l’envoi de l’événement d’humidité apparaît comme suit :

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ce code envoie au IoT Hub :

```
{"Humidity":45}
```

Jusqu’il n’y a toujours aucuns surprises. Maintenant nous allons maintenant changer la façon dont nous utilisons la macro SERIALIZE.

La macro **SERIALIZE** peut prendre plusieurs événements de données en tant qu’arguments. Cela permet de sérialiser l’événement de **température** et **d’humidité** entre eux et de les envoyer à IoT concentrateur dans un seul appel :

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Vous pouvez deviner que le résultat de ce code est que les événements de deux fichiers de données sont envoyés à IoT concentrateur :

[

{« Température » : 75},

{« Humidité » : 45}

]

En d’autres termes, vous pourriez penser que ce code est le même que l’envoi de **température** et **humidité** séparément. Il est juste une commodité pour passer les deux événements pour **SÉRIALISER** dans le même appel. Toutefois, qui n’est pas le cas. Au lieu de cela, le code ci-dessus envoie cet événement de données unique à IoT concentrateur :

{« Température » : 75, « humidité » : 45}

Cela peut sembler étrange car notre modèle définit la **température** et **l’humidité** comme deux événements *distincts* :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Plus précisément, nous n’a pas modèle ces événements où la **température** et **l’humidité** sont dans la même structure :

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Si nous avons utilisé ce modèle, il est plus facile à comprendre la **température** et **l’humidité** seraient envoyée dans le même message sérialisé. Cependant il peut-être pas clairement pourquoi il fonctionne de cette manière, lorsque vous passez les deux événements de données pour **SÉRIALISER** à l’aide du modèle 2.

Ce comportement est plus facile à comprendre si vous connaissez les hypothèses de la bibliothèque de **sérialiseur** . Sens de ce revenons à notre modèle :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pensez à ce modèle en termes d’orienté objet. Dans ce cas, nous allons modélisation un périphérique physique (un thermostat) et ce dispositif comprend des attributs tels que la **température** et **l’humidité**.

Nous pouvons envoyer l’état entier de notre modèle avec le code suivant :

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

En supposant que les valeurs de température, humidité et l’heure sont définies, nous verrions un événement envoyé à IoT concentrateur :

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Vous pouvez être uniquement amené à envoyer *certaines* propriétés du modèle vers le nuage (cela est particulièrement vrai si votre modèle contient un grand nombre d’événements de données). Il est utile envoyer uniquement un sous-ensemble des événements de données, comme dans l’exemple précédent :

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Cette action génère exactement le même événement sérialisé comme si nous avions défini un **TemperatureEvent** avec un membre de **température** et de **temps** , comme nous l’avons fait avec le modèle 1. Dans ce cas, nous avons pu générer exactement le même événement sérialisé à l’aide d’un modèle différent (modèle 2) étant donné que nous avons appelé **SERIALIZE** d’une manière différente.

Le point important est que, si vous passez plusieurs événements de données de **SERIALIZE,** puis il suppose que chaque événement est une propriété d’un objet JSON.

La meilleure approche dépend de vous et de la manière dont vous envisagez votre modèle. Si vous envoyez des « événements » dans le nuage et chaque événement contient un ensemble de propriétés défini, la première approche est très utile. Dans ce cas, vous utiliseriez **DECLARE\_structure** pour définir la structure de chaque événement et les inclure dans votre modèle avec la **WITH\_données** macro. Alors vous envoyez chaque événement comme nous l’avons fait dans le premier exemple ci-dessus. Dans cette approche vous transmettez uniquement un événement de données unique à un **sérialiseur**.

Si vous pensez sur votre modèle de manière orientée objet, puis la deuxième approche peut convenir à vous. Dans ce cas, les éléments définis à l’aide de **WITH\_données** sont « propriétés » de votre objet. Vous passez le sous-ensemble d’événements de **sérialisation** que vous le souhaitez, en fonction de la quantité de votre état de l’objet » « à envoyer vers le nuage.

Nether approche est bonne ou de mauvaise. Seulement connaître le fonctionnement de la bibliothèque de **sérialiseur** et choisissez l’approche de modélisation qui correspond le mieux à vos besoins.

## <a name="message-handling"></a>Gestion des messages

Jusqu'à présent cet article a traité uniquement d’envoi d’événements vers le Hub de IoT et n’a pas adressé la réception de messages. La raison en est que ce que nous devons savoir à propos de la réception de messages a principalement été couverte dans un [précédent article](iot-hub-device-sdk-c-intro.md). Rappeler que l’article de traiter les messages en enregistrant une fonction de rappel de message :

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Vous écrivez ensuite la fonction de rappel qui est appelée lorsqu’un message est reçu :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Cette implémentation de **IoTHubMessage** appelle la fonction spécifique pour chaque action dans votre modèle. Par exemple, si votre modèle définit cette action :

```
WITH_ACTION(SetAirResistance, int, Position)
```

Vous devez définir une fonction avec cette signature :

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** est ensuite appelée lorsque ce message est envoyé à votre appareil.

Ce que nous n’avons pas expliqué est encore l’aspect de la version sérialisée de message. En d’autres termes, si vous souhaitez envoyer un message **SetAirResistance** à votre appareil, quoi qui ressemble ?

Si vous envoyez un message à un périphérique, vous le faites via le service Azure IoT SDK. Vous devez toujours savoir quelle chaîne à envoyer appeler une action particulière. Le format standard pour l’envoi d’un message s’affiche comme suit :

```
{"Name" : "", "Parameters" : "" }
```

Vous envoyez un objet JSON sérialisé avec deux propriétés : **nom** est le nom de l’action (message) et **paramètres** contient les paramètres de cette action.

Par exemple, pour appeler **SetAirResistance** , vous pouvez envoyer ce message à un périphérique :

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Le nom de l’action doit correspondre exactement à une action définie dans votre modèle. Les noms de paramètres doivent correspondre également. Notez également le respect de la casse. **Nom** et les **paramètres** sont toujours en majuscules. Veillez à respecter la casse de votre nom d’action et les paramètres de votre modèle. Dans cet exemple, le nom de l’action est « SetAirResistance » et non « setairresistance ».

Cette section décrit tout ce dont vous avez besoin de savoir lorsque des messages d’événements de l’envoi et la réception avec la bibliothèque de **sérialiseur** . Avant de poursuivre, nous couvrirons certains que vous pouvez configurer les paramètres qui contrôlent la taille de votre modèle est.

## <a name="macro-configuration"></a>Configuration des macros

Si vous utilisez la bibliothèque de **sérialiseur** une partie importante du Kit de développement de connaître se trouve dans la bibliothèque d’azure-c-partagé-utilitaires.
Si vous avez cloné le référentiel Azure-iot-SDK à partir de GitHub à l’aide de l’option--récursive, vous trouverez cette bibliothèque d’utilitaires partagées ici :

```
.\\c\\azure-c-shared-utility
```

Si vous n’avez pas cloné la bibliothèque, vous pouvez le trouver [ici](https://github.com/Azure/azure-c-shared-utility).

Dans la bibliothèque d’utilitaires partagées, vous trouverez le dossier suivant :

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ce dossier contient une solution Visual Studio nommée **macro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

Le programme de cette solution génère le **macro\_utils.h** fichier. Il existe une macro par défaut\_fichier utils.h inclus dans le Kit de développement logiciel. Cette solution vous permet de modifier certains paramètres et puis recréer le fichier d’en-tête en fonction de ces paramètres.

Les deux paramètres clés concernés avec sont **nArithmetic** et **nMacroParameters** qui sont définis dans ces deux lignes de la macro\_utils.tt :

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Ces valeurs sont les paramètres par défaut fournis avec le Kit de développement logiciel. Chaque paramètre a la signification suivante :

-   nMacroParameters – contrôle le nombre de paramètres que vous pouvez avoir dans un DECLARE\_définition de modèle de macro.

-   nArithmetic – contrôle le nombre total de membres autorisés dans un modèle.

Ces paramètres sont importants parce car ils contrôlent la taille votre modèle peut être. Par exemple, considérez la définition de ce modèle :

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Comme mentionné précédemment, **DECLARE\_modèle** est juste une macro C. Le nom du modèle et de la **WITH\_données** instruction (encore une autre macro) est des paramètres de **DECLARE\_modèle**. **nMacroParameters** définit le nombre de paramètres peut être inclus dans **DECLARE\_modèle**. En effet, définit combien déclarations d’événement et d’action données peut avoir. En tant que tel, à la limite par défaut de 124 cela signifie que vous pouvez définir un modèle avec une combinaison d’environ 60 actions et événements de données. Si vous tentez de dépasser cette limite, vous recevrez des erreurs du compilateur se présenter comme suit :

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

Le paramètre **nArithmetic** n’est plus sur le fonctionnement interne de la langue de la macro de votre application.  Il contrôle le nombre total de membres que vous avez dans votre modèle, y compris les macros **DECLARE_STRUCT** . Si vous commencez à voir des erreurs de compilation telles que celle-ci, puis vous devez essayer d’augmenter **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Si vous souhaitez modifier ces paramètres, modifiez les valeurs dans la macro\_utils.tt de fichiers, recompilez la macro\_utils\_h\_generator.sln la solution et exécutez le programme compilé. Si vous le faites, une nouvelle macro\_utils.h fichier est généré et placé dans le. \\commune\\répertoire de inc.

Pour utiliser la nouvelle version de la macro\_utils.h, supprimer le package NuGet de **sérialiseur** de votre solution et à sa place, inclure le projet Visual Studio de **sérialiseur** . Cela permet à votre code compiler par rapport au code source de la bibliothèque de sérialiseur. Cela inclut la mise à jour macro\_utils.h. Si vous souhaitez effectuer cette opération pour **simplesample\_amqp**, commencez par supprimer le package NuGet pour la bibliothèque de sérialiseur à partir de la solution :

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Puis ajouter ce projet à votre solution Visual Studio :

> . \\c\\sérialiseur\\build\\windows\\serializer.vcxproj

Lorsque vous avez terminé, votre solution doit se présenter comme suit :

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Maintenant lorsque vous compilez votre solution, la macro de mise à jour\_utils.h est inclus dans votre fichier binaire.

Notez que l’augmenter ces valeurs suffisamment élevées peut dépasser les limites du compilateur. À ce stade, le **nMacroParameters** est le paramètre principal avec lequel concernés. La spécification de C99 Spécifie qu’un minimum de 127 paramètres sont autorisés dans une définition de macro. Le compilateur Microsoft suit la spécification exactement (et s’il a une limite de 127), afin que vous ne pourrez pas augmenter **nMacroParameters** au-delà de la valeur par défaut. Autres compilateurs permet aussi de faire (par exemple, le compilateur GNU prend en charge une limite plus élevée).

Jusqu'à présent, nous avons traité tout ce que vous devez savoir sur la façon d’écrire du code avec la bibliothèque de **sérialiseur** . Avant de conclure, nous allons revoir certaines rubriques dans les articles précédents que vous vous demandez peut-être sur.

## <a name="the-lower-level-apis"></a>Les API de niveau inférieur

L’exemple d’application sur lequel cet article se concentre est **simplesample\_amqp**. Cet exemple utilise le niveau supérieur (la non-« LL ») API pour envoyer des événements et recevoir des messages. Si vous utilisez ces API, un thread d’arrière-plan s’exécute et qui prend en charge à la fois envoyer des événements et recevoir des messages. Toutefois, vous pouvez utiliser les API (II) de niveau inférieur pour éliminer ce thread d’arrière-plan et de prendre le contrôle explicit lorsque vous envoyez des événements ou recevez des messages à partir du cloud.

Comme décrit dans un [article précédent](iot-hub-device-sdk-c-iothubclient.md), il existe un ensemble de fonctions qui se compose de l’API de niveau supérieur :

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_détruire

Ces API est illustrés dans **simplesample\_amqp**.

Il correspond également un ensemble d’API de niveau inférieur.

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_détruire

Notez que les API de niveau inférieur fonctionnent exactement de la même manière, comme décrit dans les articles précédents. Vous pouvez utiliser le premier jeu d’API si vous souhaitez un thread d’arrière-plan pour gérer les événements d’envoi et de réception de messages. Vous utilisez le deuxième jeu d’API si vous souhaitez contrôler lorsque vous envoyez et recevez des données à partir de concentrateur de IoT explicite. Un ensemble d’API travail aussi bien avec la bibliothèque de **sérialiseur** .

Pour obtenir un exemple d’utilisation de l’API de niveau inférieur avec la bibliothèque de **sérialiseur** , consultez la **simplesample\_http** application.

## <a name="additional-topics"></a>Rubriques supplémentaires

Quelques autres sujets intéressant de mentionner à nouveau appartiennent à gérer, à l’aide des options de configuration et les informations d’identification de l’autre périphérique. Il s’agit de toutes les rubriques traitées dans un [article précédent](iot-hub-device-sdk-c-iothubclient.md). Le point essentiel est que toutes ces fonctionnalités fonctionnent de la même façon avec la bibliothèque de **sérialiseur** qu’avec la bibliothèque **IoTHubClient** . Par exemple, si vous souhaitez attacher des propriétés à un événement à partir de votre modèle, vous utilisez **IoTHubMessage\_propriétés** et **carte**\_**AddorUpdate**, la même façon que décrit précédemment :

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Si l’événement a été généré à partir de la bibliothèque de **sérialiseur** ou créé manuellement à l’aide de la bibliothèque **IoTHubClient** n’a pas d’importance.

Pour les informations d’identification de périphérique auxiliaire, à l’aide de **IoTHubClient\_LL\_créer** fonctionne aussi bien en tant que **IoTHubClient\_CreateFromConnectionString** pour allouer un **IOTHUB\_CLIENT\_gérer**.

Enfin, si vous utilisez la bibliothèque de **sérialiseur** , vous pouvez définir les options de configuration avec **IoTHubClient\_LL\_SetOption** comme vous l’avez fait lors de l’utilisation de la bibliothèque **IoTHubClient** .

Une fonctionnalité qui est unique dans la bibliothèque de **sérialiseur** sont l’initialisation API. Avant de commencer à travailler avec la bibliothèque, vous devez appeler **sérialiseur\_init**:

```
serializer_init(NULL);
```

Pour cela, juste avant d’appeler **IoTHubClient\_CreateFromConnectionString**.

De même, lorsque vous avez terminé utilisation de la bibliothèque, le dernier appel, vous devez faire est de **sérialiseur\_deinit**:

```
serializer_deinit();
```

Dans le cas contraire, toutes les autres fonctionnalités répertoriées ci-dessus fonctionnent de la même façon dans la bibliothèque de **sérialiseur** comme ils le font dans la bibliothèque **IoTHubClient** . Pour plus d’informations sur ces rubriques, consultez l' [article précédent](iot-hub-device-sdk-c-iothubclient.md) de cette série.

## <a name="next-steps"></a>Étapes suivantes

Cet article décrit en détail les particularités de la bibliothèque de **sérialiseur** contenue dans le **dispositif d’Azure IoT SDK pour C**. Les informations fournies que vous devez avoir une bonne compréhension de l’utilisation des modèles pour envoyer des événements et recevoir des messages de IoT concentrateur.

Ceci termine également la série en trois parties sur le développement d’applications avec le **dispositif d’Azure IoT SDK pour C**. Il doit être suffisamment d’informations pour non seulement vous aider à démarrer, mais vous donne une connaissance approfondie du fonctionnement de l’API. Pour plus d’informations, il existe quelques exemples dans le SDK non traitée ici. Dans le cas contraire, la [documentation du SDK](https://github.com/Azure/azure-iot-sdks) est une ressource utile pour plus d’informations.


Pour en savoir plus sur le développement pour IoT Hub, consultez les [Kits de développement de concentrateur IoT][lnk-sdks].

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
