<properties
    pageTitle="Notions de base de service lot Azure | Microsoft Azure"
    description="En savoir plus sur l’utilisation du service Azure lot parallèle à grande échelle et des charges de travail HPC"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Notions de base du lot Azure

Lot Azure permet de vous afin de s’exécuter à grande échelle parallèle et haute performance computing (HPC) des applications dans le nuage. Il s’agit d’un service de plate-forme qui planifie le travail de calcul intensif pour s’exécuter sur une collection managée de machines virtuelles, et pouvez échelle calculer automatiquement les ressources pour répondre aux besoins de vos projets.

Avec le service de traitement par lots, vous définissez des ressources de calcul Azure pour exécuter vos applications en parallèle et à grande échelle. Vous pouvez exécuter à la demande ou planifiées travaux et que vous n’avez pas besoin manuellement créer, configurer et gérer un cluster HPC, les machines virtuelles, les réseaux virtuels ou un travail complexe et infrastructure de planification de la tâche.

## <a name="use-cases-for-batch"></a>Exemples d’utilisation pour le traitement par lots

Lot est un service géré Azure qui est utilisé pour le *traitement par lots* ou de *calcul de lot*--exécute un grand nombre de tâches similaires afin d’obtenir un résultat souhaité. Calcul du lot est plus couramment utilisé par les organisations qui traitent régulièrement, transforment et analyser de gros volumes de données.

Traitement par lots fonctionne bien avec les charges de travail et les applications intrinsèquement parallèles (également connu sous le nom « excessivement parallèles »). Les charges de travail intrinsèquement parallèles sont facilement fractionner en plusieurs tâches de travailler simultanément sur plusieurs ordinateurs.

![Tâches parallèles][1]<br/>

Sont des exemples de charges de travail généralement traités à l’aide de cette technique :

* Modélisation des risques financiers
* Hydrologie et climat de l’analyse des données
* Analyse, traitement et rendu de l’image
* Le codage de média et de transcodage
* Analyse de sequence GENETIQUE
* Analyse de contrainte de l’ingénierie
* Test de logiciel

Lot peut également effectuer des calculs parallèles avec une étape de réduction à la fin et exécuter des charges de travail HPC plus complexes telles que les applications [d’Interface MPI (Message Passing)](batch-mpi.md) .

Pour une comparaison entre le lot et d’autres options de solution HPC dans Azure, consultez [solutions HPC et de traitement par lots](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Développement avec le traitement par lots

Traitement des charges de travail parallèles avec le traitement par lots est généralement effectuée par programme en utilisant l’une des [API de traitement par lots](#batch-development-apis). Avec les API de traitement par lots, créer et gérer des pools de nœuds de calcul (machines virtuelles) et planifier des tâches et des tâches à exécuter sur ces nœuds. Une application cliente ou le service que vous créez utilise les API de traitement par lots pour communiquer avec le service de traitement par lots.

Vous pouvez efficacement traiter des charges de travail à grande échelle pour votre organisation, ou fournissez un front-end de service à vos clients afin qu’ils puissent exécuter les travaux et les tâches, à la demande ou selon un calendrier--sur un, des centaines voire des milliers de nœuds. Vous pouvez également utiliser le traitement par lots dans le cadre d’un flux de travail plus grande, géré par des outils tels que [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Lorsque vous êtes prêt à Explorer en profondeur à l’API de traitement par lots pour une compréhension plus approfondie des fonctionnalités qu’il fournit, consultez la [Présentation des fonctionnalités de traitement par lots pour les développeurs](batch-api-basics.md).

### <a name="azure-accounts-youll-need"></a>Vous aurez besoin de comptes Azure

Lorsque vous développez des solutions de traitement par lots, vous allez utiliser les comptes suivants dans Microsoft Azure.

- **Abonnement et compte azure** - si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [abonnés MSDN bénéficient][msdn_benefits], ou inscrivez-vous à un [compte Azure gratuit][free_account]. Lorsque vous créez un compte, un abonnement par défaut est créé pour vous.

- **Compte de lot** - lorsque vos applications interagissent avec le service de traitement par lots, le nom du compte, l’URL du compte, ainsi qu’une touche d’accès rapide sont utilisés comme informations d’identification. Toutes vos ressources de traitement par lots, tels que les pools, les nœuds, les travaux, de calcul et tâches associés à un compte de traitement par lots. Vous pouvez [créer le compte de traitement par lots](batch-account-create-portal.md) dans le portail Azure.

- **Compte de stockage** - lot prend en charge l’utilisation de fichiers dans le [Stockage Azure][azure_storage]. Presque tous les scénarios de traitement par lots utilise le stockage Azure--de mise en place de programmes qui s’exécutent les tâches et les données qu’ils traitent et pour le stockage des données de sortie qu’ils génèrent. Pour créer un compte de stockage, voir [comptes de stockage sur Azure](./../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>Développement API de traitement par lots

Vos applications et services peuvent émettre des appels API REST directs, utilisez une ou plusieurs des bibliothèques client suivantes ou une combinaison des deux à gérer calculer les ressources et l’exécution des charges de travail parallèles à grande échelle en utilisant le service de traitement par lots.

| API    | Référence de l’API | Télécharger | Exemples de code |
| ----------------- | ------------- | -------- | ------------ |
| **Lot reste** | [MSDN][batch_rest] | N/A | [MSDN][batch_rest] |
| **Lot .NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Python de lot**  | [readthedocs.IO][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Node.js de lot** | [github.IO][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Lot Java** (aperçu) | [github.IO][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Gestion des ressources de traitement par lots

En plus de l’API clientes, vous pouvez également utiliser les éléments suivants pour gérer les ressources de votre compte de traitement par lots.

- [Traitement par lots des applets de commande PowerShell][batch_ps]: applets de commande du lot d’Azure dans le module [PowerShell de Azure](../powershell-install-configure.md) permettent de gérer les ressources de traitement par lots avec PowerShell.

- [Azure CLI](../xplat-cli-install.md): l’Interface de ligne de commande Azure (CLI Azure) est un ensemble d’outils multiplates-formes qui fournit des commandes shell permettant d’interagir avec de nombreux services Azure, y compris le traitement par lots.

- La bibliothèque cliente [.NET de gestion de traitement par lots](batch-management-dotnet.md) : également disponible via [NuGet][api_net_mgmt_nuget], vous pouvez utiliser la bibliothèque cliente .NET de gestion de traitement par lots pour gérer par programme les comptes, les quotas et les packages d’applications de lot. Référence de la bibliothèque de gestion est sur [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Outils de traitement par lots

Bien que non obligatoire pour créer des solutions à l’aide du traitement par lots, ici sont de précieux outils à utiliser lors de la génération et le débogage de vos applications de traitement par lots et les services.

 - [Azure portal][portal]: vous pouvez créer, analyser et supprimer les pools par lots, les tâches et les tâches dans les lames du lot du portail Azure. Vous pouvez afficher les informations d’état pour ces et autres ressources pendant que vous exécutez vos travaux et même téléchargement des fichiers à partir des nœuds de calcul dans vos pools (télécharger l’échec d’une tâche `stderr.txt` lors de la résolution des problèmes, par exemple). Vous pouvez également télécharger les fichiers RDP (Remote Desktop) que vous pouvez utiliser pour vous connecter aux nœuds de calcul.

 - [Explorateur de lot Azure][batch_explorer]: Explorateur de lot apparente lot ressource gestion comme portail Azure, mais dans une application cliente Windows Presentation Foundation (WPF) d’autonome. Une des applications exemples .NET de lot disponibles sur [GitHub][github_samples], vous pouvez créer avec Visual Studio 2015 ou supérieure et l’utiliser pour parcourir et gérer les ressources de votre compte de lot lors du développement et de déboguer vos solutions de traitement par lots. Afficher la tâche, pool et détails des tâches, téléchargement des fichiers à partir des nœuds de calcul et se connecteront à distance à des nœuds à l’aide de fichiers RDP (Remote Desktop) que vous pouvez télécharger avec l’Explorateur de lot.

 - [Explorateur de stockage Azure Microsoft][storage_explorer]: pendant que pas strictement un outil Azure lot, l’Explorateur de stockage est un autre outil précieux pour que pendant le développement et le débogage de vos solutions de traitement par lots.

## <a name="scenario-scale-out-a-parallel-workload"></a>Scénario : Évolution de la charge de travail parallèle

Une solution courante qui utilise les API de traitement par lots pour interagir avec le service de lot implique la montée en puissance parallèle intrinsèquement travail telles que le rendu des images dans les scènes 3D--sur un ensemble de nœuds de calcul. Cette liste de nœuds de calcul peut être votre batterie « rendu » qui fournit des dizaines ou des centaines voire des milliers de cœurs de votre opération de rendu, par exemple.

Le diagramme suivant illustre un flux de travail de lot courant, avec une application cliente ou d’un service hébergé à l’aide du traitement par lots à exécuter une charge de travail parallèle.

![Workflow de la solution par lots][2]

Dans ce scénario courant, votre application ou service traite une charge de travail de calcul dans un lot d’Azure, effectuez les opérations suivantes :

1. Télécharger les **fichiers d’entrée** et l' **application** qui traitera ces fichiers à votre compte de stockage Azure. Les fichiers d’entrée peuvent être des données que votre application doit traiter, telles que les données de modélisation financière, ou des fichiers vidéo à être transcodé. Les fichiers d’application peuvent être n’importe quelle application qui est utilisée pour traiter les données, comme une application de rendu 3D ou d’un média TRANSCODEUR.

2. Créer un **pool** de traitement par lots des nœuds de calcul dans votre compte de lot--ces nœuds sont les ordinateurs virtuels qui s’exécuteront à vos tâches. Vous spécifiez les propriétés telles que la [taille du nœud](./../cloud-services/cloud-services-sizes-specs.md), leur système d’exploitation et l’emplacement dans le stockage Azure de l’application à installer lorsque les nœuds de rejoindre le pool (l’application que vous avez téléchargé à l’étape #1). Vous pouvez également configurer le pool à l' [échelle automatiquement](batch-automatic-scaling.md)--dynamiquement ajuster le nombre de nœuds de calcul dans le pool--en réponse à la charge de travail qui génèrent de vos tâches.

3. Créer une **tâche** pour exécuter la charge de travail sur le pool de nœuds de calcul. Lorsque vous créez une tâche, vous l’associez à un pool de traitement par lots.

4. Ajouter des **tâches** à la tâche. Lorsque vous ajoutez des tâches à une tâche, le service de lot planifie automatiquement les tâches pour une exécution sur les nœuds de calcul dans le pool. Chaque tâche utilise l’application que vous avez téléchargé pour traiter les fichiers d’entrée.

    - 4 a. Avant l’exécution d’une tâche, il peut télécharger les données (les fichiers d’entrée) à traiter pour le nœud de calcul à qu'il est assigné. Si l’application n’a pas déjà été installée sur le nœud (voir l’étape #2), il peut être téléchargé ici à la place. Lorsque les téléchargements sont terminés, les tâches s’exécutent sur leurs nœuds attribués.

5. Comme les tâches exécutées, vous pouvez interroger le lot pour surveiller la progression de la tâche et ses tâches. Votre service ou votre application cliente communique avec le service de traitement par lots sur HTTPS et étant donné que vous pouvez surveiller des milliers de tâches qui s’exécutent sur des milliers de nœuds de calcul, veillez à [efficace d’interroger le service de traitement par lots](batch-efficient-list-queries.md).

6. Que les tâches complètes, qu’ils peuvent télécharger leurs données de résultat pour stockage Azure. Vous pouvez également récupérer les fichiers directement à partir des nœuds de calcul.

7. Votre analyse détecte que les tâches de votre projet est terminé, votre application cliente ou un service peut télécharger les données de sortie pour un traitement ultérieur ou d’évaluation.

Gardez à l’esprit, c’est juste une façon d’utiliser le traitement par lots, et ce scénario décrit quelques-uns de ses fonctionnalités disponibles. Par exemple, vous pouvez exécuter [plusieurs tâches en parallèle](batch-parallel-node-tasks.md) sur chaque nœud de calcul, et vous pouvez utiliser des [tâches de préparation et de réalisation](batch-job-prep-release.md) pour préparer les nœuds pour vos travaux, puis les nettoyer ensuite.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble du service de traitement par lots, il est temps d’aller plus loin pour savoir comment vous pouvez l’utiliser pour traiter vos charges de travail intensives parallèles.

- Lisez la [Présentation des fonctionnalités de traitement par lots pour les développeurs](batch-api-basics.md), les informations essentielles pour toute préparation à l’utilisation du traitement par lots. Cet article contient des informations plus détaillées sur les ressources de service de lot tels que les pools, les nœuds, les travaux et les tâches et les nombreuses fonctions d’API que vous pouvez utiliser lors de la création de votre application de traitement par lots.

- [Mise en route avec la bibliothèque de commandes d’Azure pour .NET](batch-dotnet-get-started.md) pour apprendre à utiliser C# et la bibliothèque .NET de traitement par lots à exécuter une charge de travail simple à l’aide d’un flux de travail de lot commun. Cet article doit être votre première cesse tout en apprenant comment utiliser le service de traitement par lots. Il existe également une [version de Python](batch-python-tutorial.md) du didacticiel.

- Téléchargez les [exemples de code sur GitHub] [ github_samples] pour voir comment C# et Python peut s’interfacer avec lot aux charges de travail exemple planification et processus.

- Extraire le [Cursus des commandes] [ learning_path] pour obtenir une idée des ressources disponibles pour vous, que vous apprenez à travailler avec le traitement par lots.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
