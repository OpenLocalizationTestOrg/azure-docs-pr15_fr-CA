<properties
   pageTitle="Déployer des machines virtuelles de carte d’interface réseau multiples à l’aide de la CLI d’Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à déployer des ordinateurs virtuels de carte d’interface réseau multiples à l’aide de la CLI d’Azure dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>Déployer plusieurs NIC ordinateurs virtuels (classique) à l’aide de la CLI d’Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Vous pouvez créer des machines virtuelles (VM) dans Azure et attacher plusieurs interfaces réseau (cartes réseau) pour chacun de vos ordinateurs virtuels. Plusieurs cartes réseau activer la séparation des types de trafic entre les cartes d’interface réseau. Par exemple, une carte réseau peut communiquer avec Internet, tandis que l’autre communique uniquement avec des ressources internes ne pas connectés à Internet. La possibilité de séparer le trafic réseau sur plusieurs cartes réseau est nécessaire pour de nombreuses appliances virtuelles réseau, telles que la livraison des applications et des solutions d’optimisation WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actuellement, vous ne peut pas avoir des ordinateurs virtuels avec une seule carte réseau et les ordinateurs virtuels avec plusieurs cartes réseau dans le même service de cloud. Par conséquent, vous devez mettre en œuvre les serveurs principaux dans un service cloud différents et tous les autres composants dans le scénario. La procédure ci-dessous utilise un service en nuage nommé *IaaSStory* pour les ressources principales et *IaaSStory-back-end* pour les serveurs principaux.

## <a name="prerequisites"></a>Conditions préalables

Avant de déployer les serveurs principaux, vous devez déployer le service en nuage principal avec toutes les ressources nécessaires pour ce scénario. Au minimum, vous devez créer un réseau virtuel à un sous-réseau pour le serveur principal. Visitez le site de [créer un réseau virtuel à l’aide de la CLI Azure](virtual-networks-create-vnet-classic-cli.md) pour apprendre à déployer un réseau virtuel.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Déployer l’ordinateurs virtuels back-end

Les ordinateurs virtuels du back-end dépendent de la création des ressources répertoriées ci-dessous.

- **Compte de stockage pour les disques de données**. Pour améliorer les performances, les disques de données sur les serveurs de base de données utilise la technologie lecteur (SSD) à l’état solide, qui requiert un compte de stockage premium. Assurez-vous que l’emplacement Azure que vous déployez pour prendre en charge le stockage de la prime.
- **Cartes d’interface réseau**. Chaque machine virtuelle possède deux cartes réseau, une pour l’accès de la base de données et l’autre pour la gestion.
- **Disponibilité définie**. Tous les serveurs de base de données seront ajoutés à la disponibilité d’une unique valeur, pour assurer au moins un des ordinateurs virtuels est et en cours d’exécution lors de la maintenance.

### <a name="step-1---start-your-script"></a>Étape 1 : démarrer le script

Vous pouvez télécharger le script bash complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Suivez les étapes ci-dessous pour modifier le script fonctionne dans votre environnement.

1. Modifier les valeurs des variables ci-dessous en fonction de votre groupe de ressources existant déployé ci-dessus, dans les [conditions préalables](#Prerequisites).

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. Modifier les valeurs des variables suivantes basées sur les valeurs que vous souhaitez utiliser pour votre déploiement de serveur principal.

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Étape 2 : créer les ressources nécessaires pour vos ordinateurs virtuels

1. Créer un nouveau service de cloud pour tous les ordinateurs virtuels de back-end. Notez l’utilisation de la `$backendCSName` variable pour le nom de groupe de ressources, et `$location` de la région d’Azure.

        azure service create --serviceName $backendCSName \
            --location $location

2. Créez un compte de stockage de prime pour les disques du système d’exploitation et les données devant être utilisé par la vôtre, les ordinateurs virtuels.

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>Étape 3 - créer des ordinateurs virtuels avec plusieurs cartes réseau

1. Démarrer une boucle pour créer plusieurs ordinateurs virtuels, selon les `numberOfVMs` les variables.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Pour chaque machine virtuelle, spécifiez le nom et l’adresse IP de chacun des deux cartes réseau.

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. Créer la machine virtuelle. Notez l’utilisation de la `--nic-config` paramètre, contenant une liste de toutes les cartes réseau avec l’adresse IP, du sous-réseau et nom.

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Pour chaque machine virtuelle, créez deux disques de données.

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>Étape 4 - exécution du script

Maintenant que vous avez téléchargé et modifié le script en fonction de vos besoins, exécutez le script pour créer des ordinateurs virtuels de base de données le back-end avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de votre terminal **Bash** . Vous verrez la sortie initiale, comme illustré ci-dessous.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Après quelques minutes, l’exécution s’arrête et le reste de la sortie s’affiche comme illustré ci-dessous.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
