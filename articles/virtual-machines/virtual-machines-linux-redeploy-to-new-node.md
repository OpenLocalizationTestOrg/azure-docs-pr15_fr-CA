<properties 
    pageTitle="Redéployer les ordinateurs virtuels Linux | Microsoft Azure" 
    description="Explique comment redéployer des machines virtuelles de Linux pour atténuer les problèmes de connexion SSH." 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Redéployer l’ordinateur virtuel pour le nouveau nœud Azure

Si vous avez été confrontés à des difficultés de dépannage SSH ou application à accéder à une machine virtuelle Azure (VM), redéploiement de la machine virtuelle peut aider. Lorsque vous redéployez un ordinateur virtuel, il déplace de la machine virtuelle vers un nouveau nœud au sein de l’infrastructure Azure et il se met sous tension, en conservant tous vos options de configuration et de ressources associées. Cet article vous explique comment redéployer une machine virtuelle à l’aide de la CLI d’Azure ou le portail Azure.

> [AZURE.NOTE] Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques liées à l’interface réseau virtuelle sont mis à jour. 


## <a name="using-azure-cli"></a>À l’aide de la CLI Azure

Assurez-vous que vous disposez de la [Dernière Azure CLI installé](../xplat-cli-install.md) sur votre ordinateur et que vous êtes en mode de Gestionnaire des ressources (`azure config mode arm`).

Utilisez la commande suivante de la CLI d’Azure pour redéployer votre machine virtuelle :

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Vous pouvez voir le statut de la modification de la machine virtuelle comme il passe par le processus de redéploiement. Le `PowerState` de la machine virtuelle accède à partir de 'Running' à 'Mise à jour », puis « démarrage » et enfin 'Running' comme il passe par le processus de redéploiement vers un nouvel hôte. Vérifier l’état des ordinateurs virtuels au sein d’un groupe de ressources avec :

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes de connexion à votre ordinateur virtuel, vous trouverez une aide spécifique sur le [Dépannage des connexions SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) ou [SSH détaillée des étapes de dépannage](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Si vous ne peut pas accéder à une application en cours d’exécution sur votre ordinateur virtuel, vous pouvez également lire les [problèmes de résolution des problèmes d’application](virtual-machines-linux-troubleshoot-app-connection.md).