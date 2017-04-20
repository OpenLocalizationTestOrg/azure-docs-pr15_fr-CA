<properties
   pageTitle="Public et privé DC/OS Agent ACS de Pools | Microsoft Azure"
   description="Comment les pools publique et privée de l’agent fonctionnent avec un cluster Service de conteneur d’Azure."
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
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>Pools d’Agent DC/système d’exploitation pour le Service de conteneur Azure

Service de contrôleur de domaine/OS Azure conteneur divise les agents en pools publics ou privés. Un déploiement est possible à un pool, affectant d’accessibilité entre ordinateurs de votre service de conteneur. Les ordinateurs peuvent être exposés à internet (public) ou conservés (privée) interne. Cet article donne un bref aperçu des raisons pour lesquelles il existe un pool publique et privé.

### <a name="private-agents"></a>Agents privés

Les nœuds privée de l’agent s’exécuter via un réseau non routable. Ce réseau est accessible uniquement à partir de la zone admin ou par l’intermédiaire du routeur de bordure de zone publique. Par défaut, DC/système d’exploitation lance des applications sur les nœuds privée de l’agent. Pour plus d’informations sur la sécurité réseau, consultez la [documentation du contrôleur de domaine/système d’exploitation](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

### <a name="public-agents"></a>Agents publics

Nœuds d’agent public exécuter des services et des applications du contrôleur de domaine/système d’exploitation via un réseau accessible au public. Pour plus d’informations sur la sécurité réseau, consultez la [documentation du contrôleur de domaine/système d’exploitation](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

## <a name="using-agent-pools"></a>À l’aide de l’agent

Par défaut, **Marathon** déploie toute nouvelle application pour les nœuds *privée* de l’agent. Vous devez explicitement de déployer l’application vers le nœud *public* lors de la création de l’application. Sélectionnez l’onglet **facultatif** et entrez **slave_public** pour la valeur de **Rôles de ressource acceptés** . Ce processus est documenté [ici](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) et dans la documentation de [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) .

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [gestion de vos conteneurs DC/système d’exploitation](container-service-mesos-marathon-ui.md).

Découvrez comment [Ouvrir le pare-feu](container-service-enable-public-access.md) fourni par Azure pour autoriser l’accès du public à votre conteneur de contrôleur de domaine/système d’exploitation.