<properties
    pageTitle="Créer un concentrateur IoT à l’aide d’un modèle de gestionnaire de ressources Azure et de PowerShell | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide des modèles du Gestionnaire de ressources Azure pour créer un concentrateur IoT avec PowerShell."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-powershell"></a>Créer un concentrateur IoT à l’aide de PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser le Gestionnaire de ressources Azure pour créer et gérer les concentrateurs d’Azure IoT par programme. Ce didacticiel vous montre comment utiliser un modèle de gestionnaire de ressources Azure pour créer un concentrateur IoT avec PowerShell.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources Azure et classique](../resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement d’Azure le Gestionnaire de ressources.

Pour terminer ce didacticiel, vous devez les éléments suivants :

- Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou une version ultérieure.

> [AZURE.TIP] L’article [À l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure] [ lnk-powershell-arm] fournit plus d’informations sur l’utilisation des scripts PowerShell et le Gestionnaire de ressources Azure modèles pour créer des ressources Azure. 

## <a name="connect-to-your-azure-subscription"></a>Se connecter à votre abonnement Azure

Dans une invite de commande PowerShell, tapez la commande suivante pour vous connecter à votre abonnement Azure :

```
Login-AzureRmAccount
```

Vous pouvez utiliser les commandes suivantes pour découvrir où vous pouvez déployer un concentrateur IoT et les versions d’API actuellement prises en charge :

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Créer un groupe de ressources pour contenir votre concentrateur IoT à l’aide de la commande suivante dans un des emplacements pris en charge pour IoT concentrateur. Cet exemple crée un groupe de ressources appelé **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Envoyer un modèle de gestionnaire de ressources Azure pour créer un concentrateur IoT

Utiliser un modèle JSON pour créer un concentrateur IoT dans votre groupe de ressources. Vous pouvez également utiliser un modèle du Gestionnaire de ressources Azure pour apporter des modifications à un concentrateur IoT existant.

1. Utilisez un éditeur de texte pour créer un modèle de gestionnaire de ressources Azure appelé **template.json** avec la définition de la ressource suivante pour créer un nouveau concentrateur IoT standard. Cet exemple ajoute le concentrateur IoT dans la région **Des États-Unis** , crée deux groupes de consommateurs (**cg1** et **cg2**) sur le point de terminaison du concentrateur d’événements compatibles et utilise la version de **2016-02-03** API. Ce modèle également que vous devez passer le nom de concentrateur IoT sous la forme d’un paramètre appelé **hubName**. Pour la liste actuelle des emplacements qui prennent en charge le concentrateur de IoT voir [Azure état][lnk-status].

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Enregistrez le fichier de modèle de gestionnaire de ressources Azure sur votre ordinateur local. Cet exemple suppose que vous l’enregistrez dans un dossier appelé **c:\templates**.

3. Exécutez la commande suivante pour déployer votre nouveau concentrateur IoT, en passant le nom de votre concentrateur IoT en tant que paramètre. Dans cet exemple, le nom du concentrateur IoT est **abcmyiothub** (Notez que ce nom doit être unique afin qu’il doit inclure votre nom ou des initiales) :

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. La sortie affiche les clés pour le concentrateur IoT que vous avez créé.

5. Vous pouvez vérifier que votre application a ajouté le nouveau concentrateur IoT en visitant le [portail Azure] [ lnk-azure-portal] et affichez votre liste de ressources ou à l’aide de l’applet de commande PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Cet exemple d’application ajoute un concentrateur IoT Standard S1 pour lesquels vous êtes facturé. Vous pouvez supprimer le concentrateur IoT via le [portail Azure] [ lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Supprimer-AzureRmResource** lorsque vous avez terminé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez déployé un concentrateur IoT à l’aide d’un modèle de gestionnaire de ressources Azure avec PowerShell, vous souhaiterez peut-être approfondir :

- En savoir plus sur les fonctionnalités du [fournisseur de ressources IoT concentrateur API REST]de[lnk-rest-api].
- Lisez la [vue d’ensemble du Gestionnaire de ressources Azure] [ lnk-azure-rm-overview] pour en savoir plus sur les fonctionnalités du Gestionnaire de ressources Azure.

Pour en savoir plus sur le développement pour IoT Hub, consultez les rubriques suivantes :

- [Introduction au Kit de développement logiciel C][lnk-c-sdk]
- [Kits de développement logiciel IoT concentrateur][lnk-sdks]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
