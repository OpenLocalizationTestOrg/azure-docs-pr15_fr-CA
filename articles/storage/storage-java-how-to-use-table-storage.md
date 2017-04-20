<properties
    pageTitle="Comment faire pour utiliser le stockage de Table à partir de Java | Microsoft Azure"
    description="Stocker des données structurées dans le nuage avec le stockage Azure Table, un magasin de données NoSQL."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-java"></a>Comment faire pour utiliser le stockage de Table à partir de Java

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants utilisant le service de stockage de Table d’Azure. Les exemples sont écrits en Java et utilisent le [Stockage Azure SDK Java][]. Les scénarios présentés incluent la **Création**, la **mise en vente**et la **suppression** des tables, ainsi que **l’insertion**, **l’interrogation**, **modification**et **suppression** des entités dans une table. Pour plus d’informations sur les tables, consultez la section [étapes suivantes](#Next-Steps) .

Remarque : Un kit de développement est disponible pour les développeurs qui utilisent le stockage Azure sur des appareils Android. Pour plus d’informations, consultez le [Kit de développement logiciel pour Android stockage Azure][].

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Créer une application Java

Dans ce guide, vous allez utiliser les fonctionnalités de stockage qui peuvent être exécutées dans une application Java, localement ou dans le code s’exécutant dans un rôle de web ou travailleur dans Azure.

Pour ce faire, vous devez installer le Kit de développement Java (JDK) et créer un compte de stockage Azure dans votre abonnement Azure. Une fois que vous l’avez fait, vous devez vérifier que votre système de développement répond à la configuration minimale requise et les dépendances qui sont répertoriés dans le référentiel de [Stockage Azure SDK Java][] sur GitHub. Si votre système est conforme à ces exigences, vous pouvez suivre les instructions pour télécharger et installer les bibliothèques de stockage Azure pour Java sur votre système à partir de ce référentiel. Une fois ces tâches effectuées, vous serez en mesure de créer une application Java qui utilise les exemples de cet article.

## <a name="configure-your-application-to-access-table-storage"></a>Configurez votre application pour accéder au stockage de table

Ajoutez les instructions d’importation suivantes en haut du fichier Java dans lequel vous souhaitez utiliser des API de stockage Microsoft Azure pour accéder aux tables :

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="setup-an-azure-storage-connection-string"></a>Programme d’installation une chaîne de connexion du stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker les informations d’identification pour l’accès aux services de gestion de données et les points de terminaison. Lors de l’exécution dans une application cliente, vous devez fournir la chaîne de connexion de stockage dans le format suivant, en utilisant le nom de votre compte de stockage et de la clé d’accès principal pour le compte de stockage répertorié dans [Azure Portal](https://portal.azure.com) pour les valeurs de *nom de compte* et *AccountKey* . Cet exemple montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion :

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Dans une application s’exécutant dans un rôle dans Microsoft Azure, cette chaîne peut être stockée dans le fichier de configuration de service, *ServiceConfiguration.cscfg*et sont accessibles par un appel à la méthode **RoleEnvironment.getConfigurationSettings** . Voici un exemple de mise en route de la chaîne de connexion à partir d’un élément de **paramètre** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples suivants supposent que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="how-to-create-a-table"></a>Comment : créer une table

Un objet **CloudTableClient** vous permet d’obtenir des objets de référence pour les tables et les entités. Le code suivant crée un objet **CloudTableClient** et l’utilise pour créer un nouvel objet **CloudTable** qui représente une table nommée « personnes ». (Remarque : il existe des méthodes supplémentaires pour créer des objets de **CloudStorageAccount** ; pour plus d’informations, consultez **CloudStorageAccount** dans la [Référence du Kit de développement logiciel Client Azure stockage].)

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create the table if it doesn't exist.
       String tableName = "people";
       CloudTable cloudTable = tableClient.getTableReference(tableName);
       cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-tables"></a>Comment : répertorier les tables

Pour obtenir une liste de tables, appelez la méthode **CloudTableClient.listTables()** pour récupérer une liste de noms de tables d’iterable.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Loop through the collection of table names.
        for (String table : tableClient.listTables())
        {
          // Output each table name.
          System.out.println(table);
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-an-entity-to-a-table"></a>Comment : ajouter une entité à une table

Mappent des entités à des objets Java à l’aide d’une classe personnalisée **TableEntity**de mise en œuvre. Pour plus de commodité, la classe **TableServiceEntity** implémente **TableEntity** et utilise la réflexion pour mapper des propriétés à des méthodes getter et setter nommés pour les propriétés. Pour ajouter une entité à une table, créez une classe qui définit les propriétés de votre entité. Le code suivant définit une classe d’entité qui utilise le nom du client comme la clé de ligne et le nom comme clé de partition. Ensemble, la partition de l’entité et la clé de ligne identifient de manière unique l’entité dans la table. Entités avec la même clé de partition peuvent être interrogées plus rapidement que celles avec les clés de partition différent.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Les opérations de table impliquant des entités requièrent un objet **TableOperation** . Cet objet définit l’opération à effectuer sur une entité, qui peut être exécutée avec un objet **CloudTable** . Le code suivant crée une nouvelle instance de la classe **CustomerEntity** avec des données client doivent être stockées. Le code suivant appelle **TableOperation.insertOrReplace** pour créer un objet **TableOperation** pour insérer une entité dans une table et lui associe la nouvelle **CustomerEntity** . Enfin, le code appelle la méthode **execute** sur l’objet **CloudTable** , en spécifiant la table « personnes » et les nouvelles **TableOperation**, qui envoie ensuite une demande au service de stockage pour insérer la nouvelle entité de client dans la table « personnes » ou remplacer l’entité si elle existe déjà.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.setEmail("Walter@contoso.com");
        customer1.setPhoneNumber("425-555-0101");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-a-batch-of-entities"></a>Comment : insérer un lot d’entités

Vous pouvez insérer un lot d’entités au service de la table en une seule opération d’écriture. Le code suivant crée un objet **TableBatchOperation** , puis ajoute que trois opérations à celui-ci d’insertion. Chaque opération d’insertion est ajoutée en créant un nouvel objet d’entité, la définition de ses valeurs et puis en appelant la méthode **insert** de l’objet **TableBatchOperation** pour associer l’entité à une opération d’insertion. Le code appelle ensuite **exécuter** sur l’objet **CloudTable** , en spécifiant la table « personnes » et l’objet **TableBatchOperation** , qui envoie le lot d’opérations de table pour le service de stockage dans une seule demande.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Define a batch operation.
        TableBatchOperation batchOperation = new TableBatchOperation();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a customer entity to add to the table.
        CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
        customer.setEmail("Jeff@contoso.com");
        customer.setPhoneNumber("425-555-0104");
        batchOperation.insertOrReplace(customer);

       // Create another customer entity to add to the table.
       CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
       customer2.setEmail("Ben@contoso.com");
       customer2.setPhoneNumber("425-555-0102");
       batchOperation.insertOrReplace(customer2);

       // Create a third customer entity to add to the table.
       CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
       customer3.setEmail("Denise@contoso.com");
       customer3.setPhoneNumber("425-555-0103");
       batchOperation.insertOrReplace(customer3);

       // Execute the batch of operations on the "people" table.
       cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Quelques éléments à noter sur les opérations de traitement par lots :

- Vous pouvez effectuer une insertion jusqu'à 100, supprimer, fusionner, remplacer, insérer ou fusionner et insérer ou remplacer des opérations dans n’importe quelle combinaison en un seul lot.
- Une opération de traitement par lots peut avoir une opération de récupération, s’il s’agit de la seule opération dans le lot.
- Toutes les entités dans une opération par lots unique doivent avoir la même clé de partition.
- Une opération de traitement par lots est limitée à une charge utile de données 4 Mo.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Comment : récupérer toutes les entités dans une partition

Pour interroger une table pour les entités dans une partition, vous pouvez utiliser un **TableQuery**. Appelez **TableQuery.from** pour créer une requête sur une table particulière qui renvoie un type de résultat spécifié. Le code suivant spécifie un filtre pour les entités où « Smith » est la clé de partition. **TableQuery.generateFilterCondition** est une méthode d’assistance pour créer des filtres pour les requêtes. Appelez **où** la référence retournée par la méthode **TableQuery.from** pour appliquer le filtre à la requête. Lorsque la requête est exécutée avec un appel à **exécuter** sur l’objet **CloudTable** , il retourne un **itérateur** avec le type de résultat de **CustomerEntity** spécifié. Vous pouvez ensuite utiliser l' **itérateur** retournés dans une boucle for each consommer les résultats. Ce code imprime les champs de chaque entité dans les résultats de la requête à la console.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

       // Specify a partition query, using "Smith" as the partition key filter.
       TableQuery<CustomerEntity> partitionQuery =
           TableQuery.from(CustomerEntity.class)
           .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Comment : extraire une plage d’entités dans une partition

Si vous ne souhaitez pas interroger toutes les entités dans une partition, vous pouvez spécifier une plage à l’aide des opérateurs de comparaison dans un filtre. Le code suivant combine deux filtres pour obtenir les toutes les entités dans la partition « Smith », où la clé (nom) de la ligne commence par une lettre à un « E » dans l’alphabet. Il imprime ensuite les résultats de la requête. Si vous utilisez les entités ajoutées à la table dans le traitement par lots Insérer la section de ce guide, seulement deux entités sont retournées à ce moment (Ben et Denise Smith) ; Jeff Smith n’est pas inclus.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

        // Create a filter condition where the row key is less than the letter "E".
        String rowFilter = TableQuery.generateFilterCondition(
           ROW_KEY,
           QueryComparisons.LESS_THAN,
           "E");

        // Combine the two conditions into a filter expression.
        String combinedFilter = TableQuery.combineFilters(partitionFilter,
            Operators.AND, rowFilter);

        // Specify a range query, using "Smith" as the partition key,
        // with the row key being up to the letter "E".
        TableQuery<CustomerEntity> rangeQuery =
           TableQuery.from(CustomerEntity.class)
           .where(combinedFilter);

        // Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-single-entity"></a>Comment : récupérer une entité unique

Vous pouvez écrire une requête pour récupérer une entité unique et spécifique. Le code suivant appelle **TableOperation.retrieve** à la ligne et de la clé de partition des paramètres clés pour spécifier le client « Jeff Smith », au lieu de créer un **TableQuery** à l’aide de filtres pour faire la même chose. Lors de l’exécution, l’opération de récupération retourne qu’une seule entité, plutôt qu’une collection. La méthode **getResultAsType** effectue un cast du résultat vers le type de la cible d’affectation, un objet **CustomerEntity** . Si ce type n’est pas compatible avec le type spécifié pour la requête, une exception sera levée. Une valeur null est retournée si aucune entité n’a une partition exacte et les clés de ligne correspond à. Spécification de clés de partition et de ligne dans une requête est le moyen le plus rapide pour récupérer une seule entité du service de la Table.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

       // Submit the operation to the table service and get the specific entity.
       CustomerEntity specificEntity =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Output the entity.
        if (specificEntity != null)
        {
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-modify-an-entity"></a>Comment : modifier une entité

Pour modifier une entité, le récupérer à partir du service de la table, apporter des modifications à l’objet d’entité et enregistrer les modifications dans le service de la table avec une opération de remplacement ou de fusion. Le code suivant modifie le numéro de téléphone d’un client existant. Au lieu d’appeler **TableOperation.insert** comme nous l’avons fait pour insérer, ce code appelle **TableOperation.replace**. La méthode **CloudTable.execute** appelle le service de la table, et l’entité est remplacée, sauf si une autre application le modifié dans le temps étant donné que cette application extrait il. Lorsque cela se produit, une exception est levée, et l’entité doit être extrait, modifiée et enregistrée une nouvelle fois. Ce modèle de nouvelle tentative d’accès concurrentiel optimiste est courant dans un système de stockage distribué.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Submit the operation to the table service and get the specific entity.
        CustomerEntity specificEntity =
          cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Specify a new phone number.
        specificEntity.setPhoneNumber("425-555-0105");

        // Create an operation to replace the entity.
        TableOperation replaceEntity = TableOperation.replace(specificEntity);

        // Submit the operation to the table service.
        cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-query-a-subset-of-entity-properties"></a>Comment : interroger un sous-ensemble de propriétés d’entité

Une requête à une table peut récupérer seulement quelques propriétés d’une entité. Cette technique, appelée projection, réduit la bande passante et peut améliorer les performances de la requête, en particulier pour les grandes entités. La requête dans le code suivant utilise la méthode **select** pour retourner uniquement les adresses e-mail des entités dans la table. Les résultats sont projetés dans une collection de **chaînes** à l’aide d’un **EntityResolver**, qui effectue la conversion de type sur les entités retournées à partir du serveur. Pour en savoir plus sur la projection dans [les Tables Azure : Upsert de présentation et de Projection de requête][]. Notez que la projection n’est pas pris en charge sur l’émulateur de stockage local, afin que ce code s’exécute uniquement lorsque vous utilisez un compte sur le service de la table.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Define a projection query that retrieves only the Email property
        TableQuery<CustomerEntity> projectionQuery =
           TableQuery.from(CustomerEntity.class)
           .select(new String[] {"Email"});

        // Define a Entity resolver to project the entity to the Email value.
        EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-or-replace-an-entity"></a>Comment : insérer ou remplacer une entité

Souvent, vous souhaitez ajouter une entité à une table sans savoir s’il existe déjà dans la table. Une opération d’insertion ou remplacement vous permet de faire une requête qui insère l’entité si il ou n’existe pas remplacer l’existant si c’est le cas. S’appuyant sur des exemples antérieurs, le code suivant insère ou remplace l’entité pour « Walter harpe ». Après avoir créé une nouvelle entité, ce code appelle la méthode **TableOperation.insertOrReplace** . Puis, ce code appelle **l’exécuter** sur l’objet **CloudTable** avec la table et l’insertion ou opération de table que les paramètres de remplacement. Pour mettre à jour uniquement une partie d’une entité, la méthode **TableOperation.insertOrMerge** peut être utilisée à la place. Notez qu’insert-ou-le remplacement n’est pas pris en charge sur l’émulateur de stockage local, afin que ce code s’exécute uniquement lorsque vous utilisez un compte sur le service de la table. Pour en savoir plus sur l’insertion ou remplacement et insertion-ou-fusionner dans ce [les Tables Azure : Upsert de présentation et de Projection de requête][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-an-entity"></a>Comment : supprimer une entité

Vous pouvez facilement supprimer une entité une fois que vous l’avez extrait. Une fois que l’entité est récupérée, appelez **TableOperation.delete** avec l’entité à supprimer. Appelez ensuite **exécuter** sur l’objet **CloudTable** . Le code suivant récupère et supprime une entité de client.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-table"></a>Comment : supprimer une table

Enfin, le code suivant supprime une table à partir d’un compte de stockage. Une table qui a été supprimée ne sera pas disponible pour être recréés pour une période de temps suivant la suppression, généralement moins de 40 secondes.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la table, cliquez sur ces liens pour savoir comment effectuer des tâches de stockage plus complexes.

- [Stockage Azure SDK pour Java][]
- [Référence du Kit de développement logiciel Client stockage Azure][]
- [API REST de stockage Azure][]
- [Blog de l’équipe stockage Azure][]

Pour plus d’informations, consultez également le [Centre pour développeurs Java](/develop/java/).


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Stockage Azure SDK pour Java]: https://github.com/azure/azure-storage-java
[Stockage Azure SDK pour Android]: https://github.com/azure/azure-storage-android
[Référence du Kit de développement logiciel Client stockage Azure]: http://dl.windowsazure.com/storage/javadoc/
[API REST de stockage Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Les Tables Azure : Présentation Upsert et la Projection de requête]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
