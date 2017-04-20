<properties 
    pageTitle="Événement concentrateurs questions fréquemment posées (FAQ) | Microsoft Azure"
    description="Forum aux questions sur les événements concentrateurs."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>Forum aux questions sur les concentrateurs événement

Événement concentrateurs fournit à grande échelle d’admission, persistance et traitement des événements de données à partir de sources de données à haut débit et/ou des millions de périphériques. Associées à des rubriques et des files d’attente de Bus de Service, événement concentrateurs permet des déploiements de commande et de contrôle permanents pour les scénarios [Internet des objets (IoT)](https://azure.microsoft.com/services/iot-hub/) .

Cet article décrit les informations de tarification et répond à certaines questions fréquemment posées sur les concentrateurs d’événement :

## <a name="pricing-information"></a>Informations sur la tarification

Pour plus d’informations sur la tarification des concentrateurs d’événements, consultez les [Détails de tarification de concentrateurs de l’événement](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated"></a>Comment sont calculés les événements entrants de concentrateurs d’événement ?

Chaque événement envoyé à un concentrateur d’événements est considérée comme un message facturable. Un *événement d’entrée* est défini comme une unité de données sont inférieure ou égale à 64 Ko. Tout événement qui est inférieure ou égale à 64 Ko la taille est considérée comme un événement facturable. Si l’événement est supérieure à 64 Ko, le nombre d’événements facturables est calculé en fonction de la taille de l’événement, d’un multiple de 64 Ko. Par exemple, un événement de 8 Ko envoyé au concentrateur d’événements est facturé comme un événement, mais un message de 96 Ko envoyé au concentrateur d’événements est facturé comme deux événements.

Consommé à partir d’un concentrateur d’événements, comme les événements ainsi que des opérations de gestion et contrôlent des appels tels que les points de contrôle, ne sont pas comptées comme des événements d’entrée facturables, mais provisionnement à l’allocation d’unité de débit.

## <a name="what-are-event-hubs-throughput-units"></a>Quelles sont les unités de débit de concentrateurs d’événement ?

Vous sélectionnez explicitement des unités de débit de concentrateurs de l’événement, via le portail Azure ou modèles de gestionnaire de ressources de concentrateurs de l’événement. Unités de débit s’appliquent à tous les concentrateurs d’événement dans un espace de noms de concentrateurs de l’événement, et chaque unité de débit donne droit à l’espace de noms pour les fonctionnalités suivantes :

- Jusqu'à 1 Mo par seconde de pénétration événements (envoyé à un concentrateur d’événements), mais pas plus de 1000 événements de pénétration, des opérations de gestion ou un contrôle les appels API par seconde.

- Jusqu'à 2 Mo par seconde de sortie événements (consommés à partir d’un concentrateur d’événements).

- Jusqu'à 84 Go de stockage des événements (suffisant pour la période de rétention de 24 heures par défaut).

Unités de débit de concentrateurs événement sont facturées toutes les heures, en fonction du nombre maximal d’unités sélectionnées pendant l’heure donnée.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Comment sont appliquées les limites du débit de concentrateurs d’événement unité ?

Si le débit total de pénétration ou le taux d’événements de pénétration total sur tous les concentrateurs d’événement dans un espace de noms dépasse les allocations d’unité de débit total, les expéditeurs seront accélérés et recevez des erreurs indiquant que l’utilisateur a dépassé le quota de pénétration.

Si le débit total de sortie ou le taux de sortie événement total sur tous les concentrateurs d’événement dans un espace de noms dépasse les allocations d’unité de débit total, sont limitées et les récepteurs reçoivent des erreurs indiquant que le quota de sortie a été dépassé. Entrant et sortant des quotas sont appliquées séparément, afin qu’aucun expéditeur ne risque de ralentir la consommation d’événements, ni un récepteur peut empêcher d’événements soient envoyées à un concentrateur d’événements.

Notez que la sélection de l’unité débit est indépendante du nombre de partitions de concentrateurs de l’événement. Alors que chaque partition offre un débit maximal de 1 Mo par seconde pénétration (avec un maximum de 1000 événements par seconde) et de 2 Mo par seconde sortant, il n’y a aucun frais fixes pour les partitions eux-mêmes. Les frais sont pour les unités de débit agrégé sur tous les concentrateurs d’événement dans un espace de noms de concentrateurs de l’événement. Avec ce modèle, vous pouvez créer des partitions suffisant pour prendre en charge la charge maximale prévue pour leurs systèmes, sans encourir les frais d’unité de débit jusqu'à ce que la charge de l’événement sur le système en fait nécessite des nombres de débit plus élevés, sans avoir à modifier la structure et l’architecture de vos systèmes de mesure que la charge sur le système augmente.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Y a-t-il une limite sur le nombre d’unités de débit qui peuvent être sélectionnés ?

Il y a un quota par défaut de 20 unités de débit par espace de noms. Vous pouvez demander un quota plus important d’unités de débit en déposant un ticket de support. Au-delà de la limite d’unité de débit de 20, les offres sont disponibles en 20 et 100 unités de débit. Notez que l’utilisation de plus de 20 unités de débit supprime la possibilité de modifier le nombre d’unités de débit sans classement un ticket de support.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe-t-il des frais de conservation des événements de l’événement concentrateurs de plus de 24 heures ?

Le niveau d’événement concentrateurs Standard permet de rétention de message périodes de plus de 24 heures, pour un maximum de 30 jours. Si la taille du nombre total d’événements stockées dépasse l’allocation de stockage pour le nombre d’unités de débit sélectionné (84 Go par unité de débit), la taille est supérieure à l’indemnité est facturée au taux publié les Blob Azure storage. L’allocation de stockage dans chaque unité de débit couvre tous les coûts de stockage pour les périodes de rétention de 24 heures (par défaut) même si l’unité de débit est de l’indemnité de pénétration maximale.

## <a name="what-is-the-maximum-retention-period"></a>Quelle est la période de rétention maximale ?

Couche Standard de concentrateurs d’événement prend actuellement en charge d’une période de rétention maximale de 7 jours. Notez que les concentrateurs d’événement ne sont pas destinées comme un magasin de données permanentes. Périodes de rétention supérieures à 24 heures sont conçus pour les scénarios dans lesquels il est utile de relire un flux d’événements dans les systèmes mêmes ; par exemple, de formation ou de vérifier un nouveau modèle d’apprentissage machine sur les données existantes.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Comment la taille de stockage des concentrateurs d’événement est calculée et facturée ?

La taille totale de tous les événements stockées, y compris toute charge interne pour les en-têtes de l’événement ou sur les structures de stockage de disque dans tous les concentrateurs de l’événement, est mesurée au cours de la journée. À la fin de la journée, la taille maximale de stockage est calculée. L’indemnité journalière de stockage est calculée en fonction du nombre minimal d’unités de débit qui a été sélectionnée au cours de la journée (chaque unité du débit fournit une indemnité de 84 Go). Si la taille totale dépasse l’allocation de stockage quotidien calculé, le stockage excédentaire est facturé à l’aide des taux de stockage Azure Blob (au taux **Local de stockage redondant** ).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>Puis-je utiliser une seule connexion AMQP pour envoyer et recevoir à partir de concentrateurs d’événements et des files d’attente de Bus de Service/rubriques ?

Oui, tant que tous les concentrateurs d’événements, les files d’attente et les rubriques sont dans le même espace de noms. En tant que tel, vous pouvez implémenter une connectivité bidirectionnelle, contemporains pour de nombreux périphériques, avec des latences de moins, de manière rentable et hautement évolutive.

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Frais de courtage de connexion s’appliquent à des concentrateurs d’événement ?

Des expéditeurs, des frais de connexion, s’appliquent uniquement lorsque le protocole AMQP est utilisé. Il n’y a aucun frais de connexion pour l’envoi d’événements à l’aide de HTTP, quel que soit le nombre d’envoi des systèmes ou périphériques. Si vous prévoyez d’utiliser des AMQP (par exemple, pour obtenir le flux d’événement plus efficace ou pour activer la communication bidirectionnelle dans les scénarios de commande et de contrôle IoT), reportez-vous à la page [d’informations de tarification du Bus de Service](https://azure.microsoft.com/pricing/details/service-bus/) pour plus d’informations sur ce qui constitue une connexion contemporains, et comment ils sont contrôlés.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Quelle est la différence entre les événements concentrateurs base et niveaux Standard ?

Couche Standard de concentrateurs d’événement fournit des fonctionnalités au-delà de ce qui est disponible dans la base de concentrateurs événement et certains systèmes concurrents. Ces fonctionnalités incluent des périodes de conservation de plus de 24 heures et la possibilité d’utiliser une seule connexion AMQP pour envoyer des commandes à un grand nombre de périphériques avec des latences de moins, ainsi que pour envoyer des télémétrie à partir de ces périphériques concentrateurs de l’événement. Pour la liste des fonctionnalités, consultez les [Détails de tarification de concentrateurs de l’événement](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Disponibilité géographique

Concentrateurs de l’événement est disponible dans les régions suivantes :

|Geo|Régions|
|---|---|
|États-Unis|États-Unis centre, les États-Unis, américains Extrême-Orient 2, États-Unis centre sud, ouest des États-Unis|
|Europe|Europe du Nord, Europe de l’ouest|
|Asie-Pacifique|Asie de l’est, Asie du Sud-est|
|Japon|Japon East, West du Japon|
|Brésil|Sud du Brésil|
|Australie|L’Australie nord-est, Sud-est de l’Australie|

## <a name="support-and-sla"></a>Prise en charge et les contrats SLA

Support technique pour les concentrateurs d’événement est disponible via les [forums de la Communauté](https://social.msdn.microsoft.com/forums/azure/home). Prise en charge de gestion de la facturation et abonnement est proposé gratuitement.

Pour en savoir plus sur notre SLA, reportez-vous à la page de [Contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concentrateurs d’événements, consultez les articles suivants :

- [Vue d’ensemble des concentrateurs de l’événement][].
- Un [exemple d’application qui utilise des concentrateurs d’événement][]complète.

[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
