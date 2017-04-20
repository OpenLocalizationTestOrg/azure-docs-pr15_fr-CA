<properties
   pageTitle="Besoin de Configuration de l’état pour une vue d’ensemble Azure | Microsoft Azure"
   description="Vue d’ensemble pour l’utilisation du Gestionnaire d’extension Microsoft Azure pour la Configuration d’état souhaité de PowerShell. Y compris les composants requis, d’architecture, d’applets de commande..."
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

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduction au gestionnaire d’extension de Configuration d’état souhaité Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

L’Agent de VM Azure et les Extensions associées font partie des Services d’Infrastructure Microsoft Azure. Extensions de la machine virtuelle sont des composants logiciels qui étendent les fonctionnalités de la machine virtuelle et à simplifient les diverses opérations de gestion de machine virtuelle. Par exemple, l’extension de VMAccess peut être utilisée pour réinitialiser un mot de passe administrateur, ou l’extension de Script personnalisé peut être utilisée pour exécuter un script sur l’ordinateur virtuel.

Cet article présente l’Extension de Configuration d’état souhaité (PowerShell DSC) pour les machines virtuelles d’Azure dans le cadre du Kit de développement PowerShell Azure. Nouvelles applets de commande vous permet de télécharger et d’appliquer une configuration de PowerShell DSC sur un Azure VM activée avec l’extension PowerShell DSC. La DSC de PowerShell extension appelle PowerShell DSC pour mettre la configuration DSC reçue sur l’ordinateur virtuel. Cette fonctionnalité est également disponible via le portail Azure.

## <a name="prerequisites"></a>Conditions préalables ##
**Ordinateur local** Pour interagir avec l’extension Azure VM, vous devez utiliser le portail Azure ou le Kit de développement de PowerShell Azure. 

**Agent d’invité** La machine virtuelle Azure qui sera configuré par la configuration de DSC doit être un système d’exploitation qui prend en charge de Windows Management Framework (WMF) 4.0 ou 5.0. Vous trouverez la liste complète des versions prises en charge de système d’exploitation à l' [Historique de Version d’Extension DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termes et concepts ##
Ce guide suppose que vous connaissez les concepts suivants :

Configuration - il s’agit d’un document de configuration DSC. 

Nœud - il s’agit d’une cible pour une configuration de DSC. Dans ce document, « nœud » désigne toujours un Azure VM.

Configuration - un .psd1 du fichier de données contenant les données d’une configuration de l’environnement

## <a name="architectural-overview"></a>Présentation de l’architecture ##

L’extension de DSC d’Azure utilise l’infrastructure Azure VM Agent doit fournir, d’arrêter et de rapport sur les configurations de DSC sur Azure VMs. L’extension de DSC attend un fichier .zip contenant au moins un document de configuration et un ensemble de paramètres fournis par le biais du Kit de développement de PowerShell Azure ou le portail Azure.

Lorsque l’extension est appelée pour la première fois, elle exécute un processus d’installation. Ce processus installe une version de la (WMF Windows Management Framework) à l’aide de la logique suivante :

1. Si le système d’exploitation de la machine virtuelle Azure est Windows Server 2016, aucune action n’est prise. Windows Server 2016 possède déjà la version la plus récente de PowerShell installé.
2. Si le `wmfVersion` propriété est spécifiée, cette version de la WMF est installée à moins qu’il n’est pas compatible avec le système d’exploitation de l’ordinateur.
3. Si aucune `wmfVersion` propriété est spécifiée, la dernière version applicable de la WMF est installée.

Installation de la WMF requiert un redémarrage. Après le redémarrage, l’extension télécharge le fichier .zip spécifié dans le `modulesUrl` propriété. Si cet emplacement est dans le stockage blob Azure, un jeton SAS peut être spécifié dans le `sasToken` propriété pour accéder au fichier. Une fois le .zip est téléchargé et décompressé, la fonction de configuration définie dans `configurationFunction` est exécuté pour générer le fichier MOF. L’extension s’exécute puis `Start-DscConfiguration -Force` dans le fichier MOF généré. L’extension de capture de sortie et réécrit pour le canal de statut d’Azure. À ce stade, le LCM DSC gère la surveillance et correction comme à l’accoutumée. 

## <a name="powershell-cmdlets"></a>Applets de commande PowerShell ##

Applets de commande PowerShell peut servir avec ARM ou ASM à empaqueter, de publier et de contrôler les déploiements d’extension DSC. Les applets de commande suivantes sont les modules ASM, mais « Azure » peut être remplacé par « AzureRm » pour utiliser le modèle ARM. Par exemple, `Publish-AzureVMDscConfiguration` utilise ASM, où `Publish-AzureRmVMDscConfiguration` utilise ARM. 

`Publish-AzureVMDscConfiguration`prend un fichier de configuration, l’analyse pour les ressources dépendantes DSC et crée un fichier .zip contenant la configuration et les ressources de DSC nécessaires pour appliquer la configuration. Il peut également créer le package localement à l’aide de la `-ConfigurationArchivePath` paramètre. Dans le cas contraire, elle publie le fichier .zip pour le stockage des objets blob Azure et protégez-le avec un jeton d’associations de sécurité.

Le fichier .zip créé par cette applet de commande possède le script de configuration .ps1 à la racine du dossier d’archivage. Les ressources ont le dossier module placé dans le dossier d’archivage. 

`Set-AzureVMDscExtension`injecte les paramètres requis par l’extension de PowerShell DSC dans un objet de configuration de machine virtuelle, qui peut ensuite être appliqué à une VM Azure avec `Update-AzureVM`.

`Get-AzureVMDscExtension`Récupère le statut de l’extension DSC d’un ordinateur virtuel spécifique. 

`Get-AzureVMDscExtensionStatus`extrait l’état de la configuration de DSC édictée par le Gestionnaire d’extension DSC. Cette action peut être effectuée sur une machine virtuelle unique ou un groupe d’ordinateurs virtuels.

`Remove-AzureVMDscExtension`Supprime le Gestionnaire d’extension à partir d’une machine virtuelle donnée. Cette applet de commande ne **pas** supprimer la configuration, désinstallez le WMF ou modifier les paramètres appliqués sur l’ordinateur virtuel. Il supprime uniquement le Gestionnaire d’extension. 

**Principales différences dans les applets de commande ASM et ARM**

- Les applets de commande ARM sont synchrones. Les applets de commande ASM sont asynchrones.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version et l’emplacement sont tous les paramètres requis de nouveau.
- ArchiveResourceGroupName est un paramètre facultatif pour ARM. Vous pouvez spécifier ce paramètre lorsque votre compte de stockage appartient à un groupe de ressources différent que celui dans lequel l’ordinateur virtuel est créé.
- ConfigurationArchive est appelé ArchiveBlobName dans ARM
- ContainerName est appelée ArchiveContainerName dans ARM
- StorageEndpointSuffix est appelé ArchiveStorageEndpointSuffix dans ARM
- Le commutateur de mise à jour automatique a été ajouté à ARM pour activer la mise à jour automatique du Gestionnaire d’extension vers la dernière version en tant qu’et lorsqu’elle est disponible. Ce paramètre est susceptible de provoquer des nRemarquez redémarre sur la machine virtuelle lors de la publication d’une nouvelle version de la WMF. 


## <a name="azure-portal-functionality"></a>Fonctionnalités de portail Azure ##
Accédez à une machine virtuelle classique. Sous Paramètres -> cliquez sur Général, « Extensions ». Un nouveau volet est créé. Cliquez sur « Ajouter » et sélectionnez PowerShell DSC.

Le portail requiert une entrée.
**Configuration de Modules ou Script**: ce champ est obligatoire. Nécessite un fichier .ps1 contenant un script de configuration, ou un fichier .zip avec un script de configuration .ps1 à la racine et toutes les ressources dépendantes dans les dossiers de module dans le .zip. Il peut être créé avec le `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` inclus dans le Kit de développement de PowerShell Azure applet de commande. Le fichier .zip est téléchargé dans votre espace de stockage du blob utilisateur sécurisé par un jeton SAS. 

**Fichier de PSD1 de données de configuration**: ce champ est facultatif. Si votre configuration nécessite un fichier de données de configuration dans .psd1, utilisez ce champ pour le sélectionner et le télécharger dans votre stockage de blob utilisateur, où il est sécurisé par un jeton SAS. 
 
**Nom de la Configuration de Module-Qualified**: fichiers .ps1 peuvent avoir plusieurs fonctions de configuration. Entrez le nom de configuration du script .ps1 suivi d’un «\' et le nom de la fonction de la configuration. Par exemple, si votre script .ps1 a le nom « configuration.ps1 », et que la configuration est « IisInstall », vous entrez :`configuration.ps1\IisInstall`

**Arguments de configuration**: si la fonction configuration accepte des arguments, les entrer ici au format `argumentName1=value1,argumentName2=value2`. Notez que ce format est un format différent de celui de la façon dont accepte les arguments de configuration via les applets de commande PowerShell ou modèles du Gestionnaire de ressources. 

## <a name="getting-started"></a>Mise en route ##

L’extension Azure DSC prend dans les documents de configuration DSC et les active sur Azure VM. Un exemple simple de configuration suit. Enregistrez-le localement sous la forme « IisInstall.ps1 » :

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Les étapes suivantes placer le script IisInstall.ps1 sur l’ordinateur virtuel spécifié, exécuter la configuration et rapport sur l’état.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>Enregistrement dans le journal ##

Les journaux sont placés dans :

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[numéro de Version]

## <a name="next-steps"></a>Étapes suivantes ##

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examinez le [modèle Azure le Gestionnaire de ressources pour l’extension de DSC](virtual-machines-windows-extensions-dsc-template.md
). 

Pour trouver des fonctionnalités supplémentaires, vous pouvez gérer avec PowerShell DSC, [parcourez la galerie de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) pour plus de ressources DSC.

Pour plus d’informations sur le passage de paramètres sensibles dans les configurations, reportez-vous à la section [informations d’identification de gérer en toute sécurité avec le Gestionnaire d’extension DSC](virtual-machines-windows-extensions-dsc-credentials.md).
