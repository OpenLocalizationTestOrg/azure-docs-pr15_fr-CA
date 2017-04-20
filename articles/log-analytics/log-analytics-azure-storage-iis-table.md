<properties
    pageTitle="À l’aide du stockage blob pour le stockage IIS et de la table des événements | Microsoft Azure"
    description="Analytique de journal peut lire les journaux des services Azure qui écrivent les tests de diagnostic pour le stockage de table ou journaux IIS écrits dans le stockage blob."
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
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>À l’aide du stockage blob pour IIS et le stockage de table pour les événements

Analytique de journal peut lire les journaux pour les services suivants qui écrivent les tests de diagnostic pour le stockage de table ou journaux IIS écrits dans le stockage blob :

+ TISSU de service de clusters (aperçu)
+ Ordinateurs virtuels
+ Rôles de Web/travailleur

Avant journal Analytique peut collecter des données relatives à ces ressources, les diagnostics de Windows Azure doit être activé.

Une fois les tests de diagnostic sont activés, vous pouvez utiliser le portail Azure ou PowerShell configurer Analytique de journal à collecter les journaux.

Diagnostics de Windows Azure est une extension Azure qui vous permet de collecter des données de diagnostic à partir d’un rôle de travail, du rôle web ou ordinateur virtuel exécuté dans Azure. Les données sont stockées dans un compte de stockage Azure et peuvent ensuite être collectées par journal Analytique.

Pour l’Analytique des journaux collecter ces journaux de Diagnostics d’Azure, les journaux doivent être dans les emplacements suivants :

| Type de journal | Type de ressource | Emplacement |
| -------- | ------------- | -------- |
| Journaux IIS | Ordinateurs virtuels <br> Rôles Web <br> Rôles Worker | logfiles d’iis WAD (stockage Blob) |
| Journal système | Ordinateurs virtuels | LinuxsyslogVer2v0 (Table de stockage) |
| Événements opérationnels du service Fabric | Nœuds de service Fabric | WADServiceFabricSystemEventTable |
| Événements de service Fabric acteur fiable | Nœuds de service Fabric | WADServiceFabricReliableActorEventTable |
| Événements de Service fiable service Fabric | Nœuds de service Fabric | WADServiceFabricReliableServiceEventTable |
| Journaux des événements Windows | Nœuds de service Fabric <br> Ordinateurs virtuels <br> Rôles Web <br> Rôles Worker | WADWindowsEventLogsTable (stockage de Table) |
| Journaux Windows ETW | Nœuds de service Fabric <br> Ordinateurs virtuels <br> Rôles Web <br> Rôles Worker | WADETWEventTable (stockage de Table) |

>[AZURE.NOTE] Les journaux IIS à partir de sites Web d’Azure ne sont actuellement pas pris en charge.

Pour les machines virtuelles, vous avez l’option d’installation de l' [agent de journal Analytique](log-analytics-azure-vm-extension.md) dans votre machine virtuelle pour activer des éclaircissements. En plus d’être en mesure d’analyser les journaux des événements et des journaux IIS, vous pouvez effectuer une analyse plus approfondie, notamment le suivi des modifications de configuration, l’évaluation de SQL et évaluation de la mise à jour.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Activer les diagnostics de Windows Azure dans une machine virtuelle pour collection de journal de journal des événements et IIS

Utilisez la procédure suivante pour activer les diagnostics de Windows Azure dans une machine virtuelle pour une collection de journaux IIS et le journal des événements à l’aide du portail Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Pour activer les diagnostics de Windows Azure dans un ordinateur virtuel avec le portail Azure

1. Installer l’Agent de la machine virtuelle lorsque vous créez une machine virtuelle. Si l’ordinateur virtuel existe déjà, vérifiez que l’Agent de la machine virtuelle est déjà installé.
    - Dans le portail Azure, accédez à l’ordinateur virtuel et sélectionnez **Configuration facultative**, puis **Diagnostics** la valeur **statut** **sur**.

    À la fin, la machine virtuelle a l’extension Azure Diagnostics installé et en cours d’exécution. Cette extension est responsable de la collecte de vos données de tests de diagnostic.

2. Activer l’analyse et configurer l’enregistrement d’événements sur un ordinateur virtuel existant. Vous pouvez activer des diagnostics au niveau de la machine virtuelle. Pour activer les tests de diagnostic et ensuite configurer la journalisation des événements, effectuez les opérations suivantes :
    1. Sélectionnez la machine virtuelle.
    2. Cliquez sur **surveillance**.
    3. Cliquez sur **Diagnostics**.
    4. **L’état** la valeur **ON**.
    5. Sélectionnez chaque journal de diagnostic que vous souhaitez collecter.
    7. Cliquez sur **OK**.

Vous pouvez plus précisément à l’aide de PowerShell d’Azure pour spécifier les événements qui sont écrits dans le stockage Azure. Pour [collecter des données à l’aide des diagnostics de Windows Azure écrit dans les journaux de IIS écrit à blob ou de stockage de table](log-analytics-azure-storage-json.md), reportez-vous à la section.


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Activer les diagnostics de Windows Azure dans un rôle Web de collection d’événements et journaux IIS

[Comment pour activer les Diagnostics dans un Service Cloud](../cloud-services/cloud-services-dotnet-diagnostics.md) pour voir les étapes générales sur l’activation des diagnostics de Windows Azure. Les instructions ci-dessous utilisent ces informations et personnaliser pour une utilisation avec journal Analytique.

Avec les diagnostics de Windows Azure activé :

- Les journaux IIS sont stockés par défaut, avec les données transférées à l’intervalle de transfert scheduledTransferPeriod.
- Par défaut, les journaux d’événements Windows ne sont pas transférés.

### <a name="to-enable-diagnostics"></a>Pour activer les tests de diagnostic

Pour activer les journaux d’événements Windows, ou pour modifier la scheduledTransferPeriod, configurer des Diagnostics Azure à l’aide du fichier de configuration XML (diagnostics.wadcfg), comme illustré à la [étape 4 : créer votre fichier de configuration de Diagnostics et d’installer l’extension](../cloud-services/cloud-services-dotnet-diagnostics.md)

Le fichier de configuration d’exemple suivant collecte des journaux IIS et tous les événements dans les journaux système et d’Application :

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Assurez-vous que votre ConfigurationSettings spécifie un compte de stockage, comme dans l’exemple suivant :

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Les valeurs de **nom de compte** et **AccountKey** sont trouvent dans le portail Azure dans le tableau de bord stockage compte sous Gérer les touches d’accès rapide. Le protocole de la chaîne de connexion doit être **https**.

Une fois la configuration de diagnostic mis à jour est appliquée à votre service cloud et il écrit des diagnostics pour le stockage Azure, vous êtes prêt à configurer le journal Analytique.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Le portail Azure permet de collecter des journaux à partir du stockage Azure

Vous pouvez utiliser le portail Azure pour configurer Analytique des journaux pour collecter les journaux des services Azure suivants :

+ Clusters de tissu de service
+ Ordinateurs virtuels
+ Rôles de Web/travailleur

Dans le portail Azure, accédez à votre espace de travail du journal Analytique et effectuer les tâches suivantes :

1. Cliquez sur *journaux de comptes de stockage*
2. Cliquez sur la tâche *d’Ajout*
3. Sélectionnez le compte de stockage contenant les journaux de diagnostic
  - Ce compte peut être soit un compte de stockage classique, soit un compte de stockage Azure le Gestionnaire de ressources
4. Sélectionnez le Type de données que vous souhaitez collecter dans les journaux
  - Les choix sont les journaux IIS ; Événements ; Journal système (Linux) ; Journaux ETW ; Événements de tissu de service
5. La valeur de la Source est automatiquement remplie avec le type de données et ne peut pas être modifiée.
6. Cliquez sur OK pour enregistrer la configuration

Répétez les étapes 2 à 6 pour les comptes de stockage supplémentaires et les types de données que vous souhaitez Analytique de journal à collecter.

En 30 minutes environ, vous êtes en mesure de voir les données du compte de stockage dans le journal Analytique. Vous verrez uniquement les données qui sont écrites dans le stockage après application de la configuration. Analytique de journal ne lit pas les données existantes à partir du compte de stockage.

>[AZURE.NOTE] Le portail ne valide pas que la Source existe dans le compte de stockage, ou si les données nouvelles sont écrites.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Activer les diagnostics de Windows Azure dans une machine virtuelle pour la collection à l’aide de PowerShell le journal IIS et journal des événements

Utilisation de PowerShell pour lire à partir de diagnostics de Windows Azure sont écrits dans le stockage de table, suivez les étapes de [Configuration de journal Analytique pour indexer les diagnostics de Windows Azure](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) .

Vous pouvez plus précisément à l’aide de PowerShell d’Azure pour spécifier les événements qui sont écrits dans le stockage Azure.
Pour plus d’informations, voir [Activation des Diagnostics dans Azure Virtual Machines](../virtual-machines-dotnet-diagnostics.md).

Vous pouvez activer et mettre à jour des diagnostics de Windows Azure en utilisant le script PowerShell suivant.
Vous pouvez également utiliser ce script avec une configuration de journalisation personnalisé.
Modifiez le script pour définir le compte de stockage, le nom de service et le nom de l’ordinateur virtuel.
Le script utilise les applets de commande pour les machines virtuelles classiques.

Examiner l’exemple de script suivant, copier, modifier si nécessaire, enregistrez l’exemple sous la forme d’un fichier de script PowerShell et puis exécuter le script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Étapes suivantes

- Cette écriture de diagnostic dans le stockage blob au format JSON des services [JSON d’utiliser des fichiers dans le stockage blob](log-analytics-azure-storage-json.md) pour lire les journaux d’Azure.
- [Activer les Solutions](log-analytics-add-solutions.md) pour fournir des données.
- [Utilisation des requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.
