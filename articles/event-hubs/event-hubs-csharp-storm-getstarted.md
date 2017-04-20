<properties
    pageTitle="Mise en route avec les concentrateurs d’événements dans C# avec Apache tempête | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide de concentrateurs d’événement Azure ; envoi d’événements dans C# et leur réception dans un cluster de tempête d’Apache."
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
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Mise en route avec les concentrateurs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Concentrateurs d’événement est un système de réception hautement évolutive pouvant d’admission des millions d’événements par seconde, permettant à une application de traiter et analyser les quantités énormes de données produites par vos applications et de périphériques connectés. Une fois collectées dans les concentrateurs d’événement, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analytique en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la [vue d’ensemble des concentrateurs de l’événement].

Dans ce didacticiel, vous allez apprendre à réceptionner des messages à un concentrateur d’événements à l’aide d’une application de console dans C# et les récupérer en parallèle à l’aide de Apache tempête.

Pour terminer ce didacticiel, vous aurez besoin des éléments suivants :

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un environnement de développement Java configuré pour s’exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous supposerons que [Éclipse](https://www.eclipse.org/)

+ Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, reportez-vous à la section <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">D’essai Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez la classe **LogTopology** à partir d’Eclipse, puis attendre qu’il démarre les récepteurs pour toutes les partitions.

2.  Exécutez le projet de **l’expéditeur** , appuyez sur **entrée** dans la fenêtre de la console et afficher les événements s’affichent dans la fenêtre de récepteur.

    ![][22]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé une application qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez atteindre les scénarios suivants :

- Un [exemple d’application qui utilise des concentrateurs d’événement][]complète.
- L’exemple de [mise à l’échelle traitement avec les concentrateurs d’événement de l’événement][] .

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec les concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 