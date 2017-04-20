<properties
    pageTitle="Comment faire pour utiliser le stockage de file d’attente à partir de Python | Microsoft Azure"
    description="Apprenez à utiliser le service de file d’attente d’Azure à partir de Python pour créer et supprimer des files d’attente, insérer, obtenir et supprimer les messages."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>Comment faire pour utiliser le stockage de file d’attente à partir de Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants utilisant le service de stockage de file d’attente d’Azure. Les exemples sont écrits dans les Python et utilisent le [Kit de développement du stockage Microsoft Azure pour les Python]. Les scénarios présentés comprennent **Insertion**, **lecture**, **mise en route**et **suppression de** file d’attente messages, ainsi que **Création et suppression de files d’attente**. Pour plus d’informations sur les files d’attente, reportez-vous à la section [étapes suivantes].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>Comment : Créer une file d’attente

L’objet **QueueService** vous permet de travailler avec des files d’attente. Le code suivant crée un objet **QueueService** . Ajoutez le code suivant en haut de n’importe quel fichier Python dans lequel vous souhaitez accéder par programme à stockage Azure :

    from azure.storage.queue import QueueService

Le code suivant crée un objet **QueueService** à l’aide de la clé de nom de compte et le compte de stockage. Remplacez « mon compte » et « mykey » avec votre nom de compte et la clé.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>Comment : Insérer un Message dans une file d’attente

Pour insérer un message dans une file d’attente, utilisez la **put\_message** méthode pour créer un nouveau message et l’ajouter à la file d’attente.

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>Comment : Lire le Message suivant

Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant le **lecture\_messages** méthode. Par défaut, **lecture\_messages** lit un seul message.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>Comment : File d’attente les Messages

Votre code supprime un message d’une file d’attente en deux étapes. Lorsque vous appelez **obtenir\_messages**, vous obtenez le message suivant dans une file d’attente par défaut. Un message retourné à partir de **obtenir\_messages** devient invisible pour un autre code de lecture des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **Supprimer\_message**. Ce processus en deux étapes de suppression d’un message garantit que lorsque votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Votre code appelle **Supprimer\_message** avec le bouton droit une fois que le message a été traité.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.
Tout d’abord, vous pouvez obtenir un lot de messages (32 maximum). Ensuite, vous pouvez définir un délai d’expiration de l’invisibilité plus longues ou plus courtes, permettant à votre code plus ou moins de temps à traiter entièrement chaque message. Le code suivant montre comment utiliser le **obtenir\_messages** méthode afin d’obtenir 16 messages en un seul appel. Puis il traite chaque message en utilisant une boucle for. Il définit également le délai d’expiration de l’invisibilité à cinq minutes pour chaque message.

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : Modifier le contenu d’un Message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour de l’état de la tâche. Le code ci-dessous utilise le **mise à jour\_message** pour mettre à jour d’un message. Le délai d’attente de visibilité est définie sur 0, ce qui signifie le message s’affiche immédiatement, et le contenu est mis à jour.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>Comment : Obtenir la longueur de la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La **obtenir\_file d’attente\_métadonnées** méthode demande le service de file d’attente pour renvoyer les métadonnées relatives à la file d’attente et **approximate_message_count**. Le résultat n’est approximatif, car les messages peuvent être ajoutés ou supprimés une fois que le service de file d’attente répond à votre demande.

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>Comment : Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’il contient, appelez le **Supprimer\_file d’attente** méthode.

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la file d’attente, cliquez sur ces liens pour en savoir plus.

- [Centre de développement de Python](/develop/python/)
- [Services de stockage Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l’équipe stockage Azure]
- [Stockage de Microsoft Azure SDK pour les Python]

[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Stockage de Microsoft Azure SDK pour les Python]: https://github.com/Azure/azure-storage-python
