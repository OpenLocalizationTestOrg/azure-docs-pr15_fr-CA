<properties
  pageTitle="Un Service en nuage de se connecter à un contrôleur de domaine personnalisé | Microsoft Azure"
  description="Apprenez à connecter vos rôles web/travail à un domaine Active Directory personnalisée à l’aide de PowerShell et Extension de domaine AD"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Rôles des Services Azure Cloud qui se connecte à un contrôleur de domaine Active Directory hébergé dans Azure personnalisée

Nous allons tout d’abord définir un réseau virtuel (VNet) dans Azure. Nous allons ensuite ajouter un contrôleur de domaine Active Directory (hébergé sur un ordinateur virtuel de Azure) à la VNet. Ensuite, nous ajouter des rôles de service cloud existant à la VNet précréé et ensuite de les connecter au contrôleur de domaine.

Avant de commencer, quelques éléments à garder à l’esprit :

1.  Ce didacticiel utilise PowerShell, par conséquent, vérifiez que vous disposez de PowerShell Azure installé et prêt à l’emploi. Pour obtenir de l’aide sur la configuration d’Azure PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

2.  Les instances de votre contrôleur de domaine Active Directory et le rôle du travailleur/Web doivent être dans le VNet.

Suivez ce guide étape par étape et si vous rencontrez des problèmes, nous laisser un commentaire ci-dessous. Quelqu'un vous contactera pour vous (Oui, nous lisons commentaires).

3. Le réseau qui est référencé par le nuage de service <mark>doit être</mark> un **classique réseau virtuel**.

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Vous pouvez créer un réseau virtuel dans Azure à l’aide de l’Azure portal classique ou PowerShell. Pour ce didacticiel, nous allons utiliser PowerShell. Pour créer un réseau virtuel à l’aide du portail classique Azure, consultez [Créer un réseau virtuel](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Créer un ordinateur virtuel

Une fois que vous avez terminé la configuration du réseau virtuel, vous devrez créer un contrôleur de domaine Active Directory. Pour ce didacticiel, nous configurerons un contrôleur de domaine Active Directory sur un ordinateur virtuel de Azure.

Pour ce faire, créez un ordinateur virtuel via PowerShell en utilisant les commandes ci-dessous :

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promouvoir votre Machine virtuelle à un contrôleur de domaine
Pour configurer l’ordinateur virtuel comme un contrôleur de domaine Active Directory, vous devez ouvrir une session sur l’ordinateur virtuel et le configurer.

Pour vous connecter à la machine virtuelle, vous pouvez obtenir le fichier RDP via PowerShell, utilisez les commandes ci-dessous.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Une fois que vous êtes connecté à la machine virtuelle, configurer votre Machine virtuelle comme un contrôleur de domaine Active Directory en suivant le guide pas à pas comment [configurer votre client de contrôleur de domaine Active Directory](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Ajouter votre Service Cloud au réseau virtuel

Ensuite, vous devez ajouter le déploiement de votre service cloud à le VNet que vous venez de créer. Pour ce faire, modifiez votre cscfg de service cloud en ajoutant les sections correspondantes de votre cscfg à l’aide de Visual Studio ou dans l’éditeur de votre choix.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Ensuite, générez votre projet de services cloud et déployez-le sur Azure. Pour obtenir de l’aide avec le déploiement de votre package de services cloud sur Azure, consultez [comment créer et déployer un Service Cloud](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Connecter votre rôle (s) web/de travail au domaine

Une fois votre projet de service cloud est déployé sur Azure, connectez vos instances de rôles pour le domaine AD personnalisé à l’aide de l’Extension du domaine Active Directory. Pour ajouter l’Extension du domaine de la publicité pour votre déploiement de services de cloud existant et rejoignez le domaine personnalisé, exécutez les commandes suivantes dans PowerShell :

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Et c’est tout.

Cloud services doivent désormais être joint à votre contrôleur de domaine personnalisé. Si vous souhaitez en savoir plus sur les différentes options disponibles pour la configuration d’Extension de domaine Active Directory, utilisez l’aide de PowerShell comme indiqué ci-dessous.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
