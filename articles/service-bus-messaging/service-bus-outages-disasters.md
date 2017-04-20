<properties 
    pageTitle="Isolation des applications de Bus de Service contre les pannes et de sinistres | Microsoft Azure"
    description="Décrit les techniques que vous pouvez utiliser pour protéger les applications contre une panne de Bus de Service potentielle."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Méthodes conseillées pour l’isolation des applications contre les interruptions de Service Bus et les sinistres

Les applications critiques doivent fonctionner en permanence, même en présence d’interruptions non planifiées ou d’urgence. Cette rubrique décrit les techniques que vous pouvez utiliser pour protéger les applications de Service Bus contre une panne potentielle du service ou de sinistre.

Une panne est définie comme l’indisponibilité temporaire de Bus des services Azure. La panne peut affecter certains composants du Bus de Service, par exemple une banque d’information, ou même la totalité du datacenter. Une fois que le problème a été résolu, Bus de Service est de nouveau disponible. En règle générale, une panne n’entraîne pas la perte de messages ou d’autres données. Un exemple d’une défaillance d’un composant est l’indisponibilité d’un magasin de messagerie particulier. Un exemple d’une panne sur un centre de données est une coupure de centre de données ou un commutateur de réseau de datacenter défectueux. Une panne peut durer de quelques minutes à quelques jours.

Un incident est défini comme la perte définitive d’une unité d’échelle de Bus de Service ou d’un centre de données. Le centre de données peut ou ne peut pas devenir disponible à nouveau. En règle générale un incident provoque la perte de certains ou de tous les messages ou d’autres données. Incendie, inondation ou séisme sont des exemples de sinistres.

## <a name="current-architecture"></a>Architecture actuelle

Bus de service utilise plusieurs banques de messagerie pour stocker les messages qui sont envoyés à des files d’attente ou de rubriques. Un non partitionné une file d’attente ou une rubrique est affecté à une banque d’informations. Si cette banque d’informations n’est pas disponible, toutes les opérations sur cette file d’attente ou la rubrique échouera.

Toutes les entités de messagerie Service Bus (files d’attente, rubriques, relais) se trouvent dans un espace de noms de service, qui est affilié à un centre de données. Bus de service ne permet pas de géo-réplication automatique des données, pas plus qu’il permet de couvrir plusieurs centres de données, un espace de noms.

## <a name="protecting-against-acs-outages"></a>Protection contre les pannes de l’ACS

Si vous utilisez des informations d’identification de l’ACS et ACS n’est plus disponible, les clients peuvent obtenir n’a plus de jetons. Les clients qui ont un jeton au moment QU'ACS tombe en panne peuvent continuer à utiliser des Bus de Service jusqu'à l’expiration de jetons. La durée de vie de jeton par défaut est de 3 heures.

Protection contre les pannes de l’ACS, utiliser les jetons de Signature de l’accès partagé (SAS). Dans ce cas, le client s’authentifie directement avec le Bus de Service en signant un jeton minted automatique avec une clé secrète. Les appels à ACS ne sont plus nécessaires. Pour plus d’informations sur les jetons SAS, voir [Bus de Service d’authentification][].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Protection des files d’attente et les rubriques contre les défaillances de la banque d’informations de messagerie

Un non partitionné une file d’attente ou une rubrique est affecté à une banque d’informations. Si cette banque d’informations n’est pas disponible, toutes les opérations sur cette file d’attente ou la rubrique échouera. D’autre part, une file d’attente partitionnée, constitué de plusieurs fragments. Chaque fragment est stocké dans un autre magasin de messagerie. Lorsqu’un message est envoyé à une file d’attente partitionnée ou la rubrique, Bus de Service affecte le message à l’un des fragments. Si la banque d’informations correspondante n’est pas disponible, le Bus des services écrit le message à un fragment différent, si possible. Pour plus d’informations sur les entités partitionnées, voir [partitionnée d’entités de messagerie][].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Protection contre les pannes de centre de données ou de catastrophes naturelles

Pour permettre un basculement sur incident entre deux centres de données, vous pouvez créer un espace de noms de service de Bus de Service dans chaque centre de données. Par exemple, l' espace de noms du service Bus de Service **contosoPrimary.servicebus.windows.net** peut se trouver dans la région des États-Unis d’Amérique du Nord/centrale, et **contosoSecondary.servicebus.windows.net** peut se trouver dans la région du Sud/centrale nous. Si un Bus de Service d’échanges d’entité doivent rester accessible en présence d’une panne du centre de données, vous pouvez créer cette entité dans les deux espaces de noms.

Pour plus d’informations, reportez-vous à la section « Échec du Bus de Service dans un centre de données Azure » dans [les modèles de messagerie asynchrones et une haute disponibilité][].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protection des points de terminaison de relais contre les pannes de centre de données ou de catastrophes naturelles

Geo-réplication de points de terminaison de relais permet à un service qui expose un point de terminaison de relais est accessible en présence de pannes de Bus de Service. Pour obtenir la réplication géographique, le service doit créer deux points de terminaison de relais dans différents espaces de noms. Les espaces de noms doivent se trouver dans différents centres de données et les deux points de terminaison doivent avoir des noms différents. Par exemple, un point de terminaison principale est accessible sous **contosoPrimary.servicebus.windows.net/myPrimaryService**, alors que son équivalent secondaire peut être atteint que dans **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Le service écoute puis sur les deux points de terminaison et un client peut appeler le service via un point de terminaison. Une application client sélectionne l’une des relais comme principal point de terminaison et de manière aléatoire envoie sa requête au point de terminaison active. Si l’opération échoue avec un code d’erreur, cela indique que le point de terminaison de relais n’est pas disponible. L’application ouvre un canal au point de terminaison sauvegarde et soumet la demande. À ce stade actif et les points de terminaison sauvegarde changer de rôles : l’application cliente prend en compte le point de terminaison active ancien comme le nouveau point de terminaison de sauvegarde et le point de terminaison de sauvegarde ancien d’être le nouveau point de terminaison active. Si les deux envoient les opérations échouent, les rôles des deux entités restent inchangés et une erreur est renvoyée.

[Geo-réplication avec des Messages relayés Service Bus][] il illustre comment répliquer les relais.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Protection des files d’attente et les rubriques contre les pannes de centre de données ou de catastrophes naturelles

Pour obtenir une tolérance aux pannes de centre de données lorsque l’aide demandée messagerie, Bus de Service prend en charge les deux approches : réplication *active* et *passive* . Pour chaque approche, si une file d’attente ou une rubrique doit rester accessible en présence d’une panne du centre de données, vous pouvez le créer dans les deux espaces de noms. Les deux entités peuvent avoir le même nom. Par exemple, une file d’attente principale est accessible sous **contosoPrimary.servicebus.windows.net/myQueue**, alors que son équivalent secondaire peut être atteint que dans **contosoSecondary.servicebus.windows.net/myQueue**.

Si l’application ne nécessite pas de communication permanente de récepteur de l’expéditeur, l’application peut implémenter une file d’attente côté client durable afin d’éviter la perte de messages et à protéger l’expéditeur à partir des erreurs de Bus de Service temporaires.

## <a name="active-replication"></a>Réplication Active

Réplication Active utilise les entités dans les deux espaces de noms pour chaque opération. Tout client qui envoie un message envoie deux copies du même message. La première copie est envoyée à l’entité principale (par exemple, **contosoPrimary.servicebus.windows.net/sales**), et la deuxième copie du message est envoyée à l’entité secondaire (par exemple, **contosoSecondary.servicebus.windows.net/sales**).

Un client reçoit des messages dans des files d’attente. Le destinataire traite la première copie d’un message, et la seconde copie est supprimée. Pour supprimer les doublons de messages, l’expéditeur doit marquer chaque message portant un identificateur unique. Les deux copies du message doivent être étiquetées avec le même identificateur. Vous pouvez utiliser les propriétés [BrokeredMessage.MessageId][] ou [BrokeredMessage.Label][] , ou une propriété personnalisée pour le message de la balise. Le récepteur doit conserver une liste de messages qu’il a déjà reçu.

[Geo-réplication avec Service Bus négociée dans les Messages][] illustre la réplication de la messagerie d’entités active.

> [AZURE.NOTE] L’approche de la réplication active double le nombre d’opérations, par conséquent, cette approche peut mener à un coût plus élevé.

## <a name="passive-replication"></a>Réplication passive

Dans le cas d’exempts de défauts, passive réplication utilise un seul des deux entités de messagerie. Un client envoie le message à l’entité active. En cas d’échec de l’opération sur l’entité active avec un code d’erreur qui indique le centre de données hébergeant l’entité active n’est pas disponible, le client envoie une copie du message à l’entité de sauvegarde. À ce stade actif et les entités de sauvegarde changer de rôle : le client d’envoi prend en compte l’ancienne entité active à la nouvelle entité de sauvegarde, et l’entité de sauvegarde ancienne est la nouvelle entité active. Si les deux envoient les opérations échouent, les rôles des deux entités restent inchangés et une erreur est renvoyée.

Un client reçoit des messages dans des files d’attente. Dans la mesure où il est probable que le récepteur reçoit deux copies du même message, le récepteur doit supprimer les messages en double. Vous pouvez supprimer les doublons de la même manière que celles décrites pour la réplication active.

En général, la réplication passive est plus économique que la réplication active car dans la plupart des cas qu’une opération est effectuée. Débit, de latence et de coût sont identiques au scénario non répliquée.

Lorsque vous utilisez la réplication passive, dans les scénarios suivants les messages peuvent être perdus ou reçus deux fois :

-   **Perte ou du retardement du message**: supposons que l’expéditeur m1 d’un message envoyé à la file d’attente principale, et puis la file d’attente devient indisponible avant que le récepteur reçoit m1. L’expéditeur envoie un m2 de message qui s’affiche à la file d’attente secondaire. Si la file d’attente principale est temporairement indisponible, le récepteur reçoit m1 après que la file d’attente sera à nouveau disponible. En cas de sinistre, le récepteur ne peut jamais s’afficher m1.

-   **Dupliquer la réception**: supposons que l’expéditeur envoie un message m pour la file d’attente principale. Bus de service traite m avec succès mais ne parvient pas à envoyer une réponse. Après l’expiration de l’opération d’envoi, l’expéditeur envoie une copie identique de m à la file d’attente secondaire. Si le destinataire est en mesure de recevoir de la première copie de m avant que la file d’attente principale n’est pas disponible, le récepteur reçoit les deux copies de m en même temps. Si le récepteur n’est pas en mesure de recevoir de la première copie de m avant que la file d’attente principale n’est pas disponible, le récepteur reçoit uniquement la deuxième copie de m, mais il reçoit ensuite une deuxième copie de m lorsque la file d’attente principale est disponible.

[Geo-réplication avec Service Bus négociée dans les Messages][] illustre réplication passive d’entités de messagerie.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la récupération d’urgence, consultez ces articles :

- [Continuité d’activité de base de données SQL Azure][]
- [Guide technique de résilience Azure][]

  [Authentification du service de Bus]: service-bus-authentication-and-authorization.md
  [Entités de messagerie partitionnées]: service-bus-partitioning.md
  [Modèles de messagerie asynchrones et haute disponibilité]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [Geo-réplication avec Bus de Service relais de Messages]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [Geo-réplication avec Bus de Service demandé de Messages]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Continuité d’activité de base de données SQL Azure]: ../sql-database/sql-database-business-continuity.md
  [Guide technique de résilience Azure]: ../resiliency/resiliency-technical-guidance.md
