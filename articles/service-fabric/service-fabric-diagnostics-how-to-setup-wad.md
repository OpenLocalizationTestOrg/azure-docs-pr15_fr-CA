<properties
   pageTitle="Collecte des journaux à l’aide des tests de diagnostic Azure | Microsoft Azure"
   description="Cet article décrit comment configurer des Diagnostics d’Azure pour collecter des journaux à partir d’un cluster de tissu de Service en cours d’exécution dans Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Collecter les journaux à l’aide des Diagnostics d’Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Lorsque vous exécutez un cluster Service de Azure Fabric, il est judicieux de recueillir les journaux à partir de tous les nœuds dans un emplacement central. Ayant les journaux dans un emplacement central vous aide à analyser et à résoudre les problèmes de votre cluster, ou dans les applications et les services qui s’exécutent dans un cluster.

Une pour transférer et collecter les journaux consiste à utiliser l’extension de Diagnostics d’Azure, qui transfère les journaux vers le stockage Azure. Les journaux ne sont pas utiles directement dans le stockage. Mais vous pouvez utiliser un processus externe pour lire les événements du stockage et de les placer dans un produit comme [Journal Analytique](../log-analytics/log-analytics-service-fabric.md), [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md)ou une autre solution d’analyse de journal.

## <a name="prerequisites"></a>Conditions préalables
Vous utiliserez ces outils pour effectuer certaines opérations dans ce document :

* [Diagnostics de Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (liés aux Services de Cloud Azure mais a les bonnes informations et des exemples)
* [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)
* [PowerShell Azure](../powershell-install-configure.md)
* [Azure client du Gestionnaire de ressources](https://github.com/projectkudu/ARMClient)
* [Modèle de gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>Sources du journal que vous souhaitez collecter
- **Les journaux de service de Fabric**: émis à partir de la plate-forme aux canaux de Event Tracing for Windows (ETW) et de la source d’événement standard. Journaux peuvent être un des nombreux types :
  - Les événements opérationnels : la plate-forme de Service Fabric effectue les opérations dans les journaux. Création d’applications et les services, les changements d’état de nœud et mise à niveau sont des exemples.
  - [Événements du modèle de programmation d’acteurs fiables](service-fabric-reliable-actors-diagnostics.md)
  - [Événements du modèle de programmation des Services fiables](service-fabric-reliable-services-diagnostics.md)
- **Événements d’application**: événements émis à partir du code de votre service et écrites à l’aide de la classe d’assistance EventSource fournie dans les modèles Visual Studio. Pour plus d’informations sur la façon d’écrire des journaux à partir de votre application, consultez [moniteur et diagnostiquer les services dans une configuration de développement local machine](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## <a name="deploy-the-diagnostics-extension"></a>Déploiement de l’extension de Diagnostics
La première étape de collecte des journaux est de déployer l’extension de Diagnostics sur chacun des ordinateurs virtuels dans le cluster Service Fabric. L’extension de diagnostic collecte des journaux sur chaque machine virtuelle et les transfère vers le compte de stockage que vous spécifiez. Les étapes varient légèrement selon que vous utilisez le portail Azure ou Azure le Gestionnaire de ressources. Les étapes varient également selon que le déploiement fait partie de la création d’un cluster ou d’un cluster qui existe déjà. Examinons les étapes pour chaque scénario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Déploiement de l’extension de Diagnostics dans le cadre de la création d’un cluster par le biais du portail
Pour déployer l’extension de Diagnostics pour les ordinateurs virtuels du cluster dans le cadre de la création du cluster, vous utilisez le panneau Paramètres de diagnostic illustré dans l’image suivante. Pour activer la collecte des événements fiable des acteurs ou des Services fiables, assurez-vous que les tests de diagnostic est défini **sur** (le paramètre par défaut). Après avoir créé le cluster, vous ne pouvez pas modifier ces paramètres à l’aide du portail.

![Paramètres de diagnostic Azure dans le portail pour la création d’un cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

La prise en charge Azure équipe *requiert* la prise en charge s’ouvre pour aider à résoudre des demandes de support que vous créez. Ces journaux est collectés en temps réel et est stockés dans un des comptes de stockage créés dans le groupe de ressources. Les paramètres de diagnostic configurent les événements de niveau application. Ces événements incluent les événements des [Acteurs fiable](service-fabric-reliable-actors-diagnostics.md) , les événements de [Services fiables](service-fabric-reliable-services-diagnostics.md) et certains événements de tissu de Service au niveau du système pour être stockées dans le stockage Azure.

Produits de [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou de votre propre processus peuvent obtenir les événements à partir du compte de stockage. Il n’existe actuellement aucun moyen de filtrer ou de supprimer les événements qui sont envoyés à la table. Si vous n’implémentez pas un processus pour supprimer des événements à partir de la table, la table va continuer à croître.

Lorsque vous créez un cluster à l’aide du portail, nous vous recommandons de télécharger le modèle *avant de cliquer sur * *OK*** pour créer le cluster. Pour plus d’informations, reportez-vous à [configurer un cluster Service Fabric à l’aide d’un modèle de gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md). Vous devez le modèle pour apporter des modifications ultérieurement, car vous ne pouvez pas apporter des modifications à l’aide du portail.

Vous pouvez exporter les modèles à partir du portail à l’aide de la procédure suivante. Toutefois, ces modèles peuvent être plus difficiles à utiliser car ils peuvent avoir des valeurs null qui les informations requises sont manquantes.

1. Ouvrez votre groupe de ressources.
2. Sélectionnez **paramètres** pour afficher le panneau Paramètres.
3. Sélectionnez des **déploiements** pour afficher le panneau Historique de déploiement.
4. Sélectionnez un déploiement pour afficher les détails du déploiement.
5. Sélectionnez **Exporter le modèle** pour afficher le panneau du modèle.
6. Sélectionnez **Enregistrer dans un fichier** pour exporter un fichier .zip qui contient le modèle, paramètres et fichiers de PowerShell.

Après avoir exporté les fichiers, vous devez procéder à une modification. Modifiez le fichier parameters.json et supprimez l’élément **adminPassword** . Cela entraînera une invite pour le mot de passe lors de l’exécution du script de déploiement. Lorsque vous exécutez le script de déploiement, vous devrez peut-être corriger les valeurs de paramètre null.

Pour utiliser le modèle téléchargé à mettre à jour une configuration :

1. Extrayez le contenu dans un dossier sur votre ordinateur local.
2. Modifier le contenu afin de refléter la nouvelle configuration.
3. Démarrer PowerShell et accédez au dossier où vous avez extrait le contenu.
4. Exécuter **deploy.ps1** et entrez l’ID d’abonnement, le nom de groupe de ressources (utilisez le même nom à mettre à jour la configuration) et un nom uniques.


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Déploiement de l’extension de Diagnostics dans le cadre de la création d’un cluster à l’aide du Gestionnaire de ressources Azure
Pour créer un cluster à l’aide du Gestionnaire de ressources, vous devez ajouter la configuration des Diagnostics de JSON pour le modèle de gestionnaire de ressources de cluster complète avant de créer le cluster. Nous fournissons un exemple de modèle Gestionnaire de ressources de cluster cinq-VM en configuration de Diagnostics ajoutée dans le cadre de nos exemples de modèle de gestionnaire de ressources. Vous pouvez le voir à cet emplacement dans la galerie d’exemples d’Azure : [cluster à cinq nœuds avec exemple de modèle de gestionnaire de ressources de tests de diagnostic](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Pour afficher le paramètre de Diagnostics dans le modèle de gestionnaire de ressources, ouvrez le fichier de la azuredeploy.json et de la recherche pour **IaaSDiagnostics**. Pour créer un cluster à l’aide de ce modèle, sélectionnez le bouton **déployer vers Azure** disponible à l’adresse précédente.

Sinon, vous pouvez télécharger l’exemple de gestionnaire de ressources, apporter des modifications et créer un cluster avec le modèle modifié à l’aide de la `New-AzureRmResourceGroupDeployment` commande dans une fenêtre PowerShell d’Azure. Voir le code suivant pour les paramètres que vous passez à la commande. Pour obtenir des informations détaillées sur le déploiement d’un groupe de ressources à l’aide de PowerShell, voir l’article [déployer un groupe de ressources avec le modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Déploiement de l’extension de Diagnostics à un cluster existant
Si vous disposez d’un cluster existant qui n’a pas déployé de Diagnostics, ou si vous souhaitez modifier une configuration existante, vous pouvez ajouter ou mettre à jour. Modifier le modèle de gestionnaire de ressources qui est utilisé pour créer le cluster existant ou télécharger le modèle à partir du portail comme décrit plus haut. Modifiez le fichier template.json en effectuant les tâches suivantes.

Ajouter une nouvelle ressource de stockage pour le modèle en ajoutant à la section ressources.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Ensuite, ajoutez à la section Paramètres juste après les définitions de compte de stockage, entre `supportLogStorageAccountName` et `vmNodeType0Name`. Remplacer le texte d’espace réservé *Insérer ici le nom du compte de stockage* avec le nom du compte de stockage.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Puis, mettez à jour la `VirtualMachineProfile` section du fichier template.json en ajoutant le code suivant dans le tableau d’extensions. N’oubliez pas d’ajouter une virgule au début ou à la fin, en fonction de l’emplacement où il est inséré.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Après avoir modifié le fichier template.json comme décrit, republiez le modèle de gestionnaire de ressources. Si le modèle a été exporté, exécutez le fichier deploy.ps1 republie pas le modèle. Après le déploiement, vérifiez que **ProvisioningState** **a réussi**.


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Mettre à jour des Diagnostics pour la collecte et de télécharger les journaux de nouveaux canaux EventSource
Pour mettre à jour les tests de diagnostic pour collecter des journaux de nouveaux canaux EventSource qui représentent une nouvelle application que vous êtes sur le point de déployer, d’effectuer les mêmes étapes que dans la [section précédente](#deploywadarm) pour le programme d’installation de Diagnostics pour un cluster existant.

Mettre à jour la `EtwEventSourceProviderConfiguration` section dans le fichier template.json pour ajouter des entrées pour les nouvelles chaînes EventSource avant d’appliquer la configuration mise à jour à l’aide de la `New-AzureRmResourceGroupDeployment` commande PowerShell. Le nom de la source d’événements est défini en tant que partie de votre code dans le fichier ServiceEventSource.cs de généré par Visual Studio.

Par exemple, si votre source de l’événement nommé ma-Eventsource, ajoutez le code suivant pour placer les événements à partir de mon-Eventsource dans une table nommée MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Pour collecter les compteurs de performance ou les journaux des événements, modifier le modèle de gestionnaire de ressources en utilisant les exemples fournis dans [créer un ordinateur virtuel de Windows avec la surveillance et de diagnostic à l’aide d’un modèle de gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Ensuite, republiez le modèle de gestionnaire de ressources.

## <a name="next-steps"></a>Étapes suivantes
Pour comprendre plus en détail les événements que vous devez rechercher lors de la résolution des problèmes, consultez les événements de diagnostic émis pour les [Acteurs fiable](service-fabric-reliable-actors-diagnostics.md) et de [Services fiables](service-fabric-reliable-services-diagnostics.md).


## <a name="related-articles"></a>Articles connexes
* [Découvrez comment collecter les compteurs de performance ou les journaux à l’aide de l’extension de Diagnostics](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Solution de Fabric du service dans le journal Analytique](../log-analytics/log-analytics-service-fabric.md)
