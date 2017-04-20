<properties
    pageTitle="Activer les diagnostics de Services en nuage à l’aide de PowerShell Azure | Microsoft Azure"
    description="Apprenez à activer les diagnostics pour les services en nuage à l’aide de PowerShell"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Activer les diagnostics de Services en nuage Azure à l’aide de PowerShell

Vous pouvez collecter des données de diagnostic comme les journaux d’application, le compteur de performance etc. à partir d’un Service en nuage à l’aide de l’extension de Diagnostics d’Azure. Cet article décrit comment faire pour activer l’extension d’Azure Diagnostics pour un Service en nuage à l’aide de PowerShell.  Pour la configuration requise pour cet article, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Activer l’extension de diagnostics dans le cadre du déploiement d’un Service en nuage

Cette approche de bonne pour le type d’intégration continue de scénarios où l’extension de diagnostics peut être activée dans le cadre du déploiement du service de cloud. Lors de la création d’un nouveau déploiement de Cloud Service, vous pouvez activer l’extension de diagnostics en passant le paramètre *ExtensionConfiguration* de l’applet de commande [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) . Le paramètre *ExtensionConfiguration* prend un tableau de configurations de tests de diagnostic qui peuvent être créés à l’aide de l’applet de commande [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) .

L’exemple suivant montre comment vous pouvez activer des diagnostics pour un service cloud avec un WebRole et un WorkerRole chacun possédant une configuration différents tests de diagnostic.

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

Si le fichier de configuration de diagnostics spécifie un élément StorageAccount avec un nom de compte de stockage, l’applet de commande New-AzureServiceDiagnosticsExtensionConfig utilise automatiquement ce compte de stockage. Pour ce faire, le compte de stockage doit être dans le même abonnement comme Service nuage en cours de déploiement.

À partir d’Azure SDK 2.6 à publier par les fichiers de configuration d’extension générés par MSBuild sortie cible inclut le nom du compte de stockage basé sur la chaîne de configuration de diagnostics spécifiée dans le fichier de configuration de service (.cscfg). Le script ci-dessous vous montre comment analyser les fichiers de configuration d’Extension à partir de la sortie de cible de publication et configurer l’extension des tests de diagnostic pour chaque rôle lors du déploiement du service de cloud.

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

En ligne de Visual Studio utilise une approche similaire pour deploymnts automatisée des Services en nuage avec l’extension de tests de diagnostic. Pour obtenir un exemple complet, consultez [Publication-AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) .

Si aucun StorageAccount n’a été spécifié dans la configuration de diagnostics, vous devez passer le paramètre StorageAccountName à l’applet de commande. Si le paramètre StorageAccountName est spécifié, l’applet de commande utilise toujours le compte de stockage qui est spécifié dans le paramètre et non celle qui est spécifiée dans le fichier de configuration de diagnostics.

Si le compte de stockage de diagnostics est dans un autre abonnement à partir du Service de Cloud, vous devez passer explicitement dans les paramètres StorageAccountName et StorageAccountKey pour l’applet de commande. Le paramètre StorageAccountKey n’est pas nécessaire lorsque le compte de stockage de diagnostics est dans le même abonnement, comme la cmdlet peut automatiquement demander et définir la valeur de clé lors de l’activation de l’extension des tests de diagnostic. Toutefois, si le compte de stockage de diagnostics est dans un autre abonnement, l’applet de commande n’est peut-être pas en mesure d’obtenir la clé automatiquement et vous devez spécifier explicitement la clé via le paramètre StorageAccountKey.

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Activer l’extension des tests de diagnostic sur un Service Cloud existant

Vous pouvez utiliser l’applet de commande [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) pour activer ou mettre à jour la configuration des tests de diagnostic sur un Service en nuage qui est déjà en cours d’exécution.


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>Obtenir la configuration de l’extension de diagnostic en cours
Pour obtenir la configuration actuelle de diagnostics pour un service en nuage, utilisez l’applet de commande [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) .

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>Supprimer l’extension de diagnostics
Pour désactiver les tests de diagnostic sur un service en nuage, vous pouvez utiliser l’applet de commande [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) .

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Si vous avez activé l’extension de diagnostic que vous pouvez supprimer l’extension à l’aide de *AzureServiceDiagnosticsExtension-supprimer* sans le paramètre de *rôle* à l’aide de *Set-AzureServiceDiagnosticsExtension* ou *Nouvelle-AzureServiceDiagnosticsExtensionConfig* sans le paramètre de *rôle* . Si le paramètre de *rôle* a été utilisé lors de l’activation de l’extension puis il doit également être utilisé lors de la suppression de l’extension.

Pour supprimer l’extension de tests de diagnostic de chaque rôle individuel :

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation des diagnostics de Windows Azure et d’autres techniques pour résoudre les problèmes, consultez [Activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](cloud-services-dotnet-diagnostics.md).
- Le [Schéma de Configuration de Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) explique les différentes options de configuration xml pour l’extension des tests de diagnostic.
- Pour savoir comment activer l’extension de diagnostics pour les ordinateurs virtuels, voir [créer un ordinateur virtuel Windows avec la surveillance et de diagnostic à l’aide du modèle de gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
