<properties
   pageTitle="Configurer votre environnement de développement sous Linux | Microsoft Azure"
   description="Installez le runtime et le Kit de développement logiciel et de créer un cluster de développement local sur Linux. Après avoir terminé cette installation, vous serez prêt à créer des applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>Préparez votre environnement de développement sous Linux


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Pour déployer et exécuter des [applications Azure Service Fabric](service-fabric-application-model.md) sur votre machine de développement Linux, installer le runtime et le Kit de développement commun. Vous pouvez également installer facultatif SDK pour Java et .NET Core.

## <a name="prerequisites"></a>Conditions préalables
### <a name="supported-operating-system-versions"></a>Versions des systèmes d’exploitation pris en charge
Les versions de système d’exploitation suivants sont pris en charge pour le développement :

- Ubuntu 16.04 (Xerus Xenial)

## <a name="update-your-apt-sources"></a>Mise à jour de vos sources apt

Pour installer le SDK et le package de runtime associé via apt-get, vous devez d’abord mettre à jour vos sources apt.

1. Ouvrez un terminal.
2. Ajouter le mis en pension Fabric du Service à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Ajouter la nouvelle clé GPG à votre porte-clés apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Actualiser vos listes de package basés sur les référentiels nouvellement ajoutés.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Installez et configurez le Kit de développement

Une fois vos sources sont mis à jour, vous pouvez installer le Kit de développement.

1. Installez le package de Service Fabric SDK. Vous serez invité à confirmer l’installation et à accepter un contrat de licence.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Exécutez le script de configuration du Kit de développement logiciel.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>Paramétrer la CLI Azure multiplates-formes

[Azure multiplates - CLI] [ azure-xplat-cli-github] inclut des commandes pour interagir avec les entités de Service Fabric, y compris les clusters et les applications. Il est basé sur Node.js donc [vous assurer que vous avez installé le nœud] [ install-node] avant d’exécuter les instructions ci-dessous.

1. Cloner le mis en pension github à votre ordinateur de développement.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Basculez dans le mis en pension cloné et installer les dépendances de l’infrastructure du langage commun en utilisant le Gestionnaire de package de nœud (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Créer un lien symbolique à partir du dossier bin/azure le mis en pension cloné à /usr/bin/azure afin qu’il est ajouté à votre chemin d’accès et commandes sont disponibles à partir de n’importe quel répertoire.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Enfin, activer les commandes de Service Fabric de la saisie semi-automatique.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Configurer un cluster local

Si tout a installé avec succès, vous devez être en mesure de démarrer un cluster local.

1. Exécutez le script de configuration du cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Ouvrez un navigateur web et accédez à http://localhost:19080/Explorer. Si le cluster a démarré, vous devez voir le tableau de bord de Service Fabric Explorer.

    ![Service Explorateur de Fabric sur Linux][sfx-linux]

À ce stade, vous êtes en mesure de déployer des packages d’applications Fabric de Service prédéfinis ou de nouveaux basés sur les conteneurs de l’invité ou fichiers exécutables d’invité. Pour créer de nouveaux services en utilisant Java ou .NET Core SDK, suivez les étapes d’installation facultatifs ci-dessous.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Installer le Kit de développement logiciel Java et Eclipse néon plug-in (en option)

Le Kit de développement Java fournit les bibliothèques et les modèles nécessaires à la création de services de Fabric de Service à l’aide de Java.

1. Installez le package Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Exécutez le script de configuration du Kit de développement logiciel.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Vous pouvez installer le plug-in Eclipse pour tissu de Service à partir de l’IDE de néon Eclipse.

1. Dans Eclipse, assurez-vous que vous avez Buildship version 1.0.17 ou version ultérieure. Vous pouvez vérifier les versions des composants installés en choisissant **Aide > Détails de l’Installation**. Vous pouvez mettre à jour en suivant les instructions de Buildship [ici][buildship-update].

2. Pour installer le plug-in de Fabric du Service, choisissez **Aide > installer un nouveau logiciel...**

3. Dans la zone de texte « Utiliser », entrez : http://dl.windowsazure.com/eclipse/servicefabric

4. Cliquez sur Ajouter.

    ![Plug-in d’Eclipse][sf-eclipse-plugin]

5. Cliquez sur le plug-in de Fabric du Service, puis cliquez sur Suivant.

6. Procédez à l’installation et acceptez le contrat de licence utilisateur final.

## <a name="install-the-net-core-sdk-optional"></a>Installer le Kit de développement .NET Core (facultatif)

Le Kit de développement de base de .NET fournit les bibliothèques et les modèles nécessaires pour créer des services de Fabric de Service à l’aide d’inter-plateformes .NET Core.

1. Installez le package de base de développement .NET SDK.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Exécutez le script de configuration du Kit de développement logiciel.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre première application Java sous Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Préparez votre environnement de développement sur OSX](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
