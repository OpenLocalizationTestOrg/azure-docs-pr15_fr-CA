<properties
    pageTitle="Commandes de PowerShell basée sur le Gestionnaire de ressources de Azure pour Azure Web App | Microsoft Azure"
    description="Apprenez à utiliser les nouvelles commandes PowerShell de basée sur Azure le Gestionnaire de ressources pour la gestion d’applications Web Azure."
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
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>À l’aide de PowerShell basée sur le Gestionnaire de ressources Azure pour gérer les applications Web Azure#

> [AZURE.SELECTOR]
- [CLI Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Avec Microsoft Azure PowerShell version 1.0.0 nouvelles commandes ont été ajoutés, ce qui donne à l’utilisateur la possibilité d’utiliser les commandes basées sur le Gestionnaire de ressources Azure de PowerShell pour gérer des applications Web.

Pour en savoir plus sur la gestion des groupes de ressources, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md). 

Pour en savoir plus sur la liste complète des paramètres et des options pour les applets de commande PowerShell, consultez la [Référence d’applet de commande complète des applets de commande PowerShell basée sur le Gestionnaire de ressources Web application Azure](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Plans de gestion de Service de l’application ##

### <a name="create-an-app-service-plan"></a>Créer un Plan de Service d’application ###
Pour créer un plan de service d’application, utilisez l’applet de commande **New-AzureRmAppServicePlan** .

Voici les descriptions des différents paramètres :

-   **Nom**: nom du plan de service d’application.
-   **Emplacement**: emplacement de plan de service.
-   **ResourceGroupName**: groupe de ressources qui inclut le plan de service d’application nouvellement créé.
-   **Niveau**: le niveau de tarification souhaité (valeur par défaut est disponible, les autres options sont partagées, Basic, Standard et Premium).
-   **WorkerSize**: la taille des travailleurs (valeur par défaut est petite si le paramètre de niveau a été spécifié en tant que Basic, Premium ou Standard. Autres options sont Medium et Large).
-   **NumberofWorkers**: le nombre de travailleurs dans l’application de service plan (la valeur par défaut est 1). 

Exemple pour utiliser cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Créer un Plan de Service de l’application dans un environnement de Service d’application ###
Pour créer un plan de service d’application dans un environnement de service d’application, utilisez la même commande de **New-AzureRmAppServicePlan** de commande avec des paramètres supplémentaires pour spécifier le nom de l’ASE et le nom de groupe de ressources de ASE.

Exemple pour utiliser cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Pour en savoir plus sur l’environnement de service d’application, consultez [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Liste des Plans de Service d’application existants ###

Pour répertorier les plans de service application existante, utilisez l’applet de commande **Get-AzureRmAppServicePlan** .

Pour répertorier tous les plans de service d’application sous votre abonnement, utilisez : 

    Get-AzureRmAppServicePlan

Pour répertorier tous les plans de service d’application sous un groupe de ressources spécifiques, utilisez :

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir un plan de service d’application spécifique, utilisez :

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurer un Plan de Service application existante ###

Pour modifier les paramètres d’un plan de service d’application existant, utilisez l’applet de commande **Set-AzureRmAppServicePlan** . Vous pouvez modifier le niveau, la taille du travail et du nombre de travailleurs 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Mise à l’échelle d’un Plan de Service d’application ####

Pour mettre à l’échelle un Plan de Service application existantes, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Modification de la taille du travail d’un Plan de Service d’application ####

Pour modifier la taille des travailleurs dans un Plan de Service application existantes, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Modification de la couche d’un Plan de Service d’application ####

Pour modifier le niveau d’un Plan de Service application existantes, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Supprimer un Plan de Service application existante ###

Pour supprimer un plan de service d’application existant, toutes les applications web affectées doivent être déplacés ou supprimés en premier. Puis, à l’aide de l’applet de commande **Remove-AzureRmAppServicePlan** vous pouvez supprimer le plan de service d’application.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Gestion des applications de Service d’application Web ##

### <a name="create-a-web-app"></a>Créer une application Web ###

Pour créer une application web, utilisez l’applet de commande **New-AzureRmWebApp** .

Voici les descriptions des différents paramètres :

- **Nom**: nom de l’application web.
- **AppServicePlan**: nom du plan de service utilisée pour héberger l’application web.
- **ResourceGroupName**: groupe de ressources qui héberge le plan de service d’application.
- **Emplacement**: l’emplacement d’application web.

Exemple pour utiliser cette applet de commande :

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Créer une application Web dans un environnement de Service d’application ###

Pour créer une application web dans un environnement de Service de l’application (ASE). Utilisez la même commande **New-AzureRmWebApp** avec des paramètres supplémentaires pour spécifier le nom de l’équipement auxiliaire de direction et le nom du groupe de ressources auquel appartient la ASE.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Pour en savoir plus sur l’environnement de service d’application, consultez [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Supprimer une application Web existante ###

Pour supprimer une application web existante, vous pouvez utiliser l’applet de commande **Remove-AzureRmWebApp** , vous devez spécifier le nom de l’application web et le nom de groupe de ressources.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Liste des applications Web existantes ###

Pour répertorier les applications web existantes, utilisez l’applet de commande **Get-AzureRmWebApp** .

Pour répertorier toutes les applications web sous votre abonnement, utilisez :

    Get-AzureRmWebApp

Pour répertorier toutes les applications web sous un groupe de ressources spécifiques, utilisez :

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir une application web spécifique, utilisez :

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurer une application Web existante ###

Pour modifier les paramètres et les configurations d’une application web existante, utilisez l’applet de commande **Set-AzureRmWebApp** . Pour obtenir une liste complète des paramètres, consultez le [lien de référence d’applet de commande](https://msdn.microsoft.com/library/mt652487.aspx)

Exemple (1) : utilisez cette applet de commande pour modifier les chaînes de connexion

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemple (2) : ajouter ou modifier les paramètres de l’application

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemple (3) : définissez l’application web pour s’exécuter en mode 64 bits.

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Modifier l’état d’une application Web existante ###

#### <a name="restart-a-web-app"></a>Redémarrage d’une application web ####

Pour redémarrer une application web, vous devez spécifier le groupe de ressources et le nom de l’application web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Arrêt d’une application web ####

Pour arrêter une application web, vous devez spécifier le groupe de ressources et le nom de l’application web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Démarrer une application web ####

Pour démarrer une application web, vous devez spécifier le groupe de ressources et le nom de l’application web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gérer les profils de publication Web App ###

Chaque application web dispose d’un profil de publication qui peut être utilisé pour publier vos applications, plusieurs opérations peuvent être exécutées sur la publication des profils.

#### <a name="get-publishing-profile"></a>Disposez d’un profil de publication ####

Pour obtenir le profil de publication pour une application web, utilisez :

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Cette commande renvoie en écho le profil de publication à la ligne de commande en tant que sortie et le profil de publication dans un fichier texte.

#### <a name="reset-publishing-profile"></a>Rétablir le profil de publication ####

Pour réinitialiser tous les deux le mot de passe publication FTP et web déployer pour une application web, utilisez :

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Gérer les certificats d’application Web ###

Pour savoir comment gérer les certificats d’application web, consultez [liaison de certificats SSL à l’aide de PowerShell](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>Étapes suivantes ###
- Pour en savoir plus sur le support d’Azure Resource Manager PowerShell, reportez-vous à la section [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure.](../powershell-azure-resource-manager.md)
- Pour obtenir des informations sur les environnements de Service d’application, reportez-vous à la section [Introduction à l’environnement de Service d’application.](app-service-app-service-environment-intro.md)
- Pour en savoir plus sur la gestion des certificats SSL de Service d’application à l’aide de PowerShell, consultez [liaison de certificats SSL à l’aide de PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Pour connaître la liste complète des applets de commande PowerShell de basé sur le Gestionnaire de ressources Azure pour Azure Web Apps, consultez [référence d’applet de commande Azure de PowerShell Cmdlets de Web applications Azure le Gestionnaire de ressources.](https://msdn.microsoft.com/library/mt619237.aspx)
- - Pour en savoir plus sur la gestion des services d’application à l’aide de la CLI, reportez-vous à la section [CLI de XPlat Using Azure Resource Manager-Based pour Azure Web App.](app-service-web-app-azure-resource-manager-xplat-cli.md)
