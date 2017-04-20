<properties
    pageTitle="Création de jeux d’échelle de Machine virtuelle à l’aide des applets de commande PowerShell | Microsoft Azure"
    description="Commencer la création et la gestion de vos premiers ensembles d’échelle Azure Machine virtuelle à l’aide des applets de commande PowerShell de Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="danielsollondon"/>

# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>Création de jeux d’échelle de Machine virtuelle à l’aide des applets de commande PowerShell

Il s’agit d’un exemple de création d’un Set(VMSS) d’échelle de Machine virtuelle, il crée un VMSS de 3 nœuds, avec tous les associés réseau et de stockage.

## <a name="first-steps"></a>Premières étapes
Vérifiez que vous avez installé le module PowerShell de Azure plus récent, il contiendra les applets de commande PowerShell nécessaires pour mettre à jour et créer des VMSS.
Accédez aux outils de ligne de commande [ici](http://aka.ms/webpi-azps) pour les derniers Modules Azure disponible.

Pour rechercher des VMSS liées applets de commande, utilisez la chaîne de recherche \*VMSS\*.

## <a name="creating-a-vmss"></a>Création d’un VMSS

##### <a name="create-resource-group"></a>Créer le groupe de ressources

```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

##### <a name="create-storage-account"></a>Créer le compte de stockage

Définir le type de compte de stockage / nom.

```
$stoname = 'sto' + $rgname;
$stotype = 'Standard_LRS';
 New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname -Location $loc -SkuName $stotype -Kind "Storage";

$stoaccount = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname;
```

#### <a name="create-networking-vnet--subnet"></a>Créer la mise en réseau (VNET / sous-réseau)

##### <a name="subnet-specification"></a>Spécification de sous-réseau

```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

##### <a name="vnet-specification"></a>Spécification de VNET

```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

#In this case below we assume the new subnet is the only one, note difference if you have one already or have adjusted this code to more than one subnet.
$subnetId = $vnet.Subnets[0].Id;
```

##### <a name="create-public-ip-resource-to-allow-external-access"></a>Créer la ressource d’adresse IP publique pour permettre l’accès externe

Cela est lié à l’à l’équilibreur de charge.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

##### <a name="create-and-configure-load-balancer"></a>Créer et configurer l’équilibrage de la charge

```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

#Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

##### <a name="configure-load-balancer"></a>Configurer l’équilibrage de la charge
Créer la configuration de Pool d’adresse back-end, ce sera partagé par les cartes réseau des ordinateurs virtuels dans VMSS.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Définir le Port de sonde de charge équilibrée, modifiez les paramètres appropriés pour votre application.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Créer des règles NAT pour une connectivité directe routée (pas équilibrée de la charge) pour les ordinateurs virtuels dans le VMSS de via l’équilibreur de charge, Remarque Cela illustre l’utilisation de RDP, il s’agit simplement de démonstration et les méthodes VNET internes doivent être utilisées pour RDP'ing à ces serveurs.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

Créez la charge équilibrée règle, cet exemple montre charge équilibrage port 80 demandes, en utilisant les paramètres des étapes précédentes.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

Créer l’équilibreur de charge avec la configuration.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Vérifiez les paramètres de livres, de vérifier les configurations de port d’équilibrage de la charge, note, vous ne verrez pas les règles de trafic entrant de NAT jusqu'à ce que les machines virtuelles dans le VMSS sont créés.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-vmss"></a>Configurer et créer des VMSS

Notez que cette infrastructure montre comment le programme d’installation, distribuer et mettre à l’échelle du trafic web sur la VMSS, mais les Images d’ordinateurs virtuels spécifiés ici n’ont pas de tous les services web installés.

```
#specify VMSS Name
$vmssName = 'vmss' + $rgname;

##specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

$vhdContainer = "https://" + $stoname + ".blob.core.windows.net/" + $vmssName;

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Lier la carte réseau d’équilibrage de la charge et le sous-réseau

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

Créer le fichier Config VMSS

```
#Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
  	| Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
  	| Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
  	| Set-AzureRmVmssStorageProfile -Name 'test' -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName -VhdContainer $vhdContainer `
  	| Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Générer la configuration VMSS

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Vous avez maintenant créé la VMSS. Vous pouvez tester la connexion de VM individuelle à l’aide du protocole RDP dans cet exemple :

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```
