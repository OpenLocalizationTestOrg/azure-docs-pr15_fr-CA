<properties 
    pageTitle="Vue d’ensemble du service Bus AMQP avec Java | Microsoft Azure" 
    description="Découvrez comment utiliser Java avec les avancées Message Queuing Protocol (AMQP) 1.0 dans Azure." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="amqp-10-support-in-service-bus"></a>Prise en charge de AMQP 1.0 dans le Bus des services

Le service de cloud de Bus des services Azure et sur prem [Service Bus pour Windows Server (1.1 de Bus de Service)](https://msdn.microsoft.com/library/dn282144.aspx) prend en charge l’avancée Message Queueing Protocol (AMQP) 1.0. AMQP vous permet de créer entre plates-formes, applications hybrides à l’aide d’un protocole standard ouvert. Vous pouvez construire des applications à l’aide de composants qui sont générés à l’aide d’environnements et langages différents, et qui s’exécutent sur différents systèmes d’exploitation. Tous ces composants peuvent se connecter au Service Bus et en toute transparence échangent des messages de métiers structurés d’efficacité et de fidélité complet.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduction : Quelle est la AMQP 1.0 et pourquoi est-ce important ?

Généralement, les produits de middleware orienté message utilisent les protocoles propriétaires pour la communication entre les applications clientes et des courtiers. Cela signifie que, une fois que vous avez sélectionné le courtier de messagerie d’un fournisseur particulier, vous devez utiliser les bibliothèques de ce fournisseur pour se connecter à vos applications clientes pour ce service broker. Il en résulte un degré de dépendance de ce fournisseur, puisque le portage d’une application à un autre produit nécessite des modifications de code dans toutes les applications connectées. 

En outre, il est difficile de connexion messageries courtiers provenant de différents fournisseurs. Cela requiert généralement au niveau de l’application de transition pour déplacer les messages à partir d’un système à un autre et pour traduire leurs formats propriétaires de message. Il s’agit d’une exigence courante ; par exemple, lorsque vous devez fournir une nouvelle interface unifiée aux anciens systèmes disparates, ou intégrer la suite de la fusion des systèmes informatiques.

L’industrie des logiciels est une entreprise se déplacent rapidement ; nouveaux langages de programmation et les infrastructures d’application sont présentés à un rythme parfois déconcertante. De même, les exigences des systèmes informatiques évoluent dans le temps et les développeurs souhaitent tirer parti des dernières fonctionnalités de la plate-forme. Cependant, parfois le fournisseur de messagerie sélectionné ne gère pas ces plates-formes. Dans la mesure où les protocoles de messagerie sont propriétaires, il n’est pas possible pour les autres utilisateurs de fournir des bibliothèques pour ces nouvelles plates-formes. Par conséquent, vous devez utiliser les approches, telles que la création de passerelles ou ponts qui vous permettent de continuer à utiliser le produit de messagerie.

Le développement de l’avancée Message Queuing Protocol (AMQP) 1.0 a été motivé par ces problèmes. Elle a été créée à JP Morgan Chase, qui, à l’instar des sociétés de services financiers plus, sont de gros utilisateurs de middleware orienté message. L’objectif est simple : pour créer un protocole de messagerie sur une norme ouverte qui a permis de créer des applications basées sur le message à l’aide de composants créés à l’aide de différents langages, d’infrastructures et de systèmes d’exploitation, tous les à l’aide de composants de pointe à partir d’un large éventail de fournisseurs.

## <a name="amqp-10-technical-features"></a>Caractéristiques techniques de AMQP 1.0

AMQP 1.0 est un protocole de messagerie au niveau du câble, fiable et efficace que vous pouvez utiliser pour générer les robuste, multi-plateforme, applications de messagerie. Le protocole a un objectif simple : permet de définir le mécanisme de transfert sûr, fiable et efficace des messages entre deux parties. Les messages eux-mêmes sont codés à l’aide d’une représentation sous forme de données portables qui permet à des expéditeurs hétérogènes et les récepteurs d’échanger des messages commerciaux structurés à toujours une fidélité optimale. Voici un résumé des fonctionnalités plus importantes :

*    **Efficacité**: AMQP 1.0 est orienté sur une connexion de protocole qui utilise un codage binaire pour les instructions de protocole et les messages d’entreprise transféré. Il intègre des jeux sophistiqués de contrôle de flux pour optimiser l’utilisation du réseau et des composants connectés. Ceci dit, le protocole a été conçu pour un équilibre entre efficacité, flexibilité et l’interopérabilité.
*    **Reliable**: protocole le AMQP 1.0 permet de messages à échanger avec une gamme de garanties de fiabilité, d’incendie et oubli à fiable, exactement-une fois confirmée livraison.
*    **Flexible**: AMQP 1.0 est un protocole souple qui peut être utilisé pour prendre en charge des topologies différentes. Le même protocole peut être utilisé pour les communications de client à client et client-à-un courtier broker-à-un courtier.
*    **Broker-modèle indépendant**: spécification du AMQP 1.0 ne rend pas les exigences sur le modèle de messagerie utilisé par un courtier. Cela signifie qu’il est possible d’ajouter facilement la prise en charge AMQP 1.0 aux courtiers de messagerie existants.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 est un Standard (avec d’une majuscule ')

AMQP 1.0 a été en développement depuis 2008 par un groupe de plus de 20 entreprises, les fournisseurs de technologie et des entreprises de l’utilisateur final. Pendant ce temps, les entreprises d’utilisateur ont contribué à leurs besoins réels et les fournisseurs de technologies ont évolué au protocole pour répondre à ces exigences. Tout au long du processus, les fournisseurs ont participé ateliers dans lesquels ils ont collaboré pour valider l’interopérabilité entre leurs implémentations.

En octobre 2011, le travail de développement a basculé vers un comité technique au sein de l’organisation de l’avancement de structurée d’informations Standards OASIS () et la version Standard de OASIS AMQP 1.0 a été publié en octobre 2012. Les sociétés suivantes ont participé au comité technique au cours du développement de la norme :

*    **Les fournisseurs de technologies**: Axway Software, Huawei Technologies, IIT logiciel, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, progression logiciel, Red Hat, SITA, logiciel AG, systèmes de Solace, VMware, WSO2, Zenika.
*    **Les entreprises utilisateur**: Bank of America, crédit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Parmi les avantages couramment cités des normes ouvertes :

*    Moins de chance de tiers
*    Interopérabilité
*    Une large gamme de bibliothèques et outillage
*    Protection contre l’obsolescence
*    Disponibilité du personnel expérimenté
*    Risque faible et facile à gérer

## <a name="amqp-10-and-service-bus"></a>Bus 1.0 et le Service de AMQP

Prise en charge de AMQP 1.0 dans le Bus des services Azure signifie que vous pouvez exploiter le Bus des services queuing et publication/abonnement de courtage de fonctionnalités de messagerie à partir d’une gamme de plates-formes à l’aide d’un protocole binaire efficace. En outre, vous pouvez créer des applications composées les composants créés à l’aide d’un mélange de langues, les infrastructures et les systèmes d’exploitation.

La figure suivante illustre un exemple de déploiement dans lequel les clients Java sous Linux, écrit à l’aide du standard Java Message Service (JMS) API et des clients .NET s’exécutant sous Windows, échangent des messages via le Bus des services à l’aide de AMQP 1.0.

![][0]

**Figure 1 : Exemple de scénario de déploiement présentant entre les plates-formes de messagerie à l’aide du Service de Bus et AMQP 1.0**

En ce moment les bibliothèques clientes suivantes sont connus pour fonctionner avec le Bus de Service :

| Langue | Bibliothèque                                                                       |
|----------|-------------------------------------------------------------------------------|
| Java     | Client Apache Qpid Java Message Service (JMS)<br/>Client de IIT logiciel SwiftMQ Java |
| C        | Apache Qpid PROTONS-C                                                          |
| PHP      | Apache Qpid PROTONS-PHP                                                        |
| Python   | Apache Qpid PROTONS-Python                                                     |


**La figure 2 : Tableau des bibliothèques de client AMQP 1.0**

Pour plus d’informations sur la façon d’obtenir et d’utiliser ces bibliothèques avec Bus des services, consultez le [Guide du développeur de Service Bus AMQP][]. Consultez la section [étapes suivantes](service-bus-java-amqp-overview.md#next-steps) pour obtenir des liens vers davantage d’informations.

## <a name="summary"></a>Résumé

*    AMQP 1.0 est un protocole de messagerie ouvert et fiable qui vous permet de créer entre plates-formes, applications hybrides. AMQP 1.0 est une norme OASIS.
*    Prise en charge AMQP 1.0 est désormais disponible dans le Bus des services Azure ainsi que Service Bus pour Windows Server (1.1 de Bus de Service). Tarification est la même que pour les protocoles existants.

## <a name="next-steps"></a>Étapes suivantes

Visitez les liens suivants pour en savoir plus sur la prise en charge AMQP dans des Bus de Service.

*    [L’utilisation de AMQP 1.0 avec l’API .NET du Bus de Service](service-bus-dotnet-advanced-message-queuing.md)
*    [L’utilisation de l’API Java Message Service (JMS) avec Bus de Service & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Service du Guide du développeur de Bus AMQP][]
*    [Spécification de Advanced Message Queuing Protocol (AMQP) Version 1.0 OASIS](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)

[0]: ./media/service-bus-java-amqp-overview/Example1.png
[Service du Guide du développeur de Bus AMQP]: service-bus-amqp-dotnet.md

 
