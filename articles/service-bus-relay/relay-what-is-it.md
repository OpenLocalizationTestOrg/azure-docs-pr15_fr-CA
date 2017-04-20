<properties
    pageTitle="En quoi consiste le relais Azure ? | Microsoft Azure"
    description="Vue d’ensemble de relais Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>En quoi consiste le relais d’Azure ?

Service de relais Azure facilite vos applications hybrides en vous permettant d’exposer des services qui résident au sein d’un réseau d’entreprise vers le nuage public, sans avoir à ouvrir la connexion via un pare-feu, ou nécessitent de changements intrusifs dans une infrastructure de réseau d’entreprise en toute sécurité. Relais prend en charge une variété de différents protocoles de transport et les normes de services web.

Le service de relais prend en charge les traditionnels à sens unique, de demande/réponse et le trafic de peer-to-peer. Il prend également en charge la distribution d’événement au niveau de la portée d’internet pour activer la communication bidirectionnelle socket pour une efficacité accrue de point à point et les scénarios de publication et d’abonnement. 

Dans le modèle de transfert de données relayé, un service local se connecte au service de relais via un port de sortie et crée un socket bidirectionnelle pour communication liée à une adresse particulière rendezvous. Le client peut ensuite communiquer avec le service sur site en envoyant le trafic pour le service de relais qui ciblent l’adresse rendezvous. Le service de relais sera ensuite « relais » les données pour le service sur site à travers un socket bidirectionnelle dédié à chaque client. Le client ne doit pas une connexion directe au service sur site, il n’est pas nécessaire de savoir où réside le service et le service sur site n’a pas besoin de ports entrants ouvrir sur le pare-feu.

Les éléments de la clé de fonction fournis par relais sont une communication bidirectionnelle, sans tampon au-delà des limites de réseau avec limitation de type TCP, découverte de point de terminaison, état de connectivité et recouverts de sécurisation des postes clients. Possibilités de relais diffèrent des technologies d’intégration au niveau du réseau telles que VPN dans qu’elle peut être étendue à un point de terminaison d’application unique sur un seul ordinateur, tandis que la technologie VPN est beaucoup plus gênant car il repose sur la modification de l’environnement réseau.

Relais Azure a deux fonctions :

1. [Les connexions hybride](#hybrid-connections) - utilise les Sockets Web standard ouverts permettant des scénarios multi-plateformes

2. [WCF relais](#wcf-relays) - utilise Windows Communication Foundation pour activer des appels de procédure à distance

Les connexions hybride et WCF relais activer une connexion sécurisée à assests qui existent au sein d’un réseau d’entreprise. Utilisation de l’un sur l’autre dépend de vos besoins détaillés ci-dessous :

|                                    | Relais WCF | Connexions hybride |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **Cœur de .NET**                      |           |         x          |
| **.NET Framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **Protocole ouvert normalisé**  |           |         x          |
| **Plusieurs modèles de programmation RPC** |           |         x          |
*<sub>Par disponibilité générale</sub>

## <a name="hybrid-connections"></a>Connexions hybride

Les connexions de relais Azure hybride est une évolution sécurisée, protocole ouvert des fonctionnalités relais existantes qui peuvent être mises en oeuvre sur n’importe quelle plate-forme et dans n’importe quel langage qui possède une fonctionnalité base WebSocket, incluant explicitement l’API WebSocket en commun les navigateurs web. Les connexions hybride est basé sur HTTP et le protocole WebSocket.

## <a name="wcf-relays"></a>Relais WCF

Le relais de WCF fonctionne pour le.NET Framework complet (NETFX) et de WCF. Vous lancez la connexion entre votre service sur site et le service de relais à l’aide d’un ensemble de liaisons de « Relais » de WCF. En coulisses, les liaisons de relais mappent à de nouveaux éléments de liaison de transport conçus pour créer des composants de canal WCF qui s’intègrent avec le Bus de Service dans le nuage.

## <a name="service-history"></a>Historique des services

Les connexions hybride plants à l’ancienne, également appelée fonction « Services BizTalk » qui a été conçue sur le relais de WCF Azure Service Bus. La nouvelle fonctionnalité hybride connexions complète le relais WCF existant et de ces capacités de deux service seront trouvent côte à côte dans le service de relais dans l’avenir prévisible ; elles partagent une passerelle commune, mais dans le cas contraire des implémentations différentes.

Relais de WCF est le relais hérité offre que de nombreux clients peuvent déjà utiliser avec leurs modèles de programmation WCF.

## <a name="next-steps"></a>Étapes suivantes :

- [Forum aux questions de relais](relay-faq.md)
- [Créer un espace de noms](relay-create-namespace-portal.md)
- [Mise en route de .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Mise en route de nœud](relay-hybrid-connections-node-get-started.md)