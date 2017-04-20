<properties
   pageTitle="Service de Fabric et des conteneurs de déploiement sous Linux | Microsoft Azure"
   description="TISSU de service et l’utilisation de conteneurs de Docker pour déployer des applications de microservice. Cet article décrit les fonctionnalités qui fournit de tissu de Service pour les conteneurs et comment déployer une image du conteneur Docker dans un cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>Déployer un conteneur Docker au Service de Fabric

> [AZURE.SELECTOR]
- [Déployer Windows conteneur](service-fabric-deploy-container.md)
- [Déployer le conteneur de Docker](service-fabric-deploy-container-linux.md)

Cet article vous guide dans le processus de création des services dans les conteneurs de Docker sous Linux.

Service de Fabric a plusieurs fonctionnalités de conteneur qui vous aident à créer des applications qui sont composées de microservices qui sont en conteneur. Il s’agit des services.

Les fonctionnalités incluent ;

- L’activation et le déploiement d’images de conteneur
- Gouvernance de ressources
- Authentification du référentiel
- Port du conteneur sur le port de l’hôte
- Découverte de conteneur à l’autre et de la communication
- Possibilité de configurer et de définir des variables d’environnement


## <a name="packaging-a-docker-container-with-yeoman"></a>Emballage d’un conteneur docker avec yeoman
Lors de la compression d’un conteneur sous Linux, vous pouvez choisir d’utiliser un modèle d’yeoman ou de [créer le package d’application manuellement](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container).

Une application de Service Fabric peut contenir un ou plusieurs conteneurs, chacun ayant une fonction spécifique en matière de fonctionnalités de l’application. Le Service Fabric pour Linux comprend un générateur de [Yeoman](http://yeoman.io/) qui facilite la création de votre application et ajouter une image de conteneur. Nous allons utiliser Yeoman pour créer une nouvelle application à un seul conteneur Docker appelé *SimpleContainerApp*. Vous pouvez ajouter que davantage de services ultérieurement en modifiant les fichiers manifeste.

## <a name="create-the-application"></a>Création de l’application

1. Dans un terminal, tapez **yo azuresfguest**.

2. Cliquez sur **conteneur** pour le framework.

3. Nom de votre application, par exemple SimpleContainerApp

4. Indiquez l’URL de l’image du conteneur à partir d’un emprunteuses de DockerHub. Elle prend la forme [emprunteuses] / [nom de l’image]

![Générateur Yeoman de tissu de service pour les conteneurs][sf-yeoman]

## <a name="deploy-the-application"></a>Déploiement de l’application

Une fois que l’application est générée, vous pouvez la déployer vers le cluster local à l’aide de la CLI d’Azure.

1. Connectez-vous au cluster de tissu de Service local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Utilisez le script d’installation fourni dans le modèle à copier le package d’application pour le magasin d’image du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    ./install.sh
    ```

3. Ouvrez un navigateur et accédez à l’Explorateur de tissu de Service à http://localhost:19080/Explorateur (remplacer localhost avec le IP privé de la machine virtuelle si vous utilisez Vagrant sous Mac OS X).

4. Développez le nœud Applications et notez qu’il existe maintenant une entrée pour votre type d’application et l’autre pour la première instance de ce type.

5. Utilisez le script de désinstallation fourni dans le modèle à supprimer de l’instance d’application et le type d’application d’annuler l’inscription.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du Service de Fabric et des conteneurs](service-fabric-containers-overview.md)
- [Interaction avec les clusters de Service Fabric à l’aide de la CLI d’Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

