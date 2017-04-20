<properties 
   pageTitle="Créer des zones DNS et les jeux d’enregistrements dans DNS Azure à l’aide du Kit de développement .NET | Microsoft Azure" 
   description="Comment créer des jeux d’enregistrements et zones DNS dans Azure DNS à l’aide du Kit de développement .NET." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Créer des zones DNS et des jeux d’enregistrements à l’aide du Kit de développement .NET

Vous pouvez automatiser les opérations pour créer, supprimer ou mettre à jour les zones DNS, les jeux d’enregistrements et les enregistrements à l’aide de DNS SDK avec une bibliothèque de gestion de DNS .NET. Un projet Visual Studio complet est disponible [ici.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Créer un compte principal du service

En règle générale, l’accès par programme aux ressources Azure est accordé via un compte dédié plutôt que vos propres informations d’identification de l’utilisateur. Ces comptes dédiés sont appelés des comptes de « service principal ». Pour utiliser l’exemple de projet Azure SDK de DNS, vous devez d’abord créer un compte principal du service et attribuer les autorisations appropriées.

1. Suivez [ces instructions](../resource-group-authenticate-service-principal.md) pour créer un compte principal de service (l’exemple de projet Azure SDK de DNS suppose que l’authentification basée sur mot de passe.)

2. Créer un groupe de ressources ([Voici comment](../azure-portal/resource-group-portal.md)).

3. Azure RBAC permet d’accorder des autorisations « Collaborateur de Zone DNS » au groupe de ressources le compte principal du service ([Voici comment](../active-directory/role-based-access-control-configure.md).)

4. Si vous utilisez l’exemple de projet Azure SDK de DNS, modifiez le fichier « program.cs » comme suit :
    * Insérer les valeurs correctes pour le tenantId, le clientId (également appelé ID de compte), le secret (mot de passe de compte principal de service) et le subscriptionId tel qu’utilisé dans l’étape 1.
    * Entrez le nom du groupe de ressources sélectionné à l’étape 2.
    * Entrez un nom de zone DNS de votre choix.

## <a name="nuget-packages-and-namespace-declarations"></a>Les déclarations d’espace de noms et de packages NuGet

Pour utiliser le Kit de développement .NET de DNS Azure, vous devez installer le package NuGet **Bibliothèque de gestion de DNS Azure** et autres packages Azure requis.
 
1. Dans **Visual Studio**, ouvrez un projet ou un nouveau projet. 

2. Accédez à **Outils** **>** **du Gestionnaire de package de NuGet** **>** **Gérer les Packages NuGet pour Solution...**. 

3. Cliquez sur **Parcourir**, activez la case à cocher **inclure la version préliminaire** et tapez **Microsoft.Azure.Management.Dns** dans la zone de recherche.

4. Sélectionnez le package, puis cliquez sur **installer** pour l’ajouter à votre projet Visual Studio.
 
5. Répétez la procédure ci-dessus pour l’installation des modules suivants : **Microsoft.Rest.ClientRuntime.Azure.Authentication** et **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Ajouter des déclarations d’espace de noms

Ajoutez les déclarations d’espace de noms suivantes

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>Initialiser le client de gestion de DNS

La *DnsManagementClient* contient les méthodes et propriétés requises pour gérer des jeux d’enregistrements et zones DNS.  Le code suivant ouvre une session le compte principal du service et crée un objet DnsManagementClient.

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>Créer ou mettre à jour une zone DNS

Pour créer une zone DNS, tout d’abord un objet « Zone » est créé pour contenir les paramètres de la zone DNS. Étant donné que les zones DNS ne sont pas liés à une région spécifique, l’emplacement a la valeur 'global'. Dans cet exemple, un [Gestionnaire de ressources Azure 'tag'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) est également ajouté à la zone.

Pour créer ou mettre à jour la zone DNS d’Azure, l’objet de zone qui contient les paramètres de la zone est passé à la méthode *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

>[AZURE.NOTE] DnsManagementClient prend en charge trois modes de fonctionnement : synchrone ('CreateOrUpdate'), asynchrone ('CreateOrUpdateAsync'), ou asynchrone avec accès à la réponse HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Vous pouvez choisir un de ces modes, selon les besoins de votre application.

Azure DNS prend en charge l’accès concurrentiel optimiste, appelée [Etags](dns-getstarted-create-dnszone.md). Dans cet exemple, spécifiant « * » pour le « If-None-Match' en-tête indique DNS Azure pour créer une zone DNS s’il n’existe pas déjà.  L’appel échoue si une zone portant le nom spécifié existe déjà dans le groupe de ressources donné.

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>Créer des jeux d’enregistrements DNS et des enregistrements

Les enregistrements DNS sont gérés comme un jeu d’enregistrements. Un jeu d’enregistrements est un jeu d’enregistrements avec le même nom et le type d’enregistrement dans une zone.  Le nom de jeu d’enregistrements est par rapport au nom de la zone, pas le nom DNS qualifié complet.

Pour créer ou mettre à jour un enregistrement défini, un « jeu d’enregistrements » objet de paramètres est créé et transmis à *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Comme avec les zones DNS, il existe trois modes d’opération : synchrone ('CreateOrUpdate'), asynchrone ('CreateOrUpdateAsync'), ou asynchrone avec accès à la réponse HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Comme avec les zones DNS, opérations sur les jeux d’enregistrements prennent en charge l’accès concurrentiel optimiste.  Dans cet exemple, dans la mesure où ni de 'If-Match' ni de 'If-None-Match' est spécifié, le jeu d’enregistrements est toujours créé.  Cet appel écrase tout enregistrement existant est définie avec le même nom et le type d’enregistrement dans cette zone DNS.

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>Obtenir les zones et les jeux d’enregistrements

Les méthodes *DnsManagementClient.Zones.Get* et *DnsManagementClient.RecordSets.Get* respectivement de récupérer des zones et des ensembles d’enregistrements. Jeux d’enregistrements est identifiés par leur type, nom et le groupe de ressources et de la zone qu’ils existent dans. Les zones sont identifiées par leur nom et ils existent dans le groupe de ressources.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>Mise à jour d’un jeu d’enregistrements existant

Pour mettre à jour d’un jeu d’enregistrements DNS existant, tout d’abord récupérer le jeu d’enregistrements, puis à jour le contenu du jeu d’enregistrements, puis soumettre la modification.  Dans cet exemple, nous spécifions l’Etag à partir du jeu d’enregistrement récupéré dans le paramètre de 'If-Match'. L’appel échoue si une opération simultanée a modifié l’enregistrement de la valeur dans l’intervalle.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>Les zones de liste et les jeux d’enregistrements

Pour les zones de liste, utilisez les méthodes *DnsManagementClient.Zones.List...* , qui prend en charge de la liste de toutes les zones dans un groupe de ressources donné ou toutes les zones dans un abonnement Azure donné (parmi les différents groupes de ressources.) Pour répertorier les jeux d’enregistrements, utilisez les méthodes *DnsManagementClient.RecordSets.List...* , qui prend en charge d’une liste de tous les jeux d’enregistrements dans une zone donnée ou uniquement les jeux d’enregistrements d’un type spécifique.

Notez la liste des zones et des ensembles d’enregistrements résultant peuvent être paginés.  L’exemple suivant montre comment parcourir les pages de résultats. (Une taille artificiellement petite page de '2' est utilisée pour forcer la pagination ; ce paramètre doit être omis dans la pratique et la taille de page par défaut est utilisé.)

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>Étapes suivantes

Télécharger le [Kit de développement .NET Azure DNS exemple de projet](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), qui comprend d’autres exemples de la façon d’utiliser le SDK de .NET Azure DNS, y compris des exemples d’autres types d’enregistrements DNS.
