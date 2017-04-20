<properties
    pageTitle="Connecter votre application à votre réseau virtuel à l’aide de PowerShell"
    description="Instructions sur la façon de se connecter à et utiliser des réseaux virtuels à l’aide de PowerShell"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="wpickett"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="ccompy"/>

# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>Connecter votre application à votre réseau virtuel à l’aide de PowerShell #

## <a name="overview"></a>Vue d’ensemble ##

Dans le Service d’application Azure, vous pouvez connecter votre application (web, mobile ou API) à un réseau virtuel Azure (VNet) dans votre abonnement. Cette fonctionnalité est appelée intégration de VNet. La fonctionnalité d’intégration de VNet ne doit pas être confondue avec la fonctionnalité de l’environnement de Service d’application, qui vous permet d’exécuter une instance du Service d’application Azure dans votre réseau virtuel.

La fonctionnalité d’intégration de VNet a une interface utilisateur (IU) dans le nouveau portail qui vous permet d’intégrer les réseaux virtuels qui sont déployés en utilisant le modèle de déploiement classique ou le modèle de déploiement du Gestionnaire de ressources Azure. Si vous souhaitez en savoir plus sur la fonction, reportez-vous à [intégrer votre application avec un réseau virtuel Azure](web-sites-integrate-with-vnet.md).

Cet article n’est pas sur l’utilisation de l’interface utilisateur, mais plutôt sur la façon d’activer l’intégration à l’aide de PowerShell. Étant donné que les commandes pour chaque modèle de déploiement sont différents, cet article comporte une section pour chaque modèle de déploiement.  

Avant de continuer avec cet article, vérifiez que vous disposez :

- La dernière Azure PowerShell Kit de développement SDK. Vous pouvez installer avec le programme d’installation de la plate-forme Web.
- Une application dans Azure Application Service est en cours d’exécution dans une norme ou un SKU de la prime.

## <a name="classic-virtual-networks"></a>Réseaux virtuels classiques ##

Cette section explique les trois tâches de réseaux virtuels qui utilisent le modèle de déploiement classique :

1. Votre application de se connecter à un réseau virtuel préexistant qui dispose d’une passerelle et est configuré pour la connectivité du site-à-point.
1. Mettre à jour vos informations d’intégration de réseau virtuel pour votre application.
1. Déconnectez votre application du réseau virtuel.

### <a name="connect-an-app-to-a-classic-vnet"></a>Connecter une application à une VNet classique ###

Pour connecter une application à un réseau virtuel, suivez ces trois étapes :

1. Pour l’application web, déclarer qu’elle adhérez à un réseau virtuel particulier. L’application génère un certificat qui sera donné au réseau virtuel pour les connexions de point-à-site.
1. Télécharger le certificat d’application web sur le réseau virtuel et extraire l’URI du package VPN site-à-point.
1. Mettre à jour la connexion de réseau virtuel de l’application web avec l’URI de package de site-à-point.

Les première et la troisième étapes sont entièrement scriptables, mais la seconde étape nécessite une action manuelle via le portail, ou accès à effectuer des actions de **placer** ou **des correctifs** sur le point de terminaison réseau virtuel Azure le Gestionnaire de ressources unique. Contactez le Support Azure pour que cette option est activée. Avant de commencer, assurez-vous que vous disposez d’un réseau virtuel classique qui a une connexion point-à-site déjà activé et une passerelle déployée. Pour créer la passerelle et les connexions point-à-site, vous devez utiliser le portail comme décrit à la [Création d’une passerelle VPN][createvpngateway].

Le réseau virtuel classique doit se trouver dans le même abonnement que votre plan de Service d’application qui contient l’application que vous intégrez avec.

##### <a name="set-up-azure-powershell-sdk"></a>Configurer Azure SDK de PowerShell #####

Ouvrez une fenêtre PowerShell et paramétrer votre compte Azure et votre abonnement à l’aide de :

    Login-AzureRmAccount

Cette commande ouvre une invite de commandes pour obtenir vos informations d’identification Azure. Après que vous être connecté, utilisez une ou l’autre des commandes suivantes pour sélectionner l’abonnement que vous souhaitez utiliser. Assurez-vous que vous utilisez l’abonnement qui se trouvent dans votre plan de Service d’application et de réseau virtuel.

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

ou

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>Variables utilisées dans cet article #####

Pour simplifier les commandes, nous allons définir une variable PowerShell de **$Configuration** avec la configuration spécifique.

Définissez une variable comme suit dans PowerShell avec les paramètres suivants :

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

L’emplacement de l’application doit être l’emplacement sans espaces. Par exemple, les États-Unis Ouest est westus.

    $Configuration.WebAppLocation = "[Your web app Location]"

L’élément suivant est où le certificat doit être écrit. Il doit être un chemin d’accès en écriture sur votre ordinateur local. Veillez à inclure les .cer à la fin.

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Pour voir ce qui a été défini, tapez **$Configuration**.

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

Le reste de cette section suppose que vous disposez d’une variable créée comme vient d’être décrite.

##### <a name="declare-the-virtual-network-to-the-app"></a>Déclarez le réseau virtuel à l’application #####

Utilisez la commande suivante pour indiquer à l’application qu’il utilisera ce réseau virtuel particulier. Cela entraînera l’application générer des certificats nécessaires :

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Si cette commande réussit, **$vnet** doit contenir une variable de **Propriétés** . La variable de **Propriétés** doit contenir à la fois un empreinte numérique du certificat et les données du certificat.

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>Télécharger le certificat d’application web sur le réseau virtuel #####

Un manuel, étape unique est requise pour chaque abonnement et la combinaison de réseaux virtuels. Autrement dit, si vous vous connectez apps abonnement A un de réseau virtuel, vous devez effectuer cette étape qu’une seule fois, quel que soit le nombre d’applications que vous configurez. Si vous ajoutez une nouvelle application à un autre réseau virtuel, vous devez effectuer cette opération à nouveau. La raison à cela est qu’un ensemble de certificats est généré à un niveau d’abonnement dans le Service d’application Azure, et l’ensemble est généré une fois pour chaque réseau virtuel que les applications se connectera.

Les certificats ont déjà été définira si vous avez suivi ces étapes, ou si vous avez intégré avec le même réseau virtuel via le portail.

La première étape consiste à générer le fichier .cer. La deuxième étape consiste à télécharger le fichier .cer à votre réseau virtuel. Pour générer le fichier .cer à partir de l’appel d’API à l’étape précédente, exécutez les commandes suivantes.

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

Le certificat se trouve dans l’emplacement de ce **$Configuration.GeneratedCertificatePath** spécifie.

Pour télécharger le certificat manuellement, utiliser le [portail Azure] [ azureportal] et **Parcourir réseau virtuel (classique)** > **connexions VPN** > **Point-à-site** > **Gérer les certificats**. À partir de là, télécharger votre certificat.

##### <a name="get-the-point-to-site-package"></a>Obtenir le package de point-à-site #####

L’étape suivante de la configuration d’une connexion de réseau virtuel sur une application web consiste à obtenir le package de site-à-point et fournir à votre application web.

Enregistrer le modèle suivant dans un fichier appelé GetNetworkPackageUri.json de quelque part sur votre ordinateur, par exemple, C:\Azure\Templates\GetNetworkPackageUri.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


Définir les paramètres d’entrée :

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

Appeler le script :

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


La variable **$output. Outputs.packageUri** contient maintenant le package URI à donner à votre application web.

##### <a name="upload-the-point-to-site-package-to-your-app"></a>Télécharger le package de site-à-point pour votre application #####

La dernière étape consiste à fournir à l’application de ce package. Simplement, exécutez la commande suivante :

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Si un message vous demande de confirmer que vous remplacez une ressource existante, veillez à lui.

Une fois cette commande réussit, votre application doit désormais être connectée au réseau virtuel. Pour confirmer la réussite, accédez à votre console app et tapez la commande suivante :

    SET WEBSITE_

S’il existe une variable d’environnement appelée WEBSITE_VNETNAME qui a une valeur qui correspond au nom du réseau virtuel cible, toutes les configurations ont réussi.

### <a name="update-classic-vnet-integration-information"></a>Mettre à jour les informations sur l’intégration VNet classiques ###

Pour mettre à jour ou de resynchroniser vos informations, il suffit de répéter les étapes que vous avez suivi lorsque vous avez créé l’intégration en premier lieu. Ces étapes sont les suivantes :

1. Définir les informations de configuration.
1. Déclarez le réseau virtuel à l’application.
1. Obtenir le package de site-à-point.
1. Télécharger le package de site-à-point pour votre application.

### <a name="disconnect-your-app-from-a-classic-vnet"></a>Se déconnecter de votre application à partir d’un VNet classique ###

Pour vous déconnecter de l’application, vous devez les informations de configuration qui a été définies lors de l’intégration de réseau virtuel. À l’aide de ces informations, il est alors une commande pour déconnecter votre application à partir de votre réseau virtuel.

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Le Gestionnaire de ressources des réseaux virtuels ##

Réseaux virtuels de gestionnaire de ressources ont des API du Gestionnaire de ressources Azure, qui simplifient la certains processus lors de la comparaison avec les réseaux virtuels classiques. Nous avons un script qui vous permettra d’effectuer les tâches suivantes :

- Créer un réseau virtuel du Gestionnaire de ressources et d’intégrer votre application.
- Créer une passerelle, configurer la connectivité de point-à-site dans un réseau virtuel préexistant de gestionnaire de ressources et puis intégrer votre application.
- Intégrer votre application avec un réseau virtuel Gestionnaire de ressources existant qui a une passerelle et la connectivité activée point-à-site.
- Déconnectez votre application du réseau virtuel.

### <a name="resource-manager-vnet-app-service-integration-script"></a>Script d’intégration de Service Gestionnaire de ressources VNet App ###

Copiez le script suivant et enregistrez-le dans un fichier. Si vous ne souhaitez pas utiliser le script, n’hésitez pas à obtenir des informations à partir de celui-ci pour savoir comment configurer les choses avec un réseau virtuel du Gestionnaire de ressources.


    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
          else
        {
          Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

Enregistrer une copie du script. Dans cet article, il est appelé V2VnetAllinOne.ps1, mais vous pouvez utiliser un autre nom. Il n’y a pas d’arguments de ce script. Vous suffit de l’exécuter. Commencer par le script est vous invite à vous connecter. Après que vous être connecté, le script obtient des informations sur votre compte et retourne une liste d’abonnements. Sans compter la demande vos informations d’identification, l’exécution du script initial se présente comme suit :

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Abonnement de démonstration (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) Test de MS (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Abonnement de démonstration violet (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Choisissez une option : 3

    Compte : ccompy@microsoft.com environnement : AzureCloud abonnement : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d cliente : 722278f-fef1-499f-91ab-2323d011db47

    Veuillez entrer le groupe de ressources de votre application : hcdemo-rg Veuillez entrer le nom de votre application : v2vnetpowershell que voulez-vous faire ?

    1) Ajouter un nouveau réseau virtuel pour une application
    2) Ajouter un réseau virtuel existant à une application
    3) Supprimer un réseau virtuel à partir d’une application

Le reste de cette section décrit chacun de ces trois options.

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Créer un gestionnaire de ressources de VNet et intégrer ###

Pour créer un nouveau réseau virtuel qui utilise le modèle de déploiement du Gestionnaire de ressources et l’intégrer à votre application, sélectionnez **1) ajouter un nouveau réseau virtuel pour une application**. Cela vous demandera le nom du réseau virtuel. Dans mon cas, comme vous pouvez le voir dans les paramètres suivants, j’ai utilisé le nom de v2pshell.

Le script fournit des précisions sur le réseau virtuel qui est en cours de création. Si je le souhaite, je peux modifier les valeurs. L’exécution de cet exemple, j’ai créé un réseau virtuel qui possède les paramètres suivants :

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

Si vous souhaitez modifier les valeurs, tapez **o** et apportez les modifications. Lorsque vous êtes satisfait avec les paramètres de réseau virtuel, tapez **N** , ou appuyez simplement sur ENTRÉE lorsque vous êtes invité à modifier les paramètres. Problème jusqu'à la fin, le script vous indique des valeurs qu’ils ' est ainsi jusqu'à ce qu’il commence à créer de la passerelle réseau virtuel. Cette étape peut prendre jusqu'à une heure. Il n’y a aucun indicateur de progression au cours de cette phase, mais le script vous informe lorsque la passerelle a été créée.

Lorsque le script se termine, il indique **terminé**. À ce stade, vous aurez un réseau virtuel du Gestionnaire de ressources qui a le nom et les paramètres que vous avez sélectionné. Ce nouveau réseau virtuel est également intégré avec votre application.

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>Intégrer votre application avec un VNet le Gestionnaire de ressources préexistant ###

Lorsque vous êtes intégrant un réseau virtuel préexistant, si vous fournissez un gestionnaire de ressources de réseau virtuel n’ayant pas une passerelle ou un point-à-site connectivité, le script qui définit. Si le VNET est déjà ces éléments de configuration, le script va directement à l’intégration de l’application. Pour démarrer ce processus, il suffit de sélectionner **2) ajouter un réseau virtuel pour une application**.

Cette option fonctionne uniquement si vous avez un réseau virtuel Resource Manager préexistants qui se trouve dans le même abonnement en tant que votre application. Après avoir sélectionné l’option, s’affiche avec une liste de vos réseaux virtuels de gestionnaire de ressources.   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Choisissez une option : 5

Sélectionnez simplement le réseau virtuel que vous souhaitez intégrer. Si vous disposez déjà d’une passerelle qui dispose d’une connexion point-à-site activée, le script s’intégrera simplement votre application avec votre réseau virtuel. Si vous ne disposez pas d’une passerelle, vous devrez spécifier le sous-réseau de passerelle. Votre sous-réseau de passerelle doit être dans votre espace d’adressage de réseau virtuel, et il ne peut pas être dans un autre sous-réseau. Si vous disposez d’un réseau virtuel sans une passerelle et exécutez cette étape, les choses se présenter comme suit :

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

Dans cet exemple, j’ai créé une passerelle réseau virtuel qui possède les paramètres suivants :

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

Si vous souhaitez modifier ces paramètres, vous pouvez le faire. Dans le cas contraire, appuyez sur la touche entrée et le script va créer votre passerelle et attacher votre application à votre réseau virtuel. L’heure de création de passerelle est toujours une heure, cependant, vérifiez que conserver à l’esprit. Lorsque tout est terminé, le script affichera **terminé**.

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>Se déconnecter de votre application à partir d’un gestionnaire de ressources de VNet ###

Se déconnecter de votre application à partir de votre réseau virtuel ne pas arrêter la passerelle ou désactiver la connectivité du site-à-point. Vous pouvez, après tout, utiliser il pour autre chose. Elle aussi ne le déconnecte pas à partir d’autres applications de n’importe quel autre que celui que vous avez fournies. Pour effectuer cette action, sélectionnez **3) supprimer un réseau virtuel à partir d’une application**. Lorsque vous procédez ainsi, vous verrez quelque chose comme ceci :

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Bien que le script indique à SUPPR, il ne supprime pas le réseau virtuel. Il supprime simplement l’intégration. Après avoir confirmé que c’est ce que vous voulez faire, la commande est traitée très rapidement et vous indique **True** lorsqu’il est terminé.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com
