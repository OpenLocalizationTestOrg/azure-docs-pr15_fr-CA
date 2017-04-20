<properties
    pageTitle="L’utilisation d’Extension de machine virtuelle Docker pour Linux | Microsoft Azure"
    description="Décrit comment créer des ordinateurs virtuels Azure qui sont des hôtes de docker à l’aide de la CLI d’Azure dans le modèle de déploiement classiques et les extensions d’ordinateurs virtuels Azure et Docker."
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
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>À l’aide de l’Extension de la machine virtuelle Docker avec le portail classique Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/) est une des approches de virtualisation les plus populaires qui utilise des [conteneurs de Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des ordinateurs virtuels de l’isolation des données et de l’informatique sur les ressources partagées. Vous pouvez utiliser l’extension Docker VM gérée par [Agent de Linux Azure] pour créer une machine virtuelle Docker qui héberge un nombre quelconque de conteneurs pour vos applications sur Azure.

> [AZURE.NOTE] Cette rubrique décrit comment créer un ordinateur virtuel de Docker à partir du portail classique Azure. Pour savoir comment créer un ordinateur virtuel de Docker à la ligne de commande, voir [comment utiliser l’Extension de machine virtuelle Docker à partir de l’Interface de ligne de commande Azure (Azure CLI)]. Pour obtenir une description détaillée des conteneurs et leurs avantages, consultez le [Tableau blanc de niveau élevé Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="create-a-new-vm-from-the-image-gallery"></a>Créer une nouvelle machine virtuelle à partir de la galerie d’Image
La première étape nécessite une Azure VM à partir d’une image de Linux qui prend en charge l’Extension de machine virtuelle Docker, en utilisant une image Ubuntu 14.04 LTS à partir de la galerie d’Image sous la forme d’une image du serveur exemple et Ubuntu 14.04 bureau en tant que client. Dans le portail, cliquez sur **+ nouvelle** dans le coin inférieur gauche pour créer une nouvelle instance de la machine virtuelle et sélectionnez une image Ubuntu 14.04 LTS à partir des sélections disponibles ou à partir de la galerie d’Image complète, comme indiqué ci-dessous.

> [AZURE.NOTE] Actuellement, seules les images Ubuntu 14.04 LTS plus récentes que celle de juillet 2014 prend en charge l’Extension de la machine virtuelle Docker.

![Créer une nouvelle Image d’Ubuntu](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Créer des certificats de Docker

Une fois que la machine virtuelle a été créée, assurez-vous que Docker est installé sur votre ordinateur client. (Pour plus d’informations, voir [instructions d’installation de Docker](https://docs.docker.com/installation/#installation)).

Créez les fichiers de certificat et la clé pour la communication Docker en fonction de [l’Exécution de Docker avec https] et les placer dans le **`~/.docker`** répertoire sur votre ordinateur client.

> [AZURE.NOTE] L’Extension de la machine virtuelle Docker dans le portail requiert actuellement les informations d’identification qui sont codées en base64.

À l’invite de commandes, utilisez **`base64`** ou un autre outil de codage préféré pour créer des rubriques de codé en base64. Cette opération avec un ensemble de fichiers de certificat et la clé simple peut ressembler à ceci :

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Ajouter l’Extension de la machine virtuelle Docker
Pour ajouter l’Extension de la machine virtuelle Docker, localiser l’instance de machine virtuelle que vous avez créé et faites défiler jusqu'à **Extensions** et cliquez dessus pour faire apparaître les Extensions de la machine virtuelle, comme illustré ci-dessous.
> [AZURE.NOTE] Cette fonctionnalité est prise en charge dans le portail de prévisualisation uniquement : https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>Ajouter une Extension
Cliquez sur le **+ Ajouter** pour afficher les Extensions de machine virtuelle possibles, vous pouvez ajouter à cette machine virtuelle.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>Sélectionnez l’Extension de la machine virtuelle Docker
L’Extension de machine virtuelle Docker, qui affiche la description de le Docker et les liens importants, et cliquez sur **créer** en bas pour commencer la procédure d’installation.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>Ajouter votre certificat et les fichiers de clés :

Dans les champs de formulaire, entrez les versions codée en base64 de votre certificat d’autorité de certification, votre certificat de serveur et votre clé de serveur, comme le montre le graphique suivant.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] Notez que (comme dans l’image précédente) le 2376 est renseigné par défaut. Vous pouvez entrer ici n’importe quel point de terminaison, mais la prochaine étape sera d’ouvrir le point de terminaison correspondant. Si vous modifiez la valeur par défaut, veillez à ouvrir le point de terminaison correspondant à l’étape suivante.

## <a name="add-the-docker-communication-endpoint"></a>Ajouter le point de terminaison de Communication Docker
Lorsque vous affichez le groupe de ressources que vous avez créé, sélectionnez le groupe de sécurité de réseau associé à votre machine virtuelle et cliquez sur **Règles de sécurité de trafic entrant** pour afficher les règles, comme indiqué ici.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Cliquez sur **+ Ajouter** pour ajouter une autre règle et dans le cas par défaut, entrez un nom pour le point de terminaison (dans cet exemple, **Docker**), 2376 'plage de Port de Destination ». Définir la valeur de protocole montrant **TCP**, puis cliquez sur **OK** pour créer la règle.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>Test de votre Client de Docker et de l’hôte d’Azure Docker
Recherchez et copiez le nom de domaine de vos machines virtuelles et à la ligne de commande de votre ordinateur client, tapez `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (où *dockerextension* est remplacé par le sous-domaine pour votre machine virtuelle).

Le résultat doit ressembler à ceci :

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Une fois que vous terminé les étapes ci-dessus, vous avez maintenant un hôte Docker entièrement fonctionnel en cours d’exécution sur un ordinateur virtuel Azure, configuré pour être connecté à distance à partir d’autres clients.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à accéder au [Guide de l’utilisateur Docker] et d’utiliser votre machine virtuelle de Docker. Si vous souhaitez automatiser la création des hôtes de Docker sur Azure VM via l’interface de ligne de commande, voir [comment utiliser l’Extension de machine virtuelle à partir de l’Interface de ligne de commande Azure (Azure CLI) Docker]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Comment faire pour utiliser l’Extension de machine virtuelle à partir de l’Interface de ligne de commande Azure (Azure CLI) Docker]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agent de Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Docker en cours d’exécution avec https]: http://docs.docker.com/articles/https/
[Guide de l’utilisateur docker]: https://docs.docker.com/userguide/
