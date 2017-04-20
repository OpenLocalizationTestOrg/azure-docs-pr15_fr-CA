<properties
    pageTitle="Optimiser l’utilisation de nœuds de traitement par lots avec des tâches parallèles | Microsoft Azure"
    description="Augmenter l’efficacité et réduire les coûts en utilisant moins de nœuds de calcul et de tâches simultanées en cours d’exécution sur chaque nœud dans un pool d’Azure lot"
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

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Optimiser l’utilisation des ressources par lots d’Azure compute avec les tâches simultanées de nœud

En exécutant plusieurs tâches simultanément sur chaque nœud de calcul dans le pool de traitement par lots d’Azure, vous pouvez optimiser l’utilisation des ressources sur un plus petit nombre de nœuds dans le pool. Pour certaines charges de travail, cela peut entraîner des temps de travail plus courts et des coûts réduits.

Alors que certains scénarios bénéficient de consacrer toutes les ressources d’un nœud à une même tâche, plusieurs situations bénéficient permettant à plusieurs tâches de partager ces ressources :

 - **En limitant le transfert des données** lorsque les tâches sont en mesure de partager des données. Dans ce scénario, vous pouvez considérablement réduire les frais de transfert de données en copiant des données partagées à un plus petit nombre de nœuds et l’exécution de tâches en parallèle sur chaque nœud. Cela s’applique en particulier si les données à copier sur chaque nœud doivent être transférées entre les régions géographiques.

 - **Utilisation de la mémoire – optimiser** lors de tâches nécessitent une grande quantité de mémoire, mais seulement pendant de courtes périodes de temps et le temps variable pendant l’exécution. Vous pouvez utiliser les nœuds de calcul moins nombreux, mais plus grande, avec plus de mémoire pour gérer efficacement ces pics. Ces nœuds aurait plusieurs tâches en cours d’exécution en parallèle sur chaque nœud, mais chaque tâche bénéficie de mémoire d’abondante des nœuds à des moments différents.

 - **Atténuation des limites nœud** lors de la communication inter-nœud est nécessaire au sein d’un pool. Actuellement, les pools configurés pour la communication inter-nœud sont limités à 50 nœuds de calcul. Si chaque nœud dans un pool de ce type est capable d’exécuter des tâches en parallèle, un plus grand nombre de tâches peut être exécuté simultanément.

 - **Sur les sites de réplication compute cluster**, par exemple lorsque vous déplacez un environnement de calcul Azure. Si votre solution sur site exécute plusieurs tâches par nœud de calcul, vous pouvez augmenter le nombre maximal de tâches de nœud pour mieux refléter cette configuration.

## <a name="example-scenario"></a>Exemple de scénario

Comme exemple pour illustrer les avantages de l’exécution de tâches parallèles, supposons que votre application de tâche a des exigences en matière de processeur et de mémoire telles que [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) nœuds sont suffisantes. Mais, pour terminer la tâche dans le temps, les 1 000 de ces nœuds sont nécessaires.

Au lieu d’utiliser la norme\_D1 de nœuds qui ont 1 noyau de processeur, vous pouvez utiliser [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) les nœuds qui possèdent 16 noyaux et permettent l’exécution de tâches parallèles. Par conséquent, *16 fois moins de nœuds* peut être utilisé, au lieu de 1 000 postes, 63 seulement serait nécessaire. En outre, si les fichiers de l’application de grande taille ou les données de référence sont nécessaires pour chaque nœud, l’efficacité et la durée des tâches sont à nouveau améliorés dans la mesure où les données sont copiées à uniquement 16 nœuds.

## <a name="enable-parallel-task-execution"></a>Activer l’exécution des tâches parallèles

Vous configurez les nœuds de calcul pour l’exécution des tâches parallèles au niveau du pool. La bibliothèque .NET de lot, de définir la [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriété lorsque vous créez un pool. Si vous utilisez l’API REST de traitement par lots, définissez les [maxTasksPerNode] [ rest_addpool] élément dans le corps de la demande lors de la création du pool.

Azure lot vous permet de définir des tâches maximum par nœud jusqu'à quatre fois (4 x) le nombre de cœurs de nœud. Par exemple, si le pool est configuré avec les nœuds de la taille « Large » (quatre cœurs), `maxTasksPerNode` peut être défini sur 16. Pour plus d’informations sur le nombre de cœurs pour chacun des formats de nœud, consultez [tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md). Pour plus d’informations sur les limites de service, consultez [des Quotas et des limites pour le service de traitement par lots d’Azure](batch-quota-limit.md).

> [AZURE.TIP] Veillez à prendre en compte la `maxTasksPerNode` valeur lorsque vous construisez une [formule d’échelle] [ enable_autoscaling] pour votre pool. Par exemple, une formule dont le résultat est `$RunningTasks` pourrait être considérablement affecté par une augmentation des tâches par nœud. Pour plus d’informations, reportez-vous à la section [échelle de calculer automatiquement des nœuds dans un pool d’Azure lot](batch-automatic-scaling.md) .

## <a name="distribution-of-tasks"></a>Répartition des tâches

Lorsque les nœuds de calcul dans un pool peuvent exécuter des tâches simultanément, il est important de spécifier la manière dont les tâches doit être réparti entre les nœuds dans le pool.

À l’aide de la [CloudPool.TaskSchedulingPolicy] [ task_schedule] propriété, vous pouvez spécifier que les tâches doivent être assignées uniformément sur tous les nœuds dans le pool (« propagation »). Ou vous pouvez spécifier qu’autant de tâches que possible doit être assigné à chaque nœud, avant que les tâches sont affectées à un autre nœud dans le pool (« emballage »).

Examinons, par exemple de comment cette fonctionnalité est très utile, le pool de [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nœuds (dans l’exemple ci-dessus) qui est configuré avec un [CloudPool.MaxTasksPerComputeNode] de[ maxtasks_net] valeur de 16. Si la [CloudPool.TaskSchedulingPolicy] [ task_schedule] est configuré avec un [ComputeNodeFillType] de[ fill_type] du *Pack*, il serait optimiser l’utilisation de toutes les 16 noyaux de chaque nœud et permettre d’un [pool d’autoscaling](batch-automatic-scaling.md) élaguer les nœuds inutilisés du pool (nœuds sans les tâches affectées). Cela permet de réduire l’utilisation des ressources et économise de l’argent.

## <a name="batch-net-example"></a>Exemple de lot .NET

Ce [Traitement par lots .NET] [ api_net] extrait de code API affiche une demande de création d’un pool contenant quatre nœuds de grande taille avec un maximum de quatre tâches par nœud. Il spécifie une stratégie qui insère chaque nœud avant d’affecter des tâches vers un autre nœud dans la liste des tâches de planification des tâches. Pour plus d’informations sur l’ajout de pools à l’aide de l’API .NET de traitement par lots, voir [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Exemple de reste de lot

Ce [Lot reste] [ api_rest] extrait d’API affiche une demande de création d’un pool qui contient deux nœuds de grande taille avec un maximum de quatre tâches par nœud. Pour plus d’informations sur l’ajout de pools à l’aide de l’API REST, consultez [Ajouter un pool à un compte][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] Vous pouvez définir le `maxTasksPerNode` élément et [MaxTasksPerComputeNode] [ maxtasks_net] propriété qu’au moment de la création de pool. Ils ne peuvent pas être modifiés après qu’un pool a déjà été créé.

## <a name="code-sample"></a>Exemple de code

La [ParallelNodeTasks] [ parallel_tasks_sample] projet sur GitHub illustre l’utilisation de la [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriété.

Cette application de console C# utilise le [Traitement par lots .NET] [ api_net] bibliothèque pour créer un pool avec un ou plusieurs nœuds de calcul. Il exécute un nombre configurable des tâches sur ces nœuds pour simuler la charge variable. Sortie de l’application spécifie les nœuds exécuté chaque tâche. L’application fournit également un résumé des paramètres de la tâche et durée. La partie Résumé de la sortie de deux différentes exécutions de l’application d’exemple s’affiche ci-dessous.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La première exécution de l’exemple d’application montre que, avec un nœud unique dans le pool et le paramètre par défaut d’une tâche par nœud, la durée de travail est de plus de 30 minutes.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La deuxième exécution de l’exemple montre une baisse significative de la durée de la tâche. C’est parce que le pool a été configuré avec quatre tâches par nœud, ce qui permet l’exécution des tâches parallèles terminer le travail de près d’un quart du temps.

> [AZURE.NOTE] Les durées de travail dans les résumés ci-dessus n’incluent pas l’heure de création de pool. Chacune des tâches ci-dessus a été soumis à des pools créés précédemment dont les nœuds de calcul étaient à l’état *inactif* au moment de la soumission.

## <a name="next-steps"></a>Étapes suivantes

### <a name="batch-explorer-heat-map"></a>Carte de traitement par lots Explorer thermique

L' [Explorateur de lot Azure][batch_explorer], un des [exemples d’applications]Azure lot[github_samples], contient une fonctionnalité de *Carte thermique* qui permet la visualisation de l’exécution de la tâche. Lorsque vous avez exécuté le la [ParallelTasks] [ parallel_tasks_sample] exemple d’application, vous pouvez utiliser la fonctionnalité de carte thermique pour visualiser facilement l’exécution de tâches en parallèle sur chaque nœud.

![Carte de traitement par lots Explorer thermique][1]

*Carte thermique de lot Explorer affichant un pool de quatre nœuds, chaque nœud quatre tâches en cours d’exécution*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
