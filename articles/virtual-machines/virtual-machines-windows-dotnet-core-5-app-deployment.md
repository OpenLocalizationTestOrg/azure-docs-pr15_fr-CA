<properties
   pageTitle="Automatisation du déploiement d’applications avec les Extensions de la Machine virtuelle | Microsoft Azure"
   description="Didacticiel de DotNet Core Azure VM"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Déploiement d’applications avec modèles du Gestionnaire de ressources Azure

Une fois que toutes les exigences d’infrastructures Azure ont été identifiés et convertis en un modèle de déploiement, le déploiement d’une application réelle doit être résolu. Déploiement d’application fait référence à réinstaller les binaires de l’application réelle sur les ressources d’Azure. Pour l’exemple de magasin de musique, .net Core et IIS doivent être installés et configurés sur chaque machine virtuelle. Les fichiers binaires du magasin de musique doivent être installés sur l’ordinateur virtuel et la base de données de magasin de musique créés à l’avance.

Ce document décrit en détail comment les extensions de la Machine virtuelle peuvent automatiser le déploiement d’applications et la configuration des machines virtuelles Azure. Toutes les dépendances et les configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail avec le modèle de gestionnaire de ressources Azure. Le modèle terminé se trouve ici – [Déploiement du magasin de musique sous Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows).

## <a name="configuration-script"></a>Script de configuration

Extensions de la Machine virtuelle sont des programmes spécialisés qui s’exécutent sur des ordinateurs virtuels pour fournir l’automatisation de la configuration. Les extensions sont disponibles dans des buts spécifiques tels que les antivirus, la configuration de la journalisation et configuration de Docker. L’extension de Script personnalisé peut être utilisée pour exécuter un script sur une machine virtuelle. Dans l’exemple de magasin de musique, il revient à l’extension de script personnalisé pour configurer les ordinateurs virtuels de Windows et installez l’application magasin de musique.

Avant d’en détaillant comment les extensions de la machine virtuelle sont déclarées dans un modèle de gestionnaire de ressources Azure, examinez le script qui est exécuté. Ce script configure la machine virtuelle Windows pour héberger l’application magasin de musique. Lors de l’exécution, le script installe tous les nécessaires logiciel, installer l’application magasin de musique à partir du contrôle de code source et de préparer la base de données. 

> Cet exemple est destiné à des fins de démonstration.

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extension de Script de machine virtuelle

Extensions de la machine virtuelle peut être exécutées sur une machine virtuelle au moment de la génération en incluant la ressource d’extension dans le modèle de gestionnaire de ressources Azure. L’extension peut être ajoutée avec l’Assistant Visual Studio ajouter une ressource, ou en insérant un JSON valide dans le modèle. La ressource de Script Extension est imbriquée à l’intérieur de la ressource d’ordinateur virtuel ; Ceci peut être observé dans l’exemple suivant.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Extension de Script de machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Notez que, dans le sous JSON que le script est stocké dans GitHub. Ce script peut également être stocké dans le stockage des objets Blob Azure. En outre, les modèles de Azure le Gestionnaire de ressources permettent de la chaîne d’exécution de script à être construit tel que les valeurs des paramètres de modèle peuvent être utilisés comme paramètres pour l’exécution du script. Dans ce cas les données sont fournies lorsque vous déployez les modèles, et ces valeurs peuvent ensuite être utilisées lors de l’exécution du script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Pour plus d’informations sur l’utilisation de l’extension de script personnalisé, reportez-vous à la section [extensions de script personnalisé avec les modèles du Gestionnaire de ressources](./virtual-machines-windows-extensions-customscript.md).

## <a name="next-step"></a>Étape suivante

<hr>

[Explorez plus Azure modèles du Gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates)
