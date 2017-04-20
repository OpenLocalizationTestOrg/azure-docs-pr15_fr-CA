<properties 
    pageTitle="Quel est le Kit de développement logiciel WebJobs Azure" 
    description="Introduction à la WebJobs d’Azure SDK. Explique ce que le Kit de développement logiciel, des scénarios classiques, il est utile pour et exemples de code." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>Quel est le Kit de développement logiciel WebJobs Azure

## <a id="overview"></a>Vue d’ensemble

Cet article explique ce qu’est le SDK WebJobs, passe en revue quelques scénarios courants, il est utile pour et donne une vue d’ensemble de la façon dont vous l’utiliser dans votre code.

[WebJobs](websites-webjobs-resources.md) est une fonctionnalité de Service d’application Azure qui vous permet d’exécuter un programme ou un script dans le même contexte sous la forme d’une application web, API app ou application mobile. Le [Kit de développement logiciel WebJobs](websites-webjobs-resources.md) vise à simplifier le code que vous écrivez pour les tâches courantes qu’un WebJob peut effectuer, telles que le traitement d’image, de traitement de la file d’attente, RSS agrégation, gestion des fichiers et l’envoi d’e-mails. Le SDK WebJobs possède des fonctionnalités intégrées pour travailler avec le stockage Azure et Bus de Service, tâches de planification et de gestion des erreurs et de nombreux autres scénarios courants. En outre, il est conçu pour être extensible. [Kit de développement logiciel WebJobs est open source](https://github.com/Azure/azure-webjobs-sdk/), et il est un [référentiel de code source pour les extensions d’ouvrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

Le SDK WebJobs inclut les composants suivants :

* **Packages NuGet**. Les packages NuGet que vous ajoutez à un projet d’Application de Console Visual Studio fournissent une structure que votre code utilise en décorant vos méthodes avec les attributs WebJobs SDK.
  
* **Tableau de bord**. Partie du SDK WebJobs est inclus dans le Service d’application Azure ainsi enrichi de surveillance et de diagnostic pour les programmes qui utilisent les packages NuGet. Vous n’êtes pas obligé d’écrire du code pour utiliser ces fonctionnalités de surveillance et de diagnostic.

## <a id="scenarios"></a>Scénarios

Voici quelques scénarios classiques que vous pouvez gérer plus facilement le Kit de développement SDK WebJobs Azure :

* Image de transformation ou d’autre du processeur de manière intensive. Une fonctionnalité courante des sites Web est la possibilité de télécharger des images ou des vidéos. Souvent vous souhaitez manipuler le contenu une fois qu’il est chargé, mais que vous ne souhaitez pas faire patienter pendant que vous effectuez qui de l’utilisateur.

* Traitement de la file d’attente. Un moyen courant pour un site Web frontal web communiquer avec un service back-end est d’utiliser des files d’attente. Lorsque le site Web a besoin travailler, il envoie un message vers une file d’attente. Un service back-end extrait les messages de la file d’attente et effectue le travail. Vous pouvez utiliser des files d’attente pour le traitement de l’image : par exemple, une fois que l’utilisateur télécharge un certain nombre de fichiers, placez les noms de fichiers dans un message de la file d’attente pour être récupérées par le serveur principal pour traitement. Ou bien, vous pouvez utiliser des files d’attente afin d’améliorer la réactivité du site. Par exemple, au lieu d’écrire directement à une base de données SQL, écrire dans une file d’attente, indiquer à l’utilisateur que vous avez terminé et permettent d’utiliser la base de données relationnelle back-end service poignée à latence élevée. Pour obtenir un exemple de file d’attente de traitement avec un processus d’image, consultez le [didacticiel de WebJobs SDK mise en route](websites-dotnet-webjobs-sdk-get-started.md).

* Agrégation RSS. Si vous avez un site qui gère une liste de flux RSS, vous pourriez extraire dans tous les articles à partir de flux dans un processus d’arrière-plan.

* Gestion des fichiers, telles que l’agrégation ou le nettoyage des fichiers journaux.  Vous pouvez avoir des fichiers journaux en cours créés par plusieurs sites ou pour des périodes distinctes que vous voulez combiner pour exécuter des tâches d’analyse sur les. Ou vous pouvez planifier une tâche à exécuter toutes les semaines pour nettoyer les anciens fichiers journaux.

* Pénétration dans les Tables Azure. Vous pouvez ont stocké des fichiers et des objets BLOB et souhaitez les analyser et de stocker les données dans des tables. La fonction d’entrée pourrait écrire beaucoup de lignes (des millions dans certains cas), et le SDK WebJobs rend possible d’implémenter cette fonctionnalité facilement. Le SDK fournit également une surveillance en temps réel des indicateurs de progression comme le nombre de lignes écrites dans la table.

* Autres tâches à exécution longue que vous souhaitez exécuter dans un thread d’arrière-plan, telles que [l’envoi d’e-mails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

* Toutes les tâches que vous souhaitez exécuter selon une planification, par exemple pour effectuer une opération de sauvegarde chaque nuit.

Dans la plupart de ces scénarios, vous souhaiterez mettre à l’échelle d’une application web pour s’exécuter sur plusieurs ordinateurs virtuels, qui exécutent simultanément plusieurs WebJobs. Dans certains scénarios, cela peut entraîner des mêmes données traitées plusieurs fois, mais ce n’est pas un problème lorsque vous utilisez la file d’attente intégré, blob et les déclencheurs de Bus des services du SDK WebJobs. Le Kit de développement logiciel garantit que vos fonctions seront traitées qu’une seule fois pour chaque message ou d’un objet blob.

Le SDK WebJobs facilite également gérer le traitement des scénarios d’erreurs courantes. Vous pouvez définir des alertes pour envoyer des notifications lorsqu’une fonction échoue, et vous pouvez définir les délais d’attente afin qu’une fonction est automatiquement annulée si elle ne se termine dans un délai spécifié.

## <a id="code"></a>Exemples de code

Le code de gestion des tâches courantes qui fonctionnent avec le stockage Azure est simple. Dans de votre Application Console `Main` méthode vous créez un `JobHost` objet qui coordonne les appels aux méthodes que vous écrivez. Le framework WebJobs SDK sait quand il doit appeler les méthodes et les valeurs de paramètre à utiliser en fonction des attributs WebJobs SDK que vous utilisez dans les. Le SDK fournit des *déclencheurs* qui spécifient les conditions entraînent la fonction à appeler et *les classeurs* qui spécifient comment obtenir des informations vers et à partir des paramètres de méthode.

Par exemple, l’attribut [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) permet à une fonction qui est appelée lorsqu’un message est reçu sur une file d’attente, et si le message est au format JSON pour un tableau d’octets ou un type personnalisé, le message est désérialisé automatiquement. L’attribut [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) déclenche un processus lors de la création d’un objet blob de nouveau dans un compte de stockage Azure.

Voici un programme simple qui interroge une file d’attente et crée un objet blob pour chaque message de la file d’attente reçu :

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

Le `JobHost` objet est un conteneur pour un ensemble de fonctions d’arrière-plan. Le `JobHost` objet surveille les fonctions, observe les événements qui déclenchent les, et exécute les fonctions lorsque se produisent les événements déclencheurs. Vous appelez un `JobHost` méthode pour indiquer si vous souhaitez le processus conteneur s’exécute sur le thread en cours ou d’un thread d’arrière-plan. Dans l’exemple, le `RunAndBlock` méthode s’exécute le processus en continu sur le thread actuel.

Étant donné que la `ProcessQueueMessage` méthode dans cet exemple a un `QueueTrigger` d’attribut, le déclencheur pour cette fonction est la réception d’un nouveau message de la file d’attente. Le `JobHost` objet surveille les nouveaux messages de file d’attente sur la file d’attente spécifié (« webjobsqueue » dans cet exemple) et lorsqu’il en trouve, il appelle `ProcessQueueMessage`. 

Le `QueueTrigger` attribut lie le `inputText` paramètre à la valeur du message de la file d’attente. Et la `Blob` attribut lie une `TextWriter` objet à un blob appelé « blobname » dans un conteneur nommé « containername ».  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

La fonction utilise ces paramètres pour écrire la valeur du message de la file d’attente dans le blob :

        writer.WriteLine(inputText);

Les fonctionnalités de déclencheur et liant du SDK WebJobs simplifient considérablement le code à écrire. Le code de bas niveau requis pour traiter les files d’attente, des objets BLOB ou des fichiers ou pour lancer des tâches planifiées, est effectué pour vous par l’infrastructure WebJobs SDK. Par exemple, l’infrastructure crée des files d’attente qui n’existent pas encore, ouvre la file d’attente, les messages de la file d’attente des lectures, supprime la file d’attente les messages lorsque le traitement est terminé, crée des conteneurs blob qui n’existent pas encore, écrit pour les objets BLOB et ainsi de suite.

L’exemple de code suivant montre une variété de déclencheurs dans une WebJob : `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, et `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a>Planification

Le `TimerTrigger` attribut vous donne la possibilité aux fonctions de déclencheur à exécuter selon une planification. Vous pouvez planifier un WebJob comme un tout par le biais des fonctions individuelles Azure ou de la planification d’une WebJob à l’aide du SDK WebJobs `TimerTrigger`. Voici un exemple de code.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Pour des exemples de code, consultez [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) dans le référentiel d’azure-webjobs-Kit de développement logiciel-extensions sur GitHub.com.

## <a name="extensibility"></a>Extensibilité

Vous n’êtes pas limité à une fonctionnalité intégrée--le SDK WebJobs vous permet d’écrire les classeurs et les déclencheurs personnalisés.  Par exemple, vous pouvez écrire des déclencheurs pour les événements de cache et de la planification périodique. Un [référentiel de code source d’ouvrir](https://github.com/Azure/azure-webjobs-sdk-extensions) contient un [guide détaillé sur l’extensibilité du Kit de développement logiciel WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) exemple de code pour vous aider à commencer à écrire vos propres classeurs et les déclencheurs.

## <a id="workerrole"></a>À l’aide du SDK WebJobs hors WebJobs

Un programme qui utilise le le SDK WebJobs est une Application de Console standard et anywhere peut être exécuté, il ne doit pas s’exécuter comme un WebJob. Vous pouvez tester localement le programme sur votre ordinateur de développement et de production vous pouvez l’exécuter dans un rôle de collaborateur du Service en nuage ou d’un service Windows si vous préférez un de ces environnements. 

Toutefois, le tableau de bord est uniquement disponible en tant qu’extension d’une application web de Service d’application Azure. Si vous souhaitez exécuter en dehors d’un WebJob et continuer à utiliser le tableau de bord, vous pouvez configurer une application web pour utiliser le même compte de stockage que votre chaîne de connexion du tableau de bord SDK WebJobs fait référence à, et que WebJobs tableau de bord l’application web puis affiche les données sur l’exécution de la fonction à partir de votre programme est en cours d’exécution dans un autre emplacement. Vous pouvez obtenir au tableau de bord en utilisant l’URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Pour plus d’informations, consultez [mise en route d’un tableau de bord de développement local avec le Kit de développement de WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), mais notez que le billet de blog indique un ancien nom de chaîne de connexion. 

## <a id="nostorage"></a>Fonctionnalités de tableau de bord

Le SDK WebJobs offre plusieurs avantages même si vous n’utilisez pas les déclencheurs WebJobs SDK ou classeurs :

* Vous pouvez appeler les fonctions du tableau de bord.
* Vous pouvez relire les fonctions du tableau de bord.
* Vous pouvez afficher les journaux dans le tableau de bord liés à la WebJob particulier (journaux d’application, écrits à l’aide de Console.Out, Console.Error, suivi, etc.) ou liée à l’appel de fonction particulier qui les a générées (journaux générés à l’aide d’un `TextWriter` objet qui passe par le Kit de développement de la fonction en tant que paramètre). 

Pour plus d’informations, consultez [comment appeler manuellement une fonction](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) et [comment écrire des journaux](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Étapes suivantes

Pour plus d’informations sur le SDK WebJobs, reportez-vous à la section [Ressources recommandé des WebJobs d’Azure](http://go.microsoft.com/fwlink/?linkid=390226).

Pour plus d’informations sur les améliorations les plus récentes pour le SDK WebJobs, consultez les [Notes de version](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 
