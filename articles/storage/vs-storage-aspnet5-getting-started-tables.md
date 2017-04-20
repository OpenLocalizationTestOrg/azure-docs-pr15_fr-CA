<properties
    pageTitle="La mise en route avec Visual Studio et de stockage de table connectés services (ASP.NET 5) | Microsoft Azure"
    description="Comment se familiariser avec le stockage Azure Table dans un projet de 5 d’ASP.NET dans Visual Studio après la connexion à un compte de stockage à l’aide de Visual Studio de services connectés"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>La mise en route avec Visual Studio et de stockage Azure Table de services connectés

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment démarrer l’utilisation du stockage de la Table d’Azure dans Visual Studio une fois que vous avez créé ou référencé d’un compte de stockage Azure dans un projet ASP.NET 5 à l’aide de la boîte de dialogue de Visual Studio, **Ajouter des Services connectés** .

Le service de stockage Azure Table vous permet de stocker de grandes quantités de données structurées. Le service est un magasin de données NoSQL qui accepte des appels authentifiés, à l’intérieur et à l’extérieur du nuage Azure. Les tables Azure sont idéales pour le stockage des données structurées et non relationnelles.

L’opération **Ajouter Connected Services** installe les packages NuGet appropriés pour accéder au stockage Azure dans votre projet et ajoute la chaîne de connexion pour le compte de stockage pour vos fichiers de configuration de projet.

Pour plus d’informations sur l’utilisation du stockage Azure Table, reportez-vous à la section [mise en route de stockage Azure Table à l’aide de .NET](storage-dotnet-how-to-use-tables.md).

Pour commencer, vous devez d’abord créer une table dans votre compte de stockage. Nous allons vous montrer comment créer une table Azure dans le code. Nous vous montrerons également comment effectuer une table de base et les opérations de l’entité, telles que l’ajout, la modification, la lecture et la lecture des entités de la table. Les exemples sont écrits en C\# de code et d’utiliser la bibliothèque cliente du stockage Azure pour .NET.

**Remarque** - certaines API qui effectuent des appels out vers le stockage Azure dans ASP.NET 5 sont asynchrones. Pour plus d’informations, consultez [Programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx) . Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisés.

## <a name="access-tables-in-code"></a>Accéder aux tables dans le code

Pour accéder aux tables dans les projets ASP.NET 5, vous devez inclure les éléments suivants pour les fichiers sources C# qui accèdent le stockage par table Azure.

1. Assurez-vous que les déclarations d’espace de noms en haut du fichier C# incluent ces instructions **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Récupérez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d’obtenir le votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Remarque** - utiliser tout le code ci-dessus gauche du code dans les exemples suivants.

3. Récupérez un objet **CloudTableClient** pour référencer les objets de la table dans votre compte de stockage.  

        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtenir un objet de référence de **CloudTable** pour faire référence à une table spécifique et des entités.

        // Get a reference to a table named "peopleTable"
        CloudTable table = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Créer une table dans le code

Pour créer la table Azure, il suffit d’ajouter un appel à **CreateIfNotExistsAsync()**.

    // Create the CloudTable if it does not exist
    await table.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité à une table, vous créez une classe qui définit les propriétés de votre entité. Le code suivant définit une classe d’entité appelée **CustomerEntity** qui utilise le nom du client comme la clé de ligne et le nom comme clé de partition.

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

Opérations de table impliquant des entités sont effectuées à l’aide de la **CloudTable** de l’objet créé plus haut dans « Tables Access dans le code ». L’objet **TableOperation** représente l’opération à effectuer. L’exemple de code suivant montre comment créer un objet **CloudTable** et un objet **CustomerEntity** . Pour préparer l’opération, un **TableOperation** est créé pour insérer l’entité customer dans la table. Enfin, l’opération est exécutée en appelant CloudTable.ExecuteAsync.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Insérez un lot d’entités

Vous pouvez insérer plusieurs entités dans une table dans une opération d’écriture unique. L’exemple de code suivant crée deux objets d’entité (« Jeff Smith » et « Ben Smith »), les ajoute à un objet **TableBatchOperation** à l’aide de la méthode **Insert** et puis démarre l’opération en appelant CloudTable.ExecuteBatchAsync.

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Obtenir toutes les entités dans une partition
Pour interroger une table pour toutes les entités dans une partition, utilisez un objet **TableQuery** . L’exemple de code suivant spécifie un filtre pour les entités où « Smith » est la clé de partition. Cet exemple imprime les champs de chaque entité dans les résultats de la requête à la console.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

## <a name="get-a-single-entity"></a>Obtenir une entité unique
Vous pouvez écrire une requête pour obtenir une entité unique et spécifique. Le code suivant utilise un objet **TableOperation** pour spécifier un client nommé « Ben Smith ». Cette méthode retourne simplement une seule entité, plutôt que d’une collection et la valeur retournée dans **TableResult.Result** est un objet **CustomerEntity** . Spécification de clés de partition et de ligne dans une requête est le moyen le plus rapide pour récupérer une seule entité du service de la **Table** .

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Supprimer une entité
Vous pouvez supprimer une entité après que vous trouverez. Le code suivant recherche une entité client nommée « Ben Smith », si elle la trouve, il le supprime.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
