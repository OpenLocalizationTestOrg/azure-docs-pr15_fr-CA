<properties 
    pageTitle="Service de Bus et les Python avec AMQP 1.0 | Microsoft Azure"
    description="À l’aide de Bus des services depuis les Python avec AMQP."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-python-with-amqp-10"></a>À l’aide du Bus de Service à partir de Python avec AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

PROTONS-Python est une liaison de langage Python à PROTONS-C ; Autrement dit, les PROTONS-Python est implémenté en tant que wrapper autour d’un moteur implémenté dans C.

## <a name="download-the-proton-client-library"></a>Télécharger la bibliothèque client de PROTONS

Vous pouvez télécharger PROTONS-C et ses liaisons associées (y compris les Python) à partir de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le téléchargement est sous forme de code source. Pour générer le code, suivez les instructions contenues dans le package téléchargé.

Notez qu’au moment de la rédaction de ce document, la prise en charge SSL de PROTONS-C est uniquement disponible pour les systèmes d’exploitation Linux. Bus des services Azure exigeant l’utilisation de SSL, PROTONS-C (et les liaisons de langue) peuvent uniquement être permettant d’accéder aux Bus de Service de Linux pour l’instant. Pour activer les PROTONS-C avec SSL sous Windows est à cocher c’est le cas en cours consultez-la régulièrement pour des mises à jour.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Utilisation des files d’attente, des rubriques et des abonnements à partir de Python Bus des services

Le code suivant montre comment envoyer et recevoir des messages à partir d’un Bus de Service d’échanges d’entité.

### <a name="send-messages-using-proton-python"></a>Envoyer des messages à l’aide de PROTONS-Python

Le code suivant montre comment envoyer un message à un Bus de Service d’échanges d’entité.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Recevoir des messages à l’aide de PROTONS-Python

Le code suivant montre comment recevoir un message à partir d’un Bus de Service d’échanges d’entité.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Messagerie entre .NET et les Python-PROTONS

### <a name="application-properties"></a>Propriétés de l’application

#### <a name="proton-python-to-service-bus-net-apis"></a>PROTONS-Python pour API .NET de Bus de services

Messages de PROTONS-Python prend en charge les propriétés de l’application des types suivants : **int**, **long**, **float**, **uuid**, **bool**, **string**. Le code Python suivant montre comment définir les propriétés d’un message à l’aide de chacun de ces types de propriété.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Dans l’API .NET de Bus de Service, les propriétés de l’application message sont effectuées dans la collection de **Propriétés** de [BrokeredMessage][]. Le code suivant montre comment lire les propriétés de l’application d’un message reçu d’un client de Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

Le tableau suivant mappe les types de propriété de Python pour les types de propriété .NET.

| Type de propriété de Python | Type de propriété .NET |
|----------------------|--------------------|
| int                  | int                |
| float                | Double             |
| long                 | Int64              |
| UUID                 | GUID               |
| bool                 | bool               |
| chaîne               | chaîne             |

#### <a name="service-bus-net-apis-to-proton-python"></a>Bus de service API .NET à PROTONS-Python

Le type [BrokeredMessage][] prend en charge les propriétés de l’application des types suivants : **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **chaîne**, **Uri**, **DateTime**, **DateTimeOffset**et **TimeSpan**. Le code .NET suivant montre comment définir des propriétés sur un objet [BrokeredMessage][] à l’aide de chacun de ces types de propriété.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Le code Python suivant montre comment lire les propriétés d’un message reçu par un client .NET de Bus de Service d’application.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

Le tableau suivant mappe les types de propriété .NET pour les types de propriété Python.

| Type de propriété .NET | Type de propriété de Python | Notes                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| octets               | int                  | -                                                                                                                                                                     |
| sbyte              | int                  | -                                                                                                                                                                     |
| Char               | Char                 | Classe de PROTONS-Python                                                                                                                                                 |
| court              | int                  | -                                                                                                                                                                     |
| ushort             | int                  | -                                                                                                                                                                     |
| int                | int                  | -                                                                                                                                                                     |
| uint               | int                  | -                                                                                                                                                                     |
| long               | int                  | -                                                                                                                                                                     |
| ulong              | long                 | Classe de PROTONS-Python                                                                                                                                                 |
| float              | float                | -                                                                                                                                                                     |
| Double             | float                | -                                                                                                                                                                     |
| décimal            | Chaîne               | Nombre décimal n’est pas pris en charge avec PROTONS.                                                                                                                     |
| bool               | bool                 | -                                                                                                                                                                     |
| GUID               | UUID                 | Classe de PROTONS-Python                                                                                                                                                 |
| chaîne             | chaîne               | -                                                                                                                                                                     |
| DateTime           | horodatage            | Classe de PROTONS-Python                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | Mappé au type de AMQP de DateTimeOffset.UtcTicks :<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType        | Mappé au type de AMQP de Timespan.Ticks :<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType        | Mappé au type de AMQP de Uri.AbsoluteUri :<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Propriétés standard

Les tableaux suivants présentent le mappage entre les propriétés de message standard de PROTONS-Python et les propriétés de message standard [BrokeredMessage][] .

#### <a name="proton-python-to-service-bus-net-apis"></a>PROTONS-Python pour API .NET de Bus de services

| Python-PROTONS        | Bus de service .NET         | Notes                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| durable              | n/a                      | Bus de service prend uniquement en charge les messages durables.               |
| priorité             | n/a                      | Bus de service prend uniquement en charge une priorité de message unique.      |
| Durée de vie                  | Message.TimeToLive       | Conversion, les Python PROTONS durée de vie est définie en millisecondes. |
| première\_acquéreur      | n/a                      | -                                                           |
| livraison\_nombre      | n/a                      | -                                                           |
| ID                   | Message.MessageID        | -                                                           |
| utilisateur\_id             | n/a                      | -                                                           |
| adresse              | Message.To               | -                                                           |
| Objet              | Message.Label            | -                                                           |
| réponse\_pour            | Message.ReplyTo          | -                                                           |
| corrélation\_id      | Message.CorrelationID    | -                                                           |
| contenu\_type        | Message.ContentType      | -                                                           |
| contenu\_codage    | n/a                      | -                                                           |
| expiration\_temps         | n/a                      | -                                                           |
| Création\_temps       | n/a                      | -                                                           |
| groupe\_id            | Message.SessionId        | -                                                           |
| groupe\_séquence      | n/a                      | -                                                           |
| réponse\_à\_groupe\_id | Message.ReplyToSessionId | -                                                           |
| format               | n/a                      | -                                                           |

| Bus de service .NET        | PROTONS                       | Notes                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.Content\_type        | -                                                           |
| ID de corrélation           | Message.Correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | n/a                          | -                                                           |
| Étiquette                   | Message.Subject              | -                                                           |
| MessageId               | Message.ID                   | -                                                           |
| ReplyTo                 | Message.Reply\_à            | -                                                           |
| ReplyToSessionId        | Message.Reply\_à\_groupe\_id | -                                                           |
| ScheduledEnqueueTimeUtc | n/a                          | -                                                           |
| ID de session               | Message.Group\_id            | -                                                           |
| Propriété TimeToLive              | Message.TTL                  | Conversion, les Python PROTONS durée de vie est définie en millisecondes. |
| À                      | Message.Address              | -                                                           |

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Visitez les liens suivants :

- [Vue d’ensemble du service Bus AMQP]
- [AMQP dans le Bus des services pour Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP dans le Bus des services pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
