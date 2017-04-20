<properties
    pageTitle="par programme surveiller les tâches de flux de données Analytique | Microsoft Azure"
    description="Découvrez comment contrôler par programme les travaux de flux Analytique créés via Powershell, Azure SDK ou des API de reste."
    keywords="Moniteur de .net, tâche analyse de la fenêtre, application"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Créer par programmation un moniteur de travail Analytique de flux
 Cet article explique comment faire pour activer l’analyse pour une tâche de flux de données Analytique. Analytique de flux tâches créées via les API du reste, Azure SDK ou Powershell ne pas avoir surveillance activé par défaut.  Vous pouvez activer manuellement cette dans le portail Azure par page de moniteur du travail en cliquant sur le bouton Activer ou vous pouvez automatiser ce processus en suivant les étapes décrites dans cet article. Les données d’analyse apparaît dans l’onglet « Moniteur » dans le portail Azure pour votre tâche de flux de données Analytique.

![Moniteur de travail onglet tâches](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer cet article, vous devez disposer des éléments suivants :

- Visual Studio 2012 ou 2013.
- Téléchargez et installez le [Kit de développement .NET Azure](https://azure.microsoft.com/downloads/).
- Un travail Analytique de flux existant qui a besoin de contrôle.

## <a name="setup-a-project"></a>Un projet de configuration

1.  Créer une application console C# Visual Studio .net.
2.  Dans la Console du Gestionnaire de package, exécutez les commandes suivantes pour installer les packages NuGet. Le premier est le flux Analytique gestion .NET SDK Azure. Le second est le Kit de développement de moniteur Azure qui sera utilisé pour activer le contrôle. Il est le client Azure Active Directory qui sera utilisé pour l’authentification.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  Ajoutez la section appSettings suivant dans le fichier App.config.

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
Remplacez les valeurs pour le *SubscriptionId* et *ActiveDirectoryTenantId* avec votre abonnement Azure, ID de client. Vous pouvez obtenir ces valeurs en exécutant l’applet de commande PowerShell suivante :

    ```
    Get-AzureAccount
    ```
4.  Ajoutez le code suivant à l’aide des instructions dans le fichier source (Program.cs) dans le projet.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  Ajoutez une méthode d’authentification.

        public static string GetAuthorizationHeader()
            {
                AuthenticationResult result = null;
                var thread = new Thread(() =>
                {
                    try
                    {
                        var context = new AuthenticationContext(
                            ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                            ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                        result = context.AcquireToken(
                            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                            clientId: ConfigurationManager.AppSettings["AsaClientId"],
                            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                            promptBehavior: PromptBehavior.Always);
                    }
                    catch (Exception threadEx)
                    {
                        Console.WriteLine(threadEx.Message);
                    }
                });

                thread.SetApartmentState(ApartmentState.STA);
                thread.Name = "AcquireTokenThread";
                thread.Start();
                thread.Join();

                if (result != null)
                {
                    return result.AccessToken;
                }

                throw new InvalidOperationException("Failed to acquire token");
        }

## <a name="create-management-clients"></a>Créer des Clients de gestion
Le code suivant définit les variables nécessaires et les clients de gestion.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Activer l’analyse d’un projet d’Analytique de flux existant

Le code suivant permettra de surveillance d’un projet de flux de données Analytique **existant** . La première partie du code effectue une demande GET sur le service de flux de données Analytique pour récupérer des informations sur la tâche de flux Analytique donnée. Il utilise la propriété « Id » (extraite de la requête GET) comme paramètre pour la méthode Put dans la seconde moitié du code qui envoie une commande PUT demander au service de perspectives pour activer l’analyse de la tâche de flux de données Analytique.

> [AZURE.WARNING]
> Si vous avez précédemment activé la surveillance pour un autre poste Analytique de flux de données, soit via le portail d’Azure, ou par programme via la sous code, **qu’il est recommandé de fournir le même nom de compte de stockage que vous avez utilisé lorsque vous avez activé précédemment surveillance.**
>
> Le compte de stockage est lié à la région que vous avez créé votre travail de flux de données Analytique dans, ne sont pas spécifiquement à la tâche elle-même.
>
> Tous les Analytique de flux de travail (et toutes les autres ressources Azure) dans cette région même partagent ce compte de stockage pour stocker des données d’analyse. Si vous fournissez un compte de stockage différents, il peut provoquer des effets secondaires involontaires à la surveillance de vos autres tâches Analytique des flux ou les autres ressources Azure.
>
> Le nom du compte de stockage permet de remplacer ```“<YOUR STORAGE ACCOUNT NAME>”``` ci-dessous doit être un compte de stockage qui se trouve dans le même abonnement comme l’Analytique de flux de travail vous sont l’activation de la surveillance.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
