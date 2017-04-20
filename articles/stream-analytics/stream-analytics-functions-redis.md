<properties
    pageTitle="La sortie vers un Cache Azure Redis, à l’aide de fonctions d’Azure, d’Analytique de flux Azure | Microsoft Azure"
    description="Découvrez comment utiliser une fonction d’Azure connecté à une file d’attente du Bus de Service, pour remplir un Cache Azure de Redis à partir de la sortie d’une tâche de flux de données Analytique."
    keywords="flux de données, redis, cache, file d’attente de bus de service"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Comment stocker les données d’Analytique de flux Azure dans un Cache de Redis Azure à l’aide des fonctions d’Azure

Azure Analytique de flux vous permet de rapidement développer et déployer des solutions économiques pour obtenir des informations en temps réel à partir de périphériques, capteurs, infrastructure et les applications ou les flux de données. Elle permet à différents cas d’usage telles que la gestion en temps réel et commande et de contrôle, la détection des fraudes, voitures connectées et bien d’autres encore. Dans de nombreux scénarios de ce type, vous souhaitez peut-être stocker les données imprimées par Azure flux Analytique dans une banque de données distribuées comme un cache Azure Redis.

Supposons que vous faites partie d’une société de télécommunications. Vous essayez de détecter une fraude de carte SIM où plusieurs appels provenant de la même identité, en même temps, mais dans différents géographiquement emplacements. Vous êtes chargé de stocker tous les frauduleux appels téléphoniques potentiels dans un cache Azure Redis. Dans ce blog, nous fournissent des conseils sur la façon dont vous pouvez facilement mettre votre tâche. 

## <a name="prerequisites"></a>Conditions préalables
La [Détection des fraudes en temps réel] [ fraud-detection] étape par étape pour ASA

## <a name="architecture-overview"></a>Présentation de l’architecture
![Capture d’écran d’architecture](./media/stream-analytics-functions-redis/architecture-overview.png)

Comme illustré dans la figure précédente, Analytique de flux permet d’entrée de données pour être interrogé et envoyé à une sortie de diffusion en continu. En fonction de la sortie, Azure fonctions peut déclencher ensuite un type d’événement. 

Dans ce blog, nous concentrer sur la partie de fonctions d’Azure de ce pipeline, ou plus précisément le déclenchement d’un événement qui stocke les données frauduleuses dans le cache.
La fin de la [Détection des fraudes en temps réel] [ fraud-detection] (didacticiel), que vous avez entrée (un concentrateur d’événements), une requête et une sortie (stockage blob) déjà configuré et exécuté. Dans ce blog, nous avons modifier la sortie pour utiliser une file d’attente de Bus de Service à la place. Après cela, nous nous connecter une fonction Azure à cette file d’attente. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Créer et connecter une sortie de la file d’attente de Bus de Service
Pour créer une file d’attente de Bus de Service, suivez les étapes 1 et 2 de la section [Mise en route de files d’attente de Bus de Service].NET[servicebus-getstarted].
Maintenant nous allons connecter la file d’attente à la tâche flux Analytique qui a été créée dans la procédure de détection de fraude antérieure.



1. Dans le portail Azure, accédez à la lame de **sorties** de votre travail et sélectionnez **Ajouter** en haut de la page.

    ![Ajout de sorties](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Cliquez sur **File d’attente de Bus de Service** comme le **récepteur** et suivez les instructions à l’écran. Veillez à choisir l’espace de noms de la file d’attente de Bus de Service créé à la [Mise en route de files d’attente de Bus de Service][servicebus-getstarted]. Lorsque vous avez terminé, cliquez sur le bouton « droit ».
3. Spécifiez les valeurs suivantes :
    - **Format d’événement sérialiseur**: JSON
    - **Codage**: UTF8
    - **FORMAT**: ligne séparée

4. Cliquez sur le bouton **créer** pour ajouter cette source et de vérifier que flux Analytique peut se connecter avec succès pour le compte de stockage.

5. Dans l’onglet **requête** , remplacez la requête en cours par le suivant : Remplacez *[Votre nom BUS DE SERVICE]* par le nom de sortie que vous avez créé à l’étape 3. 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>Créer un Cache Azure Redis
Créer un cache Azure Redis en suivant la section .NET dans [la Cache de Redis utilisation Azure] [ use-rediscache] jusqu'à ce que la section appelée ***configurer les clients de cache***.
Une fois terminé, vous avez un nouveau Cache de Redis. Sous **tous les paramètres**, sélectionnez les **touches d’accès rapide** et la note en bas de la ***chaîne de connexion principal***.

![Capture d’écran d’architecture](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Créer une fonction d’Azure
Suivez la [Création de votre première fonction Azure] [ functions-getstarted] didacticiel mise en route avec les fonctions d’Azure. Si vous avez déjà une fonction Azure que vous souhaitez utiliser, puis passez directement à [l’écriture dans le Cache de Redis](#Writing-to-Redis-Cache)

1. Dans le portail, sélectionnez les Services d’application à partir de la navigation de gauche, puis cliquez sur le nom de votre application Azure fonction pour accéder au site Web de la fonction app.
    ![Capture d’écran de la liste de services des applications à l’aide de la fonction](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Cliquez sur **nouvelle fonction > ServiceBusQueueTrigger – C#**. Pour les champs suivants, suivez ces instructions :
    - **Nom de la file d’attente**: le même nom que le nom que vous avez entré lorsque vous avez créé la file d’attente dans [Mise en route de files d’attente de Bus de Service] [ servicebus-getstarted] (pas le nom du bus de service). Assurez-vous que vous utilisez la file d’attente qui est connecté à la sortie de flux Analytique.
    - **Connexions de Bus de service**: sélectionnez **Ajouter une chaîne de connexion**. Pour rechercher la chaîne de connexion, accédez au portail classique, sélectionnez **Service de Bus**, le bus de service que vous avez créé et **Informations de connexion** en bas de l’écran. Assurez-vous que vous êtes sur l’écran principal de cette page. Copiez et collez la chaîne de connexion. N’hésitez pas à entrer un nom de connexion.
    
        ![Capture d’écran de connexion au service de bus](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**: choisissez **Gérer**


3. Cliquez sur **créer**

## <a name="writing-to-redis-cache"></a>Écriture dans le Cache de Redis
Nous avons maintenant créé une fonction Azure qui lit à partir d’une file d’attente de Bus de Service. Tout ce qui reste à faire est notre fonction permet d’écrire des données dans le Cache de Redis. 

1. Sélectionnez votre **ServiceBusQueueTrigger**de nouvellement créé et cliquez sur **paramètres d’application de fonction** dans le coin supérieur droit. Sélectionnez **dans les paramètres de Service application > Paramètres > Paramètres de l’Application**

2. Dans la section de chaînes de connexion, créez un nom dans la section **nom** . Collez la chaîne de connexion principale que vous avez trouvé à l’étape **créer un Cache de Redis** dans la section **valeur** . Sélectionnez **personnalisé** dans la zone **De la base de données SQL**.

3. Cliquez sur **Enregistrer** en haut.

    ![Capture d’écran de connexion au service de bus](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Revenez aux paramètres du Service d’application et sélectionnez **Outils > Éditeur de Service d’application (aperçu) > sur > Atteindre**.

    ![Capture d’écran de connexion au service de bus](./media/stream-analytics-functions-redis/app-service-editor.png)

5. Dans un éditeur de votre choix, créez un fichier JSON nommé **project.json** avec le texte suivant et enregistrez-le sur votre disque local.

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. Télécharger ce fichier dans le répertoire racine de votre fonction (pas WWWROOT). Vous devriez voir un fichier nommé **project.lock.json** automatiquement s’affiche, confirmant que le Nuget packages « StackExchange.Redis » et « Newtonsoft.Json » n’ont pas été importés.

7. Dans le fichier **run.csx** , remplacez le code préalablement généré par le code suivant. Dans la fonction lazyConnection, remplacez « Nom CONN » avec le nom que vous avez créé à l’étape 2 de **stocker des données dans le cache de Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Démarrer la tâche de flux de données Analytique

1. Démarrez l’application telcodatagen.exe. L’utilisation est la suivante :````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. À partir de la blade de travail Analytique de flux dans le portail, cliquez sur **Démarrer** dans la partie supérieure de la page.

    ![Capture d’écran de tâche de début](./media/stream-analytics-functions-redis/starting-job.png)

3. De la lame de **Démarrer le travail** qui s’affiche, sélectionnez **maintenant** et puis cliquez sur le bouton **Démarrer** en bas de l’écran. Changements d’état de tâche pour le début et après des modifications de l’heure en cours d’exécution.
 
    ![Capture d’écran de sélection de temps de travail de début](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Exécutez la solution et vérifier les résultats
Pour revenir à la page **ServiceBusQueueTrigger** , vous devez maintenant voir instructions log. Ces journaux indiquent que vous vous avez quelque chose à partir de la file d’attente Bus Service, placer dans la base de données et il extrait à l’aide de l’heure comme clé !

Pour vérifier que vos données se trouvent dans votre cache de Redis, accédez à la page de cache de Redis dans le nouveau portail (comme indiqué dans l’étape précédente de [créer un Cache de Redis Azure](#Create-an-Azure-Redis-Cache) ), puis sélectionnez Console.

Vous pouvez à présent écrire les commandes Redis pour confirmer que les données sont en fait dans le cache.

![Capture d’écran de la Console de le Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Étapes suivantes
Nous sommes impatients des choses de nouvelles fonctions d’Azure et analytique de flux peut faire ensemble, et nous espérons que cette opération déverrouille des possibilités nouvelles pour vous. Si vous avez des commentaires sur ce que vous souhaitez ensuite, n’hésitez pas à utiliser le [site de UserVoice d’Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Si vous êtes nouveau Microsoft Azure, nous vous invitons à essayer en vous inscrivant pour un [compte d’essai Azure gratuit](https://azure.microsoft.com/pricing/free-trial/). Si vous ne connaissez pas Analytique de flux, puis nous vous invitons à [créer votre première tâche Analytique de flux de données](stream-analytics-create-a-job.md).

Si vous avez besoin de toute aide ou avez des questions, valider sur les forums [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) ou [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) . 

Vous pouvez également voir les ressources suivantes :

- [Référence du développeur de fonctions Azure](../azure-functions/functions-reference.md)
- [Référence du développeur Azure fonctions C#](../azure-functions/functions-reference-csharp.md)
- [Référence du développeur Azure fonctions F#](../azure-functions/functions-reference-fsharp.md)
- [Référence du développeur de fonctions NodeJS Azure](../azure-functions/functions-reference.md)
- [Les liaisons et les déclencheurs de fonctions azure](../azure-functions/functions-triggers-bindings.md)
- [Comment faire pour surveiller le Cache Redis d’Azure](../redis-cache/cache-how-to-monitor.md)

Pour rester à jour sur toutes les dernières actualités et fonctionnalités, suivez [@AzureStreaming](https://twitter.com/AzureStreaming) sur Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md
