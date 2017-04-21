#<a name="azure-service-bus"></a>Bus des services Azure

Si une application ou un service s’exécute dans le nuage ou sur site, il doit souvent interagir avec d’autres applications ou services. Pour fournir un moyen largement utile pour ce faire, Azure offre le Bus de Service. Cet article présente une vue de cette technologie, décrivant ce qu’il s’agit et pourquoi vous pouvez souhaiter l’utiliser.

## <a name="service-bus-fundamentals"></a>Principes de base de Bus des services
Situations d’appels différentes pour les différents styles de communication. Envoyer et recevoir des messages via une file d’attente simple, laissant les applications est parfois la meilleure solution. Dans d’autres situations, une file d’attente ordinaire ne suffit pas ; une file d’attente avec un mécanisme de publication et d’abonnement est préférable. Et dans certains cas, tout ce qui est vraiment nécessaire est une connexion entre les applications & #151 ; files d’attente ne sont pas obligatoires. Bus de service propose les trois options, permettant à vos applications d’interagir de différentes manières.

Bus de service est un service de nuage partagé, ce qui signifie que le service est partagé par plusieurs utilisateurs. Chaque utilisateur, comme un développeur d’applications, crée un *espace de noms*, puis définit les mécanismes de communication au sein de cet espace de noms dont elle a besoin. [Figure 1](#Fig1) montre à quoi cela ressemble.

<a name="Fig1"></a>![Diagramme de Bus des services Azure][svc-bus]
 
**Figure 1 : Bus des services fournit un service mutualisée pour connecter des applications via le nuage.**

Dans un espace de noms, vous pouvez utiliser une ou plusieurs instances de quatre mécanismes de communication différents, chacun d'entre eux connecte à des applications d’une manière différente. Les choix sont :

- *Files d’attente*, qui permettent la communication bidirectionnelle sur un. Chaque file d’attente joue le rôle d’intermédiaire (parfois appelé un *broker*) qui stocke les messages envoyés jusqu'à ce qu’ils sont reçus. Chaque message est reçu par un destinataire unique.
- *Rubriques*, qui permettent la communication unidirectionnel à l’aide *d’abonnements*- une rubrique unique peut avoir plusieurs abonnements. Comme une file d’attente, une rubrique agit comme un broker, mais chaque abonnement peut éventuellement utiliser un filtre pour recevoir uniquement les messages qui répondent à des critères spécifiques.
- *Relais*, qui fournissent une communication bidirectionnelle. Contrairement aux files d’attente et aux rubriques, un relais ne stocke pas en vol messages : c’est pas un service broker. Au lieu de cela, il les transmet simplement à l’application de destination.
- *Concentrateurs d’événements*, qui fournissent des événements et télémétrie pénètre dans le nuage à grande échelle, avec une latence faible et haute fiabilité.

Lorsque vous créez une file d’attente, le sujet, le relais ou le concentrateur d’événements, vous lui donnez un nom. Combiné avec votre votre espace de noms, ce nom crée un identificateur unique pour l’objet. Applications, vous peuvent fournir ce nom au Bus de Service, puis utiliser cette file d’attente, rubrique, relais ou événement Hub pour communiquer entre eux. 

Pour utiliser un de ces objets, les applications de Windows peuvent utiliser Windows Communication Foundation (WCF). Pour les files d’attente, des rubriques et des concentrateurs d’événements Windows applications peuvent également utiliser les API de messagerie définies par le Bus de Service. Pour faciliter ces objets à utiliser dans les applications non Windows, Microsoft fournit des kits de développement logiciel pour les autres langues, Node.js et Java. Vous pouvez également accéder des files d’attente, des rubriques et des concentrateurs d’événement à l’aide des API de reste sur HTTP. 

Il est important de comprendre que même si le Service de Bus lui-même s’exécute dans le nuage (autrement dit, dans les centres de données de Microsoft Azure), anywhere peuvent être exécuté par les applications qui l’utilisent. Bus de Service vous permet de connecter des applications qui s’exécutent sur Azure, par exemple, ou des applications qui s’exécutent à l’intérieur de votre propre centre de données. Vous pouvez également l’utiliser pour vous connecter à une application qui s’exécute sur Azure, ou une autre plateforme en nuage avec une application sur site ou avec des tablettes et des téléphones. Il est même possible de connecter des appareils électroménagers, de capteurs et d’autres périphériques à une application centrale ou à un autre. Bus de service est un mécanisme de communication générique dans le nuage est accessible à peu près tout. Comment vous utilisez cela dépend de ce que vos applications doivent faire.


## <a name="queues"></a>Files d’attente

Supposons que vous souhaitez connecter deux applications à l’aide d’une file d’attente de Bus de Service. [La figure 2](#Fig2) illustre cette situation.

<a name="Fig2"></a>![Diagramme de files d’attente de Bus de Service][queues]
 
**Figure 2 : Fournissent des files d’attente de Bus de Service queuing asynchrones à sens unique.**

Le processus est simple : un expéditeur envoie un message à une file d’attente de Bus de Service et un RECEPTEUR récupère ce message ultérieurement. Une file d’attente peut avoir uniquement un récepteur unique, comme l’illustre [la Figure 2](#Fig2) , ou plusieurs applications peuvent lire à partir de la même file d’attente. Dans ce dernier cas, chaque message est lu par un récepteur-pour un service de multidiffusion vous devez utiliser à la place une rubrique.

Chaque message comporte deux parties : un jeu de propriétés, chaque une paire clé/valeur et un corps de message binaire. Leur utilisation dépend de ce qu’une application essaie de faire. Par exemple, une application qui envoie un message à propos d’une vente récente peut-être inclure les propriétés *vendeur = « Ava »* et *Montant = 10000*. Le corps du message contient une image numérisée, le contrat de vente signé ou, si il n’existe pas, il vous suffit restent vide.

Un récepteur peut lire un message dans une file d’attente de Bus de Service de deux manières différentes. La première option, appelée *ReceiveAndDelete*, supprime un message de la file d’attente et le supprime immédiatement. C’est simple, mais si le récepteur se bloque avant la fin de traitement du message, le message sera perdu. Dans la mesure où il est retiré de la file d’attente, aucun autre récepteur ne peut y accéder. 

La deuxième option, *PeekLock*, est destinée à résoudre ce problème. Comme ReceiveAndDelete, une lecture de PeekLock supprime un message de la file d’attente. Elle ne supprime pas le message, toutefois. Au lieu de cela, il verrouille le message, la rendant invisible pour les autres destinataires, puis attend qu’un des trois événements :

- Si le destinataire traite le message avec succès, il appelle *complète*et la file d’attente supprime le message. 
- Si le récepteur décide qu’il ne peut pas traiter correctement le message, il appelle *l’abandonner*. La file d’attente puis supprime le verrou du message et la rend disponible aux autres récepteurs.
- Si le récepteur appelle aucune de ces dans un délai configurable (par défaut, 60 secondes), la file d’attente suppose que le récepteur a échoué. Dans ce cas, il se comporte comme si le récepteur avait appelé Abandon, rendant un message disponible aux autres récepteurs.

Notez que ce qui peut arriver ici : le même message peut être remis deux fois, peut-être à deux récepteurs différents. Applications à l’aide de files d’attente de Bus de Service doivent être préparées pour cela. Pour faciliter la détection des doublons, que chaque message possède une propriété MessageID unique qui, par défaut, reste la même quelle que soit la procédure autant de fois, le message est lu à partir d’une file d’attente. 

Files d’attente sont utiles dans nombreuses situations. Ils permettent aux applications de communiquer même lorsque les deux ne sont pas en cours d’exécution en même temps, ce qui s’avère particulièrement utile avec les applications mobiles et de lot. Une file d’attente avec plusieurs récepteurs fournit également l’équilibrage de charge automatique, dans la mesure où les messages envoyés sont répartis sur ces récepteurs.


## <a name="topics"></a>Rubriques

Utile comme ils le sont, files d’attente ne sont pas toujours la solution idéale. Parfois, les rubriques du Bus de Service sont préférables. [La figure 3](#Fig3) illustre ce concept.

<a name="Fig3"></a>![Diagramme des rubriques de Bus de Service et des abonnements][topics-subs]
 
**Figure 3 : Basée sur le filtre à qu'une application d’abonnement spécifie, il peut recevoir certains ou tous les messages envoyés à une rubrique du Bus de Service.**

Une rubrique est similaire à bien des égards à une file d’attente. Messages d’envoi des expéditeurs à une rubrique de la même façon qu’ils soumettent des messages à une file d’attente et les messages identiques comme des files d’attente. La différence est que rubriques permettent à chaque application réceptrice de créer son propre abonnement en définissant un *filtre*. Un abonné s’affiche uniquement les messages qui correspondent à ce filtre. Par exemple, [la Figure 3](#Fig3) illustre un expéditeur et une rubrique avec trois abonnés, chacun avec son propre filtre :

- L’abonné 1 reçoit uniquement les messages qui contiennent la propriété *vendeur = « Ava »*.
- Abonné 2 reçoit les messages qui contiennent la propriété *vendeur = « Ruby »* et/ou contiennent une propriété *Montant* dont la valeur est supérieure à 100 000. Ruby est sans doute le directeur des ventes, et donc qu’elle souhaite voir ses propres ventes et toutes les ventes de gros que qui les rend.
- Abonné 3 a défini son filtre à *True*, ce qui signifie qu’il reçoit tous les messages. Par exemple, cette application peut être responsable de la maintenance d’une piste d’audit, et donc il a besoin de voir tous les messages.

Comme avec les files d’attente, les abonnés à une rubrique peuvent lire des messages à l’aide de ReceiveAndDelete ou PeekLock. Toutefois, contrairement aux files d’attente, un message envoyé à un sujet peut être reçu par plusieurs abonnés. Cette approche, appelée communément *publier et s’abonner*, est utile lorsque plusieurs applications peuvent être intéressées par les mêmes messages. Chaque abonné peut exploiter en définissant le filtre droit, seulement la partie du flux de message qu’il souhaite voir.


## <a name="relays"></a>Relais

Les files d’attente et les rubriques fournissent une communication asynchrone unidirectionnelle par un intermédiaire financier. Flux de trafic dans les deux sens, et il n’existe aucune connexion directe entre les expéditeurs et les récepteurs. Mais que se passe-t-il si vous ne souhaitez pas que cela ? Supposons que vos applications ont besoin envoyer et recevoir des messages, ou peut-être vous un lien direct entre les deux et vous n’avez pas besoin d’un intermédiaire financier pour stocker les messages. Pour les scénarios de ce type, les Bus de Service fournit des relais, comme le montre la [Figure 4](#Fig4) .

<a name="Fig4"></a>![Diagramme de relais de Bus de Service][relay]
 
**Figure 4 : Relais de Bus des services fournit la communication synchrone, bidirectionnelle entre les applications.**

Est-ce la question évidente à poser à propos de relais : Pourquoi utiliser un ? Même si les files d’attente est inutile, pourquoi rendre les applications communiquent via un service de cloud et non seulement interagissent directement ? La réponse est que parler directement peut être plus difficile que vous ne le pensez.

Supposons que vous souhaitez connecter deux applications sur site, à la fois en cours d’exécution à l’intérieur des centres de données d’entreprise. Chacune de ces applications se trouve derrière un pare-feu, et chaque datacenter utilise probablement la traduction d’adresses réseau (NAT). Le pare-feu bloque les données entrantes sur les ports quelques et NAT implique que chaque application s’exécute sur l’ordinateur de n’a pas une adresse IP fixe que vous pouvez atteindre directement à partir de l’extérieur du centre de données. Sans une aide supplémentaire, la connexion de ces applications sur l’Internet public est problématique.

Un relais de Bus de Service fournit à cette aide. Pour communiquer de façon bidirectionnelle via un relais, chaque application établit une connexion TCP sortante avec le Bus des services, puis maintient ouverte. Toutes les communications entre les deux applications suivra sur ces connexions. Dans la mesure où chaque connexion a été établie à partir de l’intérieur du centre de données, le pare-feu autorise le trafic entrant à chaque application sans ouverture de nouveaux ports. Cette approche contourne également le problème NAT, dans la mesure où chaque application possède un point de terminaison compatible dans le nuage tout au long de la communication. En échangeant des données via le relais, les applications peuvent éviter les problèmes qui sinon serait communication difficile. 

Pour utiliser les relais de Bus des services, les applications s’appuient sur Windows Communication Foundation (WCF). Bus de service fournit des liaisons WCF qui le rendent simple pour les applications Windows d’interagir via le relais. Les applications qui utilisent déjà WCF peuvent généralement simplement spécifier l’un de ces liaisons, puis communiquent entre eux via un relais. Contrairement aux files d’attente et aux rubriques, demande toutefois des relais à partir d’applications non Windows, lorsque possible, des efforts de programmation ; Aucune bibliothèque standard n’est fournis.

Contrairement aux files d’attente et les rubriques, les applications ne créent pas explicitement de relais. En revanche, lorsqu’une application qui souhaite recevoir des messages établit une connexion TCP avec le Bus de Service, un relais est créé automatiquement. Lorsque la connexion est interrompue, le relais est supprimé. Pour permettre à une application de trouver le relais créé par un récepteur spécifique, le Bus de Service fournit un Registre qui permet aux applications de localiser un relais spécifique par son nom.

Les relais sont la solution idéale lorsque vous avez besoin d’une communication directe entre les applications. Par exemple, considérez un système de réservation de billets d’avion en cours d’exécution dans un centre de données local qui doit être accessible à partir de kiosques d’archivage, les périphériques mobiles et les autres ordinateurs. Les applications en cours d’exécution sur tous ces systèmes pourraient compter sur relais de Bus de Service dans le nuage pour communiquer, partout où ils peuvent être en cours d’exécution.

## <a name="event-hubs"></a>Concentrateurs d’événement

Concentrateurs d’événement est un système de réception hautement évolutive qui peut traiter des millions d’événements par seconde, permettant à votre application de traiter et analyser les quantités énormes de données produites par vos applications et de périphériques connectés. Par exemple, vous pouvez utiliser un concentrateur d’événements à collecter les données de performance de moteur direct à partir d’un parc de voitures. Une fois collectées dans les concentrateurs d’événement, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analytique en temps réel ou d’un cluster de stockage. Pour plus d’informations sur les concentrateurs d’événement, consultez la [vue d’ensemble des concentrateurs de l’événement][].

## <a name="summary"></a>Résumé

La connexion d’applications a toujours fait partie de la création de solutions complètes et la plage de scénarios qui requièrent des applications et des services pour communiquer entre eux est définie pour augmenter plus que des applications et périphériques sont connectés à Internet. Grâce aux technologies basées sur le nuage d’y parvenir par l’intermédiaire de files d’attente, les rubriques, les relais et les concentrateurs d’événement, Bus de Service vise à rendre cette fonction essentielle plus largement disponible et facile à implémenter.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png
[Vue d’ensemble des concentrateurs événements]: https://msdn.microsoft.com/library/azure/dn836025.aspx
