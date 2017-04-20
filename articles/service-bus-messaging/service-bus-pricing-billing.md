<properties 
    pageTitle="Bus de service de tarification et de facturation | Microsoft Azure"
    description="Vue d’ensemble de la structure de tarification de Bus de Service."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/06/2016"
    ms.author="sethm" />

# <a name="service-bus-pricing-and-billing"></a>Bus de service de tarification et de facturation

Bus de service est proposé en versions de Basic, Standard et [Premium](service-bus-premium-messaging.md) . Vous pouvez choisir un niveau de service pour chaque espace de noms de service de Bus de Service que vous créez, et que cette sélection de couche s’applique à toutes les entités créées dans cet espace de noms.

>[AZURE.NOTE] Pour plus d’informations sur la tarification de Bus de Service en cours, voir la [page de tarification du Bus des services Azure](https://azure.microsoft.com/pricing/details/service-bus/)et la [FAQ de Bus de Service](service-bus-faq.md#service-bus-pricing).

Bus de service utilise deux compteurs suivants pour les files d’attente et rubriques/abonnements :

1. **Opérations de messagerie**: définis sous forme d’appels API par rapport à la file d’attente ou rubrique/abonnement points de terminaison de service. Ce compteur mesure remplacera les messages envoyés ou reçus comme unité principale de l’utilisation facturable pour les files d’attente et rubriques/abonnements.

2. **Connexions de demandée**: défini comme le nombre maximal de connexions permanentes par rapport aux files d’attente, les rubriques ou les abonnements au cours d’une période d’échantillonnage d’une heure donnée. Ce compteur s’applique uniquement dans la couche Standard, dans laquelle vous pouvez ouvrir des connexions supplémentaires (auparavant, les connexions ont été limitées à 100 par la file d’attente/rubrique/abonnement) pour un montant nominal par connexion.

La couche **Standard** présente des prix progressifs pour les opérations effectuées avec des files d’attente et rubriques/abonnements, entraînant basée sur le volume des remises de jusqu'à 80 % sur les niveaux d’utilisation plus élevés. Il existe également une couche Standard base des frais de 10 $ par mois, ce qui vous permet d’effectuer des opérations jusqu'à 12,5 millions par mois sans frais supplémentaires.

Le niveau de **prime** fournit une isolation de ressources au niveau du processeur et la mémoire afin que chaque charge de travail du client s’exécute de manière isolée. Ce conteneur de ressources est appelé une *unité de messagerie*. Chaque espace de noms de prime est allouée au moins une unité de messagerie. Vous pouvez acheter 1, 2 ou 4 unités de messagerie pour chaque espace de noms du Service Bus Premium. Une seule charge de travail ou d’une entité peut s’étendre sur plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté, bien que la facturation est en frais de taux quotidien ou de 24 heures. Il en résulte des performances prévisibles et reproductibles pour votre solution basée sur le Bus de Service. Non seulement cette efficacité n’est plus prévisible et disponibles, mais il est également plus rapide. Prime de Bus Service Azure s’appuie sur le moteur de stockage introduit dans Azure événement concentrateurs de messagerie. Avec la messagerie de la prime, des performances de pointe sont beaucoup plus rapide que la couche Standard.

Notez que les frais de base standard sont chargé une seule fois par mois et par abonnement Azure. Cela signifie qu’après avoir créé un Standard unique ou un espace de noms Bus de Service de niveau Premium, vous pourrez créer autant Standard ou Premium couche espaces de noms supplémentaires que vous le souhaitez sous ce même abonnement Azure, sans entraîner de frais de base supplémentaires.

Espaces de noms de Bus des services existants tous créés avant le 1 novembre 2014 ont été automatiquement placés dans la couche Standard. Cela garantit que vous continuez à avoir accès à toutes les fonctionnalités disponibles avec le Bus de Service. Par la suite, vous pouvez utiliser le [Azure portal classique][] pour rétrograder vers la couche de base si vous le souhaitez.

Le tableau suivant résume les différences fonctionnelles entre les niveaux de base et Standard/Premium.

|Capacité|Base|Standard/Premium|
|---|---|---|
|Files d’attente|Oui|Oui|
|Messages planifiées|Oui|Oui|
|Rubriques/abonnements|N°|Oui|
|Relais|N°|Oui|
|Transactions|N°|Oui|
|La déduplication|N°|Oui|
|Sessions|N°|Oui|
|Messages volumineux|N°|Oui|
|ForwardTo|N°|Oui|
|SendVia|N°|Oui|
|Connexions de courtage (incluses)|100 par l’espace de noms de Bus des services|1 000 par abonnement Azure|
|Connexions de courtage (excédent autorisé)|N°|Oui (facturable)|

## <a name="messaging-operations"></a>Opérations de messagerie

Dans le cadre du nouveau modèle de tarification, facturation de files d’attente et rubriques/abonnements change. Ces entités sont en transition à partir de facturation par message à facturation par opération. Une « opération » fait référence à un appel d’API apportée par rapport à un point de terminaison de service file d’attente ou rubrique/abonnement. Cela inclut les opérations d’état de gestion, envoyer/recevoir et session.

|Type d’opération|Description|
|---|---|
|Gestion des|Création, lecture, mise à jour, supprimer (CRUD) contre les rubriques/abonnements ou les files d’attente.|
|De messagerie|Envoi et réception de messages avec des files d’attente ou de rubriques/abonnements.|
|État de session|L’obtention ou la définition de l’état de session sur une file d’attente ou une rubrique/abonnement.|

Les prix suivants ont été efficaces commençant le 1 novembre 2014 :

|Base|Coût|
|---|---|
|Opérations|0,05 $ par millions d’opérations|

|Standard|Coût|
|---|---|
|Frais de base|10 $par mois|
|Tout d’abord 12,5 millions opérations par mois|Inclus|
|12,5 millions de-100 opérations par mois|0,80 $ par millions d’opérations|
|100 millions - 2 500 millions d’opérations par mois|0,50 $ par millions d’opérations|
|Plus de 2 500 millions opérations par mois|0,20 $ par millions d’opérations|

|Premium|Coût|
|---|---|
|Tous les jours|11.13 $ fixe le taux par unité de Message|

## <a name="brokered-connections"></a>Connexions de courtage

*Connexions de Brokered* prendre en charge les modèles d’utilisation client qui impliquent un grand nombre d’expéditeurs/récepteurs « connecté en permanence » par rapport à des files d’attente, des rubriques ou des abonnements. Connecté de manière permanente des expéditeurs/destinataires sont ceux qui se connectent à l’aide de AMQP ou HTTP avec un zéro non-réception du délai d’attente (par exemple, HTTP long d’interrogation). Les expéditeurs HTTP et les récepteurs avec un délai d’expiration immédiate ne génèrent pas de connexions contemporains.

Auparavant, les files d’attente et rubriques/abonnements devaient une limite de 100 connexions simultanées par URL. Le modèle de facturation actuel supprime la limite d’URL pour les files d’attente et rubriques/abonnements et implémente des quotas et contrôle sur les connexions de courtage à l’espace de noms de Bus de Service et les niveaux d’abonnement Azure.

La couche de base inclut et est strictement limitée à 100 connexions contemporains par espace de noms de Service Bus. Supérieur à ce nombre, les connexions seront rejetées dans la couche de base. La couche Standard supprime la limite par espace de noms et le compte de l’utilisation du regroupement de connexion courtage sur l’abonnement Azure. Dans la couche Standard, 1 000 connexions contemporains par abonnement Azure pourra gratuitement (au-delà de la charge de base). À l’aide de plus d’un total de 1 000 connexions de courtage sur le Bus de Service Standard de couche espaces de noms dans un abonnement Azure seront facturés selon un planning gradué, comme indiqué dans le tableau suivant.

|Connexions de courtage (couche Standard)|Coût|
|---|---|
|Premier 1 000/mois|Inclus dans frais de base|
|1 000 à 100 000/mois|0,03 $ par connexion/mois|
|100 000-500 000/mois|0,025 $ par connexion/mois|
|500 000/mois|0.015 $ par connexion/mois|

>[AZURE.NOTE] 1 000 connexions contemporains sont incluses dans la couche de messagerie Standard (via les frais de base) et peuvent être partagées entre toutes les files d’attente, des rubriques et des abonnements dans l’abonnement Azure associé.

<br />

>[AZURE.NOTE] La facturation est basée sur le nombre maximal de connexions simultanées et est calculé au prorata en fonction de toutes les heures sur 744 heures par mois.

|Niveau de prime
|---|
|Courtage de connexions ne seront pas imputées au niveau Premium.|

Pour plus d’informations sur les connexions contemporains, reportez-vous à la section [Forum aux questions](#faq) plus loin dans cette rubrique.

## <a name="relay"></a>Relais

Les relais sont disponibles uniquement dans les espaces de noms de niveau Standard. Dans le cas contraire, les quotas de connexion et de tarification des relais restent inchangés. Cela signifie que les relais continuera à être facturés sur le nombre de messages (pas d’opérations) et relayer les heures.

|Relais de tarification|Coût|
|---|---|
|Heures de relais|0,10 $ pour toutes les heures des 100 relais|
|Messages|0,01 $ pour chaque 10 000 messages.|

## <a name="faq"></a>FAQ

### <a name="how-is-the-relay-hours-meter-calculated"></a>Comment est calculé le compteur d’heures de relais ?

Consultez [cette rubrique](service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Que sont les demandé de connexions et comment obtenir facturé pour les ?

Une connexion de courtage est définie comme l’une des opérations suivantes :

1. Une connexion de AMQP à partir d’un client à une file d’attente de Bus de Service ou de la rubrique/abonnement.

2. Un appel HTTP pour recevoir un message d’une rubrique de Bus de Service ou de la file d’attente qui a une valeur de délai d’attente de réception supérieure à zéro.

Frais de Bus de service pour le nombre maximal de connexions simultanées contemporains qui dépassent la quantité incluse (1 000 dans la couche Standard). Pics sont mesurées sur une base horaire au prorata en divisant par 744 heures par mois et ajoutées pendant la période de facturation mensuelle. La quantité incluse (1 000 connexions par mois contemporains) est appliquée à la fin de la période de facturation par rapport à la somme des pics horaires calculé au prorata.

Par exemple :

1. Chacun des 10 000 périphériques se connecte via une connexion AMQP et reçoit des commandes à partir d’une rubrique de Bus de Service. Les périphériques envoient des événements de télémesure à un concentrateur d’événements. Si tous les périphériques de se connectent pour chaque jour de 12 heures, les frais de connexion suivantes s’appliquent (en plus de toutes les autres charges rubrique Bus de Service) : 10 000 connexions *12 heures* 31 jours / 744 = 5 000 demandé de connexions. Une fois l’indemnité mensuelle de 1 000 connexions contemporains, vous serait exigé pour 4 000 connexions de courtage, à la vitesse de 0,03 $ par connexion contemporains, pour un total de 120 $.

2. 10 000 périphériques de recevoir des messages d’une file d’attente de Bus de Service via le protocole HTTP, en spécifiant un délai d’attente différent de zéro. Si tous les périphériques de se connectent pendant 12 heures tous les jours, vous verrez les frais de connexion suivant (en plus de toutes les autres charges de Bus de Service) : 10 000 réception HTTP connexions *12 heures par jour* 31 jours / heures 744 = 5 000 dans les connexions.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Frais de courtage de connexion s’appliquent à des files d’attente et rubriques/abonnements ?

Oui. Il n’y a aucun frais de connexion pour l’envoi d’événements à l’aide de HTTP, quel que soit le nombre d’envoi des systèmes ou périphériques. Recevoir des événements avec HTTP à l’aide d’un délai d’attente est supérieur à zéro, parfois appelé « interrogation longue », génère des frais de courtage de connexion. Connexions de AMQP génèrent des frais de courtage de connexion que si les connexions sont utilisées pour envoyer ou recevoir. Notez que 100 connexions contemporains sont autorisées sans frais dans un espace de noms de base. C’est également le nombre maximal de connexions de courtage autorisées pour l’abonnement Azure. Les 1 000 premières connexions courtage sur tous les espaces de noms Standard dans un abonnement Azure sont incluses sans aucun frais supplémentaire (au-delà de la charge de base). Car ces allocations sont suffisants pour couvrir plusieurs scénarios de messagerie service-service, des frais de courtage de connexion généralement uniquement sont pertinentes si vous envisagez d’utiliser d’interrogation longue AMQP ou HTTP avec un grand nombre de clients ; par exemple, pour obtenir le flux d’événement plus efficace ou activer la communication bidirectionnelle avec de nombreux périphériques ou les instances de l’application.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la tarification de Bus de Service, voir la [page de tarification du Bus des services Azure](https://azure.microsoft.com/pricing/details/service-bus/).

- Consultez le [Forum aux questions du Bus de Service](service-bus-faq.md#service-bus-pricing) pour quelques questions courantes autour de bus de Service de tarification et de facturation.

[Azure portal classique]: http://manage.windowsazure.com