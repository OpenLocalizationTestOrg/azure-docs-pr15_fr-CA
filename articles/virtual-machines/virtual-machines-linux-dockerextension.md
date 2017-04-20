<properties
   pageTitle="À l’aide de l’extension Azure VM de Docker | Microsoft Azure"
   description="Apprenez à utiliser l’extension Docker VM pour déployer rapidement et en toute sécurité un environnement Docker dans Azure à l’aide des modèles du Gestionnaire de ressources."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Créer un environnement de Docker dans Azure à l’aide de l’extension de la machine virtuelle de Docker
Docker est une plate-forme d’imagerie qui vous permet de travailler rapidement avec des conteneurs sous Linux (et ainsi de Windows) et une gestion des conteneurs les plus courants. Dans Azure, il existe différentes méthodes que vous pouvez déployer des Docker en fonction de vos besoins. Cet article se concentre sur l’utilisation de l’extension de la machine virtuelle de Docker et modèles du Gestionnaire de ressources Azure. 

Pour plus d’informations sur les différentes méthodes de déploiement, y compris l’utilisation de la Machine de Docker et Azure conteneur Services, consultez les articles suivants :

- Prototype rapidement une application, vous pouvez créer un seul hôte de Docker à l’aide de [Machine de Docker](./virtual-machines-linux-docker-machine.md).
- Pour les environnements plus grands et plus stables, vous pouvez utiliser l’extension Azure VM de Docker, qui prend également en charge le [Docker composer](https://docs.docker.com/compose/overview/) pour générer des déploiements de conteneur cohérente. Cet article explique à l’aide de l’extension Azure VM de Docker.
- Pour générer un prêt pour la production d’environnements évolutifs qui fournissent des outils de planification et de gestion supplémentaires, vous pouvez déployer un [cluster de Docker par essaim sur Azure conteneur de Services](../container-service/container-service-deployment.md).


## <a name="azure-docker-vm-extension-overview"></a>Vue d’ensemble des extension Azure VM de Docker
L’extension de Azure VM de Docker installe et configure le démon de Docker, le client de Docker et Docker composent dans votre machine virtuelle de Linux (VM). À l’aide de l’extension Azure Docker VM, vous avez plus de contrôle et de fonctionnalités que simplement à l’aide de Machine de Docker ou de création de l’hôte Docker. Ces fonctionnalités supplémentaires, telles que la [Composition de Docker](https://docs.docker.com/compose/overview/), vérifiez l’extension Azure Docker VM adaptée aux développeurs plus robuste ou les environnements de production.

Les modèles de gestionnaire de ressources Azure définissent la structure complète de votre environnement. Les modèles vous permettent de créer et de configurer les ressources de l’hôte Docker VMs, stockage, contrôles d’accès basés sur les rôles (RBAC) et diagnostics. Vous pouvez réutiliser ces modèles pour créer des déploiements supplémentaires de manière cohérente. Pour plus d’informations sur les modèles et le Gestionnaire de ressources Azure, consultez [vue d’ensemble du Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md). 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Déployer un modèle avec l’extension Azure VM de Docker
Nous allons utiliser un modèle existant de démarrage rapide pour créer une VM Ubuntu qui utilise l’extension Azure Docker VM pour installer et configurer l’hôte Docker. Vous pouvez afficher le modèle ici : [déploiement Simple d’une machine virtuelle d’Ubuntu avec Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Vous devez la [Dernière Azure CLI](../xplat-cli-install.md) installé et connecté en utilisant le mode du Gestionnaire de ressources comme suit :

```
azure config mode arm
```

Déployer le modèle à l’aide de la CLI d’Azure, en spécifiant le modèle URI. L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `WestUS` emplacement. Utiliser votre propre nom de groupe de ressources et l’emplacement comme suit :

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Répondez aux invites pour le nom de votre compte de stockage, fournir un nom d’utilisateur et le mot de passe et fournissez un nom DNS. La sortie est similaire à l’exemple suivant :

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

Retourne la CLI d’Azure vous à l’invite après quelques secondes, mais votre hôte Docker est toujours créées et configurées par l’extension Azure VM de Docker. Il faut quelques minutes pour le déploiement à la fin. Vous pouvez afficher des détails sur l’état hôte Docker à l’aide du `azure vm show` commande.

L’exemple suivant vérifie l’état de la machine virtuelle nommée `myDockerVM` (le nom par défaut à partir du modèle - ne modifiez pas ce nom) dans le groupe de ressources nommé `myResourceGroup`. Entrez le nom du groupe de ressources que vous avez créé à l’étape précédente :

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

La sortie de la `azure vm show` commande est similaire à l’exemple suivant :

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

En haut de la sortie, vous consultez la `ProvisioningState` de la machine virtuelle. Lorsque cette option affiche `Succeeded`, le déploiement est terminé et vous pouvez SSH à la machine virtuelle.

Vers la fin de la sortie, `FQDN` affiche le nom de domaine pleinement qualifié de votre hôte de Docker. Ce nom de domaine complet est ce qui vous permet de SSH à votre hôte Docker dans les étapes restantes.


## <a name="deploy-your-first-nginx-container"></a>Déploiement de votre premier conteneur nginx
Une fois le déploiement terminé, SSH vers votre nouvel hôte Docker à partir de votre ordinateur local. Entrez votre propre nom d’utilisateur et le nom de domaine complet comme suit :

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Une fois connecté à l’hôte Docker, exécutons un conteneur nginx :

```
sudo docker run -d -p 80:80 nginx
```

La sortie est similaire à l’exemple suivant comme l’image nginx est téléchargée et un conteneur de démarrage :

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Vérifier l’état des conteneurs exécutant sur votre hôte Docker comme suit :

```
sudo docker ps
```

La sortie est similaire à l’exemple suivant, montrant que le conteneur nginx est en cours d’exécution et les ports TCP 80 et 443 et transféré :

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Pour voir votre conteneur en action, ouvrez un navigateur web et entrez le nom de domaine complet de votre hôte Docker :

![Conteneur de ngnix en cours d’exécution](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Référence de modèle Azure VM de Docker extension
L’exemple précédent utilise un modèle existant de démarrage rapide. Vous pouvez également déployer l’extension Azure VM de Docker avec vos propres modèles de gestionnaire de ressources. Pour ce faire, ajoutez le code suivant vos modèles de gestionnaire de ressources, en définissant le `vmName` de votre machine virtuelle appropriée :

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Vous trouverez la procédure pas à pas plus détaillées sur l’utilisation des modèles du Gestionnaire de ressources en lisant la [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).


## <a name="next-steps"></a>Étapes suivantes
Vous souhaiterez peut-être [configurer le démon Docker port TCP](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), comprendre la [sécurité de Docker](https://docs.docker.com/engine/security/security/), ou déployer des conteneurs à l’aide de [Composer de Docker](https://docs.docker.com/compose/overview/). Pour plus d’informations sur l’Extension de machine virtuelle Docker Azure lui-même, consultez le [projet de GitHub](https://github.com/Azure/azure-docker-extension/).

En savoir plus sur les options de déploiement supplémentaires Docker dans Azure :

- [Utiliser des Docker Machine avec le pilote Azure](./virtual-machines-linux-docker-machine.md)  
- [Mise en route de Docker et composition pour définir et exécuter une application conteneur multiples sur une machine virtuelle Azure](virtual-machines-linux-docker-compose-quickstart.md).
- [Déployer un cluster Service de conteneur Azure](../container-service/container-service-deployment.md)
