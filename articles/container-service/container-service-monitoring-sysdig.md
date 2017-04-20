<properties
   pageTitle="Surveiller un cluster Service de conteneur Azure avec Sysdig | Microsoft Azure"
   description="Surveiller un cluster Service de conteneur Azure avec Sysdig."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Surveiller un cluster Service de conteneur Azure avec Sysdig

Dans cet article, nous déploierons Sysdig agents pour tous les nœuds de votre cluster Azure conteneur Service agent. Vous avez besoin d’un compte avec Sysdig pour cette configuration. 

## <a name="prerequisites"></a>Conditions préalables 

[Déployer](container-service-deployment.md) et [connecter](container-service-connect.md) un cluster configuré par Azure conteneur de Service. Explorez le [Marathon interface utilisateur](container-service-mesos-marathon-ui.md). Accédez à [http://app.sysdigcloud.com](http://app.sysdigcloud.com) pour configurer un compte de nuage Sysdig. 

## <a name="sysdig"></a>Sysdig

Sysdig est un service de surveillance qui vous permet de surveiller vos conteneurs dans votre cluster. Sysdig est connu pour aider à la résolution des problèmes, mais propose également vos mesures de surveillance de base pour le processeur, réseau, mémoire et e/s. Sysdig permet de voir quels conteneurs travaillent les plus difficiles ou essentiellement en utilisant le plus de mémoire et le processeur. Cette vue se trouve dans la section « Présentation », ce qui est actuellement en version bêta. 

![Interface utilisateur de Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurer un déploiement de Sysdig avec Marathon

Ces étapes vous montrent comment configurer et déployer des applications de Sysdig pour votre cluster avec Marathon. 

Accéder à votre interface de contrôleur de domaine/OS via [http://localhost : 80 /](http://localhost:80/) une fois dans l’interface utilisateur du contrôleur de domaine/OS atteindre le « univers », ce qui se trouve sur la partie inférieure gauche et recherchez « Sysdig ».

![Sysdig dans l’univers du contrôleur de domaine/OS](./media/container-service-monitoring-sysdig/sysdig1.png)

Pour terminer la configuration vous devez maintenant un compte de nuage Sysdig ou un compte d’essai gratuit. Une fois que vous êtes connecté au site Web de nuage Sysdig, cliquez sur votre nom d’utilisateur et sur la page, vous devez voir votre « clé d’accès ». 

![Clé de l’API de Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Ensuite, entrez votre clé d’accès dans la configuration de Sysdig dans l’univers du contrôleur de domaine/système d’exploitation. 

![Configuration de Sysdig dans l’univers du contrôleur de domaine/OS](./media/container-service-monitoring-sysdig/sysdig3.png)

Définir maintenant les instances 10000000 ainsi chaque fois qu’un nouveau nœud est ajouté au cluster Sysdig seront automatiquement déployer un agent sur ce nouveau nœud. Il s’agit d’une solution intermédiaire s’assurer que sysdig ne déploiera tous les nouveaux agents au sein du cluster. 

![Configuration de Sysdig dans les contrôleur de domaine/OS univers-instances](./media/container-service-monitoring-sysdig/sysdig4.png)

Une fois que vous avez installé le package de naviguer en arrière vers le Sysdig UI et vous serez en mesure d’Explorer les métriques d’utilisation est différent pour les conteneurs dans votre cluster. 