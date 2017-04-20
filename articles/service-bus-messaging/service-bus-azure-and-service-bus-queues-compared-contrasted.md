<properties 
    pageTitle="Azure des files d’attente et des Bus de Service des files d’attente - comparaison et contraste | Microsoft Azure"
    description="Analyse les différences et similarités entre les deux types de files d’attente proposés par Azure."
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
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure des files d’attente et des Bus de Service des files d’attente - comparaison et contraste

Cet article analyse les différences et les similitudes entre les deux types de files d’attente proposés par Microsoft Azure aujourd'hui : files d’attente de Bus de Service et les files d’attente Azure. À l’aide de ces informations, vous pouvez comparer et comparez les technologies respectives et pouvoir prendre une décision plus éclairée sur la solution qui répond le mieux à vos besoins.

## <a name="introduction"></a>Introduction

Microsoft Azure prend en charge deux types de mécanismes de file d’attente : **Files d’attente Azure** et les **Files d’attente de Bus de Service**.

**Files d’attente d’azure**, qui font partie de l’infrastructure de [stockage Azure](https://azure.microsoft.com/services/storage/) , dotés d’une interface basée sur REST Get/Put/lecture simple, fournir une messagerie de fiable et permanent au sein et entre les services.

**Files d’attente de Bus de service** font partie d’une infrastructure de [messagerie d’Azure](https://azure.microsoft.com/services/service-bus/) plus large qui prend en charge la queuing ainsi que de publication/abonnement, accès distant du service Web et modèles d’intégration. Pour plus d’informations sur les files d’attente de Bus de Service, les rubriques/abonnements et relais, consultez la [vue d’ensemble du Bus des services de messagerie](service-bus-messaging-overview.md).

Bien que les deux technologies de files d’attente existent simultanément, files d’attente Azure ont été introduites en premier lieu, sous la forme d’un mécanisme de stockage de file d’attente dédiée basé sur les services de stockage Azure. Files d’attente de Bus de services reposent sur l’infrastructure plus large « demandé de messagerie », conçu pour intégrer des applications ou des composants d’application qui s’étend sur plusieurs protocoles de communication, des contrats de données, approuver les domaines et/ou les environnements réseau.

Cet article compare les technologies de file d’attente de deux offertes par Azure en abordant les différences dans le comportement et l’implémentation des fonctionnalités fournies par chacun. L’article fournit également des conseils permettant de choisir les fonctionnalités qui correspond le mieux à vos besoins de développement d’application.

## <a name="technology-selection-considerations"></a>Considérations relatives à la sélection la technologie

Files d’attente à la fois les files d’attente Azure et Bus de Service sont des implémentations du message queuing service actuellement proposé sur Microsoft Azure. Chacun possède un ensemble de fonctionnalités légèrement différentes, ce qui signifie que vous pouvez choisir l’un ou l’autre, ou utiliser les deux, selon les besoins de votre solution particulière ou un problème métier et techniques à résoudre.

Lors de la détermination des files d’attente de la technologie adaptée à l’objectif d’une solution donnée, les développeurs et les architectes de solutions doivent prendre en compte les recommandations ci-dessous. Pour plus d’informations, consultez la section suivante.

En tant qu’architecte/développeur d’une solution, **vous devez envisager d’utiliser des files d’attente Azure** lorsque :

- Votre application doit stocker plus de 80 Go de messages dans une file d’attente, où les messages ont une durée de vie inférieure à 7 jours.

- Votre application doit suivre la progression de traitement d’un message dans la file d’attente. Cela est utile si le traitement d’un message de travailleur tombe en panne. Un travailleur suivant peut ensuite utiliser ces informations pour continuer à partir de là où le travailleur précédent s’est arrêté.

- Vous avez besoin des journaux du côté serveur de toutes les transactions exécutées sur vos files d’attente.

En tant qu’architecte/développeur d’une solution, **vous devez envisager d’utiliser des files d’attente de Bus de Service** lorsque :

- Votre solution doit être en mesure de recevoir des messages sans avoir à interroger la file d’attente. Avec un Bus de Service, ceci peut être réalisé grâce à l’utilisation de l’interrogation longue opération en utilisant les protocoles basés sur TCP/IP qui prend en charge le Bus des services de réception.

- Votre solution requiert que la file d’attente pour fournir une garantie premier-premier sorti (FIFO) commandé la livraison.

- Vous souhaitez une expérience symétrique dans Azure et de Windows Server (cloud privé). Pour plus d’informations, reportez-vous à la section [Service de Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- Votre solution doit être en mesure de prendre en charge la détection automatique des doublons.

- Vous souhaitez que votre application doit traiter les messages en tant que flux de longue parallèles (messages sont associés à un flux de données à l’aide de la propriété [SessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) dans le message). Dans ce modèle, chaque nœud dans l’application consommatrice en concurrence pour les flux, par opposition aux messages. Lorsqu’un flux est donné à un nœud de consommation, le nœud peut examiner l’état de l’état de flux de données d’application à l’aide de transactions.

- Votre solution nécessite le comportement transactionnel et l’atomicité lors de l’envoi ou la réception de plusieurs messages dans une file d’attente.

- Time-to-live (TTL) qualifie la charge de travail spécifique à l’application peut dépasser la période de 7 jours.

- Votre application gère les messages qui dépassent 64 Ko, mais limite approche probablement pas le 256 Ko.

- Vous travaillez avec une exigence de fourniture d’un modèle d’accès basé sur les rôles et les files d’attente, droits/autorisations différentes pour les expéditeurs et les récepteurs.

- La taille de votre file d’attente ne deviendront pas plue de 80 Go.

- Vous souhaitez utiliser le protocole de messagerie basé sur les normes de AMQP 1.0. Pour plus d’informations sur AMQP, reportez-vous à la section [Présentation du Service AMQP de Bus](./service-bus-amqp-overview.md).

- Vous pouvez prévoir une migration éventuelle de la communication de point à point file d’attente à un modèle d’échange de message qui permet une intégration transparente de récepteurs supplémentaires (abonnés), chacun d’eux reçoit une copie indépendante de certains ou tous les messages envoyés à la file d’attente. Le second fait référence à la fonction de publication et d’abonnement fournie de manière native par Bus de Service.

- Votre solution de messagerie doit être en mesure de prendre en charge de la garantie de livraison « La plupart-simultanément » sans avoir à créer les composants d’infrastructure supplémentaires.

- Vous souhaitez être en mesure de publier et de consommer des messages par batch.

- Vous avez besoin d’une intégration complète avec la pile de communication de Windows Communication Foundation (WCF) dans le.NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Comparaison des files d’attente de Bus de Service et les files d’attente Azure

Les tableaux dans les sections suivantes fournissent un regroupement logique des fonctionnalités de file d’attente et vous permettent de comparer, en un coup de œil, les fonctionnalités disponibles dans les files d’attente Azure et les files d’attente de Bus de Service.

## <a name="foundational-capabilities"></a>Ajouter aux fonctionnalités de

Cette section répertorie quelques-unes des fonctions principales de files d’attente fournies par les files d’attente de Bus de Service et les files d’attente Azure.

|Critères de comparaison|Files d’attente Azure|Files d’attente de Bus de service|
|---|---|---|
|Classement de garantie|**N°** <br/><br>Pour plus d’informations, consultez la première note de la section « Informations supplémentaires ».</br>|**Oui - First-In-First-Out (FIFO)**<br/><br>(à l’aide de sessions de messagerie)|
|Garantie de livraison|**Au moins une**|**Au moins une**<br/><br/>**La plupart-simultanément**|
|Prise en charge de l’opération atomique|**N°**|**Oui**<br/><br/>|
|Comportement de réception|**Non bloquant**<br/><br/>(se termine immédiatement si aucun message n’est trouvée)|**Avec/sans délai d’attente de blocage**<br/><br/>(offres long l’interrogation ou la [« Technique de Comet »](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Non bloquant**<br/><br/>(à l’aide de .NET managé API uniquement)|
|API de type push|**N°**|**Oui**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) et **OnMessage** sessions API .NET.|
|Mode de réception|**Lecture & bail**|**Lire et de verrouiller**<br/><br/>**Supprimer & de réception**|
|Mode d’accès exclusif|**Basé sur un bail**|**Verrouillage**|
|Durée de bail/verrou|**30 secondes (par défaut)**<br/><br/>**7 jours (au maximum)** (Vous pouvez renouveler ou libérer un bail de message à l’aide de l’API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) ).|**60 secondes (par défaut)**<br/><br/>Vous pouvez renouveler un verrou de message à l’aide de l’API [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .|
|Bail/verrouillage de précision|**Au niveau du message**<br/><br/>(chaque message peut avoir la valeur du délai d’attente différent, que vous pouvez ensuite de mettre à jour en fonction des besoins lors du traitement du message, à l’aide de l’API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) )|**Niveau de la file d’attente**<br/><br/>(chaque file d’attente a une précision de verrou appliquée à l’intégralité de ses messages, mais vous pouvez renouveler le verrou à l’aide de l’API [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .)|
|Par lots de réception|**Oui**<br/><br/>(explicitement en spécifiant le nombre de message lors de la récupération des messages, avec un maximum de 32 messages)|**Oui**<br/><br/>(implicitement en activant une propriété de lecture préalable soit explicitement à l’aide de transactions)|
|Envoi par lots|**N°**|**Oui**<br/><br/>(à l’aide de transactions ou le traitement par lots du côté client)|

### <a name="additional-information"></a>Informations supplémentaires

- Les messages dans les files d’attente Azure sont généralement first-in-first-out, mais ils peuvent parfois être dans le bon ordre ; par exemple, lorsque durée avant expiration de visibilité d’un message expire (par exemple, d’une application cliente qui se bloquent au cours du traitement). Lorsque le délai d’attente de visibilité expire, le message devient visible dans la file d’attente pour un autre travailleur, il la file d’attente. À ce stade, le message qui vient d’être visible peut être placé dans la file d’attente (pour être retiré à nouveau) après un message qui se trouvait en file d’attente après.

- Si vous utilisez déjà des Blobs de stockage Azure ou Tables et que vous démarrez à l’aide de files d’attente, vous avez la garantie de disponibilité de 99,9 %. Si vous utilisez les Tables ou les objets BLOB des files d’attente de Bus de Service, vous devez la disponibilité inférieure.

- Le modèle FIFO garanti dans les files d’attente de Bus de Service requiert l’utilisation de sessions de messagerie. Dans le cas où l’application se bloque lors du traitement d’un message reçu dans le mode **Lecture & Verrouiller** , la prochaine fois qu’un récepteur de la file d’attente accepte une session de messagerie, il commence par le message qui a échoué après l’expiration de son temps-durée de vie (TTL).

- Les files d’attente Azure sont conçus pour prendre en charge les scénarios de files d’attente standard, tels que les composants d’application découplage d’augmenter l’évolutivité et la tolérance de pannes, charger l’audit et la création de workflows de processus.

- Files d’attente de Bus de service prend en charge la garantie de livraison *Au moins une* . En outre, *La plupart-simultanément* sémantique peut être pris en charge à l’aide de l’état de session pour stocker l’état de l’application et à l’aide de transactions de manière atomique de recevoir des messages et de mettre à jour l’état de session.

- Les files d’attente Azure fournissent un modèle de programmation uniform et cohérent entre les files d’attente, les tableaux et les objets BLOB : pour les développeurs et les équipes d’exploitation.

- Files d’attente de Bus de service prennent en charge les transactions locales dans le contexte d’une file d’attente unique.

- Le mode de **réception et supprimer** pris en charge par le Service de Bus offre la possibilité de réduire le nombre d’opérations messagerie (et le coût associé) en échange de la garantie de livraison réduits.

- Les files d’attente Azure fournissent des baux avec la possibilité d’étendre les baux pour les messages. Ainsi, les travailleurs à mettre à jour les baux court sur les messages. Par conséquent, si un travailleur se bloque, le message peut être rapidement traité à nouveau par un autre travailleur. En outre, un travailleur peut étendre le bail d’un message si elle doit traiter la plus longue que la durée du bail en cours.

- Les files d’attente Azure offrent une visibilité du délai d’attente que vous pouvez définir sur le fait ou la file d’attente d’un message. En outre, vous pouvez mettre à jour un message avec des valeurs de bail différente au moment de l’exécution et mettre à jour des valeurs différentes entre les messages dans la même file d’attente. Délais d’attente du verrou de Bus de service sont définis dans les métadonnées de la file d’attente ; Toutefois, vous pouvez renouveler le verrou en appelant la méthode [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .

- Le délai d’attente maximal pour le blocage d’une opération de réception dans les files d’attente de Bus de Service est de 24 jours. Toutefois, des délais d’attente basée sur REST ont une valeur maximale de 55 secondes.

- Le traitement par lots de côté client fournies par Bus de Service permet à un client de la file d’attente pour une sélection de plusieurs messages dans une opération d’envoi unique. Le traitement par lots n’est disponible que pour les opérations d’envoi asynchrone.

- Des fonctionnalités telles que le plafond de 200 to Azure files d’attente (plus lorsque vous virtualisez comptes) et illimités rendent une plate-forme idéale pour les fournisseurs de SaaS.

- Files d’attente Azure fournissent un flexible et performant déléguée de mécanisme de contrôle d’accès.

## <a name="advanced-capabilities"></a>Fonctions avancées

Cette section compare les fonctionnalités avancées fournies par les files d’attente de Bus de Service et les files d’attente Azure.

|Critères de comparaison|Files d’attente Azure|Files d’attente de Bus de service|
|---|---|---|
|Livraison prévue|**Oui**|**Oui**|
|Lettres mortes automatique|**N°**|**Oui**|
|Augmentation de la valeur de durée de vie de file d’attente|**Oui**<br/><br/>(via la mise à jour de la place de la visibilité du délai d’attente)|**Oui**<br/><br/>(fournie via une fonction d’API dédiée)|
|Prise en charge du message de poison|**Oui**|**Oui**|
|Mise à jour sur place|**Oui**|**Oui**|
|Journal des transactions du côté serveur|**Oui**|**N°**|
|Indicateurs de stockage|**Oui**<br/><br/>**Les mesures de minute**: fournit des métriques en temps réel pour l’API de la disponibilité, la TPS, appellent nombre, erreurs, etc., tout en temps réel (regroupés par minute et signalé quelques minutes à partir d’agir de la sorte en production. Pour plus d’informations, consultez [à propos des indicateurs de stockage Analytique](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Oui**<br/><br/>(les requêtes en bloc en appelant [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Gestion de l’état|**N°**|**Oui**<br/><br/>De [Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), groupes de [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Transfert automatique des messages|**N°**|**Oui**|
|Fonction de file d’attente de purge|**Oui**|**N°**|
|Groupes de messages|**N°**|**Oui**<br/><br/>(à l’aide de sessions de messagerie)|
|État de l’application par groupe de message|**N°**|**Oui**|
|Détection des doublons|**N°**|**Oui**<br/><br/>(configurable sur le côté de l’expéditeur)|
|Intégration de WCF|**N°**|**Oui**<br/><br/>(offre les liaisons WCF-de-l’emploi)|
|Intégration de WF|**Personnalisé**<br/><br/>(requiert la création d’une activité personnalisée de WF)|**Natif**<br/><br/>(offre des activités de l’emploi de WF)|
|Parcourir les groupes de messages|**N°**|**Oui**|
|L’extraction des sessions de messagerie par ID|**N°**|**Oui**|

### <a name="additional-information"></a>Informations supplémentaires

- Les deux technologies de files d’attente permettent à un message être programmée ultérieurement.

- Transfert automatique de la file d’attente permet à des milliers de files d’attente de transfert automatique de leurs messages à une file d’attente unique, à partir duquel l’application réceptrice utilise le message. Vous pouvez utiliser ce mécanisme pour parvenir à la sécurité, de contrôle de flux et isoler le stockage entre chaque éditeur de message.

- Les files d’attente Azure prennent en charge la mise à jour du contenu du message. Vous pouvez utiliser cette fonctionnalité pour les informations d’état persistantes et des mises à jour de l’avancement dans le message afin qu’elles puissent être traitées à partir du dernier point de contrôle connu, au lieu de démarrer à partir de zéro. Des files d’attente de Bus de Service, vous pouvez activer le même scénario à l’aide de sessions de messagerie. Sessions permettent d’enregistrer et de récupérer l’état de traitement de l’application (en utilisant [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) et [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- [Les lettres mortes](service-bus-dead-letter-queues.md), qui est uniquement pris en charge par les files d’attente de Bus de Service, peut être utile pour isoler les messages qui ne peuvent pas être traitées avec succès par l’application réceptrice, ou lorsque les messages arrivent à destination en raison d’une propriété de (vie TTL) de time-to-live a expiré ne peut pas. La valeur de durée de vie spécifie la durée pendant laquelle un message reste dans la file d’attente. Avec le Bus des services, le message va être déplacé vers une file d’attente spéciale appelée $DeadLetterQueue lors de l’expiration de la période de durée de vie.

- Pour trouver les messages « incohérents » dans les files d’attente d’Azure, lorsque la file d’attente un message l’application examine la propriété **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** du message. Si **DequeueCount** est supérieur à un seuil donné, l’application place le message dans une file d’attente définie par l’application « mortes ».

- Files d’attente Azure activer vous permettent d’obtenir un journal détaillé de toutes les transactions exécutées par rapport à la file d’attente, comme métrique bien comme agrégées. Ces deux options sont utiles pour le débogage et comprendre comment votre application utilise des files d’attente Azure. Ils sont également utiles pour régler les performances de votre application et réduire les coûts d’utilisation des files d’attente.

- Le concept de « sessions message » pris en charge par le Bus de Service permet de messages qui appartiennent à un groupe logique d’être associé à un récepteur donné, ce qui entraîne une affinité de session de type entre les messages et leurs récepteurs respectifs. Vous pouvez activer cette fonctionnalité avancée de Bus de Service en définissant la propriété [SessionID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) sur un message. Récepteurs peuvent ensuite écouter sur un ID de session spécifiques et recevoir des messages qui partagent l’identificateur de session spécifié.

- La fonctionnalité de détection de duplication automatiquement pris en charge par les files d’attente de Bus de Service supprime les doublons de messages envoyés à une file d’attente ou une rubrique, basée sur la valeur de la propriété [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) .

## <a name="capacity-and-quotas"></a>Capacité et des quotas

Cette section compare les files d’attente de Bus de Service et les files d’attente Azure du point de vue de [capacité et des quotas](service-bus-quotas.md) qui peuvent s’appliquer.

|Critères de comparaison|Files d’attente Azure|Files d’attente de Bus de service|
|---|---|---|
|Taille maximale de file d’attente|**200 TO**<br/><br/>(limitée à une capacité de compte de stockage unique)|**1 Go à 80 Go**<br/><br/>(définie lors de la création d’une file d’attente et l' [activation de partitionnement](service-bus-partitioning.md) – reportez-vous à la section « Informations supplémentaires »)|
|Taille maximale des messages|**64 KO**<br/><br/>(48 Ko lorsque vous utilisez le codage **Base64** )<br/><br/>Azure prend en charge les messages de grande taille en associant des files d’attente et des objets BLOB : à partir de laquelle vous pouvez enqueue jusqu'à 200 Go pour un seul article.|**256 Ko** ou **1 Mo**<br/><br/>(y compris les en-tête et corps, taille de l’en-tête maximum : 64 Ko).<br/><br/>Dépend du [niveau de service](service-bus-premium-messaging.md).|
|Durée de vie maximale d’un message|**7 derniers jours**|**`TimeSpan.Max`**|
|Nombre maximal de files d’attente|**Un nombre illimité**|**10 000**<br/><br/>(par espace de noms de service, peut être augmentée)|
|Nombre maximal de clients simultanés|**Un nombre illimité**|**Un nombre illimité**<br/><br/>(limite de connexion simultanée 100 s’applique uniquement aux communications de protocole TCP)|

### <a name="additional-information"></a>Informations supplémentaires

- Bus de service met en œuvre des limites de taille de file d’attente. La taille maximale de file d’attente est spécifiée lors de la création de la file d’attente et peut avoir une valeur comprise entre 1 et 80 Go. Si la valeur de taille de file d’attente définie à la création de la file d’attente est atteinte, les messages entrants supplémentaires seront rejetées et une exception sera reçue par le code appelant. Pour plus d’informations sur les quotas dans le Bus de Service, reportez-vous à la section [Quotas de Bus de Service](service-bus-quotas.md).

- Vous pouvez créer des files d’attente de Bus de Service dans les tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est de 1 Go). Avec partitionnement activé (par défaut), le Bus de Service crée 16 partitions pour chaque Go que vous spécifiez. Par conséquent, si vous créez une file d’attente est de 5 Go en taille, avec 16 partitions la taille maximale de file d’attente devient (5 * 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d’attente partitionnée ou une rubrique en examinant son entrée sur le [portail Azure][].

- Des files d’attente d’Azure, si le contenu du message n’est pas sécurisée pour XML, puis il doit être **au format Base64** encodée. Si vous **en Base64**-coder le message, la charge d’utilisateur peut être jusqu'à 48 Ko, au lieu de 64 Ko.

- Avec les files d’attente de Bus de Service, chaque message stocké dans une file d’attente se compose de deux parties : un en-tête et un corps. La taille totale du message ne peut pas dépasser la taille maximale des messages prise en charge par le niveau de service.

- Lorsque les clients communiquent avec les files d’attente de Bus de Service via le protocole TCP, le nombre maximal de connexions simultanées à une seule file d’attente de Bus de Service est limité à 100. Ce numéro est partagé entre les expéditeurs et les récepteurs. Si ce quota est atteint, les demandes suivantes pour les connexions supplémentaires seront rejetées et une exception sera reçue par le code appelant. Cette limite n’est pas imposée sur les clients qui se connectent les files d’attente à l’aide des API de reste.

- Si vous avez besoin de plus de 10 000 files dans un espace de noms unique du Bus des services, vous pouvez contacter l’équipe de support Azure et demander une augmentation. Pour mettre à l’échelle au-delà de 10 000 files avec Bus de Service, vous pouvez également créer des espaces de noms supplémentaires à l’aide du [portail Azure][].

## <a name="management-and-operations"></a>Gestion et opérations

Cette section compare les fonctionnalités de gestion fournies par les files d’attente de Bus de Service et les files d’attente Azure.

|Critères de comparaison|Files d’attente Azure|Files d’attente de Bus de service|
|---|---|---|
|Protocole de gestion|**Placez via HTTP/HTTPS**|**PLACEZ sur HTTPS**|
|Protocole d’exécution|**Placez via HTTP/HTTPS**|**PLACEZ sur HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP avec TLS)**|
|API managée .NET|**Oui**<br/><br/>(API Client de stockage géré par .NET)|**Oui**<br/><br/>(Managé .NET demandée API de messagerie)|
|C++ natif|**Oui**|**N°**|
|API Java|**Oui**|**Oui**|
|API PHP|**Oui**|**Oui**|
|Node.js API|**Oui**|**Oui**|
|Prise en charge des métadonnées arbitraires|**Oui**|**N°**|
|Règles d’affectation des noms de file d’attente|**Jusqu'à 63 caractères.**<br/><br/>(Les lettres d’un nom de file d’attente doivent être en minuscules.)|**Jusqu'à 260 caractères.**<br/><br/>(Les noms et les chemins d’accès de la file d’attente respectent la casse.)|
|Fonction de longueur de file d’attente|**Oui**<br/><br/>(Valeur approximative si l’expiration des messages au-delà de la durée de vie sans les supprimer.)|**Oui**<br/><br/>(Valeur exacte, point-à-temps).|
|Fonction de lecture|**Oui**|**Oui**|

### <a name="additional-information"></a>Informations supplémentaires

- Les files d’attente Azure prennent en charge les attributs arbitraires qui peuvent être appliquées à la description de la file d’attente, sous la forme de paires nom/valeur.

- Les deux technologies de file d’attente offrent la possibilité de lire un message sans avoir à le verrouiller, qui peut être utile lors de l’implémentation d’un outil de navigateur/explorer de file d’attente.

- Service Bus .NET demandé de messagerie API exploiter en duplex intégral de connexions TCP pour améliorer les performances par rapport aux autres sur HTTP, et ils prennent en charge le protocole standard de AMQP 1.0.

- Les noms de files d’attente Azure peut être de 3 à 63 caractères long, peuvent contenir des lettres minuscules, chiffres et traits d’union. Pour plus d’informations, reportez-vous à la section [d’affectation de noms des files d’attente et les métadonnées](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Les noms de file d’attente de Bus de service peuvent être 260 caractères au maximum et ont des règles d’attribution de noms moins restrictives. Les noms de file d’attente de Bus de service peuvent contenir des lettres, des chiffres, des périodes, des traits d’union et des traits de soulignement.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Cette section traite des fonctionnalités d’authentification et d’autorisation pris en charge par les files d’attente de Bus de Service et les files d’attente Azure.

|Critères de comparaison|Files d’attente Azure|Files d’attente de Bus de service|
|---|---|---|
|Authentification|**Clé symétrique**|**Clé symétrique**|
|Modèle de sécurité|Accès délégué via les jetons SAS.|SAS|
|Fédération d’identité fournisseur|**N°**|**Oui**|

### <a name="additional-information"></a>Informations supplémentaires

- Chaque demande d’une des technologies de files d’attente doit être authentifié. Les files d’attente publiques avec l’accès anonyme ne sont pas pris en charge. À l’aide des [associations de sécurité](service-bus-sas-overview.md), vous pouvez résoudre ce scénario en publiant un SAS en écriture seule, SAS en lecture seule ou même un SAS en accès complet.

- Le schéma d’authentification fourni par les files d’attente Azure implique l’utilisation d’une clé symétrique, qui est un hachage-HMAC based Message Authentication Code (), calculée à l’aide de l’algorithme SHA-256 et codée sous la forme d’une chaîne **en base 64** . Pour plus d’informations sur le protocole respectif, consultez [authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx). Files d’attente de Bus de service prend en charge un modèle similaire à l’aide de clés symétriques. Pour plus d’informations, consultez [Authentification de Signature à accès partagé avec Bus de Service](service-bus-shared-access-signature-authentication.md).

## <a name="cost"></a>Coût

Cette section compare les files d’attente de Bus de Service et les files d’attente Azure à partir d’un point de vue du coût.

|Critères de comparaison|Files d’attente Azure|Files d’attente de Bus de service|
|---|---|---|
|Coût de transaction de file d’attente|**$0.0036**<br/><br/>(par 100 000 transactions)|**Couche de base**: **0,05 $**<br/><br/>(par millions d’opérations)|
|Opérations facturables|**Tous les**|**Envoi/réception uniquement**<br/><br/>(sans frais pour les autres opérations)|
|Transactions inactives|**Facturable**<br/><br/>(Interrogation d’une file d’attente vide est comptée comme une transaction facturable.)|**Facturable**<br/><br/>(Une réception sur une file d’attente vide est considéré comme un message facturable).|
|Coût de stockage|**0,07 $**<br/><br/>(par Go/mois)|**0,00 $**|
|Coûts de transfert de données sortantes|**0,12 $ - $0.19**<br/><br/>(Selon la région).|**0,12 $ - $0.19**<br/><br/>(Selon la région).|

### <a name="additional-information"></a>Informations supplémentaires

- Transferts de données sont facturés en fonction de la quantité totale de données en laissant les centres de données Azure via internet dans une période de facturation donnée.

- Transferts de données entre des services Azure situés dans la même région ne sont pas facturés.

- À ce jour, tous les transferts de données entrantes ne sont pas facturés.

- Étant donné la prise en charge de l’interrogation longue, à l’aide de files d’attente de Bus de Service peut être efficace dans des situations où la livraison de faible latence est requis.

>[AZURE.NOTE] Tous les coûts sont sujets à modification. Ce tableau reflète les tarifs actuels et n’inclut pas les offres promotionnelles qui peuvent être actuellement disponibles. Pour obtenir des informations récentes sur Azure de tarification, voir la page [tarification d’Azure](https://azure.microsoft.com/pricing/) . Pour plus d’informations sur la tarification de Bus des services, consultez [tarification des Bus de Service](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="conclusion"></a>Conclusion

À acquérir une compréhension des deux technologies, vous serez en mesure de prendre une décision plus éclairée sur quelle technologie de file d’attente à utiliser et quand. La décision relative à l’utilisation des files d’attente de Bus de Service ou de files d’attente Azure dépend clairement un certain nombre de facteurs. Ces facteurs peuvent se fondent sur les besoins de votre application et de son architecture. Si votre application utilise déjà les principales fonctionnalités de Microsoft Azure, vous pouvez choisir les files d’attente d’Azure, surtout si vous avez besoin de communication de base et de la messagerie entre des services ou des files d’attente nécessaire peuvent être supérieures à 80 Go.

Dans la mesure où les files d’attente de Bus de Service fournissent un certain nombre de fonctionnalités avancées, telles que des sessions, des transactions, de détection, automatique en double capacités-lettres mortes et durables de publication/abonnement, ils peuvent être un choix idéal si vous générez une application hybride ou si votre application sinon requiert ces fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants fournissent plus de conseils et d’informations sur l’utilisation de files d’attente de Bus de Service ou de files d’attente Azure.

- [L’utilisation de files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md)
- [Comment faire pour utiliser le Service de stockage de file d’attente](../storage/storage-dotnet-how-to-use-queues.md)
- [Meilleures pratiques pour améliorer les performances à l’aide du Bus de Service demandé de la messagerie](service-bus-performance-improvements.md)
- [Présentation des files d’attente et les rubriques du Bus des services Azure](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Le Guide du développeur de Bus des services](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [À l’aide du Service de file d’attente dans Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Facturation du stockage Azure – bande passante, Transactions et capacité de présentation](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Azure portal]: https://portal.azure.com
 
