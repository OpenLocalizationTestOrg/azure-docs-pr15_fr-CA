<properties
   pageTitle="Déployer un conteneur ASP.NET noyau Linux Docker à un hôte distant de Docker | Microsoft Azure"
   description="Apprenez à utiliser Visual Studio Tools for Docker pour déployer une application web de base d’ASP.NET à un conteneur Docker en cours d’exécution sur un ordinateur virtuel de Azure Docker hôte Linux"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Déployer un conteneur ASP.NET à un hôte distant de Docker

## <a name="overview"></a>Vue d’ensemble
Docker est un moteur de conteneur léger, semblable à certains égards à une machine virtuelle, que vous pouvez utiliser pour héberger des applications et des services.
Ce didacticiel vous guide tout au long de l’utilisation de l’extension [Des outils de 2015 Visual Studio pour Docker](http://aka.ms/DockerToolsForVS) pour déployer une application de base de ASP.NET à un hôte de Docker sur Azure à l’aide de PowerShell.

## <a name="prerequisites"></a>Conditions préalables
Les éléments suivants sont nécessaires pour effectuer ce didacticiel :

- Créer un hôte de Docker Azure VM, comme décrit dans [l’utilisation de docker-machine avec Azure](./virtual-machines/virtual-machines-linux-docker-machine.md)
- Installez la [Mise à jour de Visual Studio 2015 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Kit de développement Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
- Installer les [Outils de 2015 Visual Studio pour Docker - aperçu](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1. création d’une application web de base d’ASP.NET
La procédure suivante vous guide dans la création d’une application ASP.NET Core base qui sera utilisée dans ce didacticiel.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Ajoutez la prise en charge de Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Utilisez le Script PowerShell DockerTask.ps1 

1.  Ouvrez une invite de PowerShell dans le répertoire racine de votre projet. 

    ```
    PS C:\Src\WebApplication1>
    ```

1.  Valider la télécommande hôte est en cours d’exécution. Vous devriez voir état = en cours d’exécution 

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] Si vous utilisez la version bêta de Docker, l’hôte ne sont pas répertorié ici.

1.  Générez l’application à l’aide de-paramètre de génération

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] Si vous utilisez la version bêta de Docker, omettez l’ordinateur argument -
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  Exécutez l’application, à l’aide de-paramètre d’exécution

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] Si vous utilisez la version bêta de Docker, omettez l’ordinateur argument -
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

    Une fois la docker terminée, vous devriez voir des résultats semblables aux suivants :

    ![Permet d’afficher votre application][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
