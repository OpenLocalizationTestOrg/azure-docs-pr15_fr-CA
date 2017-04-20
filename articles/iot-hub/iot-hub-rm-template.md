<properties
    pageTitle="Créer un concentrateur IoT à l’aide d’un modèle ARM et C# | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide des modèles du Gestionnaire de ressources Azure pour créer un concentrateur IoT avec un programme C#."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Créer un concentrateur IoT à l’aide d’un programme C# avec un modèle de gestionnaire de ressources Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser le Gestionnaire de ressources Azure pour créer et gérer les concentrateurs d’Azure IoT par programme. Ce didacticiel vous montre comment utiliser un modèle de gestionnaire de ressources Azure pour créer un concentrateur IoT à partir d’un programme C#.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources Azure et classique](../resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement d’Azure le Gestionnaire de ressources.

Pour terminer ce didacticiel, vous devez les éléments suivants :

- Microsoft Visual Studio 2015.
- Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.
- Un [compte de stockage Azure] [ lnk-storage-account] dans lequel vous pouvez stocker vos fichiers de modèles du Gestionnaire de ressources Azure.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou une version ultérieure.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Préparer votre projet Visual Studio

1. Dans Visual Studio, créez un projet Visual C# Windows en utilisant le modèle de projet **Application Console** . Nommez le projet **CreateIoTHub**.

2. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet et puis cliquez sur **Gérer les Packages NuGet**.

3. Dans le Gestionnaire de package Nuget, cochez **inclure la version préliminaire**et recherchez les **Microsoft.Azure.Management.ResourceManager**. Cliquez sur **installer**, de **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter les licences.

4. Dans le Gestionnaire de package Nuget, recherchez **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Cliquez sur **installer**, de **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter la licence.

5. Dans Program.cs, remplacez les instructions **using** existantes par les éléments suivants :

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. Dans Program.cs, ajoutez les variables statiques suivantes, remplacer les valeurs d’espace réservé. Vous avez de **ApplicationId** **SubscriptionId**, **TenantId**et de **mot de passe** plus haut dans ce didacticiel. **Le stockage Azure votre nom de compte** est le nom du compte de stockage Azure où vous stockez vos fichiers de modèles du Gestionnaire de ressources Azure. **Nom du groupe de ressources** est le nom du groupe de ressources que vous utilisez lorsque vous créez le concentrateur IoT, il peut être un groupe de ressources existant ou un nouveau. **Nom du déploiement** est un nom pour le déploiement, par exemple **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Envoyer un modèle de gestionnaire de ressources Azure pour créer un concentrateur IoT

Utiliser un fichier de modèle et de paramètre JSON pour créer un concentrateur IoT dans votre groupe de ressources. Vous pouvez également utiliser un modèle du Gestionnaire de ressources Azure pour apporter des modifications à un concentrateur IoT existant.

1. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet, cliquez sur **Ajouter**, puis cliquez sur **Nouvel élément**. Ajoutez un fichier JSON appelé **template.json** à votre projet.

2. Remplacez le contenu de **template.json** par la définition de la ressource suivante pour ajouter un concentrateur IoT standard pour la région **Des États-Unis** (pour la liste actuelle des zones qui prennent en charge le concentrateur de IoT consultez [L’état Azure][lnk-status]) :

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

3. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet, cliquez sur **Ajouter**, puis cliquez sur **Nouvel élément**. Ajoutez un fichier JSON appelé **parameters.json** à votre projet.

4. Remplacez le contenu de **parameters.json** par les informations de paramètre suivantes qui définit un nom pour le nouveau concentrateur IoT telle que **{vos initiales} mynewiothub**. Notez que le nom de concentrateur IoT doit être globalement unique qu’il doit inclure votre nom ou les initiales) :

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. Dans l' **Explorateur de serveurs**, connectez-vous à votre abonnement Azure et dans votre compte de stockage Azure créer un conteneur appelé **modèles**. Dans le panneau **Propriétés** , définissez les autorisations de **Public un accès en lecture** pour le conteneur **modèles** de **Blob**.

6. Dans l' **Explorateur de serveurs**, avec le bouton droit sur le conteneur **modèles de** , puis cliquez sur **Conteneur de Blob de vue**. Cliquez sur le bouton **Charger les Blob** , sélectionnez deux fichiers, **parameters.json** et **templates.json**, puis cliquez sur **Ouvrir** pour charger les fichiers JSON au conteneur **modèles de** . Les URL des blobs contenant les données JSON sont les suivantes :

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. Dans Program.cs, ajoutez la méthode suivante :
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Ajoutez le code suivant à la méthode **CreateIoTHub** pour envoyer les fichiers de modèle et de paramètre au Gestionnaire de ressources Azure :

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Ajoutez le code suivant à la méthode **CreateIoTHub** qui affiche l’état et les touches pour le nouveau concentrateur IoT :

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Terminer et exécuter l’application

Vous pouvez maintenant exécuter l’application en appelant la méthode **CreateIoTHub** avant de générer et d’exécuter.

1. Ajoutez le code suivant à la fin de la méthode **Main** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Cliquez sur **créer** , puis sur **Générer la Solution**. Corrigez les erreurs.

3. Cliquez sur **Déboguer** , puis sur **Démarrer le débogage** pour exécuter l’application. Elle peut prendre plusieurs minutes pour le déploiement à exécuter.

4. Vous pouvez vérifier que votre application a ajouté le nouveau concentrateur IoT en visitant le [portail Azure] [ lnk-azure-portal] et affichez votre liste de ressources ou à l’aide de l’applet de commande PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Cet exemple d’application ajoute un concentrateur IoT Standard S1 pour lesquels vous êtes facturé. Vous pouvez supprimer le concentrateur IoT via le [portail Azure] [ lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Supprimer-AzureRmResource** lorsque vous avez terminé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez déployé un concentrateur IoT à l’aide d’un modèle de gestionnaire de ressources Azure avec un programme C#, vous souhaiterez peut-être Explorer davantage :

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
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
