<properties 
    pageTitle="Comment enregistrer des événements à des concentrateurs d’événement Azure dans la gestion des API Azure | Microsoft Azure" 
    description="Découvrez comment enregistrer des événements à des concentrateurs d’événement Azure dans Azure API de gestion." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Comment enregistrer des événements à des concentrateurs d’événement Azure dans la gestion des API Azure

Les concentrateurs événement Azure est un service de pénétration de données hautement évolutive qui peut traiter des millions d’événements par seconde afin que vous puissiez traiter et analyser les quantités énormes de données produites par vos applications et de périphériques connectés. Événement concentrateurs agit comme « l’entrée principale » pour un pipeline des événements, et une fois que les données sont collectées dans un concentrateur de l’événement, il peut être transformé et stockées à l’aide de n’importe quel fournisseur d’analytique en temps réel ou des cartes de stockage/le traitement par lots. Événement concentrateurs dissocie la production d’un flux d’événements à partir de la consommation de ces événements, afin que les consommateurs d’événements peuvent accéder aux événements selon leur propre calendrier.

Cet article est associée à la vidéo [d’Intégration Azure API de gestion avec les concentrateurs d’événement](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) et explique comment enregistrer des événements de gestion de l’API à l’aide de concentrateurs d’événement Azure.

## <a name="create-an-azure-event-hub"></a>Créer un concentrateur d’événements Azure

Pour créer un nouveau concentrateur d’événements, ouvrez une session dans [Azure portal classique](https://manage.windowsazure.com) et cliquez sur **Nouveau**->**Application Services**->**Service Bus**->**Concentrateur d’événements**->**Création rapide**. Entrez un nom d’événement concentrateur, région, sélectionnez un abonnement et sélectionnez un espace de noms. Si vous n’avez pas créé précédemment d’un espace de noms que vous pouvez en créer un en tapant un nom dans la zone de texte **Namespace** . Une fois que toutes les propriétés sont configurées, cliquez sur **créer un nouveau concentrateur d’événement** pour créer le concentrateur d’événements.

![Créer le concentrateur d’événements][create-event-hub]

Ensuite, accédez à l’onglet **configurer** pour votre nouveau concentrateur d’événements et créez deux **stratégies d’accès partagé**. Nommez premier **envoi** et lui **Envoyer** des autorisations.

![Stratégie d’envoi][sending-policy]

Nom de la seconde **réception**, attribuez-lui les autorisations **d’écouter** et cliquez sur **Enregistrer**.

![Stratégie de destinataire][receiving-policy]

Chaque stratégie d’accès partagé permet d’envoyer et de recevoir des événements à partir du concentrateur d’événements et les applications. Pour accéder à ces stratégies, les chaînes de connexion, accédez à l’onglet de **tableau de bord** du concentrateur de l’événement, puis cliquez sur **informations de connexion**.

![Chaîne de connexion][event-hub-dashboard]

La chaîne de connexion **d’envoi** est utilisée lors de l’enregistrement des événements, et la chaîne de connexion de **réception** utilisée lors du téléchargement des événements à partir du concentrateur de l’événement.

![Chaîne de connexion][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Créer un journal de gestion de l’API

Maintenant que vous avez un concentrateur d’événements, l’étape suivante consiste à configurer un [journal](https://msdn.microsoft.com/library/azure/mt592020.aspx) dans votre service de gestion de l’API afin qu’il puisse consigner les événements sur le concentrateur d’événements.

Journaux de gestion de l’API sont configurés à l’aide de l' [API REST de gestion API](http://aka.ms/smapi). Avant d’utiliser l’API REST pour la première fois, passez en revue les [conditions requises](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) et assurez-vous d’avoir [activé l’accès à l’API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Pour créer un journal, effectuez une requête PUT HTTP en utilisant le modèle d’URL suivant.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   Remplacer `{your service}` avec le nom de votre instance de service de gestion de l’API.
-   Remplacer `{new logger name}` avec le nom souhaité pour votre nouvel enregistreur d’événements. Vous allez référencer ce nom lorsque vous configurez la stratégie [journal-à-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Ajoutez les en-têtes suivants à la demande.

-   Type de contenu : application/json
-   Autorisation : SharedAccessSignature uid =...
    -   Pour obtenir des instructions sur la génération de la `SharedAccessSignature` consultez [Azure API gestion reste API authentification](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Spécifiez le corps de la demande en utilisant le modèle suivant.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`doit être définie sur `AzureEventHub`.
-   `description`Fournit une description facultative du journal et peut être une chaîne de longueur nulle si vous le souhaitez.
-   `credentials`contient le `name` et `connectionString` de votre événement de supervision Azure.

Lorsque vous effectuez la demande, si le journal est créé un code d’état de `201 Created` est retourné. 

>[AZURE.NOTE] Pour les autres codes de retour possibles et leurs raisons, voir [Création d’un journal](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Pour voir comment effectuer d’autres opérations telles que la liste mise à jour et suppression, reportez-vous à la documentation d’entité de [journal](https://msdn.microsoft.com/library/azure/mt592020.aspx) .

## <a name="configure-log-to-eventhubs-policies"></a>Configurer les stratégies du journal-à-eventhubs

Une fois que le journal est configuré dans la gestion de l’API, vous pouvez configurer vos stratégies de journal-à-eventhubs pour enregistrer les événements de votre choix. La stratégie de journal-à-eventhubs peut être utilisée dans la section stratégie entrante ou la stratégie sortante.

Pour configurer des stratégies, ouvrez une session dans le [Azure portal classique](https://manage.windowsazure.com), naviguer dans votre service de gestion de l’API et cliquez sur **portail de publisher** ou **Gérer** pour accéder au portail de publisher.

![Portail de Publisher][publisher-portal]

Cliquez sur **stratégies** dans le menu de gestion de l’API sur la gauche, sélectionnez le produit de votre choix et les API et cliquez sur **Ajouter une stratégie**. Dans cet exemple, nous ajoutons une stratégie à l' **API de l’écho** dans le produit **illimité** .

![Ajouter une stratégie][add-policy]

Placez votre curseur dans le `inbound` stratégie et cliquez sur la stratégie du **journal des EventHub** pour insérer le `log-to-eventhub` modèle de déclaration de stratégie.

![Éditeur de stratégie][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Remplacer `logger-id` avec le nom du journal de gestion des API que vous avez configuré à l’étape précédente.

Vous pouvez utiliser toute expression qui renvoie une chaîne comme valeur pour le `log-to-eventhub` élément. Dans cet exemple, une chaîne contenant la date et heure, nom du service, id de demande, demander l’adresse ip et nom de l’opération est enregistrée.

Cliquez sur **Enregistrer** pour enregistrer la configuration de la stratégie mise à jour. Dès qu’elle est enregistrée la stratégie est active et les événements sont consignés dans le concentrateur d’événements désignés.

## <a name="next-steps"></a>Étapes suivantes

-   En savoir plus sur les concentrateurs d’événement Azure
    -   [Mise en route avec les concentrateurs d’événement Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Recevoir des messages avec EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Concentrateurs d’événement guide de programmation](../event-hubs/event-hubs-programming-guide.md)
-   Pour en savoir plus sur l’intégration de la gestion de l’API et les concentrateurs d’événement
    -   [Référence d’entité de journal](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [référence de la stratégie de journal-à-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [Surveillez votre API avec une API Azure Management, événement et les concentrateurs Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Suivre une procédure pas à pas la vidéo

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png






