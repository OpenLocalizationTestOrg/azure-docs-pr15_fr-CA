<properties
    pageTitle="Créer un concentrateur IoT à l’aide de la CLI d’Azure | Microsoft Azure"
    description="Suivez cet article pour créer un concentrateur IoT à l’aide de l’Interface de ligne de commande d’Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>Créer un concentrateur IoT à l’aide de la CLI d’Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser l’Interface de ligne de commande de Azure pour créer et gérer les concentrateurs d’Azure IoT par programme. Cet article vous montre comment utiliser la CLI Azure pour créer un concentrateur IoT.

Pour terminer ce didacticiel, vous devez les éléments suivants :

- Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.
- [CLI Azure 0.10.4] [ lnk-CLI-install] ou une version ultérieure. Si vous avez déjà Azure CLI, vous pouvez valider la version actuelle à l’invite de commandes avec la commande suivante :
```
    azure --version
```

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources Azure et classique](../resource-manager-deployment-model.md). L’infrastructure du langage commun Azure doit être en mode de Gestionnaire des ressources Azure :
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Définissez votre compte Azure et abonnement 

1. Lors de la connexion d’invite de commandes en tapant la commande suivante
```
    azure login
```
Utilisez le navigateur web proposé et le code pour s’authentifier.

2. Si vous avez plusieurs abonnements Azure, connexion à Azure accorde l’accès à tous les abonnements Azure associée à vos informations d’identification. Vous pouvez afficher les abonnements d’Azure, ainsi que celle qui est la valeur par défaut, à l’aide de la commande
```
    azure account list 
```

Pour définir le contexte d’abonnement dans lesquelles vous souhaitez exécuter le reste de l’utilisation de commandes

```
    azure account set <subscription name>
```

3. Si vous ne disposez pas d’un groupe de ressources, vous pouvez créer un de nommé **exampleResourceGroup** 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] L’article [utiliser la CLI Azure pour gérer les ressources et groupes ressources Azure] [ lnk-CLI-arm] fournit plus d’informations sur l’utilisation de CLI d’Azure pour gérer les ressources Azure. 


## <a name="create-an-iot-hub"></a>Créer un concentrateur IoT

Paramètres requis :

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Pour afficher tous les paramètres disponibles pour la création vous pouvez utiliser la commande help dans une invite de commande
```
    azure iothub create -h 
```
Exemple rapide :

 Pour créer un concentrateur IoT appelé **exampleIoTHubName** dans du groupe ressource **exampleResourceGroup** simplement exécuter la commande suivante
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Cette commande CLI d’Azure crée un concentrateur IoT Standard S1 pour lesquels vous êtes facturé. Vous pouvez supprimer de concentrateur IoT **exampleIoTHubName** grâce à la commande 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le développement pour IoT Hub, consultez les rubriques suivantes :
- [Kits de développement logiciel IoT concentrateur][lnk-sdks]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [À l’aide du portail Azure pour gérer IoT concentrateur][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
