<properties 
    pageTitle="Vue d’ensemble des exemples de Bus des services de messagerie | Microsoft Azure"
    description="Classe et décrit le Bus de Service d’échanges d’échantillons avec des liens vers chaque."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-messaging-samples"></a>Exemples de messagerie de Bus des services

Les exemples de messagerie de Bus de Service illustrent des fonctionnalités clés de service de [Bus des services de messagerie](https://azure.microsoft.com/services/service-bus/) (cloud) et [Bus de Service pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Cet article classe et décrit les exemples disponibles, avec des liens vers chacun.

>[AZURE.NOTE] Exemples de Bus de service ne sont pas installés avec le Kit de développement logiciel. Pour obtenir des exemples, visitez la [page des exemples Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>En outre, est un ensemble de mises à jour de Bus de Service d’échanges d’échantillons [ici](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (à ce jour, ils ne sont pas décrites dans cet article).  

Pour des exemples de relais, consultez [exemples de relais de Bus de Service](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Bus des services de messagerie

Les exemples suivants montrent comment écrire des applications qui utilisent le Bus des services de messagerie.

Notez que les exemples d’échanges requièrent une chaîne de connexion pour accéder à votre espace de noms du Bus de Service.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Pour obtenir une chaîne de connexion pour le Bus des services Azure

1. Ouvrez une session sur le [portail Azure](http://portal.azure.com).

1. Dans la colonne de gauche, cliquez sur **Service Bus**.

1. Cliquez sur le nom de votre espace de noms dans la liste.

3. Dans l’espace de noms « lame », cliquez sur **stratégies d’accès partagé**.

4. De la lame de **stratégies d’accès partagé** , cliquez sur **RootManageSharedAccessKey**.

6. Copier la chaîne de connexion dans le Presse-papiers.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Pour obtenir une chaîne de connexion pour le Bus de Service pour Windows Server

1. Exécutez l’applet de commande PowerShell suivante :

    ```
    get-sbClientConfiguration
    ```

2. Collez la chaîne de connexion dans le fichier App.config de l’échantillon.

### <a name="getting-started-samples"></a>L’obtention d’échantillons démarrés

Ces exemples décrivent les fonctionnalités de messagerie de base.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Mise en route : Messagerie avec files d’attente](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Montre comment utiliser le Bus des services Microsoft Azure pour envoyer et recevoir des messages d’une file d’attente.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Mise en route : La messagerie avec les rubriques](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Montre comment utiliser le Bus des services Microsoft Azure pour envoyer et recevoir des messages à partir d’une rubrique comprenant plusieurs abonnements.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|

### <a name="exploring-features"></a>Exploration des fonctions

Les exemples suivants illustrent diverses fonctionnalités du Bus de Service.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Fournisseurs de jeton HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Montre les différentes méthodes d’authentification d’un client HTTP/reste avec le Bus de Service.|2.1|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Client du Bus de service HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Montre comment envoyer des messages et recevoir des messages de Bus de Service via HTTP/repos.|2.3|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Transfert automatique de Bus de service](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Montre comment transférer automatiquement les messages d’une file d’attente, un abonnement ou une file d’attente de lettres mortes dans une autre file d’attente ou une rubrique. Il montre également comment envoyer un message dans une file d’attente ou une rubrique via une file d’attente de transfert.|2.3|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Par courtier de messagerie : Exemple de Session de canal WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Montre comment utiliser le Bus des services Microsoft Azure à l’aide de canaux de Windows Communication Foundation (WCF). L’exemple illustre l’utilisation de canaux WCF pour envoyer et recevoir des messages via une file d’attente de Bus de Service. L’exemple illustre un session et non-session de communication sur le Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Demandé de la messagerie : Transactions](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Montre comment utiliser le Bus de Service Microsoft Azure messagerie fonctionnalités au sein d’une étendue de transaction afin d’assurer des opérations de messagerie, les lots sont validées atomiquement.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Messagerie contemporains : Opérations de gestion à l’aide d’autres](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Montre comment effectuer des opérations de gestion sur le Bus de Service à l’aide de reste.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Fournisseur de ressources reste API](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Montre comment utiliser les nouvelles API de reste Bus RDFE Service pour gérer les espaces de noms et d’entités de messagerie.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Par courtier de messagerie : Exemple de Session de Service WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Montre comment utiliser le Bus des services Microsoft Azure en utilisant le modèle de service WCF. L’exemple illustre l’utilisation du modèle de service WCF pour accomplir basée sur une session de communication via une file d’attente de Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Courtage de messagerie : Réponse de demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Montre comment utiliser le Bus de Service Microsoft Azure et la fonctionnalité de demande/réponse. L’exemple montre la simples clients et les serveurs communiquant via une file d’attente de Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Par courtier de messagerie : file d’attente de lettres mortes](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Montre comment utiliser le Bus des services Microsoft Azure et la fonctionnalité de messagerie « file d’attente de lettres mortes ». L’exemple montre un simple expéditeur et le récepteur communiquent via une file d’attente de Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Messagerie de courtage : Différé des Messages](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Montre comment utiliser la fonction de report de message de Bus des services Microsoft Azure. L’exemple montre un simple expéditeur et le récepteur communiquent via une file d’attente de Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Messagerie contemporains : Messages de Session](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Montre comment utiliser les fonctionnalités de la Session de messagerie et de Bus des services Microsoft Azure. L’exemple montre la simples expéditeurs et les récepteurs de communication via une file d’attente de Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Courtage de messagerie : Rubrique de réponse de demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Montre comment implémenter le modèle demande/réponse à l’aide des abonnements et des rubriques du Bus des services Microsoft Azure. L’exemple montre la simples clients et les serveurs communiquant via une rubrique de Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Courtage de messagerie : File d’attente de réponses pour les demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Montre comment utiliser le Bus des services Microsoft Azure et la fonctionnalité de demande/réponse. L’exemple montre la simples clients et les serveurs communiquant via deux files d’attente de Bus de Service.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Par courtier de messagerie : Détection des doublons](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Montre comment utiliser la détection de messages répliqués de Bus des services Microsoft Azure des files d’attente. Il crée deux files d’attente, avec activé la détection des doublons et autres sans la détection des doublons.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Courtage de messagerie : La messagerie asynchrone](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Montre comment utiliser le Bus des services Microsoft Azure pour envoyer et recevoir des messages de manière asynchrone à partir d’une file d’attente. La file d’attente fournit découplée communication asynchrone entre un expéditeur et un nombre quelconque de destinataires (ici, un seul récepteur).|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Messagerie de courtage : Les filtres avancés](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Montre comment utiliser le Bus des services Microsoft Azure publication/abonnement filtres avancés. Il crée une rubrique et 3 abonnements avec les définitions de filtre différent, envoie des messages de la rubrique et reçoit tous les messages à partir des abonnements.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Par courtier de messagerie : Récupération de Messages](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Montre comment utiliser la fonctionnalité de lecture préalable de messages Microsoft Azure Service Bus. Il montre comment utiliser la fonction de lecture préalable des messages lors de la réception.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|

## <a name="service-bus-reference-tools"></a>Outils de référence de Bus des services

Les exemples suivants illustrent diverses fonctionnalités du service.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Explorateur de Bus de service](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|L’Explorateur de Bus de Service permet aux utilisateurs de se connecter à un espace de noms de service de Bus de services et de gérer les entités de messagerie d’une manière facile. L’outil fournit des fonctionnalités avancées telles que les fonctionnalités d’importation/exportation et la possibilité de tester les entités de messagerie et des services de relais.|1.8|Microsoft Azure Bus de Service ; Bus des services pour Windows Server|
|[Autorisation : SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Cet exemple montre comment créer et gérer les identités de service Microsoft Azure Active Directory contrôle d’accès (également appelé Service de contrôle d’accès ou ACS) pour une utilisation avec le Bus de Service.|N/A|Bus de Service Microsoft Azure|

## <a name="next-steps"></a>Étapes suivantes

Consultez les rubriques suivantes pour une vue d’ensemble conceptuelle du Bus de Service.

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Architecture de Bus de service](service-bus-architecture.md)
- [Principes de base de Bus des services](service-bus-fundamentals-hybrid-solutions.md)
