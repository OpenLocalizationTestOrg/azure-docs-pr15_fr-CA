<properties
    pageTitle="Comment le programme d’installation de clé de coffre-fort avec rotation des clés de bout en bout et d’audit | Microsoft Azure"
    description="Utilisez cette procédure pour vous aider à obtenir le programme d’installation avec la rotation de la clé et la surveillance des journaux de clé coffre-fort"
    services="key-vault"
    documentationCenter=""
    authors="swgriffith"
    manager="mbaldwin"
    tags=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="jodehavi;stgriffi"/>
#<a name="how-to-setup-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Comment le programme d’installation clé coffre-fort avec rotation des clés de bout en bout et d’audit

##<a name="introduction"></a>Introduction

Après avoir créé votre coffre-fort de clé Azure, vous ne pourrez pas exploiter ce coffre-fort pour stocker les clés et les Secrets. Vos applications n’est plus nécessaire pour rendre persistantes les clés des secrets, mais plutôt demande les à partir du coffre-fort clé selon vos besoins. Cela vous permet de mettre à jour les clés et les secrets sans impact sur le comportement de votre application, ce qui ouvre un large éventail de possibilités autour de la clé et le comportement de la gestion des secrets.

Cet article décrit un exemple de l’utilisation du coffre-fort de clé Azure pour stocker un secret, dans ce cas, une clé de compte de stockage Azure qui est accessible par une application. Il va également vous montrer la mise en œuvre d’une rotation planifiée de cette clé de compte de stockage. Enfin, il guidera une démonstration de comment surveiller les journaux d’audit de clés de coffre-fort et de déclencher des alertes lorsque des requêtes inattendues.

> \[AZURE. Remarque\] ce didacticiel n’est pas censé expliquer en détail l’ensemble initial de votre coffre-fort de clé Azure. Pour obtenir ces informations, consultez [mise en route de la chambre forte de clé Azure](key-vault-get-started.md). Ou, pour les instructions de l’Interface de ligne de commande de plusieurs plates-formes, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).

##<a name="setting-up-keyvault"></a>Configuration de KeyVault

Afin de permettre à une application d’extraire un secret de coffre-fort de clé d’Azure, vous devez tout d’abord créer le secret et téléchargez-le dans votre chambre. Pour ce faire facilement via PowerShell comme indiqué ci-dessous.

Démarrer une session PowerShell d’Azure et vous connecter à votre compte Azure avec la commande suivante :

```powershell
Login-AzureRmAccount
```

Dans la fenêtre contextuelle, entrez votre nom d’utilisateur de compte Azure et d’un mot de passe. PowerShell Azure obtiennent tous les abonnements qui sont associés à ce compte et par défaut, utilise la première.

Si vous disposez de plusieurs abonnements, vous devrez peut-être spécifier un spécifique qui a été utilisé pour créer votre coffre-fort de clé Azure. Tapez ce qui suit pour afficher les abonnements de votre compte :

```powershell
Get-AzureRmSubscription
```

Ensuite, pour spécifier l’abonnement associé à votre coffre-fort clé que se connectent, tapez :

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Comme cet article illustre le stockage d’une clé de compte de stockage comme un secret, vous devez obtenir cette clé de compte de stockage.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Après avoir récupéré votre secret, dans ce cas votre clé de compte de stockage, vous devrez qui convertir en une chaîne sécurisée et ensuite créer un secret avec cette valeur dans votre chambre forte de clé.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Ensuite, vous devez obtenir l’URI pour le secret que vous venez de créer. Il sera utilisé dans une étape ultérieure lorsque vous appelez le coffre-fort de la clé à récupérer votre code secret. Exécutez la commande PowerShell suivante et prenez note de la valeur de 'Id', qui est l’URI de secret.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##<a name="setting-up-application"></a>Configuration d’Application

Maintenant que vous avez un secret stocké, vous devez récupérer la clé et l’utiliser à partir du code. Quelques étapes sont nécessaires pour y parvenir, le premier et le plus important qui est l’enregistrement de votre application avec Azure Active Directory, puis de coffre-fort de clé Azure les informations de votre application afin qu’il peut autoriser les requêtes de votre application.

> \[AZURE. Remarque\] votre application doit être créée sur le même locataire Azure Active Directory en tant que coffre-fort de votre clé. 

Commencez par ouvrir l’onglet applications Azure Active Directory

![Applications ouvertes dans Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Cliquez sur 'Ajouter' pour ajouter une nouvelle application à votre annonce Azure

![Cliquez sur Ajouter une Application](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Laisser le type d’application en tant que « API WEB et/ou de l’Application Web » et donner un nom à votre application.

![Nom de l’Application](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Donnez à votre application une URL' session' et un 'URI d’ID App'. Il peuvent être tout ce que vous voulez pour cette démonstration et peuvent être modifiées ultérieurement si nécessaire.

![Fournir des URI requis](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Une fois que l’application est ajoutée à Active Directory Azure, vous serez amené à la page d’application. À partir de ce point cliquez sur l’onglet « Configuration » et puis recherchez et copiez la valeur de l’ID du Client. Prenez note de l’ID client pour les étapes ultérieures.

Vous devez ensuite générer une clé pour votre application pouvoir interagir avec votre annonce Azure. Vous pouvez le créer dans la section « Clés » dans l’onglet « Configuration ». Prenez note de la la clé qui vient d’être générée à partir de votre application Azure AD pour une utilisation dans une étape ultérieure.

![Clés d’application Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Avant d’établir des appels de votre application dans le coffre-fort de la clé, vous devrez indiquer le coffre-fort de la clé sur votre application et de son « autorisations. La commande suivante prend le nom de coffre-fort et de l’ID de client à partir de votre application Azure AD et accorde un accès de 'Get' pour clé vault pour l’application.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

À ce stade, vous êtes prêt à commencer à construire votre application appelle. Dans votre application, vous devrez tout d’abord installer les packages NuGet nécessaires pour interagir avec le coffre-fort de clé d’Azure et d’Azure Active Directory. Permet d’entrer les commandes suivantes à partir de la console de Gestionnaire de package de Visual Studio. Notez qu’à la rédaction de cet article la version actuelle du package Active Directory est 3.10.305231913, donc vous pouvez confirmer la version la plus récente et mettre à jour en conséquence.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Dans le code de votre application, créez une classe pour contenir la méthode d’authentification de votre Active Directory. Dans cet exemple que la classe est appelée « Utils ». Vous devrez ajouter la suivante à l’aide.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ensuite, ajoutez la méthode suivante pour récupérer le jeton JWT de publicité Azure. Pour la facilité de maintenance que vous souhaiterez peut-être déplacer le disque dur codée en valeurs de chaîne dans la configuration de votre application web ou.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Enfin, vous pouvez ajouter le code nécessaire pour appeler le coffre-fort de la clé et récupérer votre valeur secrète. Vous devez d’abord ajouter les éléments suivants à l’aide d’instruction.

```csharp
using Microsoft.Azure.KeyVault;
```

Ensuite, vous ajouterez les appels de méthode à appeler en chambre forte de la clé et récupérer votre secret. Dans cette méthode, vous fournira le secret URI sur lequel vous avez enregistré à une étape précédente. Notez l’utilisation de la méthode GetToken à partir de la classe Utils créée ci-dessus.
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Lorsque vous exécutez votre application, vous devez maintenant être s’authentifier sur Azure Active Directory et en récupérant ensuite votre valeur secrète du coffre de clé Azure.

##<a name="key-rotation-using-azure-automation"></a>Rotation des clés à l’aide d’Automation d’Azure

Il existe diverses options pour l’implémentation d’une stratégie de rotation pour les valeurs que vous stockez en tant que secrets d’Azure clé coffre-fort. Secrets peuvent être pivotés dans le cadre d’un processus manuel, ils peuvent pivoter par programmation en tirant parti des appels d’API, ou ils peuvent pivoter par un script d’automation. Pour les besoins de cet article nous va exploiter PowerShell Azure combiné avec Automation Azure pour modifier une touche d’accès de compte de stockage Azure, et puis nous mettrons à jour un secret de clé coffre-fort avec cette nouvelle clé. 

Vous devez obtenir l’ID client pour la connexion nommée « AzureRunAsConnection », qui a été créée lorsque vous avez établi votre instance Azure Automation afin de permettre l’automatisation Azure définir des valeurs secrètes dans votre chambre forte de clé. Vous pouvez accéder à ce code en choisissant « Actifs » à partir de votre instance d’automatisation d’Azure. À partir de là, vous choisissez « Connexions » et sélectionnez le principe de service 'AzureRunAsConnection'. Vous devez prendre note de l’ID d’Application. 

![ID de Client Automation Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Alors que vous êtes toujours dans la fenêtre d’actifs, vous devez également choisir « Modules ». À partir de modules sélectionnez 'Bibliothèque' et recherchez et 'Import' mise à jour des versions de chacun des modules suivants.

    Azure
    Azure.Storage   
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage
    
> \[AZURE. Remarque\] à la rédaction de cet article seulement ci-dessus noter les modules nécessaires à la mise à jour pour le script partagé ci-dessous. Si vous trouvez que votre travail d’automation est défectueux, vous devez vérifier que vous disposez bien de tous les modules nécessaires et leurs dépendances importés.

Après avoir récupéré l’ID d’application pour votre connexion Automation d’Azure, vous devrez indiquer votre coffre-fort de clé Azure que cette application a accès à mettre à jour les secrets dans votre chambre forte. Ceci est possible avec la commande PowerShell suivante.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Ensuite, vous allez sélectionner la ressource « Procédures opérationnelles » sous votre instance Azure Automation et sélectionnez « Ajouter une procédure opérationnelle ». Sélectionnez « Création rapide ». Nom de votre procédure opérationnelle et sélectionnez « PowerShell » comme type de procédure opérationnelle. Ajoutez éventuellement une description. Enfin, cliquez sur 'Créer'.

![Créer procédure opérationnelle](./media/keyvault-keyrotation/Create_Runbook.png)

Dans le volet de l’éditeur de votre procédure de nouveau opérationnelle, vous allez coller le script PowerShell suivant.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Dans le volet de l’éditeur, vous pouvez choisir « Volet Test » pour tester votre script. Une fois que le script s’exécute sans erreur, vous pouvez sélectionner l’option « Publier », et vous pouvez ensuite appliquer un calendrier pour la procédure opérationnelle au volet configuration de procédure opérationnelle.

##<a name="key-vault-auditing-pipeline"></a>Audit-clé de coffre-fort de pipeline

Lorsque vous installez un coffre-fort de clé Azure vous pouvez activer l’audit pour collecter des journaux sur les demandes d’accès mis la chambre forte de clé. Ces journaux est stockés dans un compte de stockage Azure désigné et peut ensuite être tiré, surveillé et analysé. Ci-dessous les parcours un scénario qui exploite les fonctions d’Azure, Azure logique d’applications et la clé de coffre-fort des journaux d’audit pour créer une opportunité pour envoyer un e-mail lorsque les secrets de la chambre forte sont extraites par une application qui ne correspond pas à l’id de l’application de l’application web.

Tout d’abord, vous devez activer la journalisation sur votre coffre-fort de clé. Ceci est possible via les commandes PowerShell suivantes (tous les détails sont visibles [ici](key-vault-logging.md)) :

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Une fois que cette option est activée, les journaux d’audit démarrera collecte dans le compte de stockage désigné. Ces journaux contiendra des événements sur la manière et lorsque vos coffres-forts clé sont accessibles et par qui. 

> \[AZURE. Remarque\] vous pouvez accéder à vos informations d’enregistrement au maximum, 10 minutes après la clé de la chambre forte de cette opération. Dans la plupart des cas, il sera plus rapide que cela.

L’étape suivante consiste à [créer une file d’attente du Bus des services Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Il s’agit d’où les journaux d’audit de clés de chambre forte sont transmis. Une fois sur la file d’attente, l’application logique seront les prélever et agissez sur celles-ci. Pour créer un Service de Bus est relativement directe et vous trouverez ci-dessous les étapes de haut niveau :

1. Créer un espace de noms du Bus de services (si vous possédez déjà un que vous souhaitez utiliser, puis passez à l’étape 2).
2. Recherchez le Bus de Service dans le portail et sélectionnez l’espace de noms que vous souhaitez créer la file d’attente dans.
3. Sélectionnez Nouveau et choisissez le Bus de Service -> file d’attente et entrez les informations requises.
4. Extraire les informations de connexion de Service Bus en choisissant l’espace de noms et en cliquant sur _Informations de connexion_. Vous devez ces informations pour la prochaine partie.

Ensuite, vous allez [créer une fonction d’Azure](../azure-functions/functions-create-first-azure-function.md) pour interroger des journaux dans le compte de stockage en chambre forte de la clé et la cueillette de nouveaux événements. Il s’agit d’une fonction qui est déclenchée sur une planification.

Créer une fonction d’Azure (choisissez Nouveau -> fonction App dans le portail). Lors de la création, vous pouvez utiliser un plan d’hébergement existant ou créer un nouveau. Vous pouvez également opter pour hébergement dynamique. Vous trouverez plus de détails sur les options d’hébergement de fonction [ici](../azure-functions/functions-scale.md).

Lors de la création de la fonction d’Azure, y accéder et choisissez une minuterie fonction et C\# puis cliquez sur **créer** à partir de l’écran de démarrage.

![Fonctions Azure démarrer la Blade](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Dans l’onglet _développer_ , remplacez le code de run.csx par le texte suivant :

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> \[AZURE. Remarque\] veillez à remplacer les variables dans le code ci-dessus pour pointer sur votre compte de stockage où sont écrits les journaux en chambre forte de la clé, le Bus de Service que vous avez créé précédemment et le chemin d’accès spécifique pour les journaux de stockage en chambre forte de clé.

La fonction récupère le dernier fichier de journal à partir du compte de stockage où sont écrits les journaux en chambre forte de la clé, extrait les événements les plus récents à partir de ce fichier et les envoie vers une file d’attente de Bus de Service. Dans la mesure où un seul fichier peut avoir plusieurs événements, par exemple, plus d’une heure complète, puis nous créer un fichier de _sync.txt_ la fonction examine également pour déterminer l’horodatage du dernier événement qui a été prélevé de. Cela garantit que nous ne poussez le même événement plusieurs fois. Ce fichier _sync.txt_ contient un horodatage pour le dernier événement a été détecté. Lors du chargement, les journaux doivent être triés en fonction de l’horodatage pour s’assurer qu’ils sont correctement ordonnés.

Pour cette fonction, nous faire référence à un certain nombre de bibliothèques supplémentaires qui ne sont pas disponibles dans les fonctions d’Azure. Pour inclure ces, nous avons besoin de fonctions Azure dessus à l’aide de nuget. Choisissez l’option _Afficher les fichiers_ 

![Afficher les fichiers, option](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

et ajoutez un nouveau fichier nommé _project.json_ avec le contenu suivant :

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Cela déclenche à _Enregistrer_ les fonctions Azure pour télécharger les fichiers binaires requis. 

Passer à l’onglet **d’intégrer** et de donner au paramètre timer un nom significatif à utiliser dans la fonction. Dans le code ci-dessus, il s’attend à être appelé _myTimer_de la minuterie. Spécifiez une [expression CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) comme suit : 0 \* \* \* \* \* de la minuterie qui provoque la fonction à exécuter une fois par minute. 

Dans le même onglet **d’intégrer** , ajoutez une entrée qui sera de type _Stockage de Blob Azure_. Il pointera vers le fichier _sync.txt_ qui contient l’horodatage du dernier événement étudié par la fonction. Cela sera disponible au sein de la fonction dans le nom du paramètre. Dans le code ci-dessus, l’entrée de stockage des objets Blob Azure s’attend à être _inputBlob_le nom du paramètre. Choisissez le compte de stockage dans lequel se trouve le fichier _sync.txt_ (il peut être le même ou un compte de stockage différentes) et dans le champ chemin d’accès, le chemin d’accès où le fichier réside, sous la forme {container-name}/path/to/sync.txt.

Ajouter une sortie qui sera de type sortie de _Stockage des objets Blob Azure_ . Il sera également pointer vers le fichier _sync.txt_ que vous venez de définir dans l’entrée. Cela permettra par la fonction pour écrire l’estampille du dernier événement étudié. Le code ci-dessus prévoit que ce paramètre soit appelé _outputBlob_.

À ce stade, la fonction est prête. Assurez-vous que vous revenez à l’onglet de **développer** et _Enregistrer_ le code. Vérifiez la fenêtre de sortie pour les erreurs de compilation et de les corriger en conséquence. Si elle compile, le code doit exécuter maintenant et chaque minute vérifie les journaux clé coffre-fort et envoyer tous les nouveaux événements dans la file d’attente de Bus de Service défini. Vous devriez voir les informations de journalisation écrire dans la fenêtre journal chaque fois que vous que la fonction est déclenchée.

###<a name="azure-logic-app"></a>Application de la logique d’Azure

Ensuite, nous devrons créer une application Azure logique de choisir les événements que la fonction est en exécutant un push de la file d’attente de Bus de Service, analyser le contenu et envoyer un courrier électronique en fonction d’une condition de mise en correspondance.

[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md) en cliquant sur Nouveau -> App de logique. 

Une fois l’application logique est créée, naviguer vers elle, puis choisissez _Modifier_. Dans l’éditeur de l’application de la logique, cliquez sur que la _File d’attente de Bus de Service_ managed api et entrez vos informations d’identification de Service Bus pour le connecter à la file d’attente.

![Bus de Service d’application Azure logique](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Cliquez ensuite sur _Ajouter une condition_. Dans la condition, basculez vers l' _Éditeur avancé_ et entrez les informations suivantes, le remplacement de la APP_ID avec la APP_ID réel de votre application web :

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Cette expression essentiellement retournera la **valeur false** si la propriété **appid** de l’événement entrant (ce qui est le corps du message de Bus de Service) n’est pas l' **appid** de l’application. 

Créez à présent une action sous l’option _Si aucune, ne faites rien..._ .

![Application de logique Azure choisissez action](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Pour l’action, cliquez sur _Office 365 - envoyer un e-mail_. Remplissez les champs pour créer un message électronique à envoyer lorsque la condition définie renvoie la valeur false. Si vous ne disposez pas d’Office 365 vous pourriez consulter alternatives pour atteindre la même.

À ce stade, vous avez un pipeline de bout en bout qui, une fois par minute, recherchera de nouveaux journaux d’audit de chambre forte de clé. Les nouveaux journaux qu’il trouve, il va les pousser à une file d’attente de Bus de Service. L’application logique sera déclenchée dès qu’un nouveau message arrive dans la file d’attente et si l’identificateur de l’événement ne pas correspond à l’id d’application de l’application appelante puis envoyer un courrier électronique. 
