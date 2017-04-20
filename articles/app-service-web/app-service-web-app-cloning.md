<properties
    pageTitle="Clonage des applications Web à l’aide de PowerShell"
    description="Apprenez à vos applications Web pour les nouvelles applications Web à l’aide de PowerShell du clone."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Application de Service d’application Azure clonage à l’aide de PowerShell#

Avec le lancement de Microsoft Azure PowerShell version 1.1.0 une nouvelle option a été ajoutée à la nouvelle-AzureRMWebApp, ce qui donnerait à l’utilisateur la possibilité de cloner une application Web existante à une application créée dans une autre région ou dans la même région. Cela permettra aux clients de déployer un certain nombre d’applications dans différentes régions, rapidement et facilement.

Clonage de l’application est actuellement pris en charge uniquement pour les plans de service premium couche application. La nouvelle fonctionnalité utilise les mêmes limitations que la fonctionnalité de sauvegarde des applications Web, reportez-vous à la section [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Pour en savoir plus sur l’utilisation de gestionnaire de ressources Azure basé Azure PowerShell, les applets de commande pour gérer vos applications Web vérifier [Azure le Gestionnaire de ressources en fonction des commandes PowerShell pour Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>Clonage d’une application existante ##

Scénario : Une application web existante dans la région Sud, l’utilisateur souhaite Cloner le contenu vers une nouvelle application web dans la région du Nord centrale nous. Pour ce faire, vous pouvez à l’aide de la version du Gestionnaire de ressources Azure de l’applet de commande PowerShell pour créer une nouvelle application web avec l’option - SourceWebApp.

Connaître le nom du groupe de ressources contenant l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir des informations de l’application web source (dans ce cas nommées webapp-source) :

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Pour créer un nouveau Plan de Service App, nous pouvons utiliser la commande New-AzureRmAppServicePlan dans l’exemple suivant

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

À l’aide de la commande New-AzureRmWebApp, nous pouvons créer la nouvelle application web dans la région du Nord centrale nous et la lier à un niveau de prime existant Plan de Service d’application, plus nous pouvons utiliser le même groupe de ressources en tant que l’application web source, ou définissez un nouveau groupe de ressources, l’exemple suivant montre que :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Pour cloner une application web existante, y compris le déploiement aux emplacements, l’utilisateur doit utiliser le paramètre IncludeSourceWebAppSlots, la commande PowerShell suivante illustre l’utilisation de ce paramètre avec la commande New-AzureRmWebApp :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Pour cloner une application web existante au sein de la même région, l’utilisateur doit créer un nouveau groupe de ressources et un nouveau service app planifier dans la même région et ensuite à l’aide de la commande PowerShell suivante pour cloner l’application web

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonage d’une application existante vers un environnement de Service d’application ##

Scénario : Une application web existante dans la région Sud, l’utilisateur souhaite Cloner le contenu vers une nouvelle application web vers un existant App Service environnement (ASE).

Connaître le nom du groupe de ressources contenant l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir des informations de l’application web source (dans ce cas nommées webapp-source) :

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Connaître les nom de l’équipement de direction auxiliaire et le nom du groupe de ressources auquel appartient l’équipement de direction auxiliaire, l’utilisateur peut utiliser la commande New-AzureRmWebApp pour créer la nouvelle application web dans l’équipement de direction auxiliaire existant, l’exemple suivant montre que :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Le paramètre d’emplacement est requis en raison hérités, mais dans le cas de la création d’une application dans un équipement de direction auxiliaire il sera ignoré. 

## <a name="cloning-an-existing-app-slot"></a>Clonage d’un emplacement d’application existant ##

Scénario : L’utilisateur souhaite cloner un emplacement application Web existante soit une nouvelle application Web ou à un nouvel emplacement de l’application Web. La nouvelle application Web peuvent être dans la même région, comme l’emplacement de l’application Web d’origine ou dans une région différente.

Connaître le nom du groupe de ressources contenant l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir des informations de la source web app d’emplacement (dans ce cas nommées source-webappslot) liées à l’application Web source-webapp :

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

L’exemple suivant illustre la création d’un clone de l’application web source pour une nouvelle application web :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configuration du Gestionnaire de trafic lors de la duplication d’une application ##

Création d’applications web de plusieurs régions et de configuration du Gestionnaire de trafic Azure pour acheminer le trafic vers toutes les applications web, est un scénario n important pour s’assurer que les applications clients sont hautement disponibles, lors du clonage d’une application web existante ont la possibilité de connecter les deux applications web à un nouveau profil de gestionnaire de trafic ou un - Notez que seul le Gestionnaire de ressources Azure de Traffic Manager prend en charge.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Création d’un nouveau profil de Traffic Manager pendant le clonage d’une application ###

Scénario : L’utilisateur souhaite cloner une application web dans une autre région, lors de la configuration d’un profil de gestionnaire de trafic Azure le Gestionnaire de ressources qui incluent les deux applications web. L’exemple suivant illustre la création d’un clone de l’application web source pour une nouvelle application web lors de la configuration d’un nouveau profil de Traffic Manager :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Ajout de nouveaux de cloné Web App à un profil de gestionnaire de trafic existant ###

Scénario : L’utilisateur a déjà un profil de gestionnaire de trafic Azure le Gestionnaire de ressources qui il souhaite ajouter les deux applications web comme points de terminaison. Pour ce faire, nous devons tout d’abord assembler l’id de profil de gestionnaire du trafic existant, nous aurons besoin de l’id d’abonnement, nom du groupe de ressources et le nom de profil du Gestionnaire de trafic existant.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Après avoir l’id de gestionnaire de trafic, l’exemple suivant illustre la création d’un clone de l’application web source pour une nouvelle application web lors de leur ajout à un profil de gestionnaire de trafic existant :

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrictions en cours ##

Cette fonctionnalité est actuellement en mode Aperçu, nous travaillons pour ajouter de nouvelles fonctions au fil du temps, la liste suivante sont les restrictions connues de la version en cours de clonage d’application :

- Paramètres d’échelle automatique ne sont pas clonés.
- Paramètres de planification de la sauvegarde ne sont pas clonés.
- Paramètres de VNET ne sont pas clonés.
- Idées d’application ne sont pas automatiquement définies sur l’application web de destination
- Paramètres d’authentification simples ne sont pas clonés.
- Extension de kudu ne sont pas clonés.
- Conseil les règles ne sont pas clonés.
- Contenu de la base de données ne sont pas clonés.


### <a name="references"></a>Références ###
- [Le Gestionnaire de ressources Azure en fonction des commandes PowerShell pour Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Clonage des application Web à l’aide du portail Azure](app-service-web-app-cloning-portal.md)
- [Sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md)
- [Prise en charge du Gestionnaire de ressources Azure pour l’aperçu du Gestionnaire de trafic Azure](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)
- [À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md)
