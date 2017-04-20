<properties
    pageTitle="Vue d’ensemble des relais de Bus de service | Microsoft Azure"
    description="Vue d’ensemble de relais de Bus de Service."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>Vue d’ensemble des relais de Bus des services

Un composant majeur du Bus de Service est un service centralisé (mais hautement à charge équilibrée) *relais* qui vous permet de créer des applications hybrides qui s’exécutent dans un centre de données Azure et dans votre propre environnement d’entreprise locaux.  Le relais de Bus de Service prend en charge une variété de différents protocoles de transport et les normes de services web. Cela inclut le SOAP, WS-* et même le reste. Le service de relais facilite vos applications hybrides en vous permettant d’exposer des services Windows Communication Foundation (WCF) qui résident au sein d’un réseau d’entreprise vers le nuage public, sans avoir à ouvrir la connexion via un pare-feu, ou nécessitent de changements intrusifs dans une infrastructure de réseau d’entreprise en toute sécurité. 

![Concepts de relais](./media/service-bus-relay-overview/sb-relay-01.png)

Le service de relais prend en charge le traditionnel à sens unique de messagerie, la messagerie de demande/réponse et messagerie peer-to-peer. Il prend également en charge la distribution d’événement au niveau de la portée d’internet pour activer la communication bidirectionnelle socket pour une efficacité accrue de point à point et les scénarios de publication et d’abonnement. 

Dans le modèle de messagerie relayé, un service local se connecte au service de relais via un port de sortie et crée un socket bidirectionnelle pour communication liée à une adresse particulière rendezvous. Le client peut ensuite communiquer avec le service sur site en envoyant des messages pour le service de relais qui ciblent l’adresse rendezvous. Le service de relais sera ensuite « relais » messages au service local via le socket bidirectionnel déjà en place. Le client ne doit pas une connexion directe au service sur site, il n’est pas nécessaire de savoir où réside le service et le service sur site n’a pas besoin de ports entrants ouvrir sur le pare-feu.

Vous lancez la connexion entre votre service sur site et le service de relais à l’aide d’un ensemble de liaisons de « Relais » de WCF. En coulisses, les liaisons de relais mappent à de nouveaux éléments de liaison de transport conçus pour créer des composants de canal WCF qui s’intègrent avec le Bus de Service dans le nuage. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le relais de Bus des services, consultez les rubriques suivantes.

- [Vue d’ensemble architecturale de Service Azure Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Comment faire pour utiliser le service de relais de Bus de Service](service-bus-dotnet-how-to-use-relay.md)

 