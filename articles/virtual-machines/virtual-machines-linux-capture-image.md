<properties
    pageTitle="Capturer un Linux VM à utiliser en tant que modèle | Microsoft Azure"
    description="Découvrez comment capturer et généraliser l’image d’une Linux Azure machine virtuelle (VM) créée avec le modèle de déploiement du Gestionnaire de ressources Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capture d’une machine virtuelle de Linux qui exécute sur Azure

Suivez les étapes décrites dans cet article pour généraliser et capturer votre machine Azure Linux (VM) dans le modèle de déploiement du Gestionnaire de ressources. Lorsque vous généralisez la machine virtuelle, vous supprimez des informations personnelles et préparez la machine virtuelle à utiliser en tant qu’image. Puis, vous capturez une image de disque dur virtuel (VHD) généralisée pour le système d’exploitation, les disques durs virtuels pour les disques de données et un [Gestionnaire de ressources du modèle](../azure-resource-manager/resource-group-overview.md) pour les nouveaux déploiements de machine virtuelle. 

Pour créer des ordinateurs virtuels à l’aide de l’image, configurer les ressources de réseau pour chaque nouvel ordinateur virtuel et utiliser le modèle (un fichier JavaScript Object Notation ou JSON,) pour déployer des images VHD capturées. De cette façon, vous pouvez répliquer une machine virtuelle avec sa configuration logicielle actuelle, similaire à celle que vous utilisez des images sur le marché d’Azure.

>[AZURE.TIP]Si vous souhaitez créer une copie de votre VM Linux existants avec son état spécialisée pour la sauvegarde ou le débogage, consultez [créer une copie d’une machine virtuelle de Linux qui exécute sur Azure](virtual-machines-linux-copy-vm.md). Si vous souhaitez télécharger un VHD Linux à partir d’un ordinateur virtuel de locaux, voir [télécharger et créer une VM Linux à partir de l’image de disque personnalisé](virtual-machines-linux-upload-vhd.md).  

## <a name="before-you-begin"></a>Avant de commencer

Vous assurer que les conditions préalables suivantes :

* **Azure VM est créé dans le modèle de déploiement du Gestionnaire de ressources** - si vous n’avez pas créé une VM Linux, que vous pouvez utiliser le [portail](virtual-machines-linux-quick-create-portal.md), [Azure CLI](virtual-machines-linux-quick-create-cli.md)ou les [modèles du Gestionnaire de ressources](virtual-machines-linux-cli-deploy-templates.md). 

    Configurez l’ordinateur virtuel selon vos besoins. Par exemple, [l’ajout de disques de données](virtual-machines-linux-add-disk.md), appliquer des mises à jour et installer des applications. 
* **Azure CLI** - installation de l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) sur un ordinateur local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Étape 1 : Supprimer l’agent Azure Linux

Tout d’abord, exécutez la commande **waagent** avec le paramètre **d’annulation** sur la VM Linux. Cette commande supprime les fichiers et les données à préparer la machine virtuelle pour la généralisation. Pour plus d’informations, consultez le [guide d’utilisateur de l’Agent de Linux Azure](virtual-machines-linux-agent-user-guide.md).

1. Se connecter à votre VM Linux à l’aide d’un client SSH.

2. Dans la fenêtre SSH, tapez la commande suivante :

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] Uniquement, exécutez cette commande sur un ordinateur virtuel que vous souhaitez capturer en tant qu’image. Il ne garantit pas que l’image est effacée de toutes les informations sensibles ou qu’il est approprié pour la redistribution.

3. Tapez **y** pour continuer. Vous pouvez ajouter la **-force** paramètre afin d’éviter cette étape de confirmation.

4. Une fois la commande terminée, tapez **exit**. Cette étape ferme le client SSH.

    
## <a name="step-2-capture-the-vm"></a>Étape 2 : Capturer la machine virtuelle

L’infrastructure du langage commun Azure permet de généraliser et capturer la machine virtuelle. Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Exemples de noms de paramètre incluent **myResourceGroup**, **myVnet**et **myVM**.

5. À partir de votre ordinateur local, ouvrez l’interface CLI de Azure et d’une [connexion d’accès à votre abonnement Azure](../xplat-cli-connect.md). 

6. Assurez-vous que vous êtes en mode de Gestionnaire des ressources.

    ```
    azure config mode arm
    ```

7. Arrêtez l’ordinateur virtuel que vous déjà arrêtés à l’aide de la commande suivante :

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. Généraliser la machine virtuelle avec la commande suivante :

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. Maintenant, exécutez la commande **capture de vm azure** , qui capture de la machine virtuelle. Dans l’exemple suivant, l’image VHD sont capturés avec les noms commençant par **MyVHDNamePrefix**, et l’option **-t** spécifie un chemin d’accès au modèle de **MyTemplate.json**. 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]Ces fichiers image sont créés par défaut dans le même compte de stockage utilisé par l’ordinateur virtuel d’origine. Utilisez le *même compte de stockage* pour stocker les disques durs virtuels pour les nouveaux ordinateurs virtuels que vous créez à partir de l’image. 

6. Pour trouver l’emplacement d’une image capturée, ouvrez le modèle JSON dans un éditeur de texte. Dans la **storageProfile**, recherchez l' **uri** de l' **image** que qui se trouve dans le conteneur **système** . Par exemple, l’URI de l’image de disque du système d’exploitation est similaire à`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Étape 3 : Créer un ordinateur virtuel à partir de l’image capturée
Maintenant, utiliser l’image avec un modèle pour créer une VM Linux. Ces étapes vous montrent comment utiliser la CLI Azure et le modèle de fichier JSON capturées pour créer l’ordinateur virtuel dans un nouveau réseau virtuel.

### <a name="create-network-resources"></a>Créer des ressources de réseau

Pour utiliser le modèle, vous devez d’abord configurer un réseau virtuel et d’une carte réseau pour votre nouvelle machine virtuelle. Nous vous recommandons de que vous créez un groupe de ressources pour ces ressources à l’emplacement où est stockée votre image de machine virtuelle. Exécuter des commandes similaires aux noms suivants, en remplaçant pour un emplacement Azure approprié (« centralus » dans ces commandes) et de vos ressources :

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>Obtenir l’Id de la carte réseau

Pour déployer un ordinateur virtuel à partir de l’image à l’aide du JSON que vous avez enregistré au cours de la capture, vous avez besoin de l’Id de la carte réseau. Obtenir en exécutant la commande suivante :

    azure network nic show MyResourceGroup1 myNIC

L' **Id** de la sortie est similaire à`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>Créer un ordinateur virtuel
Maintenant, exécutez la commande suivante pour créer votre machine virtuelle à partir de l’image capturée de la machine virtuelle. Utilisez le paramètre **-f** pour spécifier le chemin d’accès au fichier JSON du modèle que vous avez enregistré.

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

Dans la sortie de la commande, vous êtes invité à fournir un nouveau nom de la machine virtuelle, le nom d’utilisateur admin et mot de passe et l’Id de la carte réseau que vous avez créé précédemment.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

L’exemple suivant montre ce que vous voyez pour un déploiement réussi :

    + Les paramètres et les configurations de modèle lors de l’initialisation
    + Création des informations de déploiement : xxxxxxx de déploiement de modèle de création
    + En attente de déploiement afin de compléter les données : DeploymentName : MyDeployment données : ResourceGroupName : données de MyResourceGroup1 : ProvisioningState : a réussi des données : Timestamp : xxxxxxx données : Mode : données incrémentielles : nom Type valeur


    data:    ------------------  ------------  -------------------------------------

    données : vmName chaîne myNewVM


    données : vmSize Standard_D1 de chaîne


    données : myAdminuser de chaîne d’adminUserName


    données : adminPassword SecureString non définie


    données : networkInterfaceId, info de /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic String : déploiement d’un groupe Créer commande OK

### <a name="verify-the-deployment"></a>Vérifier le déploiement

Maintenant SSH à la machine virtuelle que vous avez créé pour vérifier le déploiement et le démarrer à l’aide de la nouvelle machine virtuelle. Pour vous connecter via le protocole SSH, recherchez l’adresse IP de la machine virtuelle que vous avez créé en exécutant la commande suivante :

    azure network public-ip show MyResourceGroup1 myIP

L’adresse IP publique est répertorié dans la sortie de commande. Par défaut, vous vous connectez à la VM Linux par SSH sur le port 22.

## <a name="create-additional-vms"></a>Créer des ordinateurs virtuels supplémentaires
Pour déployer des ordinateurs virtuels supplémentaires en suivant les étapes dans la section précédente, utilisez le modèle et l’image capturée. Incluent les autres options pour créer des ordinateurs virtuels à partir de l’image à l’aide d’un modèle de démarrage rapide ou de l’exécution de la commande **créer d’azure vm** .

### <a name="use-the-captured-template"></a>Utilisez le modèle capturé

Pour utiliser l’image capturée et le modèle, procédez comme suit (détaillée dans la section précédente) :

* Assurez-vous que votre image d’ordinateur virtuel est dans le même compte de stockage qui héberge votre VM VHD.
* Copiez le fichier de modèle de JSON et spécifiez un nom unique pour le disque du système d’exploitation du disque dur virtuel de la nouvelle machine virtuelle (ou des disques durs virtuels). Par exemple, dans le **storageProfile**, sous le **disque dur virtuel**, dans **uri**, spécifiez un nom unique pour le disque dur virtuel, semblable à **osDisk**`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Créer une carte d’interface réseau dans le même ou un autre réseau virtuel.
* Le fichier de modèle modifié de JSON, créez un déploiement dans le groupe de ressources qui permet de paramétrer le réseau virtuel.

### <a name="use-a-quickstart-template"></a>Utiliser un modèle de démarrage rapide

Si vous souhaitez que le réseau configuré automatiquement lorsque vous créez un ordinateur virtuel à partir de l’image, vous pouvez spécifier ces ressources dans un modèle. Par exemple, consultez le [modèle de 101-vm-de-utilisateur-image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) à partir de GitHub. Ce modèle crée un ordinateur virtuel à partir de votre image personnalisée et le réseau virtuel nécessaire, adresse IP publique et ressources de la carte réseau. Pour une procédure pas à pas de l’utilisation du modèle dans le portail Azure, consultez [comment créer un ordinateur virtuel à partir d’une image personnalisée à l’aide d’un modèle de gestionnaire de ressources](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Utilisation de la machine virtuelle azure Créer commande

Il est généralement plus facile d’utiliser un modèle de gestionnaire de ressources pour créer un ordinateur virtuel à partir de l’image. Toutefois, vous pouvez créer de la VM _impérative_ à l’aide de la commande **créer d’azure vm** avec **-Q** (**--image-urn**) paramètre. Si vous utilisez cette méthode, vous transmettez également le paramètre **-d** (**--système d’exploitation-disque-disque dur virtuel**) pour spécifier l’emplacement du fichier .vhd du système d’exploitation pour la nouvelle machine virtuelle. Ce fichier doit être dans le conteneur de disques durs virtuels du compte de stockage où se trouve le fichier d’image disque dur virtuel. La commande copie le disque dur virtuel pour la nouvelle machine virtuelle automatiquement dans le conteneur de **disques durs virtuels** .

Avant d’exécuter la commande **créer d’azure vm** avec l’image, procédez comme suit :

1.  Créer un groupe de ressources, ou identifier un groupe de ressources existant pour le déploiement.

2.  Créer une ressource d’adresse IP publique et d’une ressource de la carte réseau pour la nouvelle machine virtuelle. Pour obtenir la procédure créer un réseau virtuel, une adresse IP publique et une carte d’interface réseau à l’aide de l’interface CLI, reportez-vous à la section plus haut dans cet article. (**créer des vm azure** peut créer également une carte réseau, mais vous devez passer des paramètres supplémentaires pour un sous-réseau et un réseau virtuel).


Exécutez ensuite une commande qui passe d’URI pour le nouveau fichier de disque dur virtuel de système d’exploitation et de l’image existante. Dans cet exemple, une taille virtuelle de Standard_A1 est créé dans la région des États-Unis.

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

Pour les options de commande supplémentaires, exécutez `azure help vm create`.

## <a name="next-steps"></a>Étapes suivantes

Pour gérer vos ordinateurs virtuels avec l’interface CLI, visualiser les tâches de [déployer et gérer des ordinateurs virtuels à l’aide de modèles d’Azure Resource Manager et la CLI Azure](virtual-machines-linux-cli-deploy-templates.md).
