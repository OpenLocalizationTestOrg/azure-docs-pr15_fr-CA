<properties
   pageTitle="Mise en route avec PowerShell de lot Azure | Microsoft Azure"
   description="Obtenir une brève introduction aux applets de commande PowerShell de Azure que vous pouvez utiliser pour gérer le service de traitement par lots d’Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Commencer à utiliser des applets de commande PowerShell de lot Azure
Avec les applets de commande PowerShell de lot d’Azure, vous pouvez effectuer et pour la plupart, les mêmes tâches à qu'effectuer avec les API de traitement par lots, le portail Azure et Azure Interface de ligne de commande (CLI) de script. Il s’agit d’une introduction rapide aux applets de commande que vous pouvez utiliser pour gérer vos comptes de lot et utiliser vos ressources de traitement par lots, tels que les pools, les travaux et les tâches.

Pour obtenir une liste complète des applets de commande de traitement par lots et la syntaxe de l’applet de commande détaillées, consultez la [référence d’applet de commande Batch d’Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

Cet article est basé sur les applets de commande PowerShell de Azure version 3.0.0. Nous recommandons de mettre votre PowerShell Azure fréquemment afin de tirer parti des améliorations et des mises à jour de service.

## <a name="prerequisites"></a>Conditions préalables

Effectuer les opérations suivantes pour utiliser Azure PowerShell pour gérer vos ressources de traitement par lots.

* [Installer et configurer Azure PowerShell](../powershell-install-configure.md)

* Exécutez l’applet de commande de **AzureRmAccount de la connexion** pour vous connecter à votre abonnement (le navire d’applets de commande Azure lot dans le module Gestionnaire de ressource Azure) :

    `Login-AzureRmAccount`

* **Enregistrer l’espace de noms de fournisseur de lot**. Cette opération doit être effectuée **une seule fois par abonnement**.

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gérer des clés et des comptes de lot

### <a name="create-a-batch-account"></a>Créer un compte de traitement par lots

**Nouveau-AzureRmBatchAccount** crée un compte de lot dans un groupe de ressources spécifié. Si vous ne disposez pas d’un groupe de ressources, en exécutant la cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) . Spécifiez l’une des régions Azure dans le paramètre **d’emplacement** , par exemple « US Central ». Par exemple :

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Ensuite, créez un lot compte dans le groupe de ressources, spécifiez un nom pour le compte dans <*nom_de_compte*> et l’emplacement et le nom de votre groupe de ressources. Création du compte de traitement par lots peut prendre un certain temps pour terminer. Par exemple :

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] Le nom de compte doit être unique dans la région Azure pour le groupe de ressources, contenir entre 3 et 24 caractères et utiliser des minuscules et chiffres uniquement.

### <a name="get-account-access-keys"></a>Obtenir des clés d’accès de compte
**Get-AzureRmBatchAccountKeys** affiche les touches d’accès rapide associées à un compte Azure lot. Par exemple, exécutez la commande suivante pour obtenir les clés primaires et secondaires du compte que vous avez créé.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Générer une nouvelle clé d’accès
**Nouveau-AzureRmBatchAccountKey** génère une nouvelle clé de compte principal ou secondaire pour un compte Azure lot. Par exemple, pour générer une nouvelle clé primaire pour votre compte de commandes, tapez :

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] Pour générer une nouvelle clé secondaire, spécifiez « Secondaire » pour le paramètre de **type de clé** . Vous devez regénérer les clés primaires et secondaires séparément.

### <a name="delete-a-batch-account"></a>Supprimer un compte de traitement par lots
**Supprimer-AzureRmBatchAccount** supprime un compte de traitement par lots. Par exemple :

    Remove-AzureRmBatchAccount -AccountName <account_name>

Lorsque vous y êtes invité, confirmez que vous souhaitez supprimer le compte. Suppression de compte peut prendre un certain temps pour terminer.

## <a name="create-a-batchaccountcontext-object"></a>Créer un objet BatchAccountContext

Pour s’authentifier à l’aide des applets de commande PowerShell de lot lorsque vous créez et gérez des pools lot, les tâches, les tâches et les autres ressources, commencez par créer un objet BatchAccountContext pour stocker votre nom de compte et des clés :

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Vous passez l’objet BatchAccountContext dans les applets de commande qui utilisent le paramètre **BatchContext** .

> [AZURE.NOTE] Par défaut, la clé primaire du compte est utilisé pour l’authentification, mais vous pouvez sélectionner explicitement la clé à utiliser en modifiant la propriété **KeyInUse** de l’objet BatchAccountContext : `$context.KeyInUse = "Secondary"`.

## <a name="create-and-modify-batch-resources"></a>Créer et modifier des ressources de traitement par lots
Applets de commande telles que **New-AzureBatchPool**, **New-AzureBatchJob**et **Nouveau-AzureBatchTask** permet de créer des ressources sous un compte de traitement par lots. Correspondantes sont disponibles **Get -** et applets de commande **Set-** mettre à jour les propriétés des ressources existantes et les applets de commande **Remove -** pour supprimer les ressources sous un compte de traitement par lots.

Lors de l’utilisation de la plupart de ces applets de commande, en plus du passage d’un objet BatchContext, vous devez créer ou passer des objets qui contiennent des paramètres de ressources détaillées, comme illustré dans l’exemple suivant. Consultez l’aide détaillée de chaque applet de commande pour obtenir des exemples supplémentaires.

### <a name="create-a-batch-pool"></a>Créer un pool de traitement par lots

Lors de la création ou la mise à jour d’un pool de traitement par lots, vous sélectionnez une configuration de service cloud ou une configuration de machine virtuelle pour le système d’exploitation sur les nœuds de calcul (voir la [Présentation des fonctionnalités de traitement par lots](batch-api-basics.md#pool)). Votre choix détermine si vos nœuds de calcul sont dupliqués parmi les [mises à jour de système d’exploitation invité de Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou avec l’une des images sur le marché d’Azure Linux ou la machine virtuelle de Windows prises en charge.

Lorsque vous exécutez **New-AzureBatchPool**, transmettez les paramètres de système d’exploitation dans un objet PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Par exemple, l’applet de commande suivante crée un nouveau pool de traitement par lots avec des nœuds de calcul de petite taille dans la configuration de service cloud, créer une image avec la dernière version du système d’exploitation de la famille 3 (Windows Server 2012). Ici, le paramètre **CloudServiceConfiguration** spécifie la variable *$configuration* l’objet PSCloudServiceConfiguration. Le paramètre **BatchContext** spécifie une variable défini précédemment *$context* que l’objet BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Le nombre de cibles de nœuds de calcul dans le nouveau pool est déterminé par une formule autoscaling. Dans ce cas, la formule est simplement **$TargetDedicated = 4**, indiquant le nombre de nœuds de calcul dans le pool est au maximum de 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Requête pour les pools, les tâches, tâches et autres informations

Utiliser les applets de commande **Get-AzureBatchPool**, **Get-AzureBatchJob**et **Get-AzureBatchTask** à la requête pour les entités créées sous un compte de traitement par lots.

### <a name="query-for-data"></a>Requête de données

Par exemple, utilisez **Get-AzureBatchPools** pour trouver vos pools. Par défaut cette requêtes pour tous les pools sous votre compte, en supposant que vous déjà enregistré l’objet BatchAccountContext dans *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Utiliser un filtre d’OData

Vous pouvez fournir un filtre OData à l’aide du paramètre de **filtre** pour rechercher uniquement les objets qui que vous intéressent. Par exemple, vous pouvez trouver tous les pools avec les identificateurs commençant par « monpool » :

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Cette méthode n’est pas aussi flexible que dans un pipeline de local à l’aide de « Where-Object ». Toutefois, la requête est envoyée au service par lots directement afin que tout le filtrage se produit côté serveur, l’enregistrement de la bande passante Internet.

### <a name="use-the-id-parameter"></a>Utilisez le paramètre d’Id

Une alternative à un filtre d’OData est d’utiliser le paramètre **Id** . Pour rechercher un pool spécifique avec l’id « monpool » :

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Le paramètre **Id** prend en charge uniquement les id intégral recherche, les pas de caractères génériques ou les filtres de style OData.

### <a name="use-the-maxcount-parameter"></a>Utilisez le paramètre MaxCount

Par défaut, chaque applet de commande renvoie un maximum de 1000 objets. Si vous atteignez cette limite, affiner votre filtre pour mettre des objets en moins ou explicitement défini à l’aide du paramètre **MaxCount** au maximum. Par exemple :

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Pour supprimer la limite supérieure, la valeur **MaxCount** ou égale à 0.

### <a name="use-the-powershell-pipeline"></a>Utiliser le pipeline de PowerShell

Applets de commande de lot peuvent exploiter le pipeline pour envoyer des données entre des applets de commande PowerShell. Cela a le même effet que la spécification d’un paramètre, mais il facilite l’utilisation de plusieurs entités.

Par exemple, rechercher et afficher toutes les tâches sous votre compte :

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Restart (Redémarrer) chaque nœud de calcul dans un pool :

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gestion de package d’application

Packages d’applications fournissent un moyen simple de déployer des applications sur les nœuds de calcul dans vos pools. Avec les applets de commande PowerShell de traitement par lots, vous pouvez télécharger et gérer des packages d’applications dans votre compte de traitement par lots et déployer les versions de package pour les nœuds de calcul.

**Créer** une application :

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Ajouter** un package d’application :

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Définir la **version par défaut** de l’application :

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Liste** les packages d’une application

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Supprimer** un package d’application

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Supprimer** une application

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] Vous devez supprimer toutes les versions de package d’application d’une application avant de supprimer l’application. Vous recevrez une erreur « Conflit » si vous essayez de supprimer une application qui a des packages d’application.

### <a name="deploy-an-application-package"></a>Déployer un package d’application

Vous pouvez spécifier un ou plusieurs packages d’application pour le déploiement lorsque vous créez un pool. Lorsque vous spécifiez un package au moment de la création de pool, il est déployé pour chaque nœud que le pool de jointures de nœud. Les packages sont également déployés lorsqu’un nœud est redémarré ou reconfiguration.

Spécifier la `-ApplicationPackageReference` option lors de la création d’un pool pour déployer un package d’application pour les nœuds de la grappe dès qu’ils rejoignent le pool. Tout d’abord, créez un objet **PSApplicationPackageReference** et le configurer avec la version de package et les Id d’application que vous souhaitez déployer sur les nœuds de calcul du pool :

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Maintenant créer le pool et spécifiez l’objet de référence de package comme argument de la `ApplicationPackageReferences` option :

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Vous trouverez plus d’informations sur les packages d’applications dans un [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md).

>[AZURE.IMPORTANT] Vous devez [lier un compte de stockage Azure](#linked-storage-account-autostorage) à votre compte de traitement par lots à utiliser des packages d’application.

### <a name="update-a-pools-application-packages"></a>Mettre à jour les packages d’applications d’un pool

Pour mettre à jour les applications attribuées à un pool existant, vous devez tout d’abord créer un objet PSApplicationPackageReference avec les propriétés souhaitées (version de package et les Id de l’application) :

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Obtenir le pool de traitement par lots, effacer tout package existant, ajoutez ensuite notre nouvelle référence de package et mettre à jour le service de traitement par lots avec les nouveaux paramètres de pool :

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Vous avez maintenant mis à jour les propriétés du pool dans le service de traitement par lots. Toutefois, pour déployer le nouveau package d’application pour les nœuds de calcul dans le pool, vous devez redémarrer ou créer une nouvelle image de ces nœuds. Vous pouvez redémarrer tous les nœuds dans un pool avec cette commande :

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] Vous pouvez déployer plusieurs packages d’application aux nœuds de calcul dans un pool. Si vous souhaitez *Ajouter* un package d’application au lieu de remplacer les packages actuellement déployés, omettez le `$pool.ApplicationPackageReferences.Clear()` ligne ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

* Pour la syntaxe de l’applet de commande détaillées et des exemples, consultez [référence d’applet de commande Batch d’Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Pour plus d’informations sur les applications et les packages d’applications dans le traitement par lots, consultez [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md).
