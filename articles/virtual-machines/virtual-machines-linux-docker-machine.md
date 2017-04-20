<properties
    pageTitle="Créer des hôtes de Docker dans Azure avec Docker Machine | Microsoft Azure"
    description="Décrit l’utilisation de la Machine Docker pour créer les hôtes docker dans Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="rasquill"/>

# <a name="use-docker-machine-with-the-azure-driver"></a>Utiliser des Docker Machine avec le pilote Azure

[Docker](https://www.docker.com/) est une des approches de virtualisation les plus populaires qui utilise des conteneurs de Linux plutôt que des ordinateurs virtuels comme un moyen d’isoler les données de l’application et de l’informatique sur les ressources partagées. Cette rubrique explique quand et comment utiliser [Docker Machine](https://docs.docker.com/machine/) (le `docker-machine` commande) pour créer de nouvelles machines virtuelles de Linux dans Azure activé comme un hôte de docker pour vos conteneurs de Linux.


## <a name="create-vms-with-docker-machine"></a>Créer des ordinateurs virtuels avec l’ordinateur de Docker

Créer des ordinateurs virtuels d’hôte docker dans Azure avec le `docker-machine create` de commande à l’aide de la `azure` argument pilote pour l’option de pilote (`-d`) et tous les autres arguments. 

L’exemple suivant s’appuie sur les valeurs par défaut, mais elle s’ouvre le port 80 sur l’ordinateur virtuel à l’internet pour tester avec un conteneur nginx, rend `ops` l’utilisateur d’ouverture de session SSH et des appels de la nouvelle machine virtuelle `machine`. 

Type de `docker-machine create --driver azure` pour afficher les options et leurs valeurs par défaut ; Vous pouvez également lire la [documentation du pilote d’Azure Docker](https://docs.docker.com/machine/drivers/azure/). (Notez que si vous avez activée l’authentification à deux facteurs, vous êtes invité à s’authentifier en utilisant le second facteur).

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

La sortie doit ressembler à ceci, selon que vous avez configurée dans votre compte d’authentification à deux facteurs.

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Configurer votre shell docker

Maintenant, tapez `docker-machine env <VM name>` pour voir ce que vous devez faire pour configurer le shell. 

```bash
docker-machine env machine
```

Qui imprime les informations sur l’environnement, qui se présente comme suit. Notez que l’adresse IP a été attribuée, dont vous avez besoin pour test de la machine virtuelle.

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Vous pouvez soit exécuter la commande de configuration suggérée, ou vous pouvez définir les variables d’environnement vous-même. 

## <a name="run-a-container"></a>Exécuter un conteneur

Maintenant vous pouvez exécuter un serveur web simple pour tester si tout fonctionne correctement. Ici nous allons utiliser une image standard nginx, spécifiez qu’il doit écouter sur le port 80, et que si l’ordinateur virtuel redémarre le conteneur doit redémarrent également (`--restart=always`). 

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

Pour vérifier le conteneur en cours d’exécution, tapez `docker-machine ip <VM name>` pour trouver l’adresse IP (si vous avez oublié du `env` commande) :

![Conteneur de ngnix en cours d’exécution](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes intéressé, vous pouvez essayer l' Azure [Extension de machine virtuelle Docker](virtual-machines-linux-dockerextension.md) pour effectuer la même opération à l’aide de la CLI Azure ou Gestionnaire de modèles de ressources Azure. 

Pour plus d’exemples de l’utilisation de Docker, consultez [utilisation de Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) à partir de la connexion de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour plus des Démarrages rapides à partir de la démo de HealthClinic.biz, consultez les [Démarrages rapides outils de développeur de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

