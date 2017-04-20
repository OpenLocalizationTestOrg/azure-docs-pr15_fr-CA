<properties
    pageTitle="Comment faire pour utiliser le stockage de table à partir de PHP | Microsoft Azure"
    description="Apprenez à utiliser le service de la Table à partir de PHP pour créer et supprimer une table, insérer, supprimer et interroger la table."
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>Comment faire pour utiliser le stockage de table à partir de PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants utilisant le service de la Table d’Azure. Les exemples sont écrits en PHP et utiliser le [Kit de développement logiciel Azure pour PHP][download]. Les scénarios présentés incluent la **Création et la suppression d’une table, insertion, suppression et interrogation des entités dans une table**. Pour plus d’informations sur le service de la Table d’Azure, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Création d’une application PHP

La seule condition requise pour la création d’une application PHP qui accède au service de la Table d’Azure est le faisant référence à des classes dans le Kit de développement Azure pour PHP à partir de votre code. Vous pouvez utiliser les outils de développement pour créer votre application, y compris le bloc-notes.

Dans ce guide, vous utilisez les fonctionnalités de service de Table qui peuvent être appelées à partir d’une application PHP localement ou dans le code s’exécutant dans un rôle web Azure, le rôle de travail ou le site Web.

## <a name="get-the-azure-client-libraries"></a>Obtenir les bibliothèques clientes Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurez votre application pour accéder au service de la Table

Pour utiliser l’API de service de Table d’Azure, vous devez :

1. Une référence au fichier de chargeur automatique à l’aide de la [require_once] [ require_once] instruction, et
2. Référencer toutes les classes que vous pouvez utiliser.

L’exemple suivant montre comment inclure le fichier de chargeur automatique et faire référence à la classe **ServicesBuilder** .

> [AZURE.NOTE] Cet exemple montre comment (et autres exemples dans cet article) supposent que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement, vous devez référencer la <code>WindowsAzure.php</code> fichier de chargeur automatique.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Dans les exemples ci-dessous, les `require_once` instruction est toujours affichée, mais uniquement les classes nécessaires pour exécuter l’exemple sont référencés.

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Pour instancier un client de service de Table d’Azure, vous devez avoir une chaîne de connexion valide. Le format de la chaîne de connexion de service de Table est la suivante :

Pour accéder à un service direct :

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Pour accéder au stockage de l’émulateur :

    UseDevelopmentStorage=true


Pour créer un client de service d’Azure, vous devez utiliser la classe **ServicesBuilder** . Vous pouvez :

* Passez la chaîne de connexion directement ou
* utiliser le **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    * par défaut, il est fourni avec prise en charge d’une source externe - variables d’environnement
    * Vous pouvez ajouter de nouvelles sources en étendant la classe **ConnectionStringSource**

Pour les exemples présentés ici, la chaîne de connexion sera passée directement.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>Créer une table

Un objet **TableRestProxy** vous permet de créer un tableau avec la méthode **createTable** . Lors de la création d’une table, vous pouvez définir le délai d’attente du service de Table. (Pour plus d’informations sur le délai d’attente du service de Table, consultez [Définition de délais pour les opérations de Service de Table][table-service-timeouts].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

Pour plus d’informations sur les restrictions sur les noms de table, consultez [compréhension du modèle de données de Service de Table][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité à une table, créez un nouvel objet **d’entité** et passer à **TableRestProxy -> insertEntity**. Notez que lorsque vous créez une entité, vous devez spécifier une `PartitionKey` et `RowKey`. Ces identificateurs uniques d’une entité et sont des valeurs qui peuvent être interrogées beaucoup plus rapidement que les autres propriétés de l’entité. Le système utilise `PartitionKey` pour distribuer automatiquement des entités de la table sur le nombre de nœuds de stockage. Entités avec le même `PartitionKey` sont stockés sur le même nœud. (Effectuent des opérations sur plusieurs entités sont stockées sur le même nœud supérieure aux entités stockées sur différents nœuds.) Le `RowKey` est l’ID unique d’une entité au sein d’une partition.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Pour plus d’informations sur les types et les propriétés du tableau, consultez [compréhension du modèle de données de Service de Table][table-data-model].

La classe **TableRestProxy** fournit deux autres méthodes permettant d’insérer des entités : **insertOrMergeEntity** et **insertOrReplaceEntity**. Pour utiliser ces méthodes, créez une nouvelle **entité** et les passer en tant que paramètre à une méthode. Chaque méthode insère l’entité s’il n’existe pas. Si l’entité existe déjà, **insertOrMergeEntity** met à jour les valeurs de propriété si les propriétés existent déjà et ajoute les nouvelles propriétés s’ils n’existent pas, tandis que **insertOrReplaceEntity** remplace complètement une entité existante. L’exemple suivant montre comment utiliser **insertOrMergeEntity**. Si l’entité avec `PartitionKey` « tasksSeattle » et `RowKey` « 1 » n’existe pas déjà, il sera inséré. Toutefois, si elle a été précédemment insérée (comme illustré dans l’exemple ci-dessus), la `DueDate` propriété va être mise à jour et le `Status` propriété sera ajoutée. La `Description` et `Location` sont également mises à jour, mais avec des valeurs qui efficacement les laisser inchangées. Si ces deux dernières propriétés n’étaient pas ajoutées comme indiqué dans l’exemple, mais n’est pas existées dans l’entité cible, leurs valeurs existantes reste inchangées.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>Récupérer une entité unique

La méthode **getEntity de -> TableRestProxy** vous permet de récupérer une seule entité en recherchant son `PartitionKey` et `RowKey`. Dans l’exemple ci-dessous, la clé de partition `tasksSeattle` et la clé de la ligne `1` sont passés à la méthode **getEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>Récupérer toutes les entités dans une partition

Requêtes d’entité sont construits à l’aide de filtres (pour plus d’informations, voir [entités et interroger les Tables][filters]). Pour récupérer toutes les entités dans la partition, utilisez le filtre « PartitionKey eq *nom_partition*». L’exemple suivant montre comment récupérer toutes les entités dans le `tasksSeattle` partition en passant d’un filtre à la méthode **queryEntities** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Extraire un sous-ensemble d’entités dans une partition

Le même modèle que celui utilisé dans l’exemple précédent peut servir à récupérer un sous-ensemble d’entités dans une partition. Le sous-ensemble d’entités que vous récupérez sont déterminées par le filtre que vous utilisez (pour plus d’informations, voir [entités et interroger les Tables][filters]). L’exemple suivant montre comment utiliser un filtre pour extraire toutes les entités comportant un spécifique `Location` et un `DueDate` inférieure à une date spécifiée.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>Récupérer un sous-ensemble de propriétés d’entité

Une requête peut récupérer un sous-ensemble de propriétés d’entité. Cette technique, appelée *projection*, réduit la bande passante et peut améliorer les performances de la requête, en particulier pour les grandes entités. Pour spécifier une propriété à récupérer, passez le nom de la propriété à la méthode de **requête -> addSelectField** . Vous pouvez appeler cette méthode à plusieurs reprises pour ajouter d’autres propriétés. Après l’exécution de **TableRestProxy -> queryEntities**, les entités renvoyées aura uniquement des propriétés sélectionnées. (Si vous souhaitez retourner un sous-ensemble d’entités de la Table, utilisez un filtre comme indiqué dans les requêtes ci-dessus.)

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>Mise à jour d’une entité

Une entité existante peut être mis à jour en utilisant les méthodes **setProperty -> l’entité** et **l’entité -> addProperty** sur l’entité, puis en appelant **TableRestProxy -> updateEntity**. L’exemple suivant récupère une entité modifie une propriété, supprime une autre propriété et ajoute une nouvelle propriété. Notez que vous pouvez supprimer une propriété en affectant la valeur **null**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>Supprimer une entité

Pour supprimer une entité, passez le nom de table et de l’entité `PartitionKey` et `RowKey` à la méthode **TableRestProxy -> deleteEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Notez que pour les contrôles d’accès concurrentiel, vous pouvez définir l’Etag pour une entité à supprimer en utilisant la méthode **DeleteEntityOptions -> setEtag** et en passant l’objet **DeleteEntityOptions** **deleteEntity** sous la forme d’un quatrième paramètre.

## <a name="batch-table-operations"></a>Opérations de table par lots

La méthode **TableRestProxy -> traitement par lots** vous permet d’exécuter plusieurs opérations dans une seule demande. Le modèle ici implique l’ajout d’opérations à **BatchRequest** objet et puis en passant l’objet **BatchRequest** à la méthode **TableRestProxy -> traitement par lots** . Pour ajouter une opération à un objet **BatchRequest** , vous pouvez appeler une des méthodes suivantes plusieurs fois :

* **addInsertEntity** (ajoute une opération insertEntity)
* **addUpdateEntity** (ajoute une opération updateEntity)
* **addMergeEntity** (ajoute une opération mergeEntity)
* **addInsertOrReplaceEntity** (ajoute une opération insertOrReplaceEntity)
* **addInsertOrMergeEntity** (ajoute une opération insertOrMergeEntity)
* **addDeleteEntity** (ajoute une opération deleteEntity)

L’exemple suivant montre comment exécuter des opérations de **insertEntity** et de **deleteEntity** dans une demande unique :

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Pour plus d’informations sur le traitement par lots des opérations de Table, consultez [Exécution des Transactions de groupe entité][entity-group-transactions].

## <a name="delete-a-table"></a>Supprimer une table

Enfin, pour supprimer une table, passez le nom de la table à la méthode **TableRestProxy -> deleteTable** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du service Table d’Azure, suivre ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- Visitez le [blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Pour plus d’informations, consultez également le [Centre pour développeurs PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
