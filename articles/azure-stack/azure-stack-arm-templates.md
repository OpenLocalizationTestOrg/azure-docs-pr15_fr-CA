<properties
    pageTitle="Utiliser des modèles d’Azure le Gestionnaire de ressources dans la pile d’Azure (développeurs locataire) | Microsoft Azure"
    description="Apprenez à utiliser des modèles du Gestionnaire de ressources Azure dans la pile d’Azure pour déployer et configurer toutes les ressources de votre application dans une opération unique et coordonnée."
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
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utiliser des modèles de gestionnaire de ressources Azure dans la pile d’Azure

Modèles de gestionnaire de ressources Azure déploiement et de configurer toutes les ressources de votre application dans une opération unique et coordonnée. Vous définissez les ressources de l’application et la manière dont elle sera déployée.  Vous pouvez également redéployer des modèles pour apporter des modifications aux ressources dans le groupe de ressources.

Ces modèles peuvent être déployés avec le portail Microsoft Azure pile, PowerShell, la ligne de commande et de Visual Studio.

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

Les modèles suivants sont disponibles sur [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Déploiement de SharePoint (sans haute disponibilité)

Utilisez l’extension PowerShell DSC pour créer une batterie de serveurs SharePoint 2013 comprend les éléments suivants :

-   Un réseau virtuel

-   Trois comptes de stockage

-   Deux programmes d’équilibrage de charge externe

-   Un ordinateur virtuel configuré comme un contrôleur de domaine dans une nouvelle forêt avec un seul domaine

-   Un ordinateur virtuel configuré comme un serveur autonome SQL Server 2014

-   Un ordinateur virtuel configuré comme une batterie de serveurs SharePoint 2013 une machine

## <a name="deploy-ad-non-high-availability"></a>Déployer AD (sans haute disponibilité)

Utilisez l’extension PowerShell DSC pour créer un serveur de contrôleur de domaine Active Directory qui comprend les éléments suivants :

-   Un réseau virtuel

-   Un compte de stockage

-   Un équilibreur de charge externe

-   Un ordinateur virtuel configuré comme un contrôleur de domaine dans une nouvelle forêt avec un seul domaine

## <a name="deploy-adsql-non-high-availability"></a>Déployer AD/SQL (sans haute disponibilité)

Utilisez l’extension PowerShell DSC pour créer un serveur autonome SQL Server 2014 qui inclut les éléments suivants :

-   Un réseau virtuel

-   Deux comptes de stockage

-   Un équilibreur de charge externe

-   Un ordinateur virtuel configuré comme un contrôleur de domaine dans une nouvelle forêt avec un seul domaine

-   Un ordinateur virtuel configuré comme un serveur autonome SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utilisez l’extension de DSC de PowerShell pour configurer un ordinateur virtuel existant Local Configuration Manager (LCM) et enregistrez-le dans Azure compte DSC extraire serveur Automation.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Créer un ordinateur virtuel à partir d’une image de l’utilisateur

Créer un ordinateur virtuel à partir d’une image de l’utilisateur personnalisée. Ce modèle déploie également une interface réseau, adresse IP publique et un réseau virtuel (avec DNS).

## <a name="simple-vm"></a>Machine virtuelle simple

Déployer un ordinateur virtuel Windows simple qui inclut une interface réseau, adresse IP publique et un réseau virtuel (avec DNS).

## <a name="cancel-a-running-template-deployment"></a>Annuler un déploiement du modèle en cours d’exécution

Pour annuler un déploiement du modèle en cours d’exécution, utilisez la `Stop-AzureRmResourceGroupDeployment` applet de commande PowerShell.


## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec le portail](azure-stack-deploy-template-portal.md)

[Vue d’ensemble de Gestionnaire des ressources Azure](../azure-resource-manager/resource-group-overview.md)

