<properties
   pageTitle="À l’aide de Scripts de Windows PowerShell pour publier dans des environnements de Test et de dév. | Microsoft Azure"
   description="Découvrez comment utiliser les scripts Windows PowerShell à partir de Visual Studio pour publier sur le développement et les environnements de test."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>À l’aide de scripts de Windows PowerShell pour les publier sur le développement et les environnements de test

Lorsque vous créez une application web dans Visual Studio, vous pouvez générer un script Windows PowerShell que vous pouvez utiliser ultérieurement pour automatiser la publication de votre site Web vers Azure sous la forme d’une application Web dans le Service d’application Azure ou une machine virtuelle. Vous pouvez modifier et étendre le script Windows PowerShell dans l’éditeur Visual Studio pour répondre à vos besoins ou intégrer le script de build existante, de test et les scripts de publication.

À l’aide de ces scripts, vous pouvez prévoir des versions personnalisées (également connu sous les environnements de test et développement) de votre site pour une utilisation temporaire. Par exemple, vous pouvez configurer une version particulière de votre site Web sur un ordinateur virtuel Azure ou sur l’emplacement intermédiaire sur un site Web pour exécuter une suite de tests, reproduire un bogue, une résolution de bogue, version d’évaluation de test une proposition de modification ou configurer un environnement personnalisé pour une démonstration ou une présentation. Une fois que vous avez créé un script qui publie votre projet, vous pouvez recréer les environnements identiques en exécutant de nouveau le script en fonction des besoins, ou exécuter le script avec votre propre version de votre application web pour créer un environnement personnalisé pour le test.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Kit de développement Azure version 2.3 ou ultérieure. Pour plus d’informations, reportez-vous à la section [Téléchargements de Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384) .

Vous n’avez pas besoin du Azure SDK pour générer les scripts pour les projets web. Cette fonctionnalité est pour les projets web, pas les rôles de web services en nuage.

- PowerShell Azure 0.7.4 ou une version ultérieure. Pour plus d’informations, reportez-vous à la section [comment installer et configurer Azure PowerShell](powershell-install-configure.md) .

- [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) ou version ultérieure.

## <a name="additional-tools"></a>Outils supplémentaires

Autres outils et ressources pour l’utilisation de PowerShell dans Visual Studio pour le développement d’Azure sont disponibles. Reportez-vous à la section [Outils de PowerShell pour Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Générer les scripts de publication

Vous pouvez générer les scripts de publication pour une machine virtuelle qui héberge votre site Web lorsque vous créez un nouveau projet en suivant [ces instructions](./virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md). Vous pouvez également [Générer publier des scripts pour les applications web dans le Service d’application Azure](./app-service-web/web-sites-dotnet-get-started.md).

## <a name="scripts-that-visual-studio-generates"></a>Scripts qui génère de Visual Studio

Visual Studio génère un dossier solution appelé **PublishScripts** qui contient les deux fichiers de Windows PowerShell, un script de publication pour votre ordinateur virtuel ou un site Web et un module qui contient des fonctions que vous pouvez utiliser dans les scripts. Visual Studio génère également un fichier au format JSON qui spécifie les détails du projet que vous déployez.

### <a name="windows-powershell-publish-script"></a>Publier les scripts Windows PowerShell

Le script de publication contient des procédures de publication spécifique pour le déploiement sur un site Web ou d’un ordinateur virtuel. Visual Studio fournit une syntaxe de coloration pour le développement de Windows PowerShell. L’aide de fonctions est disponible, et vous pouvez modifier librement les fonctions dans le script pour répondre à vos besoins d’évolution.

### <a name="windows-powershell-module"></a>Module de Windows PowerShell

Le module de Windows PowerShell qui génère de Visual Studio contient des fonctions utilisés par le script de publication. Ce sont des fonctions de Azure PowerShell et ne sont pas destinés à être modifiés. Pour plus d’informations, reportez-vous à la section [comment installer et configurer Azure PowerShell](powershell-install-configure.md) .

### <a name="json-configuration-file"></a>Fichier de configuration de JSON

Le fichier JSON est créé dans le dossier de **configuration** et contient des données de configuration qui spécifie exactement les ressources pour déployer sur Azure. Le nom du fichier que Visual Studio génère est projet-nom-WAWS-dev.json si vous avez créé un site Web ou projet nom-VM-dev.json si vous avez créé un ordinateur virtuel. Voici un exemple de fichier de configuration de JSON qui est généré lorsque vous créez un site Web. La plupart des valeurs est explicite. Le nom du site Web est généré par Azure, donc il peut correspond pas le nom de votre projet.

```
{
"environmentSettings": {
"webSite": {
"name": "WebApplication26632",
"location": "West US"
},
"databases": [
{
"connectionStringName": "DefaultConnection",
"databaseName": "WebApplication26632_db",
"serverName": "YourDatabaseServerName",
"user": "sqluser2",
"password": "",
"edition": "",
"size": "",
"collation": "",
"location": "West US"
}
]
}
}
```
Lorsque vous créez un ordinateur virtuel, le fichier de configuration de JSON ressemble à ce qui suit. Notez qu’un service en nuage est créé comme un conteneur pour la machine virtuelle. L’ordinateur virtuel contient les points de terminaison habituels pour un accès web via HTTP et HTTPS, ainsi que les points de terminaison pour le déploiement Web, qui vous permet de publier sur le site Web à partir de votre ordinateur local, Bureau à distance et Windows PowerShell.

```
{
"environmentSettings": {
"cloudService": {
"name": "myusernamevm1",
"affinityGroup": "",
"location": "West US",
"virtualNetwork": "",
"subnet": "",
"availabilitySet": "",
"virtualMachine": {
"name": "myusernamevm1",
"vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
"size": "Small",
"user": "vmuser1",
"password": "",
"enableWebDeployExtension": true,
"endpoints": [
{
"name": "Http",
"protocol": "TCP",
"publicPort": "80",
"privatePort": "80"
},
{
"name": "Https",
"protocol": "TCP",
"publicPort": "443",
"privatePort": "443"
},
{
"name": "WebDeploy",
"protocol": "TCP",
"publicPort": "8172",
"privatePort": "8172"
},
{
"name": "Remote Desktop",
"protocol": "TCP",
"publicPort": "3389",
"privatePort": "3389"
},
{
"name": "Powershell",
"protocol": "TCP",
"publicPort": "5986",
"privatePort": "5986"
}
]
}
},
"databases": [
{
"connectionStringName": "",
"databaseName": "",
"serverName": "",
"user": "",
"password": ""
}
],
"webDeployParameters": {
"iisWebApplicationName": "Default Web Site"
}
}
}
```

Vous pouvez modifier la configuration de JSON pour modifier ce qui se produit lorsque vous exécutez les scripts de publication. La `cloudService` et `virtualMachine` sections sont obligatoires, mais vous pouvez supprimer la `databases` de section si vous n’avez pas besoin. Les propriétés qui sont vides dans le fichier de configuration par défaut que Visual Studio génère sont facultatives ; ceux qui ont des valeurs dans le fichier de configuration par défaut sont requis.

Si vous avez un site Web qui possède plusieurs environnements de déploiement (appelées emplacements) au lieu d’un site de production unique dans Azure, vous pouvez inclure le nom de l’emplacement dans le nom du site Web dans le fichier de configuration de JSON. Par exemple, si vous avez un site Web qui a nommé **monsite** et un emplacement pour celui-ci nommé **tester** puis l’URI est monsite-test.cloudapp.net, mais le nom correct à utiliser dans le fichier de configuration est mysite(test). Vous ne pouvez le faire cette option si le site Web et emplacements existent déjà dans votre abonnement. Si elles n’existent pas, créer le site Web en exécutant le script sans spécifier l’emplacement, puis créer le slot dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885)et par la suite, exécuter le script avec le nom de site Web modifié. Pour plus d’informations sur les emplacements de déploiement pour les applications web, consultez [configuration mise en environnements pour les applications web dans le Service d’application Azure](./app-service-web/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Comment faire pour exécuter les scripts de publication

Si vous n’avez jamais exécuté un script Windows PowerShell avant, vous devez d’abord définir la stratégie d’exécution pour permettre l’exécution de scripts. Il s’agit d’une fonctionnalité de sécurité pour empêcher les utilisateurs d’exécuter des scripts de Windows PowerShell s’ils sont vulnérables aux logiciels malveillants ou de virus qui impliquent l’exécution de scripts.

### <a name="run-the-script"></a>Exécutez le script

1. Créer le package de déploiement Web pour votre projet. Un package de déploiement Web est une archive compressée (fichier .zip) contenant les fichiers que vous souhaitez copier dans votre site Web ou d’un ordinateur virtuel. Vous pouvez créer des packages de déploiement Web dans Visual Studio pour les applications web.

![Créer Web déploiement de Package](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Pour plus d’informations, consultez [Comment : créer un Package de déploiement Web dans Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Vous pouvez également automatiser la création de votre package de déploiement Web, comme décrit dans la section **personnalisation et l’extension des scripts de publication** plus loin dans cette rubrique.

1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel pour le script et puis choisissez **Ouvrir avec PowerShell ISE**.

1. Si c’est la première fois que vous avez exécuté les scripts de Windows PowerShell sur cet ordinateur, ouvrez une fenêtre d’invite de commandes avec des privilèges d’administrateur et tapez la commande suivante :

`Set-ExecutionPolicy RemoteSigned`

1. Connectez-vous à Azure à l’aide de la commande suivante.

`Add-AzureAccount`

Lorsque vous y êtes invité, fournissez votre nom d’utilisateur et le mot de passe.

Notez que cette méthode de fournir des informations d’identification d’Azure ne fonctionnera pas lors de l’automatisation, le script. Au lieu de cela, vous devez utiliser le fichier .publishsettings pour fournir des informations d’identification. Une fois seulement, vous utilisez la commande **Get-AzurePublishSettingsFile** pour télécharger le fichier à partir d’Azure et par la suite **AzurePublishSettingsFile à l’importation** pour importer le fichier. Pour obtenir des instructions détaillées, reportez-vous à la section [comment installer et configurer Azure PowerShell](powershell-install-configure.md).

1. (Facultatif) Si vous souhaitez créer des ressources Azure comme la machine virtuelle, la base de données et le site Web sans publication de votre application web, utilisez la commande **Publier-WebApplication.ps1** avec la **-Configuration** argument défini dans le fichier de configuration de JSON. Cette ligne de commande utilise le fichier de configuration de JSON pour déterminer les ressources à créer. Parce qu’il utilise les paramètres par défaut pour les autres arguments de ligne de commande, elle crée les ressources, mais ne publie pas votre application web. -Verbose option vous donne plus d’informations sur ce qui se passe.

`Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. Utilisez la commande **Publier-WebApplication.ps1** , comme indiqué dans un des exemples suivants pour appeler le script et la publication de votre application web. Si vous souhaitez substituer les paramètres par défaut pour tous les autres arguments, comme le nom de l’abonnement, de publier le nom du package, les informations d’identification de l’ordinateur virtuel ou les informations d’identification du serveur de base de données, vous pouvez spécifier ces paramètres. Utilisez le **– commentaires** option pour plus d’informations sur la progression du processus de publication.

```
Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
–SubscriptionName Contoso `
-WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
-DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
-Verbose
```

Si vous créez une machine virtuelle, la commande se présente comme suit. Cet exemple montre également comment spécifier les informations d’identification de plusieurs bases de données. Pour les ordinateurs virtuels qui créent de ces scripts, le certificat SSL n’est pas auprès d’une autorité racine de confiance. Par conséquent, vous devez utiliser l’option **– AllowUntrusted** .

```
Publish-WebApplication.ps1 `
-Configuration C:\Path\ADVM-VM-test.json `
-SubscriptionName Contoso `
-WebDeployPackage C:\Path\ADVM.zip `
-AllowUntrusted `
-VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
-DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
-Verbose
```

Le script peut créer des bases de données, mais il ne crée pas de serveurs de base de données. Si vous souhaitez créer un serveur de base de données, vous pouvez utiliser la fonction **New-AzureSqlDatabaseServer** dans le module Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Personnalisation et extension des scripts de publication

Vous pouvez personnaliser le script de publication et le fichier de configuration de JSON. Les fonctions dans le module Windows PowerShell **AzureWebAppPublishModule.psm1** ne sont pas destinées à être modifié. Si vous souhaitez simplement spécifier une autre base de données ou modifier certaines propriétés de l’ordinateur virtuel, modifiez le fichier de configuration de JSON. Si vous souhaitez étendre la fonctionnalité de script pour automatiser la création et le test de votre projet, vous pouvez implémenter les stubs de fonction dans **WebApplication.ps1 de la publication**.

Pour automatiser la génération de votre projet, ajoutez le code qui appelle MSBuild pour `New-WebDeployPackage` comme indiqué dans cet exemple de code. Le chemin d’accès à la commande MSBuild est différent selon la version de Visual Studio que vous avez installé. Pour obtenir le chemin d’accès correct, vous pouvez utiliser la fonction **Get-MSBuildCmd**, comme illustré dans cet exemple.

### <a name="to-automate-building-your-project"></a>Pour automatiser la génération de votre projet

1. Ajouter le `$ProjectFile` paramètre dans la section globale param.

```
[Parameter(Mandatory = $false)]
  [ValidateScript({Test-Path $_ -PathType Leaf})]
  [String]
  $ProjectFile,
```

1. Copiez la fonction `Get-MSBuildCmd` dans votre fichier de script.

```
function Get-MSBuildCmd
{
        process
{

             $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                   Sort-Object {[double]$_.PSChildName} -Descending |
                                   Select-Object -First 1 |
                                   Get-ItemProperty -Name MSBuildToolsPath |
                                   Select -ExpandProperty MSBuildToolsPath
       
            $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

        return Get-Item $path
    }
}
```

1. Remplacer `New-WebDeployPackage` avec le code suivant et remplacez les espaces réservés dans la construction de ligne `$msbuildCmd`. Ce code est pour Visual Studio 2015. Si vous utilisez Visual Studio 2013, affectez à la propriété de **VisualStudioVersion** sous `12.0`.

```
function New-WebDeployPackage
{
    #Write a function to build and package your web application
      
#To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
      
Write-VerboseWithTime 'Build-WebDeployPackage: Start'
      
$msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
      
Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
      
#Start execution of the build command
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
      
if ($job.ExitCode -ne 0)
{
throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName
      
#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
      
      
#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
      
Write-VerboseWithTime 'Build-WebDeployPackage: End'
      
return $WebDeployPackage
}
```

1. Appelez le `New-WebDeployPackage` fonction avant cette ligne : `$Config = Read-ConfigFile $Configuration` pour les applications web ou de `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` pour les machines virtuelles.

```
if($ProjectFile)
{
$WebDeployPackage = New-WebDeployPackage
}
```

1. Appeler le script personnalisé à partir de la ligne de commande utilisant le passage de la `$Project` argument, comme dans la ligne de commande exemple suivante.

```
.\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
-ProjectFile ..\WebApplication5\WebApplication5.csproj `
-VMPassword @{Name="VMUser";Password="Test.123"} `
-AllowUntrusted `
-Verbose
```

Pour automatiser le test de votre application, ajoutez du code à `Test-WebApplication`. Veillez à ne pas commenter les lignes d' **WebApplication.ps1 de la publication** où ces fonctions sont appelées. Si vous ne fournissez pas une implémentation, vous pouvez créer manuellement votre projet avec Visual Studio et puis exécutez le script de publication pour publier sur Azure.

## <a name="publishing-function-summary"></a>Synthèse des fonctions publication

Pour obtenir de l’aide pour les fonctions que vous pouvez utiliser à l’invite de commande Windows PowerShell, utilisez la commande `Get-Help function-name`. L’aide comprend des exemples et l’aide du paramètre. Le texte d’aide est également dans les fichiers source de script, **AzureWebAppPublishModule.psm1** et **WebApplication.ps1 de la publication**. Le script et l’aide est localisées dans la langue de Visual Studio.

**AzureWebAppPublishModule**

|Nom de la fonction|Description|
|---|---|
|Ajouter-AzureSQLDatabase|Crée une nouvelle base de données SQL d’Azure.|
|Ajouter-AzureSQLDatabases|Crée des bases de données SQL d’Azure à partir des valeurs dans le fichier de configuration JSON que Visual Studio génère.|
|Ajouter-AzureVM|Crée un ordinateur virtuel Azure et renvoie l’URL de l’ordinateur virtuel déployé. La fonction définit les conditions préalables, puis appelle la fonction **New-AzureVM** (module Azure) pour créer une nouvelle machine virtuelle.|
|Ajouter-AzureVMEndpoints|Ajoute de nouveaux points de terminaison d’entrée à une machine virtuelle et renvoie l’ordinateur virtuel avec le nouveau point de terminaison.|
|Ajouter-AzureVMStorage|Crée un nouveau compte de stockage Azure dans l’abonnement en cours. Le nom du compte commence par « devtest », suivi d’une chaîne alphanumérique unique. La fonction renvoie le nom du nouveau compte de stockage. Vous devez spécifier un emplacement ou un groupe d’affinité pour le nouveau compte de stockage.|
|Ajouter-AzureWebsite|Crée un site Web avec le nom spécifié et l’emplacement. Cette fonction appelle la fonction **New-AzureWebsite** dans le module Azure. Si l’abonnement ne contient pas déjà un site Web avec le nom spécifié, cette fonction crée le site Web et renvoie un objet de site Web. Dans le cas contraire, il renvoie `$null`.|
|Abonnement à la sauvegarde|Enregistre l’abonnement Azure en cours dans le `$Script:originalSubscription` variable dans la portée de script. Cette fonction enregistre l’abonnement Azure (obtenues par `Get-AzureSubscription -Current`) et son compte de stockage et l’abonnement est modifié par ce script (stockée dans la variable `$UserSpecifiedSubscription`) et le compte de stockage, dans la portée de script. En enregistrant les valeurs, vous pouvez utiliser une fonction, telle que `Restore-Subscription`, pour rétablir le compte d’abonnement et de stockage actuel d’origine à l’état en cours, si l’état actuel a été modifié.|
|Recherche-AzureVM|Obtient la machine virtuelle Azure spécifiée.|
|Format-DevTestMessageWithTime|Ajoute la date et l’heure à un message. Cette fonction est conçue pour les messages écrits dans les flux d’erreur et Verbose.|
|Get-AzureSQLDatabaseConnectionString|Assemble une chaîne de connexion pour vous connecter à une base de données SQL d’Azure.|
|Get-AzureVMStorage|Renvoie le nom du premier compte de stockage avec le modèle de nom « devtest*» (sensible à la casse) dans l’emplacement spécifié ou d’un groupe d’affinité. Si la « devtest*"compte de stockage ne correspond pas à l’emplacement ou l’affinité du groupe, la fonction ignore. Vous devez spécifier un emplacement ou un groupe d’affinité.|
|Get-MSDeployCmd|Renvoie une commande pour exécuter l’outil MsDeploy.exe.|
|Nouvelle-AzureVMEnvironment|Recherche ou crée un ordinateur virtuel dans l’abonnement qui correspond aux valeurs dans le fichier de configuration de JSON.|
|WebPackage publier|Utilise MsDeploy.exe et un site web publient le package. Fichier ZIP pour déployer des ressources à un site Web. Cette fonction ne génère aucune sortie. Si l’appel à MSDeploy.exe échoue, la fonction lève une exception. Pour obtenir une sortie plus détaillée, vous devez utiliser le **-Verbose** option.|
|Publication-WebPackageToVM|Vérifie les valeurs des paramètres et appelle la fonction de **Publication-WebPackage** .|
|Lecture-ConfigFile|Valide le fichier de configuration de JSON et renvoie une table de hachage des valeurs sélectionnées.|
|Restauration-abonnement|Réinitialise l’abonnement en cours à l’abonnement d’origine.|
|Test-AzureModule|Cette propriété renvoie `$true` si la version installée de module Azure est 0.7.4 ou une version ultérieure. Cette propriété renvoie `$false` si le module n’est pas installé ou une version antérieure. Cette fonction n’a pas de paramètres.|
|Test-AzureModuleVersion|Cette propriété renvoie `$true` si la version du module Azure est 0.7.4 ou une version ultérieure. Cette propriété renvoie `$false` si le module n’est pas installé ou une version antérieure. Cette fonction n’a pas de paramètres.|
|Test-HttpsUrl|Convertit l’URL d’entrée à un objet System.Uri. Cette propriété renvoie `$True` si l’URL est absolue et son modèle est https. Cette propriété renvoie `$false` si l’URL est relative, son schéma n’est pas HTTPS, ou la chaîne d’entrée ne peut pas être convertie en une URL.|
|Test-membre|Cette propriété renvoie `$true` si une propriété ou une méthode est un membre de l’objet. Dans le cas contraire, renvoie `$false`.|
|Écriture-ErrorWithTime|Écrit un message d’erreur préfixé avec l’heure actuelle. Cette fonction appelle la fonction **Format-DevTestMessageWithTime** pour ajouter l’heure avant d’écrire le message dans le flux d’erreurs.|
|Écriture-HostWithTime|Écrit un message dans le programme de l’hôte (**Write-Host**) préfixé avec l’heure actuelle. Varie en fonction de l’effet de l’écriture dans le programme hôte. La plupart des programmes qui hébergent Windows PowerShell écrire ces messages vers la sortie standard.|
|Écriture-VerboseWithTime|Écrit un message détaillé préfixé avec l’heure actuelle. Parce qu’il appelle **Write-Verbose**, le message s’affiche uniquement lorsque le script s’exécute avec le paramètre de **commentaires** ou lorsque la préférence de **VerbosePreference** est définie sur **Continue**.|

**WebApplication publier**

|Nom de la fonction|Description|
|---|---|
|Nouvelle-AzureWebApplicationEnvironment|Crée des ressources Azure, tel qu’un site Web ou d’une machine virtuelle.|
|Nouvelle-WebDeployPackage|Cette fonction n’est pas implémentée. Vous pouvez ajouter des commandes de cette fonction pour générer votre projet.|
|Publication-AzureWebApplication|Publie une application web pour Azure.|
|WebApplication publier|Crée et déploie des applications Web, les ordinateurs virtuels, les bases de données SQL et les comptes de stockage d’un projet web de Visual Studio.|
|Test-WebApplication|Cette fonction n’est pas implémentée. Vous pouvez ajouter des commandes de cette fonction pour tester votre application.|

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’écriture de scripts PowerShell en lisant [l’écriture de scripts avec Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) et consultez les autres scripts PowerShell d’Azure dans le [Centre de scripts](https://azure.microsoft.com/documentation/scripts/).
