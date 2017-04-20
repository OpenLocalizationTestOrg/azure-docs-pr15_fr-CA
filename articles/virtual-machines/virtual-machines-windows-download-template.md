<properties
    pageTitle="Créer une image de machine virtuelle à partir d’un ordinateur virtuel d’Azure | Microsoft Azure"
    description="Apprenez à créer une image généralisée de la machine virtuelle à partir d’une VM Azure existante créée dans le modèle de déploiement du Gestionnaire de ressources"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Télécharger le modèle d’un ordinateur virtuel

Lorsque vous créez un ordinateur virtuel dans Azure à l’aide du portail ou PowerShell, un modèle du Gestionnaire de ressources est automatiquement créé pour vous. Vous pouvez utiliser ce modèle pour dupliquer rapidement un déploiement. Le modèle contient des informations sur toutes les ressources dans un groupe de ressources. Pour une machine virtuelle, cela signifie que les conteneurs de modèles tout ce qui est créé à l’appui de la machine virtuelle dans ce groupe de ressources, y compris les ressources réseau.

## <a name="download-the-template-using-the-portal"></a>Télécharger le modèle à l’aide du portail

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de concentrateur, sélectionnez **Machines virtuelles**.
3. Sélectionnez l’ordinateur virtuel dans la liste.
5. Sélectionnez **script d’Automation**.
6. Sélectionnez **Télécharger** et enregistrer le fichier .zip sur votre ordinateur local.
7. Ouvrez le fichier .zip et extraire les fichiers dans un dossier. Le fichier .zip contient :
    
    - Deploy.ps1
    - Deploy.sh 
    - DEPLOYER.RB
    - DeploymentHelper.cs
    - Parameters.JSON
    - Template.JSON

Le fichier .json est le modèle.
    
## <a name="download-the-template-using-powershell"></a>Télécharger le modèle à l’aide de PowerShell

Vous pouvez également télécharger le fichier de modèle de .json à l’aide de l’applet de commande [Exportation-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) . Vous pouvez utiliser la `-path` paramètre pour fournir le nom de fichier et le chemin d’accès pour le fichier .json. Cet exemple montre comment télécharger le modèle du groupe de ressources nommé **myResourceGroup** dans le dossier **C:\users\public\downloads** sur votre ordinateur local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement des ressources à l’aide de modèles, consultez [procédure pas à pas de gestionnaire de ressources du modèle](../resource-manager-template-walkthrough.md).