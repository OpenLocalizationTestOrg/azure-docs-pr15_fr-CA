<properties
    pageTitle="Déployer des modèles de PowerShell dans la pile d’Azure | Microsoft Azure"
    description="Découvrez comment déployer un ordinateur virtuel à l’aide d’un modèle de gestionnaire de ressources et de PowerShell."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Déployer des modèles de pile Azure à l’aide de PowerShell

Utiliser PowerShell pour déployer des modèles de Azure le Gestionnaire de ressources pour le contrôle du concept pile Azure.  Modèles du Gestionnaire de ressources déploiement et configurer toutes les ressources de votre application dans une opération unique et coordonnée.

## <a name="run-azurerm-powershell-cmdlets"></a>Exécuter les applets de commande PowerShell de AzureRM

Dans cet exemple, vous exécutez un script pour déployer un ordinateur virtuel à POC de pile Azure à l’aide d’un modèle de gestionnaire de ressources.  Avant de continuer, assurez-vous que vous avez [installé et configuré de PowerShell](azure-stack-connect-powershell.md)  

Le disque dur virtuel utilisé dans cet exemple de modèle est une image de marché par défaut (Windows Server 2012-R2-centre de données).

1.  Accédez à <http://aka.ms/AzureStackGitHub>, recherchez le modèle **101-simple-windows-vm** , puis enregistrez-le à l’emplacement suivant : c:\\modèles de\\azuredeploy-101-simple-windows-vm.json.

2.  Dans PowerShell, exécutez le script de déploiement suivantes. Remplacez le *nom d’utilisateur* et le *mot de passe* avec votre nom d’utilisateur et le mot de passe. Dans les utilisations suivantes, incrémentez la valeur pour le paramètre *$myNum* empêcher le remplacement de votre déploiement.

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  Ouvrir le portail Azure pile, cliquez sur **Parcourir**, cliquez sur des **machines virtuelles**et recherchez votre nouvelle machine virtuelle (*myDeployment001*).

## <a name="video-example-hybrid-virtual-machine-deployment"></a>Exemple de vidéo : déploiement d’ordinateur virtuel hybride

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
