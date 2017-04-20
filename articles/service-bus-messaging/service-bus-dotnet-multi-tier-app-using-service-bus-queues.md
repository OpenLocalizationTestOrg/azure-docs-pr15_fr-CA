<properties
    pageTitle="Application à plusieurs niveaux de .NET | Microsoft Azure"
    description="Un didacticiel de .NET qui vous permet de développer une application n-tier dans Azure qui utilise des files d’attente de Bus de Service pour communiquer entre les différents niveaux."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Application à plusieurs niveaux de .NET à l’aide de files d’attente du Bus des services Azure

## <a name="introduction"></a>Introduction

Le développement pour Microsoft Azure est facile à l’aide de Visual Studio et le Kit de développement logiciel libre Azure pour .NET. Ce didacticiel vous guide tout au long de la procédure de création d’une application qui utilise plusieurs ressources Azure en cours d’exécution dans votre environnement local. Les étapes supposent que vous n’avez aucune expérience préalable à l’aide d’Azure.

Vous apprendrez les éléments suivants :

-   Comment permettre à votre ordinateur de développement Azure avec un seul téléchargement et l’installation.
-   Explique comment utiliser Visual Studio pour le développement pour Azure.
-   Comment créer une application n-tier dans Azure à l’aide de rôles web et worker.
-   La communication entre les niveaux à l’aide de files d’attente de Bus de Service.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

Dans ce didacticiel, vous allez générer et exécuter l’application à plusieurs niveaux dans un service cloud Azure. Le front-end est un rôle de web ASP.NET MVC et le back-end est un rôle de travail qui utilise une file d’attente de Bus de Service. Vous pouvez créer la même application à plusieurs niveaux avec le front-end sous la forme d’un projet web est déployé sur un site Web d’Azure au lieu d’un service en nuage. Pour obtenir des instructions sur la procédure à faire différemment sur un front-end de site Web Azure, consultez la section [étapes suivantes](#nextsteps) . Vous pouvez également essayer le didacticiel [d’application sur-locaux/cloud hybride de .NET](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) .

La capture d’écran suivante montre l’application terminée.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Vue d’ensemble du scénario : communication entre les rôles entre

Pour soumettre une commande pour le traitement, le composant frontal de l’interface utilisateur, en cours d’exécution dans le rôle web, doit interagir avec la logique de la couche intermédiaire en cours d’exécution dans le rôle de travail. Cet exemple utilise un Bus de Service demandé de la messagerie pour la communication entre les couches.

À l’aide de la messagerie de courtage entre le web et le niveau intermédiaire sépare les deux composants. Contrairement à la messagerie directe (c'est-à-dire, TCP ou HTTP), la couche web ne se connecte pas à la couche intermédiaire directement ; à la place qu’il exécute un push d’unités de travail, sous forme de messages, dans le Bus des services, qui fiable les conserve jusqu'à ce que le niveau intermédiaire est prêt à consommer et de les traiter.

Bus de service fournit deux entités pour prendre en charge la messagerie contemporains : files d’attente et des rubriques. Avec les files d’attente, chaque message envoyé à la file d’attente est consommée par un récepteur unique. Rubriques prend en charge le modèle de publication et d’abonnement dans lequel chaque message publié est rendu disponible pour un abonnement enregistré avec le sujet. Chaque abonnement logiquement met à jour sa propre file d’attente de messages. Abonnements peuvent également être configurés avec des règles de filtrage qui limitent l’ensemble des messages transmis à la file d’attente de l’abonnement à ceux qui correspondent au filtre. L’exemple suivant utilise des files d’attente de Bus de Service.

![][1]

Ce mécanisme de communication a plusieurs avantages par rapport à la messagerie directe :

-   **Découplage temporelle.** Avec le modèle de messagerie asynchrone, les consommateurs et les producteurs ne sont pas nécessairement en ligne en même temps. Bus de service fiable stocke les messages jusqu'à ce que la partie (consommateur) est prête à les recevoir. Ainsi, les composants de l’application distribuée à être déconnecté, soit volontairement, par exemple, pour la maintenance, ou suite à une panne de composant, sans impact sur le système dans son ensemble. En outre, l’application consommatrice suffit peut-être d’apparaître en ligne pendant certaines heures de la journée.

-   **Nivellement de charge.** Dans de nombreuses applications, la charge du système varie avec le temps, alors que le temps de traitement requis pour chaque unité de travail est généralement constant. Echanger producteurs de message et les consommateurs avec une file d’attente signifie que l’application consommatrice (le travailleur) ne doit être configuré pour prendre en compte la charge moyenne plutôt que des pics de charge. La profondeur de la file d’attente augmente et contrats comme les variations de la charge entrante. Cela directement économise de l’argent en termes d’infrastructure requise pour traiter la charge de l’application.

-   **L’équilibrage de la charge.** Que la charge augmente, plus les processus de travail peuvent être ajoutés pour lire à partir de la file d’attente. Chaque message est traité par un seul processus de travail. En outre, cette extraction d’équilibrage de charge permet une utilisation optimale des machines de travail même si les ordinateurs de travail diffèrent en termes de puissance de traitement, comme ils extrait les messages à leur propre taux maximal. Le modèle de *concurrence consommateur* est souvent qualifié de ce modèle.

    ![][2]

Les sections suivantes présentent le code qui met en œuvre cette architecture.

## <a name="set-up-the-development-environment"></a>Configurer l’environnement de développement

Avant de commencer à développer des applications Azure, obtenez les outils et de votre environnement de développement.

1.  Installer le Kit de développement Azure pour .NET à [obtenir les outils et Kit de développement logiciel][].

2.  Cliquez sur **installer le Kit de développement** de la version de Visual Studio que vous utilisez. Les étapes de ce didacticiel utilisent Visual Studio 2015.

4.  Lorsque vous êtes invité à exécuter ou enregistrer le programme d’installation, cliquez sur **exécuter**.

5.  Dans **Programme d’installation de la plate-forme Web**, cliquez sur **installer** et poursuivre l’installation.

6.  Une fois l’installation terminée, vous disposez de tous les éléments nécessaires pour commencer à développer l’application. Le SDK inclut des outils qui vous permettent de développer facilement des applications Azure dans Visual Studio. Si vous n’avez pas installé de Visual Studio, le Kit de développement installe également la version gratuite Express de Visual Studio.

## <a name="create-a-namespace"></a>Créer un espace de noms

L’étape suivante consiste à créer un espace de noms du service et obtenir une clé de Signature de l’accès partagé (SAS). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais de Bus de Service. Une clé SAS est générée par le système lors de la création d’un espace de noms. La combinaison de l’espace de noms et la clé des associations de sécurité fournit les informations d’identification pour le Bus de Service authentifier l’accès à une application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Créer un rôle web

Dans cette section, vous créez la partie frontale de votre application. Tout d’abord, vous créez les pages de votre application.
Ensuite, ajoutez le code qui envoie des articles à une file d’attente de Bus de Service et affiche des informations d’état sur la file d’attente.

### <a name="create-the-project"></a>Créer le projet

1.  À l’aide des privilèges d’administrateur, démarrez Visual Studio de Microsoft. Pour démarrer Visual Studio avec des privilèges d’administrateur, cliquez sur l’icône de programme **Visual Studio** , puis cliquez sur **Exécuter en tant qu’administrateur**. L’émulateur de calcul Azure, décrit plus loin dans cet article, nécessite que Visual Studio d’être démarré avec des privilèges d’administrateur.

    Dans Visual Studio, dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.

2.  À partir de **Modèles installés**, sous **Visual C#**, cliquez sur le **nuage** et puis cliquez sur **Azure Cloud Service**. Nommez le projet **MultiTierApp**. Puis cliquez sur **OK**.

    ![][9]

3.  Dans les rôles de **.NET Framework 4.5** , double-cliquez sur le **Rôle de Web ASP.NET**.

    ![][10]

4.  Survolez **WebRole1** sous **Azure Cloud Service solution**, cliquez sur l’icône de crayon et renommez le rôle web **FrontendWebRole**. Puis cliquez sur **OK**. (Assurez-vous que vous entrez « Frontal » avec une minuscule « e' pas « frontal ».)

    ![][11]

5.  Dans la boîte de dialogue **Nouveau projet ASP.NET** , dans la liste **Sélectionnez un modèle** , cliquez sur **MVC**.

    ![][12]

6. Toujours dans la boîte de dialogue **Nouveau projet ASP.NET** , cliquez sur le bouton **Modifier l’authentification** . Dans la boîte de dialogue **Modifier l’authentification** , cliquez sur **Aucune authentification**, puis cliquez sur **OK**. Pour ce didacticiel, vous déployez une application qui n’a pas besoin de connexion de l’utilisateur.

    ![][16]

7. Dans la boîte de dialogue **Nouveau projet ASP.NET** , cliquez sur **OK** pour créer le projet.

6.  Dans l' **Explorateur de solutions**, dans le projet **FrontendWebRole** , cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.

7.  Cliquez sur l’onglet **Parcourir** , puis recherchez les `Microsoft Azure Service Bus`. Cliquez sur **installer**et accepter les conditions d’utilisation.

    ![][13]

    Notez que les assemblys du client requis sont désormais référencés et des nouveaux fichiers de code ont été ajoutées.

9.  Dans l' **Explorateur de solutions**, cliquez sur **modèles** , puis cliquez sur **Ajouter**, sur **une classe**. Dans la zone **nom** , tapez le nom **OnlineOrder.cs**. Puis cliquez sur **Ajouter**.

### <a name="write-the-code-for-your-web-role"></a>Écrire le code pour votre rôle web

Dans cette section, vous créez les diverses pages de votre application.

1.  Dans le fichier OnlineOrder.cs dans Visual Studio, remplacez la définition de l’espace de noms existant par le code suivant :

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  Dans l' **Explorateur de solutions**, double-cliquez sur **Controllers\HomeController.cs**. Ajoutez les instructions **using** suivantes en haut du fichier à inclure les espaces de noms pour le modèle que vous venez de créer, ainsi que les Bus de Service.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  Également dans le fichier HomeController.cs dans Visual Studio, remplacez la définition de l’espace de noms existant par le code suivant. Ce code contient des méthodes permettant de gérer la présentation des éléments de la file d’attente.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  Dans le menu **Générer** , cliquez sur **Générer la Solution** pour tester l’exactitude de votre travail jusqu'à présent.

5.  À présent, créer la vue de la `Submit()` méthode que vous avez créé précédemment. Avec le bouton droit dans le `Submit()` méthode (la surcharge de `Submit()` qui ne prend aucun paramètre), puis choisissez **Ajouter une vue**.

    ![][14]

6.  Une boîte de dialogue s’affiche pour la création de la vue. Dans la liste **modèle** , sélectionnez **créer**. Dans la liste de la **classe de modèle** , cliquez sur la classe **OnlineOrder** .

    ![][15]

7.  Cliquez sur **Ajouter**.

8.  Maintenant, modifiez le nom affiché de votre application. Dans l' **Explorateur de solutions**, double-cliquez sur le **Views\Shared\\_Layout.cshtml** fichier pour l’ouvrir dans l’éditeur de Visual Studio.

9.  Remplacez toutes les occurrences de **Mon Application ASP.NET** avec **les produits de LITWARE**.

10. Supprimer les liens ** **accueil**et **Contact** **. Supprimez le code en surbrillance :

    ![][28]

11. Enfin, modifiez la page de soumission afin d’inclure des informations sur la file d’attente. Dans l' **Explorateur de solutions**, double-cliquez sur le fichier **Views\Home\Submit.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio. Ajoutez la ligne suivante après `<h2>Submit</h2>`. Pour le moment, le `ViewBag.MessageCount` est vide. Vous remplirez il ultérieurement.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. Vous avez maintenant implémenté votre interface utilisateur. Vous pouvez appuyer sur **F5** pour exécuter votre application et vérifier qu’elle s’affiche comme prévu.

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Écrire le code pour l’envoi des éléments à une file d’attente de Bus de Service

Maintenant, ajoutez le code pour l’envoi des éléments à une file d’attente. Tout d’abord, vous créez une classe qui contient vos informations de connexion de file d’attente de Bus de Service. Ensuite, initialiser la connexion à partir de Global.aspx.cs. Enfin, à jour le code de soumission créé précédemment en HomeController.cs pour réellement soumettre des éléments à une file d’attente de Bus de Service.

1.  Dans l' **Explorateur de solutions**, double-cliquez sur **FrontendWebRole** (clic droit le projet, pas le rôle). Cliquez sur **Ajouter**, puis cliquez sur la **classe**.

2.  Nom de la classe **QueueConnector.cs**. Cliquez sur **Ajouter** pour créer la classe.

3.  Maintenant, ajoutez du code qui encapsule les informations de connexion et qui initialise la connexion à une file d’attente de Bus de Service. Remplacez tout le contenu de QueueConnector.cs par le code suivant et entrez les valeurs de `your Service Bus namespace` (votre nom d’espace de noms) et `yourKey`, qui est la **clé primaire** que vous avez précédemment obtenu à partir du portail Azure.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  Maintenant, vérifiez que votre méthode **Initialize** est appelée. Dans l' **Explorateur de solutions**, double-cliquez sur **Global.asax\Global.asax.cs**.

5.  Ajoutez la ligne de code suivante à la fin de la méthode **Application_Start** .

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Enfin, mettre à jour le code web que vous avez créé précédemment, pour soumettre des éléments dans la file d’attente. Dans l' **Explorateur de solutions**, double-cliquez sur **Controllers\HomeController.cs**.

7.  Mise à jour de la `Submit()` méthode (la surcharge qui ne prend aucun paramètre) comme suit pour obtenir le nombre de messages pour la file d’attente.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Mise à jour de la `Submit(OnlineOrder order)` méthode (la surcharge qui accepte un seul paramètre) comme suit pour envoyer des informations de commande à la file d’attente.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  Vous pouvez maintenant exécuter l’application à nouveau. Chaque fois que vous soumettez une commande, le nombre de messages augmente.

    ![][18]

## <a name="create-the-worker-role"></a>Créer le rôle de collaborateur

Vous allez maintenant créer le rôle de travail qui traite les envois de commande. Cet exemple utilise le modèle de projet de Visual Studio de **Rôle de travail avec la file d’attente de Bus de Service** . Vous vous êtes procuré déjà les informations d’identification requises à partir du portail.

1. Assurez-vous que vous êtes connecté à Visual Studio à votre compte Azure.

2.  Dans Visual Studio, dans **L’Explorateur de solutions** , cliquez sur le dossier **rôles** sous le projet **MultiTierApp** .

3.  Cliquez sur **Ajouter**, puis cliquez sur **Nouveau projet de rôle de travail**. La boîte de dialogue **Ajouter un nouveau projet de rôle** s’affiche.

    ![][26]

4.  Dans la boîte de dialogue **Ajouter un nouveau projet de rôle** , cliquez sur **Rôle de travail avec la file d’attente de Bus de Service**.

    ![][23]

5.  Dans la zone **nom** , nommez le projet **OrderProcessingRole**. Puis cliquez sur **Ajouter**.

6.  Copier la chaîne de connexion que vous avez obtenu à l’étape 9 de la section « Créer un espace de noms du Bus de Service » dans le Presse-papiers.

7.  Dans l' **Explorateur de solutions**, cliquez sur **OrderProcessingRole** que vous avez créé à l’étape 5 (Assurez-vous que vous cliquez sur **OrderProcessingRole** sous **rôles**et non de la classe). Puis cliquez sur **Propriétés**.

8.  Sous l’onglet **paramètres** de la boîte de dialogue **Propriétés** , cliquez dans la zone de **valeur** pour **Microsoft.ServiceBus.ConnectionString**, puis collez la valeur de point de terminaison que vous avez copié à l’étape 6.

    ![][25]

9.  Créer une classe **OnlineOrder** pour représenter les commandes comme vous les traitez à partir de la file d’attente. Vous pouvez réutiliser une classe que vous avez déjà créé. Dans l' **Explorateur de solutions**, cliquez droit sur la classe **OrderProcessingRole** (clic droit l’icône classe, pas le rôle). Cliquez sur **Ajouter**, puis cliquez sur **Élément existant**.

10. Recherchez le sous-dossier **FrontendWebRole\Models**, puis double-cliquez sur **OnlineOrder.cs** pour l’ajouter à ce projet.

11. Dans **WorkerRole.cs**, modifiez la valeur de la variable de **nom** de `"ProcessingQueue"` à `"OrdersQueue"` comme indiqué dans le code suivant.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Ajoutez l’instruction using en haut du fichier WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. Dans le `Run()` fonctionner, à l’intérieur la `OnMessage()` appeler, remplacez le contenu de la `try` clause avec le code suivant.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. Vous avez terminé l’application. Vous pouvez tester l’application intégrale par clic droit sur le projet MultiTierApp dans l’Explorateur de solutions et sélectionnez **définir comme projet de démarrage**puis en appuyant sur F5. Notez que le nombre de messages ne s’incrémente pas, car le rôle de travail traite les éléments à partir de la file d’attente et les marque comme étant terminée. Vous pouvez voir la sortie de traçage de votre rôle de collaborateur en affichant l’interface d’émulateur Azure Compute. Vous pouvez le faire en cliquant sur l’icône de l’émulateur dans la zone de notification de votre barre des tâches et en sélectionnant **Afficher l’interface utilisateur émulateur de calcul**.

    ![][19]

    ![][20]

## <a name="next-steps"></a>Étapes suivantes  

Pour en savoir plus sur le Bus de Service, consultez les ressources suivantes :  

* [Bus des services Azure][sbmsdn]  
* [Page de service de Bus des services][sbwacom]  
* [L’utilisation de files d’attente de Bus de Service][sbwacomqhowto]  

Pour en savoir plus sur les scénarios à plusieurs niveaux, voir :  

* [Application à plusieurs niveaux de .NET à l’aide des objets BLOB, les files d’attente et les Tables de stockage][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obtenir des outils et Kit de développement logiciel]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  