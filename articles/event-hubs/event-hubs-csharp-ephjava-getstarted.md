<properties
    pageTitle="Mise en route avec les concentrateurs d’événements dans C# | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide de concentrateurs d’événement Azure ; envoi d’événements dans C# et leur réception dans Java à l’aide de la EventProcessorHost."
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
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Mise en route avec les concentrateurs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Concentrateurs d’événements est un service qui traite de grandes quantités de données d’événement (télémétrie) à partir d’applications et de périphériques connectés. Après avoir collecté les données dans les concentrateurs d’événement, vous pouvez stocker les données à l’aide d’un cluster de stockage ou transformer à l’aide d’un fournisseur d’analytique en temps réel. Cette fonctionnalité de collecte et de traitement des événements à grande échelle est un composant clé des architectures d’applications modernes, y compris de l’Internet des objets (IoT).

Ce didacticiel explique comment utiliser le portail classique Azure pour créer un concentrateur d’événements. Il vous montre également comment collecter des messages à un concentrateur d’événements à l’aide d’une application console écrite en C# et comment les récupérer en parallèle à l’aide de la bibliothèque Java événement processeur hôte.

Pour terminer ce didacticiel, vous allez besoin des éléments suivants :

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un compte Azure actif. <br/>Si vous n’en avez pas, vous pouvez créer un compte gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez le projet de **récepteur** .

    ![][21]

2.  Exécutez le projet de **l’expéditeur** .

    ![][22]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé une application qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez atteindre les scénarios suivants :

- Un [exemple d’application qui utilise des concentrateurs d’événement][]complète.
- L’exemple de [mise à l’échelle traitement avec les concentrateurs d’événement de l’événement][] .
- [Vue d’ensemble des concentrateurs événements][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec les concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
