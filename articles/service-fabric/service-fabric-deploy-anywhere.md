<properties
   pageTitle="Créer des clusters d’Azure Fabric de Service sur Windows Server et Linux | Microsoft Azure"
   description="Clusters de tissu de service s’exécutés sur Windows Server et Linux, qui signifie que vous pourrez déployer et les applications de Fabric du Service hôte n’importe où vous pouvez exécuter le serveur Windows ou Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Créer des clusters de Fabric de Service sur un serveur Windows ou Linux

Azure Fabric de Service permet la création de clusters de tissu de Service sur des ordinateurs virtuels ou des ordinateurs exécutant le serveur Windows ou Linux. Cela signifie que vous pouvez déployer et exécuter des applications de Service Fabric dans n’importe quel environnement où vous disposez d’un ensemble d’ordinateurs serveur Windows ou Linux qui sont interconnectés, que ce soit en local, Microsoft Azure ou avec n’importe quel fournisseur de nuage.

##<a name="create-service-fabric-clusters-on-azure"></a>Créer des clusters de Service Fabric sur Azure

Création d’un cluster sur Azure s’effectue via un modèle de ressource ou le portail Azure. Pour plus d’informations, consultez [créer un Fabric du Service de cluster à l’aide d’un modèle de gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md) ou [créer un cluster à structure de Service à partir du portail Azure](service-fabric-cluster-creation-via-portal.md) .

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Prise en charge des systèmes d’exploitation pour les clusters dans Azure

Vous ne pouvez créer des clusters de machines virtuelles exécutant ces systèmes d’exploitation :

* Windows Server 2012 R2
* Windows Server 2016 (une fois qu’il est annoncé comme disponible)
* 16.04 d’Ubuntu Linux (en version d’évaluation) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>Créer des clusters d’autonome de tissu de Service sur site ou avec n’importe quel fournisseur de nuage

Service Fabric offre un package d’installation de créer autonome Service Fabric clusters locaux ou sur n’importe quel fournisseur de nuage

Pour plus d’informations sur la configuration des clusters de tissu de service autonome sur Windows Server, lire le [Service Fabric création d’un cluster Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Les déploiements de nuage et les déploiements sur site
Le processus de création d’un cluster à structure de Service sur site est similaire au processus de création d’un cluster sur n’importe quel nuage de votre choix, avec un ensemble de machines virtuelles. Les étapes initiales de mise en service les ordinateurs virtuels sont régies par le fournisseur de nuage ou un environnement local que vous utilisez. Une fois que vous avez un ensemble d’ordinateurs virtuels avec la connectivité réseau entre eux, puis les étapes pour configurer le package de Service Fabric, modifier les paramètres de cluster et exécuter la création d’un cluster et des scripts de gestion sont identiques. Cela garantit que vos connaissances et l’expérience de fonctionnement et de gestion des clusters de Service Fabric est transférable lorsque vous choisissez de cibler les nouveaux environnements d’hébergement.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Avantages de la création de clusters de tissu de Service autonome
* Vous êtes libre de choisir n’importe quel fournisseur de nuage pour héberger votre cluster.
* Applications de service Fabric, écrites en une seule fois, peuvent être exécutées dans plusieurs environnements d’hébergement avec minimales à aucune modification.
* Connaissance de la construction d’applications Fabric de Service porte un environnement d’hébergement à un autre.
* Expérience opérationnelle d’exécution et de gestion de Service Fabric clusters exécute sur à partir d’un environnement à un autre.
* Large clientèle est illimitée par les contraintes de l’environnement d’hébergement.
* Il existe une couche supplémentaire de la fiabilité et la protection contre les pannes de grande envergure car vous pouvez déplacer les services sur un autre environnement de déploiement si un fournisseur de nuage ou de centre de données a une panne d’électricité.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Prise en charge des systèmes d’exploitation pour les clusters d’autonome
Vous ne pouvez créer des clusters de machines virtuelles ou des ordinateurs exécutant ces systèmes d’exploitation :

* Windows Server 2012 R2
* Windows Server 2016 (une fois qu’il est annoncé comme disponible)
* Linux (disponible prochainement)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Avantages de Service Fabric sur Azure par rapport aux clusters autonome que fabric du Service de clusters créé sur site

Clusters de tissu de Service en cours d’exécution sur Azure fournit l’option avantages sur site, si vous n’avez pas besoins spécifiques lors de l’exécution de vos clusters, puis nous suggérons que vous les exécutez sur Azure. Sur Azure, nous fournir une intégration avec d’autres fonctionnalités Azure et les services, ce qui rend les opérations et la gestion du cluster plus facile et plus fiable.

* **Azure portal :** Azure portal rend facile de créer et de gérer les clusters.

* **Le Gestionnaire de Ressources azure :** Utilisation du Gestionnaire de ressources Azure permet de faciliter la gestion de toutes les ressources utilisées par le cluster en tant qu’unité et simplifie le suivi du coût et facturation.
* **Service de Cluster comme une ressource d’Azure Fabric** Un cluster de Service Fabric étant une ressource ARM, vous pouvez le modèle comme vous le faites autres ressources ARM dans Azure.
* **Intégration avec l’Infrastructure Azure** Coordonnées du service Fabric avec l’infrastructure sous-jacente Azure pour le système d’exploitation réseau et autres mises à niveau afin d’améliorer la disponibilité et la fiabilité de vos applications.  
* **Diagnostics :** Sur Azure, nous fournir une intégration avec les diagnostics de Windows Azure et Analytique du journal.
* **-Mise à l’échelle :** Pour les clusters sur Azure, nous fournir des fonctionnalités intégrées de mise à l’échelle en raison de l’échelle des ensembles de Machine virtuelle. Dans les locaux et les autres environnements de nuage, vous devez créer votre propre fonction de mise à l’échelle ou l’échelle manuellement à l’aide de l’API qui expose de tissu de Service pour la mise à l’échelle des clusters.

## <a name="next-steps"></a>Étapes suivantes
Créer un cluster sur des ordinateurs virtuels ou des ordinateurs qui exécutent Windows Server : [Création d’un cluster Service de Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Créer un cluster sur des ordinateurs virtuels ou d’ordinateurs exécutant Linux : [TISSU Service sous Linux](service-fabric-linux-overview.md)
