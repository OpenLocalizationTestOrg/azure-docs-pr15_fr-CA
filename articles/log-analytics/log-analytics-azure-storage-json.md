<properties
    pageTitle="Analyser les journaux de diagnostic Azure à l’aide du journal Analytique | Microsoft Azure"
    description="Analytique de journal peut lire les journaux des services Azure qui écrit des journaux de diagnostics Azure BLOB de stockage au format JSON."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analyser les journaux de diagnostic Azure à l’aide du journal Analytique

Analytique de journal peut collecter les journaux des services Azure suivants qui écrivent des [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) dans le stockage blob au format JSON :

+ Automation (aperçu)
+ Clé coffre-fort (aperçu)
+ Passerelle d’application (aperçu)
+ Groupe de sécurité réseau (aperçu)

Les sections suivantes vous guident tout au long de l’utilisation de PowerShell pour :

+ Configuration Analytique de journal pour collecter les journaux de stockage de chaque ressource  
+ Activer la solution Analytique de journal pour le service Azure

Avant journal Analytique peut collecter des données relatives à ces ressources, les diagnostics de Windows Azure doit être activé. Utilisez le `Set-AzureRmDiagnosticSetting` applet de commande pour activer la journalisation.

Consultez les articles suivants pour plus d’informations sur l’activation de l’enregistrement des diagnostics :

+ [Chambre forte de clé](../key-vault/key-vault-logging.md)
+ [Passerelle d’application](../application-gateway/application-gateway-diagnostics.md)
+ [Groupe de sécurité réseau](../virtual-network/virtual-network-nsg-manage-log.md)

Cette documentation inclut également des informations sur :

+ Résolution des problèmes liés à la collecte de données
+ Arrêt de la collecte de données

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configuration Analytique de journal pour collecter les journaux de diagnostic Azure

Collecte des journaux pour ces services et de l’activation de la solution à visualiser les journaux sont effectuée à l’aide de scripts PowerShell.

L’exemple suivant active la journalisation de toutes les ressources prises en charge

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Pour certaines ressources, il est possible d’effectuer la configuration précédente à partir du portail Azure à l’aide de la procédure décrite dans [les journaux de diagnostic de vue d’ensemble d’Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configuration Analytique de journal pour collecter les journaux de diagnostic Azure

Nous avons fourni un module de script PowerShell qui exporte les deux applets de commande pour vous aider à configuration Analytique de journal :

1. `Add-AzureDiagnosticsToLogAnalyticsUI`vous demande d’entrée et est en mesure de définir des configurations simples
2. `Add-AzureDiagnosticsToLogAnalytics`prend les ressources à surveiller en entrée et configure ensuite le journal Analytique  

### <a name="pre-requisites"></a>Conditions préalables

1. PowerShell Azure avec la version 1.0.8 ou ultérieure des applets de commande des perspectives opérationnelles.
  - [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md)
  - Vérifiez votre version d’applets de commande :`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. L’enregistrement des diagnostics est configuré pour la ressource Azure que vous souhaitez surveiller. Utilisez `Set-AzureRmDiagnosticSetting` ou consultez [Analytique du journal utilisé pour collecter des données à partir des comptes de stockage Azure](log-analytics-azure-storage.md) pour savoir comment activer les tests de diagnostic.
3. Un espace de travail du [Journal Analytique](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. Le module PowerShell de AzureDiagnosticsAndLogAnalytics
  - Télécharger le module [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) à partir de la galerie de PowerShell

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Option 1 : Exécutez les Scripts de Configuration Interactive

Ouvrez PowerShell et exécutez :

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Vous êtes affiche une liste de sélections disponibles et compte tenu d’une invite de commandes pour effectuer votre sélection.
Vous êtes invité à effectuer des sélections pour chacun des éléments suivants :

+ Types de ressources (Services Azure) pour collecter des journaux à partir de
+ Pour collecter des journaux à partir des instances de ressource
+ Espace de travail Analytique pour collecter les données de session

Après l’exécution de ce script, vous devriez voir enregistrements de journal Analytique environ 30 minutes après que les nouvelles données de diagnostics sont écrite dans le stockage. Si les enregistrements ne sont pas disponibles après cette heure reportez-vous à la section de dépannage ci-dessous.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Option 2 : Créer une liste de ressources et de les transmettre à l’applet de commande de configuration

Vous pouvez créer une liste de ressources qui ont des diagnostics de Windows Azure activé, puis passer les ressources à l’applet de commande de configuration.

Vous pouvez voir des informations supplémentaires sur l’applet de commande en exécutant `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Pour en savoir plus sur OMS [Applets de commande PowerShell journal Analytique](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] Si les espaces de travail et les ressources dans les différentes souscriptions d’Azure, basculez entre eux selon vos besoins à l’aide de`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Après l’exécution de ce script, vous devriez voir enregistrements de journal Analytique environ 30 minutes après que les nouvelles données de diagnostics sont écrite dans le stockage. Si les enregistrements ne sont pas disponibles après cette heure reportez-vous à la section de dépannage ci-dessous.  

>[AZURE.NOTE] La configuration n’est pas visible dans le portail Azure. Vous pouvez vérifier la configuration à l’aide de la `Get-AzureRmOperationalInsightsStorageInsight` applet de commande.  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Arrêt du journal Analytique à partir de journaux de diagnostic collecte Azure

Pour supprimer la configuration Analytique de journal pour une ressource, utilisez le `Remove-AzureRmOperationalInsightsStorageInsight` applet de commande.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Dépannage de la configuration pour les journaux de diagnostic Azure

*Problème*

Le message d’erreur suivant s’affiche lors de la configuration d’une ressource qui est actuellement connecté à stockage classique :

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Résolution*

Se connecter à l’API pour le modèle de déploiement classique avec`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Résolution des problèmes de collecte de données pour les journaux de diagnostic Azure

Si vous ne voyez pas de données pour votre ressource Azure dans journal Analytique, vous pouvez utiliser les étapes de dépannage suivantes :

+ Vérifiez les données qui circulent sur le compte de stockage
+ Vérifiez que la solution Analytique de journal pour le service est activée.
+ Vérifiez que le journal Analytique est configuré pour lire à partir du stockage
+ Mettre à jour la clé de compte de stockage

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Vérifiez les données se dirige vers le compte de stockage

Vous pouvez vérifier si les données se dirige vers le compte de stockage avec un outil qui permet de parcourir le stockage Azure (par exemple Visual Studio) ou à l’aide de PowerShell.

Pour trouver le compte de stockage, la ressource est configurée pour ouvrir une session pour utiliser PowerShell suivante :

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Le conteneur de stockage utilisé par les tests de diagnostic Azure a un nom dont le format est de :  

`insights-logs-<Category>`

Voir la rubrique [à l’aide des applets de commande de stockage pour vérifier un conteneur pour les données récentes](../storage/storage-powershell-guide-full.md) pour en savoir plus sur l’affichage du contenu du compte de stockage.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Vérifiez que la solution Analytique de journal pour le service est activée.

Si vous utilisez `Add-AzureDiagnosticsToLogAnalyticsUI`, la solution de journal Analytique correcte est activée automatiquement pour vous.

Pour vérifier si une solution est activée, exécutez PowerShell suivante :

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Si la solution n’est pas activée, vous pouvez l’activer à l’aide de PowerShell suivante :

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Pour trouver le nom de la solution à activer pour chaque type de ressource, utilisez PowerShell suivant (cette variable est disponible une fois que vous avez importé le module) :

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Vérifiez que le journal Analytique est configuré pour lire à partir du stockage

Si vous ajoutez des ressources supplémentaires Azure, vous devez activer l’enregistrement des Diagnostics pour les et configurer des journaux Analytique pour les.
Pour vérifier les ressources et les comptes de stockage Analytique du journal est configuré pour collecter des journaux, utilisez PowerShell suivante :

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Mise à jour de la clé de stockage

Si vous modifiez la clé pour le compte de stockage, la configuration du journal Analytique doit également être mis à jour avec la nouvelle clé.
Vous pouvez mettre à jour la configuration du journal Analytique avec une nouvelle clé à l’aide de PowerShell suivante :

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Pour rechercher le nom d’un aperçu de stockage, utilisez le `Get-AzureRmOperationalInsightsStorageInsight` applet de commande, comme indiqué dans les exemples précédents.

## <a name="next-steps"></a>Étapes suivantes

- [Stockage de blob utiliser pour IIS et le stockage de table pour les événements](log-analytics-azure-storage-iis-table.md) pour lire les journaux pour Azure services que diagnostics d’écriture dans les journaux IIS écrits dans le stockage blob ou de stockage de table.
- [Activer les Solutions](log-analytics-add-solutions.md) pour fournir des données.
- [Utilisation des requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.
