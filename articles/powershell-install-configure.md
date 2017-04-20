<properties
    pageTitle="Comment faire pour installer et configurer Azure PowerShell"
    description="Découvrez comment installer et configurer Azure PowerShell."
    editor="tysonn"
    manager="dongill"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="coreyp"/>

# <a name="how-to-install-and-configure-azure-powershell"></a>Comment faire pour installer et configurer Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">CLI Azure</a></div>

##<a name="what-is-azure-powershell"></a>Nouveautés d’Azure PowerShell ?
PowerShell Azure est un ensemble de modules qui fournissent les applets de commande pour gérer les Azure avec Windows PowerShell. Vous pouvez utiliser les applets de commande pour créer, tester, déployer et gérer des solutions et des services fournis par le biais de la plateforme Azure. Dans la plupart des cas, les applets de commande peut être utilisé pour les mêmes tâches que le portail d’Azure, telles que la création et la configuration des services en nuage, les ordinateurs virtuels, les réseaux virtuels et les applications web.

## <a name="how-versioning-works"></a>Contrôle de version

PowerShell Azure utilise la sémantique de contrôle de version, qui signifie que si la version A > version B, alors la version A est l’API la plus à jour. En outre, cela signifie que dans la moyenne de la version majeure une rupture les modifications apportées dans une ou plusieurs applets de commande.  Ainsi, par exemple, version 1.7.0 est un correctif pour résoudre un changement majeur dans les versions 1.x de PowerShell d’Azure.

Pour plus d’informations sur les pratiques de gestion des versions de sémantique dans Azure PowerShell, consultez la spécification sémantique de Versioning à : http://semver.org
 
Pour obtenir les toutes dernières API, vous devez utiliser la version 2.x. Mais si vous avez des scripts écrits avec la version 1.x et que vous ne souhaitez pas absorber les nouveautés de la version 2.x décrit dans les [Notes de version](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md), de 2.x, puis vous devez installer 1.7.0.

Une incompatibilité de version peut entraîner si la dernière version du module profil est installée, et une version antérieure d’un module qui en dépend est chargée par la suite. Pour résoudre ce problème, le plus simple consiste à installer à partir de la dernière .msi. Le fichier .msi nettoie automatiquement les anciennes versions des modules.
 
###<a name="installing-module-versions-side-by-side"></a>L’installation de versions de module côte-à-côte

Version 2.1.0 (version 1.2.6 pour AzureStack) sont les premières versions de module conçues pour être installé et utilisé côte-à-côte. Car Azure PowerShell utilise les modules binaires, vous devez ouvrir une nouvelle fenêtre PowerShell et le **Module d’importation** permet d’importer une version spécifique des applets de commande AzureRM :

    Import-Module AzureRM -RequiredVersion 2.1.0**

Les versions 2.1.0 (autres que 1.2.6) ne fonctionnent pas bien côte à côte avec d’autres versions du module PowerShell de Azure. Lors du chargement d’une version antérieure des modules Azure PowerShell en utilisant une commande comme celle ci-dessus, des versions incompatibles du module **AzureRM.Profile** seront chargées, ce qui les applets de commande vous demandant d’ouvrir une session chaque fois que vous exécutez une applet de commande, même une fois que vous êtes connecté.

La façon la plus simple pour résoudre ce problème que consiste à installer PowerShell Azure plus récente à partir de la WebPI flux ou .msi – cette opération supprime les versions antérieures des modules installés à partir de la galerie. 

Notez que les modules à la fois Azure et AzureRM ont en commun, les dépendances afin que, si vous utilisez les deux modules, lors de la mise à jour d’un, vous devez mettre à jour les deux. Les versions antérieures du module Azure ont le même problème avec module côte-à-côte du chargement par les versions antérieures du module AzureRM.

<a id="Install"></a>
## <a name="step-1-install"></a>Étape 1 : installer

Voici les deux méthodes par lesquelles vous pouvez installer PowerShell d’Azure. Vous pouvez l’installer à partir de la galerie de PowerShell ou le WebPI.

###<a name="installing-azure-powershell-from-the-powershell-gallery"></a>L’installation de PowerShell Azure à partir de la galerie de PowerShell

La méthode recommandée consiste à utiliser des galerie de PowerShell. Vous avez besoin du module de PowerShellGet à utiliser la galerie de PowerShell. Cette option est disponible ici : [PowerShellGallery.com](https://www.powershellgallery.com/)

Installez Azure PowerShell version 1.3.0 ou supérieure à partir de la galerie de PowerShell à l’aide d’une invite de Windows PowerShell ou PowerShell Integrated Scripting Environment (ISE) élevée à l’aide des commandes suivantes :

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####<a name="more-about-these-commands"></a>En savoir plus sur ces commandes.

- **AzureRM du Module d’installation** installe un module de correctif cumulatif pour les applets de commande du Gestionnaire de ressources Azure. Le module AzureRM dépend 
- une plage de version spécifique pour chaque module du Gestionnaire de ressources Azure. La plage de version inclus permet de garantir qu’aucune des modifications avec rupture module ne peuvent être incluses lors de l’installation de modules de AzureRM avec la même version principale. Lorsque vous installez le module AzureRM, n’importe quel module Azure le Gestionnaire de ressources qui n’a pas encore été installé est téléchargé et installé à partir de la galerie de PowerShell. Pour plus d’informations sur le versioning sémantique utilisé par les modules PowerShell d’Azure, consultez [semver.org](http://semver.org). 
- **Module d’installation Azure** installe le module Azure. Ce module est le module de gestion des services Azure PowerShell 0.9.x. Doit être non principales modifications et interchangeables pour la version précédente du module Azure.

###<a name="installing-azure-powershell-from-webpi"></a>L’installation de PowerShell Azure dans WebPI

L’installation Azure PowerShell 1.0 et supérieur de WebPI est le même qu’il était de 0.9.x. Télécharger [PowerShell d’Azure](http://aka.ms/webpi-azps) et démarrer l’installation. Si vous avez Azure PowerShell 0.9.x installé, la version 0.9.x sera désinstallé dans le cadre de la mise à niveau. Si vous avez installé les modules Azure PowerShell à partir de la galerie de PowerShell, le programme d’installation supprime automatiquement les modules avant l’installation afin de garantir un environnement cohérent de PowerShell d’Azure.

> [AZURE.NOTE] Si vous avez déjà installé des modules Azure à partir de la galerie de PowerShell, le programme d’installation va les supprimer automatiquement. Cela empêche toute confusion sur les versions de module que vous avez installé et où ils se trouvent. Les modules PowerShell galerie seront normalement installé dans **%ProgramFiles%\WindowsPowerShell\Modules**. En revanche, WebPI sera installé les modules Azure dans * *% ProgramFiles% (x86) %\Microsoft SDKs\Azure\PowerShell\**. Si une erreur se produit lors de l’installation, vous pouvez supprimer manuellement l’Azure* dossiers dans votre dossier de **%ProgramFiles%\WindowsPowerShell\Modules** et relancez l’installation.

Une fois l’installation terminée, votre ```$env:PSModulePath``` paramètre doit inclure les répertoires contenant les applets de commande PowerShell d’Azure.

> [AZURE.NOTE] Il existe un problème connu avec PowerShell **$env : PSModulePath** qui peut se produire lors de l’installation à partir de WebPI. Si votre ordinateur nécessite un redémarrage en raison d’autres installations ou des mises à jour du système, il peut provoquer des mises à jour **$env : PSModulePath** pour ne pas inclure le chemin d’accès où Azure PowerShell est installé. Si cela se produit, un message de « applet de commande non reconnue » peut s’afficher lorsque vous essayez d’utiliser les applets de commande PowerShell de Azure après l’installation ou la mise à niveau. Si cela se produit, le redémarrage de l’ordinateur doit résoudre le problème.

Si vous recevez un message semblable à ce qui suit lorsque vous essayez de charger ou d’exécuter des applets de commande :

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Ce problème peut être corrigé par le redémarrage de l’ordinateur ou de l’importation des applets de commande à partir de C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ comme suit (où XXXX est la version de PowerShell installé :

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Étape 2 : Démarrer
Vous pouvez exécuter les applets de commande à partir de la console Windows PowerShell standard, ou à partir de PowerShell Integrated Scripting Environment (ISE).
La méthode que vous utilisez pour ouvrir une console dépend de la version de Windows que vous exécutez :

- Sur un ordinateur exécutant au moins Windows 8 ou Windows Server 2012, vous pouvez utiliser la recherche intégrée. À partir de l’écran de **démarrage** , commencez par taper l’alimentation. Cette propriété renvoie une liste des applications étendue qui inclut Windows PowerShell. Pour ouvrir la console, cliquez sur l’application. (Pour ajouter l’application à l’écran de **démarrage** , cliquez sur l’icône.)

- Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le **menu Démarrer**. Dans le menu **Démarrer** , cliquez sur **Tous les programmes**, sur **Accessoires**, cliquez sur le dossier **De Windows PowerShell** , puis cliquez sur **Windows PowerShell**.

Vous pouvez également exécuter **Windows PowerShell ISE** pour utiliser des éléments de menu et des raccourcis clavier pour réaliser les mêmes tâches que vous pouvez exécuter dans la console Windows PowerShell. Pour utiliser l’ISE, dans la console Windows PowerShell, Cmd.exe, ou dans la zone **exécuter** , tapez **powershell_ise.exe**.

###<a name="commands-to-help-you-get-started"></a>Commandes pour vous aider à démarrer

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
    
    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Étape 3 : connecter
Les applets de commande ont besoin de votre abonnement pour gérer vos services. Vous pouvez acheter un abonnement Azure si vous n’en avez pas déjà. Pour obtenir des instructions, voir [Comment acheter Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Type **Connexion-AzureRmAccount**

2. Tapez l’adresse e-mail et le mot de passe associé à votre compte. Azure authentifie et enregistre les informations d’identification et puis ferme la fenêtre.

--OU--

Connectez-vous à votre travail ou compte d’établissement :

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Si vous avez plusieurs clients associé à votre compte d’organisation, spécifiez le paramètre TenantId :

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Ce journal non interactif dans la méthode fonctionne uniquement avec un compte professionnel ou l’école. Un compte de travail ou de l’école est un utilisateur qui est géré par votre travail ou à l’école, défini dans l’instance Azure Active Directory pour votre travail ou à l’école. Si vous n’avez pas actuellement d’un compte de travail ou à l’école et sont à l’aide d’un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez facilement en créer un à l’aide de la procédure suivante.

> 1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com), puis cliquez sur **Active Directory**.

> 2. Si aucun répertoire n’existe, sélectionnez **créer votre répertoire** et fournir les informations demandées.

> 3. Sélectionnez votre répertoire et ajouter un nouvel utilisateur. Ce nouvel utilisateur peut se connecter en utilisant un compte de travail ou de l’école. Lors de la création de l’utilisateur, il vous êtes fourni avec une adresse de messagerie de l’utilisateur et un mot de passe temporaire. Enregistrez ces informations, qu’il est utilisé à l’étape 5 ci-dessous.

> 4. À partir du portail Azure classique, sélectionnez **paramètres** , puis **les administrateurs**. Sélectionnez **Ajouter**et ajouter le nouvel utilisateur en tant qu’un administrateur de collaboration. Ainsi, le compte de l’école ou de travail gérer votre abonnement Azure.

> 5. Enfin, déconnecter le portail classique Azure et puis ouvrez une session à l’aide de travail ou l’école compte. S’il s’agit de la première connexion à ce compte, vous êtes invité à modifier le mot de passe.

> Pour plus d’informations sur l’inscription à Microsoft Azure avec un compte professionnel ou à l’école, consultez [inscription à Microsoft Azure sous la forme d’une organisation](./active-directory/sign-up-organization.md).

> Pour plus d’informations sur la gestion de l’authentification et l’abonnement dans Azure, voir [Gérer les comptes, les abonnements et les rôles administratifs](http://go.microsoft.com/fwlink/?LinkId=324796).

### <a name="view-account-and-subscription-details"></a>Afficher les détails du compte et d’abonnement

Vous pouvez avoir plusieurs comptes et abonnements disponibles pour une utilisation par PowerShell d’Azure. Vous pouvez ajouter plusieurs comptes en exécutant **AzureRmAccount-ajouter** plus d’une fois.

Pour afficher les comptes Azure disponibles, tapez **Get-AzureAccount**.

Pour afficher vos abonnements Azure, tapez **Get-AzureRmSubscription**.

##<a id="Help"></a>Obtenir de l’aide##

Ces ressources fournissent une aide pour les applets de commande spécifiques :


-   À partir de la console, vous pouvez utiliser le système d’aide intégré. L’applet de commande **Get-Help** fournit l’accès à ce système. 

- Pour obtenir de l’aide à partir de la Communauté, essayez ces forums les plus courants :

 - [Forum Azure sur MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [StackOverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##<a name="learn-more"></a>Pour en savoir plus


Consultez les ressources suivantes pour en savoir plus sur l’utilisation des applets de commande :

Pour des instructions de base sur l’utilisation de Windows PowerShell, reportez-vous à la section [De Windows PowerShell à l’aide de](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Pour des informations de référence sur les applets de commande, voir [Référence d’applet de commande Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Pour des exemples de scripts et des instructions pour vous aider à apprendre à utiliser des scripts pour gérer Azure, consultez le [Centre de scripts](http://go.microsoft.com/fwlink/p/?LinkId=321940).

