<properties
    pageTitle="Prime de Bus de service et la vue d’ensemble des niveaux de prix de messagerie Standard | Microsoft Azure"
    description="Prime de Bus de service et de messagerie Standard"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Prime de Bus de service et les niveaux de messagerie Standard 

Bus des services de messagerie, qui inclut les entités telles que des files d’attente de messagerie et rubriques, combine les riches capacités de la messagerie d’entreprise de publication-abonnement sémantique à grande échelle du nuage. Bus de service de messagerie est utilisée comme l’épine dorsale de communication pour de nombreuses solutions de cloud sophistiquées.

Le niveau de *prime* de Bus des services de messagerie répond à des demandes client courantes autour d’échelle, de performances et de disponibilité pour les applications critiques. Bien que les groupes de fonctionnalités sont presque identiques, ces deux niveaux de Bus des services de messagerie est conçus pour servir les différents cas d’usage.

Des différences de haut niveau sont mis en surbrillance dans le tableau ci-dessous.

| Premium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Haut débit                       | Débit variable            |
| Performances prévisibles               | Latence de variable               |
| Prix prévisibles                   | Tarification variable de retenues |
| Possibilité de mise à l’échelle vers la charge de travail | N/A                            |
| Taille de message > 256 Ko                  | Taille du message est de 256 Ko          |

**Prime de Bus de service de messagerie** fournit une isolation de ressources au niveau du processeur et la mémoire afin que chaque charge de travail du client s’exécute de manière isolée. Ce conteneur de ressources est appelé une *unité de messagerie*. Chaque espace de noms de prime est allouée au moins une unité de messagerie. Vous pouvez acheter 1, 2 ou 4 unités de messagerie pour chaque espace de noms du Service Bus Premium. Une seule charge de travail ou d’une entité peut s’étendre sur plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté, bien que la facturation est en frais de taux quotidien ou de 24 heures. Il en résulte des performances prévisibles et reproductibles pour votre solution basée sur le Bus de Service.

Non seulement cette efficacité n’est plus prévisible et disponibles, mais il est également plus rapide. Prime de Bus de service de messagerie s’appuie sur le moteur de stockage introduit dans [Azure événement concentrateurs](https://azure.microsoft.com/services/event-hubs/). Avec la messagerie de la prime, des performances de pointe sont beaucoup plus rapide qu’avec la couche Standard.

## <a name="premium-messaging-technical-differences"></a>Différences techniques de messagerie de prime

Voici quelques différences entre les niveaux de messagerie Standard et de la prime.

### <a name="partitioned-queues-and-topics"></a>Rubriques et partitionnées de files d’attente

Rubriques et partitionnées de files d’attente sont pris en charge dans les échanges de prime, mais ils ne fonctionnent pas de la même manière que dans les couches Standard et Basic de Bus des services de messagerie. Prime de messagerie n’utilise pas SQL sous la forme d’un magasin de données et n’est plus la concurrence possible de ressources associé à une plate-forme partagée. Par conséquent, il n’est pas nécessaire de partitionnement. En outre, le nombre de partition a été modifié à partir de 16 partitions de messagerie Standard à 2 partitions de prime. Ayant deux partitions garantit la disponibilité et un nombre plus approprié pour l’environnement d’exécution de prime. Pour plus d’informations sur le partitionnement, consultez [les rubriques et les files d’attente de partitionnées](service-bus-partitioning.md).

### <a name="express-entities"></a>Entités Express

Prime de messagerie s’exécute dans un environnement d’exécution complètement isolé, entités express ne sont pas pris en charge dans les espaces de noms Premium. Pour plus d’informations sur la fonctionnalité expresse, consultez la propriété [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Bus des services de messagerie, consultez les rubriques suivantes.

- [Présentation de prime de Bus de Service Azure messagerie (publication de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Présentation de prime de Bus de Service Azure messagerie (Channel 9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [L’utilisation de files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md)
