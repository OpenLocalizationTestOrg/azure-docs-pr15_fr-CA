<properties 
    pageTitle="L’utilisation de AMQP 1.0 avec l’API de Bus de Service .NET | Microsoft Azure" 
    description="Découvrez comment utiliser Advanced Message Queuing Protodol (AMQP) 1.0 avec l’API de Bus de Service .NET Azure." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>L’utilisation de AMQP 1.0 avec l’API .NET du Bus de Service

Les avancées Message Queuing Protocol (AMQP) 1.0 est un protocole de messagerie au niveau du câble, fiable et efficace que vous pouvez utiliser pour générer les robuste, multi-plateforme, applications de messagerie.

Prise en charge pour 1.0 AMQP dans le Bus de Service signifie que vous pouvez utiliser le queuing et publication/abonnement de courtage de fonctionnalités de messagerie à partir d’une gamme de plates-formes à l’aide d’un protocole binaire efficace. En outre, vous pouvez créer des applications composées les composants créés à l’aide d’un mélange de langues, les infrastructures et les systèmes d’exploitation.

Cet article explique comment utiliser les fonctionnalités de messagerie des Bus de Service demandée (files d’attente et rubriques de publication et d’abonnement) à partir d’applications .NET à l’aide de l’API .NET du Bus de Service. Il existe un [article du Guide](service-bus-java-how-to-use-jms-api-amqp.md) qui explique comment effectuer la même à l’aide de l’API Java Message Service (JMS) standard. Vous pouvez utiliser ces deux guides ensemble pour en savoir plus sur la messagerie multiplateforme à l’aide de AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Mise en route de Bus des services

Cet article suppose que vous disposez déjà d’un espace de noms du Bus de Service contenant une file d’attente nommée « file d’attente 1. » Si vous ne le faites pas, vous pouvez créer l’espace de noms et de la file d’attente à l’aide du [portail Azure][]. Pour plus d’informations sur la création des files d’attente et les espaces de noms de Bus des services, consultez [mise en route de files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Télécharger le Kit de développement logiciel du Bus de Service

Prise en charge AMQP 1.0 est disponible dans le SDK de Bus de Service version 2.1 ou ultérieure. Vous pouvez télécharger les derniers bits de NuGet à [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Applications de code .NET

Par défaut, la bibliothèque cliente .NET de Bus de Service communique avec le service de Bus de Service à l’aide d’un protocole basé sur SOAP dédié. Pour utiliser AMQP 1.0 au lieu de la valeur par défaut le protocole requiert configuration explicite dans la chaîne de connexion de Service Bus comme décrit dans la section suivante. Que cette modification, le code d’application reste essentiellement inchangé lorsque vous utilisez AMQP 1.0.

Dans la version actuelle, il existe quelques fonctionnalités API qui ne sont pas pris en charge lors de l’utilisation de AMQP. Ces fonctionnalités non prises en charge sont répertoriées plus loin dans la section [restrictions et fonctionnalités non pris en charge](#unsupported-features-and-restrictions). Certains paramètres de configuration avancés ont également une signification différente lors de l’utilisation de AMQP. Aucun de ces paramètres sont utilisés dans cet article, mais plus de détails sont disponibles dans la [vue d’ensemble du Service de Bus AMQP](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

### <a name="configure-via-appconfig"></a>Configurer via App.config

Il est recommandé que les applications utilisent le fichier de configuration App.config pour stocker des paramètres. Pour les applications de Bus des services, vous pouvez utiliser App.config pour stocker le Bus de Service **ConnectionString**. Cet exemple d’application utilise également App.config pour stocker le nom de messagerie l’entité qu’il utilise le Bus de Service.

Voici un exemple de fichier App.config :

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Configurer la chaîne de connexion du Bus des services

La valeur du paramètre **Microsoft.ServiceBus.ConnectionString** est la chaîne de connexion de Bus de Service qui est utilisée pour configurer la connexion au Bus de Service. Le format est le suivant :

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Où `[namespace]` et `[SAS key]` sont obtenues à partir du [portail Azure][]. Pour plus d’informations, reportez-vous à la section [comment utiliser les files d’attente de Bus de Service] [].

Lorsque vous utilisez AMQP, la chaîne de connexion est ajoutée à `;TransportType=Amqp`, qui indique la bibliothèque cliente pour établir sa connexion au Bus de Service à l’aide de AMQP 1.0.

### <a name="configure-the-entity-name"></a>Configurer le nom de l’entité

Cet exemple d’application utilise le `EntityName` définition dans la section **appSettings** du fichier App.config pour configurer le nom de la file d’attente avec laquelle l’application échange des messages.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Une simple application .NET à l’aide d’une file d’attente de Bus de Service

L’exemple suivant envoie et reçoit les messages vers et à partir d’une file d’attente de Bus de Service.

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Exécution de l’application

Exécution de l’application génère une sortie du formulaire :

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Entre les plates-formes de messagerie entre JMS et .NET

Cette rubrique ont montré comment envoyer des messages à un Bus de Service à l’aide de .NET et comment recevoir des messages à l’aide de .NET. Toutefois, un des principaux avantages de AMQP 1.0 est qu’il permet aux applications basées sur des composants écrits dans différents langages, avec messages échangés fiable et à toujours une fidélité optimale.

À l’aide de l’exemple d’application .NET décrit ci-dessus et une application Java similaire prises dans le guide de [l’utilisation de l’API avec le Bus de Service & AMQP 1.0 Java Message Service (JMS)](service-bus-java-how-to-use-jms-api-amqp.md), il est possible d’échanger des messages entre .NET et Java. 

Pour plus d’informations sur les détails de messagerie à l’aide du Service de Bus et AMQP 1.0 multiplateforme, consultez la [vue d’ensemble du Service de Bus AMQP 1.0](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>JMS pour .NET

Pour illustrer JMS à .NET messaging :

* Démarrez l’exemple d’application .NET sans argument de ligne de commande.
* Démarrez l’exemple d’application Java avec l’argument de ligne de commande « sendonly ». Dans ce mode, l’application ne recevra pas les messages de la file d’attente, il enverra uniquement.
* Appuyez sur **entrée** plusieurs fois dans la console d’application Java, qui entraîne l’envoi des messages.
* Ces messages sont reçus par l’application .NET.

### <a name="output-from-jms-application"></a>Sortie à partir de l’application JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Sortie à partir de l’application .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET à JMS

Pour illustrer .NET à la messagerie JMS :

* Démarrez l’exemple d’application .NET avec l’argument de ligne de commande « sendonly ». Dans ce mode, l’application ne recevra pas les messages de la file d’attente, il enverra uniquement.
* Démarrez l’exemple d’application Java sans argument de ligne de commande.
* Appuyez sur **entrée** plusieurs fois dans la console d’application .NET, qui entraîne l’envoi des messages.
* Ces messages sont reçus par l’application Java.

#### <a name="output-from-net-application"></a>Sortie à partir de l’application .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Sortie à partir de l’application JMS

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Restrictions et fonctionnalités non prises en charge

Les fonctionnalités suivantes de l’API de Bus de Service .NET ne sont pas actuellement pris en charge lors de l’utilisation de AMQP :

 * Transactions
 * Envoyer par la destination du transfert

Pour plus d’informations, consultez [fonctionnalités non pris en charge, les restrictions et les différences de comportement](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Résumé

Cet article vous a montré comment accéder aux fonctionnalités de messagerie des Bus de Service demandée (files d’attente et rubriques de publication et d’abonnement) à partir de .NET à l’aide de l’API .NET du Bus de Service et AMQP 1.0.

Vous pouvez également utiliser le Service Bus AMQP 1.0 à partir d’autres langages, y compris Java, C, Python et PHP. Les composants créés à l’aide de ces langues peuvent échanger des messages fiable et à toujours une fidélité optimale à l’aide de AMQP 1.0 dans le Bus de Service. Pour plus d’informations, consultez la [vue d’ensemble du Service de Bus AMQP](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu une vue d’ensemble du Service Bus et AMQP avec .NET, consultez les liens suivants pour plus d’informations.

* [Prise en charge de AMQP 1.0 dans le Bus des services Azure](service-bus-amqp-overview.md)
* [L’utilisation de l’API Java Message Service (JMS) avec Bus de Service & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [L’utilisation de files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md)
 
[Azure portal]: https://portal.azure.com
