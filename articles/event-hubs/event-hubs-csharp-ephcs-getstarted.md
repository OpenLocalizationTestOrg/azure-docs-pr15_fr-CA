<properties
    pageTitle="Mise en route avec les concentrateurs d’événements dans C# | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide de concentrateurs d’événement Azure avec C# et l’utilisation de la EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Mise en route avec les concentrateurs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Concentrateurs d’événements est un service qui traite de grandes quantités de données d’événement (télémétrie) à partir d’applications et de périphériques connectés. Après avoir collecté les données dans les concentrateurs d’événement, vous pouvez stocker les données à l’aide d’un cluster de stockage ou transformer à l’aide d’un fournisseur d’analytique en temps réel. Cette fonctionnalité de collecte et de traitement des événements à grande échelle est un composant clé des architectures d’applications modernes, y compris de l’Internet des objets (IoT).

Ce didacticiel explique comment utiliser le portail classique Azure pour créer un concentrateur d’événements. Il vous montre également comment collecter des messages à un concentrateur d’événements à l’aide d’une application console écrite en C# et comment les récupérer en parallèle à l’aide de la bibliothèque C# [Événement processeur hôte][] .

Pour terminer ce didacticiel, vous allez besoin des éléments suivants :

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un compte Azure actif. Si vous n’en avez pas, vous pouvez créer un compte gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À partir de dans Visual Studio, ouvrez le projet de **récepteur** créé précédemment.

2. Avec le bouton droit de la solution du **récepteur** , puis cliquez sur **Ajouter**, puis cliquez sur **Projet existant**.
 
3. Recherchez le fichier Sender.csproj existant, puis double-cliquez dessus pour l’ajouter à la solution.
 
4. À nouveau, avec le bouton droit de la solution de **récepteur** et puis cliquez sur **Propriétés**. La page de propriétés du **récepteur** s’affiche.

5. Cliquez sur le **Projet de démarrage**, puis cliquez sur le bouton de **plusieurs projets de démarrage** . Définir la zone **d’Action** pour les projets à la fois le **récepteur** et **l’émetteur** pour **Démarrer**.

    ![][19]

6. Cliquez sur **dépendances du projet**. Dans la zone **projets** , cliquez sur **l’expéditeur**. Dans la zone **dépend** , assurez-vous que **récepteur** est activée.

    ![][20]

7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés** .

1.  Appuyez sur F5 pour exécuter le projet **récepteur** à partir de Visual Studio, puis attendez que les récepteurs pour toutes les partitions de démarrage.

    ![][21]

2.  Le projet de **l’expéditeur** s’exécutera automatiquement. Appuyez sur la touche **entrée** dans la fenêtre de console et afficher les événements s’affichent dans la fenêtre du récepteur.

    ![][22]

Appuyez sur **Ctrl + C** dans la fenêtre de **l’expéditeur** pour mettre fin à l’application de l’expéditeur, puis appuyez sur **entrée** dans la fenêtre de récepteur à fermer cette application.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé une application qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez atteindre les scénarios suivants :

- Un [exemple d’application qui utilise des concentrateurs d’événement][]complète.
- L’exemple de [mise à l’échelle traitement avec les concentrateurs d’événement de l’événement][] .
- [Vue d’ensemble des concentrateurs événements][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Événement processeur hôte]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec les concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
