<properties
   pageTitle="Créer des hôtes de Docker dans Azure avec Docker Machine | Microsoft Azure"
   description="Décrit l’utilisation de la Machine Docker pour créer les hôtes docker dans Azure."
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

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Créer les Docker hôtes dans Azure avec Docker-ordinateur

Conteneurs de [Docker](https://www.docker.com/) en cours d’exécution requiert un machine virtuelle exécutant le démon docker de l’hôte.
Cette rubrique décrit comment utiliser la commande [docker-machine](https://docs.docker.com/machine/) pour créer de nouveaux ordinateurs virtuels Linux, configuré avec le démon Docker, en cours d’exécution dans Azure. 

**Remarque :** 
- *Cet article dépend de la machine docker version 0.7.0 ou supérieure*
- *Conteneurs de Windows seront pris en charge par le biais de docker-machine dans un avenir proche*

## <a name="create-vms-with-docker-machine"></a>Créer des ordinateurs virtuels avec l’ordinateur de Docker

Créer des ordinateurs virtuels d’hôte docker dans Azure avec la `docker-machine create` de commande à l’aide de la `azure` pilote. 

Le pilote Azure aurez besoin de votre ID d’abonnement. Vous pouvez utiliser l' [Infrastructure du langage commun Azure](xplat-cli-install.md) ou le [Portail Azure](https://portal.azure.com) pour récupérer votre abonnement Azure. 

**L’utilisation du portail Azure**
- Sélectionner des abonnements à partir de la page de navigation gauche et copiez à l’id de l’abonnement.

**À l’aide de l’interface CLI Azure**
- Type de ```azure account list``` et copiez l’id de l’abonnement.

Type de `docker-machine create --driver azure` pour afficher les options et leurs valeurs par défaut.
Vous pouvez également voir la [documentation du pilote d’Azure Docker](https://docs.docker.com/machine/drivers/azure/) pour plus d’informations. 

L’exemple suivant s’appuie sur les valeurs par défaut, mais il éventuellement, ouvrir le port 80 sur l’ordinateur virtuel pour l’accès internet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Choisir un hôte docker avec docker-ordinateur
Une fois une entrée en docker-machine pour votre hôte, vous pouvez définir l’hôte par défaut lors de l’exécution des commandes de docker.
##<a name="using-powershell"></a>Utilisation de PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>À l’aide de Bash

```bash
eval $(docker-machine env MyDockerHost)
```

Vous pouvez maintenant exécuter des commandes de docker par rapport à l’hôte spécifié

```
docker ps
docker info
```

## <a name="run-a-container"></a>Exécuter un conteneur

Avec un hôte configuré, vous pouvez maintenant exécuter un simple serveur web pour vérifier si votre hôte a été configuré correctement.
Ici nous allons utiliser une image standard nginx, spécifiez qu’il doit écouter sur le port 80, et que si le redémarrage de l’ordinateur virtuel de l’hôte, le conteneur de redémarrent également (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

La sortie doit ressembler à ce qui suit :

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Le conteneur de test

Examiner les conteneurs en cours d’exécution à l’aide de `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Pour vérifier le conteneur en cours d’exécution, tapez `docker-machine ip <VM name>` pour trouver l’adresse IP à entrer dans le navigateur :

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Conteneur de ngnix en cours d’exécution](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>Résumé
Avec docker-machine, vous pouvez facilement prévoir hôtes docker dans Azure votre docker de chaque hôte de validations.
Pour l’hébergement de la production de conteneurs, consultez le [Service de conteneur Azure](http://aka.ms/AzureContainerService)

Pour développer des Applications de base de .NET avec Visual Studio, consultez [Les outils de Docker pour Visual Studio](http://aka.ms/DockerToolsForVS)
