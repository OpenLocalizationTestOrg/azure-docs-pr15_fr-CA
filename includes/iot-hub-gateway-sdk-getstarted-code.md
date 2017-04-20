## <a name="typical-output"></a>Voici une sortie type

Voici un exemple de la sortie écrite dans le fichier journal par l’exemple Hello World. Saut de ligne et les tabulations ont été ajoutés pour une meilleure lisibilité :

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Extraits de code

Cette section décrit certains éléments clés du code dans l’exemple Hello World.

### <a name="gateway-creation"></a>Création de la passerelle

Le développeur doit écrire le *processus de passerelle*. Ce programme crée l’infrastructure interne (service broker) charge les modules et configure tous les éléments fonctionnent correctement. Le SDK fournit la fonction **Gateway_Create_From_JSON** pour vous permettre d’amorçage d’une passerelle à partir d’un fichier JSON. Pour utiliser la fonction **Gateway_Create_From_JSON** , que vous devez lui transmettre le chemin d’accès à un fichier JSON qui spécifie les modules à charger. 

Vous trouverez le code pour le processus de passerelle dans l’exemple Hello World dans le [main.c] [ lnk-main-c] fichier. Pour une meilleure lisibilité, l’extrait de code ci-dessous montre une version abrégée du code du processus de passerelle. Ce programme crée une passerelle et attend ensuite que l’utilisateur appuie sur la touche **entrée** avant elle démonte la passerelle. 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

Le fichier de paramètres de JSON contient une liste de modules à charger. Chaque module doit spécifier a:

- **nom_module**: un nom unique pour le module.
- **module_path**: le chemin d’accès à la bibliothèque qui contient le module. Pour Linux est un fichier .au, sous Windows, c’est un fichier .dll.
- **arguments**: le module a besoin des informations de configuration.

Le fichier JSON contient également les liens entre les modules qui seront passés au service broker. Un lien a deux propriétés :
- **source**: un nom de module à partir de la `modules` section, ou «\*».
- **récepteur**: un nom de module à partir de la `modules` section

Chaque liaison définit un itinéraire du message et la direction. Messages du module `source` doivent être remis dans le module `sink`. Le `source` peut être défini sur «\*», indiquant que les messages à partir de n’importe quel module seront reçus par `sink`.

L’exemple suivant montre le fichier de paramètres de JSON permet de configurer l’exemple Hello World sur Linux. Tous les messages générés par le module `hello_world` sera utilisée par le module `logger`. Si un module requiert qu'un argument dépend de la conception du module. Dans cet exemple, le module de journalisation prend un argument qui est le chemin d’accès au fichier de sortie et le module de Hello World ne prend pas d’arguments :

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>Publication de messages du module Hello World

Vous pouvez trouver le code utilisé par le module de « hello world » pour publier des messages dans le ['hello_world.c'] [ lnk-helloworld-c] fichier. L’extrait de code ci-dessous montre une version modifiée avec des commentaires supplémentaires et supprimé pour une meilleure lisibilité de code de gestion des erreurs :

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>Traitement des messages de module Hello World

Le module de Hello World ne doit jamais traiter les messages qui publient des autres modules de l’intermédiaire financier. Ainsi, la mise en oeuvre du rappel de message dans le module de Hello World une fonction absence d’opération.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Message du module journal de publication et de traitement

Le module de journalisation reçoit des messages à partir du courtier et les écrit dans un fichier. Il publie jamais les messages. Par conséquent, le code du module journal n’appelle jamais la fonction **Broker_Publish** .

La fonction **Logger_Recieve** dans les [logger.c] [ lnk-logger-c] fichier est le rappel le broker appelle pour remettre des messages au module journal. L’extrait de code ci-dessous montre une version modifiée avec des commentaires supplémentaires et supprimé pour une meilleure lisibilité de code de gestion des erreurs :

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon d’utiliser le SDK de passerelle IoT, consultez les rubriques suivantes :

- [IoT passerelle SDK – envoyer des messages de périphérique-nuage avec un périphérique simulé à l’aide de Linux][lnk-gateway-simulated].
- [Passerelle de IoT Azure SDK] [ lnk-gateway-sdk] sur GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md