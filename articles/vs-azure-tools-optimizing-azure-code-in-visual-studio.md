<properties
   pageTitle="Optimisation de votre code Azure dans Visual Studio | Microsoft Azure"
   description="Découvrez comment Azure code outils d’optimisation dans Visual Studio aident à rendre votre code plus robuste et réaliser."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>Optimisation de votre Code Azure

Lorsque vous programmez des applications qui utilisent Microsoft Azure, il existe certaines méthodes de codage que vous devez suivre pour vous aider à éviter les problèmes d’évolutivité de l’application, de comportement et de performances dans un environnement de cloud. Microsoft fournit un outil d’analyse de Code Azure qui reconnaît et identifie plusieurs de ces problèmes courants et vous aide à les résoudre. Vous pouvez télécharger l’outil dans Visual Studio via NuGet.

## <a name="azure-code-analysis-rules"></a>Règles d’analyse du Code Azure

L’outil d’analyse de Code Azure utilise les règles suivantes pour marquer automatiquement votre code Azure lorsqu’il détecte des performances en évitant les problèmes connus. A détecté les problèmes s’affichent sous la forme d’un avertissement ou les erreurs du compilateur. Correctifs de code ou des suggestions pour résoudre l’erreur ou l’avertissement sont souvent fournies par une icône d’ampoule.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Évitez d’utiliser le mode d’état de session (in-process) par défaut

### <a name="id"></a>ID

AP0000

### <a name="description"></a>Description

Si vous utilisez le mode d’état de session (in-process) par défaut pour les applications en nuage, vous pouvez perdre l’état de session.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Par défaut, le mode d’état de session spécifié dans le fichier web.config est en cours de processus. En outre, si aucune entrée n’est spécifiée dans le fichier de configuration, le mode d’état de Session par défaut dans le processus. Le mode in-process stocke l’état de session en mémoire sur le serveur web. Lorsqu’une instance est redémarrée ou une nouvelle instance est utilisée pour l’équilibrage de la charge ou prise en charge du basculement, l’état de session stockée en mémoire sur le serveur web n’est pas enregistré. Cette situation empêche l’application d’être évolutif sur le nuage.

L’état de session ASP.NET prend en charge plusieurs options de stockage différentes pour les données d’état de session : InProc, StateServer, SQLServer, personnalisé et Off. Il est recommandé d’utiliser le mode personnalisé pour héberger des données dans un magasin externe de l’état de Session, comme un [fournisseur d’état de Session Azure pour Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Solution

Il est une solution recommandée pour stocker l’état de session sur un service de cache géré. Apprenez à utiliser un [fournisseur d’état de Session Azure Redis](http://go.microsoft.com/fwlink/?LinkId=401521) pour stocker l’état de session. Vous pouvez également stocker l’état de session dans d’autres endroits afin de garantir votre application évolutive sur le nuage. Pour en savoir plus sur la solution solutions Veuillez lire que les [Modes d’état de Session](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Méthode d’exécution ne doit pas être asynchrone

### <a name="id"></a>ID

AP1000

### <a name="description"></a>Description

Créer des méthodes asynchrones (par exemple, [await](https://msdn.microsoft.com/library/hh156528.aspx)) de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) et appelez les méthodes asynchrones de [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Déclaration de la méthode [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) asynchrones provoque le rôle de collaborateur entrer dans une boucle de redémarrage.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Appel de méthodes asynchrone à l’intérieur de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) provoque l’exécution du service de nuage à recycler le rôle de travail. Un rôle worker au démarrage, tous les exécution du programme s’effectue à l’intérieur de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Sortie de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) provoque le rôle de collaborateur redémarrer. Lorsque le runtime du rôle Collaborateur accède à la méthode asynchrone, il distribue toutes les opérations après la méthode asynchrone et renvoie ensuite. Ainsi, le rôle de collaborateur quitter à partir de la méthode [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) et redémarrer. Dans la prochaine itération de l’exécution, le rôle de travail atteint de nouveau la méthode async et redémarre, à l’origine de recyclage ainsi que le rôle de travail.

### <a name="solution"></a>Solution

Placez toutes les opérations asynchrones en dehors de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Ensuite, appelez la méthode async refactorisé dans à l’intérieur de la méthode [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , par exemple RunAsync () .wait. L’outil d’analyse de Code Azure peut vous aider à résoudre ce problème.

L’extrait de code suivant illustre le correctif de code pour ce problème :

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Utiliser l’authentification de la Signature de Service Bus partagés accès

### <a name="id"></a>ID

AP2000

### <a name="description"></a>Description

Utiliser la Signature de l’accès partagé (SAS) pour l’authentification. Service de contrôle d’accès (ACS) est désapprouvé pour l’authentification de bus de service.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Pour une sécurité améliorée, Azure Active Directory consiste à remplacer l’authentification ACS avec l’authentification d’associations de sécurité. Pour plus d’informations sur le plan de transition, consultez [Azure Active Directory est l’avenir de l’ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) .

### <a name="solution"></a>Solution

Utiliser l’authentification d’associations de sécurité dans vos applications. L’exemple suivant montre comment utiliser un jeton d’associations de sécurité existant pour accéder à un espace de noms de service bus ou d’une entité.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Consultez les rubriques suivantes pour plus d’informations.

- Pour une vue d’ensemble, consultez [Authentification de Signature à accès partagé avec Bus de Service](https://msdn.microsoft.com/library/dn170477.aspx)

- [Comment utiliser l’authentification de Signature accès partagé avec le Bus des services](https://msdn.microsoft.com/library/dn205161.aspx)

- Pour un exemple de projet, consultez [l’authentification à l’aide de partagés accès Signature (SAS) avec les abonnements de Bus de Service](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Envisagez l’utilisation de la méthode OnMessage pour éviter de « boucle de recevoir »

### <a name="id"></a>ID

AP2002

### <a name="description"></a>Description

Pour éviter d’entrer dans une « boucle de réception », l’appel à la méthode **OnMessage** est une meilleure solution pour la réception de messages que l’appel de la méthode de **réception** . Toutefois, si vous devez utiliser la méthode de **réception** et que vous spécifiez le temps d’attente un serveur par défaut, veillez à ce que le délai d’attente de serveur est plus d’une minute.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Lors de l’appel **OnMessage**, le client démarre une pompe de messages interne qui interroge en permanence de la file d’attente ou l’abonnement. Cette pompe de messages contient une boucle infinie qui émet un appel à recevoir des messages. Si l’appel arrive à expiration, il émet un nouvel appel. L’intervalle de délai d’attente est déterminé par la valeur de la propriété [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) de la [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)qui est utilisé.

L’avantage de l’utilisation **OnMessage** par rapport à la **réception** , que les utilisateurs ne doivent vérifier les messages, gérer les exceptions, traiter plusieurs messages en parallèle et terminer les messages manuellement.

Si vous appelez la **réception** sans l’aide de sa valeur par défaut, veillez à ce que la valeur de *ServerWaitTime* est plus d’une minute. Définition de *ServerWaitTime* plus d’une minute empêche le serveur d’arriver à expiration avant que le message a été reçu entièrement.

### <a name="solution"></a>Solution

Consultez les exemples de code suivants pour les utilisations recommandées. Pour plus d’informations, consultez [QueueClient.OnMessage, méthode (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)et [Méthode de QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Pour améliorer les performances de l’infrastructure de messagerie Azure, consultez le modèle de design [Asynchrone Introduction à la messagerie](https://msdn.microsoft.com/library/dn589781.aspx).

Voici un exemple d’utilisation de **OnMessage** pour recevoir des messages.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Voici un exemple d’utilisation de **réception** avec le délai d’attente de serveur par défaut.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Voici un exemple d’utilisation de **réception** avec un délai d’attente par défaut serveur.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Envisagez l’utilisation de méthodes asynchrones de Bus des services

### <a name="id"></a>ID

AP2003

### <a name="description"></a>Description

Méthodes asynchrones de Bus de Service permet d’améliorer les performances de messagerie contemporains.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Permet de simultanéité de programme d’application à l’aide de méthodes asynchrones, car l’exécution de chaque appel ne bloque pas le thread principal. Lors de l’utilisation du Bus des services messagerie des méthodes, l’exécution d’une opération (envoyer, recevoir, supprimer, etc.) prend du temps. Ce temps inclut le traitement de l’opération par le service de Bus de Service en plus de la latence de la demande et la réponse. Pour augmenter le nombre d’opérations par heure, les opérations doivent s’exécuter simultanément. Pour plus d’informations, veuillez consulter [Les méthodes conseillées pour les performances des améliorations à l’aide de Service Bus demandé de la messagerie](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Solution

Pour plus d’informations sur l’utilisation de la méthode asynchrone recommandée, reportez-vous à la section [Classe de QueueClient (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) .

Pour améliorer les performances de l’infrastructure de messagerie Azure, consultez le modèle de design [Asynchrone Introduction à la messagerie](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Envisagez le partitionnement des files d’attente de Bus de services et rubriques

### <a name="id"></a>ID

AP2004

### <a name="description"></a>Description

Files d’attente de Bus de Service de partition et des rubriques pour de meilleures performances avec le Bus des services de messagerie.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Files d’attente de Bus de services et rubriques le partitionnement des performances de disponibilité de service et de débit dans la mesure où le débit global d’une rubrique ou une file d’attente partitionnée n’est plus limité par les performances d’un courtier de message unique ou de la banque d’informations. En outre, une panne temporaire d’une banque d’information ne rend pas partitionnée de file d’attente ou rubrique non disponible. Pour plus d’informations, consultez [Le partitionnement des entités de messagerie](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Solution

L’extrait de code suivant montre comment partitionner des entités de messagerie.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Pour plus d’informations, consultez les rubriques et les files d’attente de Bus de Service de [partitionné | Microsoft Azure Blog](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) et l’extraction de l’échantillon de la [File d’attente de Microsoft Azure Service Bus partitionné](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) .

## <a name="do-not-set-sharedaccessstarttime"></a>Ne définissez pas de SharedAccessStartTime

### <a name="id"></a>ID

AP3001

### <a name="description"></a>Description

Vous devez éviter d’utiliser des SharedAccessStartTimeset à l’heure actuelle à commencer immédiatement à la stratégie d’accès partagé. Vous devez uniquement définir cette propriété si vous souhaitez démarrer la stratégie d’accès partagé à un moment ultérieur.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Synchronisation de l’horloge provoque une légère différence entre les centres de données. Par exemple, vous pourriez penser logiquement la définition d’une stratégie de stockage SAS, l’heure de début en tant que l’heure actuelle à l’aide de DateTime.Now ou une méthode similaire provoquera la stratégie SAS prennent effet immédiatement. Toutefois, les différences d’heure légèrement entre les centres de données peuvent entraîner des problèmes avec ce dans la mesure où certaines durées de centre de données peuvent être légèrement postérieure à l’heure de début, tandis que d’autres, une longueur d’avance. Par conséquent, la stratégie SAS peut expirer rapidement (ou même immédiatement) si la durée de vie de stratégie est trop faible.

Pour plus d’informations sur l’utilisation de la Signature de l’accès partagé sur le stockage Azure, consultez [Présentation de Table SAS (partagés accès Signature), SAS de file d’attente et la mise à jour de Blob SAS - Blog de l’équipe Microsoft Azure Storage - Site Home - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solution

Supprimez l’instruction qui définit l’heure de début de la stratégie d’accès partagé. L’outil d’analyse de Code Azure fournit un correctif pour ce problème. Pour plus d’informations sur la gestion de la sécurité, consultez le modèle de conception [Avec voiturier clé de modèle](https://msdn.microsoft.com/library/dn568102.aspx).

L’extrait de code suivant illustre le correctif de code pour le même problème.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Partagé de stratégie d’accès au délai d’expiration doit être de plus de cinq minutes.

### <a name="id"></a>ID

AP3002

### <a name="description"></a>Description

Il peut être aussi une minute cinq différence horloges entre les centres de données situés à divers endroits en raison d’une condition connue sous la forme « différence d’heure. » Pour éviter que les associations de sécurité jeton de stratégie à partir de la date d’expiration définie plus tôt que prévu, le délai d’expiration à plus de cinq minutes.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Synchronisent les centres de données à différents emplacements dans le monde entier par un signal d’horloge. Parce qu’il prend le temps de signal d’horloge à se déplacer vers des emplacements différents, il peut y avoir un écart de temps entre les centres de données à des emplacements géographiques différents bien que tout ce qui est censé synchronisé. Différence de temps peut affecter l’accès partagé début heure et date d’expiration intervalle la stratégie. Par conséquent, pour vérifier la stratégie d’accès partagé prend effet immédiatement, ne spécifiez pas l’heure de début. En outre, assurez-vous que le délai d’expiration est de plus de 5 minutes afin d’éviter le délai d’attente au plus tôt.

Pour plus d’informations sur l’utilisation de la Signature de l’accès partagé sur le stockage Azure, consultez [Présentation de Table SAS (partagés accès Signature), SAS de file d’attente et la mise à jour de Blob SAS - Blog de l’équipe Microsoft Azure Storage - Site Home - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solution

Pour plus d’informations sur la gestion de la sécurité, consultez le modèle de conception [Avec voiturier clé de modèle](https://msdn.microsoft.com/library/dn568102.aspx).

Voici un exemple de ne pas spécifier une heure de début de stratégie accès partagé.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Voici un exemple de spécification d’une heure de début de stratégie accès partagé avec une durée d’expiration de stratégie plue de cinq minutes.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Pour plus d’informations, consultez [Création et utilisation d’une Signature d’accès partagé](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Utiliser le CloudConfigurationManager

### <a name="id"></a>ID

AP4000

### <a name="description"></a>Description

Pour les projets de site Web d’Azure et d’Azure services mobiles à l’aide de la classe [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) ne présente des problèmes d’exécution. Toutefois, pour obtenir les meilleurs résultats, il est judicieux d’utiliser le nuage[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) sous la forme d’une manière unifiée de gestion des configurations pour toutes les applications Azure Cloud.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

CloudConfigurationManager lit le fichier de configuration approprié à l’environnement de l’application.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Solution

Refactoriser votre code pour utiliser le [CloudConfigurationManager classe](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Un correctif pour ce problème est fourni par l’outil d’analyse de Code Azure.

L’extrait de code suivant illustre le correctif de code pour le même problème. Remplacer

`var settings = ConfigurationManager.AppSettings["mySettings"];`

avec

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Voici un exemple de la façon de stocker les paramètres de configuration dans un fichier App.config ou Web.config. Ajoutez les paramètres de la section appSettings du fichier de configuration. Voici le fichier Web.config pour l’exemple de code précédent.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Évitez d’utiliser des chaînes de connexion codées en dur

### <a name="id"></a>ID

AP4001

### <a name="description"></a>Description

Si vous utilisez des chaînes de connexion codées en dur, et vous devez les mettre à jour ultérieurement, vous devrez apporter des modifications à votre code source et recompiler l’application. Toutefois, si vous stockez vos chaînes de connexion dans un fichier de configuration, vous pouvez les modifier ultérieurement en mettant simplement à jour le fichier de configuration.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Coder en dur les chaînes de connexion est une mauvaise pratique, car elle présente des problèmes lorsque les chaînes de connexion doivent être modifiées rapidement. En outre, si le projet doit être archivé dans le contrôle de code source, les chaînes de connexion codées en dur introduisent des failles de sécurité dans la mesure où les chaînes peuvent être affichées dans le code source.

### <a name="solution"></a>Solution

Stocker des chaînes de connexion dans les fichiers de configuration ou des environnements Azure.

- Pour les applications autonomes, utilisez app.config pour stocker des paramètres de chaîne de connexion.

- Pour les applications hébergées par IIS le web, utilisez web.config pour stocker des chaînes de connexion.

- Pour les applications ASP.NET vNext, utilisez configuration.json pour stocker les chaînes de connexion.

Pour plus d’informations sur l’utilisation de fichiers de configuration tels que web.config ou app.config, consultez [Les instructions de Configuration de Web ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Pour plus d’informations sur comment Azure travail de variables d’environnement, consultez [Sites Web Azure : comment les chaînes d’Application et de l’utilisation de chaînes de connexion](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Pour plus d’informations sur le stockage de la chaîne de connexion dans le contrôle de code source, consultez [Évitez de placer des informations sensibles telles que des chaînes de connexion dans des fichiers qui sont stockés dans le référentiel de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Utilisez le fichier de configuration de diagnostics

### <a name="id"></a>ID

AP5000

### <a name="description"></a>Description

Au lieu de configurer les paramètres de diagnostic dans votre code, par exemple à l’aide de l’API de programmation de Microsoft.WindowsAzure.Diagnostics, vous devez configurer les paramètres des diagnostics dans le fichier diagnostics.wadcfg. (Ou diagnostics.wadcfgx si vous utilisez Azure SDK 2.5). En procédant ainsi, vous pouvez modifier des paramètres de diagnostic sans avoir à recompiler votre code.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Avant d’Azure SDK 2.5 (qui utilise les diagnostics de Windows Azure 1.3), Azure Diagnostics (WAD) peut être configuré à l’aide de différentes méthodes : ajouter à l’objet blob de configuration dans le stockage à l’aide de code impératif, configuration déclarative ou la configuration par défaut. Toutefois, la meilleure façon de configurer les diagnostics est d’utiliser un fichier de configuration XML (diagnostics.wadcfg ou diagnositcs.wadcfgx pour le SDK 2.5 et version ultérieure) dans le projet d’application. Dans cette approche, le fichier diagnostics.wadcfg complètement définit la configuration et peut être mis à jour et redéployé à volonté. L’utilisation du fichier de configuration diagnostics.wadcfg de mélange avec les méthodes de programmation de la définition de configurations en utilisant les classes [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)ou [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)peut entraîner une confusion. Pour plus d’informations, reportez-vous à la section [initialisation ou modifier la Configuration de diagnostic Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) .

À partir de 1,3 WAD (inclus avec Azure SDK 2.5), il n’est plus possible d’utiliser le code pour configurer les tests de diagnostic. Par conséquent, vous ne permet que la configuration lors de l’application ou la mise à jour de l’extension des tests de diagnostic.

### <a name="solution"></a>Solution

Le Concepteur de configuration de diagnostics permet de déplacer les paramètres de diagnostic pour le fichier de configuration de diagnostics (diagnositcs.wadcfg ou diagnositcs.wadcfgx pour le SDK 2.5 et versions ultérieures). Il est également recommandé que vous installez [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) et utilisez la fonctionnalité de diagnostics plus récente.

1. Dans le menu contextuel pour le rôle que vous souhaitez configurer, cliquez sur Propriétés et puis cliquez sur l’onglet Configuration.

1. Dans la section **Diagnostics** , assurez-vous que la case à cocher **Activer les tests de diagnostic** est activée.

1. Cliquez sur le bouton **configurer** .

  ![Accès à l’option Activer les Diagnostics](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Pour plus d’informations, reportez-vous à la section [Configuration des Diagnostics pour les Services en nuage Azure et les Machines virtuelles](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) .


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Éviter de déclarer des objets de DbContext comme static

### <a name="id"></a>ID

AP6000

### <a name="description"></a>Description

Pour économiser de la mémoire, évitez de déclarer des objets DBContext comme static.

Pensez à partager vos réactions et vos suggestions à [l’évaluation de l’analyse du Code Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison

Les objets de DBContext contiennent les résultats de la requête à partir de chaque appel. Les objets statiques de DBContext ne sont pas supprimés jusqu'à ce que le domaine d’application est déchargé. Par conséquent, un objet DBContext statique peut consommer une quantité importante de mémoire.

### <a name="solution"></a>Solution

Déclarer DBContext sous la forme d’une variable locale ou un champ d’instance de non statique et l’utiliser pour une tâche puis lui être supprimées après utilisation.

L’exemple de classe de contrôleur MVC suivant montre comment utiliser l’objet DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les optimzing et résolution des problèmes d’applications Azure, consultez [résoudre les problèmes d’une application web dans le Service d’application Azure à l’aide de Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).
