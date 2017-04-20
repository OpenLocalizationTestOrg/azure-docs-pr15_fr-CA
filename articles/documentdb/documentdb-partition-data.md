<properties 
    pageTitle="Le partitionnement et la mise à l’échelle dans Azure DocumentDB | Microsoft Azure"      
    description="Obtenir des informations sur le partitionnement dans Azure DocumentDB, comment faire pour configurer le partitionnement et partitionner des clés et comment choisir la clé de partition droite pour votre application."         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Le partitionnement et la mise à l’échelle dans Azure DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est conçu pour vous aider à atteindre les performances rapides et prévisibles et une évolutivité en toute transparence avec votre application qu’elle se développe. Cet article fournit une vue d’ensemble du partitionnement dans DocumentDB et décrit comment vous pouvez configurer les collections de DocumentDB pour effectivement faire évoluer vos applications.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :   

- Fonctionnement de partitionnement dans Azure DocumentDB
- Comment configurer le partitionnement dans DocumentDB
- Quelles sont les clés de partition, et comment choisir la clé de partition droite pour mon application ?

Pour vous familiariser avec le code, téléchargez le projet à partir de [l’Exemple pilote de test de performances de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

## <a name="partitioning-in-documentdb"></a>Le partitionnement dans DocumentDB

Dans DocumentDB, vous pouvez stocker et interroger des documents JSON sans schéma avec des temps de réponse de la commande-de-milliseconde à n’importe quelle échelle. DocumentDB fournit des conteneurs de stockage des données, appelées des **collections**. Les collections sont des ressources logiques et peuvent s’étendre sur un ou plusieurs serveurs ou plusieurs partitions. Le nombre de partitions est déterminé par la DocumentDB en fonction de la taille de stockage et le débit de mise en service de la collection. Chaque partition dans DocumentDB possède une quantité fixe de stockage SSD de secours associé et est répliquée pour une haute disponibilité. Gestion des partitions est entièrement gérée par DocumentDB d’Azure, et vous n’avez pas à écrire du code complexe ou gérer vos partitions. Les collections de DocumentDB sont **pratiquement illimitées** en termes de stockage et de débit. 

Le partitionnement est totalement transparent pour votre application. DocumentDB prend en charge les lectures rapides et écrit des requêtes SQL et LINQ, JavaScript en fonction logique transactionnelle, niveaux de cohérence et contrôle d’accès précis via des appels de l’API REST à une ressource de collection unique. Le service gère les données de distribution sur plusieurs partitions et routage de requêtes vers la partition de droite. 

Comment cela fonctionne-t-il ? Lorsque vous créez une collection de DocumentDB, vous remarquerez qu’il existe une valeur de configuration de **propriété de clé de partition** que vous spécifiez. C’est la propriété JSON (ou chemin) dans vos documents peuvent utiliser DocumentDB pour distribuer les données entre plusieurs serveurs ou des partitions. DocumentDB est la valeur de clé de partition de hachage et le résultat du hachage permet de déterminer la partition dans laquelle le document JSON sera stocké. Tous les documents avec la même clé de partition sont stockés dans la même partition. 

Par exemple, prenez une application qui stocke les données sur les employés et leurs services dans DocumentDB. Nous allons choisir `"department"` en tant que la propriété de clé de partition pour les données par service. Chaque document de DocumentDB doit contenir un obligatoire `"id"` propriété qui doit être unique pour tous les documents ayant la même valeur de clé partition, par exemple, `"Marketing`». Tous les documents stockés dans une collection doivent avoir une combinaison unique de la clé de partition et les id, par exemple, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }`, et `{ "Department": "Sales", "id": "0001" }`. En d’autres termes, la propriété composée de (id de clé, partition) est la clé primaire de votre collection.

### <a name="partition-keys"></a>Clés de partition
Le choix de la clé de partition est une décision importante que vous devrez effectuer au moment du design. Vous devez choisir un nom de propriété JSON qui a un large éventail de valeurs et qui est susceptible d’avoir réparti uniformément les modèles d’accès. Clé de partition est spécifiée comme un chemin d’accès JSON, par exemple, `/department` représente le département de la propriété. 

Le tableau suivant montre des exemples de définitions de clé de partition et les valeurs JSON correspondant à chacun.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Chemin d’accès de clé de partition</strong></p></td>
            <td valign="top"><p><strong>Description</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.department où le document est le document.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ Propriétés (nom)</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.properties.name où le document est le document (propriété imbriquée).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.id (clé de partition et les id sont de la même propriété).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ « nom du service »</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc [« nom du service »], où le document est le document.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] La syntaxe de chemin d’accès de clé de partition est similaire à la spécification du chemin d’accès pour l’indexation des chemins d’accès de la stratégie à la différence de clés que le chemin d’accès correspond à la propriété au lieu de la valeur, il n’y a aucun caractère générique à la fin. Par exemple, vous devez spécifier/département / ? pour indexer les valeurs sous service, mais spécifiez /department comme la définition de clé de partition. Le chemin d’accès de clé de partition est implicitement indexé et ne peut pas être exclue de l’indexation à l’aide des remplacements de la stratégie d’indexation.

Examinons comment le choix de la clé de partition a un impact sur les performances de votre application.

### <a name="partitioning-and-provisioned-throughput"></a>Débit de partitionnement et mis en service
DocumentDB est conçu pour des performances prévisibles. Lorsque vous créez une collection, vous réservez le débit en termes d' ** [unités de la demande](documentdb-request-units.md) (RU) par seconde**. Chaque demande est affectée à un prix unitaire de demande qui est proportionnelle à la quantité de ressources système telles qu’UC et d’e/s consommées par l’opération. Lecture d’un document de 1 Ko avec cohérence multisession consomme 1 unité de demande. Une lecture est 1 RU, quel que soit le nombre d’éléments stockés ou le nombre de demandes simultanées en cours d’exécution simultanément. Documents volumineux requièrent des unités de demande plus élevées en fonction de la taille. Si vous connaissez la taille de vos entités et le nombre de lectures, que vous avez besoin pour prendre en charge pour votre application, vous pouvez prévoir exactement le montant du débit requis pour les besoins de lecture de votre application. 

Lorsque DocumentDB stocke les documents, il les distribue uniformément entre les partitions en fonction de la valeur de clé de partition. Le débit est également réparti uniformément sur les partitions c'est-à-dire le débit par partition = (débit total par collection) / (nombre de partitions). 

>[AZURE.NOTE] Pour atteindre le débit total de la collection, vous devez choisir une clé de partition qui vous permet de répartir équitablement les demandes parmi un certain nombre de valeurs de clé de partition distincte.

## <a name="single-partition-and-partitioned-collections"></a>Partition unique et Collections partitionnées
DocumentDB prend en charge la création de collections de partition unique et partitionnées. 

- **Les collections de partitionnées** peuvent couvrir plusieurs partitions et prise en charge de très grandes quantités de stockage et de débit. Vous devez spécifier une clé de partition pour la collection.
- **Partition unique collections** ont des options de prix inférieures et la possibilité d’interroger et d’effectuer des transactions de toutes les données de la collection. Ils ont les limites de l’évolutivité et le stockage d’une seule partition. Vous n’êtes pas obligé de spécifier une clé de partition pour ces collections. 

![Collections partitionnées dans DocumentDB][2] 

Pour les scénarios qui n’ont pas besoin de gros volumes de stockage ou de débit, les collections de partition unique sont parfaitement. Notez que les collections de partition unique ont l’évolutivité et les limites de stockage d’une seule partition, c'est-à-dire jusqu'à 10 Go de stockage et jusqu'à 10 000 unités de demande par seconde. 

Collections partitionnées peuvent prendre en charge de très grandes quantités de stockage et de débit. Toutefois, les offres par défaut configurés pour stocker jusqu'à 250 Go de stockage et de mise à l’échelle jusqu'à 250 000 unités de demande par seconde. Si vous avez besoin de plus de stockage ou de débit par collection, veuillez contacter le [Support d’Azure](documentdb-increase-limits.md) pour que ces accrue pour votre compte.

Le tableau suivant répertorie les différences de travailler avec une seule partition et collections partitionnées :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Collection de Partition unique</strong></p></td>
            <td valign="top"><p><strong>Collection partitionnée</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clé de partition</p></td>
            <td valign="top"><p>Aucun</p></td>
            <td valign="top"><p>Obligatoire</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clé primaire pour Document</p></td>
            <td valign="top"><p>« id »</p></td>
            <td valign="top"><p>clé composée &lt;clé de partition&gt; et « id »</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Taille de stockage minimum</p></td>
            <td valign="top"><p>0 GO</p></td>
            <td valign="top"><p>0 GO</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Stockage maximal</p></td>
            <td valign="top"><p>10 GO</p></td>
            <td valign="top"><p>Illimité (250 Go par défaut)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit minimal</p></td>
            <td valign="top"><p>400 unités de demande par seconde</p></td>
            <td valign="top"><p>10 000 unités de demande par seconde</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit maximal</p></td>
            <td valign="top"><p>10 000 unités de demande par seconde</p></td>
            <td valign="top"><p>Illimité (unités de 250 000 demande par seconde par défaut)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versions de l’API</p></td>
            <td valign="top"><p>Tous les</p></td>
            <td valign="top"><p>16-12-API 2015 et ultérieures</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Utilisation des kits de développement logiciel

Azure DocumentDB désormais en charge le partitionnement automatique avec [L’API REST version 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). Pour créer des collections partitionnées, vous devez télécharger les versions du Kit de développement logiciel 1.6.0 ou plus récente de l’une des plates-formes prises en charge du Kit de développement logiciel (.NET, Node.js, Java, Python). 

### <a name="creating-partitioned-collections"></a>Création de collections partitionnées

L’exemple suivant montre un extrait de code .NET pour créer une collection pour stocker des données de télémétrie de dispositif de 20 000 unités de demande par seconde de débit. Le Kit de développement définit la valeur de OfferThroughput (qui à son tour définit les `x-ms-offer-throughput` en-tête de demande dans l’API REST). Ici, nous définissons le `/deviceId` en tant que clé de partition. Le choix de la clé de partition est enregistré avec le reste des métadonnées telles que nom et stratégie d’indexation de collection.

Pour cet exemple, nous avons choisi `deviceId` car nous savons que (a) dans la mesure où il existe un grand nombre de périphériques, écritures peuvent être distribués uniformément entre les partitions et permet de mettre la base de données pour l’acquisition de volumes importants de données à l’échelle et (b) la plupart des demandes telles que l’extraction de la dernière lecture pour un dispositif ont une portée limitée à un seul ID de périphérique et peuvent être récupérées à partir d’une seule partition.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Pour créer des collections partitionnées, vous devez spécifier une valeur de débit d’unités de demande > 10 000 par seconde. Dans la mesure où le débit est en multiples de 100, ce fait est à 10,100 ou une version ultérieure.

Cette méthode appelle une API REST à DocumentDB, et le service met en service un certain nombre de partitions en fonction du débit requis. Vous pouvez modifier le débit d’une collection comme les performances de vos besoins évoluent. Pour plus d’informations, reportez-vous à la section [Niveaux de performances](documentdb-performance-levels.md) .

### <a name="reading-and-writing-documents"></a>Lire et écrire des documents

Maintenant, nous allons insérer des données dans DocumentDB. Voici un exemple de classe contenant un dispositif de lecture et un appel à CreateDocumentAsync pour insérer un nouveau périphérique de lecture dans une collection.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Nous allons lire le document par sa clé de partition et les id, mettre à jour et en tant que dernière étape, supprimez-le par id et la clé de partition. Notez que le message inclut une valeur PartitionKey (correspondant à la `x-ms-documentdb-partitionkey` en-tête de demande dans l’API REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Interroger des collections partitionnées

Lorsque vous interrogez des données dans des collections partitionnées, DocumentDB automatiquement l’achemine vers les partitions correspondant aux valeurs de clé de partition spécifiés dans le filtre (le cas échéant). Par exemple, cette requête est routée à simplement la partition contenant la clé de partition « XMS-0001 ».

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

La requête suivante n’a pas de filtre sur la clé de partition (DeviceId) et est serveur à toutes les partitions dans lequel elle est exécutée sur les index de la partition. Notez que vous devez spécifier la EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` dans l’API REST) pour que le Kit de développement logiciel pour exécuter une requête sur les partitions.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Exécution de requêtes en parallèle

Les kits de développement de DocumentDB 1.9.0 et au-dessus des options de l’exécution parallèle de la requête de prise en charge, qui vous permettent d’exécuter des requêtes de faible temps de latence sur collections partitionnées, même lorsqu’ils ont besoin de toucher un grand nombre de partitions. Par exemple, la requête suivante est configurée pour s’exécuter en parallèle sur plusieurs partitions.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Vous pouvez gérer l’exécution de requêtes en parallèle en réglant les paramètres suivants :

- En définissant `MaxDegreeOfParallelism`, vous pouvez contrôler le degré de parallélisme, c'est-à-dire le nombre maximal de connexions simultanées pour les partitions de la collection. Si vous affectez la valeur-1, le degré de parallélisme est géré par le SDK. Si le `MaxDegreeOfParallelism` n’est pas spécifiée ou définie à 0, qui est la valeur par défaut, il y aura une seule connexion réseau pour les partitions de la collection.
- En définissant `MaxBufferedItemCount`, vous pouvez adapter l’utilisation de mémoire côté client et la latence des requête. Si vous omettez ce paramètre ou que vous affectez la valeur-1, le nombre d’éléments mis en mémoire tampon pendant l’exécution de requêtes en parallèle est géré par le Kit de développement logiciel.

Compte tenu de l’état de la collection, une requête parallèle renvoie des résultats dans le même ordre que dans l’exécution de série. Lors de l’exécution d’une requête entre partitions qui comporte le tri (ORDER BY et/ou en haut), le SDK DocumentDB émet la requête en parallèle sur plusieurs partitions et fusionne les résultats partiellement triés sur le côté client pour produire des résultats globalement classés.

### <a name="executing-stored-procedures"></a>Exécution de procédures stockées

Vous pouvez également exécuter des transactions atomiques par rapport aux documents avec le même ID de périphérique, par exemple, si vous gérez des agrégats ou le dernier état d’un périphérique dans un seul document. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Dans la section suivante, nous examinons comment déplacer des collections partitionnées à partir des collections de partition unique.

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>Migration à partir de la partition unique aux collections partitionnées
Lorsqu’une application à l’aide d’une collection de partition unique a besoin d’un débit plus élevé (> 10 000 RU/s) ou de stockage de données plus important (> 10 Go), vous pouvez utiliser l' [Outil de Migration de données DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) pour migrer les données de la collection de partition unique à une collection partitionnée. 

Pour migrer à partir d’une collection à partition unique vers une collection partitionnée

1. Exporter des données à partir de la collection de partition unique en JSON. Pour plus d’informations, voir [Exporter vers un fichier JSON](documentdb-import-data.md#export-to-json-file) .
2. Importer les données dans une collection partitionnée créée avec une définition de clé de partition et de plus de 10 000 unités de demande débit par seconde, comme illustré dans l’exemple ci-dessous. Pour plus d’informations, reportez-vous à la section [importation de DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) .

![Migration des données à une collection partitionnées dans DocumentDB][3]  

>[AZURE.TIP] Pour importation courts, envisagez d’augmenter le nombre de demandes parallèles à 100 ou supérieure pour tirer parti de la débit disponible pour les collections partitionnées. 

Maintenant que nous avons terminé les notions de base, examinons quelques considérations de conception importante lorsque vous travaillez avec les clés de partition dans DocumentDB.

## <a name="designing-for-partitioning"></a>Conception de partitionnement
Le choix de la clé de partition est une décision importante que vous devrez effectuer au moment du design. Cette section décrit certaines des compromis lors de la sélection d’une clé de partition de votre collection.

### <a name="partition-key-as-the-transaction-boundary"></a>Clé de partition en tant que la limite de transaction
Le choix de la clé de partition doit s’équilibrer la nécessité d’activer l’utilisation de transactions par rapport à la nécessité de distribuer vos entités sur plusieurs clés pour garantir une solution évolutive. À une extrême, vous pouvez définir la même clé de partition pour tous vos documents, mais cela peut limiter l’évolutivité de votre solution. À l’autre extrême, vous pouvez affecter une clé de partition unique de chaque document, ce qui serait très évolutive, mais vous empêchent de l’utilisation de transactions entre document via des procédures stockées et les déclencheurs. Une clé de partition idéal est un qui vous permet d’utiliser des requêtes efficaces et qui a une cardinalité suffisante pour assurer que votre solution est évolutive. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Éviter les goulots d’étranglement des performances et de stockage 
Il est également important de choisir une propriété qui permet d’être distribuées sur un nombre de valeurs distinctes. Demandes de la même clé de partition ne peut pas dépasser le débit d’une seule partition et seront accélérés. Il est donc important de choisir une clé de partition qui n’entraîne pas de **« hot spots »** au sein de votre application. La taille totale de stockage de documents avec la même clé de partition peut également pas dépasser 10 Go de stockage. 

### <a name="examples-of-good-partition-keys"></a>Exemples de clés de la partition en bon état
Voici quelques exemples sur la façon de sélectionner la clé de partition de votre application :

* Si vous implémentez un back-end du profil utilisateur, l’ID utilisateur est un bon choix pour une clé de partition.
* Si vous stockez des données de IoT état du périphérique, par exemple, un ID de périphérique est un bon choix pour une clé de partition.
* Si vous utilisez DocumentDB pour l’enregistrement des données de série de l’heure, l’ID de processus ou le nom d’hôte est un bon choix pour une clé de partition.
* Si vous disposez d’une architecture mutualisée, l’ID de client est un bon choix pour une clé de partition.

Notez que, dans certains cas d’usage (par exemple, les profils utilisateur et IoT décrits ci-dessus), la clé de partition peut être à l’identique à votre id (clé de document). Dans d’autres telles que les données de série de temps, vous pouvez avoir une clé de partition qui est différente de l’id.

### <a name="partitioning-and-loggingtime-series-data"></a>Partitionnement et série/heure d’enregistrement de données
Un des cas d’usage plus courants de DocumentDB est pour la journalisation et télémétrie. Il est important de choisir une clé de partition en bon état, dans la mesure où vous devrez en lecture/écriture des volumes importants de données. Le choix sera dépendent de votre lecture et écriture taux et les types de requêtes que vous prévoyez d’exécuter. Voici quelques conseils sur le choix d’une clé de partition en bon état.

- Si votre cas d’utilisation implique un faible taux d’écrit acculumating sur une longue période de temps et date de besoin de requête par plages d’horodatages et d’autres filtres, puis en utilisant un cumul de l’horodatage par exemple comme une clé de partition est une bonne approche. Cela vous permet à la requête sur toutes les données pour une date à partir d’une seule partition. 
- Si votre charge de travail est lourde d’écriture, qui est généralement plus courant, vous devez utiliser une clé de partition qui n’est pas basée sur un timestamp afin que les DocumentDB peuvent répartir équitablement les écritures entre un nombre de partitions. Un nom d’hôte, ID de processus, l’ID activité ou une autre propriété présentant une cardinalité élevée est un bon choix. 
- Une troisième approche est un hybride une où plusieurs collections, une pour chaque jour/mois et de clé de partition est une propriété granulaire comme nom d’hôte. Cela présente l’avantage que vous pouvez définir des niveaux de performance différents en fonction de la fenêtre de temps, par exemple, la collection pour le mois en cours est mis en service avec un débit plus élevé dans la mesure où il sert de lectures et écritures, alors que des mois précédents avec une diminution débit car ils servent uniquement les lectures.

### <a name="partitioning-and-multi-tenancy"></a>Le partitionnement et l’architecture mutualisée
Si vous implémentez une application partagée à l’aide de DocumentDB, il existe deux modèles principaux de mise en oeuvre de location avec DocumentDB – clé une seule partition par le locataire et une collection par le locataire. Voici les avantages et inconvénients pour chacune :

* Une clé de Partition par le locataire : dans ce modèle, les locataires sont colocalisés dans une collection unique. Mais les requêtes et les insertions de documents au sein d’un client unique peuvent être effectuées sur une partition unique. Vous pouvez également implémenter une logique transactionnelle sur tous les documents d’un client. Étant donné que différents utilisateurs partagent une collection, vous pouvez enregistrer des coûts de stockage et de débit par le regroupement des ressources pour les locataires au sein d’une collection unique, plutôt que de mise en service d’une marge supplémentaire pour chaque client. L’inconvénient est que vous n’avez pas d’isolation des performances par les clients. Performances/débit augmente s’appliquent à l’intégralité de la collection vs augmente ciblée pour locataires.
* Un recouvrement par client : chaque client possède sa propre collection. Dans ce modèle, vous pouvez réserver des performances par les clients. Avec nouveau en fonction de la consommation modèle de tarification de DocumentDB, ce modèle est plus rentable pour les applications mutualisées avec un petit nombre de clients.

Vous pouvez également utiliser une approche de combinaison hiérarchisé qui collocates les locataires small et migre les locataires supérieures à leur propre collection.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons décrit le partitionnement dans DocumentDB d’Azure, comment vous pouvez créer des collections partitionnées et comment vous pouvez choisir une clé de partition en bon état pour votre application. 

-   Effectuer une échelle et des tests de performances avec DocumentDB. Pour obtenir un exemple, reportez-vous à la section [performances et tester l’évolutivité avec DocumentDB d’Azure](documentdb-performance-testing.md) .
-   Commencez à coder avec les [Kits de développement logiciel](documentdb-sdk-dotnet.md) ou de l' [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   En savoir plus sur [le débit mis en service dans les DocumentDB](documentdb-performance-levels.md)
-   Si vous souhaitez personnaliser la façon dont votre application effectue de partitionnement, vous pouvez brancher votre propre implémentation de partitionnement du côté client. Consultez [prise en charge de partitionnement du côté Client](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
