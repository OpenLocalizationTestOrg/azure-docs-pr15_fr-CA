<properties 
    pageTitle="Utiliser les lots externes avec les ordinateurs portables de Jupyter dans des clusters d’allumage d’Apache sur HDInsight | Azure"
    description="Instructions détaillées sur la configuration des ordinateurs portables Jupyter HDInsight Spark clusters pour utiliser les packages d’allumage externes." 
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
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="nitinme"/>


# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Utiliser les lots externes avec les ordinateurs portables de Jupyter dans des clusters d’allumage d’Apache sur HDInsight Linux

>[AZURE.NOTE] Cet article s’applique à l’allumage 1.5.2 sur HDInsight 3.3 et allumage 1.6.2 sur HDInsight 3.4. 

Apprenez à configurer un bloc-notes de Jupyter dans le cluster d’allumage d’Apache sur HDInsight (Linux) pour utiliser externe, des packages contribué à la Communauté qui ne sont pas inclus de-l’emploi dans le cluster. 

Vous pouvez rechercher dans le [référentiel de Maven](http://search.maven.org/) pour la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles provenant d’autres sources. Par exemple, une liste complète des packages de contribution à la Communauté est disponible à [l’Allumage Packages](http://spark-packages.org/).

Dans cet article, vous allez apprendre à utiliser le package [d’allumage-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) avec le portable de Jupyter.

##<a name="prerequisites"></a>Conditions préalables

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster d’allumage d’Apache sur HDInsight Linux. Pour obtenir des instructions, consultez [créer un allumage Apache des clusters dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilisez les lots externes avec les ordinateurs portables de Jupyter 

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la mosaïque pour votre cluster allumage (si vous l’épinglé à la startboard). Vous pouvez également accéder à votre cluster sous **Parcourir tous les** > **HDInsight Clusters**.   

2. À partir de la blade de cluster étincelle, cliquez sur les **Liens rapides**, puis cliquez sur **Bloc-notes de Jupyter**du **Tableau de bord de Cluster** serveur lame. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOM_CLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **allumage**.

    ![Créer un nouveau bloc-notes de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Créer un nouveau bloc-notes de Jupyter")

3. Un nouvel ordinateur portable est créé et ouverte avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure et entrez un nom convivial.

    ![Fournir un nom pour l’ordinateur portable] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour l’ordinateur portable")

4. Vous allez utiliser le `%%configure` magique pour configurer l’ordinateur portable pour utiliser un lot externe. Dans les ordinateurs portables qui utilisent les packages externes, veillez à appeler la `%%configure` magique dans la première cellule de code. Cela garantit que le noyau est configuré pour utiliser le package avant le démarrage de la session.

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    >[AZURE.IMPORTANT] Si vous oubliez de configurer le noyau dans la première cellule, vous pouvez utiliser le `%%configure` avec la `-f` paramètre, mais qui va redémarrer la session et de tous les cours seront perdues.

5. Dans l’extrait ci-dessus, `packages` attend la liste des coordonnées maven dans un référentiel Central de Maven. Dans cet extrait de code, `com.databricks:spark-csv_2.10:1.4.0` est la coordonnée maven package **d’allumage-csv** . Voici comment vous construisez les coordonnées d’un package.

    une barre d’outils. Recherchez le package dans le référentiel Maven. Pour ce didacticiel, nous utilisons [allumage-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. À partir du référentiel, rassembler les valeurs pour le **GroupId**, **ArtifactId**et **Version**.

    ![Lots externes d’utilisation avec les ordinateurs portables de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Lots externes d’utilisation avec les ordinateurs portables de Jupyter")

    c. Concaténer les trois valeurs, séparés par deux-points (****).

        com.databricks:spark-csv_2.10:1.4.0

6. Exécution de la cellule de code avec la `%%configure` magique. Cela configurera la session Livy sous-jacente pour utiliser le package que vous avez fournies. Dans les cellules suivantes dans le bloc-notes, vous pouvez maintenant utiliser le package, comme illustré ci-dessous.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. Vous pouvez ensuite exécuter les extraits de code, comme indiqué ci-dessous, pour afficher les données à partir de la dataframe que vous avez créé à l’étape précédente.

        df.show()

        df.select("Time").count()


## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Allumage commandé de Apache sur Azure HDInsight](hdinsight-apache-spark-overview.md)

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

* [Installez Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache étincelle dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tâches de suivi et de débogage en cours d’exécution sur un cluster Apache étincelle dans HDInsight](hdinsight-apache-spark-job-debugging.md)
