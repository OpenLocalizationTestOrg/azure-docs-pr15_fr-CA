<properties
    pageTitle="Comment faire pour utiliser le stockage de Table (C++) | Microsoft Azure"
    description="Stocker des données structurées dans le nuage avec le stockage Azure Table, un magasin de données NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-table-storage-from-c"></a>Comment faire pour utiliser le stockage de Table à partir de C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble  
Ce guide vous explique comment exécuter des scénarios courants en utilisant le service de stockage de Table d’Azure. Les exemples sont écrits en C++ et utilisent la [Bibliothèque de Client de stockage Azure pour C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios présentés incluent la **Création et la suppression d’une table** et **l’utilisation d’entités de la table**.

>[AZURE.NOTE] Ce guide vise la bibliothèque cliente du stockage Azure pour C++ version 1.0.0 et au-dessus. La version recommandée est bibliothèque de Client de stockage 2.2.0, qui est disponible via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>Créez une application C++  
Dans ce guide, vous allez utiliser les fonctionnalités de stockage qui peuvent être exécutées dans une application C++. Pour ce faire, vous devez installer la bibliothèque cliente du stockage Azure pour C++ et créer un compte de stockage Azure dans votre abonnement Azure.  

Pour installer la bibliothèque cliente du stockage Azure pour C++, vous pouvez utiliser les méthodes suivantes :

-   **Linux :** Suivez les instructions indiquées sur la page de la [Bibliothèque de Client de stockage Azure pour C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows :** Dans Visual Studio, cliquez sur **Outils > du Gestionnaire de package NuGet > Console de Gestionnaire de package**. Tapez la commande suivante dans la [console du Gestionnaire de package de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) et appuyez sur ENTRÉE.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Configurez votre application pour accéder au stockage de Table  
Ajoutez que les instructions au début du fichier C++ où vous souhaitez les API de stockage Azure permet d’accéder aux tables include suivantes :  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurer une chaîne de connexion du stockage Azure  
Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker les informations d’identification pour l’accès aux services de gestion de données et les points de terminaison. Lors de l’exécution d’une application cliente, vous devez fournir la chaîne de connexion de stockage dans le format suivant. Utilisez le nom de votre compte de stockage et de la clé d’accès de stockage pour le compte de stockage répertorié dans [Azure Portal](https://portal.azure.com) pour les valeurs de *nom de compte* et *AccountKey* . Pour plus d’informations sur les comptes de stockage et les touches d’accès rapide, voir [comptes de stockage sur Azure](storage-create-storage-account.md). Cet exemple montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion :  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Pour tester votre application sur votre ordinateur local basé sur Windows, vous pouvez utiliser l' [émulateur de stockage](storage-use-emulator.md) Azure qui est installé avec le [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/). L’émulateur de stockage est un utilitaire qui simule les Blob d’Azure, file d’attente et Table des services disponibles sur votre ordinateur de développement local. L’exemple suivant montre comment déclarer un champ statique pour contenir la chaîne de connexion pour votre émulateur de stockage local :  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Pour démarrer l’émulateur de stockage Azure, cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows. Commencez à taper **l’Émulateur de stockage Azure**et sélectionnez **Émulateur de stockage Microsoft Azure** à partir de la liste des applications.  

Les exemples suivants supposent que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.  

## <a name="retrieve-your-connection-string"></a>Récupérer la chaîne de connexion  
Vous pouvez utiliser la classe **cloud_storage_account** pour représenter les informations de votre compte de stockage. Pour récupérer les informations de votre compte de stockage à partir de la chaîne de connexion de stockage, vous pouvez utiliser la méthode parse.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Ensuite, obtenez une référence à une classe **cloud_table_client** , il vous permet d’obtenir des objets de référence pour les tables et les entités stockées dans le service de stockage de Table. Le code suivant crée un objet **cloud_table_client** à l’aide de l’objet de compte de stockage que nous récupérés ci-dessus :  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>Créer une table
Un objet **cloud_table_client** vous permet d’obtenir des objets de référence pour les tables et les entités. Le code suivant crée un objet **cloud_table_client** et l’utilise pour créer une nouvelle table.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table
Pour ajouter une entité à une table, créez un nouvel objet **table_entity** et la passer à **table_operation::insert_entity**. Le code suivant utilise le nom du client comme la clé de ligne et le nom comme clé de partition. Ensemble, la partition de l’entité et la clé de ligne identifient de manière unique l’entité dans la table. Entités avec la même clé de partition peuvent être interrogées plus rapidement que ceux avec des clés différentes, mais à l’aide de différentes clés permet une plus grande évolutivité de l’opération en parallèle. Pour plus d’informations, consultez [Microsoft Azure les performances de stockage et de la liste de contrôle de l’évolutivité](storage-performance-checklist.md).

Le code suivant crée une nouvelle instance de **table_entity** avec des données client doivent être stockées. Le code suivant appelle **table_operation::insert_entity** pour créer un objet **table_operation** pour insérer une entité dans une table et lui associe la nouvelle entité de table. Enfin, le code appelle la méthode execute sur l’objet **cloud_table** . Et la nouvelle **table_operation** envoie une demande au service de Table pour insérer la nouvelle entité de client dans la table « personnes ».  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>Insérez un lot d’entités
Vous pouvez insérer un lot d’entités au service de la Table en une seule opération d’écriture. Le code suivant crée un objet **table_batch_operation** , puis ajoute que trois opérations à celui-ci d’insertion. Chaque opération d’insertion est ajoutée en créant un nouvel objet d’entité, la définition de ses valeurs et puis en appelant la méthode insert de l’objet **table_batch_operation** pour associer l’entité à une opération d’insertion. Ensuite, **cloud_table.execute** est appelée pour exécuter l’opération.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Quelques éléments à noter sur les opérations de traitement par lots :  

-   Vous pouvez effectuer jusqu'à 100 insérer, supprimer, fusion, remplacer, les opérations d’insertion ou fusion et insérer ou remplacer dans n’importe quelle combinaison en un seul lot.  
-   Une opération de traitement par lots peut avoir une opération de récupération, s’il s’agit de la seule opération dans le lot.  
-   Toutes les entités dans une opération par lots unique doivent avoir la même clé de partition.  
-   Une opération de traitement par lots est limitée à une charge utile de données de 4 Mo.  

## <a name="retrieve-all-entities-in-a-partition"></a>Récupérer toutes les entités dans une partition
Pour interroger une table pour toutes les entités dans une partition, utilisez un objet **table_query** . L’exemple de code suivant spécifie un filtre pour les entités où « Smith » est la clé de partition. Cet exemple imprime les champs de chaque entité dans les résultats de la requête à la console.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

La requête de cet exemple affiche toutes les entités qui correspondent aux critères de filtre. Si vous avez des tables volumineuses et dont les entités de table de téléchargement souvent, il est recommandé que vous stocker vos données dans des objets BLOB de stockage Azure.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Extraire une plage d’entités dans une partition
Si vous ne souhaitez pas interroger toutes les entités dans une partition, vous pouvez spécifier une plage en combinant le filtre clé de partition avec un filtre de clés de ligne. L’exemple de code suivant utilise les deux filtres pour obtenir les toutes les entités dans la partition « Smith », où la clé (nom) de la ligne commençant par une lettre au plus tôt le « E » dans l’alphabet et imprime ensuite les résultats de la requête.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>Récupérer une entité unique
Vous pouvez écrire une requête pour récupérer une entité unique et spécifique. Le code suivant utilise **table_operation::retrieve_entity** pour spécifier le client « Jeff Smith ». Cette méthode retourne simplement une seule entité, plutôt qu’une collection, et la valeur retournée est dans **table_result**. Spécification de clés de partition et de ligne dans une requête est le moyen le plus rapide pour récupérer une seule entité du service de la Table.  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>Remplacer une entité
Pour remplacer une entité, le récupérer à partir du service de la Table, modifier l’objet de l’entité, puis enregistrez les modifications dans le service de la Table. Le code suivant modifie l’adresse d’e-mail et numéro de téléphone d’un client existant. Au lieu d’appeler **table_operation::insert_entity**, ce code utilise **table_operation::replace_entity**. Ainsi, l’entité à remplacer entièrement sur le serveur, à moins que l’entité sur le serveur a été modifié depuis qu’il a été récupéré, auquel cas l’opération échouera. Ce problème est d’empêcher votre application de remplacer par inadvertance une modification apportée entre l’extraction et la mise à jour par un autre composant de votre application. La manipulation de cette défaillance est à nouveau de récupérer l’entité et apportez vos modifications (si elles sont toujours valides) puis effectuer une autre opération de **table_operation::replace_entity** . La section suivante vous montrera comment substituer ce comportement.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>Insérer ou remplacer une entité
les opérations de **table_operation::replace_entity** échouera si l’entité a été modifiée depuis qu’il a été récupéré à partir du serveur. En outre, vous devez récupérer l’entité à partir du serveur en premier dans l’ordre de **table_operation::replace_entity** réussir. Parfois, cependant, vous ne savez pas si l’entité existe sur le serveur et les valeurs actuelles stockées dans celui-ci sont sans importance, la mise à jour doit remplacer tous les. Pour ce faire, vous utiliserez une opération **table_operation::insert_or_replace_entity** . Cette opération insère l’entité s’il n’existe pas, ou s’il remplace dans ce cas, que lorsque la dernière mise à jour a été effectuée. Dans l’exemple de code suivant, l’entité customer pour Jeff Smith est toujours récupérée, mais il est ensuite enregistré sur le serveur via **table_operation::insert_or_replace_entity**. Les mises à jour apportées à l’entité entre l’extraction et l’opération de mise à jour seront remplacés.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête  
Une requête à une table peut récupérer seulement quelques propriétés d’une entité. La requête dans le code suivant utilise la méthode **table_query::set_select_columns** pour renvoyer uniquement les adresses e-mail des entités dans la table.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] Interrogation de quelques propriétés d’une entité est une opération plus efficace que la récupération de toutes les propriétés.

## <a name="delete-an-entity"></a>Supprimer une entité
Vous pouvez facilement supprimer une entité une fois que vous l’avez extrait. Une fois que l’entité est récupérée, appelez **table_operation::delete_entity** avec l’entité à supprimer. Appelez ensuite la méthode **cloud_table.execute** . Le code suivant récupère et supprime une entité avec une clé de partition de « Smith » et une clé de la ligne de « François ».  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>Supprimer une table
Enfin, l’exemple de code suivant supprime une table à partir d’un compte de stockage. Une table qui a été supprimée ne sera pas disponible pour créer à nouveau pendant une période de temps suivant la suppression.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base du stockage de la table, cliquez sur ces liens pour en savoir plus sur le stockage Azure :  

-   [Comment faire pour utiliser le stockage Blob à partir de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Comment faire pour utiliser le stockage de file d’attente à partir de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Liste des ressources de stockage Azure dans C++](storage-c-plus-plus-enumeration.md)
-   [Bibliothèque Client de stockage pour la référence C++](http://azure.github.io/azure-storage-cpp)
-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
