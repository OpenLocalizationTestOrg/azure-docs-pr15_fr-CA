<properties
    pageTitle="Les services PaaS et les outils pour pile d’Azure | Microsoft Azure"
    description="Découvrez comment mettre en route les services PaaS dans la pile d’Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Outils pour pile Azure

Vous pouvez télécharger les outils décrits ci-dessous. Si vous souhaitez être averti des outils et de nouveaux services, suivez les #AzureStack sur Twitter.

## <a name="template-tools"></a>Outils de modèle

### <a name="azure-stack-github-templates"></a>Modèles de Github de pile Azure
Explorez la collection grandissante de [Modèles de GitHub de pile Azure](https://github.com/Azure/AzureStack-QuickStart-Templates). À l’instar d' [Azure](https://github.com/Azure/azure-quickstart-templates), ces modèles du Gestionnaire de ressources Azure « Démarrage rapide » visent à vous aider à démarrer avec des blocs de conception simples et des exemples, prêts à déployer sur le Microsoft Azure pile aperçu preuve de Concept environnement technique. Inclus sont des exemples de la charge de travail première partie pour [ad-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), [sql-2014-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), [sharepoint-2013-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), bien que plusieurs modèles de 101 simples comme [101-simple-windows-vm](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm).


### <a name="marketplace-item-packaging-tool-and-sample"></a>Exemple et outil d’empaquetage élément marketplace
[Télécharger et utiliser l’outil d’empaquetage](http://www.aka.ms/azurestackmarketplaceitem) pour créer des éléments pour vos propres modèles personnalisés à ajouter sur le marché de la pile d’Azure marketplace. Vous trouverez des instructions sur la façon de créer un élément de marché et le rendre disponible à vos locataires au [point de créer un marché](azure-stack-create-and-publish-marketplace-item.md).

## <a name="developer-tools"></a>Outils de développement


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>Utilisez Visual Studio et Azure pile TP2 sur la machine virtuelle MAS-CON01
Si vous souhaitez utiliser Visual Studio sur la console de machine virtuelle pour utiliser des modèles de pile d’Azure, vous devez installer les versions correctes des outils requis. Utilisez la procédure suivante pour installer les versions prises en charge pour le protocole TP2.

1. Utilisez connexion Bureau à distance pour vous connecter à l’ordinateur virtuel MAS-CON01 avec les informations d’identification d’azurestack\azurestackadmin.
2. Installez et ouvrez Web Platform Installer.
3. Rechercher et installer **Visual Studio 2015 de Communauté avec Microsoft Azure SDK - 2.9.5**.
4. À l’aide **d’Ajout/Suppression de programmes**, désinstallez **Microsoft Azure PowerShell (Sept)** qui est installé dans le cadre du Kit de développement.
5. Ouvrez le programme d’installation de la plate-forme Web.
6. Recherchez et installez **Microsoft Azure PowerShell - Aperçu technique 2 pour Azure pile**. 
7. Redémarrez l’ordinateur virtuel MAS-CON01.
8. Utilisez connexion Bureau à distance pour vous connecter à l’ordinateur virtuel MAS-CON01 avec les informations d’identification d’azurestack\azurestackadmin.
9. Ouvrez Visual Studio et valider que vous pouvez vous connecter à l’environnement de la pile d’Azure, obtenir des modèles, etc.. 

### <a name="azure-powershell-sdk"></a>Azure SDK de PowerShell
PowerShell Azure est un module qui fournit des applets de commande pour gérer Azure et pile Azure avec Windows PowerShell. Vous pouvez utiliser les applets de commande pour créer, tester, déployer et gérer des solutions et des services fournis par le biais de la plateforme Azure pile.
[Télécharger Azure PowerShell SDK](http://aka.ms/azStackPsh) et [installez PowerShell](azure-stack-connect-powershell.md).

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure entre des interfaces de ligne de commande de plate-forme
Installer rapidement l’Interface de ligne de commande de Azure (CLI Azure) pour utiliser un ensemble de commandes d’environnement open source pour la création et la gestion des ressources dans Microsoft Azure pile.

[Télécharger le Kit Windows CLI](http://aka.ms/azstack-windows-cli)

[Télécharger le Mac CLI](http://aka.ms/azstack-linux-cli)

[Télécharger le CLI Linux](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + Si vous êtes sur un ordinateur Mac ou Linux, vous pouvez également obtenir l’interface CLI à l’aide de la commande`npm install -g azure-cli@0.9.11`</br>
> + Si vous obtenez les problèmes de validation de certificat, exécutez la commande`set NODE_TLS_REJECT_UNAUTHORIZED=0`
