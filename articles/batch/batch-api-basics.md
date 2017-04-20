<properties
    pageTitle="Vue d’ensemble de la fonction Batch Azure pour les développeurs | Microsoft Azure"
    description="Découvrez les fonctionnalités du service lot et ses API à partir d’un point de vue du développement."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Présentation des fonctionnalités de traitement par lots pour les développeurs

Dans cette vue d’ensemble des composants du service de traitement par lots d’Azure, nous abordons les fonctionnalités du service principal et les ressources que les développeurs de traitement par lots peuvent utiliser pour générer les parallèle à grande échelle des solutions de calcul.

Si vous développez une application informatique distribuée ou service qui émet direct [API REST] [ batch_rest_api] appels ou si vous utilisez le [SDK de traitement par lots](batch-technical-overview.md#batch-development-apis), vous utiliserez de nombreuses ressources et fonctionnalités abordées dans cet article.

> [AZURE.TIP] Pour une présentation de niveau supérieur pour le service de traitement par lots, consultez [Notions de base du lot d’Azure](batch-technical-overview.md).

## <a name="batch-service-workflow"></a>Workflow des commandes de service

Le flux de travail de haut niveau suivant est typique de presque toutes les applications et les services qui utilisent le service de traitement par lots pour le traitement des charges de travail parallèles :

1. Télécharger les **fichiers de données** que vous souhaitez traiter un [Stockage Azure] [ azure_storage] compte. Traitement par lots inclut la prise en charge intégrée pour accéder au stockage des objets Blob Azure et vos tâches peuvent télécharger ces fichiers pour [les nœuds de calcul](#compute-node) lorsque les tâches sont exécutées.

2. Télécharger les **fichiers de l’application** qui exécuteront les tâches. Ces fichiers peuvent être des fichiers binaires ou de scripts et de leurs dépendances et sont exécutées par les tâches de vos travaux. Les tâches peuvent télécharger ces fichiers à partir de votre compte de stockage, ou vous pouvez utiliser la fonctionnalité [packages d’applications](#application-packages) de traitement par lots pour le déploiement et la gestion des applications.

3. Créer un [pool](#pool) de nœuds de calcul. Lorsque vous créez un pool, vous spécifiez le nombre de nœuds de calcul pour le pool, leur taille et le système d’exploitation. Lors de l’exécution de chaque tâche dans votre travail, il a attribué à l’exécution sur un des nœuds dans le pool.

4. Créer un [projet](#job). Une tâche gère une collection de tâches. Vous associez chaque tâche à un pool spécifique où seront exécute les tâches de ce projet.

5. Ajouter des [tâches](#task) à la tâche. Chaque tâche s’exécute l’application ou le script que vous avez téléchargé pour traiter les fichiers de données téléchargés à partir de votre compte de stockage. Que chaque tâche est terminée, il peut télécharger sa sortie vers le stockage Azure.

6. Surveiller la progression de la tâche et récupérer la sortie de la tâche du stockage Azure.

Les sections suivantes décrivent ces et les autres ressources de traitement par lots qui permettent à votre scénario de calcul distribué.

> [AZURE.NOTE] Vous avez besoin d’un [compte de traitement par lots](batch-account-create-portal.md) à utiliser le service de traitement par lots. En outre, presque toutes les solutions utilisent un [Stockage Azure] [ azure_storage] pour le stockage de fichiers et la récupération de compte. Traitement par lots actuellement prend en charge uniquement le stockage compte type à **usage général,** comme décrit à l’étape 5 de [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Ressources du service de traitement par lots

Certaines des ressources suivantes : comptes, calculent les nœuds, les pools, les travaux et les tâches--requis par toutes les solutions qui utilisent le service de traitement par lots. D’autres, telles que les planifications de travail et des packages d’application, sont des fonctionnalités utiles, mais facultatives.

- [Compte](#account)
- [Nœud de calcul](#compute-node)
- [Pool](#pool)
- [Travail](#job)

  - [Planifications de travaux](#scheduled-jobs)

- [Tâche](#task)

  - [Démarrer la tâche](#start-task)
  - [Le gestionnaire tâches](#job-manager-task)
  - [Tâches de préparation et de la version](#job-preparation-and-release-tasks)
  - [Tâche d’instances multiples (MPI)](#multi-instance-tasks)
  - [Interdépendances des tâches](#task-dependencies)

- [Packages d’applications](#application-packages)

## <a name="account"></a>Compte

Un compte de traitement par lots est une entité identifiée de façon unique au sein du service de traitement par lots. Tous les traitements sont associé à un compte de traitement par lots. Lorsque vous effectuez des opérations avec le service de traitement par lots, vous devez à la fois le nom du compte et une des clés de son compte. Vous pouvez [créer un compte Azure lot via le portail Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Nœud de calcul

Un nœud de calcul est une Azure machine virtuelle (VM) qui est dédiée au traitement d’une partie de la charge de travail de votre application. La taille d’un nœud détermine le nombre de cœurs du processeur, de capacité de mémoire et de taille de système de fichiers local qui est affectée au nœud. Vous pouvez créer des pools de nœuds Windows ou Linux à l’aide des Services en nuage Azure Marketplace de Machines virtuelles images ou. Consultez la section suivante de la [liste](#pool) pour plus d’informations sur ces options.

Nœuds peuvent exécuter un fichier exécutable ou un script qui est pris en charge par l’environnement de système d’exploitation du nœud. Cela inclut les \*.exe, \*.cmd, \*.bat et scripts PowerShell pour Windows--et binaires, shell et les Python scripts pour Linux.

Tous les nœuds de calcul dans le traitement par lots également incluent :

- Standard de [structure de dossiers](#files-and-directories) et associées [les variables d’environnement](#environment-settings-for-tasks) qui sont disponibles pour référence par les tâches.
- Paramètres de **pare-feu** qui sont configurés pour contrôler l’accès.
- [Accès à distance](#connecting-to-compute-nodes) de Windows (Remote Desktop Protocol (RDP)) et les nœuds Linux (SSH (Secure Shell)).

## <a name="pool"></a>Pool

Un pool est un ensemble de nœuds que votre application s’exécute sur. Le pool peut être créé manuellement par vous-même, ou automatiquement par le service de traitement par lots lorsque vous spécifiez le travail à effectuer. Vous pouvez créer et gérer un pool répondant aux exigences de ressources de votre application. Un pool peut être utilisé uniquement par le compte de traitement par lots dans lequel il a été créé. Un compte de traitement par lots peut avoir plus d’un pool.

Azure pools de traitement par lots Générer sur la plate-forme centrale de Azure compute. Ils fournissent l’allocation à grande échelle, installation de l’application, la distribution des données, analyse du fonctionnement et des réglages du nombre de nœuds de calcul au sein d’un pool ([mise à l’échelle](#scaling-compute-resources)).

Chaque nœud est ajouté à un pool est attribué une adresse IP et un nom unique. Lorsqu’un nœud est supprimé d’un pool, toutes les modifications effectuées sur le système d’exploitation ou les fichiers sont perdues, et son nom et son adresse IP sont libérées pour une utilisation ultérieure. Lorsqu’un nœud quitte un pool, sa durée de vie est.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- Calcul de nœud de **système d’exploitation** et **version**

    Vous avez deux options lorsque vous sélectionnez un système d’exploitation pour les nœuds dans le pool : **Configuration de Machine virtuelle** et de la **Configuration des Services Cloud**.

    **Configuration de Machine virtuelle** fournit des images de Linux et Windows pour les nœuds à partir de [Machines virtuelles de Azure Marketplace][vm_marketplace].
    Lorsque vous créez un pool qui contient les nœuds de Configuration de Machine virtuelle, vous devez spécifier non seulement la taille de nœuds, mais également la **référence d’image de machine virtuelle** et de **l’agent de nœud SKU** du lot doit être installé sur les nœuds. Pour plus d’informations sur la définition de ces propriétés de pool, voir [fourniture Linux nœuds de calcul dans Azure lot pools](batch-linux-nodes.md).

    **Configuration de Services de cloud** fournit que Windows compute nœuds *uniquement*. Les systèmes d’exploitation disponibles pour les pools de Configuration des Services de Cloud sont répertoriés dans les [versions de système d’exploitation invité de Azure et matrice de compatibilité SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool contenant des nœuds de Services dans le nuage, vous devez spécifier uniquement la taille du nœud et sa *Famille de système d’exploitation*. Lorsque vous créez des nœuds de calcul de pools de Windows, vous utilisez généralement les Services en nuage.

    - La *Famille de système d’exploitation* détermine également les versions de .NET sont installées avec le système d’exploitation.
    - Comme avec les rôles de travail au sein de Services en nuage, vous pouvez spécifier une *Version du système d’exploitation* (pour plus d’informations sur les rôles de travail, reportez-vous à la section [en savoir sur les services en nuage](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) dans la [vue d’ensemble des Services en nuage](../cloud-services/cloud-services-choose-me.md)).
    - Comme avec les rôles de travail, nous vous recommandons de spécifier `*` pour la *Version du système d’exploitation* afin que les nœuds sont mis à niveau automatiquement, et il n’y a pas de travail nécessaires pour répondre à nouvelles versions. Cas d’usage principal pour la sélection d’une version de système d’exploitation spécifique est afin de garantir la compatibilité des applications, ce qui permet des tests à réaliser avant d’autoriser la version mise à jour de compatibilité descendante. Après validation, la *Version du système d’exploitation* pour le pool peut être mis à jour et de la nouvelle image du système d’exploitation peut être installé, toutes les tâches en cours d’exécution sont interrompus et remise en attente.

- **Taille des nœuds**

    Tailles de nœud de **Configuration de Services de cloud** compute sont répertoriés dans les [tailles pour les Services en nuage](../cloud-services/cloud-services-sizes-specs.md). Traitement par lots prend en charge toutes les tailles de Services en nuage à l’exception de `ExtraSmall`.

    **Configuration de Machine virtuelle** les tailles sont répertoriées dans les [tailles pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) et les [tailles pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows) de nœud de calcul. Traitement par lots prend en charge toutes les tailles d’Azure VM à l’exception de `STANDARD_A0` et celles avec stockage de prime (`STANDARD_GS`, `STANDARD_DS`, et `STANDARD_DSV2` série).

    Lorsque vous sélectionnez une taille de nœud de calcul, pensez aux caractéristiques et les spécifications des applications que vous allez exécuter sur les nœuds. Aspects tels que si l’application est multithread et combien il consomme de la mémoire peut aider à déterminent la taille de nœud plus approprié et plus économique. Il est courant de sélectionner une taille nœud en supposant une tâche s’exécute sur un nœud à la fois. Toutefois, il est possible d’avoir plusieurs tâches (et par conséquent, plusieurs instances d’applications) de [s’exécuter en parallèle](batch-parallel-node-tasks.md) sur les nœuds de calcul lors de l’exécution du travail. Dans ce cas, il est commun de choisir une plus grande taille de nœud pour s’adapter à l’augmentation de la demande d’exécution des tâches parallèles. Pour plus d’informations, consultez [stratégie de planification de tâches](#task-scheduling-policy) .

    Tous les nœuds dans un pool ont la même taille. Si avez l’intention d’exécuter des applications présentant des exigences différentes de système ou de charger les niveaux, nous vous conseillons d’utiliser des pools séparés.

- **Nombre de postes cibles**

    Ceci est le nombre de nœuds de calcul que vous souhaitez déployer dans le pool. Cela est appelé une *cible* , car, dans certaines situations, votre pool ne peut pas atteindre le nombre de nœuds souhaité. Un pool ne peut pas atteindre le nombre de nœuds souhaité si elle atteint le [quota de base](batch-quota-limit.md#batch-account-quotas) pour votre compte de lot--ou s’il existe une formule de mise à l’échelle que vous avez appliqués au pool qui limite le nombre maximal de nœuds (voir la section suivante « Stratégie de mise à l’échelle »).

- **Stratégie de mise à l’échelle**

    En plus de spécifier un nombre statique de nœuds, vous pouvez à la place écrire et appliquer une [formule de mise à l’échelle](#scaling-compute-resources) à un pool. Le service de traitement par lots calcule votre formule périodiquement et ajuste le nombre de nœuds dans le pool en fonction de pool, de travaux et des paramètres de tâche que vous pouvez spécifier différents.

- **Stratégie de planification des tâches**

    L’option de configuration [max tâches par nœud](batch-parallel-node-tasks.md) détermine le nombre maximal de tâches pouvant être exécutées en parallèle sur chaque nœud de calcul au sein du pool.

    La configuration par défaut est qu’une seule tâche à la fois s’exécute sur un nœud, mais il existe des scénarios dans lesquels il est intéressant de disposer de plusieurs tâches exécutées simultanément sur un nœud. Consultez l' [exemple de scénario](batch-parallel-node-tasks.md#example-scenario) dans l’article [les tâches simultanées de nœud](batch-parallel-node-tasks.md) pour voir comment vous pouvez bénéficier de plusieurs tâches par nœud.

    Vous pouvez également spécifier un *type de remplissage* , qui détermine si le lot propage les tâches uniformément sur tous les nœuds dans un pool ou packs de chaque nœud avec le nombre maximal de tâches avant d’affecter des tâches vers un autre nœud.

- **Statut de la communication** des nœuds de calcul

    Dans la plupart des scénarios, les tâches fonctionnent indépendamment et n’avez pas besoin de communiquer entre eux. Il existe toutefois certaines applications dans lequel les tâches doivent communiquer, tels que [les scénarios MPI](batch-mpi.md).

    Vous pouvez configurer un pool pour permettre la communication entre les nœuds dans l’informatique, la**communication entre les nœuds**. Lorsque la communication entre les nœuds est activée, nœuds dans les pools de Configuration des Services Cloud peuvent communiquer entre eux sur les ports supérieurs à 1100 et pools de Configuration d’ordinateur virtuel ne pas restreint le trafic de n’importe quel port.

    Remarque : l’activation de communication entre les nœuds également a une incidence sur le placement des nœuds de clusters et peut limiter le nombre maximal de nœuds dans un pool en raison des restrictions de déploiement. Si votre application ne nécessite pas la communication entre les nœuds, le service de traitement par lots peut allouer un nombre potentiellement important de nœuds dans le pool à partir du nombre de clusters différent et centres de données pour activer augmente la puissance de traitement parallèle.

- **Démarrer la tâche** pour les nœuds de calcul

    L' option *Démarrer la tâche* s’exécute sur chaque nœud de ce nœud rejoint le pool, et chaque fois qu’un nœud est redémarré ou de reconfiguration. Cette tâche est particulièrement utile pour la préparation des nœuds de calcul pour l’exécution des tâches, telles que l’installation des applications qui les tâches s’exécutent sur les nœuds de calcul.

- **Packages d’applications**

    Vous pouvez spécifier les [packages d’applications](#application-packages) à déployer sur les nœuds de calcul dans le pool. Packages d’applications fournissent de simplifier le déploiement et la gestion des versions des applications qui s’exécutent de vos tâches. Packages que vous spécifiez pour un pool d’applications sont installés sur tous les nœuds qui se joint à ce pool, et chaque fois qu’un nœud est redémarré ou de reconfiguration. Packages d’applications sont actuellement pas pris en charge sur les nœuds de calcul Linux.

- **Configuration du réseau**

    Vous pouvez spécifier l’ID d’un [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) d' Azure dans lequel les nœuds de calcul du pool doit être créé. Configuration requise pour la spécification d’un VNet pour votre pool, vous pouvez trouver dans [Ajouter un pool à un compte] [ vnet] dans la référence de l’API REST de traitement par lots.

> [AZURE.IMPORTANT] Tous les comptes de lot ont un défaut **quota** qui limite le nombre de **cœurs** (et par conséquent, les nœuds de calcul) dans un compte de traitement par lots. Vous pouvez trouver les quotas par défaut et les instructions sur la façon d' [augmenter un quota](batch-quota-limit.md#increase-a-quota) (par exemple, le nombre maximal de cœurs dans votre compte de lot) dans des [Quotas et des limites pour le service de traitement par lots d’Azure](batch-quota-limit.md). Si vous vous trouvez demandant « Pourquoi ne mon pool d’atteindre plus de X de nœuds ? » Ce quota de base peut être la cause.

## <a name="job"></a>Travail

Une tâche est un ensemble de tâches. Il gère le calcul est effectué par ses tâches sur les nœuds de calcul dans un pool.

- Le projet spécifie le **pool** dans lequel le travail doit être exécuté. Vous pouvez créer un nouveau pool pour chaque tâche, ou utiliser un pool pour un grand nombre de travaux. Vous pouvez créer un pool pour chaque travail qui est associé à une planification de projet, ou pour toutes les tâches associées à la planification d’une tâche.

- Vous pouvez spécifier une option **priorité de la tâche**. Lorsqu’un travail est soumis avec une priorité plus élevée que les tâches qui sont actuellement en cours, les tâches pour le travail de priorité plus élevée sont insérés dans la file d’attente avant les tâches pour les tâches de priorité inférieure. Tâches dans les tâches de priorité inférieure qui sont déjà en cours d’exécution ne sont pas interrompus.

- **Contraintes** de la tâche vous permet de spécifier certaines limites pour vos travaux :

    Vous pouvez définir un **temps de wallclock maximale**, afin que si une tâche s’exécute plus longtemps que la durée maximale de wallclock est spécifiée, la tâche et toutes les tâches sont terminées.

    Traitement par lots peut détecter et recommencez les tâches qui ont échoué. Vous pouvez spécifier le **nombre maximal de tentatives de la tâche** en tant que contrainte, notamment si une tâche est *toujours* ou *jamais* une nouvelle tentée. Reprise d’une tâche signifie que la tâche est remise en attente pour être exécuté à nouveau.

- Votre application client peut ajouter des tâches à un projet, ou vous pouvez spécifier un [Gestionnaire de tâche](#job-manager-task). Une tâche de gestionnaire de projet contient les informations nécessaires créer les tâches requises pour un travail, avec la Gestionnaire de tâche en cours d’exécution sur l’un des nœuds de calcul dans le pool. La Gestionnaire de tâche est traitée spécialement par lot--il est en file d’attente dès que la tâche est créée et est redémarrée s’il échoue. Une gestionnaire de tâche est *requis* pour les tâches qui sont créées par une [planification du travail](#scheduled-jobs) , car il est le seul moyen de définir les tâches avant l’instanciation de la tâche.

- Par défaut, les tâches restent à l’état actif lorsque toutes les tâches du travail sont terminées. Vous pouvez modifier ce comportement afin que le travail est automatiquement interrompu lorsque toutes les tâches du travail sont terminées. Affectez la propriété **onAllTasksComplete** de la tâche ([OnAllTasksComplete] [ net_onalltaskscomplete] dans le traitement par lots .NET) à *terminatejob* pour terminer automatiquement la tâche lorsque toutes les tâches sont dans l’état terminé.

    Notez que le service de traitement par lots considère qu’un projet ne contenant *Aucune* tâche pour que toutes les tâches sont terminées. Par conséquent, cette option est généralement utilisée avec un [Gestionnaire de tâche](#job-manager-task). Si vous souhaitez utiliser l’arrêt de travail automatique sans gestionnaire de projet, vous devez initialement la valeur de propriété **d’onAllTasksComplete** d’un nouveau travail *noaction*, puis affectez-lui la valeur *terminatejob* uniquement après que vous avez terminé d’ajouter des tâches à la tâche.

### <a name="job-priority"></a>Priorité de la tâche

Vous pouvez affecter une priorité aux tâches que vous créez dans le traitement par lots. Le service de traitement par lots utilise la valeur de priorité de la tâche pour déterminer l’ordre de planification des tâches au sein d’un compte (ne doit ne pas être confondu avec un [travail planifié](#scheduled-jobs)). La priorité des valeurs comprises entre -1000 et 1000, avec-1000 étant la priorité la plus faible et 1000 la plus élevée. Vous pouvez mettre à jour la priorité d’une tâche à l’aide de la [mise à jour des propriétés d’une tâche] [ rest_update_job] opération (lot reste) ou en modifiant le [CloudJob.Priority] [ net_cloudjob_priority] propriété (lot .NET).

Dans le même compte, les travaux de priorité plus élevée ont la priorité de planification sur les travaux de priorité inférieure. Une tâche avec une valeur de priorité plus élevée dans un compte ne possède pas de planification prioritaire sur une autre pièce avec une valeur de priorité inférieure dans un autre compte.

Entre les pools de la planification des tâches sont indépendant. Entre pools différents, il n’est pas garanti qu’une priorité plus élevée est planifiée tout d’abord si son pool associé n’est pas inactifs nœuds. Dans le même pool, travaux avec le même niveau de priorité ont autant de chance d’être planifiée.

### <a name="scheduled-jobs"></a>Tâches planifiées

[Planifications de tâche] [ rest_job_schedules] vous permettent de créer des tâches périodiques dans le service de traitement par lots. Planification d’une tâche spécifie le moment de l’exécution des tâches et inclut les spécifications pour les travaux à exécuter. Vous pouvez spécifier la durée de la planification, la durée et lorsque la planification est en effet--ainsi que la fréquence au cours de cette période pendant laquelle les tâches doivent être créées.

## <a name="task"></a>Tâche

Une tâche est une unité de calcul qui est associé à une tâche. Il s’exécute sur un nœud. Les tâches sont attribuées à un nœud pour l’exécution, ou sont mises en attente jusqu'à ce qu’un nœud devient disponible. En réalité, une tâche exécute un ou plusieurs programmes ou des scripts sur un nœud de calcul pour effectuer le travail dont vous avez besoin.

Lorsque vous créez une tâche, vous pouvez spécifier :

- La **ligne de commande** de la tâche. Il s’agit de la ligne de commande qui exécute votre application ou un script sur le nœud de calcul.

    Il est important de noter que la ligne de commande n’exécute pas réellement sous un shell. Par conséquent, il ne peut pas en mode natif de tirer parti des fonctionnalités de shell comme expansion de la [variable d’environnement](#environment-settings-for-tasks) (Cela inclut le `PATH`). Pour tirer parti de ces fonctionnalités, vous devez appeler le shell de ligne de commande, par exemple en lançant `cmd.exe` sur les nœuds Windows ou `/bin/sh` sous Linux :

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Si vos tâches doivent exécuter une application ou un script qui n’est pas dans le nœud `PATH` ou référencer les variables d’environnement, appeler explicitement dans la ligne de commande des tâches, l’interpréteur de commandes.

- **Fichiers de ressources** qui contiennent les données à traiter. Ces fichiers sont copiés automatiquement vers le nœud depuis le stockage Blob dans un compte de stockage Azure **usage général** avant la ligne de commande de la tâche est exécutée. Pour plus d’informations, consultez les sections [Démarrer la tâche](#start-task) et des [fichiers et des répertoires](#files-and-directories).

- Les **variables d’environnement** qui sont requis par votre application. Pour plus d’informations, reportez-vous à la section [paramètres d’environnement pour les tâches](#environment-settings-for-tasks) .

- Les **contraintes** sous lequel la tâche doit s’exécuter. Pour l’exemple, la durée maximale pendant laquelle la tâche est autorisée à s’exécuter, le nombre maximal de fois où qu'une tâche ayant échoué doit être tentée, et la valeur maximale du temps que les fichiers dans le répertoire de travail de la tâche sont conservés.

- **Packages d’applications** à déployer sur le nœud de calcul sur lequel la tâche est planifiée pour s’exécuter. [Packages d’applications](#application-packages) fournissent de simplifier le déploiement et la gestion des versions des applications qui s’exécutent de vos tâches. Packages d’applications de niveau de tâche sont particulièrement utiles dans les environnements de pool partagé, où différentes tâches sont exécutées sur un pool et le pool n’est pas supprimé lorsqu’un travail est terminé. Si votre projet comporte des tâches moins de nœuds dans le pool, des packages d’application Office peuvent réduire le transfert de données depuis votre application est déployée uniquement sur les nœuds d’exécutent des tâches.

En plus des tâches que vous définissez pour effectuer un calcul sur un nœud, les tâches spéciales suivantes sont également fournies par le service de traitement par lots :

- [Démarrer la tâche](#start-task)
- [Le gestionnaire tâches](#job-manager-task)
- [Tâches de préparation et de la version](#job-preparation-and-release-tasks)
- [Tâches des instances multiples (MPI)](#multi-instance-tasks)
- [Interdépendances des tâches](#task-dependencies)

### <a name="start-task"></a>Démarrer la tâche

En associant un **Démarrer la tâche** à un pool, vous pouvez préparer l’environnement d’exploitation de ses nœuds. Par exemple, vous pouvez effectuer des actions telles que l’installation des applications qui s’exécutent de vos tâches et le démarrage des processus d’arrière-plan. Cette tâche s’exécute chaque fois qu’un nœud de démarrage, pour tant qu’il reste dans le pool, y compris lorsque le nœud est le premier ajouté au pool et lorsqu’il est redémarré ou de reconfiguration.

Des avantages majeurs de la tâche de début sont qu’il peut contenir toutes les informations nécessaires configurer un nœud de calcul et d’installer les applications qui sont requises pour l’exécution de la tâche. Par conséquent, il est aussi simple que de l’augmentation du nombre de nœuds dans un pool spécifiant le nouveau décompte de noeud cible--lot déjà dispose des informations nécessaires pour configurer les nouveaux nœuds et de les préparer pour l’acceptation des tâches.

Comme avec n’importe quelle tâche de traitement par lots d’Azure, vous pouvez spécifier une liste de **fichiers** de ressources dans le [Stockage Azure][azure_storage], en plus d’une **ligne de commande** à exécuter. Traitement par lots copie d’abord les fichiers de ressources vers le nœud de stockage Azure et exécute ensuite la ligne de commande. Pour une tâche de début de liste, la liste de fichiers contient généralement l’application de la tâche et ses dépendances.

Toutefois, il peut également inclure des données de référence pour être utilisée par toutes les tâches qui sont exécutent sur le nœud de calcul. Par exemple, la ligne de commande d’une tâche de début peut effectuer une `robocopy` opération de copier les fichiers d’application (qui a été spécifiés en tant que fichiers de ressources et téléchargés vers le nœud) à partir du [répertoire de travail](#files-and-directories) de de la tâche début pour le [dossier partagé](#files-and-directories)et d’exécuter un MSI ou `setup.exe`.

> [AZURE.IMPORTANT] Lot actuellement prend en charge *uniquement* le type de compte de stockage **polyvalente** , comme décrit à l’étape 5 de [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Vos tâches de traitement par lots (y compris les tâches standard, les tâches de démarrage, tâches de préparation et tâches version) doivent spécifier des fichiers de ressources qui résident *uniquement* dans les comptes de stockage **polyvalente** .

Il est généralement souhaitable pour le service de traitement par lots attendre la tâche à effectuer avant de considérer le nœud prêt à affecter des tâches de démarrage, mais vous pouvez la configurer.

Si une tâche de début échoue sur un nœud de calcul, l’état du nœud est mis à jour pour refléter l’échec, et le nœud n’est pas affecté toutes les tâches. Une tâche de début peut échouer s’il existe un problème de copie de ses fichiers de ressources de stockage, ou si le processus exécuté par sa ligne de commande retourne un code de sortie différent de zéro.

Si vous ajoutez ou mise à jour de la tâche de début pour un pool *existant* , vous devez redémarrer ses nœuds de calcul de la tâche de début à appliquer aux nœuds.

### <a name="job-manager-task"></a>Le gestionnaire tâches

Vous généralement utilisez un **Gestionnaire de tâche** au contrôle et/ou surveillez l’exécution de la tâche, par exemple, pour créer et soumettre les tâches d’un projet, déterminez les tâches supplémentaires à exécuter et de déterminer lorsque le travail est terminé. Toutefois, une gestionnaire de tâche n’est pas limitée à ces activités. Il s’agit d’une tâche chevronnée qui peut effectuer toutes les actions qui sont requises pour la tâche. Par exemple, une gestionnaire de tâche peut télécharger un fichier qui est spécifié comme un paramètre, analyser le contenu de ce fichier et soumettre les tâches supplémentaires basées sur ces matières.

Une gestionnaire de tâche est démarrée avant toutes les autres tâches. Il offre les fonctionnalités suivantes :

- Il est automatiquement soumise en tant que tâche par le service de traitement par lots lors de la création du travail.

- Elle est planifiée pour s’exécuter avant les autres tâches dans un projet.

- Son nœud associé est le dernier à être supprimé d’un pool lorsque le pool est en cours de downsized.

- La résiliation peut être liée à l’arrêt de toutes les tâches dans le projet.

- Une gestionnaire de tâche est la priorité la plus élevée lorsqu’il doit être redémarré. Si un nœud d’inactivité n’est pas disponible, le service de traitement par lots peut arrêter un des autres tâches en cours d’exécution dans le pool pour faire de la place de la tâche du gestionnaire à exécuter.

- Une gestionnaire de tâche dans un projet n’a pas la priorité sur les tâches des autres tâches. Entre les tâches, seulement les priorités de niveau tâche sont respectées.

### <a name="job-preparation-and-release-tasks"></a>Tâches de préparation et de la version

Lot présente les tâches de préparation pour l’installation de l’exécution de la tâche avant. Version de tâches sont postérieur au travail de maintenance ou de nettoyage.

- **Tâche de préparation du travail**: une tâche de préparation du projet s’exécute sur tous les nœuds de calcul qui sont planifiées pour exécuter des tâches, avant les autres tâches sont exécutées. Vous pouvez utiliser une tâche de préparation des données de copie qui est partagée par toutes les tâches, mais qui est unique au projet, par exemple.
- **Version de tâche**: lorsqu’un travail est terminé, une tâche de version s’exécute sur chaque nœud dans le pool qui a exécuté au moins une tâche. Vous pouvez utiliser une tâche de publication pour supprimer les données qui sont copiées par la tâche de préparation, ou de compresser et de télécharger des données de journal de diagnostic, par exemple.

Les deux tâches de préparation et des tâches de publication permettent de spécifier une ligne de commande à exécuter lorsque la tâche est appelée. Ils offrent des fonctionnalités telles que le téléchargement de fichiers, l’exécution avec des privilèges élevés, variables d’environnement personnalisées, durée d’exécution maximale, nombre de tentatives et la période de rétention.

Pour plus d’informations sur les tâches de préparation et de version, consultez [exécuter préparation achèvement tâches et Azure lot des nœuds de calcul](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tâche de plusieurs instances

Une [tâche de plusieurs instances](batch-mpi.md) est une tâche qui est configurée pour s’exécuter simultanément sur plusieurs nœuds de calcul. Les tâches de plusieurs instances, vous pouvez activer des scénarios informatiques hautes performances qui nécessitent un groupe de nœuds de calcul qui sont alloués entre eux pour traiter une charge de travail unique (comme Interface MPI (Message Passing)).

Pour plus d’informations sur l’exécution de travaux MPI dans le traitement par lots à l’aide de la bibliothèque .NET de traitement par lots, consultez [Utilisez les tâches de plusieurs instances pour exécuter des applications d’Interface MPI (Message Passing) dans le traitement par lots d’Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Interdépendances des tâches

[Interdépendances des tâches](batch-task-dependencies.md), comme son nom l’indique, permet de spécifier qu’une tâche dépend de l’achèvement d’autres tâches avant son exécution. Cette fonctionnalité prend en charge les situations dans lesquelles une tâche « en aval » consomme la sortie d’une tâche « en amont »--ou lorsqu’une tâche en amont effectue une initialisation qui est requis par une tâche en aval. Pour utiliser cette fonctionnalité, vous devez d’abord activer les interdépendances des tâches sur le traitement par lots. Ensuite, pour chaque tâche qui dépend d’un autre (ou bien d’autres), vous spécifiez les tâches qui dépend de cette tâche.

Avec les interdépendances des tâches, vous pouvez configurer des scénarios comme suit :

* *taskB* dépend de *taskA* (*taskB* ne commence pas l’exécution jusqu'à ce que *taskA* est terminé).
* *taskC* dépend à la fois *taskA* et *taskB*.
* *taskD* dépend d’une plage de tâches, telles que les tâches *1* à *10*, avant qu’elle exécute.

Vérifiez les [interdépendances des tâches dans un lot d’Azure](batch-task-dependencies.md) et la [TaskDependencies] [ github_sample_taskdeps] exemple de code dans les [exemples de traitement par lots azure] [ github_samples] référentiel GitHub pour plus d’informations détaillées sur cette fonctionnalité.

## <a name="environment-settings-for-tasks"></a>Paramètres d’environnement pour les tâches

Chaque tâche exécutée par le service de traitement par lots a accès aux variables d’environnement qu’il définit sur les nœuds de calcul. Cela inclut des variables d’environnement définies par le service de traitement par lots ([définis par le service][msdn_env_vars]) et les variables d’environnement personnalisées que vous pouvez définir pour vos tâches. Les applications et les scripts de qu'exécution de vos tâches ont accès à ces variables d’environnement lors de l’exécution.

Vous pouvez définir des variables d’environnement personnalisées au niveau de la tâche en remplissant la propriété de *paramètres d’environnement* pour ces entités. Par exemple, voir [Ajouter une tâche à un travail] [ rest_add_task] opération (API REST de lot), ou le [CloudTask.EnvironmentSettings] de[ net_cloudtask_env] et [CloudJob.CommonEnvironmentSettings] [ net_job_env] propriétés dans .NET de traitement par lots.

Votre service ou votre application cliente pour obtenir les variables d’environnement d’une tâche, définies par le service et personnalisés, [obtenir des informations sur une tâche] [ rest_get_task_info] opération (lot reste) ou en accédant à la [CloudTask.EnvironmentSettings] [ net_cloudtask_env] propriété (lot .NET). Processus en cours d’exécution sur un nœud de calcul peuvent accéder celles-ci et autres variables d’environnement sur le nœud, par exemple, à l’aide de la bien connue `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` syntaxe (Linux).

Vous trouverez une liste complète de toutes les variables d’environnement de service défini dans les [variables d’environnement de nœud de calcul][msdn_env_vars].

## <a name="files-and-directories"></a>Fichiers et répertoires

Chaque tâche possède un *répertoire de travail* sous lequel il crée zéro ou plus de fichiers et de répertoires. Ce répertoire de travail peut être utilisé pour stocker le programme exécuté par la tâche et les données qu’il traite la sortie de la transformation qu’il effectue. Tous les fichiers et les répertoires d’une tâche sont appartenant à l’utilisateur de la tâche.

Le service de traitement par lots expose qu’une partie du système de fichiers sur un nœud que le *répertoire racine*. Les tâches peuvent accéder au répertoire racine en référençant le `AZ_BATCH_NODE_ROOT_DIR` variable d’environnement. Pour plus d’informations sur l’utilisation de variables d’environnement, consultez [paramètres d’environnement pour les tâches](#environment-settings-for-tasks).

Le répertoire racine contient la structure de répertoire suivante :

![Structure de répertoires du nœud de calcul][1]

- **partagé**: ce répertoire fournit un accès en lecture/écriture à *toutes les* tâches qui s’exécutent sur un nœud. N’importe quelle tâche qui s’exécute sur le nœud peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Les tâches peuvent accéder à ce répertoire en référençant le `AZ_BATCH_NODE_SHARED_DIR` variable d’environnement.

- **démarrage**: ce répertoire est utilisé par une tâche de début comme répertoire de travail. Tous les fichiers qui sont téléchargés vers le nœud par la tâche de début sont stockés ici. Cette tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Les tâches peuvent accéder à ce répertoire en référençant le `AZ_BATCH_NODE_STARTUP_DIR` variable d’environnement.

- **Tâches**: un répertoire est créé pour chaque tâche qui s’exécute sur le nœud. Il est accessible en référençant le `AZ_BATCH_TASK_DIR` variable d’environnement.

    Au sein de chaque répertoire de la tâche, le service de traitement par lots crée un répertoire de travail (`wd`) unique dont le chemin est spécifié par le `AZ_BATCH_TASK_WORKING_DIR` variable d’environnement. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Ce répertoire est conservé en fonction de la contrainte de *RetentionTime* qui est spécifiée pour la tâche.

    `stdout.txt`et `stderr.txt`: ces fichiers sont écrits dans le dossier de tâches lors de l’exécution de la tâche.

>[AZURE.IMPORTANT] Lorsqu’un nœud est supprimé de la liste, *tous* les fichiers qui sont stockés sur le nœud sont supprimés.

## <a name="application-packages"></a>Packages d’applications

La fonctionnalité [packages d’applications](batch-application-packages.md) fournit la simplicité de gestion et de déploiement des applications sur les nœuds de calcul dans vos pools. Vous pouvez télécharger et gérer plusieurs versions des applications exécutées par les tâches, y compris leurs fichiers binaires et les fichiers de prise en charge. Puis, vous pouvez déployer automatiquement un ou plusieurs de ces applications aux nœuds de calcul dans le pool.

Vous pouvez spécifier des packages d’application au niveau du pool et les tâches. Lorsque vous spécifiez des packages d’application pool, l’application est déployée sur tous les nœuds dans le pool. Lorsque vous spécifiez des packages d’application de tâche, l’application est déployée qu’aux nœuds qui sont planifiées pour s’exécuter au moins une des tâches du travail juste avant l’exécution de ligne de commande de la tâche.

Lot gère les détails de l’utilisation de stockage Azure pour stocker vos packages d’applications et de les déployer pour le calcul des nœuds, afin que votre code et la gestion de surcharge peut être simplifié.

Pour en savoir plus sur la fonctionnalité de package d’application, consultez [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md).

>[AZURE.NOTE] Si vous ajoutez des packages d’application pool à un pool *existant* , vous devez redémarrer les nœuds de calcul pour les packages d’application à déployer sur les nœuds.

## <a name="pool-and-compute-node-lifetime"></a>Durée de vie de nœud pool et de compute

Lorsque vous concevez votre solution de traitement par lots d’Azure, vous devez rendre une décision sur la manière et lorsque les pools sont créés et calculent combien de temps les nœuds au sein de ces pools restent disponibles.

À une extrémité du spectre, vous pouvez créer un pool pour chaque projet que vous soumettez et supprimer le pool dès que ses tâches de fin d’exécution. Cela permet d’optimiser l’utilisation parce que les nœuds ne sont alloués que lorsque nécessaire et arrêter dès qu’ils sont inactifs. Bien que cela signifie que la tâche doit attendre pour les nœuds à allouer, il est important de noter que les tâches sont planifiées pour une exécution dès que les nœuds sont disponibles individuellement, alloué, et cette tâche est terminée. Traitement par lots est *pas* attendre jusqu'à ce que tous les nœuds au sein d’un pool sont disponibles avant d’affecter les tâches aux nœuds. Cela garantit une utilisation maximale de tous les nœuds disponibles.

À l’autre extrémité du spectre, si les tâches démarrer immédiatement est la priorité la plus élevée, vous pouvez créer un pool d’avance et rendre ses nœuds disponibles avant l’envoi des travaux. Dans ce scénario, les tâches peuvent démarrer immédiatement, mais les nœuds peuvent restez inactif en attendant leur être attribué.

Une approche combinée est généralement utilisée pour gérer une charge variable, mais il est en cours. Vous pouvez avoir un pool de plusieurs travaux sont soumis à, mais il peuvent mettre à l’échelle le nombre de nœuds vers le haut ou vers le bas en fonction de la tâche charge (voir [ressources de calcul mise à l’échelle](#scaling-compute-resources) dans la section suivante). Cela réactive, basée sur la charge en cours, ou de façon proactive, si la charge est prévisible.

## <a name="scaling-compute-resources"></a>Mise à l’échelle de ressources informatiques

[Mise à l’échelle automatique](batch-automatic-scaling.md), vous avez le service de traitement par lots Ajuster dynamiquement le nombre de nœuds de calcul dans un pool en fonction de l’utilisation de ressources et la charge de travail actuelle de votre scénario de calcul. Cela vous permet de réduire le coût total de l’exécution de votre application en utilisant uniquement les ressources dont vous avez besoin et en relâchant ceux dont vous n’avez pas besoin.

Vous activez la mise à l’échelle automatique en écrire une [formule de mise à l’échelle automatique](batch-automatic-scaling.md#automatic-scaling-formulas) et en associant cette formule à un pool. Le service de traitement par lots utilise la formule pour déterminer le nombre de cibles de nœuds dans le pool pour le prochain intervalle de mise à l’échelle (un intervalle que vous pouvez configurer). Vous pouvez spécifier les paramètres de mise à l’échelle automatiques d’un pool lorsque vous créez, ou activez la mise à l’échelle sur un pool ultérieurement. Vous pouvez également actualiser les paramètres de mise à l’échelle sur un pool prenant en charge mise à l’échelle.

Par exemple, peut-être un travail nécessite que vous envoyez un très grand nombre de tâches à exécuter. Vous pouvez affecter une formule de mise à l’échelle vers le pool qui ajuste le nombre de nœuds dans le pool selon le nombre de tâches en file d’attente et le taux d’achèvement des tâches dans le projet. Périodiquement, le service de traitement par lots calcule la formule et redimensionne le pool, en fonction de la charge de travail (ajouter des nœuds pour de nombreuses tâches en file d’attente et supprimer des nœuds pour aucune tâche en file d’attente ou en cours d’exécution) et les autres paramètres de formule.

Une formule de mise à l’échelle peut être basée sur les mesures suivantes :

- **Audits sur les temps** sont basées sur les statistiques collectées toutes les cinq minutes dans le nombre d’heures spécifié.

- **Les mesures de ressource** sont basés sur l’utilisation du processeur, l’utilisation de la bande passante, utilisation de la mémoire et nombre de nœuds.

- **Tâche** est basée sur l’état de la tâche, par exemple *actif* (en attente), *en cours d’exécution*ou *terminé*.

Lors de la mise à l’échelle automatique réduit le nombre de nœuds de calcul dans un pool, vous devez considérer comment gérer les tâches qui s’exécutent au moment de l’opération de réduction. Pour ce faire, traitement par lots propose une *option de désallocation de nœud* que vous pouvez inclure dans vos formules. Par exemple, vous pouvez spécifier que les tâches en cours d’exécution arrêtées immédiatement, arrêtés immédiatement puis remise en attente pour l’exécution sur un autre nœud ou autorisées à se terminer avant que le nœud est supprimé de la liste.

Pour plus d’informations sur l’adaptation automatique d’une application, consultez [automatiquement échelle de nœuds de calcul dans un pool de traitement par lots d’Azure](batch-automatic-scaling.md).

> [AZURE.TIP] Pour optimiser l’utilisation de ressources de calcul, définissez le nombre de cibles de nœuds à zéro à la fin d’une tâche, mais autorise l’exécution de tâches à se terminer.

## <a name="security-with-certificates"></a>Sécurité et certificats

Vous devez généralement utiliser des certificats lorsque vous chiffrer ou déchiffrer des informations sensibles pour les tâches, comme la clé pour un [compte de stockage Azure][azure_storage]. Pour ce faire, vous pouvez installer des certificats sur les nœuds. Les secrets chiffrés sont passés aux tâches via les paramètres de ligne de commande ou incorporées dans une des ressources de la tâche, et les certificats installés peuvent être utilisés pour déchiffrer les.

Vous utilisez le [certificat d’ajouter] [ rest_add_cert] opération (lot reste) ou [CertificateOperations.CreateCertificate] [ net_create_cert] méthode (lot .NET) pour ajouter un certificat à un compte de traitement par lots. Vous pouvez ensuite associer le certificat à un pool nouveau ou existant. Lorsqu’un certificat est associé à un pool, le service de traitement par lots installe le certificat sur chaque nœud dans le pool. Le service de traitement par lots installe les certificats appropriés lorsque le nœud de démarrage, avant de lancer les tâches (y compris le Gestionnaire de tâche et de la tâche de début).

Si vous ajoutez des certificats à un pool *existant* , vous devez redémarrer les nœuds de calcul pour les certificats à appliquer aux nœuds.

## <a name="error-handling"></a>Gestion des erreurs

Vous pourriez trouver nécessaire pour gérer des échecs de tâche et d’application dans votre solution de traitement par lots.

### <a name="task-failure-handling"></a>Gestion des échecs de tâche
Échecs de tâche peuvent être classées dans les catégories suivantes :

- **Planification des échecs**

    Si le transfert de fichiers qui sont spécifiés pour une tâche échoue pour une raison quelconque, une « erreur de planification » est définie pour la tâche.

    Planification des erreurs peut se produire si les fichiers de ressources de la tâche ont déplacé, le compte de stockage n’est plus disponible ou qui a empêché la réussite copie des fichiers vers le nœud a été rencontré par un autre problème.

- **Défaillances d’applications**

    Le processus qui est spécifié par la ligne de commande de la tâche peut échouer également. Le processus est considérée comme ayant échoué lorsqu’un code de sortie différent de zéro est retourné par le processus qui est exécuté par la tâche (voir *les codes de sortie de tâche* dans la section suivante).

    Pour les défaillances d’applications, vous pouvez configurer le lot pour réessayer automatiquement de la tâche jusqu'à un nombre spécifié de fois.

- **Échecs de contrainte**

    Vous pouvez définir une contrainte qui spécifie la durée maximale d’exécution pour une tâche ou d’une tâche, le *maxWallClockTime*. Cela peut être utile pour terminer les tâches de « bloqué ».

    Lorsque la durée maximale a été dépassée, la tâche est marquée comme *terminée*, mais le code de sortie a la valeur `0xC000013A` et le champ *schedulingError* est marqué comme `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Débogage des échecs d’application

- `stderr`et`stdout`

    Lors de l’exécution, une application peut produire une sortie de diagnostic que vous pouvez utiliser pour résoudre les problèmes. Comme mentionné précédemment dans la section [fichiers et répertoires](#files-and-directories), le service de traitement par lots écrit la sortie standard et la sortie d’erreur standard à `stdout.txt` et `stderr.txt` les fichiers dans le répertoire de la tâche sur le nœud de calcul. Pour télécharger ces fichiers, vous pouvez utiliser le portail Azure ou parmi les kits de développement de lot. Par exemple, vous pouvez récupérer ces et autres fichiers pour résoudre des problèmes à l’aide de [ComputeNode.GetNodeFile] [ net_getfile_node] et [CloudTask.GetNodeFile] [ net_getfile_task] dans la bibliothèque .NET de traitement par lots.

- **Codes de sortie de tâche**

    Comme mentionné précédemment, une tâche est marquée comme ayant échoué par le service de traitement par lots si le processus est exécuté par la tâche retourne un code de sortie différent de zéro. Lorsqu’une tâche exécute un processus, lot remplit la propriété de code de sortie de la tâche avec le *code de retour de la procédure*. Il est important de noter que le code de sortie d’une tâche est **pas** déterminé par le service de traitement par lots : il est déterminé par le processus lui-même ou par le système d’exploitation sur lequel le processus est exécuté.

### <a name="accounting-for-task-failures-or-interruptions"></a>Comptabilisation des échecs de tâche ou des interruptions

Les tâches peuvent parfois échouer ou être interrompus. L’application Office elle-même peut échouer, le nœud sur lequel la tâche est en cours d’exécution peut être redémarré, ou le nœud risque d’être retiré du pool au cours d’une opération de redimensionnement si la règle de désallocation du pool est définie pour supprimer les nœuds immédiatement sans attendre les tâches à se terminer. Dans tous les cas, la tâche peut être automatiquement sera remise en attente par un traitement par lots pour l’exécution sur un autre nœud.

Il est également possible pour un problème intermittent provoquer une tâche à se bloquer ou trop longue à s’exécuter. Vous pouvez définir la durée d’exécution maximale pour une tâche. Si elle est dépassée, le lot interrompt l’application Office.

### <a name="connecting-to-compute-nodes"></a>Connexion pour les nœuds de calcul

Vous pouvez effectuer le débogage et la résolution des problèmes en vous connectant à distance à un nœud de calcul supplémentaire. Vous pouvez utiliser le portail Azure pour télécharger un fichier de protocole RDP (Remote Desktop) pour les nœuds de Windows et d’obtenir des informations de connexion SSH (Secure Shell) pour les nœuds de Linux. Vous pouvez également le faire à l’aide de l’API de lot--par exemple, avec [.NET de lot] [ net_rdpfile] ou [Les Python de traitement par lots](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Pour vous connecter à un nœud via RDP ou SSH, vous devez d’abord créer un utilisateur sur le nœud. Pour ce faire, vous pouvez utiliser Azure portail, [Ajouter un compte d’utilisateur pour un nœud] [ rest_create_user] à l’aide de l’API REST de lot, appelez le [ComputeNode.CreateComputeNodeUser] de[ net_create_user] méthode dans .NET de lot ou de l' [Ajouter un utilisateur] [ py_add_user] méthode dans le module les Python de traitement par lots.

### <a name="troubleshooting-bad-compute-nodes"></a>Résolution des problèmes « mal » des nœuds de calcul

Dans le cas où certaines tâches échouent, votre application cliente de lot ou d’un service peut examiner les métadonnées des tâches pour identifier un dysfonctionnement du nœud défaillants. Chaque nœud d’un pool est donné à un ID unique et le nœud sur lequel s’exécute une tâche est inclus dans les métadonnées de la tâche. Une fois que vous avez identifié un nœud du problème, vous pouvez prendre plusieurs actions :

- **Redémarrez le nœud.** ([REST][rest_reboot] | [.NET][net_reboot])

    Redémarrer le nœud peut parfois libérer de problèmes latents comme processus manquants ou en panne. Notez que si votre pool utilise une tâche de démarrage ou si votre projet utilise une tâche de préparation du projet, elles sont exécutées lorsque le nœud redémarre.

- **Recréez le nœud** ([REST][rest_reimage] | [.NET][net_reimage])

    Cela réinstalle le système d’exploitation sur le nœud. Comme avec redémarrage d’un nœud, démarrer des tâches et tâches de préparation sont réexécutés après que le nœud a été reconfiguration.

- **Supprimer le nœud à partir du pool** ([REST][rest_remove] | [.NET][net_remove])

    Il est parfois nécessaire de supprimer complètement le nœud à partir du pool.

- **Désactiver sur le nœud de planification des tâches** ([REST][rest_offline] | [.NET][net_offline])

    Efficacement prend le nœud « hors connexion », afin qu’aucune autre tâche lui n’est affectés, ce service permet le nœud reste en cours d’exécution et dans le pool. Cela vous permet d’effectuer plus approfondi de la cause des échecs sans perdre les données de la tâche a échoué et sans le nœud provoquant des défaillances des tâches supplémentaires. Par exemple, vous pouvez désactiver la tâche de la planification sur le nœud, puis [Ouvrez une session à distance](#connecting-to-compute-nodes) examiner les journaux des événements de ce nœud ou autres de la résolution des problèmes. Une fois que vous avez terminé vos recherches, vous pouvez puis mettre le nœud en ligne par l’activation de la planification des tâches ([reste][rest_online] | [.NET][net_online]), ou effectuez l’une des actions décrites précédemment.

> [AZURE.IMPORTANT] À chaque action décrite dans cette section, vous devez redémarrer, créer une nouvelle image, supprimer et désactiver la planification des tâches, vous ne pouvez spécifier la façon dont sont gérés les tâches en cours d’exécution sur le nœud lorsque vous effectuez l’action. Par exemple, lorsque vous désactivez la planification de tâches sur un nœud à l’aide de la bibliothèque cliente .NET de traitement par lots, vous pouvez spécifier un [DisableComputeNodeSchedulingOption] [ net_offline_option] valeur enum à spécifier si pour **Terminer** l’exécution des tâches, **Requeue** leur planification sur d’autres nœuds, ou de permettre l’exécution de tâches à effectuer avant d’exécuter l’action (**TaskCompletion**).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue un exemple d’application commandes détaillée dans [mise en route avec la bibliothèque de commandes Azure pour .NET](batch-dotnet-get-started.md). Il existe également une [version de Python](batch-python-tutorial.md) du didacticiel qui s’exécute d’une charge de travail sur des nœuds de calcul Linux.

- Télécharger et générer [Lot Explorer] [ github_batchexplorer] exemple de projet à utiliser lors du développement de vos solutions de traitement par lots. À l’aide de l’Explorateur de traitement par lots, vous pouvez effectuer ce qui suit et bien plus encore :
  - Surveiller et manipuler les pools, les travaux et les tâches au sein de votre compte de traitement par lots
  - Télécharger `stdout.txt`, `stderr.txt`et d’autres fichiers à partir de nœuds
  - Créer des utilisateurs sur les nœuds et télécharger des fichiers RDP pour la connexion d’accès distante

- Découvrez comment [créer des pools de nœuds de calcul Linux](batch-linux-nodes.md).

- Visitez le [forum d’Azure lot] [ batch_forum] sur le site MSDN. Le forum est un bon emplacement pour poser des questions, que vous venez de découvrir ou êtes un expert dans l’utilisation du traitement par lots.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
