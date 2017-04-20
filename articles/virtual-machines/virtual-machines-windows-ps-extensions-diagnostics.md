<properties
    pageTitle="Permet d’activer les Diagnostics de Azure dans un ordinateur virtuel exécutant Windows PowerShell | Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="Découvrez comment utiliser PowerShell pour activer les Diagnostics de Azure dans un ordinateur virtuel exécutant Windows"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Permet d’activer les Diagnostics de Azure dans un ordinateur virtuel exécutant Windows PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Diagnostics de Windows Azure est la capacité d’Azure qui active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l’extension de tests de diagnostic pour collecter des données de diagnostics comme les journaux des applications ou des compteurs de performance à partir d’une Azure machine virtuelle (VM qui s’exécute sous Windows). Cet article décrit comment utiliser Windows PowerShell pour permettre l’extension des tests de diagnostic d’un ordinateur virtuel. Pour la configuration requise pour cet article, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Activer l’extension de diagnostics si vous utilisez le modèle de déploiement du Gestionnaire de ressources

Vous pouvez activer l’extension de diagnostic lors de la création d’une machine virtuelle de Windows via le modèle de déploiement du Gestionnaire de ressources Azure en ajoutant de la configuration de l’extension au modèle de gestionnaire de ressources. Voir [Création d’une machine virtuelle de Windows avec surveillance et diagnostic en utilisant le modèle de gestionnaire de ressources Azure](virtual-machines-windows-extensions-diagnostics-template.md).

Pour activer l’extension de tests de diagnostic sur un ordinateur virtuel existant qui a été créé via le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser l’applet de commande [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) PowerShell comme indiqué ci-dessous.


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* est le chemin d’accès au fichier qui contient la configuration des diagnostics dans le fichier XML, comme indiqué dans l' [exemple](#sample-diagnostics-configuration) ci-dessous.  

Si le fichier de configuration de diagnostics spécifie un élément **StorageAccount** avec un nom de compte de stockage, le script *AzureRMVMDiagnosticsExtension-ensemble* définira automatiquement l’extension de diagnostics pour envoyer des données de diagnostic pour ce compte de stockage. Pour ce faire, le compte de stockage doit être dans le même abonnement comme la machine virtuelle.

Si aucune **StorageAccount** a été spécifié dans la configuration de diagnostics, vous devez passer le paramètre *StorageAccountName* à l’applet de commande. Si le paramètre *StorageAccountName* est spécifié, l’applet de commande utilise toujours le compte de stockage qui est spécifié dans le paramètre et non à celui qui est spécifié dans le fichier de configuration de diagnostics.

Si le compte de stockage de diagnostics est dans un autre abonnement à partir de la machine virtuelle, vous devez passer explicitement dans les paramètres *StorageAccountName* et *StorageAccountKey* pour l’applet de commande. Le paramètre *StorageAccountKey* n’est pas nécessaire lorsque le compte de stockage de diagnostics est dans le même abonnement, comme la cmdlet peut automatiquement demander et définir la valeur de clé lors de l’activation de l’extension des tests de diagnostic. Toutefois, si le compte de stockage de diagnostics est dans un autre abonnement, l’applet de commande n’est peut-être pas en mesure d’obtenir la clé automatiquement et vous devez spécifier explicitement la clé via le paramètre *StorageAccountKey* .  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Une fois que l’extension des diagnostics est activée sur un ordinateur virtuel, vous pouvez obtenir les paramètres actuels à l’aide de l’applet de commande [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) .

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

L’applet de commande renvoie *PublicSettings*, qui contient la configuration XML dans un format codé en base 64. Pour lire le code XML, vous devez le décoder.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

L’applet de commande [Remove-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) peut être utilisé pour supprimer l’extension de tests de diagnostic de la machine virtuelle.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Activer l’extension de diagnostics si vous utilisez le modèle de déploiement classique

Vous pouvez utiliser l’applet de commande [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) pour activer une extension de tests de diagnostic sur un ordinateur virtuel que vous créez dans le modèle de déploiement classique. L’exemple suivant montre comment créer une nouvelle machine virtuelle via le modèle de déploiement classique avec l’extension de diagnostics activée.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Pour activer l’extension de tests de diagnostic sur un ordinateur virtuel existant qui a été créé via le modèle de déploiement classique, utilisez d’abord l’applet de commande [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) à la configuration de la machine virtuelle. Mettre à jour la configuration de la machine virtuelle afin d’inclure l’extension de diagnostics à l’aide de l’applet de commande [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) . Enfin, appliquez la configuration mise à jour de la machine virtuelle à l’aide de [AzureVM de la mise à jour](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Exemple de configuration de diagnostics

Le code XML suivant peut être utilisé pour la configuration de diagnostics publics avec les scripts ci-dessus. Cet exemple de configuration transférera les différents compteurs de performance pour le compte de stockage de diagnostics, ainsi que les erreurs de l’application, de sécurité et les canaux de système dans les journaux des événements Windows et les erreurs dans les journaux d’infrastructure de diagnostics.

La configuration doit être mis à jour pour inclure les éléments suivants :

- L’attribut *resourceID* de l’élément de **mesures** doit être mis à jour avec l’ID de ressource de la machine virtuelle.
    - L’ID de ressource peut être construit en utilisant le modèle suivant : « abonnements / {*ID d’abonnement pour l’abonnement avec la machine virtuelle*} /resourceGroups/ {*nom de la machine virtuelle resourcegroup*} / providers/Microsoft.Compute/virtualMachines/ {*le nom de la machine virtuelle*} ».
    - Par exemple, si l’ID d’abonnement pour l’abonnement dans lequel l’ordinateur virtuel est en cours d’exécution est **11111111-1111-1111-1111-111111111111**, le nom du groupe de ressources pour le groupe de ressources est **MyResourceGroup**et le nom de la machine virtuelle est **MyWindowsVM**, la valeur de *resourceID* serait :

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - Pour plus d’informations sur la façon de métriques sont générées en fonction de la configuration des compteurs et des métriques de performances, consultez [Azure Diagnostics métriques de stockage](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).

- L’élément **StorageAccount** doit être mis à jour avec le nom du compte de stockage des tests de diagnostic.

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur l’utilisation de la fonctionnalité de diagnostic d’Azure et d’autres techniques pour résoudre les problèmes, consultez [Activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](../cloud-services/cloud-services-dotnet-diagnostics.md).
- [Schéma de configurations de tests de diagnostic](https://msdn.microsoft.com/library/azure/mt634524.aspx) explique les différentes options de configuration XML pour l’extension des tests de diagnostic.
