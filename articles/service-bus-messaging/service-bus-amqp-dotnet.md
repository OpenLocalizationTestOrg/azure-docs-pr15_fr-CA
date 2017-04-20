<properties 
    pageTitle="Service de Bus avec .NET et AMQP 1.0 | Microsoft Azure"
    description="À l’aide du Bus de Service à partir de .NET avec AMQP"
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>À l’aide du Bus de Service à partir de .NET avec AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Télécharger le Kit de développement logiciel du Bus de Service

Prise en charge AMQP 1.0 est disponible dans le SDK de Bus de Service version 2.1 ou ultérieure. Vous vous assurent de que disposer de la dernière version en téléchargeant les bits du Bus de Service à partir de [NuGet][].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configuration des applications .NET à utiliser AMQP 1.0

Par défaut, la bibliothèque cliente .NET de Bus de Service communique avec le service de Bus de Service à l’aide d’un protocole basé sur SOAP dédié. Pour utiliser AMQP 1.0 au lieu de la valeur par défaut le protocole requiert configuration explicite sur la chaîne de connexion de Bus des services, comme décrit dans la section suivante. Que cette modification, le code d’application reste inchangé lorsque vous utilisez AMQP 1.0.

Dans la version actuelle, il existe quelques fonctionnalités API qui ne sont pas pris en charge lors de l’utilisation de AMQP. Ces fonctionnalités non prises en charge sont répertoriées plus loin dans la section [fonctionnalités non pris en charge, les restrictions et les différences de comportement](#unsupported-features-restrictions-and-behavioral-differences). Certains paramètres de configuration avancés ont également une signification différente lors de l’utilisation de AMQP.

### <a name="configuration-using-appconfig"></a>Configuration à l’aide de App.config

Il est recommandé pour les applications d’utiliser le fichier de configuration App.config pour stocker des paramètres. Pour les applications de Bus des services, vous pouvez utiliser App.config pour stocker les paramètres de la valeur de Bus de Service **ConnectionString** . Voici un exemple de fichier App.config :

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

La valeur de la `Microsoft.ServiceBus.ConnectionString` paramètre est la chaîne de connexion de Bus de Service qui est utilisée pour configurer la connexion au Bus de Service. Le format est le suivant :

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Où `[namespace]` et `SharedAccessKey` sont obtenues à partir du [portail Azure][]. Pour plus d’informations, consultez [mise en route de files d’attente de Bus de Service][].

Lorsque vous utilisez AMQP, ajoutez la chaîne de connexion avec `;TransportType=Amqp`. Cette notation informe la bibliothèque cliente pour établir sa connexion au Bus de Service à l’aide de AMQP 1.0.

## <a name="message-serialization"></a>Sérialisation de messages

Lorsque vous utilisez le protocole par défaut, le comportement de sérialisation par défaut de la bibliothèque cliente .NET est d’utiliser le type [DataContractSerializer][] pour sérialiser une instance de [BrokeredMessage][] pour le transport entre la bibliothèque cliente et le service de Bus de Service. Lorsque vous utilisez le mode de transport AMQP, la bibliothèque cliente utilise le système de type AMQP pour la sérialisation du [message traité][BrokeredMessage] dans un message AMQP. Cette sérialisation permet le message reçu et interprété par une application de réception qui est potentiellement exécuté sur une plate-forme différente, par exemple, une application Java qui utilise l’API JMS pour accéder aux Bus de Service.

Lorsque vous construisez une instance de [BrokeredMessage][] , vous pouvez fournir un objet .NET en tant que paramètre au constructeur comme le corps du message. Pour les objets qui peuvent être mappés à des types primitifs AMQP, le corps est sérialisé en types de données AMQP. Si l’objet ne peut pas être mappé directement à un type primitif de AMQP ; Autrement dit, un type personnalisé défini par l’application, puis l’objet est sérialisé à l’aide du [DataContractSerializer][]et les octets sérialisés sont envoyés dans un message de données AMQP.

Pour faciliter l’interopérabilité avec des clients non .NET, utilisez uniquement des types .NET qui peuvent être sérialisés directement dans les types AMQP pour le corps du message. Le tableau suivant détaille les types et le mappage correspondant au système de type AMQP.

| Type d’objet .NET corps          | Type AMQP mappé                          | Type de Section de corps AMQP                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool                           | valeur booléenne                                   | Valeur AMQP                                                                                                                                                |
| octets                           | ubyte                                     | Valeur AMQP                                                                                                                                                |
| ushort                         | ushort                                    | Valeur AMQP                                                                                                                                                |
| uint                           | uint                                      | Valeur AMQP                                                                                                                                                |
| ulong                          | ulong                                     | Valeur AMQP                                                                                                                                                |
| sbyte                          | octets                                      | Valeur AMQP                                                                                                                                                |
| court                          | court                                     | Valeur AMQP                                                                                                                                                |
| int                            | int                                       | Valeur AMQP                                                                                                                                                |
| long                           | long                                      | Valeur AMQP                                                                                                                                                |
| float                          | float                                     | Valeur AMQP                                                                                                                                                |
| Double                         | Double                                    | Valeur AMQP                                                                                                                                                |
| décimal                        | decimal128                                | Valeur AMQP                                                                                                                                                |
| Char                           | Char                                      | Valeur AMQP                                                                                                                                                |
| DateTime                       | horodatage                                 | Valeur AMQP                                                                                                                                                |
| GUID                           | UUID                                      | Valeur AMQP                                                                                                                                                |
| Byte]                         | fichier binaire                                    | Valeur AMQP                                                                                                                                                |
| chaîne                         | chaîne                                    | Valeur AMQP                                                                                                                                                |
| System.Collections.IList       | liste                                      | Valeur AMQP : les éléments contenus dans la collection peuvent être uniquement ceux qui sont définis dans cette table.                                                             |
| System.Array                   | tableau                                     | Valeur AMQP : les éléments contenus dans la collection peuvent être uniquement ceux qui sont définis dans cette table.                                                             |
| System.Collections.IDictionary | carte                                       | Valeur AMQP : les éléments contenus dans la collection peuvent être uniquement ceux qui sont définis dans cette table. Remarque : seules les clés de type chaîne sont pris en charge.                        |
| URI                            | Décrit la chaîne (voir le tableau suivant) | Valeur AMQP                                                                                                                                                |
| DateTimeOffset                 | Description longue (voir le tableau suivant)   | Valeur AMQP                                                                                                                                                |
| TimeSpan                       | Description longue (consultez la rubrique suivante)         | Valeur AMQP                                                                                                                                                |
| Flux de données                         | fichier binaire                                    | Données de AMQP (peut-être plusieurs). Les sections de données contiennent les octets bruts de lire à partir de l’objet de flux.                                                           |
| Autre objet                   | fichier binaire                                    | Données de AMQP (peut-être plusieurs). Contient le fichier sérialisé binaire de l’objet qui utilise le DataContractSerializer ou un sérialiseur fournie par l’application. |

| Type .NET      | Mappé AMQP décrit le Type                                                                                              | Notes                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Les fonctionnalités non prises en charge, les restrictions et les différences de comportement

Les fonctionnalités suivantes de l’API .NET du Bus de Service ne sont pas actuellement pris en charge lors de l’utilisation de AMQP :

-   Transactions

-   Envoyer par la destination du transfert

Il existe également quelques petites différences dans le comportement de l’API .NET du Bus de Service lors de l’utilisation de AMQP, par rapport au protocole par défaut :

-   La propriété [OperationTimeout][] est ignorée.

-   `MessageReceiver.Receive(TimeSpan.Zero)`est implémenté en tant que `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

-   À la fin de messages par les jetons de verrou n’est possible par les récepteurs de messages qui initialement a reçu les messages.

## <a name="controlling-amqp-protocol-settings"></a>Contrôle des paramètres de protocole AMQP

Les API .NET exposent plusieurs paramètres pour contrôler le comportement du protocole AMQP :

-   **MessageReceiver.PrefetchCount**: contrôle du crédit initial appliqué à une liaison. La valeur par défaut est 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: la taille de trame maximale AMQP proposée au cours de la négociation de la connexion de contrôles d’ouverture. La valeur par défaut est 65 536 octets.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: si les transferts sont batchable, cette valeur détermine le délai maximal pour l’envoi des dispositions. Hérité par des expéditeurs/destinataires par défaut. Émetteur/récepteur individuel peuvent substituer la valeur par défaut, ce qui est de 20 millisecondes.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: contrôle si les connexions de AMQP sont établies sur une connexion SSL. La valeur par défaut est **true**.

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Visitez les liens suivants :

- [Vue d’ensemble du service Bus AMQP]
- [Prise en charge de AMQP 1.0 pour les files d’attente de Bus de Service partitionnée et rubriques]
- [AMQP dans le Bus des services pour Windows Server]

  [Mise en route de files d’attente de Bus de Service]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
[Prise en charge de AMQP 1.0 pour les files d’attente de Bus de Service partitionnée et rubriques]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP dans le Bus des services pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
