<properties
    pageTitle="Vue d’ensemble du script de déploiement de projet de groupe de ressources Azure | Microsoft Azure"
    description="Décrit comment fonctionne le script PowerShell dans le projet de déploiement de groupe de ressources Azure."
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Vue d’ensemble du script de déploiement de projet de groupe de ressources Azure

Déploiement d’un groupe de ressources Azure projets vous aident à organiser et déployez des fichiers et autres artefacts sur Azure. Lorsque vous créez un projet de déploiement du Gestionnaire de ressources Azure dans Visual Studio, un script PowerShell nommé **Deploy-AzureResourceGroup.ps1** est ajouté au projet. Cette rubrique fournit des détails sur ce que fait ce script et comment l’exécuter à la fois au sein et en dehors de Visual Studio.

## <a name="what-does-the-script-do"></a>Que fait le script ?

Le script de déploiement-AzureResourceGroup.ps1 effectue deux opérations importantes pour le processus de déploiement.

- Télécharger des fichiers ou des éléments nécessaires pour le déploiement du modèle
- Déployer le modèle

La première partie du script transfère les fichiers et les artefacts pour le déploiement et l’applet de commande dernier dans le script de déployer le modèle. Par exemple, si un ordinateur virtuel doit être configuré à l’aide d’un script, le script de déploiement en toute sécurité charge d’abord le script de configuration d’un compte de stockage Azure. Cela rend disponible pour le Gestionnaire de ressources Azure pour configurer l’ordinateur virtuel au cours de la mise en service.

Étant donné que pas de tous les déploiements de modèle ont besoin des artefacts supplémentaires qui doivent être téléchargées, un paramètre de commutateur appelé *uploadArtifacts* est évalué. Si tous les artefacts doivent être téléchargées, définissez le commutateur *uploadArtifacts* lors de l’appel du script. Notez que le fichier de modèle principal et le fichier de paramètres ne doivent être téléchargées. Seuls les autres fichiers, tels que les scripts de configuration, imbriqués de modèles de déploiement et fichiers d’application doivent être téléchargées.

## <a name="detailed-script-description"></a>Description du script détaillé

Voici une description de ce que sélectionner des parties de ne déployer-AzureResourceGroup.ps1 Azure PowerShell script.

>[AZURE.NOTE] Ce document décrit la version 1.0 du script de déploiement-AzureResourceGroup.ps1.

1.  Déclarez des paramètres requis par le projet de déploiement d’Azure le Gestionnaire de ressources. Certains paramètres ont des valeurs par défaut qui ont été définies lorsque le projet a été créé. Vous pouvez modifier ces valeurs par défaut dans le script ou l’ajout de différentes valeurs de paramètre avant d’exécuter le script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|Paramètre|Description|
  	|---|---|
  	|$ResourceGroupLocation|Le données ou la région centre du groupe de ressources, tels que les **États-Unis Ouest** ou **d’Asie de l’est**.|
  	|$ResourceGroupName|Le nom du groupe ressources Azure.|
  	|$UploadArtifacts|Valeur binaire qui indique si les artefacts doivent être téléchargées vers Azure à partir de votre système.|
  	|$StorageAccountName|Le nom de votre compte de stockage Azure où vos artefacts sont téléchargés.|
  	|$StorageAccountResourceGroupName|Le nom du groupe de ressources Azure qui contient le compte de stockage.|
  	|$StorageContainerName|Le nom du conteneur de stockage utilisé pour le téléchargement des artefacts.|
  	|$TemplateFile|Le chemin d’accès pour le fichier de déploiement (`<app name>.json`) dans votre projet de groupe de ressources Azure.|
  	|$TemplateParametersFile|Le chemin d’accès au fichier de paramètres (`<app name>.parameters.json`) dans votre projet de groupe de ressources Azure.|
  	|$ArtifactStagingDirectory|Le chemin d’accès de votre système où artefacts localement téléchargés, y compris le dossier racine du script PowerShell. Ce chemin d’accès peut être absolu ou relatif à l’emplacement du script.|
  	|$AzCopyPath|Le chemin d’accès dans lequel l’outil AzCopy.exe copie ses fichiers .zip, y compris le dossier racine du script PowerShell. Ce chemin d’accès peut être absolu ou relatif à l’emplacement du script.|
  	|$DSCSourceFolder|Le chemin d’accès au dossier source DSC (Configuration de l’état souhaité), y compris le dossier racine du script PowerShell. Ce chemin d’accès peut être absolu ou relatif à l’emplacement du script. Reportez-vous à la section [Présentation de l’extension Azure PowerShell DSC (Configuration de l’état souhaité)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), le cas échéant, pour plus d’informations.|

1.  Vérifier si les artefacts doivent être téléchargés vers Azure. Si ce n’est pas le cas, passez à l’étape 11. Dans le cas contraire, procédez comme suit.

1.  Convertir des variables avec les chemins d’accès relatifs à des chemins d’accès absolus. Par exemple, modifier un chemin d’accès tel que `..\Tools\AzCopy.exe` à `C:\YourFolder\Tools\AzCopy.exe`. En outre, initialisez les variables *ArtifactsLocationName* et *ArtifactsLocationSasTokenName* à null. *ArtifactsLocation* et *SaSToken* peuvent être des paramètres pour le modèle. Si leurs valeurs sont null après la lecture du fichier de paramètres, le script génère des valeurs pour eux.

    Les outils Azure permet le paramètre valeurs *_artifactsLocation* et *_artifactsLocationSasToken* dans le modèle de gestion des artefacts. Si le script PowerShell détecte les paramètres avec ces noms, mais les valeurs de paramètre ne sont pas fournis, le script transfère les artefacts et renvoie les valeurs appropriées pour ces paramètres. Il les passe ensuite à l’applet de commande via `@OptionsParameters`.

  	|Variable|Description|
  	|---|---|
  	|ArtifactsLocationName|Le chemin d’accès où se trouvent les artefacts Azure.|
  	|ArtifactsLocationSasTokenName|Le nom du jeton SAS (Signature de l’accès partagé) qui est utilisé par le script à s’authentifier sur le Bus de Service. Pour plus d’informations, reportez-vous à la section [Authentification de Signature à accès partagé avec Bus de Service](service-bus-shared-access-signature-authentication.md) .|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  Cette section vérifie si les <app name>. parameters.json les fichiers (appelés « le fichier de paramètres ») a un nœud parent des **paramètres** nommé (dans le `else` bloc). Dans le cas contraire, il n’a aucun nœud parent. Un seul format est acceptable.
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  Parcourir la collection de paramètres JSON. Si une valeur de paramètre a été attribuée à *_artifactsLocation* ou *_artifactsLocationSasToken*, puis définissez la variable *$OptionalParameters* avec ces valeurs. Cela empêche le script de remplacer par inadvertance des valeurs de paramètre que vous fournissez.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  Obtenir la clé de compte de stockage et le contexte de la ressource du compte de stockage utilisée pour contenir les artefacts pour le déploiement.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  Si vous utilisez PowerShell DSC pour configurer un ordinateur virtuel, l’extension de DSC nécessite les artefacts dans un fichier zip unique. Par conséquent, créer un fichier d’archive .zip pour la configuration de DSC. Pour ce faire, vérifiez si $DSCSourceFolder existe. Si une configuration de DSC existe, supprimez-la et ensuite créer un nouveau fichier compressé appelé dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  Si aucun chemin d’accès pour les artefacts Azure n’est fourni dans le fichier de paramètres, définissez un chemin d’accès du script PowerShell à utiliser lors du chargement des artefacts. Pour ce faire, créez un chemin d’accès à l’aide d’une combinaison de chemin d’accès du point de terminaison du compte de stockage ainsi que le nom de conteneur de stockage. Puis, mettez à jour le fichier de paramètres avec ce nouveau chemin d’accès.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  Utilisez l’utilitaire **AzCopy** (inclus dans le dossier **d’Outils** de votre projet de déploiement de groupe de ressources Azure) pour copier tous les fichiers à partir de votre chemin de suppression de stockage local à votre compte en ligne de stockage Azure. Si cette étape échoue, quitter le script depuis le déploiement n’est pas susceptible de se dérouler sans les artefacts requis.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  Si un jeton SAS pour l’emplacement des artefacts n’est pas fourni dans le fichier de paramètres, créer un pour permettre un accès en lecture seule temporaire pour le conteneur de stockage en ligne. Puis, passez ce jeton SAS sur la ligne de commande sous la forme d’un « optionalParameter ». Notez que tous les paramètres transmis sur la ligne de commande ont priorité sur les valeurs fournies dans le fichier de paramètres.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Créer le groupe de ressources s’il ne pas déjà existe et vérifier le fichier de modèle et les paramètres pour les erreurs de validation qui empêchent le déploiement de réussir.

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Enfin, déployez le modèle. Ce code crée un nom unique pour le déploiement à l’aide d’une estampille.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>Déployer le groupe de ressources

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Pour déployer le groupe de ressources dans Visual Studio

1. Dans le menu contextuel du projet du groupe de ressources d’Azure, choisissez **Deploy** > **Nouveau déploiement**.

    ![][0]

1. Dans la boîte de dialogue **déployer au groupe de ressources** , soit choisir un groupe de ressource existant dans la zone de liste déroulante pour déployer ou choisir ** &lt;nouveau... &gt;** Pour créer un nouveau groupe de ressources.

    ![][1]

1. Si vous y êtes invité, entrez le nom du groupe de ressources et l’emplacement dans la boîte de dialogue **Créer un groupe de ressources** , puis choisissez le bouton **créer** .

    ![][2]

1. Cliquez sur le bouton **Modifier les paramètres** pour afficher la boîte de dialogue **Modifier les paramètres** et puis entrez les valeurs des paramètres manquants.

    ![][3]

    >[AZURE.NOTE] Si vous avez besoin de valeurs tous les paramètres requis, cette boîte de dialogue s’affiche automatiquement lorsque vous déployez.

    ![][4]

1. Lorsque vous avez terminé permet d’entrer des valeurs de paramètre, cliquez sur le bouton **Enregistrer** et cliquez sur le bouton **déployer** .

    L’exécution du script de déploiement (déploiement-AzureResourceGroup.ps1) et votre modèle, ainsi que tous les artefacts, déploie sur Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Pour déployer le groupe de ressources à l’aide de PowerShell

Si vous souhaitez exécuter le script sans l’aide de la commande de déploiement de Visual Studio et l’interface utilisateur, dans le menu contextuel pour le script, choisissez **Ouvrir avec PowerShell ISE**.

![][5]


## <a name="command-deployment-examples"></a>Exemples de déploiement de commande

### <a name="deploy-using-default-values"></a>Déployer à l’aide des valeurs par défaut

Cet exemple montre comment exécuter le script en utilisant les valeurs de paramètre par défaut. (Car le paramètre d’emplacement n’a pas de valeur par défaut, vous devez en fournir un.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Déploiement de substituer les valeurs par défaut

Cet exemple montre comment exécuter le script pour déployer des fichiers de modèle et les paramètres qui diffèrent des valeurs par défaut.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Déployer à l’aide de UploadArtifacts pour la mise en attente

Cet exemple montre comment exécuter le script pour télécharger des artefacts à partir du dossier de version et de déployer les modèles par défaut.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Cet exemple montre comment exécuter le script dans une tâche de PowerShell d’Azure dans Visual Studio en ligne.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le Gestionnaire de ressources Azure, en lisant la [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

Pour plus d’exemples sur l’utilisation des projets de groupe de ressources Azure, consultez [déployer et gérer des ressources Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) à partir de la connexion de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour plus des Démarrages rapides à partir de la démo de HealthClinic.biz, consultez les [Démarrages rapides outils de développeur de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png
