<properties
    pageTitle="Créer un concentrateur IoT à l’aide de l’API REST | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide de l’API REST pour créer un concentrateur IoT."
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

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>Didacticiel : Créer un concentrateur IoT à l’aide d’un programme C# et l’API REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser le [fournisseur de ressources IoT concentrateur API REST] [ lnk-rest-api] de créer et de gérer par programme les concentrateurs d’Azure IoT. Ce didacticiel vous montre comment utiliser le fournisseur de ressources IoT concentrateur API REST pour créer un concentrateur IoT à partir d’un programme C#.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources Azure et classique](../resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement d’Azure le Gestionnaire de ressources.

Pour terminer ce didacticiel, vous devez les éléments suivants :

- Microsoft Visual Studio 2015.
- Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] dans quelques minutes.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou une version ultérieure.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Préparer votre projet Visual Studio

1. Dans Visual Studio, créez un projet Visual C# Windows en utilisant le modèle de projet **Application Console** . Nommez le projet **CreateIoTHubREST**.

2. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet et puis cliquez sur **Gérer les Packages NuGet**.

3. Dans le Gestionnaire de package Nuget, cochez **inclure la version préliminaire**et recherchez les **Microsoft.Azure.Management.ResourceManager**. Cliquez sur **installer**, de **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter les licences.

4. Dans le Gestionnaire de package Nuget, recherchez **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Cliquez sur **installer**, de **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **J’accepte** pour accepter la licence.

6. Dans Program.cs, remplacez les instructions **using** existantes par les éléments suivants :

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. Dans Program.cs, ajoutez les variables statiques suivantes, remplacer les valeurs d’espace réservé. Vous avez de **ApplicationId** **SubscriptionId**, **TenantId**et de **mot de passe** plus haut dans ce didacticiel. **Nom du groupe de ressources** est le nom du groupe de ressources que vous utilisez lorsque vous créez le concentrateur IoT, il peut être un groupe de ressources existant ou un nouveau. **Nom IoT Hub** est le nom du concentrateur IoT que vous créez, par exemple **MyIoTHub** (ce nom doit être globalement unique, donc il doit inclure votre nom ou des initiales). **Nom du déploiement** est un nom pour le déploiement, par exemple **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>Utiliser l’API REST pour créer un concentrateur IoT

Utiliser l' [API REST de concentrateur IoT] [ lnk-rest-api] pour créer un concentrateur IoT dans votre groupe de ressources. Vous pouvez également utiliser l’API REST pour apporter des modifications à un concentrateur IoT existant.

1. Dans Program.cs, ajoutez la méthode suivante :
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Ajoutez le code suivant à la méthode **CreateIoTHub** pour créer un objet **HttpClient** avec le jeton d’authentification dans les en-têtes :

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Ajoutez le code suivant à la méthode **CreateIoTHub** pour décrire le concentrateur IoT pour créer et générer une représentation sous forme de JSON (pour la liste actuelle des emplacements qui prennent en charge le concentrateur de IoT voir [Azure état][lnk-status]) :

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Ajoutez le code suivant à la méthode **CreateIoTHub** pour soumettre la demande reste sur Azure, vérification de la réponse et extraire l’URL que vous pouvez utiliser pour surveiller l’état de la tâche de déploiement :

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Ajoutez le code suivant à la fin de la méthode **CreateIoTHub** à utiliser l’adresse de **l’asyncStatusUri** extraite à l’étape précédente à attendre pour terminer le déploiement :

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Ajoutez le code suivant à la fin de la méthode **CreateIoTHub** pour récupérer les clés du concentrateur IoT vous avez créé et les imprimer à la console :

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>Terminer et exécuter l’application

Vous pouvez maintenant exécuter l’application en appelant la méthode **CreateIoTHub** avant de générer et d’exécuter.

1. Ajoutez le code suivant à la fin de la méthode **Main** :

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Cliquez sur **créer** , puis sur **Générer la Solution**. Corrigez les erreurs.

3. Cliquez sur **Déboguer** , puis sur **Démarrer le débogage** pour exécuter l’application. Elle peut prendre plusieurs minutes pour le déploiement à exécuter.

4. Vous pouvez vérifier que votre application a ajouté le nouveau concentrateur IoT en visitant le [portail Azure] [ lnk-azure-portal] et affichez votre liste de ressources ou à l’aide de l’applet de commande PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Cet exemple d’application ajoute un concentrateur IoT Standard S1 pour lesquels vous êtes facturé. Lorsque vous avez terminé, vous pouvez supprimer le concentrateur IoT via le [portail Azure] [ lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Supprimer-AzureRmResource** lorsque vous avez terminé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez déployé un concentrateur IoT à l’aide de l’API REST, vous souhaiterez peut-être Explorer davantage :

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
