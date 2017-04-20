<properties 
    pageTitle="Environnement de test de simulation hybride nuage | Microsoft Azure" 
    description="Créer un environnement de cloud hybride simulé pour IT pro ou tests de développement, à l’aide de deux réseaux virtuels Azure et une connexion VNet-à-VNet." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configurer un environnement de cloud hybride simulée pour les tests

Cet article vous guide dans le processus de création d’un environnement de cloud hybride simulé avec Microsoft Azure à l’aide de deux réseaux virtuels Azure. Voici la configuration obtenue.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Cela simule un environnement de production de cloud hybride et se compose de :

- Un réseau simulé et simplifié sur site hébergé dans un réseau virtuel Azure (le réseau virtuel du labo de test).
- Un réseau virtuel simulé coexistence hébergé dans Azure (TestVNET).
- Connexion entre les deux réseaux virtuels VNet-à-VNet.
- Un contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cela fournit qu'une base ou un démarrage commun point à partir duquel vous pouvez :

- Développer et tester des applications dans un environnement de cloud hybride simulé.
- Créer des configurations de test des ordinateurs, certains au sein du réseau virtuel du labo de test et d’autres au sein du réseau virtuel TestVNET, pour simuler des charges de travail informatiques de nuage hybride.

Il existe quatre principales étapes pour configurer cet environnement de test de cloud hybride :

1.  Configurer le réseau virtuel de labo de test.
2.  Créez le réseau virtuel de coexistence.
3.  Créer la connexion VPN de VNet-à-VNet.
4.  Configurer DC2. 

Cette configuration nécessite un abonnement Azure. Si vous avez un abonnement MSDN ou Visual Studio, reportez-vous à la section [crédit mensuel Azure pour les abonnés de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Ordinateurs virtuels et des passerelles de réseau virtuel dans Azure entraînent un coût en cours lors de l’exécution. Ce coût est facturé par rapport à votre MSDN ou payants. Une passerelle VPN d’Azure est implémentée comme un ensemble de deux machines virtuelles Azure. Pour réduire les coûts, créer l’environnement de test et réalisez votre nécessaires test et démonstration aussi rapidement que possible.

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Phase 1 : Configurer le réseau virtuel de labo de test

Suivez les instructions dans la rubrique de [l’environnement de test de Base de Configuration](https://technet.microsoft.com/library/mt771177.aspx) pour configurer les ordinateurs DC1, APP1 et CLIENT1 dans le réseau virtuel Azure nommé labo de test. 

Ensuite, démarrez une invite de PowerShell d’Azure.

> [AZURE.NOTE] La commande suivante définit l’utilisation Azure PowerShell 1.0 et versions ultérieures.

Connectez-vous à votre compte.

    Login-AzureRMAccount

Obtenir le nom de votre abonnement à l’aide de la commande suivante.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure. Utilisez le même abonnement que vous avez utilisé pour créer votre configuration de base à la Phase 1. Remplacez tout entre guillemets, y compris la < et > caractères, avec le nom correct.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Ensuite, ajoutez un sous-réseau passerelle au réseau virtuel labo de test de votre configuration de base, qui sera utilisée pour héberger la passerelle Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Ensuite, demandez une adresse IP publique pour affecter à la passerelle pour le réseau virtuel de labo de test.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Ensuite, créez votre passerelle.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Gardez à l’esprit que les nouvelles passerelles peuvent prendre 20 minutes ou plus pour créer.

À partir du portail Azure sur votre ordinateur local, connectez à DC1 avec les informations d’identification de CORP\User1. Pour configurer le domaine CORP afin que les utilisateurs et les ordinateurs utilisent leur contrôleur de domaine local pour l’authentification, exécuter ces commandes à partir d’une invite de commande de Windows PowerShell de niveau administrateur sur DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## <a name="phase-2-create-the-testvnet-virtual-network"></a>Phase 2 : Créer le réseau virtuel TestVNET

Tout d’abord, créez le réseau virtuel TestVNET et protégez-le avec un groupe de sécurité du réseau.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Ensuite, demandez une adresse IP publique à allouer à la passerelle pour le réseau virtuel TestVNET, créer votre passerelle.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##<a name="phase-3-create-the-vnet-to-vnet-connection"></a>Phase 3 : Créer la connexion VNet à VNet

Tout d’abord, obtenir une clé prépartagée aléatoire et forte, 32 caractères auprès de votre administrateur réseau ou de sécurité. Vous pouvez également utiliser les informations à [créer une chaîne aléatoire pour une clé pré-partagée IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) pour obtenir une clé prépartagée.

Ensuite, utilisez ces commandes pour créer la connexion VPN de VNet-à-VNet, qui peut prendre du temps.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Après quelques minutes, la connexion doit être établie.

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## <a name="phase-4-configure-dc2"></a>Phase 4 : Configurer DC2

Tout d’abord, créez un ordinateur virtuel pour DC2. Exécuter ces commandes à l’invite de commande PowerShell de Azure sur votre ordinateur local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, vous connecter à la nouvelle machine virtuelle DC2 à partir du portail Azure.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d’une invite de commande de Windows PowerShell de niveau administrateur sur DC2, exécutez ces commandes.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

Quatre réponses réussies à partir de l’adresse 10.0.0.4 doit entraîner la commande ping. Ceci est un test de trafic sur la connexion VNet-à-VNet.

Ensuite, ajoutez le disque de données supplémentaires sur DC2 en tant que nouveau volume avec la lettre de lecteur F:.

1.  Dans le volet gauche du Gestionnaire de serveur, cliquez sur **fichier et Services de stockage**, puis cliquez sur **les disques**.
2.  Dans le volet de contenu, dans le groupe de **disques** , cliquez sur **le disque 2** (avec la **Partition de** valeur **inconnue**).
3.  Cliquez sur **tâches**, puis cliquez sur **Nouveau Volume**.
4.  Sur l’avant vous commencer de la page de l’Assistant Nouveau Volume, cliquez sur **suivant**.
5.  Sur la page Sélectionner le serveur et le disque, cliquez sur le **disque 2**, puis cliquez sur **suivant**. Lorsque vous y êtes invité, cliquez sur **OK**.
6.  Dans la zone spécifier la taille de la page de volume, cliquez sur **suivant**.
7.  Sur l’attribuer à une page de lettre ou d’un dossier du lecteur, cliquez sur **suivant**.
8.  Dans la page Paramètres de système de fichier, cliquez sur **suivant**.
9.  Dans la page Confirmer les sélections, cliquez sur **créer**.
10. Lorsque vous avez terminé, cliquez sur **Fermer**.

Ensuite, configurez DC2 comme un contrôleur de domaine de réplica pour le domaine corp.contoso.com. Exécuter ces commandes à partir de l’invite de commande Windows PowerShell sur DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Notez que vous êtes invité à fournir le mot de passe de CORP\User1 et un mot de passe du Mode de restauration des Services annuaire (DSRM) et redémarrez DC2.

Maintenant que le réseau virtuel TestVNET possède son propre serveur DNS (DC2), vous devez configurer le réseau virtuel de TestVNET pour utiliser ce serveur DNS.

1.  Dans le volet gauche du portail Azure, cliquez sur l’icône de réseaux virtuels, puis cliquez sur **TestVNET**.
2.  Dans l’onglet **paramètres** , cliquez sur **serveurs DNS**.
3.  Dans **serveur DNS principal**, tapez **192.168.0.4** pour remplacer 10.0.0.4.
4.  Cliquez sur **Enregistrer**.

Il s’agit de votre configuration actuelle. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
Votre environnement en nuage hybride simulé est maintenant prêt à être testé.

## <a name="next-step"></a>Étape suivante

- Définir une [ligne de basée sur le web, d’applications d’entreprise](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) dans cet environnement.
