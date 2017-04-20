<properties
    pageTitle="À l’aide du Kit de développement logiciel du périphérique Azure IoT pour C | Microsoft Azure"
    description="En savoir plus sur et commencer à utiliser avec l’exemple de code dans le Kit de développement logiciel de périphérique Azure IoT pour C."
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

# <a name="introducing-the-azure-iot-device-sdk-for-c"></a>Présentation du Kit de développement logiciel du périphérique Azure IoT pour C

Le **dispositif d’Azure IoT SDK** est un ensemble de bibliothèques conçues pour simplifier le processus d’envoi d’événements et de la réception de messages à partir du service **d’Azure IoT concentrateur** . Il existe différentes variations du SDK, ciblant chacun une plate-forme spécifique, mais cet article décrit le **périphérique de IoT d’Azure SDK pour C**.

Le Kit de développement logiciel de périphérique Azure IoT pour C est écrit en ANSI C (C99) afin d’optimiser la portabilité. Cela le rend bien adapté pour fonctionner sur un certain nombre de plates-formes et de périphériques, particulièrement en cas de réduction du disque et l’encombrement mémoire est une priorité.  

Il existe un large éventail de plates-formes, sur lequel le Kit de développement logiciel a été testé (voir la [Azure certifié pour le catalogue du périphérique IoT](https://catalog.azureiotsuite.com/) pour plus de détails). Bien que cet article contient des procédures pas à pas, des exemples de code en cours d’exécution sur la plate-forme Windows, gardez à l’esprit que le code décrit dans cet article est exactement le même sur la gamme de plates-formes prises en charge.

Dans cet article vous seront présentés à l’architecture du périphérique IoT d’Azure SDK pour C. Nous montrerons comment initialiser la bibliothèque de périphérique, envoyer des événements à IoT concentrateur ainsi que recevoir des messages à partir de celui-ci. Les informations contenues dans cet article doivent être suffisant pour commencer à utiliser le Kit de développement, mais il fournit également des liens vers des informations supplémentaires sur les bibliothèques.

## <a name="sdk-architecture"></a>Architecture du Kit de développement logiciel

Vous pouvez trouver le **périphérique de IoT d’Azure SDK pour C** dans le référentiel de [Kits de développement logiciel Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks) GitHub et afficher les détails de l’API dans la [référence de l’API C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

Vous trouverez la version la plus récente des bibliothèques dans la branche **principale** de ce référentiel :

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Ce référentiel contient toute la gamme de dispositif d’Azure IoT kits de développement logiciel. Toutefois, cet article est sur le périphérique IoT d’Azure SDK *pour C* qui se trouve dans le dossier **c** .

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* Vous trouverez l’implémentation de base du Kit de développement de la **iothub\_client** le dossier qui contient l’implémentation de la couche la plus basse API dans le Kit de développement logiciel : la bibliothèque **IoTHubClient** . La bibliothèque **IoTHubClient** contient les API de mise en œuvre de la messagerie brut pour envoyer des messages au Hub de IoT, ainsi que la réception de messages à partir de celui-ci. Lors de l’utilisation de cette bibliothèque, vous êtes responsable de l’implémentation de la sérialisation de message (éventuellement à l’aide de l’exemple de sérialiseur décrite ci-dessous), mais les autres détails de communication avec IoT Hub sont gérées pour vous.
* Le dossier de **sérialiseur** contient des fonctions d’assistance et des exemples illustrant comment sérialiser des données avant de l’envoyer au concentrateur de IoT Azure à l’aide de la bibliothèque cliente. Notez que l’utilisation du sérialiseur n’est pas obligatoire et uniquement fournie dans un but pratique. Si vous utilisez la bibliothèque de **sérialiseur** , vous démarrez en définissant un modèle qui spécifie les événements que vous souhaitez envoyer à IoT Hub, ainsi que les messages que vous souhaitez recevoir à partir de celui-ci. Une fois que le modèle est défini, le SDK fournit une surface d’API qui vous permet de travailler facilement avec des événements et des messages sans avoir à vous soucier des détails de la sérialisation.
La bibliothèque dépend des autres bibliothèques open source qui implémentent le transport à l’aide de plusieurs protocoles (MQTT, AMQP).
* La bibliothèque **IoTHubClient** dépend d’autres bibliothèques open source :
   * La bibliothèque [Azure C partagé utilitaire](https://github.com/Azure/azure-c-shared-utility) qui fournit des fonctionnalités communes pour les tâches de base (tels que chaîne, la manipulation de la liste, e/s, etc....) nécessaire sur plusieurs kits SDK C Azure liés
   * La bibliothèque [uAMQP Azure](https://github.com/Azure/azure-uamqp-c) qui est mise en oeuvre du côté client de AMQP optimisé pour les périphériques de contrainte de ressource.
   * La bibliothèque [uMQTT Azure](https://github.com/Azure/azure-umqtt-c) , qui est une bibliothèque à usage général mettant en oeuvre le protocole MQTT et optimisé pour les périphériques de contrainte de ressource.

Tout cela est plus facile à comprendre à l’exemple de code. Les sections suivantes décrivent quelques exemples d’applications qui sont inclus dans le Kit de développement logiciel. Cela devrait vous donner une idée des différentes capacités des couches de l’architecture du Kit de développement ainsi qu’une introduction au fonctionnement de l’API.

## <a name="before-running-the-samples"></a>Avant d’exécuter les exemples

Avant de pouvoir exécuter les exemples dans le Kit de développement logiciel de périphérique Azure IoT pour C, vous devez créer une instance du service d’abonnement Azure si vous ne pas déjà un et effectuer les 2 tâches :
* Préparation de votre environnement de développement
* obtenir des informations d’identification de périphérique.

Si vous devez créer une instance d’Azure IoT concentrateur sur votre abonnement Azure, suivez les instructions fournies [ici](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

Le [fichier Lisezmoi](https://github.com/Azure/azure-iot-sdks/tree/master/c) inclus avec le SDK fournit des instructions pour la préparation de votre environnement de développement et obtenir des informations d’identification de périphérique.
Les sections suivantes incluent des commentaires supplémentaires sur ces instructions.

### <a name="preparing-your-development-environment"></a>Préparation de votre environnement de développement

Alors que les packages sont fournis pour certaines plates-formes (telles que NuGet pour Windows ou apt_get pour Debian et Ubuntu) et les exemples utilisent ces packages lorsqu’elles sont disponibles, les instructions ci-dessous expliquent comment générer la bibliothèque et les exemples directement le code du formulaire.

Tout d’abord, vous devez obtenir une copie du Kit de développement de GitHub et ensuite créer la source. Vous devez obtenir une copie de la source de la branche **principale** du [référentiel de GitHub](https://github.com/Azure/azure-iot-sdks).

Lorsque vous avez téléchargé une copie de la source, vous devez effectuer les étapes décrites dans l’article de kit de développement logiciel [« Préparation de votre environnement de développement »](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md).


Voici quelques conseils pour vous aider à effectuer la procédure décrite dans le guide de préparation :

-   Lorsque vous installez l’utilitaire **CMake** , choisissez l’option Ajouter **CMake** au chemin d’accès système pour **tous les utilisateurs** (en ajoutant à ainsi que les travaux de **l’utilisateur en cours** ) :

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   Avant d’ouvrir l' **invite de commande de développeur pour VS2015**, installez les outils de ligne de commande Git. Pour installer ces outils, procédez comme suit :

    1. Lancez le programme d’installation de **Visual Studio 2015** (ou avez choisi **Microsoft Visual Studio 2015** à partir du Panneau de configuration **programmes et fonctionnalités** et sélectionnez **Modifier**).
    
    2. Assurez-vous que la fonctionnalité de **Git pour Windows** est sélectionnée dans le programme d’installation, mais vous pouvez également activer l’option **d’Extension de GitHub pour Visual Studio** pour fournir une intégration IDE :

        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

    3. Terminez l’Assistant d’installation pour installer les outils.

    4. Ajoutez le répertoire **bin** d’outils Git à la variable d’environnement système **PATH** . Sous Windows, cela ressemble à ceci :

        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


Lorsque vous avez terminé toutes les étapes décrites dans la page [« Préparation de votre environnement de développement »](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) , vous êtes prêt à compiler les exemples d’applications.

### <a name="obtaining-device-credentials"></a>Obtention d’informations d’identification de périphérique

Maintenant que votre environnement de développement est défini, la prochaine chose à faire est d’obtenir un ensemble d’informations d’identification de périphérique.  Pour un périphérique pouvoir accéder à un concentrateur IoT, vous devez d’abord ajouter le périphérique dans le Registre de périphérique IoT concentrateur. Lorsque vous ajoutez votre périphérique, vous obtiendrez un jeu d’informations d’identification de périphérique dont vous avez besoin pour le périphérique pouvoir se connecter à un concentrateur IoT. Les applications exemple que nous examinerons dans la section suivante s’attendent à ces informations d’identification sous la forme d’une **chaîne de connexion de périphérique**.

Il existe quelques outils fournis dans le référentiel de code source ouvert SDK pour aider à gérer le concentrateur IoT. L’une est une application appelée Explorateur de périphérique, l’autre est un node.js outil CLI de plates-formes croisées iothub-Explorateur de Windows. Plus d’informations sur ces outils [ici](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

Comme nous allons à exécuter les exemples sous Windows de cet article, nous utilisons l’outil Explorateur de périphérique. Mais vous pouvez également utiliser iothub-explorer si vous préférez les outils CLI.

L’outil [Explorateur de périphérique](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) utilise les bibliothèques de service IoT d’Azure pour effectuer diverses tâches sur IoT concentrateur, y compris l’ajout de périphériques. Si vous utilisez l’Explorateur de périphérique pour ajouter un périphérique, vous obtiendrez une chaîne de connexion correspondante. Vous avez besoin de cette chaîne de connexion pour que l’exemple, les applications s’exécutent.

Dans le cas où vous n’êtes pas familiarisé avec le processus, la procédure suivante décrit comment utiliser l’Explorateur de périphérique pour ajouter un périphérique et obtenir une chaîne de connexion de périphérique.

Vous pouvez trouver un programme d’installation de Windows de l’outil Explorateur de périphérique sur la [page mise à jour SDK](https://github.com/Azure/azure-iot-sdks/releases). Mais vous pouvez également exécuter l’outil directement à partir de son code, ouvrez **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** dans **Visual Studio 2015** et la génération de la solution.

Lorsque vous exécutez le programme, vous verrez cette interface :

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Tapez votre **Chaîne de connexion du concentrateur IoT** dans le premier champ, puis cliquez sur **mise à jour**. Cela permet de configurer l’outil afin que celle-ci puisse communiquer avec IoT concentrateur.

Une fois la chaîne de connexion IoT concentrateur est configurée, cliquez sur l’onglet de **gestion** :

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Il s’agit d’où vous permet de gérer les périphériques sont enregistrés dans votre concentrateur IoT.

Vous pouvez créer un périphérique en cliquant sur le bouton **créer** . Une boîte de dialogue s’affiche avec un jeu de clés prédéfinis (principales et secondaires). Il vous suffit est Entrez un **ID de périphérique** puis cliquez sur **créer**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Une fois que le périphérique est créé, la liste des périphériques est mis à jour avec tous les périphériques inscrits, y compris celui que vous venez de créer. Si vous cliquez sur votre nouveau périphérique, vous verrez ce menu :

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Si vous choisissez l’option **Copier la chaîne de connexion pour le périphérique sélectionné** , la chaîne de connexion de votre périphérique est copiée dans le Presse-papiers. Conserver une copie de la chaîne de connexion. Vous en aurez besoin lors de l’exécution des exemples d’applications décrits dans les sections suivantes.

Une fois que vous avez effectué les étapes ci-dessus, vous êtes prêt à commencer l’exécution du code. Les deux exemples ont une constante en haut du fichier source principal qui vous permet d’entrer une chaîne de connexion. Par exemple, la ligne correspondante de la **iothub\_client\_exemple\_amqp** application apparaît comme suit.

```
static const char* connectionString = "[device connection string]";
```

Si vous souhaitez suivre, entrez ici votre chaîne de connexion périphérique, recompilez la solution, et vous serez en mesure d’exécuter l’exemple.

## <a name="iothubclient"></a>IoTHubClient

Dans la **iothub\_client** dossier dans le référentiel d’azure-iot-SDK, il existe un dossier **d’exemples** qui contient une application appelée **iothub\_client\_exemple\_amqp**.

La version Windows de la **iothub\_client\_exemple\_ampq** application inclut la solution Visual Studio suivante :

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Cette solution contient un seul projet. Il est important de noter qu’il y a quatre NuGet packages installés dans cette solution :

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.uamqp

Vous devez toujours le package **Microsoft.Azure.C.SharedUtility** lorsque vous travaillez avec le Kit de développement logiciel. Étant donné que cet exemple s’appuie sur AMQP, vous devez également inclure les packages **Microsoft.Azure.uamqp** et **Microsoft.Azure.IoTHub.AmqpTransport** (il existe des packages équivalents pour MQTT et HTTP). Étant donné que l’exemple utilise la bibliothèque **IoTHubClient** , vous devez également inclure le package **Microsoft.Azure.IoTHub.IoTHubClient** dans votre solution.

Vous pouvez trouver l’implémentation de l’exemple d’application dans le **iothub\_client\_exemple\_amqp.c** fichier de code source.

Cet exemple d’application nous permet de vous guider dans ce qui est nécessaire pour utiliser la bibliothèque **IoTHubClient** .

### <a name="initializing-the-library"></a>Initialisation de la bibliothèque

> [AZURE.NOTE] Avant de commencer à travailler avec les bibliothèques, vous devrez peut-être effectuer une initialisation spécifique de plate-forme. Par exemple, si vous prévoyez d’utiliser des AMQP sous Linux, vous devez initialiser la bibliothèque OpenSSL. Les exemples dans le [référentiel de GitHub](https://github.com/Azure/azure-iot-sdks) appellent la fonction d’utilitaire **platform_init** lorsque le client démarre et appeler la fonction **platform_deinit** avant de quitter. Ces fonctions sont déclarées dans le fichier d’en-tête « platform.h ». Vous devez examiner les définitions de ces fonctions pour votre plate-forme cible dans le [référentiel](https://github.com/Azure/azure-iot-sdks) pour déterminer si vous devez inclure un code d’initialisation de plate-forme de votre client.

Pour commencer à travailler avec les bibliothèques, vous devez allouer tout d’abord un handle de client IoT Hub :

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Notez que nous transmettons une copie de notre chaîne de connexion de périphérique à cette fonction (celui nous obtenus à partir de l’Explorateur de périphérique). Nous avons également désigner le protocole à utiliser. Cet exemple utilise AMQP, mais MQTT et HTTP sont également une option.

Lorsque vous avez une **IOTHUB\_CLIENT\_gérer**, vous pouvez commencer à appeler les API pour envoyer des événements et recevoir des messages de IoT concentrateur. Nous examinerons suivant.

### <a name="sending-events"></a>Envoi d’événements

Envoi d’événements vers le Hub de IoT nécessite que vous effectuez les étapes suivantes :

Tout d’abord, créez un message :

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Ensuite, vous enverrez le message :

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Chaque fois que vous envoyez un message, vous spécifiez une référence à une fonction de rappel qui est appelée lorsque les données sont envoyées :

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Notez l’appel à la **IoTHubMessage\_détruire** fonction lorsque vous avez terminé avec le message. Vous devez effectuer cet appel pour libérer les ressources allouées lorsque vous avez créé le message.

### <a name="receiving-messages"></a>Réception de messages

Réception d’un message est une opération asynchrone. Tout d’abord vous inscrivez un rappel à appeler lorsque le périphérique reçoit un message :

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Le dernier paramètre est un pointeur void vers ce que vous voulez. Dans l’exemple, il s’agit d’un pointeur vers un entier, mais il peut être un pointeur vers une structure de données plus complexe. Cela permet à la fonction de rappel fonctionner sur un état partagé à l’appelant de cette fonction.

Lorsque le périphérique reçoit un message, la fonction de rappel enregistré est appelée :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Notez que vous utilisez la **IoTHubMessage\_GetByteArray** fonction pour récupérer le message, ce qui, dans cet exemple, est une chaîne.

### <a name="uninitializing-the-library"></a>Annuler l’initialisation de la bibliothèque

Lorsque vous avez terminé les événements d’envoi et de réception de messages, vous pouvez annuler l’initialisation de la bibliothèque IoT. Pour ce faire, établissez l’appel de fonction suivant :

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Cela permet de libérer les ressources précédemment allouées par la **IoTHubClient\_CreateFromConnectionString** fonction.

Comme vous pouvez le voir, il est facile d’envoyer des événements et recevoir des messages avec la bibliothèque **IoTHubClient** . La bibliothèque gère les détails de communication avec le IoT Hub, y compris le protocole à utiliser (du point de vue du développeur, il s’agit d’une option de configuration simple).

La bibliothèque **IoTHubClient** offre également un contrôle précis sur la façon de sérialiser les événements que votre périphérique envoie à IoT concentrateur. Dans certains cas, c’est un avantage, mais dans d’autres cas, c’est un détail d’implémentation avec qui vous ne souhaitez pas concernés. Si tel est le cas, vous pouvez envisager d’utiliser la bibliothèque de **sérialiseur** , nous allons décrire dans la section suivante.

## <a name="serializer"></a>Sérialiseur

Conceptuellement, la bibliothèque de **sérialiseur** se trouve sur la bibliothèque de **IoTHubClient** dans le Kit de développement. Il utilise la bibliothèque de **IoTHubClient** pour la communication sous-jacente avec IoT concentrateur, mais il ajoute des fonctions de modélisation que supprimer la charge de traitement avec la sérialisation de message du développeur. Comment fonctionne de cette bibliothèque est préférable illustrée par un exemple.

Dans le **sérialiseur** de dossier dans le référentiel d’azure-iot-SDK est un dossier **d’exemples** qui contient une application appelée **simplesample\_amqp**. La version Windows de cet exemple comprend la solution Visual Studio suivante :

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Comme avec l’exemple précédent, celui-ci contient plusieurs packages NuGet :

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.IoTHub.Serializer
- Microsoft.Azure.uamqp

Nous avons vu la plupart de ces éléments dans l’exemple précédent, mais **Microsoft.Azure.IoTHub.Serializer** est nouveau. Cela est nécessaire lorsque nous utilisons la bibliothèque du **sérialiseur** .

Vous pouvez trouver l’implémentation de l’exemple d’application dans le **simplesample\_amqp.c** fichier.

Les sections suivantes vous guident tout au travers des zones clés de cet exemple.

### <a name="initializing-the-library"></a>Initialisation de la bibliothèque

Pour commencer à travailler avec la bibliothèque de **sérialiseur** , vous devez appeler l’API d’initialisation :

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

L’appel à la **sérialiseur\_init** fonction d’appel unique et est utilisée pour initialiser la bibliothèque sous-jacente. Ensuite, vous appelez le **IoTHubClient\_CreateFromConnectionString** fonction, qui est la même API, comme dans l’exemple **IoTHubClient** . Cet appel définit la chaîne de connexion de périphérique (c’est également dans laquelle vous choisissez le protocole à utiliser). Notez que cet exemple utilise AMQP comme moyen de transport, mais aurait pu utiliser HTTP.

Enfin, appelez la **Création\_modèle\_INSTANCE** fonction. Notez que **WeatherStation** est l’espace de noms du modèle et **ContosoAnemometer** est le nom du modèle. Une fois que l’instance de modèle est créé, vous pouvez l’utiliser pour démarrer l’envoi d’événements et de recevoir des messages. Toutefois, il est important de comprendre quel un modèle est.

### <a name="defining-the-model"></a>Définition du modèle

Un modèle dans la bibliothèque de **sérialiseur** définit les événements que votre périphérique peut envoyer à IoT concentrateur et des messages, appelés « *actions* » dans le langage de modélisation, il peut recevoir. Vous définissez un modèle à l’aide d’un ensemble de macros C comme dans la **simplesample\_amqp** exemple d’application :

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

Les **commencer\_espace de noms** et **fin\_espace de noms** macros les deux prennent de l’espace de noms du modèle en tant qu’argument. Il est prévu que quoi que ce soit entre ces macros est la définition de votre modèle et les structures de données qui utilisent des modèles.

Dans cet exemple, il existe un seul modèle appelé **ContosoAnemometer**. Ce modèle définit deux événements votre périphérique peut envoyer à IoT concentrateur : **ID de périphérique** et de la **vitesse du vent**. Il définit également les trois actions (messages) que votre appareil peut recevoir : **TurnFanOn**, **TurnFanOff**et **SetAirResistance**. Chaque événement possède un type et chaque action possède un nom (et éventuellement un ensemble de paramètres).

Les événements et les actions définies dans le modèle permet de définir une surface d’API que vous pouvez utiliser pour envoyer des événements à IoT concentrateur, ainsi que de répondre aux messages envoyés au périphérique. Il est entendu mieux via un exemple.

### <a name="sending-events"></a>Envoi d’événements

Le modèle définit les événements que vous pouvez envoyer à IoT concentrateur. Dans cet exemple, cela signifie qu’un des deux événements définis à l’aide de la macro **WITH_DATA** . Par exemple, si vous souhaitez envoyer un événement de la **vitesse du vent** à un concentrateur IoT, il existe quelques étapes impliquées dans y arriver. La première consiste à définir les données que vous souhaitez envoyer :

```
myWeather->WindSpeed = 15;
```

Le modèle que nous avons défini plus haut nous permet cela, définissez un membre d’un **struct**. Ensuite, nous sérialiser l’événement que vous souhaitez envoyer :

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Ce code sérialise l’événement dans une mémoire tampon (référencée par la **destination**). Enfin, nous enverrons l’événement au concentrateur IoT avec ce code :

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Il s’agit d’une fonction d’assistance dans l’exemple d’application qui envoie de notre événement sérialisé à IoT concentrateur :

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Ce code est très semblable à ce que nous avons vu dans le **iothub\_client\_exemple\_amqp** application, dans laquelle nous avons créé un message à partir d’un tableau d’octets et ensuite utilisé **IoTHubClient\_SendEventAsync** pour l’envoyer à IoT concentrateur. Après que nous simplement ont libérer le handle de message et nous allouées précédemment de mémoire tampon de données de sérialisation.

La seconde au dernier paramètre de **IoTHubClient\_SendEventAsync** est une référence à une fonction de rappel qui est appelée lorsque les données sont envoyées avec succès. Voici un exemple d’une fonction de rappel :

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Le deuxième paramètre est un pointeur vers le contexte de l’utilisateur ; le même pointeur que nous avons passés à **IoTHubClient\_SendEventAsync**. Dans ce cas le contexte est un compteur simple, mais elle peut être tout ce que vous voulez.

C’est tout pour l’envoi d’événements. La seule chose qui reste à couvrir est de recevoir des messages.

### <a name="receiving-messages"></a>Réception de messages

Réception d’un message fonctionne de la même façon aux façon dont les messages de travail dans la bibliothèque **IoTHubClient** . Tout d’abord, vous enregistrez une fonction de rappel de message :

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Ensuite, vous écrivez la fonction de rappel qui est appelée lorsqu’un message est reçu :

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

Ce code est réutilisable : c’est le même que pour n’importe quelle solution. Cette fonction reçoit le message et qu’il prend en charge le routage à la fonction appropriée par le biais de l’appel à **EXECUTE\_commande**. La fonction appelée à ce stade dépend de la définition des actions dans notre modèle.

Lorsque vous définissez une action dans votre modèle, vous devez implémenter une fonction qui est appelée lorsque le périphérique reçoit le message correspondant. Par exemple, si votre modèle définit cette action :

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

Notez que le nom de la fonction correspond au nom de l’action dans le modèle et que les paramètres de la fonction correspondent aux paramètres spécifiés pour l’action. Le premier paramètre est toujours requis et contient un pointeur vers l’instance de notre modèle.

Lorsque le périphérique reçoit un message qui correspond à cette signature, la fonction correspondante est appelée. Par conséquent, en plus d’avoir à inclure le code à partir de **IoTHubMessage**, recevoir des messages est qu’une question de la définition d’une fonction simple pour chaque action définie dans votre modèle.

### <a name="uninitializing-the-library"></a>Annuler l’initialisation de la bibliothèque

Lorsque vous avez terminé d’envoyer des données et la réception de messages, vous pouvez annuler l’initialisation de la bibliothèque IoT :

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Chacune de ces trois fonctions s’aligne avec les trois fonctions d’initialisation décrites précédemment. Appellent ces API permet de garantir que vous libérez des ressources précédemment alloués.

## <a name="next-steps"></a>Étapes suivantes

Cet article a couvert les principes fondamentaux de l’utilisation des bibliothèques dans le **dispositif d’Azure IoT SDK pour C**. Il vous fourni suffisamment d’informations pour comprendre ce qui est inclus dans le Kit de développement logiciel, de son architecture et comment commencer à utiliser avec les exemples de Windows. Le prochain article continue la description du SDK en expliquant [plus en détail la bibliothèque IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Pour en savoir plus sur le développement pour IoT Hub, consultez les [Kits de développement de concentrateur IoT][lnk-sdks].

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]


[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
