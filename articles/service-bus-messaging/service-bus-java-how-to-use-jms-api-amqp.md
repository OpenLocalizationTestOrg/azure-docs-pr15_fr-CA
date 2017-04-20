<properties 
    pageTitle="L’utilisation de AMQP 1.0 avec l’API Java du Bus de Service | Microsoft Azure" 
    description="Comment utiliser Java Message Service (JMS) avec Bus des services Azure et Advanced Message Queuing Protodol (AMQP) 1.0." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>L’utilisation de l’API Java Message Service (JMS) avec Bus de Service et de AMQP 1.0

Les avancées Message Queuing Protocol (AMQP) 1.0 est un protocole de messagerie au niveau du câble, fiable et efficace que vous pouvez utiliser pour créer des applications de messagerie robustes, multiplates-formes.

Prise en charge pour 1.0 AMQP dans le Bus de Service signifie que vous pouvez utiliser le queuing et publication/abonnement de courtage de fonctionnalités de messagerie à partir d’une gamme de plates-formes à l’aide d’un protocole binaire efficace. En outre, vous pouvez créer des applications composées les composants créés à l’aide d’un mélange de langues, les infrastructures et les systèmes d’exploitation.

Cet article explique comment utiliser le Bus de Service d’échanges de fonctionnalités (files d’attente et rubriques de publication et d’abonnement) à partir des applications Java à l’aide de la populaire Java Message Service (JMS) API standard. Il existe un [article du Guide](service-bus-dotnet-advanced-message-queuing.md) qui explique comment effectuer la même opération à l’aide de l’API .NET du Bus de Service. Vous pouvez utiliser ces deux guides ensemble pour en savoir plus sur la messagerie multiplateforme à l’aide de AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Mise en route de Bus des services

Ce guide suppose que vous disposez déjà d’un espace de noms du Bus de Service contenant une file d’attente appelée **file d’attente 1**. Si vous ne le faites pas, vous pouvez [créer de l’espace de noms et de la file d’attente](service-bus-create-namespace-portal.md) à l’aide du [portail Azure](https://portal.azure.com). Pour plus d’informations sur la création des files d’attente et les espaces de noms de Bus de Service, voir [comment utiliser les files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md).

> [AZURE.NOTE] Rubriques et les files d’attente partitionnées prennent également en charge les AMQP. Pour plus d’informations, voir [entités de messagerie partitionnées](service-bus-partitioning.md) et [prise en charge de AMQP 1.0 pour le Bus de Service partitionnée de files d’attente et les rubriques](service-bus-partitioned-queues-and-topics-amqp-overview.md).

## <a name="downloading-the-amqp-10-jms-client-library"></a>Téléchargement de la bibliothèque de client AMQP 1.0 JMS

Pour plus d’informations sur l’emplacement où télécharger la dernière version de la bibliothèque de client Apache Qpid JMS AMQP 1.0, visitez le site [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Vous devez ajouter les quatre fichiers JAR suivants à partir de l’archive de la distribution 1.0 de AMQP JMS Apache Qpid au chemin de classe Java lors de la création et l’exécution d’applications JMS avec Bus de Service :

- geronimo-jms\_1.1\_1.0.jar-spécifications
- qpid-amqp-1-0-client-[version].jar
- qpid-amqp-1-0-client-JMS-[version].jar
- qpid-amqp-1-0-Common-[version].jar

## <a name="coding-java-applications"></a>Les applications Java de codage

### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming et Directory l’Interface JNDI)

JMS utilise le Java JNDI Naming and Directory Interface () pour créer une séparation entre les noms logiques et physiques. Deux types d’objets JMS sont résolus à l’aide de JNDI : ConnectionFactory et Destination. JNDI utilise un modèle de fournisseur dans lequel vous pouvez brancher des différents services d’annuaire pour gérer les tâches de résolution de nom. Mettre en forme la version Apache Qpid JMS AMQP 1.0 bibliothèque est fourni avec les propriétés d’un simple fournisseur JNDI basée sur un fichier qui est configuré à l’aide d’un fichier de propriétés des éléments suivants :

```
# servicebus.properties - sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurer la ConnectionFactory

L’entrée utilisée pour définir une **ConnectionFactory** dans le fournisseur Qpid propriétés JNDI est au format suivant :

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Où **[jndi_name]** et **[ConnectionURL]** ont les significations suivantes :

- **[jndi_name]**: le nom logique de la ConnectionFactory. Il s’agit du nom qui sera résolu dans l’application Java à l’aide de la méthode IntialContext.lookup() de JNDI.
- **[ConnectionURL]**: une URL qui fournit la bibliothèque JMS avec les informations requises pour le service Broker pour les AMQP.

Le format de la **ConnectionURL** est la suivante :

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Où **[namespace]**, **[SASPolicyName]** et **[SASPolicyKey]** ont les significations suivantes :

- **[namespace]**: espace de noms du Bus de Service.
- **[SASPolicyName]**: nom de la Signature des accès partagés de file d’attente la stratégie.
- **[SASPolicyKey]**: clé de stratégie de la Signature de l’accès partagé file d’attente.

> [AZURE.NOTE] Vous devez coder par URL le mot de passe manuellement. Un utilitaire pratique de codage URL est disponible à l’adresse [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### <a name="configure-destinations"></a>Configurer les destinations

L’entrée utilisée pour définir une destination dans le fournisseur Qpid propriétés JNDI est au format suivant :

```
queue.[jndi_name] = [physical_name]
```

ou

```
topic.[jndi_name] = [physical_name]
```

Où **[jndi\_nom]** et **[physique\_nom]** ont les significations suivantes :

- **[jndi_name]**: le nom logique de la destination. Il s’agit du nom qui sera résolu dans l’application Java à l’aide de la méthode IntialContext.lookup() de JNDI.
- **[nom_physique]**: le nom de l’entité de Bus de Service auquel l’application envoie ou reçoit des messages.

> [AZURE.NOTE] Lors de la réception d’un abonnement de rubrique du Bus de Service, le nom physique spécifié dans JNDI doit être le nom de la rubrique. Le nom de l’abonnement est fourni lors de la création de l’abonnement durable dans le code d’application JMS. Le [Guide du développeur Service Bus AMQP 1.0](service-bus-amqp-dotnet.md) fournit plus de détails sur l’utilisation des abonnements Bus de Service rubrique JMS.

### <a name="write-the-jms-application"></a>Écrire l’application JMS

Il n’y a aucune API spéciaux ou les options requises lors de l’utilisation de JMS avec Bus de Service. Toutefois, il y a quelques restrictions qui seront abordées ultérieurement. Comme avec toute application JMS, la première chose que nécessaire est la configuration de l’environnement JNDI, pour être en mesure de résoudre une **ConnectionFactory** et les destinations.

#### <a name="configure-the-jndi-initialcontext"></a>Configurer le InitialContext JNDI

L’environnement JNDI est configuré en passant d’une table de hachage des informations de configuration dans le constructeur de la classe javax.naming.InitialContext. Les deux requis dans la table de hachage, les éléments sont le nom de classe de la fabrique de contexte Initial et l’URL du fournisseur. Le code suivant montre comment configurer l’environnement JNDI pour utiliser le fichier de propriétés de Qpid basée JNDI fournisseur avec un fichier de propriétés nommé **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Une simple application de JMS à l’aide d’une file d’attente de Bus de Service

L’exemple de programme suivant envoie JMS TextMessages à une file d’attente de Bus de Service avec le nom logique de JNDI de file d’attente et reçoit les messages de retour.

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Exécution de l’application

Exécution de l’application génère une sortie du formulaire :

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Entre les plates-formes de messagerie entre JMS et .NET

Ce guide vous a montré comment envoyer et recevoir des messages vers et à partir de Bus des services à l’aide de JMS. Toutefois, un des principaux avantages de AMQP 1.0 est qu’il permet aux applications basées sur des composants écrits dans différents langages, avec messages échangés fiable et à toujours une fidélité optimale.

À l’aide de l’exemple d’application JMS décrites ci-dessus et une application .NET similaire prises dans le guide de [l’utilisation de 1.0 AMQP avec l’API de .NET de Bus de Service .NET](service-bus-dotnet-advanced-message-queuing.md), vous pouvez échanger des messages entre .NET et Java. 

Pour plus d’informations sur les détails de messagerie à l’aide du Service de Bus et AMQP 1.0 multiplateforme, consultez le [Guide du développeur Service Bus AMQP 1.0](service-bus-amqp-dotnet.md).

### <a name="jms-to-net"></a>JMS pour .NET

Pour illustrer JMS à .NET messaging :

* Démarrez l’exemple d’application .NET sans argument de ligne de commande.
* Démarrez l’exemple d’application Java avec l’argument de ligne de commande « sendonly ». Dans ce mode, l’application ne recevra pas les messages de la file d’attente, il enverra uniquement.
* Appuyez sur **entrée** plusieurs fois dans la console d’application Java, qui entraîne l’envoi des messages.
* Ces messages sont reçus par l’application .NET.

#### <a name="output-from-jms-application"></a>Sortie à partir de l’application JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Sortie à partir de l’application .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET à JMS

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

Les restrictions suivantes s’appliquent lors de l’utilisation de JMS sur 1.0 AMQP avec Bus de Service, à savoir :

* Un seul **MessageProducer** ou **MessageConsumer** est autorisée par **Session**. Si vous devez créer plusieurs **MessageProducers** ou **MessageConsumers** dans une application, créez une **Session** de dédiée pour chacun d’eux.
* Abonnement à une rubrique volatiles n’est actuellement pas pris en charge.
* **MessageSelectors** ne sont pas actuellement pris en charge.
* Destinations temporaires, par exemple, **TemporaryQueue**, **TemporaryTopic** ne sont pas actuellement pris en charge, ainsi que les **QueueRequestor** et les **TopicRequestor** des API qui les utilisent.
* Sessions transactionnelles et les transactions distribuées ne sont pas pris en charge.

## <a name="summary"></a>Résumé

Ce manuel vous a montré comment utiliser le Bus de Service demandé de fonctionnalités de messagerie (files d’attente et rubriques de publication et d’abonnement) à partir de Java à l’aide de l’API de JMS populaires et AMQP 1.0.

Vous pouvez également utiliser le Service Bus AMQP 1.0 à partir d’autres langages, y compris .NET, C, Python et PHP. Les composants créés à l’aide de ces différentes langues peuvent échanger des messages de façon fiable et à toujours une fidélité optimale à l’aide de la version 1.0 AMQP la prise en charge dans le Bus des services. Pour plus d’informations, consultez le [Guide du développeur Service Bus AMQP 1.0](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Étapes suivantes

* [Prise en charge de AMQP 1.0 dans le Bus des services Azure](service-bus-amqp-overview.md)
* [L’utilisation de AMQP 1.0 avec l’API .NET du Bus de Service](service-bus-dotnet-advanced-message-queuing.md)
* [Service de Bus AMQP 1.0 Guide du développeur](service-bus-amqp-dotnet.md)
* [L’utilisation de files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md)
* [Centre de développement Java](/develop/java/).



 
