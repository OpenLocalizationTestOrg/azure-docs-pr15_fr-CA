<properties
    pageTitle="Créer une copie de votre machine virtuelle Linux de Azure | Microsoft Azure"
    description="Apprenez à créer une copie de votre machine virtuelle de Linux d’Azure dans le modèle de déploiement du Gestionnaire de ressources"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Créer une copie d’une machine virtuelle de Linux qui exécute sur Azure


Cet article vous montre comment créer une copie de votre Azure machine virtuelle (VM) Linux utilisant le modèle de déploiement du Gestionnaire de ressources en cours d’exécution. Tout d’abord copier le système d’exploitation et les disques de données vers un nouveau conteneur, puis configurer les ressources réseau et créer l’ordinateur virtuel.

Vous pouvez également [télécharger et de créer un ordinateur virtuel à partir d’images de disques personnalisées](virtual-machines-linux-upload-vhd.md).


## <a name="before-you-begin"></a>Avant de commencer

Vous assurer que les conditions préalables suivantes avant de commencer les étapes :

- Vous avez le [CLI Azure] (... / xplat-cli-install.md) téléchargées et installées sur votre ordinateur. 

- Vous devez également certaines informations sur votre existant Azure VM de Linux :

| Informations de source de machine virtuelle | Où l’obtenir |
|------------|-----------------|
| Nom de la machine virtuelle | `azure vm list` |
| Nom du groupe de ressources | `azure vm list` |
| Emplacement | `azure vm list` |
| Nom du compte de stockage | `azure storage account list -g <resourceGroup>` |
| Nom du conteneur | `azure storage container list -a <sourcestorageaccountname>` |
| Nom du fichier source VM VHD | `azure storage blob list --container <containerName>` |



- Vous devez faire certains choix sur votre nouvelle machine virtuelle :   <br> -Nom du conteneur   <br> Nom de la machine virtuelle-   <br> Taille de mémoire virtuelle-   <br> vNet - nom   <br> : Nom du sous-réseau   <br> IP - nom   <br> Nom de la carte d’interface réseau-
    

## <a name="login-and-set-your-subscription"></a>Connexion et définissez votre abonnement

1. Connexion à l’interface CLI.
        
        azure login

2. Assurez-vous que vous êtes en mode de Gestionnaire des ressources.
    
        azure config mode arm

3. Définir l’abonnement approprié. Vous pouvez utiliser « liste compte azure » pour consulter tous vos abonnements.

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>Arrêter la machine virtuelle 

Arrêtez et libérer de la mémoire virtuelle de la source. Vous pouvez utiliser 'azure vm list' pour obtenir la liste de tous les ordinateurs virtuels de votre abonnement et leurs ressources les noms de groupe.
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>Copie du disque dur virtuel


Vous pouvez copier le disque dur virtuel à partir du stockage de la source à la destination à l’aide de la `azure storage blob copy start`. Dans cet exemple, nous allons copier le disque dur virtuel pour le même compte de stockage, mais un autre conteneur.

Pour copier le disque dur virtuel à un autre conteneur dans le même compte de stockage, tapez :

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurer le réseau virtuel pour votre nouvelle machine virtuelle

Configurer un réseau virtuel et d’une carte réseau pour votre nouvelle machine virtuelle. 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>Créer la nouvelle machine virtuelle 

Vous pouvez maintenant créer une machine virtuelle à partir de votre téléchargé disque virtuel [à l’aide d’un modèle de gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou par le biais de l’interface CLI en spécifiant l’URI pour votre disque copié en tapant :

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser Azure CLI pour gérer votre nouvelle machine virtuelle, consultez [commandes de CLI d’Azure pour le Gestionnaire de ressources Azure](azure-cli-arm-commands.md).
