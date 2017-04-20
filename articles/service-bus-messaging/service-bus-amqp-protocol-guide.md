<properties 
    pageTitle="1.0 AMQP dans le guide de protocole de Bus des services Azure et concentrateurs d’événement | Microsoft Azure" 
    description="Guide de protocole pour les expressions et la description de 1.0 AMQP dans le Bus des services Azure et concentrateurs d’événement" 
    services="service-bus,event-hubs" 
    documentationCenter=".net" 
    authors="clemensv" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="07/01/2016"
    ms.author="clemensv;jotaub;hillaryc;sethm"/>

# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>1.0 AMQP dans le guide de protocole de Bus des services Azure et concentrateurs d’événement

Les avancées Message Queueing protocole 1.0 est un protocole normalisé de cadrage et de transfert pour transférer de manière asynchrone, en toute sécurité et fiable des messages entre deux parties. Il est le principal protocole de messagerie d’Azure Service Bus et concentrateurs d’événement Azure. Les deux services prennent également en charge le protocole HTTPS. Le protocole propriétaire SBMP également prise en charge est progressivement au profit de AMQP.

AMQP 1.0 est le résultat d’une collaboration plus étendue du marché qui a rassemblé des vendeurs d’intergiciels, tels que Microsoft et Red Hat, comptant de nombreux utilisateurs de middleware de messagerie comme JP Morgan Chase représentant le secteur des services financiers. Forum technique de normalisation pour les spécifications de protocole et l’extension AMQP est OASIS et il a obtenu une approbation formelle en tant que norme internationale en ISO/IEC 19494.

## <a name="goals"></a>Objectifs

Cet article résume les concepts fondamentaux de la messagerie spécification avec un petit ensemble de spécifications extension qui sont actuellement en cours de finalisation dans le comité technique OASIS AMQP version 1.0 AMQP brièvement et explique comment le Bus des services Azure implémente et s’appuie sur ces spécifications.

L’objectif est que tout développeur utilisant une pile de client AMQP 1.0 existante sur n’importe quelle plate-forme pour pouvoir interagir avec le Bus des services Azure via AMQP 1.0.

Des piles usage général AMQP 1.0 courants, tels que Apache PROTONS ou AMQP.NET Lite, implémentent déjà tous les mouvements de base AMQP 1,0. Les mouvements de base sont parfois encapsulés avec un niveau supérieur API ; Les PROTONS Apache offre même deux, les API Messenger impérative et réacteur réactive.

Dans la discussion suivante, nous supposerons que la gestion des connexions, les sessions et les liens AMQP et la gestion des transferts de cadre et de contrôle de flux sont gérés par la pile respective (tel que Apache PROTONS-C) et ne nécessitent pas beaucoup le cas échéant une attention particulière à partir des développeurs d’applications. Nous supposerons abstraitement l’existence de plusieurs primitives d’API tels que la possibilité de se connecter et pour créer des objets de l’abstraction d’une forme de *l’expéditeur* et le *destinataire* , qui ont une forme de `send()` et `receive()` opérations, respectivement.

Lorsque vous abordez les fonctions avancées du Bus des services Azure, telles que la navigation de message ou de la gestion des sessions, ceux vous seront expliqués en termes AMQP, mais également comme une implémentation en couche pseudo sur cette abstraction API supposée.

## <a name="what-is-amqp"></a>Quel est AMQP ?

AMQP est un protocole de transfert et le tramage. Tramage signifie qu’il fournit de structure pour les flux de données binaires qui circulent dans les deux sens d’une connexion réseau. La structure fournit la délimitation de blocs distincts de données – cadres – doivent être échangées entre les parties connectées. Les capacités de transfert Assurez-vous que les deux parties qui communiquent peuvent établir une compréhension commune sur lorsque les trames sont transférés, et lorsque les transferts sont considérées comme terminées.

Contrairement à précédemment expiré brouillons produites par le groupe de travail AMQP qui sont en cours d’utilisation par quelques courtiers de messages, du groupe travail final et standardisé AMQP 1.0 le protocole n’impose pas la présence d’un agent de message ou de n’importe quelle topologie particulière pour les entités à l’intérieur d’un agent de message.

Le protocole peut être utilisé pour les communications peer-to-peer symétrique, pour l’interaction avec les courtiers de messages qui prennent en charge des files d’attente et d’entités de publication et d’abonnement, comme le fait de Bus des services Azure. Il peut également être utilisé pour l’interaction avec l’infrastructure de messagerie dans lequel les modèles interaction diffèrent des files d’attente normales, comme c’est le cas avec les concentrateurs d’événement Azure. Un concentrateur d’événements se comporte comme une file d’attente lorsque des événements sont envoyées, mais il agit plus comme un service de stockage de série lorsque les événements sont lus à partir de celui-ci ; elle ressemble un peu à un lecteur de bande. Le client choisit un décalage dans le flux de données disponibles, puis est pris en charge tous les événements de ce décalage les plus récents disponibles.

Le protocole AMQP 1.0 est conçu pour être extensible, ce qui autorise les spécifications améliorer ses fonctionnalités. Les spécifications de trois extension que seront abordées dans ce document illustrent ce principe. Pour une communication sur l’infrastructure existante de HTTPS/protocole WebSocket où la configuration des ports AMQP TCP natifs peut s’avérer difficile, une spécification de liaison définit la couche AMQP via le protocole WebSocket. Pour interagir avec l’infrastructure de messagerie d’une manière de demande/réponse à des fins de gestion ou de fournir des fonctionnalités avancées, la spécification de la gestion des AMQP définit les primitives de l’interaction de base requis. Pour l’intégration du modèle d’autorisation fédérés, la spécification de la sécurité basée sur des revendications AMQP définit comment associer et renouvellement de jetons d’autorisations associés aux liens.

## <a name="basic-amqp-scenarios"></a>Scénarios de base AMQP

Cette section décrit l’utilisation de base de Bus des services Azure, qui inclut la création de connexions, les sessions et les liens et le transfert des messages vers et à partir des entités de Bus de Service telles que les files d’attente, des rubriques et des abonnements AMQP 1.0.

La source faisant le plus autoritée pour en savoir plus sur le fonctionnement de AMQP est la spécification AMQP 1.0, mais la spécification a été écrit pour guider avec précision de mise en œuvre et à ne pas animer le protocole. Cette section se concentre sur la présentation de la terminologie d’autant que nécessaire pour décrire comment le Bus de Service utilise AMQP 1.0. Pour une présentation plus complète de AMQP, ainsi que d’une discussion plus large de AMQP 1.0, vous pouvez consulter [ce cours vidéo][].

### <a name="connections-and-sessions"></a>Connexions et sessions

![][1]

AMQP appelle les programmes communiquant *conteneurs*; ceux contiennent des *nœuds*, qui sont les entités de communication à l’intérieur de ces conteneurs. Une file d’attente peut être un nœud. AMQP permet de multiplexage, une seule connexion peut être utilisée pour de nombreux chemins de communication entre les nœuds ; par exemple, une application cliente peut simultanément recevoir d’une file d’attente et envoyer à la file d’attente une autre sur la même connexion réseau.

La connexion réseau est donc ancrée dans le conteneur. Il est initié par le conteneur dans le rôle de client effectue une connexion de socket TCP sortante à un conteneur dans le rôle de récepteur qui écoute et accepte les connexions TCP entrantes. La négociation de connexion inclut la négociation de la version du protocole, déclarer ou la négociation de l’utilisation de la sécurité de niveau Transport (TLS/SSL) et une négociation d’authentification/autorisation au niveau de la portée de connexion basé sur SASL.

Bus des services Azure requiert l’utilisation de TLS à tout moment. Il prend en charge les connexions sur le port TCP 5671, selon laquelle la connexion TCP est tout d’abord recouvertes sur TLS avant d’entrer dans la négociation de protocole AMQP et prend également en charge les connexions sur le port TCP 5672 selon laquelle le serveur immédiatement propose une mise à niveau obligatoire de connexion TLS en utilisant le modèle prescrit de AMQP. La liaison de protocole WebSocket de AMQP crée un tunnel sur le port TCP 443 qui est équivalent à AMQP 5671 connexions.

Après avoir configuré la connexion et le TLS, Bus de Service offre deux options de mécanisme SASL :

-   SASL brut est généralement utilisée pour passer des informations d’identification de nom d’utilisateur et le mot de passe à un serveur. Bus de service n’a pas de comptes, mais nommé des [règles de sécurité de l’accès partagé](service-bus-shared-access-signature-authentication.md), qui confèrent des droits et sont associées à une clé. Le nom d’une règle est utilisé comme nom d’utilisateur et la clé (comme le texte crypté en Base64) est utilisé comme mot de passe. Les droits associés à la règle choisie régissent les opérations autorisées sur la connexion.

-   SASL anonyme est utilisé pour le contournement de l’autorisation de SASL lorsque le client souhaite utiliser le modèle (CBS) sécurité basée sur des revendications qui est décrites ultérieurement. Avec cette option, une connexion client peut être établie anonyme pendant une courte période pendant laquelle le client peut uniquement interagir avec le point de terminaison CBS et la négociation CBS doit effectuer.

Une fois la connexion de transport est établie, les conteneurs déclarer la taille de trame maximale qu’ils sont prêts à traiter, et après le délai d’inactivité qu’ils vous déconnectez unilatéralement si il n’y a aucune activité sur la connexion.

Ils déclarent également le nombre de canaux simultané est pris en charge. Un canal est un chemin d’accès virtuel, unidirectionnel sortant de transfert sur la connexion. Une session prend un canal à partir de chacun des conteneurs interconnectés pour former un chemin de communication bidirectionnelle.

Sessions possèdent un modèle de contrôle de flux sur fenêtre ; Lorsqu’une session est créée, chaque partie déclare le nombre d’images qu’il est prêt à accepter dans la fenêtre de réception. En tant que parties exchange cadres, remplissage de cadres transférés fenêtre et transferts arrêter lorsque la fenêtre est complet et jusqu'à ce que la fenêtre obtient de développé à l’aide du *flux de performative* ou réinitialisation (*performative* est le terme AMQP pour les mouvements au niveau du protocole échangés entre les deux parties).

Ce modèle basé sur la fenêtre est à peu près similaire au concept de contrôle de flux, basées sur fenêtre, mais au niveau de la session à l’intérieur du socket TCP. Concept du protocole de permettant à plusieurs sessions simultanées existe afin que le trafic de priorité élevée pourrait être se sont précipité après le trafic normal accélérée, comme sur une voie express de bus.

Bus des services Azure utilise actuellement qu’une seule session pour chaque connexion. Le Bus de Service maximal-taille de trame est 262 144 octets (256 Ko) pour le Bus de Service Standard et des concentrateurs de l’événement. Il est de 1 048 576 (1 Mo) pour la prime de Bus de Service. Bus de service n’impose pas les fenêtres de régulation de niveau session particulières, mais réinitialise la fenêtre de régulièrement dans le cadre de contrôle de flux au niveau du lien (voir [la section suivante](#links)).

Les sessions, les canaux et les connexions sont éphémères. Si la connexion sous-jacente est réduit, les connexions, le tunnel TLS, contexte d’autorisation de SASL, sessions et doivent être rétablies.

### <a name="links"></a>Liens

![][2]

AMQP transfère les messages sur des liaisons. Un lien est un chemin de communication créé au cours d’une session qui permet le transfert de messages dans une seule direction ; la négociation de statut de transfert est sur le lien et bidirectionnelle entre les parties connectées.

Les liens peuvent être créées par un conteneur à tout moment et sur une session existante, ce qui rend les AMQP différent de nombreux autres protocoles, y compris HTTP et MQTT, où l’ouverture de transferts et de chemin d’accès de transfert est un privilège exclusif de la partie de création de la connexion de socket.

Le conteneur qui initie le lien demande au conteneur opposé à accepter un lien, et il choisit un rôle de l’expéditeur ou du destinataire. Par conséquent, conteneur peut lancer la création unidirectionnels ou les chemins de communication bidirectionnelle, avec ces derniers modélisée en tant que paires de liens.

Des liens sont nommées et associés aux nœuds. Comme indiqué au début, les nœuds sont les entités de communication à l’intérieur d’un conteneur.

Dans le Bus des services Azure, un nœud correspond à une file d’attente, un sujet, un abonnement ou une file d’attente lettres mortes secondaire d’une file d’attente ou d’abonnement. Le nom de nœud utilisé dans AMQP est donc le nom relatif de l’entité dans l’espace de noms du Bus de Service. Si une file d’attente est nommée **myqueue**, qui est également son nom de nœud AMQP. Un abonnement de rubrique suit la convention de l’API HTTP en cours de tri dans une collection de ressources « abonnements » et, par conséquent, un abonnement **sub** ou une rubrique **mytopic** est le nom du nœud AMQP **mytopic/abonnements/sub**.

Le client qui se connecte est également requis pour utiliser un nom de nœud local pour la création de liens ; Bus de service n’est pas normatif sur les noms de nœuds et n’interprète les. Piles de client AMQP 1.0 généralement utilisent un modèle pour s’assurer que ces noms de nœud éphémères sont uniques dans la portée du client.

### <a name="transfers"></a>Transferts

![][3]

Une fois qu’une liaison a été établie, les messages peuvent être transférés sur ce lien. Dans AMQP, un transfert est effectué avec un mouvement de protocole explicite ( *transfert* performative) qui déplace un message de l’expéditeur au destinataire via une liaison. Un transfert est terminé lorsqu’il « régler », ce qui signifie que les deux parties ont établie une compréhension commune des résultats de ce transfert.

Dans le cas le plus simple, l’expéditeur peut choisir d’envoyer des messages « préalablement réglés », ce qui signifie que le client n’est pas intéressé par le résultat et le récepteur ne fournira pas les commentaires sur le résultat de l’opération. Ce mode est pris en charge par le Bus des services Azure au niveau du protocole AMQP, mais non exposé dans n’importe quel client API.

Le cas normal est que les messages sont envoyés non réglées et le récepteur indiquera puis acceptation ou le rejet à l’aide de la *disposition* performative. Refus se produit lorsque le récepteur ne peut pas accepter le message pour une raison quelconque, le message de refus contient des informations sur le motif, qui est une structure d’erreur définie par AMQP. Si les messages sont rejetés en raison d’erreurs internes dans le Bus des services Azure, le service retourne des informations supplémentaires à l’intérieur de cette structure qui peut être utilisée pour fournir des indications de diagnostics du personnel de support si vous utilisez les demandes d’assistance. Vous apprendrez plus loin plus de détails sur les erreurs.

Une forme spéciale de rejet est l’état *publié* , ce qui indique que le récepteur n’a pas d’objection technique pour le transfert, mais également d’aucun intérêt en réglant le transfert. Que cas existe, par exemple, lorsqu’un message est envoyé à un client de Service Bus et le client choisit le message « abandon » car il ne peut pas effectuer le travail de traitement du message lors de la remise du message lui-même n’est pas en faute. Une variation de cet état est l’état *modifié* , ce qui permet de modifier le message tel qu’il est publié. Cet état n’est pas utilisé par le Bus de Service à l’heure actuelle.

La spécification AMQP 1.0 définit une autre disposition état *reçu* qui aide à gérer la récupération du lien. Récupération de lien permet de reconstituer l’état d’un lien et en attente de livraison sur une nouvelle connexion et la session, lorsque la connexion antérieure et session ont été perdues.

Bus des services Azure ne gère pas la récupération de lien ; Si le client perd la connexion au Bus de Service avec un message non réglé en attente de transfert, ce transfert de message est perdu, et le client doit se reconnecter, rétablir le lien et réessayez le transfert.

En tant que tel, le Bus des services Azure et concentrateurs d’événement prennent en charge « au moins une fois » transferts où l’expéditeur peut être assurée pour le message ayant été stocké et accepté, mais il ne gère pas « une seule fois » transferts au niveau AMQP, où le système doit tenter de récupérer le lien et continuent à négocier l’état de la livraison pour éviter la duplication de transfert du message.

Pour compenser le doublon possible envoie, Bus des services Azure prend en charge la détection des doublons en option sur les files d’attente et des rubriques. Des doublons d’enregistrements de détection ID de tous les messages entrants de message au cours d’une fenêtre d’heure définis par l’utilisateur et en mode silencieux de supprimer tous les messages envoyés avec les mêmes ID de message au cours de cette même fenêtre.

### <a name="flow-control"></a>Contrôle de flux

![][4]

En plus du modèle de contrôle de flux au niveau de la session qui a été abordé précédemment, chaque liaison possède son propre modèle de flux de contrôle. Contrôle de flux au niveau de la session protège le devoir gérer trop de cadres en une seule fois, contrôle de flux au niveau du lien place l’application charge le nombre de messages qu’il souhaite gérer à partir d’un lien et lorsque le conteneur.

Sur une liaison, les transferts peuvent uniquement se produire lorsque l’expéditeur a suffisamment « crédit de liaison ». Crédit de liaison est un ensemble de compteurs en le récepteur à l’aide de *flux* performative, qui est étend à un lien. Lorsqu’et alors que l’expéditeur reçoit credit de liaison, il tentera d’utiliser ce crédit qui envoient des messages. Chaque décrémente de remise du message le lien restant de crédit par un. Lorsque le crédit de liaison est utilisé, les livraisons s’arrêter.

Lorsque le Service Bus est dans le rôle de récepteur il instantanément fournira l’expéditeur crédit de lien suffisant, afin que les messages peuvent être envoyés immédiatement. Comme credit de liaison est utilisé, Bus de Service parfois envoie un *flux* performative à l’expéditeur de la mettre à jour le solde de crédit de lien.

Dans le rôle expéditeur, Bus de Service dynamiquement enverra les messages d’utiliser tout crédit en suspens de lien.

Un appel « recevoir » au niveau de l’API se traduit par un *flux* performative envoyés au Bus de Service par le client, et Bus de Service consommera ce crédit en prenant le premier message disponible, non verrouillé à partir de la file d’attente, verrouillage et transférer. Si aucun message n’est facilement disponibles pour la livraison, les encours de crédit par n’importe quel lien établi avec qu’entité particulière restent enregistrée dans l’ordre d’arrivée et messages seront verrouillés et transférés lorsqu’elles sont disponibles pour utiliser des encours de crédit.

Le verrou d’un message est libéré lorsque le transfert est réglé dans un des états terminaux *accepté*, *rejeté*ou *publié*. Le message est supprimé de Bus de Service lorsque l’état de terminal est *accepté*. Il reste dans le Bus de Service et est envoyée au récepteur suivant lorsque le transfert atteint un des autres États. Bus de service va passer automatiquement le message dans la file d’attente de lettres mortes de l’entité lorsqu’il atteint le nombre maximal de remise autorisé pour l’entité rejets répétées ou des versions.

Bien que les API de Bus de Service officiel n’exposent pas directement d’une telle option aujourd'hui, un client de protocole de niveau inférieur AMQP pouvez utiliser le modèle de liaison-crédit pour activer l’interaction de style « pull » de l’émission d’une unité de crédit pour chaque demande de réception dans un modèle « push-style » en émettant un très grand nombre de crédits de lien et recevoir ensuite des messages dès qu’elles sont disponibles, sans aucune intervention supplémentaire. Push est pris en charge via les paramètres de propriété [MessagingFactory.PrefetchCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.prefetchcount.aspx) ou [MessageReceiver.PrefetchCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.prefetchcount.aspx) . Lorsqu’ils sont zéro, le client AMQP l’utilise comme le crédit de lien.

Dans ce contexte, il est important de comprendre que l’horloge de l’expiration du verrou sur le message à l’intérieur de l’entité démarre lorsque le message provient de l’entité, et non lorsque le message est mis sur le réseau. Chaque fois que le client indique prêt à recevoir des messages à l’aide de credit de liaison, il est donc supposé être activement extraient des messages sur le réseau et d’être prêt à les gérer. Sinon, le verrou de message a peut-être expiré avant même de remettre le message. L’utilisation de crédit de la liaison de contrôle de flux doit refléter directement de la disponibilité immédiate de traiter expédiés vers le récepteur de messages disponibles.

En résumé, les sections suivantes fournissent une vue d’ensemble schématique du flux performative lors des interactions de l’API. Chaque section décrit une opération logique différente. Il se peut que certaines de ces interactions peuvent être « paresseux », c'est-à-dire qu’ils ne peuvent être exécutées une fois requis. Création d’un expéditeur de message risque pas une interaction du réseau jusqu'à ce que le premier message est envoyé ou demandé.

Les flèches indiquent le sens de déroulement performative.

#### <a name="create-message-receiver"></a>Créer le récepteur du Message

| Client                                                                                                                                                | Bus des services                                                                                                                                   |
|---------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------   |
| --> Joindre ()<br/>nom = {nom du lien,}<br/>gérer = {handle numérique},<br/>rôle =**récepteur**,<br/>source = {nom d’entité,}<br/>cible = {id de lien client}<br/>)         | Client est joint à l’entité en tant que récepteur                                                                                                         |
| Réponses de Bus de service y attacher la fin du lien                                                                                                     | <--joindre ()<br/>nom = {nom du lien,}<br/>gérer = {handle numérique},<br/>rôle =**l’expéditeur**,<br/>source = {nom d’entité,}<br/>cible = {id de lien client}<br/>)       |

#### <a name="create-message-sender"></a>Créer l’expéditeur du Message

| Client                                                                                                            | Bus des services                                                                                                           |
|------------------------------------------------------------------------------------------------------------------ |--------------------------------------------------------------------------------------------------------------------   |
| --> Joindre ()<br/>nom = {nom du lien,}<br/>gérer = {handle numérique},<br/>rôle =**l’expéditeur**,<br/>source = {id de lien client},<br/>cible = {nom de l’entité}<br/>)   | Aucune action                                                                                                                     |
| Aucune action                                                                                                                 | <--joindre ()<br/>nom = {nom du lien,}<br/>gérer = {handle numérique},<br/>rôle =**récepteur**,<br/>source = {id de lien client},<br/>cible = {nom de l’entité}<br/>)     |

#### <a name="create-message-sender-error"></a>Créer l’expéditeur du Message (erreur)

| Client                                                                                                            | Bus des services                                                           |
|------------------------------------------------------------------------------------------------------------------ |---------------------------------------------------------------------  |
| --> Joindre ()<br/>nom = {nom du lien,}<br/>gérer = {handle numérique},<br/>rôle =**l’expéditeur**,<br/>source = {id de lien client},<br/>cible = {nom de l’entité}<br/>)   | Aucune action                                                                     |
| Aucune action                                                                                                                 | <--joindre ()<br/>nom = {nom du lien,}<br/>gérer = {handle numérique},<br/>rôle =**récepteur**,<br/>source = null,<br/>cible = null<br/>)<br/><br/><--détacher ()<br/>gérer = {handle numérique},<br/>fermé =**true**,<br/>erreur = {erreur info}<br/>)  |

#### <a name="close-message-receiversender"></a>Fermer le Message destinataire/expéditeur

| Client                                            | Bus des services                                       |
|-------------------------------------------------  |-------------------------------------------------  |
| --> Détacher ()<br/>gérer = {handle numérique},<br/>fermé =**true**<br/>)    | Aucune action                                                 |
| Aucune action                                                 | <--détacher ()<br/>gérer = {handle numérique},<br/>fermé =**true**<br/>)    |

#### <a name="send-success"></a>Envoyer (succès)

| Client                                                                                                                        | Bus des services                                                                                           |
|------------------------------------------------------------------------------------------------------------------------------ |------------------------------------------------------------------------------------------------------ |
| --> (de transfert<br/>livraison-id = {handle numérique},<br/>livraison-balise = {handle binaire},<br/>réglée =**false**, plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   | Aucune action                                                                                                     |
| Aucune action                                                                                                                             | <--(de disposition<br/>rôle = récepteur,<br/>tout d’abord = {id de livraison},<br/>nom = {id de livraison},<br/>réglée =**true**,<br/>état =**accepté**<br/>)   |

#### <a name="send-error"></a>Envoyer (erreur)

| Client                                                                                                                        | Bus des services                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------ |-----------------------------------------------------------------------------------------------------------------------------  |
| --> (de transfert<br/>livraison-id = {handle numérique},<br/>livraison-balise = {handle binaire},<br/>réglée =**false**, plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   | Aucune action                                                                                                                             |
| Aucune action                                                                                                                             | <--(de disposition<br/>rôle = récepteur,<br/>tout d’abord = {id de livraison},<br/>nom = {id de livraison},<br/>réglée =**true**,<br/>état =**rejetée**()<br/>erreur = {erreur info}<br/>)<br/>)     |

#### <a name="receive"></a>Recevoir

| Client                                                                                                | Bus des services                                                                                                                   |
|------------------------------------------------------------------------------------------------------ |------------------------------------------------------------------------------------------------------------------------------ |
| --> (de flux<br/>lien-crédit = 1<br/>)                                                                                 | Aucune action                                                                                                                             |
| Aucune action                                                                                                     | < transférer ()<br/>livraison-id = {handle numérique},<br/>livraison-balise = {handle binaire},<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)     |
| --> la destruction)<br/>rôle =**récepteur**,<br/>tout d’abord = {id de livraison},<br/>nom = {id de livraison},<br/>réglée =**true**,<br/>état =**accepté**<br/>)   | Aucune action                                                                                                                             |

#### <a name="multi-message-receive"></a>Réception des messages multiples

| Client                                                                                                    | Bus des services                                                                                                                       |
|--------------------------------------------------------------------------------------------------------   |--------------------------------------------------------------------------------------------------------------------------------   |
| --> (de flux<br/>lien-crédit = 3<br/>)                                                                                 | Aucune action                                                                                                                                 |
| Aucune action                                                                                                         | < transférer ()<br/>livraison-id = {handle numérique},<br/>livraison-balise = {handle binaire},<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)     |
| Aucune action                                                                                                         | < transférer ()<br/>livraison-id = {handle numérique + 1},<br/>livraison-balise = {handle binaire},<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   |
| Aucune action                                                                                                         | < transférer ()<br/>livraison-id = {handle numérique + 2},<br/>livraison-balise = {handle binaire},<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   |
| --> la destruction)<br/>rôle = récepteur,<br/>tout d’abord = {id de livraison},<br/>nom = {id de livraison + 2},<br/>réglée =**true**,<br/>état =**accepté**<br/>)     | Aucune action                                                                                                                                 |

### <a name="messages"></a>Messages

Les sections suivantes expliquent les propriétés des sections message AMQP standard utilisées par Bus de services et leur correspondance avec les API de Bus de Service officiel.

#### <a name="header"></a>en-tête

| Nom de champ        | Utilisation de                             | Nom de l’API          |
|----------------   |-------------------------------    |---------------    |
| durable           | -                                 | -                 |
| priorité          | -                                 | -                 |
| durée de vie               | Durée de vie pour ce message     | [Propriété TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)     |
| premier acquéreur    | -                                 | -                 |
| nombre de livraison    | -                                 | [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx)   |

#### <a name="properties"></a>propriétés

| Nom de champ            | Utilisation de                                                                                                                             | Nom de l’API                                      |
|---------------------- |---------------------------------------------------------------------------------------------------------------------------------  |--------------------------------------------   |
| id de message            | Identificateur défini par l’application, de forme libre pour ce message. Utilisé pour la détection des doublons.                                         | [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)                                   |
| id de l’utilisateur               | Identificateur de l’utilisateur de défini par l’application, ne pas interprété par le Bus de Service.                                                              | Non accessible par le biais de l’API du Bus de Service.   |
| À                    | Identificateur de défini par l’application de destination, ne pas interprété par le Bus de Service.                                                       | [À](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.to.aspx)                                             |
| Objet               | Identificateur du rôle message défini par l’application, ne pas interprété par le Bus de Service.                                                   | [Étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)                                       |
| répondre à              | Indicateur de chemin-réponse définie par l’application, ne pas interprété par le Bus de Service.                                                         | [ReplyTo](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.replyto.aspx)                                       |
| id de la corrélation        | Identificateur de corrélation de défini par l’application, ne pas interprété par le Bus de Service.                                                       | [ID de corrélation](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.correlationid.aspx)                               |
| type de contenu          | Indicateur de type de contenu défini par l’application pour le corps, ne pas interprété par le Bus de Service.                                          | [ContentType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx)                                   |
| codage de contenu      | Défini par l’application indicateur codage de contenu pour le corps, ne pas interprété par le Bus de Service.                                      | Non accessible par le biais de l’API du Bus de Service.   |
| délai d’expiration absolue  | Déclare à laquelle absolue instantanée le message expire. Ignoré en entrée (ttl de l’en-tête est observé), qui fait autorité sur la sortie.   | [ExpiresAtUtc](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.expiresatutc.aspx)                                 |
| heure de création         | Déclare à partir duquel le message a été créé. Pas utilisée par le Bus des services                                                           | Non accessible par le biais de l’API du Bus de Service.   |
| id de groupe              | Identificateur défini par l’application d’un ensemble de messages connexes. Utilisé pour les sessions de Bus de Service.                                      | [ID de session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)                                   |
| groupe séquence        | Compteur d’identifier le numéro de séquence relative du message à l’intérieur d’une session. Ignoré par le Bus de Service.                         | Non accessible par le biais de l’API du Bus de Service.   |
| réponse-à-group-id     | -                                                                                                                                 | [ReplyToSessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.replytosessionid.aspx)                             |

## <a name="advanced-service-bus-capabilities"></a>Fonctions avancées de Bus des services

Cette section traite des fonctionnalités avancées de Bus des services Azure qui sont basées sur les extensions brouillon AMQP actuellement en cours de développement au sein du comité technique OASIS pour AMQP. Bus des services Azure implémente l’état le plus récent de ces projets et adoptera les modifications introduites comme ces brouillons atteint état standard.

> [AZURE.NOTE] Bus de service messagerie avancées sont prises en charge en quelque sorte un modèle demande/réponse. Les détails de ces opérations sont décrites dans le document [1.0 AMQP dans le Bus de Service : opérations de demande/réponse de](https://msdn.microsoft.com/library/azure/mt727956.aspx).

### <a name="amqp-management"></a>Gestion de AMQP

La spécification de la gestion de AMQP est la première des extensions brouillon que dont nous parlerons ici. Cette spécification définit un ensemble de mouvements de protocole utilisée en couche sur le protocole AMQP qui permettent des interactions de gestion avec l’infrastructure de messagerie sur AMQP. La spécification définit des opérations génériques telles que *créer*, *lire*, *mettre à jour*et *Supprimer* des entités à l’intérieur d’une infrastructure de messagerie et d’un ensemble d’opérations de requête.

Tous les mouvements de ces nécessitent une interaction demande/réponse entre le client et l’infrastructure de messagerie, et par conséquent la spécification définit comment modéliser ce modèle d’interaction sur AMQP : le client se connecte à l’infrastructure de messagerie, lance une session et crée une paire de liaisons. Sur une liaison, le client agissant en tant qu’expéditeur et d’autre part agit comme récepteur, créant ainsi une paire de liaisons qui peuvent agir comme un canal bidirectionnel.

| Opération logique            | Client                      | Bus des services                 |
|------------------------------|-----------------------------|-----------------------------|
| Créer le chemin d’accès de requête réponse | --> Joindre ()<br/>nom = {*nom du lien*,}<br/>gérer = {*handle numérique*},<br/>rôle =**l’expéditeur**,<br/>source =**null**,<br/>cible = « gestion des myentity / $»<br/>)                            |Aucune action                             |
|Créer le chemin d’accès de requête réponse                              |Aucune action                             | \<--joindre ()<br/>nom = {*nom du lien*,}<br/>gérer = {*handle numérique*},<br/>rôle =**récepteur**,<br/>source = null,<br/>cible = « myentity »<br/>)                            |
|Créer le chemin d’accès de requête réponse                              | --> Joindre ()<br/>nom = {*nom du lien*,}<br/>gérer = {*handle numérique*},<br/>rôle =**récepteur**,<br/>source = « gestion des myentity / $»,<br/>cible = « id de $myclient »<br/>)                            |                             |Aucune action
|Créer le chemin d’accès de requête réponse                              |Aucune action                             | \<--joindre ()<br/>nom = {*nom du lien*,}<br/>gérer = {*handle numérique*},<br/>rôle =**l’expéditeur**,<br/>source = « myentity »,<br/>cible = « id de $myclient »<br/>)                            |

Cette paire de liaisons en place, la mise en oeuvre de demande/réponse est simple : une requête est un message envoyé à une entité au sein de l’infrastructure de messagerie qui comprend ce modèle. Dans ce message de la demande, le champ de *réponse* dans la section *Propriétés* est défini à l’identificateur de *cible* pour le lien sur lequel fournir la réponse. L’entité de gestion traitera la demande et alors fournir la réponse sur le lien dont l’identificateur de *cible* correspond à l’identificateur indiqué *réponse-à* .

Le modèle requiert évidemment que le conteneur de client et de l’identificateur généré par le client pour la destination de réponse sont uniques pour tous les clients et, pour des raisons de sécurité, également difficiles à prévoir.

Les échanges de messages utilisés pour le protocole de gestion et tous les autres protocoles qui utilisent le même modèle de se produisent au niveau de l’application ; ils ne définissent pas de nouveaux mouvements de protocole au niveau AMQP. C’est intentionnel afin que les applications peuvent tirer parti immédiatement de ces extensions avec des piles AMQP 1.0 conformes.

Bus des services Azure n’implémente pas actuellement une des principales fonctionnalités de la spécification de la gestion, mais le modèle demande/réponse défini par la spécification de gestion est des éléments fondamentaux pour la fonctionnalité de sécurité en fonction des demandes et pour presque toutes les fonctionnalités avancées que nous aborderons dans les sections suivantes.

### <a name="claims-based-authorization"></a>Autorisation basée sur les revendications

Le brouillon de la spécification AMQP autorisation par des créances (CBS) s’appuie sur le modèle demande/réponse de la spécification de la gestion et décrit un modèle généralisé pour l’utilisation des jetons de sécurité fédérée avec AMQP.

Le modèle de sécurité par défaut de AMQP décrite dans l’introduction est basé sur SASL et s’intègre à la négociation de la connexion AMQP. À l’aide de SASL a l’avantage qu’il fournit un modèle extensible pour lesquels un ensemble de mécanismes définis à partir de laquelle n’importe quel protocole qui utilise formellement SASL peut bénéficier. Parmi ces mécanismes sont « PLAIN » pour le transfert de noms d’utilisateurs et mots de passe, « Externes » pour créer une liaison avec la sécurité au niveau des TLS, « ANONYMOUS » pour exprimer l’absence d’authentification/autorisation explicite et une grande variété de mécanismes supplémentaires qui permettent de passer d’authentification et/ou les informations d’identification de l’autorisation ou les jetons.

Intégration de SASL de AMQP présente deux inconvénients :

-   Toutes les informations d’identification et les jetons ont une portée limitées à la connexion. Une infrastructure de messagerie souhaiterez peut-être fournir un contrôle des accès différenciés sur une base par entité. Par exemple, ce qui permet du porteur d’un jeton à envoyer à la file d’attente A, mais pas à la file d’attente de B. Avec le contexte d’autorisation est ancré sur la connexion, il n’est pas possible d’utiliser une connexion unique et encore utiliser des jetons d’accès différents pour la file d’attente A et file B.

-   Les jetons d’accès sont en général uniquement valides pendant une période limitée. Cela oblige l’utilisateur à acquérir périodiquement des jetons et offre la possibilité à l’émetteur de jeton de refuser de délivrer un jeton fraîche si les autorisations d’accès de l’utilisateur ont été modifiés. Connexions de AMQP peuvent durer de très longues périodes. Le modèle SASL fournit uniquement la possibilité de définir un jeton au moment de la connexion, ce qui signifie que l’infrastructure de messagerie a pour déconnecter le client lorsque le jeton a expiré ou il doit accepter le risque de permettre des communications continues avec un client qui a des droits d’accès ont été révoqués dans l’intervalle.

La spécification AMQP CBS, mis en oeuvre par le Bus des services Azure, permet une solution élégante pour les deux de ces problèmes : il permet à un client pour associer des jetons d’accès à chaque nœud et mettre à jour ces jetons avant leur expiration, sans interrompre le flux de messages.

CBS définit un nœud Gestion virtuelle, nommé *$cbs*, fournis par l’infrastructure de messagerie. Le nœud Gestion accepte des jetons de tous les autres nœuds dans l’infrastructure de messagerie.

Le mouvement de protocole est un échange de demande/réponse, tel que défini par la spécification de la gestion. Cela signifie que le client établit une paire de liaisons avec le nœud *$cbs* et puis transmet une demande sur le lien sortant et attend la réponse sur le lien entrant.

Le message de demande possède les propriétés suivantes de l’application :

| Clé        | Facultatif | Type de valeur | Contenu de valeur                             |
|------------|----------|------------|--------------------------------------------|
| opération  | N°       | chaîne     | **Put-jeton**                                |
| type de       | N°       | chaîne     | Le type du jeton mis.            |
| nom       | N°       | chaîne     | À laquelle s’applique le jeton « public ». |
| expiration | Oui      | horodatage  | Le délai d’expiration du jeton.              |

La propriété *name* identifie l’entité à laquelle le jeton doit être associé. Il est le chemin d’accès à la file d’attente ou rubrique/abonnement Bus de Service. La propriété *type* identifie le type de jeton :

| Type de jeton                      | Description de jeton      | Type de corps           | Notes                                                    |
|---------------------------------|------------------------|---------------------|----------------------------------------------------------|
| amqp:jwt                        | Jeton de Web JSON (JWT)   | Valeur de AMQP (chaîne) | N’est pas encore disponible.  |
| amqp:SWT                        | Jeton de Web simple (SWT) | Valeur de AMQP (chaîne) | Prise en charge uniquement pour les jetons SWT émis par DAS/ACS          |
| ServiceBus.Windows.NET:sastoken | Jeton SAS de Bus de service  | Valeur de AMQP (chaîne) | -                                                        |

Jetons de conférant des droits. Bus de service sait trois droits fondamentaux : « Envoyer » permet l’envoi, « Écouter » permet de recevoir et de « Gérer » permet de manipuler des entités. Les jetons SWT émis par DAS/ACS explicitement incluent ces droits en tant que revendications. Jetons de service Bus SAS font référence aux règles configurées sur l’espace de noms ou d’une entité, et ces règles sont configurées avec les droits. Le jeton de signature avec la clé associée à cette règle qui rend donc le jeton express les droits respectifs. Le jeton associé à une entité à l’aide du *jeton de put* autorisera le client connecté à interagir avec l’entité par les droits de jetons. Un lien où le client joue le rôle de *l’expéditeur* exige la « envoyer » droite, prenez le rôle de *récepteur* nécessite le droit « D’écoute ».

Le message de réponse a les valeurs de *Propriétés de l’application* suivante

| Clé                | Facultatif | Type de valeur | Contenu de valeur                    |
|--------------------|----------|------------|-----------------------------------|
| code d’état        | N°       | int        | Code de réponse HTTP **[RFC2616]**. |
| description de l’état | Oui      | chaîne     | Description de l’état.        |

Le client peut appeler *put-jeton* à plusieurs reprises et pour toutes les entités dans l’infrastructure de messagerie. Les jetons sont portées au client en cours et ancrés sur la connexion courante, c'est-à-dire que le serveur va supprimer des jetons conservés lorsque la connexion est abandonnée.

L’implémentation actuelle de Bus de Service permet uniquement de CBS conjointement avec la méthode SASL « Anonyme ». Une connexion SSL/TLS doit toujours exister avant la négociation SASL.

Le mécanisme anonyme doit par conséquent être pris en charge par le client de AMQP 1.0 choisi. L’accès anonyme signifie que la négociation de la connexion initiale, y compris la création de la session initiale se produit sans le Service Bus savoir qui consiste à créer la connexion.

Une fois la session et la connexion est établie, attacher les liens à la *$cbs* nœud et l’envoi de la demande de *jeton de put* sont les seuls autorisés les opérations. Un jeton valide doit être défini avec une demande de *jeton de put* pour un nœud de l’entité dans les 20 secondes après que la connexion a été établie, sinon la connexion est supprimée unilatéralement par Bus de Service.

Le client est ensuite chargé de suivi d’expiration du jeton. Lorsqu’un jeton arrive à expiration, Bus de Service va supprimer rapidement tous les liens sur la connexion à l’entité correspondante. Pour éviter cela, le client peut remplacer le jeton pour le nœud par un autre à tout moment par le nœud de gestion virtual *$cbs* avec le même mouvement de *jetons de put* et sans compromettre le trafic de charge utile qui circulent sur différents liens.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur AMQP, consultez les liens suivants :

- [Vue d’ensemble du service Bus AMQP]
- [Prise en charge de AMQP 1.0 pour les files d’attente de Bus de Service partitionnée et rubriques]
- [AMQP dans le Bus des services pour Windows Server]

[Ce cours vidéo]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp/amqp1.png
[2]: ./media/service-bus-amqp/amqp2.png
[3]: ./media/service-bus-amqp/amqp3.png
[4]: ./media/service-bus-amqp/amqp4.png

[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
[Prise en charge de AMQP 1.0 pour les files d’attente de Bus de Service partitionnée et rubriques]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP dans le Bus des services pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx