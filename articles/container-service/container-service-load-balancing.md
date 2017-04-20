<properties
   pageTitle="Charger des conteneurs de solde dans un cluster Service de conteneur Azure | Microsoft Azure"
   description="Équilibrer les charges sur plusieurs conteneurs dans un cluster Service de conteneur d’Azure."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, Micro-services, le contrôleur de domaine/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Conteneurs de solde de charge dans un cluster Service de conteneur Azure

Dans cet article, nous allons explorer la création d’un équilibreur de charge interne dans un un contrôleur de domaine/OS managed Service de conteneur d’Azure à l’aide de Marathon-LB. Cela vous permettra de mettre à l’échelle horizontalement de vos applications. Il permettra également profiter de l’agent public et privé clusters en plaçant votre équilibreurs de charge sur le cluster publique et vos conteneurs d’application sur le cluster privé.

## <a name="prerequisites"></a>Conditions préalables

[Déployer une instance de Service de conteneur Azure](container-service-deployment.md) avec type d’orchestrator DC/système d’exploitation et [vous assurer que votre client peut se connecter à votre cluster](container-service-connect.md). 

## <a name="load-balancing"></a>Équilibrage de la charge

Il existe deux couches d’équilibrage de la charge dans le cluster Service de conteneur que vous allez générer : 

  1. Azure équilibreur de charge fournit des points d’entrée publics (ceux que les utilisateurs finaux seront atteints). Cela est fournie automatiquement par le Service de conteneur Azure et est, par défaut, configuré pour présenter le port 80, 443 et 8080.
  2. L’équilibreur de charge Marathon (marathon-lb) achemine les demandes entrantes pour les instances du conteneur que les demandes de service. Comme nous mettre à l’échelle les conteneurs fournissant notre service web, marathon-lb s’adapte dynamiquement. Ce programme d’équilibrage de charge n’est pas fourni par défaut dans votre conteneur de Service, mais il est très facile à installer.

## <a name="marathon-load-balancer"></a>Équilibrage de la charge marathon

Équilibrage de la charge marathon reconfigure dynamiquement elle-même en se basant sur les conteneurs que vous avez déployé. Il est également résistant à la perte d’un conteneur ou un agent - si cela se produit, Apache Mesos simplement redémarrera le conteneur ailleurs et marathon-lb s’adapte.

Pour installer l’équilibrage de charge Marathon que vous pouvez utiliser soit le contrôleur de domaine/système d’exploitation web interface utilisateur ou la ligne de commande.

### <a name="install-marathon-lb-using-dcos-web-ui"></a>Installer Marathon-LB à l’aide de l’interface utilisateur Web de contrôleur de domaine/OS

  1. Cliquez sur « Univers »
  2. Recherche de 'Marathon-LB'
  3. Cliquez sur 'Installer'

![L’installation via l’Interface Web de contrôleur de domaine/OS marathon-lb](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dcos-cli"></a>Installer à l’aide de l’interface CLI de contrôleur de domaine/OS de Marathon-LB

Après l’installation de l’interface CLI de contrôleur de domaine/système d’exploitation et assurer, vous pouvez vous connecter à votre cluster, exécutez la commande suivante à partir de votre ordinateur client :

```bash
dcos package install marathon-lb
```

Cette commande installe automatiquement l’équilibrage de la charge sur le cluster d’agents publics.

## <a name="deploy-a-load-balanced-web-application"></a>Déployer une charge équilibrée d’Application Web

Maintenant que nous avons le package marathon-lb, nous pouvons déployer un conteneur d’application que nous souhaitons pour équilibrer la charge. Pour cet exemple, nous déploierons un simple serveur web à l’aide de la configuration suivante :

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * Définir la valeur de `HAProxy_0_VHOST` pour le nom de domaine complet de l’équilibreur de charge de vos agents. Il s’agit de la forme `<acsName>agents.<region>.cloudapp.azure.com`. Par exemple, si vous créez un cluster Service de conteneur avec le nom `myacs` dans la région `West US`, le nom de domaine complet est `myacsagents.westus.cloudapp.azure.com`. Vous pouvez également trouver cette pour l’équilibreur de charge avec un « agent » dans le nom lors d’une recherche au moyen des ressources du groupe de ressources que vous avez créé pour le Service de conteneur dans [Azure portal](https://portal.azure.com).
  * La valeur de la servicePort à un port > = 10 000. Cela identifie le service qui est en cours d’exécution dans ce conteneur--marathon-lb utilise pour identifier les services qui il doit équilibrer.
  * Définir le `HAPROXY_GROUP` étiquette « externe ».
  * La valeur `hostPort` sur 0. Cela signifie que le Marathon allouera arbitrairement un port disponible.
  * La valeur `instances` pour le nombre d’instances que vous souhaitez créer. Vous pouvez toujours évoluer ces ultérieurement.

Il est important de noing par défaut que marathon déploiera le cluster privé, cela signifie que le déploiement ci-dessus sera uniquement accessible via votre équilibreur de charge, qui est généralement le comportement que nous souhaité.

### <a name="deploy-using-the-dcos-web-ui"></a>Déployer à l’aide de l’interface utilisateur Web de contrôleur de domaine/OS

  1. Visitez la page Marathon à http://localhost/marathon (après la configuration de votre [tunnel SSH](container-service-connect.md) , puis cliquez sur`Create Appliction`
  2. Dans le `New Application` de dialogue cliquez sur `JSON Mode` dans le coin supérieur droit
  3. Collez le JSON ci-dessus dans l’éditeur
  4. Cliquez sur`Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>Déployer à l’aide de l’interface CLI de contrôleur de domaine/OS

Pour déployer cette application avec l’interface CLI de contrôleur de domaine/OS copier simplement le JSON ci-dessus dans un fichier appelé `hello-web.json`et exécutez :

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Équilibreur de charge Azure

Par défaut, équilibrage de la charge Azure expose les ports 80, 8080 et 443. Si vous utilisez un de ces trois ports (comme nous dans l’exemple ci-dessus), puis il n’avez rien à faire. Vous devez être en mesure de rencontrer le nom de domaine complet de votre équilibreur de charge de l’agent--et chaque fois que vous actualisez, de choisir un de vos serveurs trois web de manière alternée. Toutefois, si vous utilisez un port différent, vous devez ajouter une règle de répétition alternée et une sonde de l’équilibreur de charge pour le port que vous avez utilisé. Vous devez pour cela à partir de la [CLI d’Azure](../xplat-cli-azure-resource-manager.md), avec les commandes `azure network lb rule create` et `azure network lb probe create`. Vous pouvez également le faire en utilisant le portail Azure.


## <a name="additional-scenarios"></a>Scénarios supplémentaires

Vous pouvez avoir un scénario où vous utilisez différents domaines d’exposer des services différents. Par exemple :

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

Pour ce faire, consultez les [hôtes virtuels](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), qui fournissent un moyen d’associer des domaines à des chemins spécifiques marathon-lb.

Ou bien, vous pouvez exposer des ports différents et les remappe vers le bon service derrière marathon-lb. Par exemple :

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation de contrôleur de domaine/système d’exploitation pour sur plus [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).
