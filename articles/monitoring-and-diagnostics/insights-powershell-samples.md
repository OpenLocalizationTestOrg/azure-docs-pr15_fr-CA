<properties
    pageTitle="Exemples de démarrage rapide de moniteur PowerShell Azure. | Microsoft Azure"
    description="Utilisation de PowerShell pour accéder aux fonctionnalités du moniteur d’Azure comme échelle automatique, alertes, webhooks et les journaux d’activité de recherche."
    authors="kamathashwin"
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
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemples de démarrage rapide moniteur PowerShell Azure

Cet article explique les exemples de commandes PowerShell pour vous aider à accéder aux fonctionnalités du moniteur Azure. Moniteur Azure permet à l’échelle automatique des Services de nuage, les ordinateurs virtuels et les applications Web et pour envoyer des notifications d’alerte ou appeler des URL web en fonction des valeurs de données télémétriques configuré.

>[AZURE.NOTE] Moniteur Azure est le nouveau nom de ce qui fut appelé « Perspectives Azure » jusqu'à ce que le 25 septembre 2016. Cependant, les espaces de noms et, par conséquent, les commandes ci-dessous contiennent encore les « perspectives ».

## <a name="set-up-powershell"></a>Paramétrage de PowerShell
Si vous ne l’avez pas déjà, définir PowerShell pour s’exécuter sur votre ordinateur. Pour plus d’informations, voir [Comment faire pour installer et configurer de PowerShell](../powershell-install-configure.md) .

## <a name="examples-in-this-article"></a>Exemples de cet article

Les exemples dans cet article illustrent comment vous pouvez utiliser les applets de commande de moniteur d’Azure. Vous pouvez également consulter la liste complète des applets de commande PowerShell de moniteur Azure aux [Cmdlets de moniteur d’Azure (aperçu)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).


## <a name="sign-in-and-use-subscriptions"></a>Se connecter et d’utiliser des abonnements

Tout d’abord vous connecter à votre abonnement Azure.

```PowerShell
Login-AzureRmAccount
```

Vous devez vous connecter. Une fois que vous le faites, votre compte, TenantId et par défaut Id d’abonnement sont affichent. Toutes les applets de commande Azure fonctionnent dans le cadre de votre abonnement par défaut. Pour afficher la liste des abonnements que vous avez accès, utilisez la commande suivante.

```PowerShell
Get-AzureRmSubscription
```

Pour modifier votre contexte de travail à un autre abonnement, utilisez la commande suivante.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Récupérer les journaux d’Audit pour un abonnement
Utilisez le `Get-AzureRmLog` applet de commande.  Voici quelques exemples courants.

Obtenir des entrées du journal à partir de cette date/heure pour présenter :

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obtenir les entrées de journal entre une plage de date/heure :

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir les entrées du journal d’un groupe de ressources spécifique :

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obtenir les entrées du journal auprès d’un fournisseur de ressources spécifique entre une plage de date/heure :

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir toutes les entrées de journal avec un appelant spécifique :

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

La commande suivante extrait les 1000 derniers événements dans le journal d’audit :

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`prend en charge de nombreux autres paramètres. Consultez le `Get-AzureRmLog` référence pour plus d’informations.

>[AZURE.NOTE] `Get-AzureRmLog`fournit uniquement des 15 jours d’historique. Le paramètre **- MaxEvents** vous permet d’interroger les derniers événements de N, au-delà de quinze jours. Pour accès les événements datant de plus de 15 jours, utilisez l’API REST ou le Kit de développement (C# exemple à l’aide du SDK). Si vous n’incluez pas **d’heure de début**, la valeur par défaut est **l’heure de fin** moins d’une heure. Si vous n’incluez pas **d’heure de fin**, la valeur par défaut est heure actuelle. Toutes les heures sont en UTC.

## <a name="retrieve-alerts-history"></a>Récupérer l’historique des alertes
Pour afficher tous les événements d’alerte, vous pouvez interroger les journaux d’Azure le Gestionnaire de ressources à l’aide de la procédure ci-après.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Pour afficher l’historique d’une règle d’alerte spécifique, vous pouvez utiliser la `Get-AzureRmAlertHistory` applet de commande, en passant l’ID de ressource de la règle d’alerte.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Le `Get-AzureRmAlertHistory` applet de commande prend en charge les différents paramètres. Plus d’informations, reportez-vous à la section [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## <a name="retrieve-information-on-alert-rules"></a>Récupérer des informations sur les règles d’alerte
Toutes les commandes suivantes s’appliquent à un groupe de ressources nommé « montest ».

Permet d’afficher toutes les propriétés de la règle d’alerte :

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Récupérer toutes les alertes sur un groupe de ressources :

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Récupérer toutes les règles d’alerte pour une ressource cible. Par exemple, toutes les règles d’alerte définies sur une machine virtuelle.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`prend en charge les autres paramètres. Pour plus d’informations, reportez-vous à la section [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-alert-rules"></a>Créer des règles d’alerte
Vous pouvez utiliser la `Add-AlertRule` applet de commande pour créer, mettre à jour ou désactiver une règle d’alerte.

Vous pouvez créer des propriétés e-mail et webhook à l’aide de `New-AzureRmAlertRuleEmail` et `New-AzureRmAlertRuleWebhook`, respectivement. Dans l’applet de commande règle d’alerte, affecter ces actions à la propriété **Actions** de la règle d’alerte.

La section suivante contient un exemple qui montre comment créer une règle d’alerte avec des paramètres différents.

### <a name="alert-rule-on-a-metric"></a>Règle d’alerte sur une mesure
Le tableau suivant décrit les paramètres et les valeurs utilisées pour créer une alerte à l’aide d’une mesure.


|paramètre|valeur|
|---|---|
|Nom|  simpletestdiskwrite|
|Emplacement de cette règle d’alerte|   Les États-Unis|
|ResourceGroup| MonTest|
|TargetResourceId|  /Subscriptions/s1/resourceGroups/MonTest/Providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName de l’alerte créée|   \PhysicalDisk (_Total) \Disk écritures/s. Consultez le `Get-MetricDefinitions` applet de commande sur la procédure pour récupérer les noms de mesure exactes sous|
|opérateur|  GreaterThan|
|Valeur de seuil (nombre/sec pour cette mesure)|    1|
|WindowSize (format hh : mm :)|  00:05:00|
|agrégation (statistique de la mesure, qui utilise un nombre moyen, dans ce cas)|  Moyenne|
|e-mails personnalisés (tableau de chaînes)|'foo@example.com','bar@example.com'|
|envoyer un e-mail aux propriétaires, les collaborateurs et les lecteurs|    -SendToServiceOwners|

Créer une action de messagerie

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Créer une action Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Créer la règle d’alerte sur la mesure de % CPU sur une machine virtuelle classique

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Récupérer la règle d’alerte

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

L’applet de commande Add alerte met également à jour la règle, s’il existe déjà une règle d’alerte pour les propriétés spécifiées. Pour désactiver une règle d’alerte, incluez le paramètre **- DisableRule**.

### <a name="alert-on-audit-log-event"></a>Alerte sur les événements du journal d’audit

>[AZURE.NOTE] Cette fonctionnalité est toujours dans l’aperçu.

Dans ce scénario, vous allez envoyer e-mail lorsqu’un site Web est démarré dans mon abonnement dans groupe de ressources *abhingrgtest123*.

Programme d’installation une règle de courrier électronique

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Programme d’installation une règle webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Créer la règle sur l’événement

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Récupérer la règle d’alerte

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

Le `Add-AlertRule` applet de commande permet à divers autres paramètres. Plus d’informations, consultez [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obtenir une liste des statistiques disponibles pour les alertes
Vous pouvez utiliser la `Get-AzureRmMetricDefinition` applet de commande pour afficher la liste de toutes les mesures pour une ressource spécifique.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

L’exemple suivant génère une table avec la nom de la mesure et l’unité pour lui.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Une liste complète des options disponibles pour `Get-AzureRmMetricDefinition` est disponible à l’adresse [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## <a name="create-and-manage-autoscale-settings"></a>Créer et gérer les paramètres d’échelle automatique
Une ressource, comme une application Web, VM, Service Cloud ou VM échelle définie peut avoir qu’un seul paramètre autoscale configuré pour cela.
Toutefois, chaque paramètre échelle automatique peut avoir plusieurs profils. Par exemple, un pour un profil en fonction des performances une échelle et une seconde pour une planification basée profil. Chaque profil peut avoir plusieurs règles configurées sur elle. Pour plus d’informations sur l’échelle automatique, consultez [l’échelle d’une Application](../cloud-services/cloud-services-how-to-scale.md).

Voici les étapes que nous allons utiliser :

1. Créer une ou plusieurs règles.
2. Créer des profils de mappage les règles que vous avez créé précédemment pour les profils.
3. Facultatif : Créez des notifications pour l’échelle automatique en configurant les propriétés webhook et de la messagerie.
4. Créer un paramètre d’échelle automatique avec un nom sur la ressource cible en mappant les profils et les notifications que vous avez créé dans les étapes précédentes.

Les exemples suivants vous montrent comment vous pouvez créer un paramètre d’échelle d’une échelle de machine virtuelle pour un système d’exploitation Windows à l’aide de la métrique de l’utilisation du processeur.

Tout d’abord, créez une règle horizontale, avec une augmentation de nombre d’instance.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

Ensuite, créez une règle d’échelle de, avec une baisse de nombre d’instance.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Ensuite, créez un profil pour les règles.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Créer une propriété webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Créer la propriété de notification pour le paramètre échelle automatique, y compris les e-mails et le webhook que vous avez créé précédemment.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Enfin, créez le paramètre d’échelle à ajouter le profil que vous avez créé ci-dessus.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Pour plus d’informations sur la gestion des paramètres d’échelle automatique, consultez [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historique de l’échelle automatique
L’exemple suivant vous montre comment vous pouvez consulter autoscale récent et des événements d’alerte. La recherche du journal d’audit permet d’afficher l’historique de l’échelle.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Vous pouvez utiliser la `Get-AzureRmAutoScaleHistory` applet de commande pour récupérer l’historique de l’échelle.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Pour plus d’informations, consultez [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Afficher les détails d’un paramètre d’échelle automatique
Vous pouvez utiliser la `Get-Autoscalesetting` applet de commande pour récupérer des informations sur le paramètre échelle automatique.

L’exemple suivant affiche les détails sur tous les paramètres d’échelle automatique dans le groupe de ressources 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

L’exemple suivant montre des informations détaillées sur tous les paramètres d’échelle automatique dans le groupe de ressources 'myrg1' et en particulier le paramètre échelle automatique nommé « MyScaleVMSSSetting ».

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Supprimer un paramètre d’échelle automatique
Vous pouvez utiliser la `Remove-Autoscalesetting` applet de commande pour supprimer un paramètre d’échelle.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>Gérer les profils de journal pour les journaux d’audit

Vous pouvez créer un *profil de journal* et exporter des données à partir de vos journaux d’audit pour un compte de stockage, et vous pouvez configurer la rétention des données pour celui-ci. Si vous le souhaitez, vous pouvez aussi diffuser les données à votre concentrateur d’événements. Notez que cette fonctionnalité est en cours d’aperçu et vous ne pouvez créer qu’un seul profil de journal par abonnement. Vous pouvez utiliser les applets de commande suivantes avec votre abonnement actuel pour créer et gérer des profils du journal. Vous pouvez également choisir un abonnement particulier. Bien que PowerShell utilise par défaut l’abonnement en cours, vous pouvez toujours modifier ce à l’aide de `Set-AzureRmContext`. Vous pouvez configurer les journaux d’audit pour router les données à n’importe quel compte de stockage ou le concentrateur d’événements au sein de cet abonnement. Les données sont écrites en tant que fichiers blob au format JSON.

### <a name="get-a-log-profile"></a>Obtenir un profil de journal
Pour extraire vos profils de journal existant, utilisez la `Get-AzureRmLogProfile` applet de commande.

### <a name="add-a-log-profile-without-data-retention"></a>Ajouter un profil de journal sans conservation des données

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Supprimer un profil de journal

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Ajouter un profil de journal avec la conservation des données

Vous pouvez spécifier la propriété **- RetentionInDays** avec le nombre de jours, sous la forme d’un entier positif, où les données sont conservées.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Ajouter un profil de journal avec des règles de rétention et EventHub
En plus de routage de vos données de compte de stockage, vous pouvez également le flux à un concentrateur d’événements. Notez que dans cette version d’évaluation et le stockage de la configuration du compte est obligatoire mais le concentrateur d’événements configuration est facultative.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurer les journaux de diagnostic
De nombreux services Azure fournissent des journaux supplémentaires et télémétrie, y compris Azure les groupes de sécurité de réseau, logiciel les équilibreurs de charge, clé de stockage en chambre forte, Azure des Services de recherche, et la logique d’applications et peut être configurés pour enregistrer les données dans votre compte de stockage Azure. Cette opération ne peut être effectuée qu’au niveau d’une ressource et le compte de stockage doit être présent dans la même zone que la ressource cible où les tests de diagnostic est défini.

### <a name="get-diagnostic-setting"></a>Obtenir les paramètres de diagnostic

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Désactiver les paramètres de diagnostic

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Activer le paramètre de diagnostic sans rétention

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Activer le paramètre de diagnostic avec rétention

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Activer le paramètre de diagnostic avec rétention pour une catégorie de journal des événements spécifique

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```
