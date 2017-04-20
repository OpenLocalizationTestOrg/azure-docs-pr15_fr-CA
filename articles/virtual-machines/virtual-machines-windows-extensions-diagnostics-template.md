<properties
    pageTitle="Créer un ordinateur virtuel Windows avec la surveillance et de diagnostic à l’aide du modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Un modèle de gestionnaire de ressources Azure permet de créer une machine virtuelle Windows avec l’extension de diagnostics de Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>Créer un ordinateur virtuel Windows avec la surveillance et de diagnostic à l’aide du modèle de gestionnaire de ressources Azure

L’Extension de Diagnostics Azure fournit la surveillance et des capacités de tests de diagnostic sur un Windows Azure VM. Vous pouvez activer ces fonctionnalités sur l’ordinateur virtuel en incluant l’extension en tant que partie du modèle de gestionnaire de Ressources azure. Pour plus d’informations sur l’inclusion de n’importe quelle extension dans le cadre d’un modèle d’ordinateur virtuel, consultez [Création de modèles Azure le Gestionnaire de ressources avec des Extensions de la machine virtuelle](virtual-machines-windows-extensions-authoring-templates.md) . Cet article décrit comment vous pouvez ajouter l’extension Azure Diagnostics pour un modèle d’ordinateur virtuel windows.  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Ajouter l’extension Azure Diagnostics à la définition de ressource d’ordinateur virtuel 

Pour activer l’extension de tests de diagnostic sur une Machine virtuelle de Windows, vous devez ajouter l’extension sous la forme d’une ressource d’ordinateur virtuel dans le modèle de gestionnaire de ressources.

Un simple gestionnaire de ressources des machines virtuelles en fonction Ajouter la configuration de l’extension au tableau des *ressources* pour l’ordinateur virtuel : 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Une autre convention courante est d’ajouter la configuration de l’extension au nœud racine ressources du modèle au lieu de définir celle-ci sous le nœud des ressources de la machine virtuelle. Avec cette approche, vous devez spécifier de manière explicite une relation hiérarchique entre l’extension et l’ordinateur virtuel avec les valeurs de *nom* et le *type* . Par exemple : 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

L’extension est toujours associée à la machine virtuelle, vous pouvez directement définir directement sous le nœud de la ressource de l’ordinateur virtuel ou le définir au niveau de base et utiliser la convention d’affectation de noms hiérarchique pour l’associer à la machine virtuelle.

Pour les jeux d’échelle de Machine virtuelle, la configuration des extensions est spécifiée dans la propriété *extensionProfile* de l' *VirtualMachineProfile*.
   
La propriété *publisher* avec la valeur de **Microsoft.Azure.Diagnostics** et la propriété du *type* avec la valeur de **IaaSDiagnostics** identifier de manière unique l’extension Azure Diagnostics.

La valeur de la propriété *name* peut être utilisée pour faire référence à l’extension du groupe de ressources. La valeur spécifiquement **Microsoft.Insights.VMDiagnosticsSettings** permettra de l’identifier facilement par la Azure classique portail portail en vous assurant que les graphiques de contrôle s’afficheront correctement dans Azure portal classique.

La *typeHandlerVersion* indique la version de l’extension que vous souhaitez utiliser. *AutoUpgradeMinorVersion* version mineure **true** garantit que vous obtenez la dernière version mineure de l’extension n’est disponible. Il est fortement recommandé de toujours définir *autoUpgradeMinorVersion* doit toujours être **true** de sorte que vous devez toujours utiliser la dernière extension diagnostics disponibles avec toutes les nouvelles fonctionnalités et des correctifs de bogues. 

L’élément *paramètres* contient des propriétés de configuration de l’extension qui peut être défini et de l’extension (parfois dénommée configuration publique). La propriété *xmlcfg* contient la configuration basé sur xml pour les journaux de diagnostic, etc. qui seront collectées par l’agent de diagnostic des compteurs de performance. Pour plus d’informations sur le schéma xml, voir [Schéma de Configuration de Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) . Une pratique courante consiste à stocker la configuration xml réel en tant que variable dans le modèle de gestionnaire de ressources Azure et ensuite concaténer et base64 encoder les pour définir la valeur de *xmlcfg*. Reportez-vous à la section sur les [variables de configuration de diagnostics](#diagnostics-configuration-variables) pour en savoir plus sur la façon de stocker les données xml dans des variables. La propriété *storageAccount* Spécifie le nom du compte de stockage vers lequel les données de diagnostic seront transférées. 
 
Les propriétés de *protectedSettings* (parfois dénommé configuration privée) peuvent être définies, mais ne peut pas être lu une fois définie. La nature écriture seule de *protectedSettings* permet de stocker des secrets tels que la clé de compte de stockage où seront écrit les données de diagnostic.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Spécification du compte de stockage des tests de diagnostic en tant que paramètres 

Le tests de diagnostic extension json extrait de code ci-dessus suppose deux paramètres *existingdiagnosticsStorageAccountName* et *existingdiagnosticsStorageResourceGroup* pour spécifier le compte de stockage diagnostics où seront stockées les données de diagnostic. Spécification du compte de stockage de diagnostic comme un paramètre, il est facile de modifier le compte de stockage diagnostics dans différents environnements par exemple, vous souhaiterez utiliser un compte de stockage différents tests de diagnostic pour les tests et une autre pour votre déploiement en production.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

Il est recommandé de spécifier un compte de stockage de diagnostics dans un groupe de ressources différent que le groupe de ressources pour l’ordinateur virtuel. Un groupe de ressources peut être considéré comme une unité de déploiement avec sa propre durée de vie, une machine virtuelle peut être déployée et redéployée que de nouvelles mises à jour de configurations qu’il lui sont apportées, mais vous souhaiterez peut-être continuer à stocker les données de diagnostic dans le même compte de stockage entre les déploiements de machine virtuelle. Avec le compte de stockage dans une autre ressource permet le compte de stockage accepter des données de plusieurs déploiements de machine virtuelle ce qui facilite la résolution des problèmes sur les différentes versions.

>[AZURE.NOTE] Si vous créez un modèle d’ordinateur virtuel windows à partir de Visual Studio du compte de stockage par défaut peut être défini pour utiliser le même compte de stockage dans lequel l’ordinateur virtuel VHD est téléchargé. Il s’agit pour simplifier la configuration initiale de la machine virtuelle. Le modèle pour utiliser un compte de stockage différent peut être passé comme un paramètre du nouveau facteur. 

## <a name="diagnostics-configuration-variables"></a>Variables de configuration de Diagnostics
 
L’extrait de json d’extension diagnostics ci-dessus définit une variable de *accountid* pour simplifier l’obtention de la clé de compte de stockage pour le stockage des tests de diagnostic :   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


La propriété *xmlcfg* pour l’extension des diagnostics est définie à l’aide de plusieurs variables concaténées. Les valeurs de ces variables sont au format xml afin qu’ils doivent être échappés correctement lorsque vous définissez les variables de json.

Le tableau suivant décrit le xml de configuration de tests de diagnostic qui collecte les compteurs de performance au niveau de système standard ainsi que des journaux des événements windows et les journaux de diagnostics infrastructure. Il a une séquence d’échappement et correctement mis en forme afin que la configuration peut directement être collée dans la section variables de votre modèle. Voir le [Schéma de Configuration de Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) pour obtenir un exemple plu lisible du fichier de configuration xml.
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

Nœud xml de définition de mesures dans la configuration ci-dessus est un élément de configuration importante car elle définit comment les compteurs de performances définis précédemment dans le fichier xml dans le nœud de *PerformanceCounter* seront regroupées et stockées. 

> [AZURE.IMPORTANT] Ces mesures de lecteur les graphiques de surveillance et les alertes dans le portail Azure.  Le nœud de **métriques** avec le *resourceID* et **MetricAggregation** doit figurer dans la configuration de tests de diagnostic pour votre ordinateur virtuel si vous souhaitez afficher les données d’analyse dans le portail Azure VM. 

Voici un exemple de xml pour les définitions des statistiques : 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

L’attribut *resourceID* identifie de façon unique l’ordinateur virtuel dans votre abonnement. Assurez-vous d’utiliser les fonctions subscription() et resourceGroup() afin que le modèle met automatiquement à jour ces valeurs en fonction de l’abonnement et le groupe de ressources que vous déployez sur.

Si vous créez plusieurs Machines virtuelles dans une boucle vous devrez remplir la valeur *resourceID* avec une fonction copyIndex() correctement différencier chaque VM individuel. La valeur de *xmlCfg* peut être mis à jour pour prendre en charge cela comme suit :  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

La valeur de MetricAggregation de *PT1H* et *PT1M* indiquent une agrégation par minute et une agrégation de plus d’une heure.

## <a name="wadmetrics-tables-in-storage"></a>Tables de WADMetrics de stockage

La configuration des mesures ci-dessus génère des tables dans votre compte de stockage diagnostics avec les conventions d’affectation de noms suivantes :

- **WADMetrics** : préfixe Standard pour toutes les tables de WADMetrics
- **PT1H** ou **PT1M** : signifie que la table contient des données d’agrégation plus 1 heure ou une minute
- **P10D** : indique la table contiendra les données de dix jours à partir de la table le démarrage de la collecte de données
- **V2S** : constante de chaîne
- **AAAAMMJJ** : la date à laquelle la table a commencé à collecter des données

Exemple : *WADMetricsPT1HP10DV2S20151108* contient des données de mesures regroupées plus d’une heure pour 10 jours en commençant à 11-Nov-2015    

Chaque table WADMetrics contient les colonnes suivantes :

- **PartitionKey**: le partitionkey est construit selon la valeur *resourceID* pour identifier de manière unique la ressource d’ordinateur virtuel. pour, par exemple : 002Fsubscriptions :<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : le format <Descending time tick>:<Performance Counter Name>. Calcul de graduations du temps décroissant est de graduations du temps max moins l’heure de début de la période de globalisation. Par exemple Si la période d’échantillonnage démarré sur 10-Nov-2015 et 00:00Hrs UTC puis le calcul serait : DateTime.MaxValue.Ticks - (DateTime(2015,11,10,0,0,0,DateTimeKind.Utc) de nouveau. Graduations). Pour la clé de la ligne du compteur de performance des octets disponibles de mémoire se présente comme : 2519551871999999999__:005CMemory:005CAvailable:0020 octets
- **CounterName** : est le nom du compteur de performance. Cela correspond à la *counterSpecifier* définie dans la configuration xml.
- **Maximale** : la valeur maximale du compteur de performance sur la période de globalisation.
- **Minimale** : la valeur minimale du compteur de performance sur la période de globalisation.
- **Total** : la somme de toutes les valeurs du compteur de performance fait état pour la période de globalisation.
- **Count** : nombre total de valeurs déclarées pour le compteur de performance.
- **Moyenne** : la valeur moyenne (nombre total de /) du compteur de performance sur la période de globalisation.


## <a name="next-steps"></a>Étapes suivantes

- Pour un exemple complet de modèle d’un ordinateur virtuel de Windows avec l’extension de diagnostics, consultez [201-vm-surveillance-diagnostic-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- Déployer le modèle de gestionnaire de ressources à l’aide de [PowerShell d’Azure](virtual-machines-windows-ps-manage.md) ou de la [ligne de commande d’Azure](virtual-machines-linux-cli-deploy-templates.md)
- En savoir plus sur la [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md)







