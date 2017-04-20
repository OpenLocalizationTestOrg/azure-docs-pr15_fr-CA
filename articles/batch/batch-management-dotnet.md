<properties
    pageTitle="Gestion des ressources avec .NET de gestion de traitement par lots de compte | Microsoft Azure"
    description="Créer, supprimer et modifier des ressources de compte Azure lot avec la bibliothèque .NET de gestion de traitement par lots."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Gérer les quotas avec .NET de gestion de traitement par lots et comptes Batch d’Azure

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Gestion de lot .NET](batch-management-dotnet.md)

Vous pouvez réduire maintenance charge dans vos applications Azure lot en utilisant le [Lot gestion .NET] [ api_mgmt_net] bibliothèque pour automatiser la création de comptes de lot, la suppression, la gestion des clés et découverte de quota.

- **Créer et supprimer des comptes de lot** dans n’importe quelle région. Si, par exemple, sous la forme d’un éditeur de logiciels indépendant (ISV) vous fournissez un service pour vos clients, dans laquelle est attribué à chacun un compte séparé de traitement par lots pour la facturation, vous pouvez ajouter des fonctionnalités de création et la suppression de compte à votre portail client.
- **Récupérer et compte de régénérer les clés** par programme à tous vos comptes de lot. Cela peut vous aider à satisfaire aux stratégies de sécurité qui mettent en application la substitution périodique ou l’expiration des clés de compte. Lorsque vous avez plusieurs comptes de traitement par lots dans différentes régions d’Azure, l’automatisation de ce processus de substitution augmente l’efficacité de votre solution.
- **Vérifiez vos quotas de compte** et prendre les incertitudes d’essais et d’erreurs déterminer quels comptes de lot ont les limites. En vérifiant les quotas de votre compte avant de démarrer les travaux, création de pools, ou en ajoutant des nœuds de calcul, vous pouvez ajuster proactive de where ou lorsque ces ressources sont créées. Vous pouvez déterminer les comptes qui nécessitent que les augmentations de quota avant d’allouer des ressources supplémentaires dans ces comptes.
- **Combiner les fonctionnalités d’autres services Azure** pour une expérience de gestion complète, à l’aide de .NET de gestion de traitement par lots, [Azure Active Directory][aad_about]et le [Gestionnaire de ressources Azure] [ resman_overview] ensemble dans la même application. À l’aide de ces fonctionnalités et leurs API, vous pouvez fournir une expérience d’authentification frictionless, la possibilité de créer et de supprimer des groupes de ressources et des capacités qui sont décrites ci-dessus pour une solution de gestion de bout en bout.

> [AZURE.NOTE] Bien que cet article se concentre sur la gestion par programme de vos comptes par lots, les clés et les quotas, vous pouvez effectuer la plupart de ces activités à l’aide du [portail Azure][azure_portal]. Pour plus d’informations, consultez [Création d’un compte Azure lot à l’aide du portail Azure](batch-account-create-portal.md) et [les Quotas et les limites pour le service de traitement par lots d’Azure](batch-quota-limit.md).

## <a name="create-and-delete-batch-accounts"></a>Créer et supprimer des comptes de lot

Comme mentionné, une des fonctionnalités principales de l’API de gestion de traitement par lots est pour créer et supprimer des comptes de lot dans une région d’Azure. Pour ce faire, utilisez [BatchManagementClient.Account.CreateAsync] [ net_create] et [DeleteAsync][net_delete], ou leurs homologues synchrones.

L’extrait de code suivant crée un compte, obtient le compte nouvellement créé à partir du service de traitement par lots et puis le supprime. Dans cet extrait de code et les autres dans cet article, `batchManagementClient` est une instance complètement initialisée de [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Les applications qui utilisent la bibliothèque .NET de gestion de traitement par lots et sa classe BatchManagementClient besoin d’un accès **administrateur du service** ou de **coadministrator** à l’abonnement qui possède le compte de lot à gérer. Pour plus d’informations, consultez la section [Azure Active Directory](#azure-active-directory) et [AccountManagement] [ acct_mgmt_sample] exemple de code.

## <a name="retrieve-and-regenerate-account-keys"></a>Récupérer ou de régénérer les clés de compte

Obtenir des clés de compte principaux et secondaires à partir de n’importe quel compte de traitement par lots dans votre abonnement à l’aide de [ListKeysAsync][net_list_keys]. Vous pouvez régénérer ces clés à l’aide de [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] Vous pouvez créer un flux de travail rationalisé de connexion pour vos applications de gestion. Tout d’abord, obtenir une clé de compte pour le compte de lot que vous souhaitez gérer avec [ListKeysAsync][net_list_keys]. Ensuite, utilisez cette clé lors de l’initialisation de la bibliothèque commandes .NET [BatchSharedKeyCredentials] [ net_sharedkeycred] (classe), qui est utilisé lors de l’initialisation de [BatchClient][net_batch_client].

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Vérifiez abonnement Azure et compte les quotas de lot

Abonnements Azure et Azure services individuels comme traitement par lots de tous disposent quotas par défaut limitent le nombre de certaines entités qu’ils contiennent. Pour les quotas par défaut pour les abonnements d’Azure, consultez [abonnement Azure et les limites de service, les quotas et les contraintes](./../azure-subscription-service-limits.md). Pour les quotas par défaut du service de traitement par lots, consultez [des Quotas et des limites pour le service de traitement par lots d’Azure](batch-quota-limit.md). À l’aide de la bibliothèque .NET de gestion de traitement par lots, vous pouvez vérifier ces quotas dans vos applications. Cela vous permet de prendre des décisions d’allocation avant d’ajouter des comptes ou calculer les ressources telles que les pools et les nœuds de calcul.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Vérifier un abonnement Azure pour compte les quotas de lot

Avant de créer un compte de traitement par lots dans une région, vous pouvez vérifier votre abonnement Azure pour voir si vous êtes en mesure d’ajouter un compte dans cette région.

Dans l’extrait de code ci-dessous, nous avons tout d’abord utiliser [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] pour obtenir une collection de tous les comptes de lot se trouvant dans un abonnement. Une fois que nous avons obtenu cette collection, nous déterminer combien de comptes est dans la région cible. Puis nous utilisons [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] pour obtenir le quota de compte par lots et de déterminer le nombre de comptes (le cas échéant) peut être créé dans cette région.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Dans l’extrait ci-dessus, `creds` est une instance de [TokenCloudCredentials][azure_tokencreds]. Pour voir un exemple de création de cet objet, consultez [AccountManagement] [ acct_mgmt_sample] exemple de code de GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Vérifier un compte de traitement par lots pour que les quotas de ressources de calcul

Avant d’augmenter les ressources informatiques de votre solution de traitement par lots, vous pouvez vérifier pour vous assurer les ressources que vous souhaitez allouer n’excède pas les quotas du compte. Dans l’extrait de code ci-dessous, nous imprimer les informations de quota pour le compte de traitement par lots nommé `mybatchaccount`. Dans votre application, vous pouvez utiliser ces informations pour déterminer si le compte peut gérer les ressources supplémentaires à créer.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] S’il existe des quotas par défaut pour les services et les abonnements Azure, bon nombre de ces limites peuvent être déclenchés par l’émission d’une demande dans le [portail Azure][azure_portal]. Voir, par exemple, [les Quotas et les limites pour le service de traitement par lots d’Azure](batch-quota-limit.md) pour obtenir des instructions sur l’augmentation de votre compte les quotas de traitement par lots.

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Lot de gestion .NET, AD Azure et le Gestionnaire de ressources

Lorsque vous travaillez avec la bibliothèque .NET de gestion de traitement par lots, vous en général également utiliserez [Azure Active Directory] [ aad_about] (Azure AD) et le [Gestionnaire de ressources Azure][resman_overview]. L’exemple de projet décrit ci-dessous utilise Azure Active Directory et le Gestionnaire de ressources pendant qu’il illustre l’API .NET de gestion par lots.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure lui-même utilise Azure AD pour l’authentification des clients, les administrateurs de service et d’organisation utilisateurs. Dans le contexte de .NET de gestion de traitement par lots, vous utilisez AD Azure pour authentifier un administrateur d’abonnement ou co l’administrateur. Cela permet à la bibliothèque de gestion interroger le service de traitement par lots et d’effectuer les opérations décrites dans cet article.

Dans l’exemple de projet indiqué ci-dessous, la [Bibliothèque de l’authentification Active Directory] d' Azure[ aad_adal] (ADAL) est utilisé pour demander à l’utilisateur ses informations d’identification de Microsoft. Lorsque les informations d’identification administrateur ou coadministrator service sont fournies, l’application peut interroger Azure pour obtenir la liste des abonnements--et peut créer et supprimer les groupes de ressources et les comptes de lot.

### <a name="resource-manager"></a>Gestionnaire de ressources

Lorsque vous créez des comptes de lot avec la bibliothèque .NET de gestion de traitement par lots, vous allez généralement créer les au sein d’un [groupe de ressources][resman_overview]. Vous pouvez créer le groupe de ressources par programme à l’aide de la [ResourceManagementClient] [ resman_client] classe dans le [Gestionnaire de ressources .NET] [ resman_api] bibliothèque. Ou vous pouvez ajouter un compte à un groupe de ressources existant que vous avez créée précédemment à l’aide du [portail Azure][azure_portal].

## <a name="sample-project-on-github"></a>Exemple de projet sur GitHub

Pour découvrir .NET de gestion de traitement par lots en action, consultez [AccountManagment] [ acct_mgmt_sample] exemple de projet sur GitHub. Cette application console illustre la création et l’utilisation de [BatchManagementClient] [ net_mgmt_client] et [ResourceManagementClient][resman_client]. Il illustre également l’utilisation de la [Bibliothèque de l’authentification Active Directory] d' Azure[ aad_adal] (ADAL), qui est requis par les clients.

Pour exécuter l’exemple d’application, vous devez d’abord l’inscrire avec Azure AD à l’aide du portail Azure. Suivez les étapes de la section [Ajout d’une Application](../active-directory/active-directory-integrating-applications.md#adding-an-application) dans les [applications d’intégration avec Azure Active Directory] [ aad_integrate] pour enregistrer l’exemple d’application au sein de votre propre compte du répertoire par défaut. Veillez à sélectionner l' **Application Native de Client** pour le type d’application, et vous pouvez spécifier n’importe quel URI valide (tel que `http://myaccountmanagementsample`) pour l' **URI de redirection**: il n’a pas besoin d’être un véritable point de terminaison.

Après avoir ajouté votre application, déléguer l’autorisation de **Gestion des services Azure Access en tant qu’organisation** de l’application de *l’API de gestion de Service de Windows Azure* dans les paramètres de l’application dans le portail :

![Autorisations d’application dans Azure portal][2]

> [AZURE.TIP] Si **l’API de gestion des services Windows Azure** n’apparaît pas sous *autorisations à d’autres applications*, cliquez sur **Ajouter application**, sélectionnez des **API de gestion de Service de Windows Azure**, puis cliquez sur le bouton de coche. Ensuite, déléguer les autorisations comme indiqué ci-dessus.

Une fois que vous avez ajouté l’application comme décrit ci-dessus, mettre à jour `Program.cs` dans les [AccountManagment] [ acct_mgmt_sample] exemple de projet avec l’URI de redirection de votre application et l’ID de Client. Vous pouvez trouver ces valeurs dans l’onglet de **configuration** de votre application :

![Configuration de l’application dans Azure portal][3]

[AccountManagment] [ acct_mgmt_sample] exemple d’application illustre les opérations suivantes :

1. Acquérir un jeton de sécurité d’Active Directory Azure à l’aide de [ADAL][aad_adal]. Si l’utilisateur n’est pas déjà connecté, ils sont invités pour leurs informations d’identification Azure.
2. À l’aide du jeton de sécurité obtenu à partir d’Active Directory Azure, créer un [SubscriptionClient] de[ resman_subclient] de requêtes Azure pour obtenir la liste des abonnements associés au compte. Cela permet la sélection d’un abonnement si plusieurs sont trouvés.
3. Créer un objet d’informations d’identification associé à l’abonnement sélectionné.
4. Créer des [ResourceManagementClient] [ resman_client] en utilisant les informations d’identification.
5. Utiliser les [ResourceManagementClient] [ resman_client] pour créer un groupe de ressources.
6. Utiliser les [BatchManagementClient] [ net_mgmt_client] pour effectuer plusieurs opérations de compte de lot :
  - Créez un compte de lot dans le nouveau groupe de ressources.
  - Obtenez le compte nouvellement créé à partir du service de traitement par lots.
  - Imprimer les clés de compte pour le nouveau compte.
  - Régénérer une nouvelle clé primaire pour le compte.
  - Imprimer les informations de quota pour le compte.
  - Imprimer les informations de quota pour l’abonnement.
  - Imprimer tous les comptes dans l’abonnement.
  - Supprimer le compte nouvellement créé.
7. Supprimer le groupe de ressources.

Avant de supprimer le groupe de comptes et de ressources créé par lots, vous pouvez inspecter les deux dans [Azure portal][azure_portal]:

![Azure portal affiche le groupe de ressources et le compte de traitement par lots][1]
<br />
*Azure portal affichant le nouveau groupe de ressources et le compte de traitement par lots*

[aad_about]: ../active-directory/active-directory-whatis.md "Nouveautés d’Azure Active Directory ?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénarios d’authentification pour Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Intégration des Applications avec Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
