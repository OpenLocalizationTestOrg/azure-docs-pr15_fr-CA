<properties
    pageTitle="Mise à l’échelle automatique et la machine virtuelle mise à l’échelle les jeux | Microsoft Azure"
    description="Obtenir des informations sur l’utilisation des diagnostics et des ressources de l’échelle mise à l’échelle automatiquement les ordinateurs virtuels dans un jeu d’échelle."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Mise à l’échelle automatique et la machine virtuelle mise à l’échelle les jeux

La mise à l’échelle automatique des ordinateurs virtuels dans un ensemble d’échelle est la création ou la suppression des ordinateurs de l’ensemble que nécessaire pour répondre aux exigences de performances. Au fur et à mesure du volume de travail, une application peut nécessiter des ressources supplémentaires pour pouvoir exécuter les tâches de manière efficace.

Mise à l’échelle automatique est un processus automatisé qui facilite des frais de gestion. En réduisant les frais généraux, vous n’avez pas besoin surveiller les performances du système en permanence ou de décider comment gérer les ressources. Mise à l’échelle est un processus élastique. Plus de ressources peuvent être ajoutés en tant que la charge augmente, mais en tant que demande les sorties de ressources peuvent être supprimés pour réduire les coûts et de maintenir les niveaux de performances.

Permet de paramétrer la mise à l’échelle automatique sur une échelle définie à l’aide d’un modèle de gestionnaire de ressources Azure, Azure PowerShell, Azure CLI ou l’Azure portal.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configurer la mise à l’échelle à l’aide des modèles du Gestionnaire de ressources

Au lieu de déploiement et de gestion de chaque ressource de votre application séparément, utiliser un modèle qui déploie toutes les ressources dans une opération unique et coordonnée. Dans le modèle, les ressources d’application sont définies, et les paramètres de déploiement sont spécifiés pour les différents environnements. Le modèle est composé de JSON et des expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. Pour plus d’informations, consultez [modèles de création d’un gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Dans le modèle, vous spécifiez l’élément de capacité :

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Capacité identifie le nombre de machines virtuelles dans le jeu. Vous pouvez modifier manuellement la capacité par le déploiement d’un modèle avec une valeur différente. Si vous déployez un modèle pour modifier uniquement la capacité, vous pouvez inclure uniquement l’élément de référence avec la capacité de mise à jour.

Modifier automatiquement la capacité de votre échelle définie à l’aide de la combinaison de la ressource d’autoscaleSettings et de l’extension des tests de diagnostic.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurez l’extension de Diagnostics d’Azure

Mise à l’échelle automatique n’est possible si la collecte de métriques est réussie sur chaque ordinateur virtuel dans l’ensemble de l’échelle. L’Extension de Diagnostics Azure fournit les capacités de surveillance et de diagnostic qui répond aux besoins de la ressource autoscale collection métriques. Vous pouvez installer l’extension dans le cadre du modèle de gestionnaire de ressources.

Cet exemple montre les variables qui sont utilisées dans le modèle de configuration de l’extension des tests de diagnostic :

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Les paramètres sont fournis lorsque le modèle est déployé. Dans cet exemple, le nom du compte de stockage où sont stockées les données et le nom de l’ensemble de l’échelle à partir de laquelle les données sont collectées sont fournis. Également dans cet exemple de Windows Server, seul le compteur de performance nombre de threads est collecté. Tous les compteurs de performance disponibles dans Windows ou Linux peuvent être utilisés pour collecter des informations de diagnostics et peuvent être inclus dans la configuration de l’extension.

Cet exemple illustre la définition de l’extension dans le modèle :

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Lorsque l’extension de diagnostics s’exécute, les données sont collectées dans une table qui se trouve dans le compte de stockage que vous spécifiez. Dans la table WADPerformanceCounters, vous pouvez trouver les données collectées :

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurez la ressource autoScaleSettings

La ressource autoscaleSettings utilise les informations de l’extension de diagnostics pour décider s’il faut augmenter ou diminuer le nombre d’ordinateurs virtuels dans l’ensemble de l’échelle.

Cet exemple illustre la configuration de la ressource dans le modèle :

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

Dans l’exemple ci-dessus, les deux règles sont créées pour définir les actions de mise à l’échelle automatiques. La première règle définit l’action de l’horizontale et la deuxième règle définit l’action d’échelle. Ces valeurs sont fournies dans les règles :

- **metricName** - cette valeur est le même que le compteur de performance que vous avez défini dans la variable wadperfcounter pour l’extension des tests de diagnostic. Dans l’exemple ci-dessus, le compteur du nombre de threads est utilisé.  
- **metricResourceUri** - cette valeur est l’identificateur de ressource de l’ensemble d’échelle de machine virtuelle. Cet identificateur contient le nom du groupe ressources, le nom du fournisseur de ressources et le nom de l’échelle définie à l’échelle.
- **timeGrain** : cette valeur est la granularité des mesures qui sont collectées. Dans l’exemple précédent, les données sont collectées sur un intervalle d’une minute. Cette valeur est utilisée avec la durée.
- **statistique** – cette valeur détermine la façon dont les mesures sont combinées pour s’adapter à l’action de mise à l’échelle automatique. Les valeurs possibles sont : moyenne, Min, Max.
- **durée** : cette valeur est l’intervalle de temps dans laquelle les données de l’instance sont collectées. Il doit être compris entre 5 et 12 heures.
- **timeAggregation** – cette valeur détermine la façon dont les données collectées doivent être combinées dans le temps. La valeur par défaut est moyenne. Les valeurs possibles sont : moyenne, Minimum, Maximum, dernier, Total, comptage.
- **opérateur** – cette valeur est l’opérateur qui est utilisé pour comparer les données de mesure et le seuil. Les valeurs possibles sont : est égal à, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **seuil** : cette valeur est la valeur qui déclenche l’action de l’échelle. Veillez à fournir une différence suffisante entre le seuil de l’action horizontale et le seuil de l’action d’échelle. Si vous définissez les valeurs identiques, le système prévoit des changements constants, ce qui empêche leur mise en oeuvre d’une action de mise à l’échelle. Par exemple, affecter à 600 threads dans l’exemple précédent ne fonctionne pas.
- **direction** – cette valeur détermine l’action à entreprendre lorsque le seuil est atteint. Les valeurs possibles sont à augmenter ou diminuer.
- **type** , cette valeur est le type d’action qui doit se produire et qui doit être définie sur ChangeCount.
- **valeur** : cette valeur est le nombre d’ordinateurs virtuels qui sont ajoutés ou supprimés de l’ensemble de l’échelle. Cette valeur doit être 1 ou supérieur.
- **ralentissement** – cette valeur est la quantité de temps d’attente depuis la dernière opération de mise à l’échelle avant la prochaine action se produit. Cette valeur doit être entre 1 minute et une semaine.

Selon le compteur de performance que vous utilisez, certains des éléments de la configuration du modèle sont utilisés différemment. Dans l’exemple précédent, le compteur de performance est le nombre de threads, la valeur du seuil est de 650 pour une action horizontale et la valeur de seuil est 550 pour l’action d’échelle. Si vous utilisez un compteur % temps processeur, par exemple, la valeur de seuil est définie en fonction du pourcentage d’utilisation du processeur qui détermine une action de mise à l’échelle.

Lors de la création d’une charge sur les ordinateurs virtuels qui déclenche une action de mise à l’échelle, la capacité de l’ensemble est augmentée en fonction de la valeur dans le modèle. Par exemple, dans une échelle de jeu où la capacité a 3 et la valeur d’échelle action est définie sur 1 :

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Lorsque la charge est créée qui provoque que le nombre moyen de threads aller au-dessus du seuil de 650 :

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Une action horizontale est déclenchée qui provoque la capacité de l’ensemble à être augmenté d’une unité :

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Et un ordinateur virtuel est ajouté à l’ensemble de l’échelle :

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Après une période de ralentissement de cinq minutes, si le nombre moyen de threads sur les ordinateurs reste plus de 600, un autre ordinateur est ajouté au jeu. Si le nombre de threads moyenne demeure en dessous de 550, la capacité de l’ensemble de l’échelle est diminuée d’une unité et un ordinateur est supprimé de l’ensemble.

## <a name="set-up-scaling-using-azure-powershell"></a>Définir l’échelle à l’aide de PowerShell d’Azure

Pour voir des exemples d’utilisation de PowerShell pour configurer autoscaling, examinez [PowerShell de moniteur Azure rapide démarrer exemples](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Définir l’échelle à l’aide de la CLI d’Azure

Pour voir des exemples d’utilisation de CLI d’Azure pour configurer autoscaling, examinez le [CLI de multiplates-formes Azure moniteur rapide démarrer exemples](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Configurer mise à l’échelle à l’aide du portail Azure

Pour voir un exemple d’utilisation du portail Azure pour configurer autoscaling, consultez [créer une échelle de Machine virtuelle définie à l’aide du portail Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Examinez les actions de mise à l’échelle

- [Azure portal]() - vous trouverez actuellement une quantité limitée d’informations via le portail.
- [Explorateur de Ressources azure]() - cet outil est la meilleure pour l’exploration de l’état actuel de l’ensemble de l’échelle. Suivez ce chemin d’accès et vous devriez voir la vue d’instance de l’ensemble d’échelle que vous avez créé : abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Compute > virtualMachineScaleSets > {votre ensemble d’échelle} > desmachines virtuelles
- PowerShell Azure - Utilisez cette commande pour obtenir des informations :

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- Se connecter à l’ordinateur virtuel jumpbox comme vous le feriez pour n’importe quel autre ordinateur, et vous pouvez ensuite accéder à distance les ordinateurs virtuels de l’échelle définie pour analyser les processus individuels.

## <a name="next-steps"></a>Étapes suivantes

- Observez comment [redimensionner automatiquement les ordinateurs dans une échelle de Machine virtuelle définie](virtual-machine-scale-sets-windows-autoscale.md) pour voir un exemple de création d’une échelle définie avec la mise à l’échelle automatique configurée.
- Rechercher des exemples de moniteur d’Azure fonctionnalités dans [PowerShell de moniteur Azure rapide démarrer les échantillons](../monitoring-and-diagnostics/insights-powershell-samples.md) d’analyse
- Découvrez les fonctionnalités de notification dans [utiliser des actions autoscale pour envoyer des notifications d’alerte e-mail et webhook dans le moniteur d’Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
- Découvrez comment les [journaux d’audit de l’utilisation pour envoyer des notifications d’alerte e-mail et webhook dans le moniteur d’Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Obtenir des informations sur [les scénarios avancés autoscale](./virtual-machine-scale-sets-advanced-autoscale.md).
