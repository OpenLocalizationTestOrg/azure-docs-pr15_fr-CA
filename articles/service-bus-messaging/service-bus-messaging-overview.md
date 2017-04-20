<properties
    pageTitle="Vue d’ensemble de la messagerie Service Bus | Microsoft Azure"
    description="Messagerie du Bus de service : livraison de données flexible dans le nuage"
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
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Bus des services de messagerie : livraison flexible des données dans le nuage

Bus des services Microsoft Azure est un service de livraison fiable d’informations. L’objectif de ce service est de rendre les communications plus facile. Lorsque deux ou plusieurs parties souhaitez échanger des informations, ils ont besoin d’un mécanisme de communication. Bus de service est un mécanisme de communication de courtage ou tiers. Ceci est similaire à un service postal dans le monde physique. Services postaux rend très facile d’envoyer les différents types de lettres et de packages avec une variété de garanties de livraison, n’importe où dans le monde.

Similaire au service postal livraison de lettres, Bus de Service est de diffusion d’informations flexible à la fois l’expéditeur et le destinataire. Le service de messagerie permet de s’assurer que l’information est transmise même si les deux parties ne sont jamais les deux en ligne en même temps, ou si elles ne sont pas disponibles au même moment. De cette façon, la messagerie est similaire à l’envoi d’une lettre, non négociée dans la communication est similaire à placer un appel téléphonique (ou utilisation d’un appel téléphonique à - avant un appel en attente et appelant l’ID, qui sont beaucoup plus comme messagerie contemporains).

L’expéditeur du message peut également exiger des diverses caractéristiques de livraison, y compris des transactions, la détection des doublons, d’expiration basés sur l’heure et le traitement par lots. Ces modèles ont des analogies postal ainsi : Répétez la livraison, signature requise, modification de l’adresse ou rappel.

Bus de service prend en charge deux modèles de messagerie distinctes : *relais* et *courtage de messagerie*.

## <a name="service-bus-relay"></a>Relais de Bus de service

Le composant [relais](../service-bus-relay/service-bus-relay-overview.md) du Bus de Service est un service centralisé (mais extrêmement avec équilibrage de charge) qui prend en charge une variété de différents protocoles de transport et les normes de services Web. Cela inclut le SOAP, WS-* et même le reste. Le [service de relais](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) fournit une gamme d’options de connectivité de relais différent et peut aider à négocier des connexions peer-to-peer directes lorsque cela est possible. Bus de service est optimisé pour les développeurs .NET qui utilisent le Windows Communication Foundation (WCF), à la fois en ce qui concerne les performances et la facilité d’utilisation et fournit un accès complet à son service de relais via des interfaces SOAP et le reste. Cela rend possible pour SOAP ni reste environnement de programmation pour s’intégrer à un Bus de Service.

Le service de relais prend en charge le traditionnel à sens unique de messagerie, la messagerie de demande/réponse et messagerie peer-to-peer. Il prend également en charge les distribution des événements au niveau de la portée d’Internet pour activer la publication-abonnement de scénarios et communication bidirectionnelle socket pour une efficacité accrue de point à point. Dans le modèle de messagerie relayé, un service local se connecte au service de relais via un port de sortie et crée un socket bidirectionnelle pour communication liée à une adresse particulière rendezvous. Le client peut ensuite communiquer avec le service sur site en envoyant des messages pour le service de relais qui ciblent l’adresse rendezvous. Le service de relais sera ensuite « relais » messages au service local via le socket bidirectionnel déjà en place. Le client ne doit pas une connexion directe au service sur site, ni qu’il est nécessaire de savoir où réside le service et le service sur site n’a pas besoin de ports entrants ouvrir sur le pare-feu.

Vous lancez la connexion entre votre service local et le service de relais, à l’aide d’un ensemble de liaisons de « Relais » de WCF. En coulisses, les liaisons de relais mappent pour le transport d’éléments de liaison conçus pour créer des composants de canal WCF qui s’intègrent avec le Bus de Service dans le nuage.

Relais de Bus de service offre de nombreux avantages, mais il requiert le serveur et le client à la fois en ligne en même temps afin d’envoyer et de recevoir des messages. Ce n’est pas optimal pour la communication HTTP-style, dans lesquelles les demandes peuvent être pas généralement longue durée de vie, ni pour les clients qui se connectent qu’occasionnellement, comme les navigateurs, les applications mobiles et ainsi de suite. Dans la communication de découplé prend en charge la messagerie et possède ses propres avantages ; les serveurs et les clients peuvent se connecter lorsque cela est nécessaire pour exécuter leurs opérations de manière asynchrone.

## <a name="brokered-messaging"></a>Demandé de la messagerie

À la différence du type de relais, [courtage de messagerie](service-bus-queues-topics-subscriptions.md) peut être considéré comme asynchrones ou « temporellement découplée. » (Expéditeurs) les producteurs et les consommateurs (récepteurs) n’ont pas à être en ligne en même temps. L’infrastructure de messagerie fiable stocke les messages dans un « courtier » (par exemple, une file d’attente) jusqu'à ce que la partie (consommateur) est prête à recevoir les. Ainsi, les composants de l’application distribuée à être déconnecté, soit volontairement ; par exemple, pour la maintenance, ou suite à une panne de composant, sans affecter la totalité du système. En outre, l’application réceptrice ne peut traiter que d’apparaître en ligne pendant certaines heures de la journée, telles qu’un système de gestion de stock seulement nécessaire à l’exécution à la fin de la journée.

Les principaux composants de l’infrastructure de messagerie contemporains Bus de Service sont des files d’attente, des rubriques et des abonnements.  La principale différence est que les rubriques prennent en charge les fonctionnalités de publication et d’abonnement qui peuvent être utilisées pour basé sur contenu routage et la remise de la logique sophistiquée, notamment l’envoi à plusieurs destinataires. Ces composants permettent de nouveaux scénarios de messagerie asynchrones, tels que le découplage temporelle, publication et d’abonnement et équilibrage de la chargement. Pour plus d’informations sur ces entités de messagerie, reportez-vous à [abonnements, des rubriques et des files d’attente de Bus de Service](service-bus-queues-topics-subscriptions.md).

À l’instar de l’infrastructure de relais, les capacités de messagerie contemporains sont fournie pour les programmeurs WCF et.NET Framework, ainsi que via le reste.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Bus des services de messagerie, consultez les rubriques suivantes.

- [Principes de base de Bus des services](service-bus-fundamentals-hybrid-solutions.md)
- [Abonnements, des rubriques et des files d’attente de Bus de Service](service-bus-queues-topics-subscriptions.md)
- [L’utilisation de files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md)
- [Comment faire pour utiliser des abonnements et des rubriques du Bus des services](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
