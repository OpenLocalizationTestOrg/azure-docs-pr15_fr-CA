<properties 
    pageTitle="Architecture de Bus de service | Microsoft Azure"
    description="Décrit le message et le relais de l’architecture de Bus des services Azure de traitement."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>Architecture de Bus de service

Cet article décrit le message et le relais de l’architecture de Bus des services Azure de traitement.

## <a name="service-bus-scale-units"></a>Unités d’échelle de Bus des services

Bus de service est organisé en *unités de l’échelle*. Une unité d’échelle est une unité de déploiement et contient tous les composants requis exécutent le service. Chaque région déploie une ou plusieurs unités d’échelle de Bus de Service.

Bus de Service d’un espace de noms est mappé à une unité d’échelle. L’unité d’échelle gère tous les types d’entités de Service Bus : relais et entités de messagerie contemporains (files d’attente, rubriques, abonnements). Une unité d’échelle de Bus de Service se compose des éléments suivants :

- **Un ensemble de nœuds de la passerelle.** Nœuds de passerelle authentifient les demandes entrantes et traiter les demandes de relais. Chaque nœud de la passerelle est une adresse IP publique.

- **Un ensemble de nœuds de service Broker pour la messagerie.** Nœuds de messagerie Service Broker pour traitent les demandes relatives aux entités de messagerie.

- **Magasin d’une passerelle.** La banque de passerelle contient les données de chaque entité qui est définie dans cette unité d’échelle. La banque de la passerelle est implémentée sur une base de données SQL Azure.

- **Plusieurs banques de messagerie.** Les banques de messagerie conservent les messages de toutes les files d’attente, les rubriques et les abonnements qui sont définis dans cette unité d’échelle. Il contient également toutes les données d’abonnement. Sauf si la valeur est [partitionnée d’entités de messagerie](service-bus-partitioning.md) est activée, une file d’attente ou une rubrique est mappé à une seule banque d’informations. Les abonnements sont stockés dans la même banque d’informations comme leur sujet parent. À l’exception des Bus de Service [Premium de messagerie](service-bus-premium-messaging.md), les banques de messagerie sont implémentées sur les bases de données SQL Azure.

## <a name="containers"></a>Conteneurs

Chaque entité de messagerie est affectée à un conteneur spécifique. Un conteneur est une construction logique qui utilise une seule messagerie magasin à toutes les données pertinentes pour ce conteneur. Chaque conteneur est affecté à un nœud de courtier de messagerie. En règle générale, il existe des conteneurs de plus que la messagerie Service Broker pour les nœuds. Par conséquent, chaque nœud broker messagerie charge plusieurs conteneurs. La distribution de conteneurs à un nœud de courtier de messagerie est organisée de sorte que tous les nœuds de courtier de messagerie sont également chargés. Si la charge de motif de modifications (par exemple, parmi les conteneurs d’est très occupés), ou si un nœud de courtier de messagerie est momentanément indisponible, les conteneurs sont redistribués entre les nœuds de courtier de messagerie.

## <a name="processing-of-incoming-messaging-requests"></a>Traitement des demandes de messagerie entrants

Lorsqu’un client envoie une demande au Service Bus, l’équilibreur de charge Azure achemine vers un des nœuds de la passerelle. Le nœud de passerelle autorise la demande. Si la demande concerne une entité messagerie (file d’attente, rubrique, abonnement), le nœud de passerelle recherche l’entité dans le magasin de passerelle et détermine dans quelle banque de messagerie se trouve l’entité. Il recherche ensuite le nœud de service broker messagerie traite actuellement ce conteneur et envoie la demande à ce nœud courtier de messagerie. Le nœud de courtier de messagerie traite la requête et met à jour de l’état d’entité dans le magasin de conteneur. Le nœud de courtier de messagerie envoie ensuite la réponse vers le nœud de la passerelle, qui envoie une réponse appropriée au client qui a émis la demande d’origine.

![Traitement des demandes de messagerie entrants](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Traitement des demandes entrantes de relais

Lorsqu’un client envoie une demande au Service Bus, l’équilibreur de charge Azure achemine vers un des nœuds de la passerelle. Si la demande est une demande à l’écoute, le nœud de passerelle crée un nouveau relais. Si la demande est une demande de connexion pour un relais spécifique, le nœud de passerelle transfère la demande de connexion pour le nœud de passerelle qui possède le relais. Le nœud de passerelle qui possède le relais envoie une demande de rendez-vous pour l’écoute du client, demandant l’écouteur pour créer un canal temporaire vers le nœud de passerelle qui a reçu la demande de connexion.

Lors de la connexion de relais est établie, les clients peuvent échanger des messages via le nœud de passerelle qui est utilisé pour les rendez-vous.

![Traitement des demandes entrantes de relais](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu une vue d’ensemble de l’architecture de Bus des services, mise en route visitez les liens suivants :

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Principes de base de Bus des services](service-bus-fundamentals-hybrid-solutions.md)
- [Une solution de messagerie en file d’attente à l’aide de files d’attente de Bus de Service](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
