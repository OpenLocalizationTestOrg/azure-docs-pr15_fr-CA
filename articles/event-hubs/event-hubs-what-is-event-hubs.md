<properties
    pageTitle="Ce qui est des concentrateurs d’événement Azure ? | Microsoft Azure"
    description="Vue d’ensemble et une description des événements Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="sethm"/>

# <a name="what-is-azure-event-hubs"></a>Ce qui est des concentrateurs d’événement Azure ?

Les concentrateurs événement Azure est un service de pénétration de données hautement évolutive qui peut traiter des millions d’événements par seconde afin que vous puissiez traiter et analyser les quantités énormes de données produites par vos applications et de périphériques connectés. Événement concentrateurs agit comme « l’entrée principale » pour un pipeline des événements, et une fois que les données sont collectées dans un concentrateur de l’événement, il peut être transformé et stockées à l’aide de n’importe quel fournisseur d’analytique en temps réel ou des cartes de stockage/le traitement par lots. Événement concentrateurs dissocie la production d’un flux d’événements à partir de la consommation de ces événements, afin que les consommateurs d’événements peuvent accéder aux événements selon leur propre calendrier. Pour plus d’informations et de détails techniques, consultez la [vue d’ensemble des concentrateurs de l’événement](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Fonctions de concentrateurs d’événements

Concentrateurs d’événements est un service qui fournit des événements et le traitement de télémétrie à grande échelle, avec une latence faible et haute fiabilité de traitement des événements. Ce service est particulièrement utile pour :

- Instrumentation de l’application
- Expérience de l’utilisateur ou du traitement du workflow
- Scénarios de l’Internet des objets (IoT)

Certaines autres fonctionnalités de concentrateurs d’événement clées inclut le comportement de suivi dans les applications mobiles, le trafic des informations à partir de batteries de serveurs web, capture les événements dans le jeu dans les jeux de console ou de télémétrie recueillies à partir des machines industrielles ou de véhicules connectés.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations détaillées sur les concentrateurs d’événements, consultez les rubriques suivantes.

- [Vue d’ensemble des concentrateurs événements](event-hubs-overview.md)
- [Concentrateurs d’événement guide de programmation](event-hubs-programming-guide.md)
- [Forum aux questions sur disponibilité et le support de concentrateurs d’événement](event-hubs-availability-and-support-faq.md)
- Mise en route avec un [didacticiel de concentrateurs d’événement][]
- Un [exemple d’application qui utilise des concentrateurs d’événement][] complète

[Didacticiel de concentrateurs d’événement]: event-hubs-csharp-ephcs-getstarted.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
