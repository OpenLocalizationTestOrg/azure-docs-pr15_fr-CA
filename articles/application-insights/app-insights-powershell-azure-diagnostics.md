<properties
    pageTitle="Utilisation de PowerShell pour installation perspectives d’Application dans un Azure | Microsoft Azure"
    description="Automatiser la configuration des Diagnostics d’Azure au canal, aux analyses de l’Application."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Utilisation de PowerShell pour configurer les perspectives d’Application pour une application web Azure

[Microsoft Azure](https://azure.com) peut être [configuré pour envoyer des Diagnostics d’Azure](app-insights-azure-diagnostics.md) aux [Analyses d’Application Visual Studio](app-insights-overview.md). Les tests de diagnostic se rapportent à des Services en nuage Azure et d’Azure VM. Elles complètent la télémétrie que vous envoyez à partir de l’application à l’aide du SDK de perspectives d’Application. Dans le cadre de l’automatisation du processus de création de nouvelles ressources dans Azure, vous pouvez configurer des diagnostics à l’aide de PowerShell.

## <a name="azure-template"></a>Modèle Azure

Si l’application web est dans Azure et créez vos ressources à l’aide d’un modèle de gestionnaire de ressources Azure, vous pouvez configurer les perspectives de l’Application en ajoutant ceci au nœud de ressources :

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-nom de la ressource d’idées d’Application
* `myWebAppName`-l’id de l’application web


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Activer l’extension de diagnostics dans le cadre du déploiement d’un Service en nuage

Le `New-AzureDeployment` applet de commande a un paramètre `ExtensionConfiguration`, qui accepte un tableau de configurations de tests de diagnostic. Vous pouvez créer à l’aide de la `New-AzureServiceDiagnosticsExtensionConfig` applet de commande. Par exemple :

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Activer l’extension des tests de diagnostic sur un Service Cloud existant

Sur un service existant, vous devez utiliser `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obtenir la configuration de l’extension de diagnostic en cours

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Supprimer l’extension de diagnostics

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Si vous avez activé l’extension de diagnostics à l’aide de le `Set-AzureServiceDiagnosticsExtension` ou `New-AzureServiceDiagnosticsExtensionConfig` sans le paramètre de rôle, vous pouvez supprimer l’extension à l’aide de `Remove-AzureServiceDiagnosticsExtension` sans le paramètre de rôle. Si le paramètre de rôle a été utilisé lors de l’activation de l’extension puis il doit également être utilisé lors de la suppression de l’extension.

Pour supprimer l’extension de tests de diagnostic de chaque rôle individuel :

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Voir aussi

* [Surveiller Azure Cloud Services apps avec aperçus de l’Application](app-insights-cloudservices.md)
* [Envoyer les Diagnostics de Windows Azure pour les perspectives de l’Application](app-insights-azure-diagnostics.md)
* [Automatiser la configuration des alertes](app-insights-powershell-alerts.md)

