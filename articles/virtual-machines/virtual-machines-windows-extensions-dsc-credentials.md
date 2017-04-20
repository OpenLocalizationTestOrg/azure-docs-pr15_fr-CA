<properties
   pageTitle="En passant les informations d’identification vers Azure avec DSC | Microsoft Azure"
   description="Vue d’ensemble sur le passage d’informations d’identification en toute sécurité Azure des machines virtuelles à l’aide de la Configuration d’état souhaité de PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>En passant les informations d’identification au gestionnaire d’extension Azure DSC #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cet article traite de l’extension de Configuration de l’état souhaité pour Azure. Vous trouverez une vue d’ensemble du Gestionnaire d’extension DSC à [Introduction au gestionnaire d’extension Azure Configuration d’état souhaitée](virtual-machines-windows-extensions-dsc-overview.md). 


## <a name="passing-in-credentials"></a>En passant les informations d’identification
Dans le cadre du processus de configuration, pouvez pour configurer des comptes d’utilisateur, accéder aux services, ou que vous installez un programme dans un contexte utilisateur. Pour effectuer ces opérations, vous devez fournir des informations d’identification. 

DSC permet des configurations paramétrées dans lequel les informations d’identification sont passées dans la configuration et stockées en toute sécurité dans les fichiers MOF. Le Gestionnaire d’Extension Azure simplifie la gestion des informations d’identification grâce à une gestion automatique de certificats. 

Examinez le script de configuration DSC suivant qui crée un compte d’utilisateur local avec le mot de passe :

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Il est important d’inclure un *nœud localhost* dans le cadre de la configuration. Si cette instruction est manquante, les étapes suivantes ne fonctionnent pas comme le Gestionnaire de l’extension recherche spécifiquement l’instruction localhost de nœud. Il est également important d’inclure la conversion de type *[PsCredential]*, comme ce type spécifique déclenche l’extension pour crypter les informations d’identification. 

Ce script de publication pour le stockage des objets blob :

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Définir l’extension DSC d’Azure et fournir les informations d’identification :

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Comment les informations d’identification sont sécurisées
Exécution de ce code demande une information d’identification. Une fois qu’il est fourni, il est stocké en mémoire brièvement. Lorsqu’il est publié avec `Set-AzureVmDscExtension` applet de commande, il est transmis via le protocole HTTPS pour la machine virtuelle, dans le cas où Azure stocke cryptées sur le disque, en utilisant le certificat local de la machine virtuelle. Il est brièvement déchiffré en mémoire, puis crypté pour passer à DSC.

Ce comportement est différent de celui [à l’aide de configurations sécurisées sans le Gestionnaire de l’extension](https://msdn.microsoft.com/powershell/dsc/securemof). L’environnement Azure permet de transmettre des données de configuration en toute sécurité au moyen de certificats. Lorsque vous utilisez le Gestionnaire d’extension DSC, il n’est pas nécessaire de fournir des $CertificatePath ou un $CertificateID / entrée de $Thumbprint dans ConfigurationData.


## <a name="next-steps"></a>Étapes suivantes ##

Pour plus d’informations sur le Gestionnaire d’extension DSC d’Azure, consultez [Introduction au gestionnaire d’extension Azure Configuration d’état souhaitée](virtual-machines-windows-extensions-dsc-overview.md). 

Examinez le [modèle Azure le Gestionnaire de ressources pour l’extension de DSC](virtual-machines-windows-extensions-dsc-template.md).

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Pour trouver des fonctionnalités supplémentaires, vous pouvez gérer avec PowerShell DSC, [parcourez la galerie de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) pour plus de ressources DSC.
