<properties
   pageTitle="Gestion des conteneurs conteneur Service Azure avec Docker par essaim | Microsoft Azure"
   description="Déployer des conteneurs à un Docker par essaims particulaires dans Azure conteneur Service"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Services-docker, conteneurs, Micro, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-with-docker-swarm"></a>Gestion des conteneurs avec Docker par essaim

Docker essaim fournit un environnement pour le déploiement des charges de travail sur un ensemble mis en commun des hôtes de Docker. Docker essaim utilise l’API native de Docker. Le flux de travail pour la gestion des conteneurs sur un Docker par essaim est presque identique à ce qu’elle serait sur un hôte de conteneur unique. Ce document fournit des exemples simples de déploiement des charges de travail dans une instance d’Azure conteneur Service de Docker par essaim. Pour une documentation plus détaillée sur Docker par essaims particulaires, consultez [Docker par essaim sur Docker.com](https://docs.docker.com/swarm/).

Conditions préalables pour les exercices présentés dans ce document :

[Créer un cluster par essaims particulaires dans Azure conteneur Service](container-service-deployment.md)

[Se connecter avec le cluster par essaims particulaires dans Azure conteneur Service](container-service-connect.md)

## <a name="deploy-a-new-container"></a>Déployer un nouveau conteneur

Pour créer un nouveau conteneur dans le Docker par essaims particulaires, utilisez le `docker run` commande (veiller à ce que vous avez ouvert un tunnel SSH pour les formes de base en fonction de la configuration requise ci-dessus). Cet exemple crée un conteneur de la `yeasy/simple-web` image :


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Une fois le conteneur a été créé, utilisez `docker ps` pour renvoyer des informations sur le conteneur. Notez ici que l’agent essaim qui héberge le conteneur est répertorié :


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Vous pouvez désormais accéder l’application qui s’exécute dans ce conteneur via le nom DNS public de l’équilibreur de charge de l’agent par essaims particulaires. Vous pouvez trouver ces informations dans le portail Azure :  


![Résultats de la visite réel](media/real-visit.jpg)  

Par défaut, l’équilibreur de charge a les ports 80, 8080 et 443 ouvert. Si vous souhaitez vous connecter sur un autre port, vous devrez ouvrir ce port sur l’équilibreur de charge Azure pour le Pool de l’Agent.

## <a name="deploy-multiple-containers"></a>Déployer plusieurs conteneurs

Plusieurs conteneurs sont lancées, par l’exécution de 'exécuter docker' plusieurs fois, vous pouvez utiliser la `docker ps` commande pour voir qui héberge les conteneurs sont en cours d’exécution. Dans l’exemple ci-dessous, trois conteneurs sont répartis à égalité sur les trois agents essaim :  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Déployer des conteneurs à l’aide de composer de Docker

Vous pouvez utiliser Docker composer pour automatiser le déploiement et la configuration de plusieurs conteneurs. Pour ce faire, assurez-vous qu’un tunnel SSH (Secure Shell) a été créé et que la variable DOCKER_HOST a été définie (voir les conditions préalables ci-dessus).

Créez un fichier docker-compose.yml sur votre système local. Pour ce faire, utilisez cet [exemple](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Exécutez `docker-compose up -d` pour démarrer les déploiements de conteneur :


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Enfin, la liste des conteneurs en cours d’exécution est retournée. Cette liste répertorie les conteneurs qui ont été déployées à l’aide de composer de Docker :


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturellement, vous pouvez utiliser `docker-compose ps` d’examiner uniquement les conteneurs définis dans votre `compose.yml` fichier.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur Docker par essaim](https://docs.docker.com/swarm/)
