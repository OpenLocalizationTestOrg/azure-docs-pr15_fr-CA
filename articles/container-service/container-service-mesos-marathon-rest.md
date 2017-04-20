<properties
   pageTitle="Gestion des conteneurs Azure Service du conteneur par le biais de l’API REST | Microsoft Azure"
   description="Déployer des conteneurs à un cluster Azure conteneur Service Mesos à l’aide de l’API REST de Marathon."
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

# <a name="container-management-through-the-rest-api"></a>Gestion du conteneur par le biais de l’API REST

Contrôleur de domaine/système d’exploitation fournit un environnement de déploiement et de mise à l’échelle des charges de travail ordonné en clusters, tout en faisant abstraction du matériel sous-jacent. Sur le contrôleur de domaine/système d’exploitation, il est un framework qui gère la planification et l’exécution du calcul des charges de travail.

Bien que les structures sont disponibles pour de nombreuses charges de travail les plus courants, ce document décrit comment vous pouvez créer et mettre à l’échelle des déploiements de conteneur à l’aide de Marathon. Avant de suivre ces exemples, vous avez besoin d’un cluster/OS du contrôleur de domaine qui est configuré dans le Service de conteneur d’Azure. Vous devez également disposer d’une connexion à distance à ce cluster. Pour plus d’informations sur ces éléments, consultez les articles suivants :

- [Déployer un cluster Service de conteneur Azure](container-service-deployment.md)
- [Connexion à un cluster Service de conteneur Azure](container-service-connect.md)

Après que vous être connecté au cluster Service de conteneur d’Azure, vous pouvez accéder le DC/système d’exploitation et des API reste connexes par le biais de ports http://localhost:local. Les exemples de ce document supposent que vous créez un tunnel sur le port 80. Par exemple, le point de terminaison Marathon peut être contacté à `http://localhost/marathon/v2/`. Pour plus d’informations sur les différentes API, consultez la documentation de la mésosphère de l' [API de Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) et l' [API de Chronos](https://mesos.github.io/chronos/docs/api.html)et de la documentation Apache pour l' [API du Planificateur de Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Rassembler des informations de contrôleur de domaine/OS et Marathon

Avant de déployer les conteneurs au cluster DC/OS, recueillir des informations sur le cluster DC/système d’exploitation, comme le nom et l’état actuel des agents DC/système d’exploitation. Pour ce faire, vous devez interroger la `master/slaves` point de terminaison de l’API REST de contrôleur de domaine/système d’exploitation. Si tout se passe bien, vous verrez une liste des agents de contrôleur de domaine/système d’exploitation et de plusieurs propriétés pour chaque.

```bash
curl http://localhost/mesos/master/slaves
```

À présent, utilisez le Marathon `/apps` point de terminaison pour vérifier les déploiements d’applications en cours sur le cluster de contrôleur de domaine/système d’exploitation. S’il s’agit d’un nouveau cluster, vous verrez un tableau vide pour les applications.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Déployer un conteneur au format Docker

Vous déployez des conteneurs au format Docker de via Marathon en utilisant un fichier JSON qui décrit le déploiement prévu. L’exemple suivant déploiera le conteneur Nginx, port de liaison 80 de l’agent cc/OS vers le port 80 du conteneur. Notez également que la propriété 'acceptedResourceRoles' a la valeur 'slave_public'. Cette action déploie le conteneur à un agent dans le jeu d’échelle orienté public agent.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Pour déployer un conteneur au format Docker, créer votre propre fichier JSON, ou utiliser l’exemple fourni à la [Démo Azure conteneur de Service](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Stocker dans un emplacement accessible. Ensuite, pour déployer le conteneur, exécutez la commande suivante. Spécifiez le nom du fichier JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Le résultat sera semblable au suivant :

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Maintenant, si vous interrogez le Marathon pour les applications, cette nouvelle application affiche dans la sortie.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Mettre à l’échelle vos conteneurs

Vous pouvez également utiliser l’API de Marathon à évoluer ou mettre à l’échelle dans les déploiements d’applications. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons mettre à l’échelle cette sortie à trois instances d’une application. Pour ce faire, créez un fichier JSON en utilisant le texte JSON suivant et stockez-la dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante à l’échelle de l’application.

>[AZURE.NOTE] L’URI est http://localhost/marathon/v2/apps/ et le code de l’application à l’échelle. Si vous utilisez l’exemple Nginx fourni ici, l’URI est http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Enfin, la requête le point de terminaison Marathon pour les applications. Vous verrez qu’il y a maintenant trois des conteneurs Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Utilisation de PowerShell pour cet exercice : interaction API REST de Marathon avec PowerShell

Vous pouvez effectuer ces actions mêmes sur un système Windows à l’aide des commandes PowerShell.

Pour collecter des informations concernant le cluster DC/système d’exploitation, tels que les noms des agents et l’état de l’agent, exécutez la commande suivante.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Vous déployez des conteneurs au format Docker de via Marathon en utilisant un fichier JSON qui décrit le déploiement prévu. L’exemple suivant déploiera le conteneur Nginx, port de liaison 80 de l’agent cc/OS vers le port 80 du conteneur.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Créer votre propre fichier JSON, ou utiliser l’exemple fourni à la [Démo Azure conteneur de Service](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Stocker dans un emplacement accessible. Ensuite, pour déployer le conteneur, exécutez la commande suivante. Spécifiez le nom du fichier JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Vous pouvez également utiliser l’API de Marathon à évoluer ou mettre à l’échelle dans les déploiements d’applications. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons mettre à l’échelle cette sortie à trois instances d’une application. Pour ce faire, créez un fichier JSON en utilisant le texte JSON suivant et stockez-la dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante à l’échelle de l’application.

> [AZURE.NOTE] L’URI est http://localhost/marathon/v2/apps/ et le code de l’application à l’échelle. Si vous utilisez l’exemple Nginx fourni ici, l’URI est http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les points de terminaison HTTP de Mesos]( http://mesos.apache.org/documentation/latest/endpoints/).
- [En savoir plus sur l’API REST de Marathon]( https://mesosphere.github.io/marathon/docs/rest-api.html).
