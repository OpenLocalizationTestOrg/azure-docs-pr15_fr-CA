<properties
   pageTitle="Mise en route avec docker essaim sur Azure"
   description="Décrit comment créer un groupe d’ordinateurs virtuels avec l’Extension de la machine virtuelle Docker et permet de créer un cluster Docker essaim."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# <a name="how-to-use-docker-with-swarm"></a>L’utilisation de docker avec essaim

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Cette rubrique montre un moyen très simple à utiliser [docker](https://www.docker.com/) avec [par essaim](https://github.com/docker/swarm) pour créer un cluster géré par essaims particulaires dans Azure. Il crée quatre ordinateurs virtuels dans Azure, un en tant que gestionnaire de l’essaim et trois dans le cadre du cluster des hôtes de docker. Lorsque vous avez terminé, vous pouvez utiliser l’essaim pour le cluster et commencez à utiliser docker dessus. En outre, les appels de CLI d’Azure dans cette rubrique utilisent le mode de gestion (asm) de service. 

> [AZURE.NOTE] Cette rubrique utilise docker avec essaim et la CLI d’Azure *sans* l’utilisation de **docker-machine** pour afficher comment les différents outils fonctionnent ensemble, mais restent indépendantes. **docker-machine** a **--par essaim** les commutateurs qui vous permettent d’utiliser **docker-machine** pour ajouter directement des nœuds à un essaim. Pour obtenir un exemple, consultez la documentation de [l’ordinateur docker](https://github.com/docker/machine) . Si vous avez manqué **docker-machine** exécute dans Azure VM, voir [comment utiliser docker-machine avec Azure](virtual-machines-linux-docker-machine.md).

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>Créer des hôtes de docker avec plusieurs Machines virtuelles Azure

Cette rubrique crée quatre VMs, mais vous pouvez utiliser n’importe quel nombre de que votre choix. Appeler les opérations suivantes avec * &lt;mot de passe&gt; * remplacé par le mot de passe que vous avez choisi.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Lorsque vous avez terminé, vous serez en mesure d’utiliser la **liste d’azure vm** pour voir vos ordinateurs virtuels d’Azure :

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>L’installation par essaims particulaires dans le masque de l’essaim VM

Cette rubrique utilise le [modèle de conteneur d’installation à partir de la docker par essaim documentation](https://github.com/docker/swarm#1---docker-image) --mais vous pourriez également SSH pour le **maître de l’essaim**. Dans ce modèle, **par essaim** est téléchargé sous la forme d’un conteneur docker en cours d’exécution par essaims particulaires. Ci-après, nous allons effectuer cette étape *à distance à partir de notre ordinateur portable à l’aide de docker* pour se connecter à la machine virtuelle **essaim maître** et lui indique d’utiliser la commande de création de l’id cluster, **créer de l’essaim**. L’id du cluster est comment **par essaim** découvre les membres du groupe de l’essaim. (Vous pouvez également cloner le référentiel et de le générer vous-même, ce qui vous donne un contrôle total et activer le débogage.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

Cette dernière ligne est l’id du cluster ; copier quelque part, car vous l’utiliserez à nouveau lorsque vous joignez le nœud ordinateurs virtuels au masque essaim pour créer l’essaim « ». Dans cet exemple, l’id du cluster est **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Pour être clair, nous utilisons notre installation docker local pour se connecter au **maître essaim** VM dans Azure et instruction **essaim maître** pour télécharger, installer et exécuter la commande **create** , qui renvoie les id notre cluster que nous utilisons ultérieurement à des fins de découverte.
<!-- -->
> Pour confirmer cela, exécutez `docker -H tcp://` * &lt;nom d’hôte&gt; * ` images` pour répertorier les processus conteneur sur l’ordinateur **maître essaim** et sur un autre nœud pour la comparaison (car nous a exécuté la commande essaim précédente avec le commutateur **--rm** , le conteneur a été supprimé une fois l’opération terminée, à l’aide de **docker ps - un** ne retourne rien). :


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
>Si vous êtes familiarisé avec **docker**, vous savez que les autres nœuds qu’aucune entrée car aucune image n’ont pas été téléchargés et exécuter encore.

## <a name="join-the-node-vms-to-our-docker-cluster"></a>Joindre le nœud ordinateurs virtuels à notre cluster docker

Pour chaque nœud, répertorier les informations de point de terminaison à l’aide de la CLI d’Azure. Ci-dessous nous faire pour que l’hôte de docker **essaim-nœud-1** afin d’obtenir le port de docker du nœud.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


À l’aide de **docker** et le `-H` option de client docker sur le nœud de votre machine virtuelle, rejoindre ce nœud à l’essaim que vous créez en passant l’id du cluster et le port de docker du nœud (ce dernier à l’aide de **--addr**) :

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Qui convient. Pour confirmer que **par essaim** s’exécute sur **essaim-nœud-1** , que nous allons saisir :

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Répétez pour tous les autres nœuds du cluster. Dans notre cas, nous le faire pour **essaim-nœud-2** et **essaim-nœud-3**.

## <a name="begin-managing-the-swarm-cluster"></a>Commencer la gestion du cluster de l’essaim

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

et puis vous pouvez répertorier vos nœuds de votre cluster :

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Accédez à exécuter des opérations sur votre essaim. Pour rechercher des modèles, reportez-vous à [https://github.com/docker/swarm/](https://github.com/docker/swarm/), ou peut-être une [vidéo](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 
