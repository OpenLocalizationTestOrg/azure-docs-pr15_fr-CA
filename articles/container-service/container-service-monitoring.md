<properties
   pageTitle="Surveiller un cluster Service de conteneur Azure avec Datadog | Microsoft Azure"
   description="Surveiller un cluster Service de conteneur Azure avec Datadog. Utilisez l’interface utilisateur du web DC/système d’exploitation pour déployer les agents Datadog à votre cluster."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, DC/OS, Docker essaim, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"   
   ms.date="07/28/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Surveiller un cluster Service de conteneur Azure avec Datadog

Dans cet article, nous déploierons Datadog agents pour tous les nœuds de votre cluster Azure conteneur Service agent. Vous devez Datadog un compte pour cette configuration. 

## <a name="prerequisites"></a>Conditions préalables 

[Déployer](container-service-deployment.md) et [connecter](container-service-connect.md) un cluster configuré par Azure conteneur de Service. Explorez le [Marathon interface utilisateur](container-service-mesos-marathon-ui.md). Accédez à [http://datadoghq.com](http://datadoghq.com) pour configurer un compte Datadog. 

## <a name="datadog"></a>Datadog 

Datadog est un service de surveillance qui rassemble des données d’analyse à partir de vos conteneurs dans votre cluster Service de conteneur d’Azure. Datadog est un tableau de bord intégration Docker où vous pouvez consulter les mesures spécifiques au sein de vos conteneurs. Métriques recueillies auprès de vos conteneurs sont organisés par unité centrale, mémoire, réseau et d’e/s. Datadog fractionne les mesures dans les conteneurs et les images. Un exemple de quoi ressemble l’interface utilisateur pour l’utilisation du processeur est inférieure.

![Interface utilisateur de Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurer un déploiement de Datadog avec Marathon

Ces étapes vous montrent comment configurer et déployer des applications de Datadog pour votre cluster avec Marathon. 

Accéder à votre interface de contrôleur de domaine/OS via [http://localhost : 80 /](http://localhost:80/). Une fois dans l’interface utilisateur du contrôleur de domaine/OS atteindre le « univers » qui se trouve sur la partie inférieure gauche, puis recherchez « Datadog » et cliquez sur « Installer ».

![Package Datadog dans l’univers du contrôleur de domaine/OS](./media/container-service-monitoring/datadog1.png)

Maintenant pour terminer la configuration vous devez un compte Datadog ou un compte d’essai gratuit. Une fois que vous êtes connecté à l’aspect du site Web de Datadog à gauche et atteindre les intégrations -> puis l’API. 

![Clé de l’API de Datadog](./media/container-service-monitoring/datadog2.png)

Ensuite, entrez votre clé API dans la configuration de Datadog dans l’univers du contrôleur de domaine/système d’exploitation. 

![Configuration de Datadog dans l’univers du contrôleur de domaine/OS](./media/container-service-monitoring/datadog3.png) 

Définir des instances dans la configuration ci-dessus à 10000000, chaque fois qu’un nouveau nœud est ajouté au cluster Datadog va déployer automatiquement un agent vers ce nœud. Il s’agit d’une solution temporaire. Une fois que vous avez installé le package, vous devez naviguer en arrière vers le site Web Datadog et rechercher « Tableaux de bord ». À partir de là, vous verrez personnalisé et l’intégration des tableaux de bord. Le tableau de bord Docker intégration auront toutes les mesures de conteneur que vous avez besoin pour la surveillance de votre cluster. 
