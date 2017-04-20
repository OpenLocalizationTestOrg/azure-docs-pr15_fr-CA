<properties 
    pageTitle="Connexions protocole hybride à relais Azure | Microsoft Azure"
    description="zure relais hybride connexions protocole Guide."
    services="service-bus"
    documentationCenter="na"
    authors="clemensv"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="sethm" />

# <a name="azure-relay-hybrid-connections-protocol"></a>Connexions protocole hybride à relais Azure

Relais Azure est un des piliers des fonctionnalités clés de la plateforme Azure Service Bus. Du relais nouvelles « Connexions hybride » est une évolution sécurisée, protocole ouvert basée sur HTTP et le protocole WebSocket.

Il remplace l’ancienne, également appelée fonction de « BizTalk Services » a été fondée sur un protocole propriétaire. L’intégration des connexions d’hybrides dans les Services d’application Azure va continuer de fonctionner en tant que-est.

« Connexions hybride » permet d’établir une communication bidirectionnelle, binaire de flux de données entre deux applications en réseau, par laquelle les deux parties peuvent résider derrière des pare-feu ou des traducteurs d’adresses réseau.

Ce document décrit les interactions de côté client avec le relais hybride connexions pour la connexion des clients dans les rôles de récepteur et émetteur et comment écouteurs d’acceptent de nouvelles connexions.

## <a name="interaction-model"></a>Modèle d’interaction

Le relais hybride connexions connecte deux parties en fournissant un point de rendez-vous dans le nuage Azure que les deux parties de découvrir et de se connecter au point de vue de leur propre réseau. Ce point de rendez-vous est appelé « Hybride de connexion » dans ce document et autres documentations, dans les API et également dans le portail Azure. Le point de terminaison de service hybride connexions sera dénommé « service » pour le reste de ce document.

La nomenclature établie par de nombreuses autres API réseau utilise le modèle d’interaction :

Il existe un écouteur qui indique tout d’abord de prêt pour gérer des connexions entrantes et par la suite les accepte qu’elles arrivent. De l’autre côté, il existe un connexion client qui se connectera à l’écouteur, attendu de cette connexion acceptée pour établir un chemin de communication bidirectionnelle. « Se connecter », « Écoute », « Accepter » est les mêmes termes que vous trouverez sur le socket de la plupart des API.

N’importe quel modèle de communication relayé a des parties à établir des connexions sortantes vers un point de terminaison de service, qui permet de le « écouteur » également un « client » dans Utilisation de langage commun et peut également générer d’autres surcharges de la terminologie ; le terme précis, que nous avons par conséquent d’utiliser pour les connexions hybride est la suivante :

Les programmes installés sur les deux côtés d’une connexion sont appelés « client », dans la mesure où ils sont des clients au service. Le client qui attend et accepte les connexions est le « écouteur » ou dit dans le rôle « écouteur ». Le client qui initie une nouvelle connexion vers un port d’écoute du service est appelé le « sender » ou dans le rôle « expéditeur ».

## <a name="listener-interactions"></a>Interactions du récepteur

L’écouteur a quatre interactions avec le service ; tous les détails de fil sont décrites plus loin dans ce document, dans la section Références.

### <a name="listen"></a>Écouter

Pour indiquer la disponibilité sur le service si un écouteur est prêt à accepter les connexions, il crée une connexion de socket web sortante. La négociation de connexion porte le nom d’une connexion hybride définis dans l’espace de noms de relais et un jeton de sécurité qui confère le « écoute » de droit sur ce nom. Lorsque le support web est accepté par le service, l’inscription est terminée et le socket établie web reste actif en tant que « canal de contrôle » pour l’activation de toutes les interactions résultantes. Le service permet jusqu'à 25 écouteurs simultanées sur une connexion hybride. S’il existe des écouteurs de 2 ou plus actifs, les connexions entrantes sont équilibrées entre eux dans un ordre aléatoire ; répartition équitable n’est pas garantie.

### <a name="accept"></a>Accepter

Chaque fois qu’un expéditeur ouvre une nouvelle connexion sur le service, le service du prélèvement et avertir un des écouteurs actives sur la connexion hybride. La notification est envoyée à l’écouteur sur le canal de contrôle pour l’ouvrir en tant que JSON message contenant l’URL du point de terminaison Web socket l’écouteur doit se connecter à pour accepter la connexion.

L’URL peut et doit être utilisée directement par le port d’écoute sans travail supplémentaire ; les informations codées ne sont valides que pendant une courte période de temps, essentiellement pour aussi longtemps que l’expéditeur est disposé à attendre pour la connexion est établie à bout en bout, mais avec un maximum de 30 secondes. L’URL seulement peut être utilisée que pour la tentative d’établissement d’une connexion. Dès que la connexion de socket Web avec l’URL de rendez-vous est établie, toute activité supplémentaire sur ce socket Web est relayée à partir et à l’expéditeur, sans aucune intervention ni l’interprétation par le service.

### <a name="renew"></a>Renouveler 

Le jeton de sécurité qui doit être utilisé pour inscrire le port d’écoute et de maintenir le canal de contrôle peut expirer alors que l’écouteur est actif. L’expiration du jeton n’affecte pas les connexions en cours, mais il entraînera le canal de contrôle peuvent être supprimées par le service au ou peu après le moment de l’expiration. Le mouvement « renouveler » est un message JSON qui l’écouteur peut envoyer à remplacer le jeton associé au canal de contrôle, de sorte que le canal de contrôle peut être conservé pendant des périodes prolongées.

### <a name="ping"></a>Ping 

Si le canal de contrôle reste inactif pendant un certain temps, intermédiaires dans le même temps, par exemple charge équilibreurs ou NAT peut supprimer la connexion TCP. Le mouvement de la commande « ping » permet d’éviter que par l’envoi d’une petite quantité de données sur le canal de rappel de tous les membres de l’itinéraire réseau que la connexion est destinée à être actif, et il sert également un test d’activité pour le port d’écoute. Si la commande ping échoue, le canal de contrôle doit être considéré comme inutilisable et le récepteur doit se reconnecter.

## <a name="sender-interaction"></a>Interaction de l’expéditeur

L’expéditeur n’a qu’une seule interaction avec le service, il se connecte.

### <a name="connect"></a>Se connecter

Le mouvement « se connecter » ouvre un socket Web sur le service, en fournissant le nom de la connexion d’hybrides et d’une (facultatif, mais requis par défaut) le jeton de sécurité donnant l’autorisation « Envoyer » dans la chaîne de requête. Le service puis interagissent avec l’écouteur de la manière décrite ci-dessus et que l’écouteur à créer une connexion de rendez-vous qui sera joint à ce socket Web. Une fois le socket Web a été accepté, toutes les interactions supplémentaires sur le socket Web sera donc un récepteur connecté.

## <a name="interaction-summary"></a>Synthèse d’interaction

Le résultat de ce modèle d’interaction est que le client de l’expéditeur est sorti de la négociation avec un socket Web « propre », qui est connecté à un port d’écoute et qui a besoin d’aucune PRÉAMBULES supplémentaire ou la préparation. Cela permet à pratiquement n’importe quel implémentation du client Web socket existante facilement tirer parti du service hybride connexions en fournissant une URL correctement construite dans leur couche de client Web socket.

La connexion rendezvous Web Socket que le récepteur obtient par le biais de l’interaction d’accepter est également propre et peut être transmise à toute implémentation du serveur socket Web existante avec une abstraction supplémentaire minimale qui fait la distinction entre les opérations de « accepter » sur les ports d’écoute de leur infrastructure réseau local et les connexions hybride à distance « accepter ».

## <a name="protocol-reference"></a>Référence de protocole

Cette section décrit les détails des interactions protocole décrites ci-dessus.

Toutes les connexions de socket Web sont effectuées sur le port 443 sous la forme d’une mise à niveau de 1.1 HTTPS, qui sont couramment abstraites par certains framework de socket Web ou API. La description ici est conservée implémentation neutre, sans proposer une infrastructure spécifique.

## <a name="listener-protocol"></a>Protocole de port d’écoute

Le protocole du port d’écoute est constitué de deux mouvements de connexion et de trois opérations de message.

### <a name="listener-control-channel-connection"></a>Connexion de canal de contrôle de port d’écoute

Le canal de contrôle est ouvert à la création d’une connexion de socket Web à :

*wss : / / {adresse namespace} /* *$servicebus* */* *hybridconnection /**{path} ? sb-hc-action =... & sb-hc-id =... & sb-hc-token =... »*

L' *adresse de l’espace de noms* est le nom de domaine complet de l’espace de noms Azure relais qui héberge la connexion hybride, généralement du formulaire {*monnom}. servicebus.windows.net.*

Les options de paramètre de chaîne de requête sont comme suit.

| Param        | Obligatoire ? | Description                                                                                                                                                                                        |
|--------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SB-hc-action | Oui       | Pour le rôle de l’écouteur, le paramètre doit être **sb-hc-action = écoute**                                                                                                                                |
| {path}       | Oui       | Le chemin d’accès de l’espace de noms URL codée de la connexion hybride préconfigurés pour enregistrer cet écouteur sur. Cette expression est ajoutée à la liste des * **$servicebus**/**hybridconnection /*** partie du chemin d’accès. |
| SB-hc-jeton  | Oui\*     | Le récepteur doit fournir un valide, codé URL Service Bus partagé jeton d’accès pour l’espace de noms ou une connexion hybride qui confère le droit **d’écouter** .                                           |
| SB-hc-id     | N°        | Cet ID facultatif fournis par le client permet de bout en bout pour le suivi de diagnostic.                                                                                                                             |

Si la connexion de Socket de Web échoue parce que le chemin d’accès de connexion de hybride ne pas enregistré, ou un jeton non valide ou manquant ou une autre erreur, les commentaires de l’erreur fournira l’utilisation du modèle de commentaires d’état HTTP 1.1 régulière. Description de l’état contient un erreur-id de suivi qui peut être communiqué au Support d’Azure :

| Code | Erreur          | Description                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Non trouvé      | Le **chemin d’accès** de hybride connexion n’est pas valide ou l’URL de base est incorrect |
| 401  | Non autorisé   | Le jeton de sécurité est manquant ou incorrect ou non valide                  |
| 403  | Interdit      | Le jeton de sécurité n’est pas valide pour ce chemin d’accès pour cette action          |
| 500  | Erreur interne | Un problème dans le service                                    |

Si la connexion du socket est intentionnellement arrêtée par le service après que qu’il a été initialement configuré, la raison de cette opération seront communiqués à l’aide de Web socket protocole erreur code approprié avec un message d’erreur descriptif qui inclura également un id de suivi. Le service n’éteint pas le canal de contrôle sans rencontrer une condition d’erreur. Tout arrêt est client contrôlée.

| État de WS | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Le chemin d’accès de la connexion de hybride a été supprimé ou désactivé.                           |
| 1008      | Le jeton de sécurité a expiré et la stratégie d’autorisation est violée. |
| 1011      | Quelque chose s’est passé à l’intérieur du service.                                           |

### <a name="accept-handshake"></a>Accepter le protocole de transfert 

La notification d’acceptation est envoyée par le service pour le port d’écoute sur le canal de contrôle établie précédemment sous la forme d’un message JSON dans un cadre Web socket. Il n’y a pas de réponse à ce message.

Le message contient un objet JSON nommé « accepter », qui définit les propriétés suivantes pour l’instant :

-   **adresse** – la chaîne d’URL à utiliser pour l’établissement de support pour le service Web pour accepter une connexion entrante.

-   **id** – identificateur unique pour cette connexion. Si l’id a été fourni par le client de l’expéditeur, c’est la valeur fournie de l’expéditeur, dans le cas contraire, elle est une valeur générée par le système.

-   **connectHeaders** – tous les en-têtes HTTP qui ont été fournies par l’expéditeur, qui inclut également le Sec-WebSocket-Protocol et les en-têtes Sec-WebSocket-Extensions pour le point de terminaison de relais.

| Accepter le Message                                                                     |
|------------------------------------------------------------------------------------|
```json
{                                                                                  
    "accept" : {                                                                                    
        "address" : "wss://168.61.148.205:443/$servicebus/hybridconnection/{path}?...",                                                                          
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736\_G0\_G1",                         
        "connectHeaders" : {                                                                
            "Host" : "…",                                                                       
            "Sec-WebSocket-Protocol" : "…",                                                     
            "Sec-WebSocket-Extensions" : "…"                                                    
        }                                                                                     
    }
}
```

L’URL de l’adresse fournie dans le message JSON est utilisé par le port d’écoute pour établir le Socket Web pour accepter ou rejeter le socket de l’expéditeur.

#### <a name="accepting-the-socket"></a>Acceptation du socket

Pour accepter, l’écouteur établit une connexion WebSocket à l’adresse fournie.

L’esprit que pour la période d’aperçu, l’adresse URI peut utiliser une adresse IP sans système d’exploitation et le certificat TLS fourni par le serveur échoue à la validation de cette adresse. Cela sera supprimée lors de la visualisation.

Si le message « accepter » comporte un en-tête « Sec-WebSocket-Protocol », il est prévu que l’écouteur n’accepte la WebSocket si elle prend en charge ce protocole et qu’il définit l’en-tête de la WebSocket est établi.

Il en va de même pour l’en-tête « Sec-WebSocket-Extensions ». Si le framework prend en charge une extension, il doit définir l’en-tête de la réponse du côté *serveur* de la poignée de main « Sec-WebSocket-Extensions » requise pour l’extension.

L’URL doit être utilisée en tant que-est d’établir le socket accept, mais contient les paramètres suivants :

| Param        | Obligatoire ? | Description                                                                                                                                                                                                                                                                                   |
|--------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SB-hc-action | Oui       | Pour accepter un socket, le paramètre doit être **sb-hc-action = accepter**                                                                                                                                                                                                                          |
| {path}       | Oui       | Le chemin d’accès de l’espace de noms URL codée de la connexion hybride préconfigurés pour enregistrer cet écouteur sur. Cette expression est ajoutée à la liste des * **$servicebus**/**hybridconnection /*** partie du chemin d’accès.                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The path expression MAY be extended with a suffix and a query string expression that follows the registered name after a separating forward slash.                                                                                                                                             
                                                                                                                                                                                                                                                                                                                           
                            This allows the sender client to pass dispatch arguments to the accepting listener when it is not possible to include HTTP headers.                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The expectation is that the listener framework will parse out the fixed path portion and the registered name from the path and make the remainder, possibly without any query string arguments prefixed by “sb-“, available to the application for deciding whether to accept the connection.  
                                                                                                                                                                                                                                                                                                                           
                            For more detail see the “Sender Protocol” section below.                                                                                                                                                                                                                                       |
| SB-hc-id | No        | Consultez la description **id** .                                                                                                                                                                                                                                                              |

Si une erreur existe, le service peut répondre comme suit :

| Code | Erreur          | Description                         |
|------|----------------|-------------------------------------|
| 403  | Interdit      | L’URL n’est pas valide.               |
| 500  | Erreur interne | Un problème dans le service |

Une fois la connexion établie, le serveur sera arrêté le support Web lorsque le socket Web expéditeur arrête, ou avec l’état suivant

| État de WS | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Le client expéditeur arrête la connexion                                        |
| 1001      | Le chemin d’accès de la connexion de hybride a été supprimé ou désactivé.                           |
| 1008      | Le jeton de sécurité a expiré et la stratégie d’autorisation est violée. |
| 1011      | Quelque chose s’est passé à l’intérieur du service.                                           |

#### <a name="rejecting-the-socket"></a>Rejet du socket

Rejet du socket après inspection le message « accepter » de requiert une négociation similaire afin que le code d’état et description communiquer la raison du rejet de l’état peuvent être transmis à l’expéditeur.

Le choix de conception de protocole ici est d’utiliser un protocole de transfert WebSocket (qui est conçu pour mettre fin à un état d’erreur définis) afin que les implémentations de l’écouteur client peuvent continuer à compter sur un client WebSocket et n’êtes pas obligé d’utiliser un extra, sans système d’exploitation client HTTP.

Pour rejeter le socket, le client prend l’adresse URI à partir du message « accept » et il ajoute deux paramètres de chaîne de requête :

| Param             | Obligatoire ? | Description                             |
|-------------------|-----------|-----------------------------------------|
| statusCode        | Oui       | Code d’état HTTP numérique                |
| statusDescription | Oui       | La raison du rejet lisible |

L’URI qui en résulte est ensuite utilisé pour établir une connexion WebSocket ; là encore, l’esprit que le certificat TLS ne peut pas correspondre à l’adresse lors de la visualisation, validation peut donc être désactivé.

Lorsqu’il a terminé correctement, cette négociation intentionnellement échoue avec un code d’erreur HTTP 410, dans la mesure où aucun WebSocket n’a été établie. Si une erreur se produit, les options sont les :

| Code | Erreur          | Description                         |
|------|----------------|-------------------------------------|
| 403  | Interdit      | L’URL n’est pas valide.               |
| 500  | Erreur interne | Un problème dans le service |

### <a name="listener-token-renewal"></a>Renouvellement de jeton d’écouteur

Lorsque le jeton de l’écouteur est sur le point d’expirer, il peut le remplacer par l’envoi d’un message de bloc de texte au service via le canal de contrôle établie. Le message contient un objet JSON nommé « renewToken », qui définit la propriété suivante pour l’instant :

-   **jeton** – un valide, codé URL Service Bus partagé jeton d’accès pour l’espace de noms ou de connexion hybride qui confère le droit **d’écouter** .

| renewToken de Message                                                                                                                                                    
|------------------------------------------------------------------------------------------------|
```json
{
    "renewToken" : {      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fHybridConnection1%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
    }                                                                                                                                                            
}
```                                                                                                                                                                      

Si la jeton validation échoue, l’accès est refusé et service cloud fermera le websocket de canal de contrôle avec une erreur, sinon il n’est aucune réponse.

| État de WS | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1008      | Le jeton de sécurité a expiré et la stratégie d’autorisation est violée. |

<a name="sender-protocol"></a>Protocole de l’expéditeur
---------------

Le protocole de l’expéditeur est similaire au mode d’établissement d’un port d’écoute. L’objectif est la transparence maximale du socket Web de bout en bout. L’adresse pour se connecter à la même que celle de l’écouteur, mais diffère de « action » et le jeton doit avoir une autorisation différente :

*wss : / / {adresse namespace} /* *$servicebus* */* *hybridconnection /**{path} ? sb-hc-action =... & sb-hc-id =... \[& sbc-hc-token =... \]*

L' *adresse de l’espace de noms* est le nom de domaine complet de l’espace de noms Azure relais qui héberge la connexion hybride, généralement du formulaire {*monnom}. servicebus.windows.net.*

La demande peut contenir arbitraires en-têtes HTTP supplémentaires, y compris par l’application. Tous les en-têtes fournis au récepteur de flux et se trouve sur l’objet « connectHeader » du message de contrôle de « accepter ».

Les options de paramètre de chaîne de requête sont comme suit.

| Param        | Obligatoire ? | Description                                                                                                                                                                                                       |
|--------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SB-hc-action | Oui       | Pour le rôle de l’écouteur, le paramètre doit être **action = se connecter**                                                                                                                                                    |
| {path}       | Oui       | Le chemin d’accès de l’espace de noms URL codée de la connexion hybride préconfigurés pour enregistrer cet écouteur sur.                                                                                                               
                                                                                                                                                                                                                                               
                            The path expression MAY be extended with a suffix and a query string expression to communicate further                                                                                                             
                                                                                                                                                                                                                                               
                            If the Hybrid Connection is registered under the path “hyco”, the path expression can be “**hyco/**suffix?param=value&…” followed by the query string parameters defined here. A complete expression may then be:  
                                                                                                                                                                                                                                               
                            *wss://{ns}/**$servicebus**/**hybridconnection/*** **hyco/**suffix?param=value*& sb-hc-action=…&sb-hc-id=…\[&sbc-hc-token=…\]*                                                                                     
                                                                                                                                                                                                                                               
                            The path expression is passed through to the listener in the address URI contained in the “accept” control message.                                                                                                |
| SB-hc-jeton | Yes\*     | Le récepteur doit fournir un valide, codé URL Service Bus partagé jeton d’accès pour l’espace de noms ou une connexion hybride qui confère le droit **d’Envoyer** .                                                            | | SB-hc-id | No        | ID facultatif qui permet le suivi de diagnostic de bout en bout et est mis à disposition l’écouteur pendant la négociation de l’accepter.                                                                                       |

Si la connexion de Socket de Web échoue parce que le chemin d’accès de connexion de hybride ne pas enregistré, ou un jeton non valide ou manquant ou une autre erreur, les commentaires de l’erreur fournira l’utilisation du modèle de commentaires d’état HTTP 1.1 régulière. Description de l’état contient un erreur-id de suivi qui peut être communiqué au Support d’Azure :

| Code | Erreur          | Description                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Non trouvé      | Le **chemin d’accès** de hybride connexion n’est pas valide ou l’URL de base est incorrect |
| 401  | Non autorisé   | Le jeton de sécurité est manquant ou incorrect ou non valide                  |
| 403  | Interdit      | Le jeton de sécurité n’est pas valide pour ce chemin d’accès pour cette action          |
| 500  | Erreur interne | Un problème dans le service                                    |

Si la connexion du socket est intentionnellement arrêtée par le service après que qu’il a été initialement configuré, la raison de cette opération seront communiqués à l’aide de Web socket protocole erreur code approprié avec un message d’erreur descriptif qui inclura également un id de suivi.

| État de WS | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1000      | L’écouteur arrête le socket.                                                 |
| 1001      | Le chemin d’accès de la connexion de hybride a été supprimé ou désactivé.                           |
| 1008      | Le jeton de sécurité a expiré et la stratégie d’autorisation est violée. |
| 1011      | Quelque chose s’est passé à l’intérieur du service.                                           |

## <a name="next-steps"></a>Étapes suivantes :

- [Forum aux questions de relais](relay-faq.md)
- [Créer un espace de noms](relay-create-namespace-portal.md)
- [Mise en route de .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Mise en route de nœud](relay-hybrid-connections-node-get-started.md)