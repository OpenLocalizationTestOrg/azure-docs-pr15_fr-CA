<properties 
    pageTitle="L’activation des indicateurs de stockage dans le portail Azure | Microsoft Azure" 
    description="Comment faire pour activer les indicateurs de stockage pour les services de Blob, la file d’attente, Table et fichier" 
    services="storage" 
    documentationCenter="" 
    authors="robinsh" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/03/2016" 
    ms.author="robinsh"/>

# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>L’activation des indicateurs de stockage et l’affichage des données de métrique

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Vue d’ensemble

Par défaut, les mesures de stockage n’est pas activé pour les services de stockage. Vous pouvez activer la surveillance de l’utilisation du [Portail classique d’Azure](https://manage.windowsazure.com), Windows PowerShell, ou par programme par le biais d’une API de stockage.

Lorsque vous activez les indicateurs de stockage, vous devez choisir une période de rétention pour les données : cette période détermine combien de temps le stockage service conserve les mesures et les frais de l’espace nécessaire pour les stocker. En règle générale, vous devez utiliser une période de rétention plus courte pour les mesures minutes que mesures horaires en raison de l’espace supplémentaire important requis pour les mesures de minute. Vous devez choisir une période de rétention que vous avez suffisamment de temps pour analyser les données et télécharger des mesures que vous souhaitez conserver pour une analyse hors ligne ou des rapports. N’oubliez pas que vous seront également facturés pour le téléchargement des données de la métrique à partir de votre compte de stockage.

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Comment faire pour activer les indicateurs de stockage via le portail classique Azure

Dans le [Portail classique d’Azure](https://manage.windowsazure.com), vous utilisez la page Configurer pour un compte de stockage pour le contrôle des mesures de stockage. Pour une analyse, vous pouvez définir un niveau et une période de rétention en jours pour chacun des objets BLOB, les tables et les files d’attente. Dans chaque cas, le niveau est une des opérations suivantes :

- Désactivé : Aucune métrique n’est collectés.

- Minimal — Des indicateurs de stockage collecte un ensemble de mesures telles que les pourcentages de réussite, qui sont regroupés pour les services de Blob, Table et file d’attente, la disponibilité, la latence et ENTREE/sortie de base.

- Verbose : Indicateurs de stockage collecte un ensemble complet de mesures qui inclut les mêmes mesures pour chaque opération de l’API, outre les mesures de niveau de service de stockage. Mesures détaillées permettent une analyse plus approfondie des problèmes qui se produisent pendant des opérations de l’application.

Notez que le portail classique Azure ne pas actuellement active vous permet de configurer les mesures de minute dans votre compte de stockage ; Vous devez activer les mesures minutes à l’aide de PowerShell ou par programme.


## <a name="how-to-enable-storage-metrics-using-powershell"></a>Comment faire pour activer les indicateurs de stockage à l’aide de PowerShell

Vous pouvez utiliser PowerShell sur votre ordinateur local pour configurer les mesures de stockage dans votre compte de stockage à l’aide de l’applet de commande PowerShell d’Azure, Get-AzureStorageServiceMetricsProperty pour extraire les paramètres en cours et l’applet de commande Set-AzureStorageServiceMetricsProperty pour modifier les paramètres en cours.

Les applets de commande qui contrôlent les indicateurs de stockage utilisent les paramètres suivants :

- MetricsType les valeurs possibles sont les heures et les minutes.

- ServiceType les valeurs possibles sont le Blob et Table file d’attente.

- MetricsLevel les valeurs possibles sont None (équivalent à Off dans Azure Portal classique), Service (équivalent au minimum dans le portail classique Azure) et ServiceAndApi (équivalent à Verbose dans Azure Portal classique).

Par exemple, la commande suivante bascule des métriques de minute pour le service d’objet blob dans votre compte de stockage par défaut avec la période de rétention définie à cinq jours :

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

La commande suivante extrait les en cours horaire métriques niveau et rétention jours pour le service d’objet blob dans votre compte de stockage par défaut :

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Pour plus d’informations sur la façon de configurer les applets de commande PowerShell de Azure pour travailler avec votre abonnement Azure et comment sélectionner le compte de stockage par défaut à utiliser, reportez-vous à la section : [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Comment faire pour activer les indicateurs de stockage par programme

L’extrait de code C# suivant montre comment activer les statistiques et enregistrement pour le service d’objet Blob à l’aide de la bibliothèque de client de stockage pour .NET :

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);
    
## <a name="viewing-storage-metrics"></a>Affichage des indicateurs de stockage

Lorsque vous avez configuré les mesures de stockage pour contrôler votre compte de stockage, il enregistre les mesures dans un ensemble de tables connus dans votre compte de stockage. Vous pouvez utiliser la page Moniteur pour votre compte de stockage dans le portail classique Azure pour afficher les mesures horaires dès qu’elles sont disponibles sur un graphique. Sur cette page de portail Azure classique, vous pouvez :

- Permet de sélectionner les mesures à tracer dans le graphique (le choix des mesures disponibles dépend de si vous avez choisi de surveillance détaillée ou un minimum pour le service sur la page Configurer).


- Sélectionnez la plage de temps pour les mesures affichées sur le graphique.


- Choisir d’utiliser une échelle absolue ou relative à tracer les métriques.


- Configurer des alertes par courrier électronique pour vous avertir lorsqu’une mesure spécifique atteint une certaine valeur.


Si vous souhaitez télécharger les métriques de stockage à long terme ou les analyser localement, vous devez utiliser un outil ou d’écrire du code pour la lecture des tables. Vous devez télécharger les métriques de minute pour l’analyse. Les tableaux n’apparaissent pas si la liste de toutes les tables dans votre compte de stockage, mais vous pouvez y accéder directement par nom. De nombreux outils de navigation de stockage tiers sont conscients de ces tables et vous permettre de les afficher directement (consultez le blog [Microsoft Azure stockage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) pour obtenir la liste des outils disponibles).

### <a name="hourly-metrics"></a>Mesures de l’horaires
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Mesures de minute
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacité
- $MetricsCapacityBlob

Vous trouverez tous les détails des schémas pour ces tables au [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/azure/hh343264.aspx). Les exemples de lignes ci-dessous afficher uniquement un sous-ensemble des colonnes disponibles, mais ils illustrent quelques-unes des fonctionnalités importantes de la manière dont les mesures de stockage enregistre ces mesures :

| PartitionKey  |       RowKey       |                    Horodatage | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilité | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      utilisateur ; Tous les      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | utilisateur ; QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7.8                  | 100            |
| 20140522T1100 |  utilisateur ; QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | utilisateur ; UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

Dans cet exemple de données métriques minute, clé de partition utilise l’heure à une résolution minute. La clé de ligne identifie le type d’informations qui sont stockées dans la ligne et il est composé de deux éléments d’information, le type d’accès et le type de demande :

- Le type d’accès est utilisateur ou système, où utilisateur fait référence à toutes les demandes de l’utilisateur au service de stockage, et système pour les demandes effectuées par stockage Analytique.

- Le type de demande est tout auquel cas il est une ligne récapitulative ou il identifie l’API spécifique, tels que QueryEntity ou UpdateEntity.


Les données de l’exemple ci-dessus affiche tous les enregistrements d’une seule minute (commençant à 11:00 PST), afin que le nombre de demandes de QueryEntities ainsi que le nombre de QueryEntity demande plus le nombre de UpdateEntity demande ajouter jusqu'à sept, qui est le total indiqué sur la ligne de l’utilisateur : tous les. De même, vous pouvez dériver la latence moyenne bout 104.4286 sur la ligne de l’utilisateur : tout en calculant ((143.8 * 5) + 3 + 9) / 7.

Vous devez envisager de définir les alertes dans le portail classique d’Azure dans la page Moniteur afin que des mesures de stockage peut automatiquement vous informer de toute modification importante dans le comportement de vos services de stockage. Si vous utilisez un outil Explorateur de stockage pour télécharger des données métriques dans un format délimité, vous pouvez utiliser Microsoft Excel pour analyser les données. Consultez le blog [Microsoft Azure stockage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) pour obtenir la liste des outils de l’Explorateur de stockage disponible.



## <a name="accessing-metrics-data-programmatically"></a>Accès par programme aux données de la métrique

La liste suivante répertorie des exemples C# code qui accède à la minute métrique pour une plage de minutes et affiche les résultats dans une fenêtre de console. Il utilise la bibliothèque de stockage Azure version 4 qui inclut la classe CloudAnalyticsClient qui simplifie l’accès aux tables métriques de stockage.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Frais vous subissez lorsque vous activez les indicateurs de stockage ?

Écrire des requêtes pour créer des entités de table pour les mesures sont payées au taux standard applicable à toutes les opérations de stockage Azure.

Les demandes de lecture et suppression par un client pour les données de métrique sont également facturer au taux standard. Si vous avez configuré une stratégie de rétention de données, vous ne sont pas facturés lorsque le stockage Azure supprime les anciennes données de mesures. Toutefois, si vous supprimez les données d’analytique, votre compte est facturé pour les opérations de suppression.

La capacité utilisée par les tables de mesures est également facturable : vous pouvez utiliser les éléments suivants pour estimer la quantité de capacité utilisée pour stocker des données de mesures :

- Si chaque heure un service utilise chaque API dans chaque service, puis environ 148 Ko de données sont stockée toutes les heures dans les tables de transactions de mesures si vous avez activé le service et niveau API résumé.

- Si chaque heure un service utilise chaque API dans chaque service, puis environ 12 Ko de données sont stockée toutes les heures dans les tables de transactions de mesures si vous avez activé uniquement les niveaux de service résumé.

- La table de capacité pour les objets BLOB a deux lignes ajoutées chaque jour (si l’utilisateur a choisi de journaux) : cela signifie que chaque jour la taille du tableau augmente par environ 300 octets.

## <a name="next-steps"></a>Étapes suivantes :
[L’activation de stockage Analytique de journalisation et l’accès aux données du journal](https://msdn.microsoft.com/library/dn782840.aspx)
 