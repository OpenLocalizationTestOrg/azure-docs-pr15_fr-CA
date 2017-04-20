<properties
    pageTitle="Les outils de ligne de commande multiplateforme basé sur le Gestionnaire de ressources Azure pour Azure Web App | Microsoft Azure"
    description="Apprenez à utiliser les nouveaux outils de ligne de commande multiplateforme de basée sur Azure le Gestionnaire de ressources pour la gestion d’applications Web Azure."
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

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>À l’aide de la CLI de XPlat basée sur le Gestionnaire des ressources Azure pour Azure Web App#

> [AZURE.SELECTOR]
- [CLI Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Avec la sortie de la version des outils de ligne de commande de Microsoft Azure multiplates-0.10.5, les nouvelles commandes ont été ajoutées. Ces commandes permettent à l’utilisateur la possibilité d’utiliser les commandes basées sur le Gestionnaire de ressources Azure de PowerShell pour gérer des applications Web.

Pour en savoir plus sur la gestion des groupes de ressources, consultez [utiliser la CLI Azure pour gérer les ressources et groupes ressources Azure](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Plans de gestion de Service de l’application ##

### <a name="create-an-app-service-plan"></a>Créer un Plan de Service d’application ###
Pour créer un plan de service d’application, utilisez la commande **créer d’azure appserviceplan** .

Voici les descriptions des différents paramètres :

-   **--des groupes de ressources**: groupe de ressources qui inclut le plan de service d’application nouvellement créé.
-   **: nom de**: nom du plan de service d’application.
-   **--emplacement**: emplacement de plan de service app.
-   **--niveau**: la référence sku de tarification souhaité (les options sont : F1 (Free). D1 (partagé). B1 (petite base), B2 (support de base) et B3 (Basic volumineux). S1 (petite Standard), S2 (support Standard) et S3 (Standard volumineux). P1 (petite Premium), P2 (support Premium) et P3 (Premium grande).)
-   **--instances**: le nombre de travailleurs dans l’application de service plan (la valeur par défaut est 1).

Exemple pour utiliser cette applet de commande :

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>Liste des Plans de Service d’application existants ###

Pour répertorier les plans de service application existante, utilisez la commande **liste des appserviceplan azure** .

Pour répertorier tous les plans de service d’application sous un groupe de ressources spécifiques, utilisez :

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Pour obtenir un plan de service d’application spécifique, utilisez la commande **show de l’appserviceplan azure** :

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurer un Plan de Service application existante ###

Pour modifier les paramètres d’un plan de service d’application existant, utilisez la commande **appserviceplan azure config** . Vous pouvez modifier le point de stock et le nombre des travailleurs 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Mise à l’échelle d’un Plan de Service d’application ####

Pour mettre à l’échelle un Plan de Service application existantes, utilisez :

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Modification de la référence SKU d’un Plan de Service d’application ####

Pour modifier la référence d’un Plan de Service application existantes, utilisez :

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Supprimer un Plan de Service application existante ###

Pour supprimer un plan de service d’application existant, toutes les applications web affectées doivent être déplacés ou supprimés en premier. Puis, à l’aide de la commande **Supprimer de webapp azure** , que vous pouvez supprimer le plan de service d’application.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Gestion des applications de Service d’application Web ##

### <a name="create-a-web-app"></a>Créer une application Web ###

Pour créer une application web, utilisez la commande **créer de webapp azure** .

Voici les descriptions des différents paramètres :

- **: nom de**: nom de l’application web.
- **--plan**: nom du plan de service utilisée pour héberger l’application web.
- **--des groupes de ressources**: groupe de ressources qui héberge le plan de service d’application.
- **--emplacement**: l’emplacement d’application web.

Exemple pour utiliser cette applet de commande :

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Supprimer une application Web existante ###

Pour supprimer une application web existante, vous pouvez utiliser la commande **Supprimer de webapp azure** , vous devez spécifier le nom de l’application web et le nom de groupe de ressources.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Liste des applications Web existantes ###

Pour répertorier les applications web existantes, utilisez la commande **liste de webapp azure** .

Pour répertorier toutes les applications web sous un groupe de ressources spécifiques, utilisez :

    azure webapp list --resource-group ContosoAzureResourceGroup

Pour obtenir une application web spécifique, utilisez la commande **show de webapp azure** .

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurer une application Web existante ###

Pour modifier les paramètres et les configurations d’une application web existante, utilisez la commande **config de webapp azure** .

Exemple (1) : modifier la version de php d’une application web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Exemple (2) : ajouter ou modifier les paramètres de l’application

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Pour connaître les autre la configuration peut être modifiée, utilisez la commande **config de webapp azure set -h** .

### <a name="change-the-state-of-an-existing-web-app"></a>Modifier l’état d’une application Web existante ###

#### <a name="restart-a-web-app"></a>Redémarrage d’une application web ####

Pour redémarrer une application web, vous devez spécifier le groupe de ressources et le nom de l’application web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Arrêt d’une application web ####

Pour arrêter une application web, vous devez spécifier le groupe de ressources et le nom de l’application web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Démarrer une application web ####

Pour démarrer une application web, vous devez spécifier le groupe de ressources et le nom de l’application web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gérer les profils de publication Web App ###

Chaque application web dispose d’un profil de publication qui peut être utilisé pour publier vos applications.

#### <a name="get-publishing-profile"></a>Disposez d’un profil de publication ####

Pour obtenir le profil de publication pour une application web, utilisez :

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Cette commande renvoie en écho la publication profil utilisateur et le mot de passe à la ligne de commande.

### <a name="manage-web-app-hostnames"></a>Gérer les noms d’hôte de l’application Web ###

Pour gérer les liaisons de nom d’hôte pour votre application web, utilisez la commande de **noms d’hôtes de webapp azure config**  

#### <a name="list-hostname-bindings"></a>Liaisons de nom d’hôte de liste ####

Pour obtenir les liaisons de nom d’hôte actif pour une application web, utilisez :

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Ajout de liaisons de nom d’hôte ####

Pour ajouter des liaisons de nom d’hôte à une application web, utilisez :

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Suppression des liaisons d’un nom d’hôte ####

Pour supprimer les liaisons de nom d’hôte, utilisez :

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Étapes suivantes ###
- Pour en savoir plus sur le support d’Azure Resource Manager CLI, reportez-vous à la section [la CLI Azure permet de gérer les ressources et groupes ressources Azure.](../xplat-cli-azure-resource-manager.md)
- Pour en savoir plus sur la gestion des services d’application à l’aide de PowerShell, consultez [PowerShell Using Azure Resource Manager-Based pour gérer les applications Web Azure.](app-service-web-app-azure-resource-manager-powershell.md)
