> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Cet article fournit une description détaillée de l' [exemple de code Hello World] [ lnk-helloworld-sample] pour illustrer les composants fondamentaux du [SDK de passerelle Azure IoT] [ lnk-gateway-sdk] architecture. L’exemple utilise le Kit de développement logiciel IoT concentrateur passerelle pour créer une passerelle simple qui enregistre un message « hello world » dans un fichier toutes les cinq secondes.

Cette procédure pas à pas couvre :

- **Concepts**: une vue d’ensemble conceptuelle des composants qui composent toute passerelle que vous créez avec le SDK de passerelle IoT.  
- **Hello World, exemple d’architecture**: décrit comment les concepts s’appliquent à l’exemple Hello World et comment les composants s’imbriquent.
- **Comment générer l’exemple**: les étapes requises pour générer l’exemple.
- **Comment faire pour exécuter l’exemple**: les étapes requises pour exécuter l’exemple. 
- **Voici une sortie type**: un exemple de la sortie d’attendre lorsque vous exécutez l’exemple.
- **Les extraits de code**: une collection d’extraits de code pour montrer comment l’exemple Hello World implémente les composants clés de passerelle.

## <a name="azure-iot-gateway-sdk-concepts"></a>Concepts de IoT passerelle SDK Azure

Avant d’examiner l’exemple de code ou créer votre propre passerelle de champ à l’aide du SDK de passerelle IoT, vous devez comprendre les concepts fondamentaux qui sous-tendent l’architecture du Kit de développement.

### <a name="modules"></a>Modules

Vous générez une passerelle avec le SDK de passerelle IoT Azure en créant et assemblage de *modules*. Modules permet d’échanger des données entre les *messages* . Un module reçoit un message, exécute une action sur celui-ci, éventuellement le transforme en un nouveau message et puis le publie des autres modules à traiter. Certains modules peuvent produire uniquement les nouveaux messages et jamais traiter les messages entrants. Une chaîne de modules crée un pipeline de traitement de données à chaque module effectue une transformation sur les données à un moment donné de ce pipeline.

![Une chaîne de modules de passerelle intégrée avec le SDK de passerelle IoT Azure][1]
 
Le SDK contient les éléments suivants :

- Des modules qui exécutent les fonctions courantes de passerelle.
- Les interfaces, un développeur peut utiliser pour écrire des modules personnalisés.
- L’infrastructure nécessaire pour déployer et exécuter une série de modules.

Le SDK fournit une couche d’abstraction qui vous permet de construire des passerelles pour s’exécuter sur différents systèmes d’exploitation et plates-formes.

![Couche d’abstraction IoT passerelle SDK Azure][2]

### <a name="messages"></a>Messages

Bien que penser des modules en passant des messages est un moyen pratique pour conceptualiser le fonctionnement d’une passerelle, il ne reflète pas précisément ce qui se passe. Les modules utilisent un courtier pour communiquer entre eux, ils publier des messages sur le broker (bus, pubsub ou tout autre modèle de messagerie) et laisser le service Broker pour router le message vers les modules qui lui sont connectés.

Un module utilise la fonction **Broker_Publish** pour publier un message de service broker. Le broker remet les messages à un module en appelant une fonction de rappel. Un message se compose d’un ensemble de propriétés de clé/valeur et contenu passé sous la forme d’un bloc de mémoire.

![Le rôle de service Broker dans le SDK de passerelle IoT Azure][3]

### <a name="message-routing-and-filtering"></a>Routage des messages et le filtrage

Il existe deux façons d’acheminement des messages vers les modules appropriés. Un ensemble de liens peut être passé au broker afin que le broker connaît la source et le récepteur pour chaque module, ou module permet de filtrer les propriétés du message. Un module doit agir uniquement un message si le message est destiné. Les liens et le filtrage des messages est effectivement un pipeline de message.

## <a name="hello-world-sample-architecture"></a>Hello World exemple d’architecture

L’exemple Hello World illustre les concepts décrits dans la section précédente. L’exemple Hello World implémente une passerelle ayant un pipeline composé de deux modules :

-   Le module *Bonjour* crée un message toutes les cinq secondes et le passe au module journal.
-   Le module de *journalisation* écrit les messages qu’il reçoit dans un fichier.

![Architecture de l’exemple Hello World avec le SDK de passerelle IoT Azure][4]

Comme décrit dans la section précédente, le module de Hello World ne transmet pas les messages directement au module journal toutes les cinq secondes. Au lieu de cela, elle publie un message vers le courtier toutes les cinq secondes.

Le module de l’enregistreur d’événements reçoit le message de service broker et agit, écrit le contenu du message dans un fichier.

Le module journal consomme seulement des messages à partir du courtier, il publie jamais les nouveaux messages à service broker.

![Comment service broker route les messages entre les modules dans le SDK de passerelle IoT Azure][5]

La figure ci-dessus illustre l’architecture de l’exemple Hello World et les chemins d’accès relatifs pour les fichiers sources qui implémentent différentes parties de l’échantillon dans le [référentiel][lnk-gateway-sdk]. Explorer le code vous-même, ou utiliser les extraits de code ci-dessous comme guide.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk