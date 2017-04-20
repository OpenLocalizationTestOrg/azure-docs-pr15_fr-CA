<properties
    pageTitle="Azure reste API procédure de surveillance | Microsoft Azure"
    description="Comment authentifier les demandes à et utiliser l’API REST de surveillance Azure."
    authors="mcollier, rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Procédure pas à pas de repos API de surveillance des Azure
Cet article vous montre comment effectuer l’authentification afin que votre code peut utiliser la [Référence de l’API de Microsoft Azure moniteur reste](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

L’API de moniteur Azure permet de récupérer par programme des valeurs métriques, de granularité et les définitions de mesure par défaut disponibles (le type de mesure de temps UC, demandes, etc.). Une fois extraites, les données peuvent être enregistrées dans une banque de données comme base de données de SQL Azure, DocumentDB ou lac de données Azure. À partir de là, une analyse supplémentaire peut être effectuée en fonction des besoins.

En plus de fonctionner avec plusieurs points de données métriques, comme le montre cet article, l’API de moniteur permet à la liste des règles d’alerte, afficher les journaux d’activité et bien plus encore. Pour obtenir une liste complète des opérations disponibles, consultez la [Référence sur l’API Microsoft Azure moniteur reste](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>L’authentification des demandes de moniteur Azure

La première étape consiste à authentifier la demande.

Toutes les tâches exécutées par rapport à l’API d’analyseur Azure utilisent le modèle d’authentification Azure le Gestionnaire de ressources. Par conséquent, toutes les demandes doivent être authentifiés avec Azure Active Directory (AD Azure). Une approche pour l’authentification de l’application cliente doit créer une annonce Azure principal du service et de récupérer le jeton d’authentification (JWT). L’exemple de script suivant illustre la création d’un service d’annonces Azure principal via PowerShell. Pour une vue d’ensemble plus détaillée, reportez-vous à la documentation sur [l’utilisation de PowerShell Azure pour créer un service principal pour accéder aux ressources](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). Il est également possible de [créer un principe de service via le portail Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Pour interroger l’API d’analyseur Azure, l’application cliente doit utiliser l’entité service créé précédemment pour l’authentification. L’exemple de script PowerShell suivant présente une approche, à l’aide de la [Bibliothèque de l’authentification Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL) pour obtenir l’authentification JWT jeton. Le jeton JWT est transmis comme une partie d’un paramètre d’autorisation HTTP dans les demandes à l’API REST de moniteur Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Une fois l’étape de configuration de l’authentification est terminée, les requêtes puis peuvent être exécutées que par rapport à l’API REST de moniteur Azure. Il existe deux requêtes utiles :

1. Répertorie les définitions de mesure d’une ressource

2. Récupérer les valeurs métriques


## <a name="retrieve-metric-definitions"></a>Extraire des définitions de métriques
>[AZURE.NOTE] Pour récupérer les définitions de mesure à l’aide de l’API REST de moniteur Azure, utilisez « 2016-03-01 » en tant que la version de l’API.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Pour une application de logique d’Azure, les définitions de mesure devrait ressembler à la capture d’écran suivante :

![ALT « Vue JSON de la réponse de la définition de métriques ».](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Pour plus d’informations, consultez la documentation de [répertorier les définitions de mesure d’une ressource dans l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Récupérer les valeurs métriques
Une fois que les définitions de mesure disponibles sont connues, il est alors possible de récupérer les valeurs métriques liées. Utiliser nom 'valeur' de la métrique (pas ' localizedValue') pour toutes les demandes de filtrage (par exemple, récupérer les points de données métriques « Heure CPU » et « Demande »). Si aucun filtre n’est spécifiée, la métrique par défaut est retournée.

>[AZURE.NOTE] Pour récupérer les valeurs de mesure à l’aide de l’API REST de moniteur Azure, utilisez « 2016-06-01 » en tant que la version de l’API.

**Méthode**: obtenir

**URI de la demande**:_{nom de groupe de ressource}_/providers/_{ressource-fournisseur-namespace}_https://management.azure.com/subscriptions/_{id d’abonnement}_/resourceGroups//_{type de ressource}_/_{nom de ressource}_/providers/microsoft.insights/metrics?$filter=_{filter}_& version de l’api =_{apiVersion}_

Par exemple, pour récupérer les points de données métriques de RunsSucceeded pour la plage de temps donnée et pour un grain de temps d’une heure, la demande est comme suit :

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

Le résultat devrait ressembler à l’exemple suivant capture d’écran :

![ALT « Réponse JSON montrant la valeur métrique du temps de réponse moyen »](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Pour récupérer plusieurs points de données ou d’agrégation, ajoutez les noms de la définition de métriques et les types d’agrégation pour le filtre, comme illustré dans l’exemple suivant :

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Utiliser ARMClient
Une alternative à l’utilisation de PowerShell (comme indiqué ci-dessus), consiste à utiliser des [ARMClient](https://github.com/projectkudu/ARMClient) sur votre ordinateur Windows. ARMClient gère automatiquement de l’authentification Active Directory Azure (et le jeton résultant de JWT). Les étapes suivantes décrivent l’utilisation de ARMClient pour extraire des données de mesure :

1. Installer [Chocolatey](https://chocolatey.org/) et [ARMClient](https://github.com/projectkudu/ARMClient).

2. Dans une fenêtre de terminal, tapez _connexion de armclient.exe_. Il vous invite à ouvrir une session Azure.

3. Type de _armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. Type de _armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![ALT « À l’aide de ARMClient pour travailler avec l’API REST de surveillance Azure »](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>Récupérer l’ID de ressource
À l’aide de l’API REST peut vraiment aider à comprendre les définitions de mesure disponibles, granularité et valeurs connexes. Cette information est utile lors de l’utilisation de la [Bibliothèque de gestion Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Le code précédent, l’ID de ressource à utiliser est le chemin d’accès complet à la ressource Azure souhaitée. Par exemple, pour interroger par rapport à une application Web de Azure, l’ID de ressource serait :

*/Subscriptions/{Subscription-ID}/resourceGroups/{Resource-Group-Name}/Providers/Microsoft.Web/sites/{Site-Name}/*

La liste suivante contient quelques exemples de formats d’ID de ressource pour les différentes ressources Azure :

* **IoT Hub** - /subscriptions/_{id d’abonnement}_/resourceGroups/_{nom de groupe de ressource}_/providers/Microsoft.Devices/IotHubs/_{iot-hub-name}_

* **Pool de SQL élastique** - /subscriptions/_{id d’abonnement}_/resourceGroups/_{nom de groupe de ressource}_/providers/Microsoft.Sql/servers/_{pool-db}_/elasticpools/_{sql-pool-name}_

* **Base de données SQL (v12)** : /subscriptions/_{id d’abonnement}_/resourceGroups/_{nom de groupe de ressource}_/providers/Microsoft.Sql/servers/_{nom serveur}_/databases/_{nom de la base de données}_

* **Bus de Service** - /subscriptions/_{id d’abonnement}_/resourceGroups/_{nom de groupe de ressource}_/providers/Microsoft.ServiceBus/_{namespace}_/_{servicebus-name}_

* **Jeux d’échelle VM** - /subscriptions/_{id d’abonnement}_/resourceGroups/_{nom de groupe de ressource}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{vm-name}_

* **Ordinateurs virtuels** - /subscriptions/_{id d’abonnement}_/resourceGroups/_{nom de groupe de ressource}_/providers/Microsoft.Compute/virtualMachines/_{vm-name}_

* **Événement concentrateurs** - /subscriptions/_{id d’abonnement}_/resourceGroups/_{nom de groupe de ressource}_/providers/Microsoft.EventHub/namespaces/_{eventhub-namespace}_


Il existe des approches alternatives à la récupération de l’ID de ressource, y compris avec l’Explorateur de ressources Azure, affichage de la ressource souhaitée dans le portail Azure et via PowerShell ou l’interface CLI d’Azure.

### <a name="azure-resource-explorer"></a>Explorateur de ressources Azure
Pour rechercher l’ID de ressource d’une ressource de votre choix, une approche utile est d’utiliser l’outil [Explorateur de ressources Azure](https://resources.azure.com) . Accédez à la ressource souhaitée et observez l’ID indiqué, comme dans la capture d’écran suivante :

![ALT « Ressource Azure Explorer »](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure portal
L’ID de ressource peut également être obtenue à partir du portail Azure. Pour ce faire, accédez à la ressource souhaitée et sélectionnez Propriétés. L’ID de ressource est affiché dans la lame de propriétés, comme illustré dans la capture d’écran suivante :

![ALT « N° ressource affichée dans la lame de propriétés dans le portail Azure »](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>PowerShell Azure
L’ID de ressource peut être récupérée à l’aide des applets de commande PowerShell de Azure ainsi. Par exemple, pour obtenir l’ID de ressource pour une application Web de Azure, exécutez l’applet de commande Get-AzureRmWebApp, comme dans la capture d’écran suivante :

![ALT « N° ressource obtenu via PowerShell »](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>CLI Azure
Pour récupérer l’ID de ressource à l’aide de la CLI d’Azure, exécute la commande « show webapp azure » spécifiant le '--json' option, comme illustré dans la capture d’écran suivante :

![ALT « N° ressource obtenu via PowerShell »](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Récupérer des données de journal d’activité
En plus de l’utilisation des définitions de métriques et valeurs associées, il est également possible de les récupérer plus intéressantes liées aux ressources Azure. Par exemple, il est possible d’interroger les données de [journal d’activité](https://msdn.microsoft.com/library/azure/dn931934.aspx) . L’exemple suivant illustre l’utilisation de l’API de reste de moniteur de Azure pour interroger les données de journal des activités au sein d’une plage de dates spécifique pour un abonnement Azure :

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Étapes suivantes
* Consultez [vue d’ensemble de la surveillance](monitoring-overview.md).
* Permet d’afficher les [mesures prises en charge avec le moniteur d’Azure](monitoring-supported-metrics.md).
* Passez en revue la [Référence de l’API Microsoft Azure moniteur reste](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Passez en revue la [bibliothèque de gestion Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
