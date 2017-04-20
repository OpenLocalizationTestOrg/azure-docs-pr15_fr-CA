<properties
   pageTitle="Gestion des conteneurs Azure Service du conteneur par le biais de l’interface utilisateur web | Microsoft Azure"
   description="Déployer des conteneurs à un service de cluster Azure conteneur à l’aide de l’interface utilisateur du web Marathon."
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
   ms.date="09/19/2016"
   ms.author="timlt"/>

# <a name="container-management-through-the-web-ui"></a>Gestion du conteneur par le biais de l’interface utilisateur web

Contrôleur de domaine/système d’exploitation fournit un environnement de déploiement et de mise à l’échelle des charges de travail ordonné en clusters, tout en faisant abstraction du matériel sous-jacent. Sur le contrôleur de domaine/système d’exploitation, il est un framework qui gère la planification et l’exécution du calcul des charges de travail.

Alors que les infrastructures sont disponibles pour de nombreuses charges de travail les plus courants, ce document décrit comment vous pouvez créer et mettre à l’échelle des déploiements de conteneur avec Marathon. Avant de suivre ces exemples, vous aurez besoin d’un cluster/OS du contrôleur de domaine qui est configuré dans le Service de conteneur d’Azure. Vous devez également disposer d’une connexion à distance à ce cluster. Pour plus d’informations sur ces éléments, consultez les articles suivants :

- [Déployer un cluster Service de conteneur Azure](container-service-deployment.md)
- [Se connecter à un cluster Service de conteneur Azure](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Explorez le contrôleur de domaine/système d’exploitation l’interface utilisateur

Avec un tunnel SSH (Secure Shell) est établi, accédez à http://localhost/. Il charge de l’interface utilisateur du web DC/système d’exploitation et affiche des informations sur le cluster, tel que les ressources utilisées, des agents actifs et des services en cours d’exécution.

![INTERFACE UTILISATEUR DU CONTRÔLEUR DE DOMAINE/OS](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Explorez le Marathon l’interface utilisateur

Pour afficher l’interface utilisateur de Marathon, accédez à http://localhost/Marathon. À partir de cet écran, vous pouvez démarrer un nouveau conteneur ou une autre application sur le cluster Azure conteneur Service DC/système d’exploitation. Vous pouvez également voir d’informations sur les conteneurs et les applications en cours d’exécution.  

![Marathon l’interface utilisateur](media/dcos/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Déployer un conteneur au format Docker

Pour déployer un nouveau conteneur à l’aide de Marathon, cliquez sur le bouton **Créer une Application** et entrez les informations suivantes dans le formulaire :

Champ           | Valeur
----------------|-----------
ID              | nginx
Image           | nginx
Réseau         | Partie d’un pont
Port de l’hôte       | 80
Protocole        | TCP

![Nouvelle interface utilisateur d’Application : général](media/dcos/dcos4.png)

![Nouvelle Application de l’interface utilisateur : conteneur de Docker](media/dcos/dcos5.png)

![Nouvelle Application interface utilisateur--Ports et découverte du Service](media/dcos/dcos6.png)

Si vous souhaitez mapper de manière statique le port conteneur à un port de l’agent, vous devez utiliser le Mode de JSON. Pour ce faire, activez l’Assistant Nouvelle Application **Mode de JSON** à l’aide de la bascule. Entrez les données suivantes dans le `portMappings` section de la définition d’application. Cet exemple lie le port 80 du conteneur au port 80 de l’agent du contrôleur de domaine/système d’exploitation. Vous pouvez passer cet Assistant JSON mode après avoir apporté cette modification.

```none
"hostPort": 80,
```

![Nouvelle Application de l’interface utilisateur : exemple de port 80](media/dcos/dcos13.png)

Le cluster de contrôleur de domaine/système d’exploitation est déployé avec un ensemble d’agents publics et privés. Pour le cluster doit pouvoir accéder à des applications à partir d’Internet, vous avez besoin déployer les applications à un agent public. Pour ce faire, sélectionnez l’onglet **facultatif** de l’Assistant Nouvelle Application, puis entrez **slave_public** pour les **Rôles de ressource acceptés**.

![Nouvelle interface utilisateur d’Application--l’agent public](media/dcos/dcos14.png)

Sur la page principale de Marathon, vous pouvez voir l’état de déploiement pour le conteneur.

![Page principale de marathon UI--statut du déploiement conteneur](media/dcos/dcos7.png)

Lorsque vous revenez à l’interface de l’utilisateur (http://localhost/) de web de DC/système d’exploitation, vous verrez qu’une tâche (dans ce cas, un conteneur au format Docker) est en cours d’exécution sur le cluster de contrôleur de domaine/système d’exploitation.

![Contrôleur de domaine/OS web interface utilisateur--la tâche en cours d’exécution sur le cluster](media/dcos/dcos8.png)

Vous pouvez également voir le nœud de cluster qui exécute la tâche sur.

![Contrôleur de domaine/OS interface web--nœud du cluster](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Mettre à l’échelle vos conteneurs

Vous pouvez utiliser l’interface utilisateur de Marathon à l’échelle le nombre d’instances d’un conteneur. Pour ce faire, accédez à la page **Marathon** , sélectionnez le conteneur que vous souhaitez mettre à l’échelle et cliquez sur le bouton de **l’échelle** . Dans la boîte de dialogue **Échelle Application** , entrez le nombre d’instances de conteneur que vous souhaitez et sélectionnez **Application d’échelle**.

![Marathon UI--boîte de dialogue échelle Application](media/dcos/dcos10.png)

Après la mise à l’échelle, vous verrez plusieurs instances de la même tâche, réparties sur les agents DC/système d’exploitation.

![Tableau de bord DC/OS web interface utilisateur : la tâche de propagation entre les agents](media/dcos/dcos11.png)

![Contrôleur de domaine/OS web interface utilisateur--nœuds](media/dcos/dcos12.png)

## <a name="next-steps"></a>Étapes suivantes

- [Travailler avec le contrôleur de domaine/système d’exploitation et de l’API de Marathon](container-service-mesos-marathon-rest.md)

Plongée dans le Service de conteneur Azure avec Mesos

> [AZURE. Azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos de vidéo]]
