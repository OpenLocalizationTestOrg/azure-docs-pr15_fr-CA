<properties
    pageTitle="Requêtes de liste efficace dans le traitement par lots d’Azure | Microsoft Azure"
    description="Augmenter les performances en filtrant vos requêtes lorsque vous demandez des informations sur les ressources de traitement par lots comme les pools, les tâches et les tâches et les nœuds de calcul."
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
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="query-the-azure-batch-service-efficiently"></a>Interroger le service de traitement par lots d’Azure efficacement

Ici, vous découvrirez comment augmenter les performances de votre application Azure lot en réduisant la quantité de données retournées par le service lorsque vous interrogez les tâches, les tâches et les nœuds avec le [Traitement par lots .NET] [ api_net] bibliothèque.

Presque toutes les applications de traitement par lots est nécessaire effectuer un certain type d’opération de surveillance ou d’autre que des requêtes du service de traitement par lots, souvent à intervalles réguliers. Par exemple, pour déterminer s’il existe des tâches en file d’attente dans une tâche, vous devez obtenir des données sur chaque tâche de la tâche. Pour déterminer l’état des nœuds dans le pool, vous devez obtenir des données sur chaque nœud dans le pool. Cet article explique comment exécuter des requêtes de la manière la plus efficace.

## <a name="meet-the-detaillevel"></a>Atteindre le niveau de détail

Dans une application de traitement par lots de production, des entités telles que les tâches, les tâches et les nœuds de calcul peuvent compter en milliers. Lorsque vous demandez des informations sur ces ressources, une quantité volumineuse de données doit « traverser le câble » à partir du service de traitement par lots à votre application sur chaque requête. En limitant le nombre d’éléments et le type d’informations retournées par une requête, vous pouvez augmenter la vitesse des requêtes et par conséquent les performances de votre application.

Ce [Traitement par lots .NET] [ api_net] extrait de code API répertorie *chaque* tâche qui est associée à une tâche, ainsi que *toutes* les propriétés de chaque tâche :

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Vous pouvez effectuer une requête de liste beaucoup plus efficace, toutefois, en appliquant un niveau de détail « » à votre requête. Pour ce faire, vous fournissant une [ODATADetailLevel] d'[ odata] objet à le [JobOperations.ListTasks] de[ net_list_tasks] méthode. Cet extrait retourne uniquement l’ID, ligne de commande et compute nœud des propriétés des informations de tâches achevées :

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Dans cet exemple, s’il y a des milliers de tâches dans le projet, les résultats de la deuxième requête seront en général être renvoyés beaucoup plus rapide que la première. Plus d’informations sur l’utilisation de ODATADetailLevel lorsque vous mettez des objets avec l’API .NET de lot est inclus [ci-dessous](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> Il est vivement recommandé que vous *toujours* fournir un objet ODATADetailLevel à votre liste des appels API .NET afin de garantir une efficacité maximale et les performances de votre application. En spécifiant un niveau de détail, vous pouvez aider à réduire le temps de réponse par lot, améliorer l’utilisation du réseau et réduire l’utilisation de la mémoire par les applications clientes.

## <a name="filter-select-and-expand"></a>Filtrer, sélectionnez et développez

Le [Traitement par lots .NET] [ api_net] et le [Reste du lot] [ api_rest] API permettre de réduire le nombre d’éléments qui sont retournés dans une liste, ainsi que la quantité d’informations retournées pour chaque. Pour cela, en spécifiant le **filtre**, **Sélectionnez**et **développez des chaînes** lors de l’exécution des requêtes de la liste.

### <a name="filter"></a>Filtre
La chaîne de filtrage est une expression qui permet de réduire le nombre d’éléments qui sont retournés. Par exemple, Lister uniquement les tâches en cours d’exécution pour une tâche, ou uniquement les nœuds de calcul que vous êtes prêts à exécuter les tâches de la liste.

- La chaîne de filtre se compose d’une ou plusieurs expressions, avec une expression qui se compose d’un nom de propriété, un opérateur et une valeur. Les propriétés qui peuvent être spécifiées sont spécifiques à chaque type d’entité que vous interrogez, comme le sont les opérateurs pris en charge pour chaque propriété.
- Plusieurs expressions peuvent être combinées à l’aide des opérateurs logiques `and` et `or`.
- Cet exemple montre comment filtrer des listes de chaînes uniquement l’exécution « rendu » tâches : `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Sélectionnez
La chaîne de sélection limite les valeurs de propriété sont renvoyées pour chaque article. Vous spécifiez une liste de noms de propriété, et que les valeurs de propriété sont renvoyées pour les éléments dans les résultats de la requête.

- La chaîne de sélection se compose d’une liste séparée par des virgules de noms de propriétés. Vous pouvez spécifier des propriétés pour le type d’entité que vous interrogez.
- Cet exemple de chaîne select Spécifie que seules trois valeurs de propriété doivent être retournées pour chaque tâche : `id, state, stateTransitionTime`.

### <a name="expand"></a>Développez
La chaîne de développement réduit le nombre d’appels d’API qui sont nécessaires pour obtenir certaines informations. Lorsque vous utilisez une chaîne de développer, plus d’informations sur chaque élément peuvent être obtenus avec un seul appel d’API. Au lieu de la première obtention de la liste des entités, puis demande d’informations pour chaque élément de la liste, une chaîne de développement vous permet d’obtenir les mêmes informations dans un seul appel d’API. Moins les appels API signifie des performances accrues.

- Similaire à la chaîne de sélection, la chaîne de développer des contrôles si certaines données sont incluses dans les résultats de la requête liste.
- La chaîne de développer uniquement est pris en charge que lorsqu’il est utilisé dans la liste des travaux, les planifications, les tâches et les pools. Actuellement, il prend uniquement en charge les informations statistiques.
- Lorsque toutes les propriétés sont requises et aucune chaîne de sélection n’est spécifié, la chaîne de développement *doit* être utilisée pour obtenir des informations statistiques. Si une chaîne select est utilisée pour obtenir un sous-ensemble de propriétés, puis `stats` peut être spécifié dans la chaîne de sélection, et la chaîne complète n’a pas besoin d’être spécifié.
- Cet exemple montre comment développer la chaîne spécifie que les informations statistiques doivent être retournées pour chaque élément de la liste : `stats`.

> [AZURE.NOTE] Lors de la construction de tous les types de chaîne de requête de trois (filtrer, sélectionnez et développez), vous devez vous assurer que les noms de propriété et les cas correspondent à ceux de leurs homologues d’élément API REST. Par exemple, lorsque vous travaillez avec la classe .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , vous devez spécifier l' **état** au lieu de l' **état**, même si la propriété .NET est [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consultez les tableaux ci-dessous pour les mappages de propriété entre les autres API .NET.

### <a name="rules-for-filter-select-and-expand-strings"></a>Règles de filtre, sélectionnez et développez des chaînes

- Les noms de propriétés dans un filtre, sélectionnez et développez des chaînes doit apparaître comme dans le [Reste du lot] [ api_rest] API--même lorsque vous utilisez le [Traitement par lots .NET] [ api_net] ou l’une des autres kits de développement de lot.
- Tous les noms de propriété sont sensibles à la casse, mais les valeurs de propriété sont en majuscules ou en minuscules.
- Date/heure peut être un des deux formats de chaînes et doivent être précédées de `DateTime`.

  - Exemple de format DTF-W3C :`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemple de format RFC 1123 :`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Chaînes booléennes sont `true` ou `false`.
- Si un opérateur ou une propriété non valide est spécifié, un `400 (Bad Request)` erreur se produit.

## <a name="efficient-querying-in-batch-net"></a>Efficace de requête dans .NET de lot

Dans le [Traitement par lots .NET] [ api_net] API, de [ODATADetailLevel] [ odata] classe est utilisée pour fournir des filtres, sélectionnez et développez des chaînes pour les opérations de la liste. La classe ODataDetailLevel a trois propriétés de chaîne publique qui peuvent être spécifiées dans le constructeur, ou définissez directement sur l’objet. Vous passez ensuite l’objet de ODataDetailLevel en tant que paramètre pour les différentes opérations de liste tel que [ListPools][net_list_pools], [ListJobs][net_list_jobs]et [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Limiter le nombre d’éléments qui sont retournés.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Spécifier les valeurs de propriété sont retournées avec chaque élément.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Récupérer les données pour tous les éléments dans une seule API appellent plutôt que les appels distinctes pour chaque élément.

L’extrait de code suivant utilise l’API .NET de lot interroger efficacement le service de traitement par lots pour les statistiques d’un ensemble spécifique de pools. Dans ce scénario, l’utilisateur du lot a pools de test et de production. L’ID du pool de test sont précédés de « test », et le regroupement de production ID sont précédés de « prod ». Dans l’extrait de code, *myBatchClient* est une instance initialisée correctement de la classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) .

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Une instance de [ODATADetailLevel] [ odata] qui est configuré avec Select et des clauses de développement peuvent également être passées approprié, les méthodes Get, par exemple [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), pour limiter la quantité de données retournées.

## <a name="batch-rest-to-net-api-mappings"></a>Lot reste aux mappages d’API .NET

Les noms de propriété dans filtre, sélectionnez et développez des chaînes *doivent* refléter leurs équivalents API REST, à la fois dans le nom et la casse. Les tableaux ci-dessous fournissent mappages entre les équivalents .NET et l’API REST.

### <a name="mappings-for-filter-strings"></a>Mappages de chaînes de filtre

- **Méthodes de liste .NET**: chacune des méthodes de l’API .NET dans cette colonne accepte un [ODATADetailLevel] [ odata] objet comme paramètre.
- **Requêtes de la liste reste**: page de chaque API de reste lié à cette colonne contient un tableau qui spécifie les propriétés et les opérations qui sont autorisées dans les chaînes de *filtre* . Vous allez utiliser ces noms de propriétés et les opérations lorsque vous construisez une [ODATADetailLevel.FilterClause] [ odata_filter] chaîne.

| Méthodes de liste .NET | Requêtes de la liste reste |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Liste les certificats d’un compte][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Liste des fichiers associés à une tâche][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Liste de l’état de la préparation du travail et les tâches de version pour un travail][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Répertorier les travaux dans un compte][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Répertorier les fichiers sur un nœud][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Liste des tâches associées à un travail][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Afficher les planifications du travail d’un compte][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Liste des tâches associées à un calendrier des tâches][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Liste des nœuds de calcul dans un pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Liste les pools d’un compte][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Mappages de chaînes select

- **Les types de lot .NET**: types d’API .NET de traitement par lots.
- **Entités de l’API REST**: chaque page de cette colonne contient une ou plusieurs tables qui répertorient les noms de propriété pour le type d’API REST. Ces noms de propriété sont utilisés lors de la construction de *Sélectionner* des chaînes. Vous allez utiliser ces noms de propriété même lorsque vous construisez une [ODATADetailLevel.SelectClause] [ odata_select] chaîne.

| Types de lots .NET | Entités de l’API REST |
|---|---|
| [Certificat][net_cert] | [Obtenir des informations sur un certificat][rest_get_cert] |
| [CloudJob][net_job] | [Obtenir des informations sur une tâche][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obtenir des informations sur la planification d’une tâche][rest_get_schedule] |
| [ComputeNode][net_node] | [Obtenir des informations sur un nœud][rest_get_node] |
| [CloudPool][net_pool] | [Obtenir des informations sur un pool][rest_get_pool] |
| [CloudTask][net_task] | [Obtenir des informations sur une tâche][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemple : construire une chaîne de filtre

Lorsque vous construisez une chaîne de filtrage pour [ODATADetailLevel.FilterClause][odata_filter], consultez le tableau ci-dessus sous « Des mappages pour les chaînes de filtre » à la page de documentation de l’API REST de recherche qui correspond à l’opération de liste que vous souhaitez effectuer. Vous trouverez les propriétés filtrables et leurs opérateurs pris en charge dans la première table multirow sur cette page. Si vous souhaitez récupérer toutes les tâches dont le code sortie était différent de zéro, par exemple, cette ligne sur la [liste des tâches associées à un travail] [ rest_list_tasks] spécifie la chaîne de la propriété applicable et les opérateurs autorisés :

| Propriété | Opérations autorisées | Type de |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Par conséquent, la chaîne de filtre pour la liste de toutes les tâches avec un code de sortie différent de zéro serait :

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemple : construire une chaîne de sélection

Pour construire les [ODATADetailLevel.SelectClause][odata_select], consultez le tableau ci-dessus sous « Mappages de chaînes select » et accédez à la page de l’API REST qui correspond au type d’entité que vous mettez en vente. Vous trouverez les propriétés pouvant être sélectionnées et leurs opérateurs pris en charge dans la première table multirow sur cette page. Si vous souhaitez récupérer uniquement l’ID et la ligne de commande pour chaque tâche dans une liste, par exemple, vous trouverez ces lignes dans la table applicable sur [obtenir des informations sur une tâche][rest_get_task]:

| Propriété | Type de | Notes |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La chaîne de sélection pour inclure uniquement les ID et ligne de commande avec chaque tâche de la liste serait alors :

`id, commandLine`

## <a name="code-samples"></a>Exemples de code

### <a name="efficient-list-queries-code-sample"></a>Exemple de code de requêtes de liste efficace

Extraire de [EfficientListQueries] [ efficient_query_sample] exemple de projet sur GitHub pour voir l’efficacité interrogation de liste peut affecter les performances dans une application. Cette application de console C# crée et ajoute un grand nombre de tâches à une tâche. Puis, il effectue plusieurs appels vers le [JobOperations.ListTasks] [ net_list_tasks] méthode et passe [ODATADetailLevel] [ odata] les objets qui sont configurés avec différentes valeurs de propriété pour faire varier la quantité de données à renvoyer. Il produit une sortie semblable à la suivante :

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Comme indiqué dans les temps, vous pouvez réduire considérablement les temps de réponse de requête en limitant les propriétés et le nombre d’éléments qui sont retournés. Vous pouvez trouver cette et autres exemples de projets dans les [exemples de traitement par lots azure] [ github_samples] référentiel sur GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Exemples de code et la bibliothèque BatchMetrics

En plus de l’exemple de code EfficientListQueries ci-dessus, vous pouvez trouver la [BatchMetrics] [ batch_metrics] projet dans les [exemples de traitement par lots azure] [ github_samples] référentiel de GitHub. L’exemple de projet BatchMetrics montre comment surveiller efficacement la progression du projet Azure lot à l’aide de l’API de traitement par lots.

La [BatchMetrics] [ batch_metrics] exemple inclut un projet de bibliothèque de classes .NET qui vous pouvez incorporer dans vos propres projets et un programme de ligne de commande simple exercice et illustrent l’utilisation de la bibliothèque.

L’exemple d’application au sein du projet illustre les opérations suivantes :

1. Sélection des attributs spécifiques pour télécharger uniquement les propriétés dont vous avez besoin
2. Filtrage sur les temps de transition d’état pour télécharger uniquement les modifications apportées depuis la dernière requête

Par exemple, la méthode suivante s’affiche dans la bibliothèque BatchMetrics. Elle retourne un ODATADetailLevel qui spécifie que seuls les `id` et `state` propriétés doivent être obtenues pour les entités qui sont interrogées. Il spécifie également que seules les entités dont l’état a changé depuis le `DateTime` paramètre doit être retourné.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="parallel-node-tasks"></a>Nœud parallèle des tâches

[Optimiser le lot Azure compute de l’utilisation des ressources avec les tâches simultanées de nœud](batch-parallel-node-tasks.md) est un autre article lié aux performances des applications de traitement par lots. Certains types de charges de travail peuvent tirer parti de l’exécution de tâches parallèles sur Agrandir--mais moins--les nœuds de calcul. Consultez l' [exemple de scénario](batch-parallel-node-tasks.md#example-scenario) dans l’article pour plus d’informations sur un tel scénario.

### <a name="batch-forum"></a>Forum du lot

Le [Forum du lot Azure] [ forum] sur le site MSDN est l’endroit idéal pour discuter des lots et de poser des questions sur le service. Accédez pour les publications de « rémanentes » utiles et publiez vos questions, qu’ils surviennent alors que vous générez vos solutions de traitement par lots.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
