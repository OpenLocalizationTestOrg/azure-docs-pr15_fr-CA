<properties 
    pageTitle="Vue d’ensemble des exemples de relais de Bus de service | Microsoft Azure"
    description="Classe et présente des exemples de relais de Bus de Service avec des liens vers chacun."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-samples"></a>Exemples de relais de Bus des services

Les exemples de relais de Bus de Service illustrent des fonctionnalités clés de [relais de Bus de Service](https://azure.microsoft.com/services/service-bus/). Cet article classe et décrit les exemples disponibles, avec des liens vers chacun.

>[AZURE.NOTE] Exemples de Bus de service ne sont pas installés avec le Kit de développement logiciel. Pour obtenir des exemples, visitez la [page des exemples Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>En outre, est un ensemble de mises à jour des échantillons de relais de Bus de Service [ici](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (à ce jour, ils ne sont pas décrites dans cet article).  

Pour les échanges d’échantillons, consultez [exemples d’échanges Bus de Service](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Relais de Bus des services

Les exemples suivants montrent comment écrire des applications qui utilisent le service de relais de Bus de Service.

Notez que les exemples de relais nécessitent une chaîne de connexion pour accéder à votre espace de noms du Bus de Service.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Pour obtenir une chaîne de connexion pour le Bus des services Azure

1. Ouvrez une session sur le [portail Azure](http://portal.azure.com).

1. Dans la colonne de gauche, cliquez sur **Service Bus**.

1. Cliquez sur le nom de votre espace de noms dans la liste.

3. Dans l’espace de noms « lame », cliquez sur **stratégies d’accès partagé**.

4. De la lame de **stratégies d’accès partagé** , cliquez sur **RootManageSharedAccessKey**.

6. Copier la chaîne de connexion dans le Presse-papiers.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Pour obtenir une chaîne de connexion pour le Bus de Service pour Windows Server

1. Exécutez l’applet de commande PowerShell suivante :

    ```
    get-sbClientConfiguration
    ```

2. Collez la chaîne de connexion dans le fichier App.config de l’échantillon.

## <a name="service-bus-relay"></a>Relais de Bus des services

Exemples qui illustrent le relais de Bus de Service.

### <a name="getting-started"></a>Mise en route

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Relais de messagerie : Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Montre comment exécuter un service et un client de Service Bus sur Azure. Cet exemple configure le Bus des services par programme. Seules les informations de sécurité et d’environnement sont stockées dans les fichiers de configuration.|1.8|Bus de Service Microsoft Azure|
|[Messagerie relayé l’authentification : Secret partagé](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Montre comment utiliser un nom de l’émetteur et le secret de l’émetteur pour authentifier avec le Bus de Service.|1.8|Bus de Service Microsoft Azure|
|[Relais d’authentification de messagerie : WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Montre comment exposer un service HTTP qui ne nécessite pas d’authentification de l’utilisateur client.|1.8|Bus de Service Microsoft Azure|
|[Relais de messagerie liaisons : WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Montre comment utiliser la liaison de **WebHttpRelayBinding** pour renvoyer des données binaires en utilisant le modèle de programmation Web.|1.8|Bus de Service Microsoft Azure|
|[Relayés liaisons messageries : NetTcp relayé](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Montre comment utiliser la liaison de **NetTcpRelayBinding** .|1.8|Bus de Service Microsoft Azure|

### <a name="exploring-features"></a>Exploration des fonctions

Exemples qui illustrent diverses fonctionnalités de relais de Bus de Service.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Authentification de messagerie relayée : WebToken Simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Montre comment utiliser des données d’identification simple web token se pour authentifier avec le Bus de Service. L’exemple est semblable à l’exemple d’écho, avec quelques modifications. Plus précisément, cet exemple ajoute un comportement dans le ServiceHost (service) et les applications de ChannelFactory (client).|1.8|Bus de Service Microsoft Azure|
|[Messagerie relayé : Équilibrer la charge](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Montre comment utiliser le Bus des services Microsoft Azure pour router les messages vers plusieurs destinataires. Il affiche plusieurs instances d’un simple service de communication avec un client via la liaison de **NetTcpRelayBinding**|1.8|Bus de Service Microsoft Azure|
|[Relayés liens de messagerie : Événement de Net](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Illustre l’utilisation de la liaison de **NetEventRelayBinding** sur le Bus des services Microsoft Azure.|1.8|Bus de Service Microsoft Azure|
|[Relayés liens de messagerie : Session de WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Illustre l’utilisation de la liaison de **WS2007HttpRelayBinding** avec des sessions fiables activées. Il montre également comment spécifier des informations d’identification de Bus de Service dans le fichier de configuration que par programme.|1.8|Bus de Service Microsoft Azure|
|[Les liaisons de messagerie relayés : WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Montre comment utiliser la liaison de **WS2007HttpRelayBinding** avec la sécurité de message pour sécuriser les messages de bout en bout pour tout en nécessitant toujours les clients à s’authentifier avec le Bus de Service.|1.8|Bus de Service Microsoft Azure|
|[Relayé de messagerie : Échange de métadonnées](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Montre comment exposer un point de terminaison de métadonnées qui utilise la liaison de relais. **MetadataExchange** est pris en charge dans les liaisons de relais suivants : **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding**et **WS2007HttpRelayBinding**.|1.8|Bus de Service Microsoft Azure|
|[Les liaisons de messagerie relayés : NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Montre comment configurer le mode de connexion **hybride** qui établit d’abord une connexion relayée et, si possible, passe automatiquement une connexion directe entre un client et un service de prise en charge de la liaison **NetTcpRelayBinding** .|1.8|Bus de Service Microsoft Azure|
|[Liaisons de messagerie relayés : le nom d’utilisateur NetTcp MsgSec](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Montre comment utiliser la liaison de **NetTcpRelayBinding** avec la sécurité de message.|1.8|Bus de Service Microsoft Azure|
|[Liaisons de messagerie relayés : Oneway Net](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Montre comment exposer et de consommer un point de terminaison de service à l’aide de la liaison de **NetOnewayRelayBinding** .|1.8|Bus de Service Microsoft Azure|
|[Les liaisons de messagerie relayés : WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Illustre l’utilisation de la liaison de **WS2007HttpRelayBinding** . Il montre un service simple qui utilise sans options de sécurité et ne nécessite pas de clients à s’authentifier.|1.8|Bus de Service Microsoft Azure|

## <a name="next-steps"></a>Étapes suivantes

Consultez les rubriques suivantes pour une vue d’ensemble conceptuelle du Bus de Service.

- [Vue d’ensemble des relais de Bus des services](service-bus-relay-overview.md)
- [Architecture de Bus de service](../service-bus-messaging/service-bus-architecture.md)
- [Principes de base de Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)