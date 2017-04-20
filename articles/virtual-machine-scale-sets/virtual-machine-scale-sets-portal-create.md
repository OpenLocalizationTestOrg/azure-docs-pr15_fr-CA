<properties
    pageTitle="Créer un ensemble d’échelle de Machine virtuelle via le portail Azure | Microsoft Azure"
    description="Déployer des jeux d’échelle à l’aide d’Azure portal."
    keywords="jeux d’échelle de machine virtuelle" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Créer un ensemble d’échelle de Machine virtuelle via le portail Azure

Ce didacticiel vous montre combien il est facile pour créer un ensemble d’échelle de Machine virtuelle en quelques minutes, à l’aide du portail Azure. Si vous n’avez pas un abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Cliquez sur l’image de l’ordinateur virtuel depuis le site marketplace

À partir du portail, vous pouvez facilement déployer une échelle définie avec CentOS, CoreOS, Debian, Open Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, serveur d’Ubuntu ou images de Windows Server.

Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur web. Cliquez sur `New`, recherchez `scale set`, puis sélectionnez le `Virtual machine scale set` entrée :

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Créer la machine virtuelle Linux

Vous pouvez désormais utiliser les paramètres par défaut et rapidement créer l’ordinateur virtuel.

* Sur le `Basics` lame, entrez un nom pour l’ensemble de l’échelle. Ce nom devient la base du nom de domaine complet de l’équilibreur de charge devant l’ensemble de l’échelle, par conséquent, assurez-vous que le nom est unique dans l’ensemble d’Azure.

* Sélectionnez votre système d’exploitation souhaité type, entrez votre nom d’utilisateur souhaité, sélectionnez le type d’authentification que vous préférez. Si vous choisissez un mot de passe, il doit être d’au moins 12 caractères et répondre à trois des quatre types de caractères suivants : une minuscule, une majuscule, un chiffre et un caractère spécial. Reportez-vous à la section plus d’informations sur [les exigences de nom d’utilisateur et le mot de passe](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Si vous choisissez `SSH public key`, assurez-vous de ne coller dans votre clé publique, pas votre clé privée :

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Entrez votre nom de groupe de ressource de votre choix et l’emplacement, puis cliquez sur `OK`.

* Sur le `Virtual machine scale set service settings` blade : entrez votre étiquette de nom de domaine de votre choix (la base du nom de domaine complet de l’équilibreur de charge devant l’ensemble de l’échelle). Cette étiquette doit être unique dans l’ensemble d’Azure.

* Choisissez votre image de disque de système d’exploitation, le nombre d’instances et la taille de l’ordinateur.

* Activer ou désactiver l’échelle automatique et configurer si activé :

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Sur le `Summary` lame, lorsque la validation est effectuée, cliquez sur `OK`.

* Enfin, sur le `Purchase` lame, cliquez sur `Purchase` pour démarrer l’échelle définie au déploiement.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Se connecter à un ordinateur virtuel dans l’ensemble de l’échelle

Une fois votre jeu d’échelle est déployé, accédez à la `Inbound NAT Rules` onglet de l’équilibreur de charge pour l’ensemble de l’échelle :

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Vous pouvez vous connecter à chaque ordinateur virtuel dans l’échelle à l’aide de ces règles NAT. Par exemple, pour un ensemble d’échelle de Windows, s’il existe une règle NAT sur le port entrant 50000, d’une connexion à cet ordinateur via le protocole RDP sur `<load-balancer-ip-address>:50000`. Pour un ensemble d’échelle de Linux, vous connecte à l’aide de la commande `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Étapes suivantes

Pour des informations sur le déploiement de jeux d’échelle à partir de l’interface CLI, consultez [cette documentation](./virtual-machine-scale-sets-cli-quick-create.md).

Pour des informations sur le déploiement de jeux d’échelle à partir de PowerShell, consultez [cette documentation](./virtual-machine-scale-sets-windows-create.md).

Pour des informations sur le déploiement de jeux d’échelle à partir de Visual Studio, consultez [cette documentation](./virtual-machine-scale-sets-vs-create.md).

Pour des informations générales, consultez la [page Vue d’ensemble de la documentation pour l’échelle définit](./virtual-machine-scale-sets-overview.md).

Pour plus d’informations, consultez la [page d’accueil pour les jeux de l’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

