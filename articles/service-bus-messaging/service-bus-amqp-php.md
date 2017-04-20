<properties 
    pageTitle="Service Bus et PHP avec AMQP 1.0 | Microsoft Azure"
    description="À l’aide de Bus de Service à partir de PHP avec AMQP."
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

# <a name="using-service-bus-from-php-with-amqp-10"></a>À l’aide du Bus de Service à partir de PHP avec AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

PROTONS-PHP est une liaison de langage PHP à PROTONS-C ; Autrement dit, les PROTONS-PHP est implémenté en tant que wrapper autour d’un moteur implémenté dans C.

## <a name="downloading-the-proton-client-library"></a>Téléchargement de la bibliothèque cliente de PROTONS

Vous pouvez télécharger PROTONS-C et ses liaisons associées (notamment PHP) à partir de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le téléchargement est sous forme de code source. Pour générer le code, suivez les instructions contenues dans le package téléchargé.

> [AZURE.IMPORTANT] Au moment de la rédaction de ce document, la prise en charge SSL de PROTONS-C est uniquement disponible pour les systèmes d’exploitation Linux. Bus des services Azure exigeant l’utilisation de SSL, PROTONS-C (et les liaisons de langue) peuvent uniquement être permettant d’accéder aux Bus de Service de Linux pour l’instant. Pour activer les PROTONS-C avec SSL sous Windows est en cours d’exécution, consultez donc consultez-la régulièrement pour des mises à jour.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>Utilisation des files d’attente Bus des services, des rubriques et des abonnements à partir de PHP

Le code suivant montre comment envoyer et recevoir des messages à partir d’un Bus de Service d’échanges d’entité.

### <a name="sending-messages-using-proton-php"></a>Envoi de messages à l’aide de PROTONS-PHP

Le code suivant montre comment envoyer un message à un Bus de Service d’échanges d’entité.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Réception de messages à l’aide de PROTONS-PHP

Le code suivant montre comment recevoir un message à partir d’un Bus de Service d’échanges d’entité.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>Messagerie entre .NET et PHP-PROTONS

### <a name="application-properties"></a>Propriétés de l’application

#### <a name="protonphp-to-service-bus-net-apis"></a>ProtonPHP de l’API .NET de Bus de services

Messages de PROTONS-PHP prend en charge les propriétés de l’application des types suivants : **entier**, **double**, **booléen**, **chaîne**et **objet**. Le code PHP suivant montre comment définir les propriétés d’un message à l’aide de chacun de ces types de propriété.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Dans les API .NET de Bus de Service, les propriétés de l’application message sont effectuées dans la collection de **Propriétés** de [BrokeredMessage][]. Le code suivant montre comment lire les propriétés de l’application d’un message reçu d’un client PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

Le tableau suivant mappe les types de propriété PHP vers les types de propriété .NET.

| Type de propriété PHP | Type de propriété .NET |
|-------------------|--------------------|
| nombre entier           | int                |
| Double            | Double             |
| valeur booléenne           | bool               |
| chaîne            | chaîne             |
| objet            | Objet             |

#### <a name="service-bus-net-apis-to-php"></a>Bus de service API .NET pour PHP

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
```

Le code PHP suivant montre comment lire les propriétés d’un message reçu par un client .NET de Bus de Service d’application.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

Le tableau suivant mappe les types de propriété .NET pour les types de propriétés PHP.

| Type de propriété .NET | Type de propriété PHP | Notes                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| octets               | nombre entier           | -                                                                                                                                                                     |
| sbyte              | nombre entier           | -                                                                                                                                                                     |
| Char               | Char              | Classe de PROTONS-PHP                                                                                                                                                    |
| court              | nombre entier           | -                                                                                                                                                                     |
| ushort             | nombre entier           | -                                                                                                                                                                     |
| int                | nombre entier           | -                                                                                                                                                                     |
| uint               | Nombre entier           | -                                                                                                                                                                     |
| long               | nombre entier           | -                                                                                                                                                                     |
| ulong              | nombre entier           | -                                                                                                                                                                     |
| float              | Double            | -                                                                                                                                                                     |
| Double             | Double            | -                                                                                                                                                                     |
| décimal            | chaîne            | Nombre décimal n’est pas pris en charge avec PROTONS.                                                                                                                     |
| bool               | valeur booléenne           | -                                                                                                                                                                     |
| GUID               | UUID              | Classe de PROTONS-PHP                                                                                                                                                    |
| chaîne             | chaîne            | -                                                                                                                                                                     |
| DateTime           | nombre entier           | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | Mappé au type de AMQP de DateTimeOffset.UtcTicks :<type name="datetime-offset" class=restricted source="long"><descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan           | DescribedType     | Mappé au type de AMQP de Timespan.Ticks :<type name="timespan" class=restricted source="long"><descriptor name="com.microsoft:timespan" /></type>                        |
| URI                | DescribedType     | Mappé au type de AMQP de Uri.AbsoluteUri :<type name="uri" class=restricted source="string"><descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>Propriétés standard

Les tableaux suivants présentent le mappage entre les propriétés de message standard de PROTONS-PHP et les propriétés de message standard [BrokeredMessage][] .

| PHP-PROTONS           | Bus de service .NET         | Notes                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| Durable              | n/a                      | Bus de service prend uniquement en charge les messages durables.          |
| Priorité             | n/a                      | Bus de service prend uniquement en charge une priorité de message unique. |
| Durée de vie                  | Message.TimeToLive       | Conversion, durée de vie de PROTONS-PHP est définie en millisecondes.   |
| première\_acquéreur      | -                          | -                                                          |
| livraison\_nombre      | -                          | -                                                          |
| ID                   | Message.Id               | -                                                          |
| utilisateur\_id             | -                          | -                                                          |
| Adresse              | Message.To               | -                                                          |
| Objet              | Message.Label            | -                                                          |
| réponse\_pour            | Message.ReplyTo          | -                                                          |
| corrélation\_id      | Message.CorrelationId    | -                                                          |
| contenu\_type        | Message.ContentType      | -                                                          |
| contenu\_codage    | n/a                      | -                                                          |
| expiration\_temps         | Message.ExpiresAtUTC     | -                                                          |
| Création\_temps       | n/a                      | -                                                          |
| groupe\_id            | Message.SessionId        | -                                                          |
| groupe\_séquence      | -                          | -                                                          |
| réponse\_à\_groupe\_id | Message.ReplyToSessionId | -                                                          |
| Format               | n/a                      | -

#### <a name="service-bus-net-apis-to-proton-php"></a>Bus de service API .NET à PROTONS-PHP

| Bus de service .NET        | PHP-PROTONS                                             | Notes                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | Message -\>contenu\_type                                | -                                                        |
| ID de corrélation           | Message -\>alias\_id                              | -                                                        |
| EnqueuedTimeUtc         | Message -\>annotations [x-opt-en file d’attente-heure]             | -                                                        |
| Étiquette                   | Message -\>objet                                      | -                                                        |
| MessageId               | Message -\>id                                           | -                                                        |
| ReplyTo                 | Message -\>réponse\_pour                                    | -                                                        |
| ReplyToSessionId        | Message -\>réponse\_à\_groupe\_id                         | -                                                        |
| ScheduledEnqueueTimeUtc | Message -\>annotations [« x-opt-planifié-file d’attente-heure »] | -                                                        |
| ID de session               | Message -\>groupe\_id                                    | -                                                        |
| Propriété TimeToLive              | Message -\>ttl                                          | Conversion, durée de vie de PROTONS-PHP est définie en millisecondes. |
| À                      | Message -\>adresse                                      | -                                                        |

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Visitez les liens suivants :

- [Vue d’ensemble du service Bus AMQP]
- [AMQP dans le Bus des services pour Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP dans le Bus des services pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
