<properties
   pageTitle="Créer votre première application Fabric du Service sous Linux à l’aide de C# | Microsoft Azure"
   description="Créer et déployer une application de Service Fabric à l’aide de C#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>Créer votre première application Azure Fabric de Service

> [AZURE.SELECTOR]
- [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Service de Fabric fournit des kits de développement logiciel pour créer des services sur Linux dans l’infrastructure de .NET et Java. Dans ce didacticiel, nous examinons comment créer une application pour Linux et de créer un service à l’aide de C# (.NET de base).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, assurez-vous d’avoir [configuré votre environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement d’une case de Linux sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Création de l’application

Une application de Service Fabric peut contenir un ou plusieurs services, chacun ayant une fonction spécifique en matière de fonctionnalités de l’application. Le Service Fabric pour Linux comprend un générateur de [Yeoman](http://yeoman.io/) qui facilite la création de votre premier service et pour en ajouter davantage ultérieurement. Nous allons utiliser Yeoman pour créer une application avec un seul service.

1. Dans un terminal, tapez la commande suivante pour démarrer la création de la génération de modèles automatique :`yo azuresfcsharp`

2. Nom de votre application.

3. Choisissez le type de votre premier service et le nom. Pour les besoins de ce didacticiel, nous choisissons un acteur un Service fiable.

  ![Générateur de service Fabric Yeoman pour C#][sf-yeoman]

>[AZURE.NOTE] Pour plus d’informations sur les options, reportez-vous à la section [Présentation du modèle de programmation Service Fabric](service-fabric-choose-framework.md).

## <a name="build-the-application"></a>Générer l’application

Les modèles de Yeoman structure de Service incluent un script de génération que vous pouvez utiliser pour générer l’application à partir du terminal (après la navigation vers le dossier de l’application).

  ```bash
 cd myapp 
 ./build.sh 
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Démarrez le client de test et effectuer un basculement sur incident

Projets d’acteur ne font rien sur leurs propres. Ils nécessitent un autre service ou client d’envoyer les messages. Le modèle de l’acteur inclut un script de test simple que vous pouvez utiliser pour interagir avec le service de l’acteur.

1. Exécutez le script à l’aide de l’utilitaire de surveillance pour voir la sortie du service acteur.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Dans l’Explorateur de tissu de Service, recherchez le nœud qui héberge le réplica principal pour le service de l’acteur. Dans la capture d’écran ci-dessous, il est le nœud 3.

    ![Recherche le réplica principal dans l’Explorateur de tissu de Service][sfx-primary]

3. Cliquez sur le nœud que vous avez trouvé à l’étape précédente, puis sélectionnez **désactiver (redémarrage)** dans le menu Actions. Cette action redémarre un des cinq nœuds dans votre cluster local forcer un basculement sur incident à un réplica secondaire en cours d’exécution sur un autre nœud. Lorsque vous effectuez cette action, faites attention à la sortie de test client et notez que le compteur continue à incrémenter malgré le basculement.


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les acteurs fiable](service-fabric-reliable-actors-introduction.md)
- [Interaction avec les clusters de Service Fabric à l’aide de la CLI d’Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
