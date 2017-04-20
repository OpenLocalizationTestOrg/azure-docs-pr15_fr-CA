<properties
    pageTitle="Créer un espace de noms d’événements concentrateurs avec concentrateur d’événements et permettre l’Archive à l’aide d’un modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Créer un espace de noms d’événements concentrateurs avec concentrateur d’événements et permettre l’Archive à l’aide du modèle de gestionnaire de ressources Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Créer un espace de noms d’événements concentrateurs avec concentrateur d’événements et permettre l’Archive à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article explique comment utiliser un modèle de gestionnaire de ressources Azure qui crée un espace de noms de concentrateurs d’événement avec un concentrateur d’événements et permet l’archivage sur votre concentrateur d’événements. Vous apprenez à définir les ressources qui sont déployés et comment définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou personnaliser pour l’adapter à vos besoins

Pour plus d’informations sur la création de modèles, consultez [modèles de création d’un gestionnaire de ressources Azure][].

Pour plus d’informations sur les pratiques et les modèles sur Azure ressources des conventions d’affectation de noms, consultez [Conventions d’affectation de noms des ressources d’Azure][].

Pour le modèle complet, consultez [concentrateur d’événements et le modèle d’archivage][] sur GitHub.

>[AZURE.NOTE]
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et rechercher les concentrateurs d’événement.

## <a name="what-you-deploy"></a>Vous déployez ?

Avec ce modèle, vous déployez les concentrateurs d’événement d’un espace de noms avec un concentrateur d’événements et activez l’archivage.

[Concentrateurs d’événement](../event-hubs/event-hubs-what-is-event-hubs.md) est un événement de traitement du service utilisé pour fournir des entrée d’événement et la télémétrie vers Azure à grande échelle, avec une latence faible et haute fiabilité. Événement concentrateurs Archive vous permettra fournir automatiquement des données en continu dans vos concentrateurs d’événement au stockage Azure Blob de votre choix dans un temps spécifié ou l’intervalle de la taille de votre choix.

Pour exécuter le déploiement automatique, cliquez sur le bouton suivant :

[![Déployer vers Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources d’Azure, vous définissez des paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section intitulée `Parameters` qui contient toutes les valeurs de paramètre. Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui doivent restent les mêmes. Chaque valeur de paramètre est utilisé dans le modèle pour définir les ressources qui sont déployés.

Le modèle définit les paramètres suivants.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Le nom de l’espace de noms de concentrateurs d’événement à créer.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Le nom du concentrateur d’événements créé dans l’événement concentrateurs espace de noms.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Le nombre de jours pendant lesquels les messages conservés dans votre concentrateur d’événements. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Le nombre de partitions que vous souhaitez dans votre concentrateur d’événements.

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled

Activer l’archivage sur votre concentrateur d’événements.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat

Le format de codage que vous spécifiez pour sérialiser les données d’événement.

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime

L’intervalle de temps auxquelles l’Archive démarre l’archivage des données dans le stockage blob Azure.

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>taille de l’archive

L’intervalle de taille auxquelles l’Archive démarre l’archivage des données dans le stockage blob Azure.

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

L’Archive nécessite un id de ressource de compte de stockage, pour activer l’archivage pour votre stockage Azure souhaité.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Archivé le conteneur blob où vous souhaitez que vos données d’événement.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion

La version de l’API du modèle.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Ressources pour le déploiement

Crée un espace de noms du type **EventHubs**, avec un concentrateur d’événements et permet l’archivage.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }
                  
               }
               
            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>Commandes à exécuter de déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Création de modèles du Gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
[Modèles d’Azure Quickstart]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Conventions d’affectation de noms de ressources Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Concentrateur d’événements et le modèle de l’Archive]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive
