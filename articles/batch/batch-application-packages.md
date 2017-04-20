<properties
    pageTitle="Installation d’une application simple et gestion dans Azure lot | Microsoft Azure"
    description="Utiliser la fonctionnalité packages d’application de lot Azure pour gérer facilement plusieurs applications et versions pour une installation sur le traitement par lots des nœuds de calcul."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Déploiement d’applications avec des packages d’application Azure lot

La fonctionnalité de packages d’application de lot d’Azure fournit faciliter la gestion des demandes de tâche et leur déploiement vers les nœuds de calcul dans le pool. Avec des packages d’application, vous pouvez télécharger et gérer plusieurs versions d’applications que vos tâches sont exécutées, y compris leurs fichiers de support. Vous pouvez ensuite déployer automatiquement un ou plusieurs de ces applications aux nœuds de calcul dans le pool.

Dans cet article, vous apprendrez comment charger et gérer les packages d’applications dans le portail Azure. Vous apprendrez ensuite comment les installer sur des nœuds de calcul d’un pool avec le [Traitement par lots .NET] [ api_net] bibliothèque.

> [AZURE.NOTE] La fonctionnalité de packages d’application décrite ici remplace la fonctionnalité de « Lot applications » disponible dans les versions précédentes du service.

## <a name="application-package-requirements"></a>Exigences de package d’application

Vous devez [lier un compte de stockage Azure](#link-a-storage-account) à votre compte de traitement par lots à utiliser des packages d’application.

La fonctionnalité de packages d’application abordée dans cet article est compatible *uniquement* avec les pools de lots qui ont été créées après le 10 mars 2016. Packages d’application ne seront pas déployés pour les nœuds de calcul dans des pools créés avant cette date.

Cette fonctionnalité a été introduite dans [l’API REST de lot] [ api_rest] version 2015-12-01.2.2). et [Lot .NET] correspondant[ api_net] version 3.1.0 de la bibliothèque. Nous vous recommandons de toujours utiliser la dernière version de l’API lorsque vous travaillez avec le traitement par lots.

> [AZURE.IMPORTANT] Actuellement, seuls les pools de *CloudServiceConfiguration* prend en charge les packages d’applications. Vous ne pouvez pas utiliser des packages d’applications dans des pools créés à l’aide d’images de VirtualMachineConfiguration. Reportez-vous à la section de [configuration d’ordinateur virtuel](batch-linux-nodes.md#virtual-machine-configuration) de [nœuds de disposition Linux dans des pools d’Azure lot](batch-linux-nodes.md) pour plus d’informations sur les deux configurations différentes.

## <a name="about-applications-and-application-packages"></a>Sur les applications et les packages d’applications

Dans le lot d’Azure, une *application* fait référence à un ensemble de versions binaires qui peuvent être téléchargés automatiquement aux nœuds de calcul dans le pool. Un *package d’application* fait référence à un *ensemble spécifique* de ces programmes binaires et représente une *version* de donnée de l’application.

![Diagramme de haut niveau des applications et des packages d’applications][1]

### <a name="applications"></a>Applications

Une application dans un lot contienne une ou plus application packages et spécifie des options de configuration de l’application. Par exemple, une application peut spécifier la version de package d’application par défaut pour installer sur des nœuds de calcul et si ses packages pouvant être mis à jour ou supprimés.

### <a name="application-packages"></a>Packages d’applications

Un package d’application est un fichier .zip qui contient les fichiers binaires d’application et les fichiers de prise en charge qui sont requis pour l’exécution de vos tâches. Chaque package d’application représente une version spécifique de l’application.

Vous pouvez spécifier des packages d’application au niveau du pool et les tâches. Lorsque vous créez une tâche ou un pool, vous pouvez spécifier une ou plusieurs de ces packages et (éventuellement) une version.

* **Packages d’applications de pool** sont déployés sur *chaque* nœud dans le pool. Les applications sont déployées lorsqu’un nœud rejoint un pool, et lorsqu’il est redémarré ou reconfiguration.

    Packages d’applications de pool sont appropriées lorsque tous les nœuds dans un pool d’exécuter des tâches d’un projet. Vous pouvez spécifier un ou plusieurs packages d’application lorsque vous créez un pool, vous pouvez ajouter ou mettre à jour les packages d’un pool existant. Si vous mettez à jour les packages d’applications d’un pool existant, vous devez redémarrer les nœuds pour installer le nouveau package.

* **Packages d’applications de tâche** sont déployées uniquement à un nœud de calcul planifié pour exécuter une tâche, juste avant la ligne de commande de la tâche en cours d’exécution. Si la version et le package de l’application spécifiée est déjà sur le nœud, il n’est pas redéployé et le package existant est utilisé.

    Packages d’applications Office sont utiles dans les environnements de pool partagé, où différentes tâches sont exécutées sur un pool et le pool n’est pas supprimé lorsqu’un travail est terminé. Si votre projet a moins de tâches que les nœuds dans la liste, des packages d’application Office peuvent réduire le transfert de données depuis votre application est déployée uniquement sur les nœuds d’exécutent des tâches.

    Autres scénarios qui peuvent tirer parti des packages d’application de tâches sont des tâches qui utilisent une application particulièrement importante, mais uniquement un petit nombre de tâches. Par exemple, une étape de prétraitement ou une tâche de fusion, où l’application de traitement préalable ou de fusion est lourd.

> [AZURE.IMPORTANT] Il existe des restrictions sur le nombre d’applications et les packages d’applications dans un compte du lot, ainsi que la taille de lot maximale des applications. Pour plus d’informations sur ces limites, reportez-vous à la section [Quotas et limites pour le service de traitement par lots d’Azure](batch-quota-limit.md) .

### <a name="benefits-of-application-packages"></a>Avantages de packages d’applications

Packages d’applications peuvent simplifier le code de votre solution de traitement par lots et réduire le temps système requis pour gérer les applications qui exécutent des tâches.

Tâche de début de votre pool ne doit spécifier une longue liste de fichiers de ressources individuels pour installation sur les nœuds. Vous n’êtes pas obligé de gérer manuellement plusieurs versions de vos fichiers d’application dans le stockage Azure, ou sur les nœuds. Et bien, vous n’avez pas besoin de vous inquiéter à propos de la génération d' [URL de SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) pour fournir l’accès aux fichiers de votre compte de stockage. Traitement par lots fonctionne en arrière-plan avec le stockage Azure pour stocker les packages d’applications et de les déployer pour les nœuds de calcul.

## <a name="upload-and-manage-applications"></a>Télécharger et gérer des applications

Vous pouvez utiliser le [portail Azure] [ portal] ou de la bibliothèque [.NET de gestion de traitement par lots](batch-management-dotnet.md) pour gérer les packages d’application dans votre compte de traitement par lots. Dans les sections suivantes, nous avons d’abord lier un compte de stockage, puis discuter de l’ajout d’applications et de packages et de les gérer avec le portail.

### <a name="link-a-storage-account"></a>Lier un compte de stockage

Pour utiliser des packages d’application, vous devez d’abord lier un compte de stockage Azure à votre compte de traitement par lots. Si vous n’avez pas encore configuré un compte de stockage pour votre compte de traitement par lots, le portail Azure affichera un avertissement la première fois que vous cliquez sur la mosaïque **d’Applications** dans la lame **compte de traitement par lots** .

> [AZURE.IMPORTANT] Lot actuellement prend en charge *uniquement* le type de compte de stockage **polyvalente** comme décrit à l’étape 5, [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Lorsque vous liez un compte de stockage Azure à votre compte de lot, lien *uniquement* un compte de stockage **polyvalente** .

![Aucun compte de stockage ne configuré avertissement dans Azure portal][9]

Le service de traitement par lots utilise le compte de stockage associé pour le stockage et la récupération des packages d’application. Après avoir lié les deux comptes, traitement par lots peut déployer automatiquement les packages stockés dans le compte de stockage lié à vos nœuds de calcul. Cliquez sur **paramètres de compte de stockage** sur la blade **d’Avertissement** , puis cliquez sur **Compte de stockage** sur la lame de **Compte de stockage** pour lier un compte de stockage pour votre compte de traitement par lots.

![Choisissez la lame de compte de stockage Azure Portal][10]

Nous vous recommandons de créer un stockage compte *spécifiquement* pour une utilisation avec votre compte de traitement par lots et de sélectionner ici. Pour plus d’informations sur la création d’un compte de stockage, reportez-vous à la section « Création d’un compte de stockage » dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Une fois que vous avez créé un compte de stockage, vous pouvez ensuite la possibilité de le lier à votre compte de traitement par lots à l’aide de la lame de **Compte de stockage** .

> [AZURE.WARNING] Étant donné que le lot utilise le stockage Azure pour stocker vos packages de l’application, vous êtes [facturé comme normal] [ storage_pricing] pour les blocs de données blob. Veillez à prendre en compte la taille et le nombre de packages de votre application et supprimez régulièrement les packages obsolètes pour réduire les coûts.

### <a name="view-current-applications"></a>Afficher les applications en cours

Pour afficher les applications dans votre compte de traitement par lots, cliquez sur l’élément de menu des **Applications** dans le menu de gauche tout en visualisant la lame **compte de traitement par lots** .

![Mosaïque d’applications][2]

Cette action ouvre la blade **d’Applications** :

![Liste des applications][3]

La blade **d’Applications** affiche l’ID de chaque application dans votre compte et les propriétés suivantes :

* **Packages**--le nombre de versions associées à cette application.
* **Version par défaut**: la version qui sera installée si vous ne spécifiez pas une version lors de l’application d’un pool. Ce paramètre est facultatif.
* **Autoriser mises à jour**, la valeur qui spécifie si le package de mises à jour, les suppressions et les ajouts sont autorisés. Si la valeur est **non**, les mises à jour de package et les suppressions sont désactivées pour l’application. Uniquement des versions de lot application nouveaux peuvent être ajoutées. La valeur par défaut est **Oui**.

### <a name="view-application-details"></a>Afficher les détails

Cliquez sur une application dans la **Applications** de lame pour ouvrir la lame qui inclut les détails de cette application.

![Détails de l’application][4]

Dans la lame de détails d’application, vous pouvez configurer les paramètres suivants pour votre application.

* **Allow updates**--spécifier si ses packages d’application peuvent être mis à jour ou supprimés. Plus loin dans cet article, reportez-vous à la section « Mettre à jour ou supprimer un package d’application ».
* **Version par défaut**: spécifiez un package d’application par défaut pour déployer des nœuds de calcul.
* **Nom complet**: spécifiez un « » nom convivial votre solution peut utiliser lorsqu’il affiche des informations sur l’application, comme dans l’interface utilisateur d’un service que vous fournissez à vos clients par le biais de commandes de lots.

### <a name="add-a-new-application"></a>Ajoutez une nouvelle application

Pour créer une nouvelle application, ajoutez un package d’application et de spécifier un ID d’application de nouveaux et uniques. Le premier package d’application que vous ajoutez avec le nouvel ID d’application crée également la nouvelle application.

Cliquez sur **Ajouter** sur la blade **d’Applications** pour ouvrir la lame de la **nouvelle application** .

![Nouvelle blade d’application dans Azure portal][5]

La lame de la **nouvelle application** contient les champs suivants pour spécifier les paramètres de votre nouvelle application et le package d’application.

**Id de l’application**

Ce champ spécifie l’ID de votre nouvelle application, qui est soumis aux règles de validation de code de lot Azure standard :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris les traits d’union et des traits de soulignement.
* Ne peut pas contenir plus de 64 caractères.
* Doit être unique dans le compte de traitement par lots.
* Préserve et est indépendante de la.

**Version**

Spécifie la version du package d’application que vous téléchargez. Les chaînes de version sont soumis aux règles de validation suivantes :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris les traits d’union, des traits de soulignement et des périodes.
* Ne peut pas contenir plus de 64 caractères.
* Doit être unique au sein de l’application.
* Cas de conservation et de la casse.

**Package d’application**

Ce champ spécifie le fichier .zip qui contient les fichiers binaires d’application et les fichiers de prise en charge qui sont nécessaires pour exécuter l’application. Cliquez sur la zone **Sélectionner un fichier** ou sur l’icône de dossier pour rechercher et sélectionner un fichier .zip qui contient les fichiers de votre application.

Une fois que vous avez sélectionné un fichier, cliquez sur **OK** pour commencer le téléchargement vers le stockage Azure. Lorsque l’opération de téléchargement est terminée, vous serez averti et la lame va se fermer. En fonction de la taille du fichier que vous téléchargez et la vitesse de votre connexion réseau, cette opération peut prendre un certain temps.

> [AZURE.WARNING] Ne fermez pas la lame de la **nouvelle application** avant que l’opération de téléchargement soit terminée. Cela arrête le processus de téléchargement.

### <a name="add-a-new-application-package"></a>Ajouter un nouveau package d’application

Pour ajouter une nouvelle version de package d’application pour une application existante, sélectionnez une application dans la blade **d’Applications** , cliquez sur **Packages**, puis cliquez sur **Ajouter** pour ouvrir la lame **d’un package** .

![Ajouter des lames de package d’application dans Azure portal][8]

Comme vous pouvez le voir, les champs correspondent à celles de la lame de la **nouvelle application** , mais la zone **id de l’Application** est désactivée. Comme vous l’avez fait pour la nouvelle application, spécifiez la **Version** de votre nouveau package, recherchez votre fichier .zip de **package d’Application** , puis cliquez sur **OK** pour télécharger le package.

### <a name="update-or-delete-an-application-package"></a>Mettre à jour ou de supprimer un package d’application

Pour mettre à jour ou de supprimer un package d’application existant, ouvrez la lame de détails pour l’application, cliquez sur pour ouvrir la lame de **Packages** , cliquez sur **bouton de sélection** dans la ligne du package d’application que vous souhaitez modifier et sélectionnez l’action que vous souhaitez exécuter les **Packages** .

![Mettre à jour ou de supprimer le package dans Azure portal][7]

**Mise à jour**

Lorsque vous cliquez sur **mise à jour**, la lame du *package de mise à jour* s’affiche. Cette blade est similaire à la lame du *nouveau package d’application* , toutefois uniquement le champ de sélection de package est activé, ce qui vous permet de spécifier un nouveau fichier ZIP à télécharger.

![Lame de package de mise à jour dans Azure portal][11]

**Supprimer**

Lorsque vous cliquez sur **Supprimer**, vous êtes invité à confirmer la suppression de la version du lot et lot supprime le package à partir du stockage Azure. Si vous supprimez la version par défaut d’une application, le paramètre de **version par défaut** est supprimé de l’application.

![Supprimer l’application][12]

## <a name="install-applications-on-compute-nodes"></a>Installer des applications sur des nœuds de calcul

Maintenant que vous avez vu comment gérer les packages d’application avec le portail Azure, nous pouvons voir comment les déployer pour les nœuds de calcul et de les exécuter sur des tâches de traitement par lots.

### <a name="install-pool-application-packages"></a>Installer des packages d’application pool

Pour installer un package d’application sur tous les nœuds de calcul dans un pool, spécifiez une ou plusieurs application package *références* pour le pool. Les packages d’application que vous spécifiez pour un pool sont installés sur chaque nœud de calcul lorsque ce nœud rejoint le pool, et lorsque le nœud est redémarré ou de reconfiguration.

Dans lot .NET, spécifiez un ou plusieurs [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] lorsque vous créez un nouveau pool, ou pour un pool existant. [ApplicationPackageReference] [ net_pkgref] classe spécifie un ID d’application et la version à installer sur un pool nœuds de calcul.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Si un déploiement de package d’application échoue pour une raison quelconque, le service de traitement par lots marque le nœud [inutilisable][net_nodestate], et aucune tâche ne sera planifié pour l’exécution sur ce nœud. Dans ce cas, vous devez **redémarrer** le nœud à redémarrer le déploiement du package. Redémarrer le nœud permet également la planification des tâches à nouveau sur le nœud.

### <a name="install-task-application-packages"></a>Installer les packages d’applications Office

Similaire à un pool, vous spécifiez au package d’application et *les références* d’une tâche. Lorsqu’une tâche est planifiée pour s’exécuter sur un nœud, le package est téléchargé et extrait juste avant la ligne de commande de la tâche est exécutée. Si une version et le package spécifié est déjà installé sur le nœud, le package n’est pas téléchargé et le package existant est utilisé.

Pour installer un package d’application Office, configuration de la tâche [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] propriété :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Exécuter les applications installées

Les packages que vous avez spécifiés pour un pool ou d’une tâche sont téléchargés et extraits dans un répertoire nommé dans le `AZ_BATCH_ROOT_DIR` du nœud. Traitement par lots crée également une variable d’environnement qui contient le chemin d’accès au répertoire nommé. Les lignes de commande de tâche utiliser cette variable d’environnement lors du référencement de l’application sur le nœud. La variable est dans le format suivant :

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`et `version` sont des valeurs qui correspondent à la version de l’application et le package que vous avez spécifié pour le déploiement. Par exemple, si vous avez spécifié que la version 2.7 d’application *mélangeur* doit être installé, votre tâche de lignes de commande utiliserait cette variable d’environnement pour accéder à ses fichiers :

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Si vous spécifiez la version par défaut pour une application, vous pouvez omettre le suffixe de version. Par exemple, si vous définissez l’option « 2.7 » comme version par défaut pour l' application *blender*, vos tâches peuvent faire référence à la variable d’environnement suivante et ils exécuteront la version 2.7 :

`AZ_BATCH_APP_PACKAGE_BLENDER`

L’extrait de code suivant montre un exemple de ligne de commande tâche qui lance la version par défaut de l’application du *mélangeur* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Consultez les [paramètres d’environnement pour les tâches](batch-api-basics.md#environment-settings-for-tasks) dans [Présentation des fonctionnalités de traitement par lots](batch-api-basics.md) pour plus d’informations sur les paramètres d’environnement de nœud de calcul.

## <a name="update-a-pools-application-packages"></a>Mettre à jour les packages d’applications d’un pool

Si un pool existant a déjà été configuré avec un package d’application, vous pouvez spécifier un nouveau package pour le pool. Si vous spécifiez une nouvelle référence de package d’un pool, les conditions suivantes s’appliquent :

* Tous les nouveaux nœuds qui relient le pool et n’importe quel nœud existant qui est redémarré ou de reconfiguration va installer le package nouvellement spécifié.
* Calculer les nœuds qui sont déjà dans le pool lorsque vous mettez à jour les références du lot ne parvient pas à installer automatiquement le nouveau package d’application. Calculer les nœuds doivent être redémarrés ou de reconfiguration pour recevoir le nouveau package.
* Lors du déployée d’un nouveau package, les variables d’environnement créée reflètent les nouvelles références de package d’application.

Dans cet exemple, le pool existant a la version 2.7 de l’application du *blender* configurée comme l’un de ses [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. Pour mettre à jour les nœuds du pool avec la version 2.76b, spécifiez une nouvelle [ApplicationPackageReference] [ net_pkgref] avec la nouvelle version et valider la modification.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Maintenant que la nouvelle version a été configurée, les *nouveaux* nœuds qui associe le pool doit avoir la version 2.76b déployée sur celui-ci. Pour installer 2.76b sur les nœuds qui sont *déjà* dans le pool, le redémarrage et recréez les. Notez que les nœuds réamorcés conservera les fichiers à partir des précédents déploiements de packages.

## <a name="list-the-applications-in-a-batch-account"></a>Répertorier les applications dans un compte de traitement par lots

Vous pouvez répertorier les applications et les packages dans un compte de traitement par lots à l’aide de [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] méthode.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>De synthèse

Avec des packages d’application, vous pouvez aider vos clients sélectionner les applications pour leurs travaux et spécifier la version exacte à utiliser lors du traitement des travaux avec votre service de traitement par lots. Vous pouvez également fournir la possibilité pour vos clients de télécharger et de suivre leurs propres applications dans votre service.

## <a name="next-steps"></a>Étapes suivantes

* L' [API REST de lot] [ api_rest] fournit également la prise en charge pour travailler avec des packages d’application. Par exemple, consultez [applicationPackageReferences] [ rest_add_pool_with_packages] élément dans [Ajouter un pool à un compte] [ rest_add_pool] pour plus d’informations sur la façon de spécifier les packages à installer à l’aide de l’API REST. Consultez [Applications] [ rest_applications] pour plus d’informations sur la façon d’obtenir des informations sur l’application à l’aide de l’API REST de traitement par lots.

* Découvrez comment les [Gérer les quotas avec .NET de gestion de traitement par lots et comptes d’Azure lot](batch-management-dotnet.md)par programme. Le [Lot de gestion .NET] [ api_net_mgmt] bibliothèque peut activer des fonctionnalités de création et de suppression de compte pour votre application de traitement par lots ou d’un service.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramme de packages d’application générale"
[2]: ./media/batch-application-packages/app_pkg_02.png "Mosaïque d’applications Azure Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Lame d’applications Azure Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Lame de détails d’application dans Azure portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nouvelle blade d’application dans Azure portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Mettre à jour ou supprimer des packages déroulante dans Azure portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nouvelle lame de package d’application dans Azure portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Aucune alerte de compte de stockage lié"
[10]: ./media/batch-application-packages/app_pkg_10.png "Choisissez la lame de compte de stockage Azure Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Lame de package de mise à jour dans Azure portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Supprimer la boîte de dialogue confirmation package dans Azure portal"
