<properties 
    pageTitle="Une vue d’ensemble de l’allumage d’Apache dans HDInsight | Microsoft Azure" 
    description="Introduction à l’allumage Apache dans HDInsight et les scénarios dans lesquels utiliser étincelle sur HDInsight dans vos applications." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="overview-apache-spark-on-hdinsight-linux"></a>Vue d’ensemble : Allumage commandé de Apache sur HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Allumage de Apache</a> est un framework qui prend en charge le traitement en mémoire pour augmenter les performances des applications analytiques de données volumineuses de traitement en parallèle open source. Moteur de traitement et est conçu pour la vitesse, facilité d’utilisation et analytique sophistiquées. En mémoire calcul fonctionnalités d’allumage rendre un bon choix pour les algorithmes itératifs dans les calculs de formation et graphique de machine. Allumage est également compatible avec le stockage Azure Blob (WASB) afin que vos données existantes stockées dans Azure peuvent facilement être traitées via l’allumage.

Lorsque vous créez un cluster d’étincelle dans HDInsight, vous créez des ressources de calcul Azure avec allumage installé et configuré. Il ne prend environ dix minutes pour créer un cluster d’étincelle dans HDInsight. Les données à traiter sont stockées dans le stockage des objets Blob Azure. Consultez [utilisation du stockage Blob Azure avec HDInsight][hdinsight-storage].

![Allumage Apache sur Azure HDInsight] (./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Allumage Apache sur Azure HDInsight")


**Vous souhaitez commencer avec allumage Apache sur Azure HDInsight ?** Consultez [QuickStart : créer un cluster à allumage sur HDInsight Linux et exécuter les exemples d’applications à l’aide de Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Pour obtenir une liste des problèmes connus et limitations de la version actuelle, reportez-vous à la section [problèmes d’allumage Apache dans Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="why-use-spark-on-azure-hdinsight"></a>Pourquoi utiliser étincelle sur Azure HDInsight ? 

HDInsight Azure propose un service d’étincelle entièrement géré. Avantages de l’utilisation d’allumage sur HDInsight sont les suivantes :

| Fonctionnalité                             | Description       |
|-------------------------------------|-------------------|
| Facilité de création de clusters            | Vous pouvez créer un nouveau cluster d’allumage sur HDInsight en quelques minutes à l’aide du portail de gestion Azure, Azure PowerShell ou le Kit de développement .NET HDInsight. Reportez-vous à la section [mise en route de cluster d’étincelle dans HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilité d’utilisation                     | Allumage dans les clusters de HDInsight inclut des portables Jupyter préconfigurés. Vous pouvez les utiliser pour le traitement de données interactive et de visualisation. L’URL pour l’est https://CLUSTERNAME.azurehdinsight.net/jupyter. Remplacez __NOM_CLUSTER__ avec le nom de votre cluster d’allumage HDInsight.|
| AUTRES API                       | Allumage dans HDInsight inclut [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), une API REST-en fonction de serveur de travaux d’allumage pour soumettre et contrôler les tâches en cours d’exécution à distance. |
| Prise en charge de la banque de LAC de données Azure | Explosion de HDInsight peut être configurée pour utiliser un magasin de LAC de données Azure comme un espace de stockage supplémentaire. Pour plus d’informations sur le magasin de données lac, consultez [Vue d’ensemble du magasin de LAC des données Azure](../data-lake-store/data-lake-store-overview.md).
| Intégration avec les services Azure | Explosion de HDInsight est fourni avec un connecteur à des concentrateurs d’événement Azure. Les clients peuvent créer des applications en continu à l’aide de l’événement concentrateurs, en plus de [Kafka](http://kafka.apache.org/), qui est déjà disponible dans le cadre de l’explosion. |
| Prise en charge de serveur de R  | Vous pouvez configurer un serveur R sur cluster de HDInsight Spark pour exécuter des calculs de R distribuées avec les vitesses confirmées avec un cluster d’allumage. Pour plus d’informations, consultez [mise en route à l’aide du serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md).   |
| Intégration avec l’idée d’IntelliJ  | Vous pouvez utiliser le HDInsight Plugin pour IntelliJ pour créer et soumettre des applications sur un cluster HDInsight Spark. Pour plus d’informations, consultez [Utilisation HDInsight outils plug-in pour idée IntelliJ créer des applications de cluster Linux d’allumage HDInsight d’allumage](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Demandes simultanées              | Allumage dans HDInsight prend en charge les requêtes simultanées. Cela permet à plusieurs requêtes à partir d’un seul utilisateur ou plusieurs requêtes à partir de différents utilisateurs et applications de partager les mêmes ressources de cluster. |
| La mise en cache sur SSDs                 | Vous pouvez choisir en cache les données en mémoire ou dans SSDs liés à des nœuds de cluster. La mise en cache dans la mémoire offre les meilleures performances de la requête, mais peut être coûteux ; la mise en cache dans SSDs fournit une option intéressante pour améliorer les performances de requête sans avoir besoin de créer un cluster dont la taille est nécessaire pour faire tenir l’ensemble du dataset en mémoire.|
| Intégration avec les outils d’analyse Décisionnelle       | Allumage pour HDInsight fournit des connecteurs pour outils de BI par exemple [Alimentation BI](http://www.powerbi.com/) et le [Tableau](http://www.tableau.com/products/desktop) pour l’analytique des données.|
| Bibliothèques Anaconda préalablement chargés        | Érosion clusters sur HDInsight sont fournis avec les bibliothèques Anaconda préinstallés. [Anaconda](http://docs.continuum.io/anaconda/) fournit près de 200 bibliothèques pour l’apprentissage automatique, analyse des données, visualisation, etc..|
| Évolutivité                     | Vous pouvez spécifier le nombre de nœuds dans votre cluster lors de la création, vous souhaiterez peut-être agrandir ou réduire le cluster afin de correspondre à la charge de travail. Tous les clusters HDInsight permettent de modifier le nombre de nœuds dans le cluster. Également, les clusters d’allumage peuvent être supprimés sans aucune perte de données puisque toutes les données sont stockées dans le stockage Blob Azure. |
| La prise en charge 24 x 7                    | Allumage de HDInsight est fourni avec la prise en charge au niveau de l’entreprise 24 x 7 et d’un contrat SLA de durée 99,9 %.|



## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quels sont les cas d’usage pour l’allumage sur HDInsight ?

Allumage Apache dans HDInsight permet les scénarios suivants.

### <a name="interactive-data-analysis-and-bi"></a>Analyse interactive des données et la BI

[Consulter un didacticiel](hdinsight-apache-spark-use-bi-tools.md)

Allumage Apache dans HDInsight stocke les données dans les BLOB Azure. Les experts professionnels et principaux décideurs peuvent analyser et générer des rapports sur ces données et BI de puissance de Microsoft permet de générer des rapports interactifs à partir des données analysées. Les analystes peuvent démarrer à partir des données non structurées/semi structuré dans le stockage Azure, définir un schéma pour les données à l’aide d’ordinateurs portables et puis créez les modèles de données à l’aide de la puissance de Microsoft BI. Allumage dans HDInsight prend également en charge un certain nombre d’outils tiers BI comme Tableau, Qlikview et Lumira de SAP en une plate-forme idéale pour les analystes de données, les experts professionnels et principaux décideurs.

### <a name="iterative-machine-learning"></a>Apprentissage automatique itératif

[Consulter un didacticiel : prévoir création de températures en utilisant les données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Consulter un didacticiel : prédire les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Allumage de Apache est fourni avec [MLlib](http://spark.apache.org/mllib/), une machine d’apprentissage basé sur l’allumage. En outre, allumage sur HDInsight inclut également Anaconda, une distribution Python avec une variété de packages pour l’apprentissage de l’ordinateur. Ajoutez à cela une prise en charge intégrée pour les PC portables de Jupyter, et que vous disposez d’un environnement haut-de-gamme pour créer des applications de formation de machine.  

### <a name="streaming-and-real-time-data-analysis"></a>Analyse de données de transmission en continu et en temps réel

[Consulter un didacticiel](hdinsight-apache-spark-eventhub-streaming.md)

Analyse des données en temps réel est utilisé pour les scénarios allant réduisant le temps d’analyse de données de traitement des données qu’il arrive, à la création d’une véritable solution de diffusion en continu. Explosion de HDInsight offre une prise en charge complète pour la création de solutions d’analytique en temps réel. Allumage a déjà des connecteurs pour recevoir des données provenant de nombreuses sources telles que les sockets Kafka, Flume, Twitter, ZeroMQ ou TCP, étincelle dans HDInsight ajoute la prise en charge exhaustive pour traiter les données de concentrateurs d’événement Azure. Concentrateurs d’événement sont le service de file d’attente la plus largement utilisé sur Azure. Ayant une prise en charge de-l’emploi pour les concentrateurs d’événements permet au service de HDInsight une plate-forme idéale pour la création du pipeline d’analytique en temps réel.

##<a name="next-steps"></a>Les composants sont inclus dans le cadre d’un cluster d’allumage ?

Explosion de HDInsight inclut les composants suivants sont disponibles sur les clusters par défaut.

- [Base d’allumage](https://spark.apache.org/docs/1.5.1/). Inclut étincelle Core, allumage SQL, allumage flux API, GraphX et MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Jupyter portable](https://jupyter.org)

Allumage de HDInsight fournit également un [pilote ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) pour la connectivité aux clusters d’étincelle dans HDInsight à partir d’outils BI tels que Microsoft BI de puissance et de Tableau.

## <a name="where-do-i-start"></a>Par où commencer ?

Commencer par la création d’un cluster d’allumage sur HDInsight Linux. Consultez [QuickStart : créer un cluster à allumage sur HDInsight Linux et exécuter les exemples d’applications à l’aide de Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Étapes suivantes

### <a name="scenarios"></a>Scénarios

* [Allumage avec BI : effectuer l’analyse interactive des données à l’aide d’étincelle dans HDInsight avec les outils d’analyse Décisionnelle](hdinsight-apache-spark-use-bi-tools.md)

* [Allumage avec apprentissage automatique : allumage d’utilisation dans les HDInsight d’analyse de température de construction à l’aide des données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Allumage avec apprentissage automatique : allumage utilisation de HDInsight pour prédire les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu de l’allumage : Allumage d’utilisation dans HDInsight pour générer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’étincelle dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications

* [Créez une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécuter des tâches à distance sur un cluster d’allumage à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Plug-in d’outils HDInsight idée de IntelliJ permet de créer et soumettre des applications d’allumage Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Plug-in d’outils HDInsight IntelliJ idée permet de déboguer des applications d’allumage commandé à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilisez les portables Zeppelin avec un cluster d’allumage sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le cluster d’allumage pour HDInsight ordinateur portable Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilisez les lots externes avec les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installez Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache étincelle dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tâches de suivi et de débogage en cours d’exécution sur un cluster Apache étincelle dans HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
