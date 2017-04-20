<properties
    pageTitle="À l’aide de l’Extension de la machine virtuelle Docker pour Linux sur Azure"
    description="Décrit les Docker et les extensions d’ordinateurs virtuels Azure et montre comment créer par programme des Machines virtuelles sur Azure qui sont des hôtes de docker à partir de la ligne de commande à l’aide de la CLI d’Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>À l’aide de l’Extension de la machine virtuelle Docker à partir de la ligne de commande d’Azure Interface (CLI Azure)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



Cette rubrique décrit comment créer un ordinateur virtuel avec l’Extension de la machine virtuelle Docker à partir du mode de gestion (asm) service dans Azure CLI sur n’importe quelle plate-forme. [Docker](https://www.docker.com/) est une des approches de virtualisation les plus populaires qui utilise des [conteneurs de Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des ordinateurs virtuels de l’isolation des données et de l’informatique sur les ressources partagées. Vous pouvez utiliser l’extension de la machine virtuelle de Docker et de l' [Agent de Linux Azure](virtual-machines-linux-agent-user-guide.md) pour créer une machine virtuelle Docker qui héberge un nombre quelconque de conteneurs pour vos applications sur Azure. Pour obtenir une description détaillée des conteneurs et leurs avantages, consultez le [Tableau blanc de niveau élevé Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>Comment faire pour utiliser l’Extension de la machine virtuelle Docker avec Azure
Pour utiliser l’extension Docker VM avec Azure, vous devez installer une version de l' [Interface de ligne de commande Azure](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) supérieur à 0.8.6 (comme de la rédaction, la version actuelle est 0.10.0). Vous pouvez installer l’interface CLI d’Azure sous Windows, Linux et Mac.


Le processus complet d’utiliser Docker sur Azure est simple :

+ Installer l’interface CLI d’Azure et ses dépendances sur l’ordinateur à partir duquel vous souhaitez contrôler Azure (sous Windows, il s’agit une distribution Linux fonctionne comme une machine virtuelle)
+ Utilisez les commandes Azure CLI Docker pour créer un hôte de la machine virtuelle Docker dans Azure
+ Les commandes Docker locales permet de gérer vos conteneurs Docker dans votre ordinateur virtuel de Docker dans Azure.


### <a name="install-the-azure-command-line-interface-azure-cli"></a>Installer l’Azure l’Interface de ligne de commande (CLI Azure)

Pour installer et configurer l’infrastructure du langage commun Azure, consultez [comment installer l’Interface de ligne de commande Azure](../xplat-cli-install.md). Pour confirmer l’installation, tapez `azure` à l’invite de commande et après un court moment que vous devez voir l’art Azure CLI ASCII, qui répertorie les commandes de base à votre disposition. Si l’installation a fonctionné correctement, vous devez être en mesure de taper `azure help vm` et qu’une de la liste de commandes est « docker ».

> [AZURE.NOTE] Docker propose des outils pour Windows, [Docker Machine](https://docs.docker.com/installation/windows/), ce qui vous permet également d’automatiser la création d’un client de docker que vous pouvez utiliser pour travailler avec des ordinateurs virtuels d’Azure en tant qu’hôtes de docker.

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Connecter le CLI Azure pour votre compte Azure
Avant de pouvoir utiliser la CLI d’Azure, vous devez associer vos informations d’identification de compte Azure avec la CLI d’Azure sur votre plate-forme. La section [comment se connecter à votre abonnement Azure](../xplat-cli-connect.md) explique comment télécharger et importer votre fichier **.publishsettings** ou associer votre CLI Azure avec un id d’organisation.

> [AZURE.NOTE] Il existe quelques différences de comportement lors de l’utilisation d’un ou autres méthodes d’authentification, afin d’assurer lire le document ci-dessus pour comprendre les différentes fonctionnalités.

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Installation de Docker et l’utilisation de l’Extension de la machine virtuelle Docker pour Azure
Suivez les [instructions d’installation de Docker](https://docs.docker.com/installation/#installation) pour installer le Docker localement sur votre ordinateur.

Pour utiliser Docker avec une Machine virtuelle de Azure, l’image de Linux utilisée pour la machine virtuelle doit avoir installé l' [Agent de machine virtuelle Linux Azure](virtual-machines-linux-agent-user-guide.md) . Actuellement, il n’y a que deux types d’images que fournissent :

+ Une image Ubuntu à partir de la galerie d’Image Azure ou

+ Une image de Linux personnalisée que vous avez créées avec l’Agent de machine virtuelle Linux Azure installé et configuré. Pour plus d’informations sur la façon de créer une VM Linux personnalisé avec l’Agent de la machine virtuelle Azure, reportez-vous à la section [Agent de machine virtuelle Linux Azure](virtual-machines-linux-agent-user-guide.md) .

### <a name="using-the-azure-image-gallery"></a>À l’aide de la galerie d’Image Azure

À partir d’un Bash ou une session Terminal Server, utilisez la commande CLI d’Azure suivante pour localiser l’image Ubuntu plus récente dans la bibliothèque virtuelle à utiliser en tapant

`azure vm image list | grep Ubuntu-14_04`

et sélectionnez un des noms d’images, tel que `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`et utilisez la commande suivante pour créer une nouvelle machine virtuelle à l’aide de cette image.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

où :

+ * &lt;nom de machine virtuelle-cloudservice&gt; * est le nom de la machine virtuelle qui va devenir l’ordinateur hôte du conteneur Docker dans Azure

+  * &lt;nom d’utilisateur&gt; * est le nom de l’utilisateur racine par défaut de la machine virtuelle

+ * &lt;mot de passe&gt; * est le mot de passe du compte *utilisateur* répondant aux normes de complexité pour Azure

> [AZURE.NOTE] Actuellement, un mot de passe doit comporter au moins 8 caractères, contenir une minuscule et une majuscule, un nombre et un caractère spécial tel qu’un des caractères suivants : `!@#$%^&+=`. Non, la période à la fin de la phrase précédente n’est pas un caractère spécial.

Si la commande a réussi, vous devez voir quelque chose comme suit, en fonction des arguments précis et options que vous avez utilisé :

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] Création d’une machine virtuelle peut prendre quelques minutes, mais après qu’il a été mis en service (la valeur d’état est `ReadyRole`) le démon Docker (service Docker) démarre et vous pouvez vous connecter à l’hôte de conteneur Docker.

Pour tester la machine virtuelle Docker que vous avez créé dans Azure, tapez

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

où * &lt;vm-nom-vous-utilisé&gt; * est le nom de l’ordinateur virtuel que vous avez utilisé dans votre appel à `azure vm docker create`. Vous devriez voir quelque chose de semblable à la suivante, qui indique que vos machines virtuelles Docker est exécuté dans Azure et en attente de vos commandes. 

Maintenant vous pouvez essayer de vous connecter à l’aide de votre client de docker pour obtenir des informations (dans certaines configurations de client Docker, tel que celui sur le Mac, il se peut que vous deviez utiliser `sudo`) :

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Juste pour être sûr qu’il s’agit d’utilisation, vous pouvez examiner la machine virtuelle pour l’extension de Docker :

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Authentification de l’ordinateur virtuel docker hôte

Outre la création de la machine virtuelle Docker, le `azure vm docker create` commande crée également automatiquement les certificats nécessaires pour permettre à votre ordinateur de client Docker pour vous connecter à l’hôte de conteneur Azure utilisant le protocole HTTPS et les certificats sont stockés sur des ordinateurs à la fois le client et l’hôte, selon le cas. Lors des tentatives suivantes, les certificats existants sont réutilisées et partagées avec le nouvel hôte.

Par défaut, les certificats sont placés dans `~/.docker`, et Docker sera configuré pour s’exécuter sur le port **2376**. Si vous souhaitez utiliser un autre port ou un répertoire, puis que vous pouvez utiliser une des opérations suivantes `azure vm docker create` les options de ligne de commande pour configurer votre conteneur Docker hébergent des VM à utiliser un autre port ou certificats différents pour les clients qui se connectent :

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Le démon Docker sur l’hôte est configuré pour écouter et authentifier les connexions client sur le port spécifié à l’aide de certificats générés par le `azure vm docker create` commande. L’ordinateur client doit disposer de ces certificats pour accéder à l’hôte Docker.

> [AZURE.NOTE] Un hôte réseau exécutant sans ces certificats sera vulnérable à tout utilisateur pouvant pour se connecter à l’ordinateur. Avant de modifier la configuration par défaut, assurez-vous que vous comprenez les risques pour vos ordinateurs et vos applications.

## <a name="next-steps"></a>Étapes suivantes

* Vous êtes prêt à accéder au [Guide de l’utilisateur Docker] et d’utiliser votre machine virtuelle de Docker. Pour créer un ordinateur virtuel de Docker-activé dans le nouveau portail, voir [comment utiliser l’Extension de la machine virtuelle Docker avec le portail].

* L’extension Azure Docker VM prend également en charge la composition de Docker, qui utilise un fichier YAML déclaratif pour prendre une application modélisé de développeur dans tous les environnements et générer un déploiement cohérent. Reportez-vous à la section [Mise en route de Docker et composition pour définir et exécuter une application conteneur multiples sur une machine virtuelle Azure].  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Comment faire pour utiliser l’Extension de la machine virtuelle Docker avec le portail]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guide de l’utilisateur docker]: https://docs.docker.com/userguide/
 
[Mise en route de Docker et composition pour définir et exécuter une application conteneur multiples sur une machine virtuelle Azure]:virtual-machines-linux-docker-compose-quickstart.md