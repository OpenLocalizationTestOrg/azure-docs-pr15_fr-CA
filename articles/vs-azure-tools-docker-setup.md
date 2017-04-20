<properties
   pageTitle="Configurer un hôte de Docker avec VirtualBox | Microsoft Azure"
   description="Instructions pas à pas pour configurer une instance de Docker par défaut à l’aide de Machine de Docker et de VirtualBox"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>Configurer un hôte de Docker avec VirtualBox

## <a name="overview"></a>Vue d’ensemble
Cet article vous guide dans le processus de configuration d’une instance de Docker par défaut à l’aide de Machine de Docker et VirtualBox. Si vous utilisez la [version bêta de Docker pour Windows](http://beta.docker.com/), cette configuration n’est pas nécessaire.

## <a name="prerequisites"></a>Conditions préalables
Les outils suivants doivent être installés.

- [Boîte à outils de docker](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configurer le client de Docker avec Windows PowerShell

Pour configurer un client Docker, ouvrir Windows PowerShell et effectuez les opérations suivantes :

1. Créez une instance de l’hôte docker par défaut.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Vérifiez que l’instance par défaut est configuré et en cours d’exécution. (Vous devriez voir une instance nommée 'default' en cours d’exécution.

    ```PowerShell
    docker-machine ls 
    ```
        
    ![sortie de docker-machine ls][0]
 
1. La valeur par défaut comme l’hôte actuel et configurer votre shell.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Afficher les conteneurs active Docker. La liste doit être vide.

    ```PowerShell
    docker ps
    ```

    ![sortie de ps docker][1]
 
> [AZURE.NOTE]Chaque fois que vous redémarrez votre ordinateur de développement, vous devrez redémarrer votre hôte local docker.
> Pour ce faire, exécutez la commande suivante à une invite de commande : `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
