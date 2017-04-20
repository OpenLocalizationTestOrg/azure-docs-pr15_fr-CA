 <properties
    pageTitle="Introduction à Hadoop - Nouveautés Hadoop sur HDInsight ? | Microsoft Azure"
    description="Découvrez une présentation d’Hadoop, traitement de données volumineux distribué et analyse et les composants de l’écosystème Hadoop dans le nuage sur HDInsight."
    keywords="analyse de données volumineuses, introduction à hadoop, nouveautés hadoop, pile de technologie hadoop, hadoop écosystème"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Une introduction à l’écosystème Hadoop sur Azure HDInsight

 Cet article fournit une introduction à Hadoop sur Azure HDInsight, son écosystème et des données volumineuses. Obtenir des informations sur les composants Hadoop, terminologie commune et les scénarios d’analyse de données volumineuses.

## <a name="what-is-hadoop-on-hdinsight"></a>Nouveautés d’Hadoop sur HDInsight ?

Hadoop fait référence à un écosystème de logiciels open source qui est une infrastructure de traitement distribué, le stockage et l’analyse des grands ensembles de données sur les clusters d’ordinateurs. HDInsight Azure rend les composants Hadoop de la distribution de la **Plate-forme de données Hortonworks (HDP)** disponible dans le nuage et déploie et met en clusters gérés avec une plus grande fiabilité et la disponibilité.  

Apache Hadoop a été le projet open source d’origine pour le traitement de données volumineux. Suivant a été le développement de logiciels et utilitaires considérés comme faisant partie de la pile de technologie Hadoop, y compris la ruche Apache, Apache HBase, Apache étincelle et bien d’autres. Pour plus d’informations, consultez [vue d’ensemble de l’écosystème Hadoop dans HDInsight](#overview) .

## <a name="what-is-big-data"></a>Quelles sont les données big ?

Données volumineuses décrivent tout organisme volumineux d’informations numériques, à partir du texte dans un Twitter de l’alimentation, les informations de capteur à partir d’équipements industriels, aux informations sur le client de navigation et les achats sur un site Web. Données volumineuses peuvent être historiques (c'est-à-dire les données stockées) ou en temps réel (c'est-à-dire transmis directement à partir de la source). Données volumineuses sont collectées dans l’évolution des volumes, à des vitesses de plus en plus élevés et dans une extension de différents formats.

Pour les données volumineuses à fournir des processus intelligents ou insight, vous devez collecter des données pertinentes et posez les bonnes questions. Vous devez également vous que les données sont accessibles, nettoyés, analysées et présentés de manière pratique. C’est là une analyse de données volumineuses sur Hadoop dans HDInsight peut vous aider.

## <a name="overview"></a>Vue d’ensemble de l’écosystème Hadoop dans HDInsight

HDInsight est une distribution de nuage de Microsoft Azure de la pile de technologie Apache Hadoop expansion rapide pour l’analyse de données volumineuses. Elle comprend des implémentations de l’allumage d’Apache, HBase, tempête, porc, ruche, Sqoop, Oozie, Ambari et ainsi de suite. HDInsight s’intègre également avec les outils d’analyse décisionnelle (BI) comme alimentation BI, Excel, SQL Server Analysis Services et SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, étincelle, tempête et clusters personnalisés

HDInsight fournit des configurations de cluster pour Apache Hadoop, étincelle, HBase ou tempête. Ou bien, vous pouvez [Personnaliser les clusters avec les actions de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: fournit un stockage fiable des données [très](#hdfs)et un modèle de programmation simple [MapReduce](#mapreduce) à traiter et analyser des données en parallèle.

* **<a target="_blank" href="http://spark.apache.org/">Allumage de Apache</a>**: une infrastructure de traitement parallèle qui prend en charge le traitement en mémoire pour augmenter les performances des applications d’analyse de données volumineuses et works pour SQL, diffusion en continu de données et machine d’apprentissage. Reportez-vous à la section [présentation : nouveautés étincelle Apache dans HDInsight ?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: une base de données NoSQL basé sur Hadoop qui fournit un accès aléatoire et forte cohérence pour de grandes quantités de données non structurées et semi-structurées, potentiellement des milliards de lignes fois millions de colonnes. Consultez [vue d’ensemble de HBase sur HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: un système de calcul distribué, en temps réel pour le traitement rapide de grands flux de données. Tempête est proposée comme un cluster managé dans HDInsight. Voir [analyse des données de capteur en temps réel à l’aide de la tempête et Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Exemples de scripts de personnalisation

Actions de script sont des scripts qui s’exécutent lors de la configuration du cluster et peuvent être utilisés pour installer des composants supplémentaires sur le cluster. Pour les clusters sous Linux, ce sont les scripts Bash.

Les exemples de scripts suivants sont fournis par l’équipe de HDInsight :

* [Teinte](hdinsight-hadoop-hue-linux.md): ensemble d’applications web permettant d’interagir avec un cluster. Clusters Linux uniquement.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): traitement pour modéliser des relations entre les personnes ou les éléments de graphique.

* [R](hdinsight-hadoop-r-scripts-linux.md): un environnement pour le calcul de statistiques utilisées dans l’apprentissage automatique et le langage de l’open source.

* [Mode série sur LAN.r](hdinsight-hadoop-solr-install-linux.md): une plate-forme de recherche à l’échelle de l’entreprise qui permet la recherche de texte intégral sur des données.

Pour plus d’informations sur le développement de vos propres Actions de Script, consultez [développement de Script Action avec HDInsight](hdinsight-hadoop-script-actions-linux.md).


## <a name="what-are-the-hadoop-components-and-utilities"></a>Quels sont les utilitaires et les composants d’Hadoop ?

Les composants et les utilitaires suivants sont inclus dans les clusters HDInsight.

* **[Ambari](#ambari)**: mise en service, gestion, surveillance et utilitaires de Cluster.

* **[Avro](#avro)** (Bibliothèque Microsoft .NET pour Avro) : sérialisation des données pour l’environnement Microsoft .NET.

* **[Ruche & HCatalog](#hive)**: du langage SQL (SQL)-telles que l’interrogation et une couche de gestion de stockage et de la table.

* **[Mahout](#mahout)**: applications de formation pour machine évolutive.

* **[MapReduce](#mapreduce)**: framework hérité d’Hadoop distribué de gestion des ressources et de traitement. Voir les [fils](#yarn), la structure des ressources de nouvelle génération.

* **[Oozie](#oozie)**: gestion de Workflow.

* **[Phoenix](#phoenix)**: couche de base de données relationnelle sur HBase.

* **[Porcs](#pig)**: script plus simple pour les transformations de MapReduce.

* **[Sqoop](#sqoop)**: importation et exportation de données.

* **[Tez](#tez)**: permet aux processus consommatrices de données pour s’exécuter efficacement à grande échelle.

* **[Fils](#yarn)**: partie de la bibliothèque principale de Hadoop et la nouvelle génération de l’infrastructure logicielle MapReduce.

* **[Soigneur](#zookeeper)**: Coordination des processus de systèmes distribués.

> [AZURE.NOTE] Pour des informations sur les composants spécifiques et les informations de version, voir [composants d’Hadoop, du versioning et offres de services de HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari est pour la mise en service, la gestion et la surveillance des clusters d’Apache Hadoop. Il inclut un ensemble intuitif d’outils de l’opérateur et un solide ensemble d’API qui masque la complexité de Hadoop, simplifiant l’opération des clusters. HDInsight basé sur Linux clusters offrent à la fois le Ambari web interface utilisateur et l’API REST Ambari, bien que les clusters basés sur Windows offrent un sous-ensemble de l’API REST. Vues de Ambari sur des clusters de HDInsight permettent de fonctionnalités d’interface utilisateur plug-in.

Voir [HDInsight de gérer les clusters à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md) (Linux uniquement), les [clusters de moniteur Hadoop dans HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md)et <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">référence de l’API de Ambari d’Apache</a>.

### <a name="avro"></a>Avro (bibliothèque Microsoft .NET pour Avro)

La bibliothèque Microsoft .NET pour Avro implémente le format d’échange des données binaires compact Apache Avro pour la sérialisation de l’environnement Microsoft .NET. Il utilise la <a target="_blank" href="http://www.json.org/">Notation JSON (JavaScript Object)</a> pour définir un schéma indépendant du langage qui couvre l’interopérabilité des langages, ce qui signifie des données sérialisées dans une langue peuvent être lus dans un autre. Vous trouverez des informations détaillées sur le format de le < cible = _ des href « vide » = « http://avro.apache.org/docs/current/spec.html » > Apache Avro spécification</a>.
Le format des fichiers de Avro prend en charge le modèle de programmation distribué MapReduce. Les fichiers sont « séparables », ce qui signifie vous pouvez rechercher n’importe quel point dans un fichier et le démarrage de la lecture à partir d’un bloc particulier. Pour savoir comment procéder, consultez [sérialisation des données avec la bibliothèque Microsoft .NET pour Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>TRÈS

Hadoop distribuées fichier système très est un système de fichiers distribués qui, MapReduce et fils, constitue le cœur de l’écosystème Hadoop. TRÈS est le système de fichiers standard pour les clusters Hadoop sur HDInsight.

### <a name="hive"></a>Ruche & HCatalog

<a target="_blank" href="http://hive.apache.org/">La ruche Apache</a> est un logiciel d’entrepôt de données basé sur Hadoop qui vous permet d’interroger et de gérer de grands ensembles de données dans le stockage distribué à l’aide d’un langage de type SQL appelé HiveQL. Ruche, comme les porcins, est une abstraction MapReduce. Lors de l’exécution, ruche traduit des requêtes dans une série de tâches de MapReduce. Ruche est conceptuellement plus proche à un système de gestion de base de données relationnelle que les porcs et est par conséquent approprié pour une utilisation avec des données plus structurées. Pour les données non structurées, porc est le meilleur choix. Voir [utiliser la ruche avec Hadoop dans HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> est une couche de gestion de stockage et de la table d’Hadoop qui offre aux utilisateurs une vue relationnelle des données. Dans HCatalog, vous pouvez lire et écrire des fichiers dans n’importe quel format pour lesquels un SerDe la ruche (sérialiseur-désérialiseur) peut être écrite.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> est une bibliothèque évolutive de l’apprentissage des algorithmes qui s’exécutent sur Hadoop de l’ordinateur. À l’aide des principes applicables aux statistiques, les applications de formation machine enseignent systèmes apprendre à partir des données et permet de déterminer le comportement futur au-delà de résultats. Voir [les recommandations de film générer à l’aide de Mahout sur Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce est l’infrastructure de logiciel hérité d’Hadoop pour écrire des applications pour une sélection de jeux de données volumineux processus en parallèle. Un travail MapReduce fractionne les grands ensembles de données et organise les données dans les paires clé-valeur pour traitement.

[Fils](#yarn) est l’infrastructure de gestionnaire et application de nouvelle génération ressource Hadoop et est appelé MapReduce 2.0. MapReduce travaux s’exécutent sur les fils.

Pour plus d’informations sur MapReduce, consultez <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> dans le Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> est un système de coordination de flux de travail qui gère les travaux d’Hadoop. Il est intégré à la pile d’Hadoop et prend en charge les travaux Hadoop pour MapReduce, porc, la ruche et Sqoop. Il peut également être utilisé pour planifier des tâches spécifiques à un système, tels que les programmes Java ou des scripts de shell. Reportez-vous à la section [Oozie utilisation avec Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> est une couche de base de données relationnelle sur HBase. Phoenix comprend un pilote JDBC qui permet aux utilisateurs d’interroger et de gérer les tables SQL directement. Phoenix traduit les requêtes et les autres instructions dans des appels d’API de NoSQL - et non à l’aide de MapReduce - permettant ainsi des applications plus rapides sur des magasins de NoSQL. Reportez-vous à la section [utilisation Apache Phoenix et non avec des clusters de HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Porcs
<a  target="_blank" href="http://pig.apache.org/">Apache porc</a> est une plate-forme de haut niveau qui vous permet d’effectuer des transformations de MapReduce complexes sur très grands ensembles de données à l’aide d’un langage de script simple appelé Latin de porc. Porcs traduit les scripts Latin de porc afin qu’elles s’exécutent au sein d’Hadoop. Vous pouvez créer des fonctions de défini par l’utilisateur (UDF) pour étendre le porc Latin. Reportez-vous à la section [utilisation des porcs avec Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> est un outil que les transferts en bloc des données entre Hadoop et bases de données relationnelles tel un SQL ou autres magasins de données structurées, aussi efficacement que possible. Reportez-vous à la section [Sqoop utilisation avec Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> est une infrastructure d’application basée sur les fils d’Hadoop qui exécute des graphiques complexes, acycliques général du traitement des données. Il est plus flexible et plus puissante à l’infrastructure de MapReduce qui permet des processus utilisant énormément de données, tels que de la ruche, peuvent fonctionner plus efficacement à l’échelle. Reportez-vous à la section [« Utilisation des Tez de Apache pour améliorer les performances » dans Utilisation de la ruche et HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>FILS
FILS de Apache est la nouvelle génération de MapReduce (MapReduce 2.0 ou MRv2) et prend en charge les scénarios de traitement de données au-delà de MapReduce traitement par lots avec une plus grande évolutivité et traitement en temps réel. FIL fournit la gestion des ressources et une infrastructure d’applications distribuées. MapReduce travaux s’exécutent sur les fils.

Pour en savoir plus sur les fils, consultez <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (fils)</a>.


### <a name="zookeeper"></a>Soigneur
<a  target="_blank" href="http://zookeeper.apache.org/">Apache soigneur</a> coordonne des processus dans les grands systèmes distribués au moyen de l’espace de noms hiérarchique partagé des registres de données (znodes). Znodes contenir de petites quantités d’informations de métadonnées nécessaire pour coordonner le processus : état, emplacement, configuration et ainsi de suite.

## <a name="programming-languages-on-hdinsight"></a>Langages de programmation sur HDInsight

Compte tenu des clusters d’HBase, tempête et érosion clusters--Hadoop, HDInsight--prennent en charge un certain nombre de langages de programmation, mais certains ne sont pas installés par défaut. Pour les bibliothèques de modules ou packages non installés par défaut, utilisent une action de script pour installer le composant. Reportez-vous à la section [développement d’action de Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Valeur par défaut de la prise en charge du langage de programmation

Par défaut, la prise en charge des clusters de HDInsight :

* Java

* Python

Des langues supplémentaires peuvent être installés à l’aide des actions de script : [développement d’action de Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Langages de Java virtual machine (JVM)

De nombreux langages autres que Java peuvent être exécutées à l’aide d’une machine virtuelle de Java (JVM) ; Toutefois, certains de ces langues peut nécessiter des composants supplémentaires installés sur le cluster.

Ces langages de JVM sont pris en charge sur les clusters de HDInsight :

* Clojure

* Jython (les Python pour Java)

* Scala

### <a name="hadoop-specific-languages"></a>Langages spécifiques Hadoop

HDInsight clusters prennent en charge les langues suivantes sont spécifiques à l’écosystème Hadoop :

* Latin de porc pour les travaux de porc

* HiveQL pour les tâches de la ruche et de SparkSQL


## <a name="advantage"></a>Avantages d’Hadoop dans le nuage

Dans le cadre de l’écosystème Azure cloud, Hadoop dans HDInsight offre de nombreux avantages, parmi lesquels :

* La configuration automatique d’Hadoop clusters. HDInsight clusters sont plus faciles à créer que de configurer manuellement des clusters d’Hadoop. Pour plus d’informations, voir [clusters de fourniture Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Composants d’Hadoop-de-la-pointe. Pour plus d’informations, consultez [composants d’Hadoop, du versioning et offres de services de HDInsight][component-versioning].

* Haute disponibilité et la fiabilité des clusters. Pour plus d’informations, reportez-vous à la [disponibilité et la fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md) .

* Stockage de données efficace et économique avec le stockage Azure Blob, une option Hadoop compatible. Pour plus d’informations, consultez [stockage Blob Azure d’utilisation avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md) .

* Intégration avec d’autres services, y compris les [applications Web](https://azure.microsoft.com/documentation/services/app-service/web/) et [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)Azure.

* Autres tailles de machine virtuelle et les types de HDInsight les clusters en cours d’exécution. Consultez [Hadoop composants, versioning et offres de services dans les HDInsight] [ component-versioning] pour plus de détails.

* Mise à l’échelle du cluster. Mise à l’échelle du cluster vous permet de modifier le nombre de nœuds d’un cluster de HDInsight en cours d’exécution sans avoir à supprimer ou de le recréer.

* La prise en charge de réseau virtuelle. HDInsight clusters peuvent servir pour prendre en charge de l’isolation de scénarios hybride qui lient les ressources de cloud avec celles de votre centre de données ou de ressources de cloud avec le réseau virtuel d’Azure.

* Entrée de faible coût. Démarrer une [version d’évaluation gratuite](https://azure.microsoft.com/free/), ou consulter [les détails de tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pour en savoir plus sur les avantages de Hadoop dans HDInsight, reportez-vous à la [page fonctionnalités Azure pour HDInsight]de[marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard et Premium de HDInsight

HDInsight propose des offres de nuage de données volumineuses dans deux catégories, Standard et Premium. HDInsight Standard fournit un cluster à l’échelle de l’entreprise que les organisations peuvent utiliser pour exécuter leurs charges de travail de données volumineuses. HDInsight prime qui reprend et offrent avancée analytique et les fonctions de sécurité pour un cluster de HDInsight. Pour plus d’informations, consultez [Azure HDInsight prime](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Ressources pour en savoir plus sur l’analyse des données volumineuses, Hadoop et HDInsight

Construire sur cette introduction à Hadoop dans le nuage et l’analyse de données avec les ressources ci-dessous.


### <a name="hadoop-documentation-for-hdinsight"></a>Documentation d’Hadoop pour HDInsight

* [Documentation de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): la page de documentation d’Hadoop sur Azure HDInsight avec des liens vers des articles, des vidéos et des ressources supplémentaires.

* [Mise en route de Hadoop dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): un didacticiel de démarrage rapide pour HDInsight Hadoop clusters de mise en service et l’exécution des exemples de requêtes de ruche.

* [Mise en route de l’allumage dans HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): un didacticiel de démarrage rapide pour la création d’un cluster d’allumage et exécute des requêtes SQL d’allumage interactives.

* [Utiliser le serveur de HDInsight R](hdinsight-hadoop-r-server-get-started.md): commencer à utiliser le serveur de R HDInsight Premium.

* [Disposition HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md): Apprenez à configurer un cluster HDInsight Hadoop sur Azure portal, Azure CLI ou Azure PowerShell.


### <a name="apache-hadoop"></a>Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: en savoir plus sur la librairie de logiciels Apache Hadoop, un cadre qui permet le traitement distribué de grands ensembles de données sur les clusters d’ordinateurs.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">Très</a>: en savoir plus sur l’architecture et la conception d’Hadoop DFS, le système de stockage principal utilisé par les applications d’Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce didacticiel</a>: en savoir plus sur l’infrastructure de programmation pour l’écriture d’applications Hadoop traitent rapidement d’importants volumes de données en parallèle sur des clusters de nœuds de calcul de grande taille.


### <a name="microsoft-business-intelligence"></a>Business intelligence de Microsoft

Outils d’analyse décisionnelle (BI) familier business - comme Excel, PowerPivot, SQL Server Analysis Services et SQL Server Reporting Services - récupérer, analyser et signalent des données intégrées à HDInsight en utilisant le complément de requête de l’alimentation ou le pilote ODBC de Microsoft de la ruche.

Ces outils d’analyse Décisionnelle peuvent aider dans l’analyse de données volumineuses :

* [Hadoop avec la requête de la puissance d’Excel se connecter](hdinsight-connect-excel-power-query.md): Apprenez à Excel de se connecter sur le compte de stockage Azure qui stocke les données associées à votre cluster HDInsight à l’aide de la puissance de Microsoft Query pour Excel. Station de travail Windows requise. Fonctionne avec Windows ou Linux basés sur le cluster.

* [Excel de se connecter à Hadoop avec le pilote ODBC de Microsoft la ruche](hdinsight-connect-excel-hive-odbc-driver.md): Apprenez à importer des données à partir de HDInsight avec le pilote ODBC de Microsoft de la ruche. Station de travail Windows requise. Fonctionne avec Windows ou Linux basés sur le cluster.

* [Plate-forme de nuage de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): en savoir plus sur alimentation BI pour Office 365, téléchargez la version d’évaluation de SQL Server et configurer SharePoint Server 2013 et BI de SQL Server.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
