<properties
    pageTitle="Comment faire pour utiliser le stockage par Table Azure de Ruby | Microsoft Azure"
    description="Stocker des données structurées dans le nuage avec le stockage Azure Table, un magasin de données NoSQL."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>Comment faire pour utiliser le stockage par Table Azure de Ruby

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants utilisant le service de la Table d’Azure. Ces exemples sont écrits à l’aide de l’API Ruby. Les scénarios présentés incluent la **Création et suppression d’une table, insertion et l’interrogation des entités dans une table**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Créer une application Ruby

Pour obtenir des instructions comment créer une application Ruby, consultez [Ruby on Rails Web des applications sur un ordinateur virtuel d’Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).


## <a name="configure-your-application-to-access-storage"></a>Configurez votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous devez télécharger et utiliser le package azure Ruby qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de stockage reste.

### <a name="use-rubygems-to-obtain-the-package"></a>Utiliser RubyGems pour obtenir le package

1. Utilisez une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Dans la fenêtre de commande pour installer la marque et les dépendances, tapez **gem installer azure** .

### <a name="import-the-package"></a>Importer le package

Utilisez votre éditeur de texte, ajoutez le code suivant en haut du fichier Ruby où vous avez l’intention d’utiliser le stockage :

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Le module azure lira les variables d’environnement **AZURE\_stockage\_compte** et **AZURE\_stockage\_accès\_clé** pour plus d’informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte avant d’utiliser **Azure::TableService** avec le code suivant :

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Pour obtenir ces valeurs à partir d’un classique ou compte de gestionnaire de ressources de stockage dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Atteindre le compte de stockage que vous souhaitez utiliser.
3. De la lame de paramètres sur la droite, cliquez sur **Les touches d’accès**.
4. De la lame de clés d’accès qui s’affiche, vous verrez la touche d’accès rapide 1 et la touche d’accès rapide 2. Vous pouvez utiliser une ou l’autre de ces. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers. 

Pour obtenir ces valeurs à partir d’un compte de stockage classique dans le portail Azure classique :

1. Connectez-vous au [portail d’Azure classique](https://manage.windowsazure.com).
2. Atteindre le compte de stockage que vous souhaitez utiliser.
3. Cliquez sur **Gérer les touches d’accès rapide** au bas du volet de navigation.
4. Dans la boîte de dialogue contextuelle, vous verrez le nom de compte de stockage, la clé d’accès principal et la clé d’accès secondaire. Pour la touche d’accès rapide, vous pouvez utiliser soit principal, soit le secondaire. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers.

## <a name="create-a-table"></a>Créer une table

L’objet **Azure::TableService** vous permet de travailler avec les tables et les entités. Pour créer une table, utilisez la **créer\_table()** méthode. L’exemple suivant crée une table ou une impression de l’erreur, s’il en existe une.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité, tout d’abord créer un objet de hachage qui définit les propriétés d’entité. Notez que pour chaque entité, vous devez spécifier un **PartitionKey** et un **RowKey**. Celles-ci constituent les identificateurs uniques de vos entités et sont des valeurs qui peuvent être interrogées beaucoup plus rapidement que les autres propriétés. Le stockage Azure utilise **PartitionKey** pour distribuer automatiquement des entités de la table sur le nombre de nœuds de stockage. Les entités avec le même **PartitionKey** sont stockées sur le même nœud. Le **RowKey** est l’ID unique de l’entité dans la partition qu'auquel il appartient.

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>Mise à jour d’une entité

Il existe plusieurs méthodes mettre à jour d’une entité existante :

* **mise à jour\_entity() :** Mettre à jour une entité existante en le remplaçant.
* **fusion\_entity() :** Met à jour une entité existante en fusionnant les nouvelles valeurs de propriété dans l’entité existante.
* **Insérer\_ou\_fusion\_entity() :** Met à jour une entité existante en le remplaçant. Si aucune entité n’existe, un nouveau est inséré :
* **Insérer\_ou\_remplacer\_entity() :** Met à jour une entité existante en fusionnant les nouvelles valeurs de propriété dans l’entité existante. Si aucune entité n’existe, une nouvelle sera insérée.

L’exemple suivant illustre la mise à jour d’une entité à l’aide de **à jour\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Avec **mise à jour\_entity()** et **fusion\_entity()**, si l’entité que vous mettez à jour n’existe pas l’opération de mise à jour échouera. Par conséquent, si vous souhaitez stocker une entité que si elle existe déjà, vous devez utiliser **Insérer\_ou\_remplacer\_entity()** ou **Insérer\_ou\_fusion\_entity()**.

## <a name="work-with-groups-of-entities"></a>Travailler avec des groupes d’entités

Il est parfois judicieux d’envoyer plusieurs opérations dans un lot pour garantir atomique de traitement par le serveur. Pour ce faire, vous commencez par créer un objet de **traitement par lots** , puis utilisez la **exécuter\_batch()** méthode sur **TableService**. L’exemple suivant illustre l’envoi des deux entités avec RowKey 2 et 3 dans un lot. Notez qu’il ne fonctionne que pour les entités comprenant la même PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>Requête d’une entité

Pour interroger une entité dans un tableau, utilisez la **obtenir\_entity()** méthode, en passant le nom de la table, **PartitionKey** et **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>Interroger un ensemble d’entités

Pour interroger un ensemble d’entités dans une table, créez un objet de hachage de requête et utilisez la **requête\_entities()** méthode. L’exemple suivant illustre l’obtention de toutes les entités avec la même **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Si le jeu de résultats est trop grand pour une seule requête à retourner, un jeton de continuation sera renvoyé que vous pouvez utiliser pour récupérer les pages suivantes.

## <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête

Une requête à une table peut récupérer seulement quelques propriétés d’une entité. Cette technique, appelée « projection », réduit la bande passante et peut améliorer les performances de la requête, en particulier pour les grandes entités. Utilisez la clause select et passe les noms des propriétés que vous souhaitez afficher le client.

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>Supprimer une entité

Pour supprimer une entité, vous devez utiliser le **Supprimer\_entity()** méthode. Vous devez passer le nom de la table qui contient l’entité, la PartitionKey et le RowKey de l’entité.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>Supprimer une table

Pour supprimer une table, utilisez la **Supprimer\_table()** méthode et passez le nom de la table que vous souhaitez supprimer.

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations sur les tâches de stockage plus complexes, cliquez sur ces liens :

- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Référentiel [d’Azure SDK pour Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub
