<properties
    pageTitle="Qu’est-R sur HDInsight ? Introduction au serveur R HDInsight (aperçu) | Microsoft Azure"
    description="Quel est le serveur R HDInsight (aperçu) et comment utiliser R Server pour créer des applications pour l’analyse de données volumineuses."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>Vue d’ensemble du serveur R HDInsight \(aperçu\)

Avec Microsoft Azure HDInsight Premium, Microsoft R Server est désormais disponible sous forme d’option lorsque vous créez des clusters de HDInsight dans Azure. Cette nouvelle fonctionnalité fournit des données scientifiques, les statisticiens et programmeurs R avec accès à la demande à évolutif, distribué méthodes d’analytique sur HDInsight.

Les clusters peuvent être dimensionnés pour les projets et les tâches en cours et détruits lorsqu’ils ne sont plus nécessaires. Car ils sont partie de HDInsight d’Azure, ces clusters sont fournis avec la prise en charge au niveau de l’entreprise 24 x 7, un contrat SLA de 99,9 % et la flexibilité pour l’intégration avec d’autres composants de l’écosystème Azure.

>[AZURE.NOTE] Serveur de R est disponible uniquement avec HDInsight prime. Actuellement, HDInsight Premium est uniquement disponible pour les clusters Hadoop et allumage. Ainsi, actuellement vous pouvez utiliser R serveur uniquement avec Hadoop et érosion clusters sur HDInsight. Pour plus d’informations, consultez [Quels sont les différents niveaux de service et les composants d’Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md).

Serveur R HDInsight fournit les capacités les plus récentes pour l’analytique de base R sur grands ensembles de données qui sont chargées pour le stockage des objets Blob Azure. Dans la mesure où R Server repose sur Ouvrir la source de R, les applications R que vous créez peuvent exploiter des packages open source R 8000 +, ainsi que les routines de DETARTREUR, package d’analytique de données volumineuses de Microsoft fourni avec le serveur de R.

Le nœud de bord de clusters de prime fournit un emplacement pratique pour se connecter au cluster et exécuter vos scripts de R. Avec un nœud, vous avez la possibilité d’exécuter des fonctions de distribuées parallélisées de DETARTREUR sur les cœurs du serveur edge nœud. Vous avez également la possibilité de les exécuter sur les nœuds du cluster à l’aide Hadoop carte réduire de DETARTREUR ou étincelle calculer des contextes.

Les modèles ou les prévisions que vous peuvent télécharger les résultats des analyses pour utilisent sur site. Ils peuvent également être operationalized ailleurs dans Azure, tels que via un [service web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)de [Studio de formation de Machine Azure](http://studio.azureml.net) .

## <a name="get-started-with-r-on-hdinsight"></a>Mise en route de R sur HDInsight

Pour inclure R Server dans un cluster de HDInsight, vous devez créer un Hadoop ou un allumage de cluster avec l’option Premium lorsque vous créez un cluster à l’aide du portail Azure. Les deux types de cluster utilisent la même configuration, ce qui inclut le serveur de R sur les noeuds d’un cluster et un nœud comme zone lancement pour l’analytique de R serveur. Pour une vue d’ensemble approfondie sur la création d’un cluster, consultez [Mise en route du serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md) .

## <a name="learn-about-data-storage-options"></a>En savoir plus sur les options de stockage de données

Stockage par défaut pour les clusters de HDInsight est le stockage Blob avec le système de fichiers très mappé à un conteneur d’objet blob. Cela garantit que toutes les données sont téléchargées vers le stockage de cluster, ou écrites dans le stockage de cluster au cours de l’analyse, est conservés de façon permanente. Vous pouvez utiliser l’utilitaire [AzCopy](../storage/storage-use-azcopy.md) pour copier les données vers et à partir de l’objet blob.

En plus d’utiliser le stockage Blob, vous avez la possibilité d’utiliser [Le stockage Azure données lac](https://azure.microsoft.com/services/data-lake-store/) avec votre cluster. Si vous utilisez des données lac, vous pouvez utiliser à la fois stockage Blob et lac de données pour le stockage de très.

Vous pouvez également utiliser des [Fichiers d’Azure](../storage/storage-how-to-use-files-linux.md) sous la forme d’une option de stockage pour une utilisation sur le nœud du bord. Fichiers Azure vous permet de monter un partage de fichiers qui a été créé dans le stockage Azure pour le système de fichiers Linux. Pour plus d’informations sur les options de stockage de données pour le serveur de R sur cluster de HDInsight, reportez-vous à la section [options de stockage pour serveur R sur les clusters de HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Serveur d’accès R sur le cluster

Une fois que vous avez créé un cluster avec serveur de R, vous pouvez vous connecter à la console R sur le nœud du bord du cluster via SSH/PuTTY. Vous pouvez également vous connecter via un navigateur si vous choisissez d’installer l’IDE du serveur RStudio facultatif sur le nœud du bord. Pour plus d’informations sur l’installation de RStudio Server, voir [Installation de Server sur des clusters de HDInsight RStudio](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Développer et exécuter des scripts de R

Vous créez et exécutez les scripts de R peuvent utiliser des packages open source R 8000 + en plus les routines parallélisés et distribuées dans la bibliothèque ScaleR. En général, qui est exécuté dans R Server sur le nœud de bord s’exécute dans l’interpréteur R sur ce nœud. L’exception concerne les opérations qui appellent un ScaleR fonctionnent avec un contexte de calcul défini pour réduire de mappage d’Hadoop (RxHadoopMR) ou d’allumage (RxSpark).

Dans ce cas, la fonction s’exécute dans un mode distribué sur ces données nœuds (tâche) du cluster qui sont associés aux données référencées. Pour plus d’informations sur les options de contexte de calcul différentes, consultez [calculer les options de contexte du serveur R HDInsight prime](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Effectuent un modèle

Lorsque votre modélisation des données est terminée, vous pouvez effectuent le modèle pour effectuer des prévisions pour les nouvelles données dans Azure et sur site. Ce processus est appelé le score. Voici quelques exemples.

### <a name="score-in-hdinsight"></a>Score de HDInsight

Pour HDInsight de score, écrire une fonction de R qui appelle votre modèle pour établir des prévisions pour un nouveau fichier de données que vous avez chargé sur votre compte de stockage. Puis enregistrez les prévisions vers le compte de stockage. Vous pouvez exécuter la routine à la demande sur le nœud de bord de votre cluster ou à l’aide d’une tâche planifiée.  

### <a name="score-in-azure-machine-learning"></a>Score dans l’apprentissage automatique Azure

Pour le score à l’aide d’un service web de formation de Machine d’Azure, utilisez le package Azure Machine Learning R open source appelé [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) pour publier votre modèle sous la forme d’un service web Azure. Pour plus de commodité, ce package est déjà installé sur le nœud du bord. Ensuite, les installations dans l’apprentissage automatique permet de créer une interface utilisateur pour le service web et ensuite appeler le service web en fonction des besoins pour l’attribution de scores.

Si vous choisissez cette option, vous devez convertir les objets de modèle de DETARTREUR aux objets modèle d’open source équivalente pour une utilisation avec le service web. Ceci est possible grâce à l’utilisation de fonctions de coercition ScaleR, tel que `as.randomForest()` basée sur un ensemble de modèles.


### <a name="score-on-premises"></a>Score sur site

Pour local après la création de votre modèle de score, vous pouvez sérialiser le modèle dans le R, télécharger, désérialiser et puis l’utiliser pour l’évaluation des nouvelles données. Vous pouvez score de nouvelles données à l’aide de l’approche décrite plus haut dans le [score de HDInsight](#scoring-in-hdinsight) ou à l’aide de [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Gestion du cluster

### <a name="install-and-maintain-r-packages"></a>Installer et mettre à jour les packages de R

La plupart des packages R que vous utilisez sera nécessaire sur le nœud de bord étant donné que la plupart de vos scripts de R s’il exécute. Pour installer des packages supplémentaires R sur le nœud de bord, vous pouvez utiliser la commande habituel `install.packages()` méthode dans R.

Dans la plupart des cas, vous n’avez pas besoin d’installer des packages supplémentaires R sur les nœuds de données si vous utilisez uniquement des routines de la bibliothèque ScaleR au sein du cluster. Toutefois, vous devrez peut-être des packages supplémentaires pour prendre en charge l’utilisation de **rxExec** ou de l’exécution de **RxDataStep** sur les nœuds de données.

Dans ces cas, les packages supplémentaires doivent être spécifiés via l’utilisation d’une action de script après la création du cluster. Pour plus d’informations, consultez [Création d’un cluster HDInsight avec serveur de R](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Modifier les paramètres de mémoire Hadoop réduire de mappage

Un cluster peut être modifié pour modifier la quantité de mémoire disponible pour le serveur de R lorsqu’il exécute une tâche de réduction de mappage. Pour modifier un cluster, utilisez l’interface utilisateur Ambari Apache qui est disponible par le biais de la lame de portail Azure pour votre cluster. Pour savoir comment accéder à la Ambari UI pour votre cluster, voir [HDInsight de gérer les clusters à l’aide de l’interface utilisateur Web de Ambari](hdinsight-hadoop-manage-ambari.md).

Il est également possible de modifier la quantité de mémoire disponible pour R Server à l’aide de commutateurs d’Hadoop dans l’appel à **RxHadoopMR** comme suit :

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Faire évoluer votre cluster

Un cluster existant peut être mis à l’échelle vers le haut ou vers le bas via le portail. Par la mise à l’échelle, vous avez la capacité supplémentaire que vous devrez peut-être de plus grandes tâches de traitement, ou vous pouvez mettre à l’échelle en un cluster lorsqu’il est inactif. Pour obtenir des instructions sur la façon de mettre à l’échelle d’un cluster, consultez le [HDInsight de gérer les clusters](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Maintenance du système

Opération de maintenance sur les ordinateurs virtuels Linux sous-jacente dans un cluster HDInsight pendant les heures creuses pour appliquer des correctifs de système d’exploitation et d’autres mises à jour. En règle générale, la maintenance est effectuée à 3 h 30 (selon l’heure locale de la machine virtuelle) chaque lundi et le jeudi. Mises à jour sont effectuées de manière à ce qu’elles n’agissent pas sur plus d’un quart du cluster à la fois.  

Étant donné que les nœuds têtes sont redondants et pas tous les nœuds de données sont concernés, tous les travaux qui sont en cours d’exécution risquent de ralentir. Elles doivent s’exécutent toujours à la fin, toutefois. N’importe quel logiciel personnalisé ou les données locales que vous avez préservées dans ces événements de maintenance, sauf si une panne grave se produit qui nécessite une reconstruction du cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>En savoir plus sur les options IDE pour R Server sur un cluster HDInsight

Le nœud de bord Linux d’un cluster HDInsight prime est la zone de lancement pour l’analyse basée sur R. Après la connexion au cluster, vous pouvez lancer l’interface de la console au serveur de R en tapant **R** à l’invite de commande Linux. Utilisation de l’interface de la console est améliorée si vous exécutez un éditeur de texte pour le développement de script R dans une autre fenêtre et coupez et collez des sections de votre script dans la console de R en fonction des besoins.

Un outil plus sophistiqué pour le développement de votre script de R est l’IDE basé sur R pour une utilisation sur le bureau, telles que Microsoft a récemment annoncé que les [Outils R pour Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTV). Il s’agit d’une gamme d’outils de bureau et de serveur à partir de [RStudio](https://www.rstudio.com/products/rstudio-server/). Vous pouvez également utiliser basés sur Eclipse de Walware [StatET](http://www.walware.de/goto/statet).

Une autre option consiste à installer un IDE sur le nœud de bord Linux lui-même.  Un choix répandu est le [Serveur de RStudio](https://www.rstudio.com/products/rstudio-server/), qui fournit un IDE basé sur navigateur pour une utilisation par des clients distants. L’installation serveur de RStudio sur le nœud du bord d’un cluster HDInsight prime offre une complète IDE pour le développement et l’exécution de scripts de R avec R Server sur le cluster. Il peut être beaucoup plus productif qu’avec la console R.  Si vous souhaitez utiliser le serveur de RStudio, reportez-vous à la section [Installation de Server sur des clusters de HDInsight RStudio](hdinsight-hadoop-r-server-install-r-studio.md).

## <a name="learn-about-pricing"></a>En savoir plus sur la tarification

Les frais sont associés à un cluster HDInsight Premium avec serveur de R sont structurés de la même façon aux redevances pour les clusters HDInsight standard. Ils sont basés sur la taille des ordinateurs virtuels sous-jacent sur le nom, les données et les noeuds, avec l’ajout d’une majoration de core-heure de prime. Pour plus d’informations sur la prime de HDInsight prix, y compris les prix au cours de la version d’évaluation et de la disponibilité d’une version d’évaluation gratuite de 30 jours, reportez-vous à la section [HDInsight de tarification](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Étapes suivantes

Suivez les liens ci-dessous pour en savoir plus sur l’utilisation du serveur de R HDInsight clusters.

- [Mise en route du serveur R HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Ajouter les serveur de RStudio à HDInsight prime](hdinsight-hadoop-r-server-install-r-studio.md)

- [Calculer les options de contexte pour le serveur de R sur HDInsight (aperçu)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Options de stockage Azure pour R Server sur HDInsight prime](hdinsight-hadoop-r-server-storage.md)
