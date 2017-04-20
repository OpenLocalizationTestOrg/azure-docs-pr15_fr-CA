<properties
    pageTitle="Mise en route avec les concentrateurs d’événement avec C et Apache tempête | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide de concentrateurs d’événement Azure ; envoi d’événements de C et de leur réception dans un cluster de tempête d’Apache."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="java"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Mise en route avec les concentrateurs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Concentrateurs d’événement est un système de réception hautement évolutive pouvant d’admission des millions d’événements par seconde, permettant à une application de traiter et analyser les quantités énormes de données produites par vos applications et de périphériques connectés. Une fois collectées dans les concentrateurs d’événement, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analytique en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez [vue d’ensemble des concentrateurs de l’événement].

Dans ce didacticiel, vous allez apprendre à réceptionner des messages à un concentrateur d’événements à l’aide d’une application de console dans C et les récupérer en parallèle à l’aide de Apache tempête.

Pour terminer ce didacticiel, vous devrez les éléments suivants :

+ Un environnement de développement C. Pour ce didacticiel, nous supposerons la pile gcc sur une [Machine virtuelle Linux de Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) avec 14.04 d’Ubuntu. Vous trouverez des instructions pour d’autres environnements de liens externes.

+ Un environnement de développement Java configuré pour s’exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous supposerons [Eclipse](https://www.eclipse.org/).

+ Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez la classe **LogTopology** à partir d’Eclipse, puis attendre qu’il démarre les récepteurs pour toutes les partitions.

2.  Exécutez le programme de **l’expéditeur** et afficher les événements s’affichent dans la fenêtre du récepteur.

    ![][23]

> [AZURE.NOTE] Dans ce didacticiel uniquement, utilisez tempête en mode local à des fins de développement. Reportez-vous à la [vue d’ensemble de la tempête de HDInsight] et de la documentation officielle de [Tempête d’Apache] pour plus d’informations des modèles et des déploiements de tempête.

## <a name="next-steps"></a>Étapes suivantes

Les ressources suivantes sont disponibles pour le développement d’applications intégrant les concentrateurs d’événement et de tempête.

- [Analyse des données de capteur avec tempête et HDInsight][] est un didacticiel de scénario complet à l’aide d’événements concentrateurs, tempête et HBase pour ajouter des données de capteur dans un cluster Hadoop.
- [Diffusion en continu de développer des applications de traitement de données avec SCP.NET et C# sur la tempête et HDInsight][] est un didacticiel sur l’écriture de pipelines Storm à l’aide de C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md

[Tempête de Apache]: https://storm.incubator.apache.org
[Vue d’ensemble de la tempête de HDInsight]: ../hdinsight/hdinsight-storm-overview.md/
[Analyse des données de capteur avec tempête et HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Développer des applications de traitement de données avec SCP.NET et C# sur la tempête et des HDInsight de diffusion en continu]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
