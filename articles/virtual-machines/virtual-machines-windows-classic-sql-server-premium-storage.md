<properties
    pageTitle="Utilisez le stockage de prime Azure avec SQL Server | Microsoft Azure"
    description="Cet article utilise les ressources créées avec le modèle classique de déploiement et fournit des conseils sur l’utilisation du stockage de prime Azure avec SQL Server en cours d’exécution sur Azure Virtual Machines."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilisez le stockage de prime Azure avec SQL Server sur des Machines virtuelles


## <a name="overview"></a>Vue d’ensemble

[Le stockage Azure Premium](../storage/storage-premium-storage.md) est la nouvelle génération de stockage qui offre une faible latence et un débit élevé d’e/s. Il convient le mieux pour la clé d’e/s charges de travail intensives, telles que SQL Server sur des [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/)IaaS.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Cet article fournit des conseils de planification et de la migration d’un ordinateur virtuel exécutant SQL Server d’utiliser le stockage de la prime. Cela inclut Azure infrastructure (réseau, stockage) et les étapes de Windows VM guest. Dans l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) montre une migration complète de bout en bout de la façon de déplacer des machines virtuelles de plus à tirer parti de l’amélioration du stockage SSD local avec PowerShell.

Il est important de comprendre le processus de bout en bout d’utilisant le stockage Azure Premium avec SQL Server sur des machines virtuelles de IAAS. Cela inclut :

- Identification des conditions requises pour utiliser le stockage de la prime.
- Exemples de déploiement de SQL Server sur IaaS au stockage de prime pour les nouveaux déploiements.
- Exemples de migration déploiements existants, les serveurs autonomes et les déploiements à l’aide de SQL toujours sur les groupes de disponibilité.
- Approches de migration possibles.
- Exemple complet de bout en bout illustrant les étapes Azure, Windows et SQL Server pour la migration d’une implémentation existante toujours actif.

Pour plus d’informations sur SQL Server dans Azure Virtual Machines, consultez [SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

**Auteur :** Sol de Daniel **réviseurs techniques :** Luis Carlos Vargas hareng, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Lefaure.

## <a name="prerequisites-for-premium-storage"></a>Conditions requises pour le stockage de la prime

Il existe plusieurs conditions préalables pour l’utilisation du stockage de la prime.

### <a name="machine-size"></a>Taille de l’ordinateur

Pour utiliser le stockage de prime, vous devrez utiliser la gamme DS series de Machines virtuelles (VM). Si vous n’avez pas utilisé les ordinateurs de la gamme DS Series dans votre service cloud avant, vous devez supprimer l’ordinateur virtuel existant, conserver les disques attachés et puis créer un nouveau service en nuage avant de recréer la machine virtuelle comme taille de rôle DS *. Pour plus d’informations sur la taille de la Machine virtuelle, voir [l’ordinateur virtuel et des tailles de Service Cloud pour Azure](virtual-machines-linux-sizes.md).

### <a name="cloud-services"></a>Services en nuage

Vous ne pouvez utiliser DS * machines virtuelles avec le stockage de la prime que lorsqu’ils sont créés dans un service en nuage. Si vous utilisez SQL Server Always On dans Azure, toujours sur récepteur fait référence à l’adresse Azure interne ou externe IP d’équilibrage de charge qui est associé à un service en nuage. Cet article se concentre sur la migration tout en conservant la disponibilité de ce scénario.

> [AZURE.NOTE] Une série DS * doit être la première machine virtuelle qui est déployée sur le nouveau Service de Cloud.

### <a name="regional-vnets"></a>VNETS régional

Pour les machines virtuelles DS *, vous devez configurer le réseau (VNET virtuel) qui héberge vos ordinateurs virtuels pour être régionaux. Cette « s’étend » le VNET est de permettre à être mis en service dans les autres clusters et autoriser la communication entre les ordinateurs virtuels plus grandes. Dans la capture d’écran suivante, l’emplacement mis en surbrillance indique VNETs régionaux, alors que le premier résultat montre un VNET « étroite ».

![RegionalVNET][1]

Vous pouvez déclencher un ticket de support Microsoft pour migrer vers un VNET régionaux, Microsoft va apporter une modification, puis pour effectuer la migration vers VNETs régionales, modifier la propriété AffinityGroup dans la configuration du réseau. Tout d’abord exporter la Configuration du réseau dans PowerShell et remplacer la propriété **AffinityGroup** dans l’élément **VirtualNetworkSite** avec une propriété de **l’emplacement** . Spécifier `Location = XXXX` où `XXXX` est une région d’Azure. Puis importez la nouvelle configuration.

Par exemple, en tenant compte de la configuration de VNET suivante :

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Pour déplacer ce à un VNET régionaux en Europe de l’ouest, modifier la configuration pour les éléments suivants :

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Comptes de stockage

Vous devez créer un nouveau compte de stockage qui est configuré pour le stockage de la prime. Notez que l’utilisation du stockage de prime est définie au niveau du compte de stockage, pas sur les disques durs virtuels individuels, mais lors de l’utilisation d’une machine virtuelle DS * série, vous pouvez attacher du VHD à partir de comptes de prime et de stockage Standard. Vous pouvez envisager cela si vous ne souhaitez pas placer le disque dur virtuel de système d’exploitation au compte de stockage de la prime.

La commande **New-AzureStorageAccountPowerShell** suivante avec le **Type** de « Premium_LRS » crée un compte de stockage Premium :

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Paramètres de Cache de disques durs virtuels

La principale différence entre la création des disques qui font partie d’un compte de stockage de la prime est le paramètre de cache disque. Pour les disques de volume de données de SQL Server, il est recommandé d’utiliser «**La mise en cache de lecture**». Pour les volumes de journal de Transaction, le paramètre de cache disque doit indiquer '**None**'. Cela est différent de recommandations pour les comptes de stockage Standard.

Une fois que les disques durs virtuels ont été joints, le paramètre de cache ne peut pas être modifié. Vous devez détacher et rattacher le VHD avec un paramètre de cache mis à jour.

### <a name="windows-storage-spaces"></a>Espaces de stockage Windows

Vous pouvez utiliser des [Espaces de stockage de Windows](https://technet.microsoft.com/library/hh831739.aspx) comme vous le faisiez avec le stockage Standard précédent, cela vous permettra de migrer un ordinateur virtuel qui utilise déjà des espaces de stockage. L’exemple [l’appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (étape 9 et suivant) montre le code Powershell pour extraire et importer un ordinateur virtuel avec plusieurs disques durs virtuels reliés.

Pools de stockage ont été utilisés avec un compte de stockage Azure Standard pour l’amélioration du débit et de réduire la latence. Vous pouvez trouver la valeur dans le test des Pools de stockage avec un stockage de prime pour les nouveaux déploiements, mais ils ajoutez une complexité supplémentaire avec le programme d’installation de stockage.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Comment trouver la carte Azure des disques virtuels à des pools de stockage

Qu’il existe des recommandations de cache différent pour les disques durs virtuels attachés, vous pouvez décider de copier les disques durs virtuels à un compte de stockage de la prime. Toutefois, lorsque vous les rattacherez la série DS de machine virtuelle, vous devrez peut-être à modifier les paramètres de cache. Il est plus simple d’appliquer la prime de stockage recommandée des paramètres de cache lorsque vous avez des disques durs virtuels distincts pour les fichiers de données de SQL et les fichiers journaux (plutôt qu’un disque dur virtuel unique qui contient à la fois).

> [AZURE.NOTE] Si vous avez des fichiers de données et de journaux de SQL Server sur le même volume, l’option de mise en cache que vous choisissez dépend des modèles d’accès aux e/s pour vos charges de travail de base de données. Test seulement peut démontrer l’option mise en cache est plus adaptée à ce scénario.

Toutefois, si vous utilisez des espaces de stockage de Windows qui sont constitués de plusieurs disques durs virtuels vous devrez examiner vos scripts d’origine pour identifier qui joint les disques durs virtuels sont dans le pool spécifique, donc vous pouvez ensuite définir les paramètres de cache en conséquence pour chaque disque.

Si vous ne disposez pas d’un script d’origine disponible pour afficher les disques durs virtuels mappés au pool de stockage, vous pouvez utiliser les étapes suivantes pour déterminer le mappage de pool de stockage sur disque /.

Pour chaque disque, procédez comme suit :

1. Obtenir la liste des disques associés à une machine virtuelle avec la commande **Get-AzureVM** :

    Get-AzureVM - ServiceName <servicename> -nom de <vmname> | Get-AzureDataDisk

1. Notez le Diskname et les LUN.

    ![DisknameAndLUN][2]

1. Bureau à distance dans la machine virtuelle. Passez à la **Gestion de l’ordinateur** | **Le Gestionnaire de périphériques** | **les lecteurs de disque**. Examinez les propriétés de chacun des « disques virtuels Microsoft »

    ![VirtualDiskProperties][3]

1. Ce numéro de LUN est une référence à un numéro de LUN que vous spécifiez lorsque vous connectez le disque dur virtuel pour la machine virtuelle.
1. Pour le « disque virtuel Microsoft », consultez l’onglet **Détails** , puis dans la liste **propriété** , atteindre la **Clé du pilote**. Dans la zone **valeur**, notez le **décalage**, lequel 0002 dans la capture d’écran suivante. Le 0002 indique le PhysicalDisk2 qui fait référence à du pool de stockage.

    ![VirtualDiskPropertyDetails][4]

2. Pour chaque pool de stockage, vider les disques associés :

    Get-StoragePool FriendlyName - AMS1pooldata | Get-disque physique

    ![GetStoragePool][5]

Vous pouvez désormais utiliser ces informations pour associer joint disques durs virtuels sur des disques physiques dans des Pools de stockage.

Une fois que vous avez mappé les disques durs virtuels sur des disques physiques dans des Pools de stockage, vous pouvez détacher, puis les copier sur un compte de stockage de la prime, puis les joindre avec le paramètre approprié. Consultez l’exemple dans l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), les étapes 8 à 12. Ces étapes montrent comment extraire une configuration de disque attaché à la machine virtuelle de disque dur virtuel à un fichier CSV, copiez les VHD, modifier les paramètres de cache de configuration de disque et enfin redéployer la machine virtuelle sous la forme d’une série DS VM avec tous les disques attachés.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>La bande passante du stockage de machine virtuelle et le débit de stockage de disque dur virtuel

Le montant des performances de stockage dépend de la taille de la VM DS * spécifiée et les tailles de disque dur virtuel. Les ordinateurs virtuels ont différentes allocations pour le nombre de disques durs virtuels qui peut être liée et la bande passante maximale qu’ils prendront en charge (Mo/s). Pour les numéros de bande passante spécifique, voir les [tailles de Service de Cloud pour Azure et de la Machine virtuelle](virtual-machines-linux-sizes.md).

Augmentation IOPS sont obtenus avec des tailles de disque supérieures. Pensez à ceci lorsque vous réfléchissez à votre chemin de migration. Pour plus d’informations, [consultez le tableau pour l’e/s et les Types de disques](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

Enfin, pensez que les ordinateurs virtuels ont bandes passantes d’autre disque maximale qu’ils prendront en charge pour tous les disques connectés. Sous une charge élevée, vous pourriez saturer la bande passante du disque maximal disponible pour cette taille de rôle VM. Par exemple un Standard_DS14 prendra en charge jusqu'à 512 Mo/s ; Par conséquent, avec trois disques de P30 vous pourriez saturer la bande passante du disque de la machine virtuelle. Mais, dans cet exemple, la limite de débit pourrait être dépassée en fonction de la combinaison de lecture et d’écriture d’e/s.

## <a name="new-deployments"></a>Nouveaux déploiements

Les deux sections suivantes montrent comment vous pouvez déployer des machines virtuelles de SQL Server pour le stockage de la prime. Comme mentionné précédemment, il est nécessairement inutile de placer le disque du système d’exploitation vers un stockage de prime. Vous pouvez choisir de le faire si vous avez l’intention de placer des charges de travail intensives d’e/s sur le disque dur virtuel du système d’exploitation.

Le premier exemple montre l’utilisation d’Images de la galerie Azure existante. Le deuxième exemple montre comment utiliser une image de machine virtuelle personnalisée dont vous disposez d’un compte de stockage Standard existant.

> [AZURE.NOTE] Ces exemples supposent que vous avez déjà créé un VNET régional.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Créer une nouvelle machine virtuelle avec stockage Premium avec photo de la galerie

L’exemple ci-dessous montre comment placer le disque dur virtuel de système d’exploitation vers un stockage de prime et de joindre des disques durs virtuels de stockage Premium. Toutefois, vous pouvez également placer le disque du système d’exploitation dans un compte de stockage Standard et puis attacher des disques durs virtuels qui résident dans un compte de stockage de la prime. Les deux scénarios sont expliqués.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Étape 1 : Créer un compte de stockage Premium


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Étape 2 : Créer un nouveau Service de Cloud

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Étape 3 : Réserver un VIP de Service Cloud (facultatif)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Étape 4 : Créer un conteneur de machine virtuelle
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Étape 5 : Placement OS VHD sur Standard ou Premium stockage
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Étape 6 : Créer la machine virtuelle
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Créer une nouvelle machine virtuelle pour utiliser le stockage de prime avec une image personnalisée

Ce scénario montre où vous avez des images personnalisées existantes qui résident dans un compte de stockage Standard. Vous devez copier l’image qui existe dans le compte de stockage Standard et de les transférer à une prime de stockage avant de pouvoir être utilisée comme indiqué si vous souhaitez placer le disque dur virtuel du système d’exploitation sur le stockage de la prime. Si vous avez une image sur le site, vous pouvez également permet de cette méthode pour copier qui directement dans le compte de stockage de la prime.

#### <a name="step-1-create-storage-account"></a>Étape 1 : Créer le compte de stockage
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Étape 2 créer Service Cloud
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Étape 3 : Utiliser une image existante
Vous pouvez utiliser une image existante. Ou bien, vous pouvez [prendre une image d’un ordinateur existant](virtual-machines-windows-classic-capture-image.md). Notez la machine vous image ne doit pas être DS* machine. Une fois que vous avez l’image, les étapes suivantes indiquent comment copier dans le compte de stockage de la prime à la * *AzureStorageBlobCopy-début** applet de commande PowerShell.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Étape 4 : Les Blob de copie entre les comptes de stockage
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Étape 5 : Vérifier régulièrement l’état de la copie :
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Étape 6 : Ajouter un disque de l’Image sur le disque Azure référentiel dans l’abonnement
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] Vous pouvez constater que même si les rapports d’état comme réussie, vous pouvez toujours obtenir une erreur de bail du disque. Dans ce cas, attendez environ 10 minutes.

#### <a name="step-7--build-the-vm"></a>Étape 7 : Créer la machine virtuelle
Vous créez ici la machine virtuelle à partir de votre image et l’attachement de deux disques durs virtuels de stockage Premium :

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Déploiements qui n’utilisent pas toujours sur les groupes de disponibilité

> [AZURE.NOTE] Pour les déploiements existants, voir d’abord la section [conditions préalables](#prerequisites-for-premium-storage) de cette rubrique.

Il existe différentes considérations pour les déploiements SQL Server qui n’utilisent pas toujours sur les groupes de disponibilité et de celles qui le sont. Si vous n’utilisez pas toujours et que vous avez un existant autonome de SQL Server, vous pouvez mettre à niveau vers le stockage de la prime à l’aide d’un nouveau compte de service et de stockage cloud. Envisagez les options suivantes :

- **Créer une nouvelle machine SQL Server virtuelle**. Vous pouvez créer un nouveau SQL Server ordinateur virtuel qui utilise un compte de stockage de la prime, comme indiqué dans les nouveaux déploiements. Puis sauvegarder et restaurer vos bases de données utilisateur et de configuration de SQL Server. L’application devra être mis à jour pour faire référence à la nouvelle de SQL Server si elle accède en interne ou en externe. Vous devez copier tous les objets de 'de la base de données' comme si vous exécutiez une migration côte à côte (SxS) SQL Server. Cela inclut les objets tels que des connexions d’accès, les certificats et les serveurs liés.
- **Migrer une VM de SQL Server existant**. Cette opération nécessite la déconnexion de la machine virtuelle de SQL Server, puis le transférer à un nouveau service en nuage, qui inclut toutes ses disques durs virtuels joints copie pour le compte de stockage de la prime. Lorsque l’ordinateur virtuel est en ligne, l’application référencera le nom d’hôte du serveur comme avant. N’oubliez pas que la taille du disque existant affecte les caractéristiques de performances. Par exemple, un disque de 400 Go obtient arrondi à un P20. Si vous savez que vous ne nécessitent pas de performances de ce disque, puis vous pouvez recréer la machine virtuelle comme une VM de la série DS et attacher des disques durs virtuels de stockage Premium de la spécification de la taille/les performances que vous avez besoin. Puis, vous pouvez détacher et rattacher les fichiers de base de données SQL.

> [AZURE.NOTE] Lors de la copie les disques de disque dur virtuel que vous devez être conscient de la taille, en fonction de la taille signifie que le type de disque de stockage de prime dont ils relèvent, cela détermine les spécifications de performances de disque. Taille de Azure arrondit sur le disque le plus proche, afin que si vous avez un disque de 400 Go, il sera arrondie à un P20. En fonction de vos exigences d’e/s existantes de ce dernier système d’exploitation, vous devrez pas effectuer la migration d’un compte de stockage de la prime.

Si votre SQL Server est accessible en externe, l’adresse IP virtuelle du service de cloud changera. Vous devez également mettre à jour les points de terminaison, ACL et DNS paramètres.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Déploiements existants qui utilisent toujours des groupes de disponibilité

> [AZURE.NOTE] Pour les déploiements existants, voir d’abord la section [conditions préalables](#prerequisites-for-premium-storage) de cette rubrique.

Initialement dans cette section, nous examinerons toujours sur interaction avec Azure réseau. Nous seront puis décomposer migrations dans deux scénarios : migrations où des interruptions de service peuvent être tolérées et migrations où vous devez parvenir à temps d’arrêt minimal.

Local SQL Server toujours sur les groupes de disponibilité utilisez un écouteur local qui enregistre un nom DNS virtuel avec une adresse IP qui est partagée entre un ou plusieurs serveurs SQL. Lorsque les clients se connectent, ils sont acheminés par le biais de l’adresse IP du port d’écoute à la principale de SQL Server. Il s’agit du serveur qui possède la ressource toujours sur IP à ce moment.

![DeploymentsUseAlways sur][6]

Dans Microsoft Azure qu'avoir qu’une seule adresse IP attribuée à une carte réseau sur l’ordinateur virtuel, donc pour atteindre la même couche d’abstraction comme local, Azure utilise l’adresse IP qui est attribuée pour les équilibreurs de charge interne/externe (ILB/ELB). La ressource IP qui est partagée entre les serveurs est définie sur la même adresse IP que le ILB/ELB. Il est publié dans le système DNS et le trafic client est passé par le biais de la ILB/ELB pour le réplica principal de SQL Server. La ILB/ELB sait c'est-à-dire SQL Server principal car il utilise des sondes pour détecter la ressource toujours sur IP. Dans l’exemple précédent, il teste chaque nœud qui a un point de terminaison référencé par la ELB/ILB, selon ce qui répond est le principal SQL Server.

> [AZURE.NOTE] Les ILB et ELB sont affectés à un service particulier Azure cloud, donc aucune migration de nuage dans Azure probablement signifie que l’IP d’équilibrage de la charge change.

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migration toujours sur les déploiements qui peuvent permettre une interruption de service

Il existe deux stratégies pour migrer toujours sur les déploiements qui permettent une interruption de service :

1. **Ajouter plusieurs réplicas secondaires à un toujours sur Cluster existant**
1. **Migration vers un nouveau toujours sur Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Ajoutez plusieurs réplicas secondaire à un toujours sur Cluster existant

Une stratégie consiste à ajouter plus de serveurs secondaires pour la toujours sur le groupe de disponibilité. Vous devez les ajouter dans un nouveau service en nuage et de mettre à jour le port d’écoute avec la nouvelle période d’équilibrage de la charge.

##### <a name="points-of-downtime"></a>Points d’arrêt :

- Validation de cluster.
- Test toujours sur les basculements de nouveaux secondaires.

Si vous utilisez des Pools de stockage de Windows au sein de la machine virtuelle pour le débit d’e/s, puis elles seront déconnectées au cours d’une Validation complète de Cluster. Le test de validation est obligatoire lorsque vous ajoutez des nœuds au cluster. Le temps que nécessaire à l’exécution du test peut varier, donc vous devez le tester dans votre environnement de test représentatif pour obtenir une durée approximative de la durée de cette opération.

Mettre en service les fois où vous pouvez effectuer un basculement sur incident manuel et chaos test sur les nœuds récemment ajoutés pour garantir toujours sur haute disponibilité fonctionne comme prévu.

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] Vous devez arrêter toutes les instances de SQL Server où les Pools de stockage sont utilisés avant l’exécution de la Validation.
##### <a name="high-level-steps"></a>Étapes principales

1. Créez deux nouveaux serveurs SQL dans le service en nuage avec stockage Premium.
1. Copier les sauvegardes complètes et de restauration avec **NORECOVERY**.
1. Copier les objets qui en dépendent, telles que des connexions etc. via 'd’utilisateur DB'.
1. Créer un nouveau interne charge Équilibrage (ILB) ou utiliser un équilibreur de charge externe (ELB) et ensuite configurer les points de terminaison objet d’un équilibrage de charge sur les deux nouveaux nœuds.
> [AZURE.NOTE] Vérifier que tous les nœuds ont la configuration appropriée de point de terminaison avant de continuer

1. Arrêter l’accès utilisateur / d’Application pour le SQL Server (si vous utilisez des Pools de stockage).
1. Arrêter les Services de moteur de SQL Server sur tous les nœuds (si vous utilisez des Pools de stockage).
1. Ajouter de nouveaux nœuds de cluster et exécuter la validation complète.
1. Une fois la Validation est réussie, démarrer tous les Services de SQL Server.
1. Sauvegarde des journaux de transactions et restaurer les bases de données utilisateur.
1. Ajouter de nouveaux nœuds dans le groupe toujours sur disponibilité et placer la réplication **synchrone**.
1. Ajoutez la ressource d’adresse IP de la nouvelle Cloud Service ILB/ELB via PowerShell pour toujours basé sur l’exemple multisite dans l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). Dans Windows clustering, définissez les **propriétaires possibles** de la ressource **Adresse IP** sur les nouveaux nœuds anciens. Consultez la section « Ajout de ressource d’adresse IP sur le même sous-réseau » de l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Basculement vers un des nouveaux nœuds.
1. Vérifiez les nouveaux nœuds basculements de partenaires de basculement automatique et de test.
1. Supprimer des nœuds d’origine du groupe de disponibilité.

##### <a name="advantages"></a>Avantages

- Nouveaux serveurs SQL peut être testé (SQL Server et Application) avant d’être ajoutés sur toujours actif.
- Vous pouvez modifier la taille de la mémoire virtuelle et personnaliser le stockage selon vos besoins. Toutefois, il serait utile de conserver tous les chemins d’accès de fichier SQL.
- Vous pouvez contrôler lorsque le transfert de sauvegardes de la base de données pour les réplicas secondaire sont démarrés. Cela diffère à l’aide d’applet de commande Azure **Début-AzureStorageBlobCopy** pour copier les disques durs virtuels, car il s’agit d’une copie asynchrone.

##### <a name="disadvantages"></a>Inconvénients
- Lorsque vous utilisez des Pools de stockage Windows, il existe des interruptions de service de Cluster lors de la Validation de Cluster complète pour les nouveaux nœuds supplémentaires.
- Selon la Version de SQL Server et au nombre existant de réplicas secondaires, vous est peut-être pas en mesure d’ajouter davantage de réplicas secondaire sans supprimer les serveurs secondaires existants.
- Il peut être long temps de transfert de données SQL lors de la définition des copies secondaires.
- Il existe des coûts supplémentaires lors de la migration alors que vous avez de nouvelles machines en cours d’exécution en parallèle.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migrer vers un nouveau toujours sur Cluster

Une autre stratégie consiste à créer un tout nouveau toujours sur Cluster avec les nouveaux nœuds de service en nuage et puis de rediriger les clients pour l’utiliser.

##### <a name="points-of-downtime"></a>Points d’arrêt

Il est temps d’arrêt lorsque vous transférez des utilisateurs et des applications vers le nouvel écouteur toujours sur. Dépend de la durée d’indisponibilité :

- Le temps nécessaire pour restaurer les sauvegardes du journal des transactions final aux bases de données sur les nouveaux serveurs.
- Le temps nécessaire pour mettre à jour les applications clientes d’utiliser de nouveau toujours sur port d’écoute.

##### <a name="advantages"></a>Avantages

- Vous pouvez tester l’environnement de production réel, SQL Server, et les modifications de build du système d’exploitation.
- Vous pouvez personnaliser le stockage et potentiellement réduire la taille de la machine virtuelle. Cela peut entraîner une réduction des coûts.
- Vous pouvez mettre à jour votre version de SQL Server ou une version au cours de ce processus. Vous pouvez aussi mettre le système d’exploitation.
- La précédente toujours sur Cluster peut agir comme une cible de restauration solide.

##### <a name="disadvantages"></a>Inconvénients

- Vous devez modifier le nom DNS de l’écouteur si vous souhaitez que les deux clusters toujours en cours d’exécution simultanément. Cela ajoute une administration surcharge lors de la migration comme chaînes de l’application client doivent refléter le nouveau nom de port d’écoute.
- Vous devez implémenter un mécanisme de synchronisation entre les deux environnements pour les conserver aussi proche que possible afin de réduire les exigences de synchronisation finale avant la migration.
- Supplémentaire coût lors de la migration avec le nouvel environnement d’exécution.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migration toujours sur les déploiements de temps d’arrêt minimal

Il existe deux stratégies pour les déploiements de toujours de migration pour les temps d’arrêt minimal :

1. **Utiliser un secondaire existant : Site unique**
1. **Utiliser les réplicas secondaires existants : plusieurs sites**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. utiliser un secondaire existant : Site unique

Une stratégie de temps d’arrêt minimal consiste à prendre un nuage existant secondaire et le supprimer de l’actuel service cloud. Copier les disques durs virtuels vers le nouveau compte de stockage de la prime, puis créer la machine virtuelle dans le nouveau service de cloud. Mettre à jour puis de l’écouteur dans la gestion de clusters et le basculement.

##### <a name="points-of-downtime"></a>Points d’arrêt

- Il est temps d’arrêt lorsque vous mettez à jour le nœud final avec le point de terminaison avec équilibrage de charge.
- La reconnexion du client peut être retardée en fonction de votre configuration client/DNS.
- Il est temps d’arrêt supplémentaires si vous choisissez de mettre le groupe toujours sur le Cluster en mode hors connexion pour remplacer les adresses IP. Vous pouvez éviter cela en utilisant une dépendance de l’OR et des propriétaires possibles pour la ressource adresse IP ajoutée. Consultez la section « Ajout de ressource d’adresse IP sur le même sous-réseau » de l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Lorsque vous souhaitez que le nœud ajouté partake dans sous la forme d’un toujours sur partenaire de basculement, vous devez ajouter un point de terminaison Azure avec une référence à l’ensemble d’équilibrage de charge. Lorsque vous exécutez la commande **Add-AzureEndpoint** pour ce faire, les connexions en cours reste ouvertes, mais de nouvelles connexions à l’écouteur ne sera pas en mesure d’être établie tant que l’équilibreur de charge a mis à jour. Dans ce test a été observé au dernier 90-120 secondes, cela doit être testée.

##### <a name="advantages"></a>Avantages

- Sans supplément coûts encourus au cours de la migration.
- Une migration d’un à un.
- Réduction de la complexité.
- Permet une augmentation IOPS de stockage de haut de gamme ont. Lorsque les disques sont détachés de la machine virtuelle et copiés dans le nouveau service de cloud, la partie un 3ème outil peut être utilisé pour augmenter la taille du disque dur virtuel, qui offre des débits plus importants. Pour augmenter la taille du disque dur virtuel, voir ce [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Inconvénients

- Il existe une perte temporaire de la haute disponibilité et reprise après sinistre lors de la migration.
- Comme il s’agit d’une migration de 1:1, vous devrez utiliser une taille de mémoire virtuelle minimale qui prend en charge le nombre de disques durs virtuels, afin que vous ne pourrez pas les versements de vos ordinateurs virtuels.
- Ce scénario utilisez l’applet de commande Azure **Début-AzureStorageBlobCopy** , qui est asynchrone. Il n’y a pas de SLA à la fin de la copie. Le temps des copies varie, cela dépend de l’attente dans la file d’attente qu'il dépend également de la quantité de données à transférer. La durée de la copie augmente si le transfert est destiné à un autre centre de données Azure qui prend en charge le stockage de la prime dans une autre région. Si vous avez seulement 2 nœuds, envisagez une atténuation possibles dans le cas où la copie prend plus de temps que dans le test. Cela peut inclure des idées suivantes.
    - Ajouter un nœud de SQL Server de 3e temporaire pour HA avant la migration de temps convenue.
    - Exécuter la migration en dehors d’Azure maintenance planifiée.
    - Assurez-vous que vous avez correctement configuré le quorum du cluster.  

##### <a name="high-level-steps"></a>Étapes principales

Ce document ne présente pas un exemple complet de bout en bout, mais l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) fournit des informations qui peuvent être exploitées pour effectuer cela.

![MinimalDowntime][8]

- Collecter la configuration du disque et supprimez le nœud (ne supprimez pas les disques durs virtuels reliés).
- Créer un compte de stockage de prime et de copier des disques durs virtuels à partir du compte de stockage Standard
- Créer le nouveau service de cloud et redéployer la VM SQL2 dans ce service de cloud. Créer la machine virtuelle en utilisant le disque dur virtuel d’origine du système d’exploitation copiée et attacher les VHD copiées.
- Configurez ILB / ELB et ajouter des points de terminaison.
- Écouteur de mise à jour par une ou l’autre :
    - Prenant toujours de groupe en mode hors connexion et de mise à jour de la toujours sur port d’écoute avec ILB nouveau / adresse IP de ELB.
    - Ou l’ajout de la ressource d’adresse IP du nouveau Cloud Service ILB/ELB par le biais de PowerShell dans Windows clustering. Puis attribuez les propriétaires possibles de la ressource adresse IP vers le nœud migré, SQL2 et ce en tant que dépendance ou dans le nom de réseau. Consultez la section « Ajout de ressource d’adresse IP sur le même sous-réseau » de l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Vérifier la propagation/configuration de DNS pour les clients.
- Migration de VM de SQL1 et passent par des étapes 2 à 4.
- Si à l’aide de la procédure 5ii, ajoutez SQL1 comme propriétaire Possible de la ressource d’adresse IP ajoutée
- Tester les basculements.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. utiliser les réplicas secondaires existants : plusieurs sites

Si vous avez des nœuds dans plus d’un centre de données Azure (DC) ou si vous disposez d’un environnement hybride, vous pouvez utiliser une configuration toujours dans cet environnement afin de minimiser les interruptions de service.

L’approche consiste à remplacer la synchronisation toujours synchrone pour le local ou contrôleur secondaire de domaine Azure et basculement sur pour que SQL Server. Copier les disques durs virtuels à un compte de stockage de la prime, puis redéployer l’ordinateur dans un service en nuage. Mise à jour de l’écouteur et puis restauré.

##### <a name="points-of-downtime"></a>Points d’arrêt

Le temps d’indisponibilité est constitué par le temps de basculement et les autres DC arrière. Il dépend également de la configuration de votre client/DNS et votre reconnexion du client peut être retardée.
Prenons l’exemple suivant d’une hybride toujours de configuration :

![MultiSite1][9]

##### <a name="advantages"></a>Avantages

- Vous pouvez utiliser l’infrastructure existante.
- Vous avez l’option de mise à niveau avant le stockage Azure sur le contrôleur de domaine de récupération après incident Azure.
- Le stockage du contrôleur de domaine de récupération après incident Azure peut être reconfiguré.
- Il existe un minimum de deux basculements pendant la migration, à l’exclusion des basculements de test.
- Vous n’avez pas besoin de déplacer des données de SQL Server avec la sauvegarde et de restauration.

##### <a name="disadvantages"></a>Inconvénients

- En fonction de l’accès du client à SQL Server, il peut être latence accrue lorsque SQL Server est en cours d’exécution dans un autre contrôleur de domaine à l’application.
- L’heure de la copie de disques durs virtuels pour le stockage de la prime peut être long. Cela peut affecter votre décision sur si vous souhaitez conserver le nœud dans le groupe de disponibilité. Considérez cela lorsque le travail intensif de journal charges sont en cours d’exécution lors de la migration est nécessaire, étant donné que le nœud principal obligé de conserver les transactions non répliquées dans son journal des transactions. Par conséquent, cela peut augmenter considérablement la taille.
- Ce scénario utilisez l’applet de commande Azure **Début-AzureStorageBlobCopy** , qui est asynchrone. Il n’y a pas de SLA à la fin. L’heure de la copie varie, cela dépend de l’attente dans la file d’attente, il dépend également de la quantité de données à transférer. Par conséquent, vous avez juste un seul nœud dans votre centre de données 2, vous devez prendre des mesures d’atténuation dans le cas où la copie prend plus de temps que dans le test. Cela peut inclure des idées suivantes.
    - Ajouter un nœud SQL de 2e temporaire pour HA avant la migration de temps convenue.
    - Exécuter la migration en dehors d’Azure maintenance planifiée.
    - Assurez-vous que vous avez correctement configuré le quorum du cluster.

Ce scénario suppose que vous avez documenté votre installation et connaissez la façon dont le stockage est mappé pour apporter des modifications de paramètres de cache de disque optimales.

##### <a name="high-level-steps"></a>Étapes principales
![Multisite2][10]

- Vérifiez sur site / Azure le contrôleur de domaine de remplacement SQL Server principale et il les autres automatique basculement partenaire (AFP).
- Collecter des informations de configuration de disque à partir de SQL2 et supprimez le nœud (ne supprimez pas les disques durs virtuels reliés).
- Créer un compte de stockage de prime et de copier des disques durs virtuels à partir du compte de stockage Standard.
- Créez un nouveau service en nuage et la machine virtuelle de SQL2 avec ses disques de stockage de primes.
- Configurez ILB / ELB et ajouter des points de terminaison.
- Mettre à jour le toujours sur port d’écoute avec ILB nouveau / ELB IP adresse et testez le basculement.
- Vérifiez la configuration DNS.
- Remplacez le AFP SQL2, puis migrer SQL1 et passent par des étapes 2 à 5.
- Tester les basculements.
- Basculer la AFP SQL1 et SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Appendice : Migration un fonctionnalité Multisite toujours sur le Cluster pour le stockage de la prime

Le reste de cette rubrique fournit un exemple détaillé de la conversion d’un cluster de toujours sur plusieurs sites de stockage de la prime. Il convertit également le port d’écoute d’utiliser un équilibreur de charge externe (ELB) à un équilibreur de charge interne (ILB).

### <a name="environment"></a>Environnement

- Windows 2k 12 / SQL 2k 12
- Fichiers de 1 DB sur SP
- 2 x des Pools de stockage par nœud

![Appendix1][11]

### <a name="vm"></a>MACHINE VIRTUELLE :

Dans cet exemple, nous allons montrer le déplacement d’un ELB à ILB. ELB était disponible avant de ILB, il indique comment passer à cela au cours de la migration.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Étapes de pré : Se connecter à l’abonnement

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Étape 1 : Créer le nouveau compte de stockage et de Service de Cloud
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Étape 2 : Augmenter les pannes autorisées sur les ressources<Optional>
Sur certaines ressources qui appartiennent à votre groupe toujours sur disponibilité est limité dans le nombre d’erreurs qui peut se produire dans une période, dans lequel le service de cluster tente de redémarrer le groupe de ressources. Il est recommandé de qu'augmenter cette tandis que vous parcourez par le biais de cette procédure, depuis si vous n’est pas manuellement les basculements de basculement et de déclenchement par l’arrêt des ordinateurs que vous pouvez rapproche de cette limite.

Il serait prudent de double de l’allocation de la défaillance, pour ce faire dans le Gestionnaire de Cluster de basculement, accédez aux propriétés du groupe de ressources toujours sur :

![Appendix3][13]

Modifier le nombre maximal d’échecs à 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Étape 3 : Ressource d’adresse IP du Addition pour un groupe de clusters<Optional>

Si vous avez uniquement une adresse IP pour le groupe de Cluster, et il est aligné sur le sous-réseau du nuage, attention, si vous prenez accidentellement en mode hors connexion tous les nœuds de cluster dans le nuage sur ce réseau, puis la ressource adresse IP du Cluster et le nom réseau de Cluster ne sera pas mise en ligne. En cas de ce qu’il empêchera les mises à jour vers d’autres ressources de cluster.

#### <a name="step-4-dns-configuration"></a>Étape 4 : Configuration de DNS

Pour implémenter une lisse transition dépend de comment DNS est utilisé et mis à jour.
Toujours est installé, il crée un groupe de ressources de Cluster de Windows, si vous ouvrez le Gestionnaire du Cluster de basculement, vous verrez qu’au moins elle aura trois ressources, les deux le document fait référence à :

- Nom de réseau virtuel (VNN) – il s’agit du nom DNS que le client se connecter à lorsque vous souhaitez vous connecter aux serveurs SQL via toujours.
- Ressource d’adresse IP – il s’agit de l’adresse IP associée à la VNN, vous pouvez avoir plus d’un, et dans une configuration multisite vous dispose d’une adresse IP par site/sous-réseau.

Lors de la connexion à SQL Server, le Client de SQL Server, pilote va récupérer les enregistrements DNS associés à l’écouteur et essayez de vous connecter à chaque toujours sur associé à adresse IP, nous abordons ci-dessous certains facteurs qui peuvent influencer cette.

Le nombre d’enregistrements DNS simultanées qui sont associés au nom du récepteur dépend non seulement le nombre d’adresses IP associé, mais le ' RegisterAllIpProviders'setting dans le Clustering avec basculement de la ressource toujours sur VNN.

Lorsque vous déployez toujours dans Azure différentes étapes pour créer le port d’écoute et les adresses IP, vous devez configurer manuellement le 'RegisterAllIpProviders' à 1, c’est différent pour un déploiement de toujours sur site où elle est déjà définie sur 1.

Si 'RegisterAllIpProviders' est 0, vous verrez uniquement un enregistrement DNS dans le système DNS associé à l’écoute :

![Appendix4][14]

Si 'RegisterAllIpProviders' est 1 :

![Appendix5][15]

Le code ci-dessous vider les paramètres VNN et définissez-la pour vous, veuillez noter, pour que la modification prenne effet, vous devez déconnecter le VNN et mettez-le en ligne, cette prise de l’écouteur en mode hors connexion à l’origine de perturbations de connectivité client.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

Dans une étape ultérieure de migration vous devez mettre à jour le port d’écoute toujours avec une adresse IP mise à jour qui référence un équilibreur de charge, cela implique une suppression de ressources d’adresse IP et l’addition. Après la mise à jour IP, vous devez vous assurer que les clients sont à jour leur cache DNS local et de que la nouvelle adresse IP a été mis à jour dans la Zone DNS.

Si vos clients se trouvent dans un autre segment de réseau et faire référence à un autre serveur DNS, vous devez tenir compte de ce qui se passe sur le transfert de Zone DNS lors de la migration, l’application reconnecter durée sera limitée par au moins le temps de transfert de Zone de toutes les nouvelles adresses IP pour l’écouteur. Si vous êtes sous contrainte de temps ici, vous devez discuter et tester forcer un transfert de zone incrémentiel avec vos équipes de Windows, et également placer l’enregistrement d’hôte DNS pour un faible temps de vie (TTL), les clients de mettre à jour. Pour plus d’informations, reportez-vous à la section [Transferts de Zone incrémentiels](https://technet.microsoft.com/library/cc958973.aspx) et [Début-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Par défaut, la durée de vie pour l’enregistrement DNS qui est associé avec le port d’écoute toujours sur dans Azure est de 1 200 secondes. Vous pouvez souhaiter réduire cela si vous êtes sous contrainte pendant la migration pour les clients mettent à jour leurs DNS avec l’adresse IP mis à jour pour l’écouteur de l’heure. Vous pouvez voir et modifier la configuration par le dumping, la configuration de la VNN :

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Veuillez noter, moins le 'HostRecordTTL', un montant plus élevé du trafic DNS se produit.

##### <a name="client-application-settings"></a>Paramètres de l’application client

Si votre application cliente SQL prend en charge le .net 4.5 SQLClient, puis vous pouvez utiliser ' MULTISUBNETFAILOVER = TRUE' mot clé, il est recommandé à appliquer car elle permet une connexion plus rapide à SQL toujours sur le groupe de disponibilité au cours du basculement. Il énumère toutes les adresses IP associées à l’écouteur toujours en parallèle et effectue une vitesse de nouvelles tentatives de connexion TCP plus agressif lors d’un basculement.

Pour plus d’informations sur les paramètres ci-dessus, veuillez consulter [MultiSubnetFailover mot-clé et les fonctionnalités associées aux](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consultez également la [Prise en charge de SqlClient pour une haute disponibilité, reprise après sinistre](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Étape 5 : Les paramètres du quorum du Cluster

Comme vous vous apprêtez à prendre au moins une de SQL Server vers le bas à la fois, vous devez modifier le paramètre de quorum de cluster, si vous utilisez le fichier partage témoin avec 2 nœuds, vous devez définir le quorum à autoriser le nœud majoritaire et à utiliser le vote dynamique, et c’est pour permettre un seul nœud à rester debout.


    Set-ClusterQuorum -NodeMajority  

Pour plus d’informations sur la gestion et la configuration du quorum du cluster, reportez-vous à [configurer et à gérer le Quorum dans un Cluster de basculement Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Étape 6 : Extraire les ACL et les points de terminaison existants
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Enregistrer dans un fichier texte.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Étape 7 : Modifier les Modes de réplication et de partenaires de basculement

Si vous avez plus de 2 serveurs SQL, vous devez modifier la reprise d’une autre secondaire dans un autre contrôleur de domaine ou sur le site pour « Synchrone » et faites-en un partenaire de basculement sur incident automatique (AFP), il s’agit donc de la mettre à jour HA tandis que vous apportez des modifications. Vous devez pour cela par le biais de TSQL de modifier cependant SSMS :

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Étape 8 : Supprimer les VM secondaire de service cloud

Vous devez être envisagez de migrer un nœud secondaire du nuage tout d’abord, s’il s’agit actuellement principal, vous devez initier un basculement manuel.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Étape 9 : Modifier la mise en cache des paramètres dans le fichier CSV et enregistrer

Pour les volumes de données il doivent indiquer en lecture seule.

Pour les volumes TLOG ces doivent être définies sur NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Étape 10 : Copie les disques durs virtuels
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Vous pouvez vérifier le statut de la copie des disques durs virtuels pour le compte de stockage de prime :

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Attendez que tous ces sont validées avec succès.

Pour plus d’informations pour les objets BLOB individuels :

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Étape 11 : Disque de Registre du système d’exploitation

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Étape 12 : Importer secondaire service en nuage

Le code ci-dessous utilise également l’ajout de l’option ici vous pouvez importer l’ordinateur et utiliser l’adresse IP virtuelle conservables.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Étape 13 : Créer ILB sur Nouveau nuage Svc, ajouter charge équilibrée des points de terminaison et les ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>Étape 14 : Mise à jour de toujours
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Supprimer le service en nuage ancienne adresse IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Étape 15 : Mise à jour DNS vérification

Maintenant Vérifiez les serveurs DNS de vos réseaux de client SQL Server et assurez-vous que le clustering a ajouté l’enregistrement hôte supplémentaire pour l’adresse IP supplémentaire. Si ces serveurs DNS n’ont pas été mis à jour, essayez de forcer un transfert de Zone DNS et vous assurer que les clients d’il sous-réseau sont en mesure de résoudre les deux adresses toujours sur IP, soit vous n’avez pas besoin d’attendre que la réplication DNS automatique.

#### <a name="step-16-reconfigure-always-on"></a>Étape 16 : Reconfigurer toujours

À ce stade vous attendez secondaire ce nœud qui a été migré pour resynchroniser totalement avec le nœud sur site et basculer vers un nœud de réplication synchrone et de la AFP.  

#### <a name="step-17-migrate-second-node"></a>Étape 17 : Migrer le deuxième nœud
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Étape 18 : Modifier la mise en cache des paramètres dans le fichier CSV et enregistrer

Pour les volumes de données il doivent indiquer en lecture seule.

Pour les volumes TLOG ces doivent être définies sur NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Étape 19 : Créer nouveau compte de stockage indépendant pour le nœud secondaire
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Étape 20 : Copier les disques durs virtuels
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Vous pouvez vérifier l’état de copie de disque dur virtuel pour tous les disques durs virtuels avec : ForEach ($disk dans $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. Étiquette de disquette $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Attendez que tous ces sont validées avec succès.

Pour plus d’informations pour les objets BLOB individuels : état de blob induvidual #Check Get-AzureStorageBlobCopyState-Blob « danRegSvcAms-dansqlams1-2014-07-03.vhd »-conteneur $containerName-contexte de $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Étape 21 : Disque de Registre du système d’exploitation
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Étape 22 : Ajouter à charge équilibrée des points de terminaison et les ACL
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Étape 23 : Basculement de Test

Laissez le nœud migré désormais synchroniser avec le nœud toujours sur site, placez-le en mode de réplication synchrone et attendre jusqu'à ce qu’il est synchronisé. Basculement à partir de prem vers le premier nœud migrées, qui est la AFP. Une fois qu’a travaillé, modifier le dernier nœud ayant migré à l’AFP.

Vous devez tester les basculements entre tous les nœuds et exécuté même si les tests de chaos pour garantir des basculements de travail attendu et une manière opportune.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Étape 24 : Remettez les paramètres du quorum du cluster / DNS TTL / Failover Pntrs / paramètres de synchronisation
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Ajout de ressource d’adresse IP sur le même sous-réseau

Si vous avez seulement 2 serveurs de SQL et à les migrer vers un nouveau service en nuage, mais de les conserver sur le même sous-réseau, vous pouvez éviter de prendre l’écouteur en mode hors connexion pour supprimer l’adresse d’origine toujours sur IP et ajouter la nouvelle adresse IP. Si vous migrez les ordinateurs virtuels vers un autre sous-réseau, vous devrez pas cela comme il y a un réseau de cluster supplémentaires qui font référence à ce sous-réseau.

Une fois que vous avez mis le secondaire migré et ajouté à la nouvelle ressource d’adresse IP pour le nouveau service de cloud avant le basculement principal existant, vous devez prendre ces étapes dans le Gestionnaire de basculement de Cluster :

Pour ajouter dans une adresse IP, reportez-vous à l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), étape 14.

1. Pour la ressource d’adresse IP en cours, changer le propriétaire possible « Existant principal SQL Server », dans l’exemple ci-dessous, 'dansqlams4' :

    ![Appendix13][23]

1. Pour la nouvelle ressource d’adresse IP, changer le propriétaire possible pour 'Migrée secondaire SQL Server », dans l’exemple ci-dessous, 'dansqlams5' :

    ![Appendix14][24]

1. Une fois cela défini vous pouvez basculer, et lorsque le dernier nœud est migré les propriétaires possibles doivent être modifiées afin que ce nœud est ajouté en tant que propriétaire Possible :

    ![Appendix15][25]

## <a name="additional-resources"></a>Ressources supplémentaires
- [Stockage de prime Azure](../storage/storage-premium-storage.md)
- [Ordinateurs virtuels](https://azure.microsoft.com/services/virtual-machines/)
- [SQL Server sur des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
