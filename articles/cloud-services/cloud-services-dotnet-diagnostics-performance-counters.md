<properties
   pageTitle="Utilisez les compteurs de Performance dans les Diagnostics de Windows Azure | Microsoft Azure"
   description="Utilisez les compteurs de performance dans les services en nuage Azure ou une machine virtuelle pour trouver les goulots d’étranglement et d’optimiser les performances."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Créer et utiliser des compteurs de performance dans une application Azure

Cet article décrit les avantages d’et comment placer des compteurs de performance dans votre application Azure. Vous pouvez les utiliser pour collecter des données, rechercher des goulots d’étranglement et régler les performances du système et des applications.

Compteurs de performance disponibles pour Windows Server, IIS et ASP.NET peuvent également être collectées et utilisées pour déterminer la santé de vos rôles web Azure, rôles worker et de Machines virtuelles. Vous pouvez également créer et utiliser des compteurs de performance personnalisés.  

Vous pouvez examiner les données de compteur de performance
1. Directement sur l’hôte d’application avec l’outil Analyseur de performances accédé à l’aide du Bureau à distance
2. System Center Operations Manager à l’aide du Pack d’administration Azure
3. Avec d’autres outils de surveillance qui accèdent aux données Diagnostics transféré vers le stockage Azure. Pour plus d’informations, reportez-vous à la section [banque et afficher les données de Diagnostic dans le stockage Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Pour plus d’informations sur l’analyse des performances de votre application dans [Azure portal classique](http://manage.azure.com/), consultez [le moniteur de Services Cloud](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Pour plus d’informations détaillées sur la création d’un enregistrement et le suivi de stratégie et à l’aide des tests de diagnostic et d’autres techniques pour résoudre les problèmes et optimiser les applications Azure, consultez [Dépannage des meilleures pratiques de développement d’Applications Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## <a name="enable-performance-counter-monitoring"></a>Activer l’analyse des compteurs de performances

Les compteurs de performance ne sont pas activés par défaut. Votre application ou une tâche de démarrage doit modifier la configuration de l’agent de diagnostic par défaut pour les compteurs de performance spécifiques que vous souhaitez surveiller pour chaque instance de rôle.

### <a name="performance-counters-available-for-microsoft-azure"></a>Compteurs de performance disponibles pour Microsoft Azure

Azure fournit un sous-ensemble des compteurs de performance disponibles pour Windows Server, IIS et la pile ASP.NET. Le tableau suivant répertorie certains des compteurs de performance d’un intérêt particulier pour les applications Azure.

|Catégorie de compteurs : Objet (Instance)|Nom du compteur      |Référence|
|---|---|---|
|Exceptions de CLR .NET (_Global_)|# Exceptions levées / s   |Compteurs de Performance d’exception|
|Mémoire CLR .NET (_Global_)    |% De temps dans le GC            |Compteurs de Performance de mémoire|
|ASP.NET                      |Redémarrages de l’application    |Compteurs de performance pour ASP.NET|
|ASP.NET                      |Temps d’exécution requête  |Compteurs de performance pour ASP.NET|
|ASP.NET                      |Demandes déconnectées   |Compteurs de performance pour ASP.NET|
|ASP.NET                      |Redémarrages processus de travail |Compteurs de performance pour ASP.NET|
|Applications ASP.NET (__Total__)|Total des demandes        |Compteurs de performance pour ASP.NET|
|Applications ASP.NET (__Total__)|Demandes/s          |Compteurs de performance pour ASP.NET|
|ASP.NET v4.0.30319           |Temps d’exécution requête  |Compteurs de performance pour ASP.NET|
|ASP.NET v4.0.30319           |Temps d’attente demande       |Compteurs de performance pour ASP.NET|
|ASP.NET v4.0.30319           |Demandes actuelles        |Compteurs de performance pour ASP.NET|
|ASP.NET v4.0.30319           |Demandes en attente         |Compteurs de performance pour ASP.NET|
|ASP.NET v4.0.30319           |Demandes rejetées       |Compteurs de performance pour ASP.NET|
|Mémoire                       |Mégaoctets disponibles        |Compteurs de Performance de mémoire|
|Mémoire                       |Octets validés         |Compteurs de Performance de mémoire|
|Processeur (_Total)            |% Temps processeur        |Compteurs de performance pour ASP.NET|
|TCPv4                        |Échecs de connexion     |Objet TCP|
|TCPv4                        |Connexions établies |Objet TCP|
|TCPv4                        |Réinitialisation des connexions       |Objet TCP|
|TCPv4                        |Segments envoyés/s       |Objet TCP|
|Réseau Interface(*)         |Octets reçus/s      |Objet d’Interface réseau|
|Réseau Interface(*)         |Octets envoyés/s          |Objet d’Interface réseau|
|Interface réseau (carte _2 du réseau Bus de Machine virtuelle Microsoft)|Octets reçus/s|Objet d’Interface réseau|
|Interface réseau (carte _2 du réseau Bus de Machine virtuelle Microsoft)|Octets envoyés/s|Objet d’Interface réseau|
|Interface réseau (carte _2 du réseau Bus de Machine virtuelle Microsoft)|Total des octets/s|Objet d’Interface réseau|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Créez et ajoutez des compteurs de performance personnalisés pour votre application

Azure a prise en charge pour créer et modifier des compteurs de performance personnalisés pour les rôles web et worker. Les compteurs peuvent être utilisés pour suivre et surveiller le comportement spécifique à l’application. Vous pouvez créer et supprimer des spécificateurs et des catégories de compteurs de performance personnalisés à partir d’une tâche au démarrage, rôle web ou de rôle de travail avec des autorisations élevées.

>[AZURE.NOTE] Code qui apporte des modifications aux compteurs de performance personnalisés doit avoir des autorisations élevées pour s’exécuter. Si le code est dans un rôle de web ou travailleur, le rôle doit inclure la balise <Runtime executionContext="elevated" /> dans le fichier ServiceDefinition.csdef pour le rôle à s’initialiser correctement.

Vous pouvez envoyer les données du compteur de performance personnalisé au stockage Azure à l’aide de l’agent de diagnostics.

Les données de compteur de performance standard sont générées par les processus d’Azure. Les données de compteur de performance personnalisé doivent être créées par votre application de rôle web rôle ou de travail. Pour plus d’informations sur les types de données qui peuvent être stockées dans des compteurs de performance personnalisés, consultez [Types de compteurs de performances](https://msdn.microsoft.com/library/z573042h.aspx) . Pour obtenir un exemple qui crée et définit les données du compteur de performance personnalisé dans un rôle web, reportez-vous à la section [Exemples de compteurs de performance](http://code.msdn.microsoft.com/azure/) .

## <a name="store-and-view-performance-counter-data"></a>Données de compteur de performance de banque et d’affichage

Azure met en cache les données de compteur de performance avec les autres informations de diagnostic. Ces données sont disponibles pour la surveillance à distance pendant l’exécution de l’instance de rôle à l’aide d’accès Bureau à distance pour afficher les outils de l’Analyseur de performances. Pour rendre persistantes les données en dehors de l’instance de rôle, l’agent de diagnostic doit transférer les données vers le stockage Azure. La limite de taille des données de compteur de performances de mise en cache peut être configurée dans l’agent de diagnostics, ou il peut être configuré pour faire partie d’une limite partagée pour toutes les données de Diagnostics. Pour plus d’informations sur la définition de la taille de la mémoire tampon, consultez [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) et [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Pour une vue d’ensemble de la configuration de l’agent de diagnostic pour transférer des données vers un compte de stockage, reportez-vous à la section [banque et afficher les données de Diagnostic dans le stockage Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .

Chaque instance de compteur de performance configurée est enregistrée à un taux d’échantillonnage spécifié et données échantillonnées sont transférées vers le compte de stockage par une demande de transfert planifiée ou d’une demande de transfert de la demande. Transferts automatiques peuvent être planifiées aussi souvent qu’une fois par minute. Les données de compteur de performance transférées par l’agent de diagnostic sont stockées dans une table, WADPerformanceCountersTable, dans le compte de stockage. Cette table peut être accessible et interrogée avec des méthodes de stockage Azure standard API. Voir des [Exemples de compteurs de performance Microsoft Azure](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) pour obtenir un exemple de l’interrogation et l’affichage des données de compteur de performance à partir de la table WADPerformanceCountersTable.

>[AZURE.NOTE] En fonction de la fréquence de transfert de l’agent de diagnostic et de la latence de la file d’attente, les dernières données de compteur de performance dans le compte de stockage peuvent être mis à jour de plusieurs minutes.

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Activer les compteurs de performance à l’aide du fichier de configuration de diagnostics

Utilisez la procédure suivante pour activer les compteurs de performances dans votre application Azure.

## <a name="prerequisites"></a>Conditions préalables

Cette section suppose que vous avez importées de l’écran de Diagnostics dans votre application et ajouter le fichier de configuration de diagnostics à votre solution Visual Studio (diagnostics.wadcfg dans le Kit de développement logiciel 2.4 et ci-dessous ou diagnostics.wadcfgx SDK 2.5 et plus). Voir les étapes 1 et 2 dans [L’activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](./cloud-services-dotnet-diagnostics.md)) pour plus d’informations.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Étape 1 : Collecter et stocker les données des compteurs de performance

Une fois que vous avez ajouté le fichier de diagnostic à votre solution Visual Studio, vous pouvez configurer la collecte et le stockage des données de compteur de performance dans une application Azure. Cela en ajoutant des compteurs de performance dans le fichier de diagnostic. Les données de diagnostic, y compris les compteurs de performance sont collectées tout d’abord sur l’instance. Les données sont ensuite rendue persistante dans la table WADPerformanceCountersTable dans le service de la Table d’Azure, vous devez également spécifier le compte de stockage dans votre application. Si vous testez votre application localement dans l’émulateur de calcul, vous pouvez également stocker localement les données de diagnostic dans l’émulateur de stockage. Avant de stocker des données de diagnostic vous devez accédez d’abord à [Azure portal classique](http://manage.windowsazure.com/) et créer un compte de stockage. Une meilleure solution consiste à localiser votre compte de stockage dans le même emplacement géographique que votre application Azure pour éviter de payer les coûts de bande passante externe et réduire la latence.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Ajouter des compteurs de performance dans le fichier de diagnostic

Il existe de nombreux compteurs, que vous pouvez utiliser. L’exemple suivant montre plusieurs compteurs de performance qui sont recommandés pour le web et le contrôle de rôle de travail.

Ouvrez le fichier de diagnostic (diagnostics.wadcfg dans le Kit de développement logiciel 2.4 et ci-dessous ou diagnostics.wadcfgx dans le SDK 2.5 et versions ultérieures) et ajoutez le code suivant à l’élément DiagnosticMonitorConfiguration :

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

L’attribut bufferQuotaInMB, qui spécifie la quantité maximale de stockage de système de fichiers qui est disponible pour le type de collection de données (journaux d’Azure, les journaux IIS, etc.). La valeur par défaut est 0. Lorsque le quota est atteint, les données plus anciennes sont supprimées que de nouvelles données sont ajoutées. La somme de toutes les propriétés de bufferQuotaInMB doit être supérieure à la valeur de l’attribut OverallQuotaInMB. Pour obtenir une description plus détaillée de la détermination de la quantité de stockage sera nécessaire pour la collecte de données de diagnostic, reportez-vous à la section Installation WAD de [Résolution des problèmes de meilleures pratiques pour le développement d’Applications Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

L’attribut scheduledTransferPeriod, qui spécifie l’intervalle entre les transferts de données planifiées, arrondie à la minute la plus proche. Dans les exemples suivants, il est défini à PT30M (30 minutes). La définition de la période de transfert à une petite valeur, par exemple 1 minute, impact négatif sur les performances de votre application en production, mais peut être utile pour travailler rapidement lorsque vous testez les diagnostics voir. La période de transfert planifiée doit être suffisamment petite pour s’assurer que les données de Diagnostics ne sont pas remplacées sur l’instance, mais assez grand pour qu’il aura aucun impact sur les performances de votre application.

L’attribut counterSpecifier Spécifie le compteur de performance à collecter. L’attribut sampleRate indique le taux auquel le compteur de performance doit être échantillonné, dans ce cas 30 secondes.

Une fois que vous avez ajouté les compteurs de performance que vous souhaitez collecter, enregistrer vos modifications dans le fichier de diagnostic. Ensuite, vous devez spécifier le compte de stockage que les données de diagnostic sont rendues persistantes dans.

### <a name="specify-the-storage-account"></a>Spécifiez le compte de stockage

Pour conserver vos informations de diagnostic à votre compte de stockage Azure, vous devez spécifier une chaîne de connexion dans votre fichier de configuration (ServiceConfiguration.cscfg) du service.

Pour Azure SDK 2.5, du compte de stockage peuvent être spécifié dans le fichier diagnostics.wadcfgx.

>[AZURE.NOTE] Ces instructions s’appliquent uniquement à Azure SDK 2.4 et en dessous. Pour Azure SDK 2.5, du compte de stockage peuvent être spécifié dans le fichier diagnostics.wadcfgx.

Pour définir les chaînes de connexion :

1. Ouvrez le fichier ServiceConfiguration.Cloud.cscfg à l’aide de votre éditeur de texte et la valeur de la chaîne de connexion pour le stockage. Les valeurs de *nom de compte* et *AccountKey* sont trouvent dans le portail Azure classique du tableau de bord de compte de stockage, sous gestion des clés.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Enregistrez le fichier ServiceConfiguration.Cloud.cscfg.

3. Ouvrez le fichier ServiceConfiguration.Local.cscfg et vérifiez que UseDevelopmentStorage est définie sur true.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Maintenant que les chaînes de connexion sont définies, votre application rend persistantes les données des tests de diagnostic à votre compte de stockage lors du déployée de votre application.
4. Enregistrez et générez votre projet, puis déployer votre application.

## <a name="step-2-optional-create-custom-performance-counters"></a>Étape 2 : Compteurs de performance personnalisés (facultatif) créer

Outre les compteurs de performance prédéfinis, vous pouvez ajouter vos propres compteurs de performance personnalisés pour surveiller les rôles web ou travailleur. Compteurs de performance personnalisés peuvent être utilisés pour suivre et surveiller le comportement spécifique à l’application et peuvent être créés ou supprimés dans une tâche au démarrage, rôle web ou de rôle de travail avec des autorisations élevées.

L’agent de diagnostics de Windows Azure actualise la configuration de compteur de performance à partir du fichier .wadcfg une minute après le démarrage.  Si vous créez des compteurs de performance personnalisés dans la méthode OnStart et vos tâches de démarrage prennent plus d’une minute pour exécuter, vos compteurs de performance personnalisés n’a été créées lorsque l’agent de Diagnostics d’Azure essaie de les charger.  Dans ce scénario, vous verrez que les tests de diagnostic Azure capture correctement toutes les données de diagnostic, à l’exception de vos compteurs de performance personnalisés.  Pour résoudre ce problème, créez les compteurs de performance dans une tâche de démarrage ou déplacer certaines de vos tâches de démarrage fonctionnent à la méthode OnStart après avoir créé les compteurs de performance.

Effectuez les étapes suivantes pour créer un compteur nommé « \MyCustomCounterCategory\MyButton1Counter » de performances personnalisé simple :

1. Ouvrez le fichier de définition de service (CSDEF) pour votre application.
2. Ajoutez l’élément de Runtime à le WebRole ou l’élément WorkerRole pour autoriser l’exécution avec des privilèges élevés :

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Enregistrez le fichier.
4. Ouvrez le fichier de diagnostic (diagnostics.wadcfg dans le Kit de développement logiciel 2.4 et ci-dessous ou diagnostics.wadcfgx dans le SDK 2.5 et versions ultérieures) et ajoutez le code suivant à la DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Enregistrez le fichier.
6. Créer la catégorie de compteur de performance personnalisé dans la méthode OnStart de votre rôle, avant d’appeler base. OnStart. L’exemple C# suivant crée une catégorie personnalisée, s’il n’existe pas déjà :

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Mettre à jour les compteurs de votre application. L’exemple suivant met à jour un compteur de performance personnalisé sur les événements Button1_Click :

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Enregistrez le fichier.  

Les données de compteur de performance personnalisé seront désormais collectées par le moniteur de diagnostics de Windows Azure.

## <a name="step-3-query-performance-counter-data"></a>Étape 3 : Interroger les données de compteur de performance

Une fois que votre application est déployée et l’écran de Diagnostics en cours d’exécution commence la collecte des compteurs de performance et de la persistance des données de stockage Azure. Vous utilisez des outils tels que l’Explorateur de serveurs dans Visual Studio, [Explorateur de stockage Azure](http://azurestorageexplorer.codeplex.com/)ou [Gestionnaire de tests de diagnostic d’Azure](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) par Cerebrata pour afficher les données de compteurs de performance dans la table WADPerformanceCountersTable. Vous pouvez également par programmation interroger le service de la Table à l’aide de [C#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md)ou [PHP](../storage/storage-php-how-to-use-table-storage.md).

L’exemple C# suivant illustre une requête simple sur la table WADPerformanceCountersTable et enregistre les données de diagnostic dans un fichier CSV. Une fois que les compteurs de performance sont enregistrés dans un fichier CSV, vous pouvez utiliser les fonctionnalités graphiques de Microsoft Excel ou un autre outil pour visualiser les données. Veillez à ajouter une référence à Microsoft.WindowsAzure.Storage.dll, ce qui est inclus dans le Kit de développement Azure pour .NET octobre 2012 et les versions ultérieures. L’assembly est installé dans le répertoire programme Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Mappage d’entités pour les objets C# à l’aide d’une classe personnalisée dérivée de **TableEntity**. Le code suivant définit une classe d’entité qui représente un compteur de performance dans la table **WADPerformanceCountersTable** .


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>Étapes suivantes
[Afficher des articles supplémentaires sur les tests de diagnostic Azure] (.. / azure-diagnostics.md)
