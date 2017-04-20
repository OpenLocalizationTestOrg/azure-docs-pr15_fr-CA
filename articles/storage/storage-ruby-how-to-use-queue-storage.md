<properties 
    pageTitle="Comment faire pour utiliser le stockage de file d’attente à partir de Ruby | Microsoft Azure" 
    description="Apprenez à utiliser le service de file d’attente d’Azure pour créer et supprimer des files d’attente, insérer, obtenir et supprimer les messages. Exemples écrites en Ruby." 
    services="storage" 
    documentationCenter="ruby" 
    authors="robinsh" 
    manager="carmonm" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-ruby"></a>Comment faire pour utiliser le stockage de file d’attente à partir de Ruby

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment exécuter des scénarios courants utilisant le service de stockage de file d’attente Microsoft Azure. Ces exemples sont écrits à l’aide de l’API d’Azure Ruby.
Les scénarios présentés comprennent **Insertion**, **lecture**, **mise en route**et **suppression de** file d’attente messages, ainsi que **Création et suppression de files d’attente**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Créer une Application Ruby

Créez une application Ruby. Pour obtenir des instructions, reportez-vous à la section [Ruby on Rails Web des applications sur un ordinateur virtuel d’Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Configurez votre Application pour le stockage d’accès

Pour utiliser le stockage Azure, vous devez télécharger et utiliser le package azure Ruby, qui inclut un ensemble de bibliothèques de commodité qui communiquent avec les services de reste de stockage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utiliser RubyGems pour obtenir le package

1. Utilisez une interface de ligne de commande **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Tapez « azure d’installation gem » dans la fenêtre de commande pour installer la marque et les dépendances.

### <a name="import-the-package"></a>Importer le package

Utilisez votre éditeur de texte, ajoutez le code suivant en haut du fichier Ruby où vous avez l’intention d’utiliser le stockage :

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Programme d’installation une connexion de stockage Azure

Le module azure lira les variables d’environnement **AZURE\_stockage\_compte** et **AZURE\_stockage\_ACCESS_KEY** pour les informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte avant d’utiliser **Azure::QueueService** avec le code suivant :

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your Azure storage access key>"

 
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

## <a name="how-to-create-a-queue"></a>Comment : Créer une file d’attente

Le code suivant crée un objet **Azure::QueueService** , qui vous permet d’utiliser des files d’attente.

    azure_queue_service = Azure::QueueService.new

Utilisez la méthode **create_queue()** pour créer une file d’attente avec le nom spécifié.

    begin
      azure_queue_service.create_queue("test-queue")
    rescue
      puts $!
    end

## <a name="how-to-insert-a-message-into-a-queue"></a>Comment : Insérer un Message dans une file d’attente

Pour insérer un message dans une file d’attente, utilisez la méthode **create_message()** pour créer un nouveau message et l’ajouter à la file d’attente.

    azure_queue_service.create_message("test-queue", "test message")

## <a name="how-to-peek-at-the-next-message"></a>Comment : Lire le Message suivant

Vous pouvez lire le message avant une file d’attente sans le supprimer de la file d’attente en appelant le **lecture\_messages()** méthode. Par défaut, **lecture\_messages()** lit un seul message. Vous pouvez également spécifier le nombre de messages vous souhaitez lire.

    result = azure_queue_service.peek_messages("test-queue",
      {:number_of_messages => 10})

## <a name="how-to-dequeue-the-next-message"></a>Comment : File d’attente du Message suivant

Vous pouvez supprimer un message d’une file d’attente en deux étapes.

1. Lorsque vous appelez **liste\_messages()**, vous obtenez le message suivant dans une file d’attente par défaut. Vous pouvez également spécifier le nombre de messages vous souhaitez obtenir. Les messages renvoyés à partir de **liste\_messages()** devient invisible pour un autre code de lecture des messages de cette file d’attente. Vous passez dans le délai d’attente de visibilité dans les secondes sous la forme d’un paramètre.

2. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **delete_message()**.

Ce processus en deux étapes de suppression d’un message garantit que lorsque votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Votre code appelle **Supprimer\_à message()** avec le bouton droit une fois que le message a été traité.

    messages = azure_queue_service.list_messages("test-queue", 30)
    azure_queue_service.delete_message("test-queue", 
      messages[0].id, messages[0].pop_receipt)

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : Modifier le contenu d’un Message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Le code ci-dessous utilise la méthode **update_message()** mettre à jour un message. La méthode retourne un tuple qui contient le pop reçu de message de la file d’attente et une valeur d’heure UTC date représentant lorsque le message sera visible sur la file d’attente.

    message = azure_queue_service.list_messages("test-queue", 30)
    pop_receipt, time_next_visible = azure_queue_service.update_message(
      "test-queue", message.id, message.pop_receipt, "updated test message", 
      30)

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Comment : Obtenir des Options supplémentaires pour la file d’attente des Messages

Il existe deux méthodes que vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.

1. Vous pouvez obtenir un lot de messages.

2. Vous pouvez définir un délai d’expiration de l’invisibilité plus longues ou plus courtes, permettant à votre code plus ou moins de temps à traiter entièrement chaque message.

Le code suivant montre comment utiliser le **liste\_messages()** méthode pour obtenir les 15 messages en un seul appel. Puis, il imprime et supprime chaque message. Il définit également le délai d’expiration de l’invisibilité à cinq minutes pour chaque message.

    azure_queue_service.list_messages("test-queue", 300
      {:number_of_messages => 15}).each do |m|
      puts m.message_text
      azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
    end

## <a name="how-to-get-the-queue-length"></a>Comment : Obtenir la longueur de la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans la file d’attente. La **obtenir\_file d’attente\_metadata()** méthode demande le service de file d’attente pour renvoyer le nombre approximatif de message et les métadonnées relatives à la file d’attente.

    message_count, metadata = azure_queue_service.get_queue_metadata(
      "test-queue")

## <a name="how-to-delete-a-queue"></a>Comment : Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’il contient, appelez le **Supprimer\_queue()** méthode sur l’objet de file d’attente.

    azure_queue_service.delete_queue("test-queue")

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du stockage de la file d’attente, cliquez sur ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- Visitez le [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Visitez le référentiel [Azure SDK pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub

Pour une comparaison entre le Service de file d’attente d’Azure abordées dans cet article et les files d’attente du Bus Azure Service mentionné dans l’article [comment utiliser les files d’attente de Bus de Service](/develop/ruby/how-to-guides/service-bus-queues/) , voir [files d’attente Azure et les files d’attente de Bus de Service - en comparaison et Contrasted](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
