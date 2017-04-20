<properties
    pageTitle="Mise en route de stockage Azure Table à l’aide de .NET | Microsoft Azure"
    description="Stocker des données structurées dans le nuage avec le stockage Azure Table, un magasin de données NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-table-storage-using-net"></a>Mise en route de stockage Azure Table à l’aide de .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage par Table Azure est un service qui stocke les données NoSQL structurées dans le nuage. Stockage de table est un magasin de clé ou d’un attribut avec une conception schemaless. Stockage de Table étant schemaless, il est facile d’adapter vos données selon les besoins du evolve de votre application. Accès aux données est rapide et économique pour toutes sortes d’applications. Stockage de table est en général sensiblement inférieur de coût SQL traditionnel pour les volumes de données similaires.

Vous pouvez utiliser le stockage de Table pour stocker des groupes de données flexible, comme les données utilisateur pour les applications web, des carnets d’adresses, informations sur les périphériques et tout autre type de métadonnées nécessaires à votre service. Vous pouvez stocker n’importe quel nombre d’entités dans une table, et un compte de stockage peut contenir un nombre quelconque de tables, dans la limite de la capacité du compte de stockage.

### <a name="about-this-tutorial"></a>À propos de ce didacticiel

Ce didacticiel explique comment écrire du code .NET dans quelques scénarios courants utilisant le stockage Azure Table, y compris la création et suppression d’une table et insertion, mise à jour, suppression et interrogation des données de la table.

**Estimée durée :** 45 minutes

**Prerequisities :**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Bibliothèque Client de stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestionnaire de Configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account)

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Plus d’échantillons

Pour obtenir des exemples supplémentaires à l’aide de stockage de Table, reportez-vous à la section [Mise en route avec le stockage par Table Azure dans .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Vous pouvez télécharger l’exemple d’application et l’exécuter ou parcourir le code de GitHub.


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Ajouter des déclarations d’espace de noms

Ajoutez le code suivant `using` instructions en haut de la `program.cs` fichier :

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types

### <a name="parse-the-connection-string"></a>Analyser la chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Créer le client de service de Table

La classe **CloudTableClient** vous permet de récupérer les tables et les entités stockées dans le stockage Table. Voici une méthode pour créer le client du service :

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

Vous êtes maintenant prêt à écrire du code qui lit les données depuis et écrit des données dans le stockage Table.

## <a name="create-a-table"></a>Créer une table

Cet exemple montre comment créer un tableau, s’il n’existe pas déjà :

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Retrieve a reference to the table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table if it doesn't exist.
    table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Mappent des entités à C\# objets à l’aide d’une classe personnalisée dérivée de **TableEntity**. Pour ajouter une entité à une table, créez une classe qui définit les propriétés de votre entité. Le code suivant définit une classe d’entité qui utilise le nom du client comme la clé de ligne et le nom comme clé de partition. Ensemble, la partition de l’entité et la clé de ligne identifient de manière unique l’entité dans la table. Entités avec la même clé de partition peuvent être interrogées plus rapidement que celles avec les clés de partition différente, mais à l’aide de différentes clés permet une plus grande évolutivité d’opérations parallèles.  Pour toute propriété qui doit être stockée dans le service de la Table, la propriété doit être une propriété publique d’un type pris en charge qui expose les deux `get` et `set`.
En outre, le type d’entité *doit* exposer un constructeur sans paramètre.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Opérations de table qui concernent les entités sont exécutées via l’objet **CloudTable** que vous avez créé précédemment dans la section « Création d’une table ». L’opération à effectuer est représentée par un objet **TableOperation** .  L’exemple de code suivant illustre la création de l’objet **CloudTable** , puis un objet **CustomerEntity** .  Pour préparer l’opération, un objet **TableOperation** est créé pour insérer l’entité customer dans la table.  Enfin, l’opération est exécutée en appelant **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Insérez un lot d’entités

Vous pouvez insérer un lot d’entités dans une table en une seule opération d’écriture. Quelques autres remarques sur les opérations de traitement par lots :

-  Vous pouvez effectuer des mises à jour, supprime et l’insère dans la même opération de lot unique.
-  Une opération par lots unique peut inclure des entités jusqu'à 100.
-  Toutes les entités dans une opération par lots unique doivent avoir la même clé de partition.
-  Il est possible d’effectuer une requête sous la forme d’une opération de traitement par lots, il doit être la seule opération dans le lot.

<!-- -->
L’exemple de code suivant crée deux objets d’entité et l’ajoute au **TableBatchOperation** à l’aide de la méthode **Insert** . Ensuite, **CloudTable.Execute** est appelée pour exécuter l’opération.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities-in-a-partition"></a>Récupérer toutes les entités dans une partition

Pour interroger une table pour toutes les entités dans une partition, utilisez un objet **TableQuery** .
L’exemple de code suivant spécifie un filtre pour les entités où « Smith » est la clé de partition. Cet exemple imprime les champs de chaque entité dans les résultats de la requête à la console.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Extraire une plage d’entités dans une partition

Si vous ne souhaitez pas interroger toutes les entités dans une partition, vous pouvez spécifier une plage en combinant le filtre clé de partition avec un filtre de clés de ligne. L’exemple de code suivant utilise les deux filtres pour obtenir les toutes les entités dans la partition « Smith », où la clé (nom) de la ligne commençant par une lettre au plus tôt le « E » dans l’alphabet et imprime ensuite les résultats de la requête.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-single-entity"></a>Récupérer une entité unique

Vous pouvez écrire une requête pour récupérer une entité unique et spécifique. Le code suivant utilise **TableOperation** pour spécifier le client « Ben Smith ».
Cette méthode retourne simplement une entité plutôt qu’une collection et la valeur retournée dans **TableResult.Result** est un objet **CustomerEntity** .
Spécification de clés de partition et de ligne dans une requête est le moyen le plus rapide pour récupérer une seule entité du service de la Table.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-an-entity"></a>Remplacer une entité

Pour mettre à jour une entité, le récupérer à partir du service de la Table, modifier l’objet de l’entité, puis enregistrez les modifications dans le service de la Table. Le code suivant modifie le numéro de téléphone d’un client existant. Au lieu d’appeler **Insérer**, ce code utilise le **Remplacer**. Ainsi, l’entité à remplacer entièrement sur le serveur, à moins que l’entité sur le serveur a été modifié depuis qu’il a été récupéré, auquel cas l’opération échouera.  Ce problème est d’empêcher votre application de remplacer par inadvertance une modification apportée entre l’extraction et la mise à jour par un autre composant de votre application.  La manipulation de cette défaillance est à nouveau de récupérer l’entité et apportez vos modifications (si elles sont toujours valides) puis effectuer une autre opération de **remplacement** .  La section suivante vous montrera comment substituer ce comportement.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the Replace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-an-entity"></a>Insérer ou remplacer une entité

**Remplacez** les opérations échoue si l’entité a été modifiée depuis qu’il a été récupéré à partir du serveur.  En outre, vous devez récupérer l’entité à partir du serveur en premier dans l’ordre pour que l’opération de **remplacement** .
Parfois, cependant, vous ne connaissez pas si l’entité existe sur le serveur et les valeurs actuelles stockées ne sont pas appliquées. Votre mise à jour doit remplacer tous les.  Pour ce faire, vous utiliserez une opération **InsertOrReplace** .  Cette opération insère l’entité s’il n’existe pas, ou s’il remplace dans ce cas, que lorsque la dernière mise à jour a été effectuée.  Dans l’exemple de code suivant, l’entité customer pour Ben Smith est toujours récupérée, mais il est ensuite enregistré sur le serveur via **InsertOrReplace**.  Les mises à jour apportées à l’entité entre les opérations de récupération et de mise à jour seront remplacés.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête

Une table de requête peut récupérer seulement quelques propriétés d’une entité au lieu de toutes les propriétés d’entité. Cette technique, appelée projection, réduit la bande passante et peut améliorer les performances de la requête, en particulier pour les grandes entités. La requête dans le code suivant renvoie uniquement les adresses e-mail des entités dans la table. Cela est fait à l’aide d’une requête de **DynamicTableEntity** et de **EntityResolver**. Vous pouvez en savoir plus sur la projection sur le [Upsert de présentation et de Projection de requête blog valider][]. Notez que la projection n’est pas pris en charge sur l’émulateur de stockage local, afin que ce code s’exécute uniquement lorsque vous utilisez un compte de service de la Table.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-an-entity"></a>Supprimer une entité

Vous pouvez facilement supprimer une entité après avoir récupéré, en utilisant le même modèle que celui indiqué pour la mise à jour d’une entité.  Le code suivant récupère et supprime une entité de client.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-a-table"></a>Supprimer une table

Enfin, l’exemple de code suivant supprime une table à partir d’un compte de stockage. Une table qui a été supprimée ne sera pas disponible pour créer à nouveau pendant une période de temps suivant la suppression.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="retrieve-entities-in-pages-asynchronously"></a>Récupérer des entités dans les pages de manière asynchrone

Si vous lisez un grand nombre d’entités, et vous souhaitez processus/afficher les entités comme ils sont récupérés plutôt que d’attendre que tous les retourner, vous pouvez récupérer des entités en utilisant une requête segmentée. Cet exemple montre comment renvoyer des résultats dans des pages en utilisant le modèle Async Await afin que l’exécution n’est pas bloquée pendant que vous attendez d’un grand jeu de résultats à retourner. Pour plus d’informations sur l’utilisation du modèle asynchrone-Await dans .NET, consultez [programmation asynchrone avec Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la Table, cliquez sur ces liens pour en savoir plus sur les tâches de stockage plus complexes :

- Voir les autres exemples de stockage de Table dans [Mise en route avec le stockage par Table Azure dans .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
- Consulter la documentation de référence de service de Table pour plus d’informations sur l’API disponibles :
    - [Bibliothèque Client de stockage de référence .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Référence de l’API REST](http://msdn.microsoft.com/library/azure/dd179355)
- Découvrez comment simplifier le code que vous écrivez pour utiliser le stockage Azure à l’aide du [Kit de développement logiciel Azure WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- Permet d’afficher plusieurs guides d’en savoir plus sur les options supplémentaires pour le stockage des données dans Azure.
    - [Mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) pour stocker des données non structurées.
    - [Se connecter à la base de données SQL à l’aide de .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) pour stocker des données relationnelles.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Billet de blog Upsert de présentation et de Projection de requête]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage
