<properties
    pageTitle="Capturer une image d’une VM Linux | Microsoft Azure"
    description="Découvrez comment capturer l’image d’une Linux Azure machine virtuelle (VM) créée avec le modèle de déploiement classique."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Comment faire pour capturer un ordinateur de virtuel Linux classique sous forme d’image

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-machines-linux-capture-image.md).

Cet article vous montre comment capturer une machine virtuelle Azure classique que Linux en cours d’exécution sous forme d’image pour créer d’autres machines virtuelles. Cette image inclut le disque du système d’exploitation et les disques de données associés à la machine virtuelle. Il n’inclut pas la configuration réseau, vous devez configurer que lorsque vous créez des autres ordinateurs virtuels à partir de l’image.

Azure stocke l’image sous **Images**, avec toutes les images que vous avez téléchargée. Pour plus d’informations sur les images, voir [Images de Machine virtuelle dans Azure] [].

## <a name="before-you-begin"></a>Avant de commencer

Ces étapes supposent que vous avez déjà créé une machine virtuelle Azure en utilisant le modèle de déploiement standard et configuré le système d’exploitation, y compris l’attachement des disques de données. Si vous avez besoin créer une machine virtuelle, Découvrez [comment créer une Machine virtuelle Linux] [].


## <a name="capture-the-virtual-machine"></a>Capture de la machine virtuelle

1. [Se connecter à l’ordinateur virtuel](virtual-machines-linux-mac-create-ssh-keys.md) à l’aide d’un client SSH de votre choix.

2. Dans la fenêtre SSH, tapez la commande suivante. La sortie de `waagent` peuvent varier légèrement selon la version de cet utilitaire :

    `sudo waagent -deprovision+user`

    Cette commande tente de nettoyer le système et le rendre approprié pour le réapprovisionnement. Cette opération effectue les tâches suivantes :

    - Supprime les clés d’hôte SSH (si Provisioning.RegenerateSshHostKeyPair est « y » dans le fichier de configuration)
    - Efface la configuration du serveur de noms dans /etc/resolv.conf
    - Supprime la `root` mot de passe à partir / etc/shadow (si Provisioning.DeleteRootPassword est « y » dans le fichier de configuration)
    - Supprime mis en cache les baux des clients DHCP
    - Nom d’hôte réinitialise localhost.localdomain
    - Supprime le dernier utilisateur mis en service compte (obtenu à partir de /var/lib/waagent) **et les données associées**.

    >[AZURE.NOTE] Mise hors service supprime les fichiers et les données de l’image « généraliser ». Uniquement, exécutez cette commande sur un ordinateur virtuel que vous souhaitez capturer en tant que nouvelle image modèle. Il ne garantit pas que l’image est effacée de toutes les informations sensibles ou qu’il est approprié pour la redistribution à des tiers.


3. Tapez **y** pour continuer. Vous pouvez ajouter la `-force` paramètre afin d’éviter cette étape de confirmation.

4. Tapez **Exit** pour fermer le client SSH.

    >[AZURE.NOTE] Les étapes restantes supposent que vous avez déjà [installé l’interface CLI d’Azure](../xplat-cli-install.md) sur votre ordinateur client. Toutes les étapes suivantes peuvent également être effectuées dans le [Azure portal classique] [].

5. À partir de votre ordinateur client, ouvrez Azure CLI et connexion d’accès à votre abonnement Azure. Pour plus d’informations, lire la [connexion à un abonnement Azure à partir de la CLI d’Azure](../xplat-cli-connect.md).

6. Assurez-vous que vous êtes en mode de gestion de Service :

    `azure config mode asm`

7. Arrêtez l’ordinateur virtuel qui est déjà arrêté dans les étapes précédentes avec :

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] Vous trouverez tous les ordinateurs virtuels créés dans votre abonnement à l’aide de`azure vm list`

8. Lorsque la machine virtuelle est arrêtée, capturer l’image avec la commande :

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    Tapez le nom d’image à la place du _nom de nouvelle image_. Cette commande crée une image de système d’exploitation généralisée. Le `-t` sous-commande supprime l’ordinateur virtuel d’origine.

9.  La nouvelle image est maintenant disponible dans la liste d’images qui peut être utilisé pour configurer les nouvelles machines virtuelles. Vous pouvez l’afficher avec la commande :

    `azure vm image list`

    Sur [Azure portal classique] [], il s’affiche dans la liste **d’IMAGES** .

    ![Capture de l’image réussie](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>Étapes suivantes
L’image est prêt à être utilisé pour créer des machines virtuelles. Vous pouvez utiliser la commande CLI d’Azure `azure vm create` et fournir le nom d’image que vous avez créé. Pour plus d’informations sur la commande, reportez-vous [à l’aide de la CLI Azure avec le modèle de déploiement standard](../virtual-machines-command-line-tools.md) . Le [Azure portal classique] [] permet également de créer un ordinateur virtuel personnalisé en utilisant la méthode **De la galerie** et sélectionnez l’image que vous avez créé. Pour plus d’informations, reportez-vous à la section [comment créer un ordinateur virtuel de personnalisé] [] .

**Voir aussi :** [Guide de l’utilisateur Azure Agent de Linux](virtual-machines-linux-agent-user-guide.md)

[Azure portal classique]: http://manage.windowsazure.com
[À propos des Images de Machine virtuelle dans Azure]: virtual-machines-linux-classic-about-images.md
[Comment faire pour créer un ordinateur virtuel personnalisé]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Comment faire pour créer une Machine virtuelle Linux]: virtual-machines-linux-classic-create-custom.md
