<properties
    pageTitle="Mise en route avec les concentrateurs d’événement dans C | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide de concentrateurs d’événement Azure ; envoi d’événements de C et de leur réception dans Java à l’aide de l’hôte du processeur d’événements."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Mise en route avec les concentrateurs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Concentrateurs d’événement est un système de réception hautement évolutive pouvant d’admission des millions d’événements par seconde, permettant à une application de traiter et analyser les quantités énormes de données produites par vos applications et de périphériques connectés. Une fois collectées dans les concentrateurs d’événement, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analytique en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la [vue d’ensemble des concentrateurs de l’événement][].

Dans ce didacticiel, vous allez apprendre à réceptionner des messages à un concentrateur d’événements à l’aide d’une application de console dans C et les récupérer en parallèle à l’aide de la bibliothèque C# [Événement processeur hôte][] .

Pour terminer ce didacticiel, vous aurez besoin des éléments suivants :

+ Un environnement de développement C. Pour ce didacticiel, nous supposerons la pile gcc sur une [Machine virtuelle Linux de Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) avec 14.04 d’Ubuntu. Vous trouverez des instructions pour d’autres environnements de liens externes.

+ Express de Microsoft Visual Studio pour Windows

+ Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, reportez-vous à la section <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">D’essai Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez le projet de **récepteur** .

    ![][21]

2.  Exécutez le programme de **l’expéditeur** et notez les événements s’affichent dans la fenêtre du récepteur.

    ![][24]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé une application qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez atteindre les scénarios suivants :

- Un [exemple d’application qui utilise des concentrateurs d’événement][]complète.
- L’exemple de [mise à l’échelle traitement avec les concentrateurs d’événement de l’événement][] .
- [Vue d’ensemble des concentrateurs événements][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Événement processeur hôte]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec les concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
