<properties
   pageTitle="Configurer votre environnement de développement sur Mac OS X | Microsoft Azure"
   description="Installer les outils, le Kit de développement logiciel et le runtime et créer un cluster de développement local. Après avoir terminé cette installation, vous serez prêt à créer des applications sous Mac OS X."
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurer votre environnement de développement sur Mac OS X

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Vous pouvez créer des applications de tissu de Service à exécuter sur les clusters Linux à l’aide de Mac OS X. Cet article explique comment configurer votre Mac pour le développement.

## <a name="prerequisites"></a>Conditions préalables

TISSU de service ne s’exécute pas en mode natif sur OS X. Pour exécuter un cluster Service Fabric local, nous fournissons une VM Ubuntu préconfiguré à l’aide de Vagrant et VirtualBox. Avant de commencer, vous devez :

- [Vagrant (v1.8.4 ou version ultérieure)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Créer la VM locale

Pour créer la VM locale contenant un cluster à 5 nœuds Service Fabric, effectuez les opérations suivantes :

1. Cloner le mis en pension Vagrantfile

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Naviguez vers le clone local de la mis en pension

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Facultatif) Modifier les paramètres de mémoire virtuelle par défaut

    Par défaut, la machine virtuelle locale est configurée comme suit :

    - 3 Go de mémoire allouée
    - Réseau privé hôte configuré IP 192.168.50.50 activer le relais du trafic de l’hôte de Mac

    Vous pouvez modifier un de ces paramètres ou ajouter une autre configuration de la machine virtuelle dans la Vagrantfile. Consultez la [documentation de Vagrant](http://www.vagrantup.com/docs) pour la liste complète des options de configuration.

4. Créer la machine virtuelle

    ```bash
    vagrant up
    ```

    Cette étape télécharge l’image de machine virtuelle préconfiguré, démarrage localement, puis paramétrez un tissu local de Service de cluster qu’il. Vous devez vous attendre à prendre quelques minutes. Si le programme d’installation se termine correctement, vous verrez un message dans la sortie indiquant que le cluster démarre.

    ![Le programme d’installation démarrage suivant la mise en service de la machine virtuelle du cluster][cluster-setup-script]

5. Vérifiez si le cluster a été correctement configuré en accédant à l’Explorateur de tissu de Service à http://192.168.50.50:19080/Explorateur (en supposant que vous avez conservé l’adresse IP du réseau privé par défaut).

    ![Service de Fabric Explorateur visualisée à partir de l’hôte Mac][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Installer le plug-in Service Fabric pour néon d’Eclipse (facultatif)

TISSU de service fournit un plug-in pour l’IDE Eclipse néon qui peuvent simplifier le processus de création et de déploiement des services de Java.

1. Dans Eclipse, assurez-vous que vous avez Buildship version 1.0.17 ou version ultérieure. Vous pouvez vérifier les versions des composants installés en choisissant **Aide > Détails de l’Installation**. Vous pouvez mettre à jour en suivant les instructions de Buildship [ici][buildship-update].

2. Pour installer le plug-in de Fabric du Service, choisissez **Aide > installer un nouveau logiciel...**

3. Dans la zone de texte « Utiliser », entrez : http://dl.windowsazure.com/eclipse/servicefabric.

4. Cliquez sur Ajouter.

    ![Plug-in de néon Eclipse pour tissu de Service][sf-eclipse-plugin-install]

5. Cliquez sur le plug-in de Fabric du Service, puis cliquez sur Suivant.

6. Procédez à l’installation et acceptez le contrat de licence utilisateur final.

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre première application Service Fabric pour Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Créer un cluster de Service Fabric dans le portail Azure](service-fabric-cluster-creation-via-portal.md)
- [Créer un cluster de Service Fabric à l’aide du Gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md)
- [Comprendre le modèle d’application Fabric du Service](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
