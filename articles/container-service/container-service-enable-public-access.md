<properties
   pageTitle="Activer l’accès du Public à une application d’ACS | Microsoft Azure"
   description="Comment faire pour activer l’accès du public à un Service de conteneur Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Services-docker, conteneurs, Micro, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="timlt"/>

# <a name="enable-public-access-to-an-azure-container-service-application"></a>Activer l’accès public à une application de Service de conteneur Azure

N’importe quel conteneur de contrôleur de domaine/OS dans le [pool du public de l’agent](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) d’ACS est automatiquement exposée à internet. Par défaut, les ports **80**, **443**, **8080** sont ouverts, et tout conteneur (public) écoute sur ces ports sont accessibles. Cet article vous indique comment ouvrir plusieurs ports pour vos applications Azure conteneur de service.

## <a name="open-a-port-portal"></a>Ouvrir un port (portail) 

Tout d’abord, nous devons ouvrir le port que nous voulons.

1. Ouvrez une session sur le portail.
2. Recherchez le groupe de ressources que vous avez déployé le Service de conteneur Azure.
3. Activez l’équilibrage de charge de l’agent (qui est un nom semblable à **XXXX-agent-lb-XXXX**).

    ![Équilibreur de charge service de conteneur Azure](media/container-service-dcos-agents/agent-load-balancer.png)

4. Cliquez sur les **sondes** , puis sur **Ajouter**.

    ![Sondes de l’équilibreur de charge service de conteneur Azure](media/container-service-dcos-agents/add-probe.png)

5. Remplissez le formulaire de la sonde et cliquez sur **OK**.

  	| Champ | Description |
  	| ----- | ----------- |
  	| Nom  | Nom descriptif de la sonde. |
  	| Port  | Le port du conteneur de test. |
  	| Chemin d’accès  | (En mode HTTP) Le chemin d’accès de site Web relatif à la sonde. HTTPS non pris en charge. |
  	| Intervalle | Tente de l’intervalle de temps entre la sonde, en quelques secondes. |
  	| Seuil de mauvais fonctionnement | Nombre de sonde consécutif de tentatives avant de considérer le conteneur incorrect. | 
    

6. Retour à des propriétés de l’équilibrage de charge de l’agent, cliquez sur **règles d’équilibrage de charge** , puis sur **Ajouter**.

    ![Règles d’équilibrage de la charge Azure conteneur service](media/container-service-dcos-agents/add-balancer-rule.png)

7. Remplissez le formulaire d’équilibrage de la charge, puis cliquez sur **OK**.

  	| Champ | Description |
  	| ----- | ----------- |
  	| Nom  | Nom descriptif de l’équilibreur de charge. |
  	| Port  | Le port entrant public. |
  	| Port du serveur principal | Port public interne du conteneur pour acheminer le trafic vers. |
  	| Pool de back-end | Les conteneurs de ce pool sera la cible de l’équilibrage de la charge. |
  	| Sonde | La sonde est utilisée pour déterminer si une cible dans le **pool de serveur principal** est en bon état. |
  	| Persistance de la session | Détermine comment le trafic à partir d’un client doit être traité pendant la durée de la session.<br><br>**None**: les requêtes successives émanant du même client peuvent être gérés par n’importe quel conteneur.<br>**IP du client**: les demandes successives à partir de la même adresse IP de client sont gérées par le même conteneur.<br>**IP du client et protocole**: les demandes successives à partir de la même combinaison d’adresse IP et le protocole de client sont gérées par le même conteneur. |
  	| Délai d’inactivité | TCP (uniquement) En quelques minutes, la durée de conservation d’un client TCP/HTTP ouvrir sans se baser sur *keep-alive* messages. |

## <a name="add-a-security-rule-portal"></a>Ajouter une règle de sécurité (portail)

Ensuite, nous devons ajouter une règle de sécurité qui achemine le trafic à partir de notre port ouvert via le pare-feu.

1. Ouvrez une session sur le portail.
2. Recherchez le groupe de ressources que vous avez déployé le Service de conteneur Azure.
3. Sélectionnez le groupe de sécurité de réseau agent **public** (qui est un nom semblable à **XXXX-agent-public-nsg-XXXX**).

    ![Groupe de sécurité Azure conteneur service réseau](media/container-service-dcos-agents/agent-nsg.png)

4. Sélectionnez les **règles de sécurité de trafic entrant** , puis sur **Ajouter**.

    ![Règles du groupe de sécurité Azure conteneur service réseau](media/container-service-dcos-agents/add-firewall-rule.png)

5. Indiquez la règle de pare-feu pour autoriser le port de votre public, puis cliquez sur **OK**.

  	| Champ | Description |
  	| ----- | ----------- |
  	| Nom  | Nom descriptif de la règle de pare-feu. |
  	| Priorité | Rang de priorité de la règle. Le nombre plus la priorité est faible. |
  	| Source | Restreindre la plage d’adresses IP entrante pour être interdits ou autorisés par cette règle. Permet de **n’importe quel** pour ne pas spécifier une restriction. |
  	| Service | Sélectionnez un ensemble de services prédéfinis qu'est cette règle de sécurité. Dans le cas contraire, utilisez **personnalisé** pour créer votre propre. |
  	| Protocole | Restreindre le trafic basé sur **TCP** ou **UDP**. Permet de **n’importe quel** pour ne pas spécifier une restriction. |
  	| Plage de port | Lorsque le **Service** est **personnalisé**, spécifie la plage de ports qui affecte de cette règle. Vous pouvez utiliser un seul port, par exemple **80**, comme **1024-1500**. |
  	| Action | Autoriser ou refuser le trafic qui répond aux critères. |

## <a name="next-steps"></a>Étapes suivantes

Obtenir des informations sur la différence entre [agents de contrôleur de domaine/OS publiques et privées](container-service-dcos-agents.md).

En savoir plus sur la [gestion de vos conteneurs DC/système d’exploitation](container-service-mesos-marathon-ui.md).