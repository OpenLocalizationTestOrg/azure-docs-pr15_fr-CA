<properties
    pageTitle="Comment faire pour utiliser le stockage de Table à partir de Python | Microsoft Azure"
    description="Stocker des données structurées dans le nuage avec le stockage Azure Table, un magasin de données NoSQL."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>Comment faire pour utiliser le stockage de Table à partir de Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants en utilisant le service de stockage de Table d’Azure. Les exemples sont écrits dans les Python et utilisent le [Kit de développement du stockage Microsoft Azure pour les Python]. Les scénarios couverts comprennent la création et la suppression d’une table, en plus de l’insertion et l’interrogation des entités dans une table.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Créer une table

L’objet **TableService** vous permet de travailler avec les services de la table. Le code suivant crée un objet **TableService** . Ajoutez le code en haut de n’importe quel fichier Python, dans laquelle vous souhaitez accéder par programme à stockage Azure :

    from azure.storage.table import TableService, Entity

Le code suivant crée un objet **TableService** à l’aide de la clé de nom de compte et le compte de stockage.  Remplacez « mon compte » et « mykey » avec votre nom de compte et la clé.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité, commencez par créer un dictionnaire ou une entité qui définit vos noms de propriété d’entité et les valeurs. Notez que pour chaque entité, vous devez spécifier **PartitionKey** et **RowKey**. Ce sont les identificateurs uniques de vos entités. Vous pouvez interroger à l’aide de ces valeurs bien plus vite que vous pouvez interroger les propriétés d’autres. Le système utilise **PartitionKey** pour distribuer automatiquement les entités de table sur le nombre de nœuds de stockage.
Les entités qui ont le même **PartitionKey** sont stockées sur le même nœud. **RowKey** est l’ID unique de l’entité dans la partition auquel il appartient.

Pour ajouter une entité à la table, passez un objet de dictionnaire à la **Insérer\_entité** méthode.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

Vous pouvez également passer une instance de la classe **d’entité** pour le **Insérer\_entité** méthode.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>Mise à jour d’une entité

Ce code montre comment remplacer l’ancienne version d’une entité existante avec une version mise à jour.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

Si l’entité est en cours de mise à jour n’existe pas, l’opération de mise à jour échouera. Si vous souhaitez stocker une entité que qu’il existe ou non avant, utilisez **Insérer\_ou\_replace_entity**.
Dans l’exemple suivant, le premier appel remplace l’entité existante. Le deuxième appel insère une nouvelle entité depuis aucune entité avec la spécifiée, **PartitionKey** et **RowKey** existe dans la table.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>Modifier un groupe d’entités

Il est parfois judicieux d’envoyer plusieurs opérations dans un lot pour garantir atomique de traitement par le serveur. Pour ce faire, vous utilisez la classe **TableBatch** . Lorsque vous ne souhaitez pas soumettre le lot, vous appelez **validation\_lot**. Notez que toutes les entités doivent être dans la même partition afin d’être modifié en tant que lot. L’exemple suivant ajoute deux entités ensemble dans un lot.

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

Lots peuvent également être utilisés avec la syntaxe du Gestionnaire de contexte :

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>Requête d’une entité

Pour interroger une entité dans un tableau, utilisez la **obtenir\_entité** méthode en passant **PartitionKey** et **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>Interroger un ensemble d’entités

Cet exemple recherche que toutes les tâches de Seattle basée sur **PartitionKey**.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête

Une requête à une table peut récupérer seulement quelques propriétés d’une entité.
Cette technique, appelée *projection*, réduit la bande passante et peut améliorer les performances de la requête, en particulier pour les grandes entités. Utilisez le paramètre de **Sélectionner** et de transmettre les noms des propriétés que vous souhaitez afficher le client.

La requête dans le code suivant renvoie uniquement les descriptions des entités de la table.

[AZURE.NOTE] L’extrait suivant fonctionne uniquement avec le service de stockage cloud. Ce n’est pas pris en charge par l’émulateur de stockage.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>Supprimer une entité

Vous pouvez supprimer une entité à l’aide de sa clé de partition et de ligne.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>Supprimer une table

Le code suivant supprime une table dans un compte de stockage.

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la Table, cliquez sur ces liens pour en savoir plus.

- [Centre de développement de Python](/develop/python/)
- [Services de stockage Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l’équipe stockage Azure]
- [Stockage de Microsoft Azure SDK pour les Python]

[Blog de l’équipe de stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Stockage de Microsoft Azure SDK pour les Python]: https://github.com/Azure/azure-storage-python
