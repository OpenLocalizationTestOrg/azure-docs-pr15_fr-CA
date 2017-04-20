<properties 
    pageTitle="Utiliser les portables Zeppelin avec cluster d’allumage sous Linux de HDInsight | Microsoft Azure" 
    description="Instructions détaillées sur l’utilisation des ordinateurs portables de Zeppelin avec les clusters d’allumage sur HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-hdinsight-linux"></a>Utilisez les portables Zeppelin avec cluster Apache étincelle sur HDInsight Linux

HDInsight Spark clusters incluent des ordinateurs portables Zeppelin que vous pouvez utiliser pour exécuter des tâches d’allumage. Dans cet article, vous allez apprendre à utiliser le bloc-notes Zeppelin sur un cluster HDInsight.


**Conditions préalables :**

* Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster d’allumage d’Apache. Pour obtenir des instructions, consultez [créer un allumage Apache des clusters dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Lancer un bloc-notes Zeppelin

1. De la lame de cluster étincelle, cliquez sur **Tableau de bord de Cluster**, puis cliquez sur **Bloc-notes de Zeppelin**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Zeppelin pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOM_CLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Créer un nouveau bloc-notes. Dans le volet d’en-tête, cliquez sur **bloc-notes**, puis cliquez sur **Créer une Note**.

    ![Créer un nouveau bloc-notes Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "Créer un nouveau bloc-notes Zeppelin")

    Entrez un nom pour l’ordinateur portable, puis cliquez sur **Créer une Note**.

3. Assurez-vous également que l’en-tête de l’ordinateur portable affiche un état connecté. Il est indiqué par un point vert dans le coin supérieur droit.

    ![État des blocs-notes Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "État des blocs-notes Zeppelin")

4. Charger des exemples de données dans une table temporaire. Lorsque vous créez un cluster d’étincelle dans HDInsight, l’exemple de fichier de données, **hvac.csv**, est copié dans le compte de stockage associé sous **\HdiSamples\SensorSampleData\hvac**.

    Dans le paragraphe vide qui est créé par défaut dans le bloc-notes, collez l’extrait de code suivant.

        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Appuyez sur **MAJ + ENTRÉE** ou cliquez sur le bouton **lecture** du paragraphe exécuter l’extrait de code. L’état dans le coin droit du paragraphe doit progresser de prêt, en attente, en cours d’exécution à terminé. La sortie s’affiche au bas du même paragraphe. La capture d’écran ressemble à ceci :

    ![Créer une table temporaire à partir des données brutes] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Créer une table temporaire à partir des données brutes")

    Vous pouvez également fournir un titre pour chaque paragraphe. À partir de la droite, cliquez sur l’icône de **paramètres** , puis cliquez sur **Afficher le titre**.

5. Vous pouvez maintenant exécuter les instructions SQL d’allumage de la table **hvac** . Collez la requête suivante dans un nouveau paragraphe. La requête récupère l’ID de bâtiment et de la différence entre la cible et les températures réelles pour chaque bâtiment à une date donnée. Appuyez sur **MAJ + ENTRÉE**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 

    L’instruction **sql de %** au début indique à l’ordinateur portable à utiliser l’interpréteur Livy Scala.

    La capture d’écran suivante affiche la sortie.

    ![Exécution d’une instruction SQL de l’explosion à l’aide de l’ordinateur portable] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Exécution d’une instruction SQL de l’explosion à l’aide de l’ordinateur portable")

     Cliquez sur les options d’affichage (mis en surbrillance dans le rectangle) pour basculer entre les représentations différentes pour la même sortie. Cliquez sur **paramètres** pour choisir quelles constitue la clé et les valeurs dans la sortie. La capture d’écran ci-dessus utilise **buildingID** comme clé et la moyenne de **temp_diff** comme valeur.

    
6. Vous pouvez également exécuter des instructions SQL d’allumage à l’aide de variables dans la requête. L’extrait de code suivant montre comment définir une variable **temporaire**, de la requête avec les valeurs possibles que vous souhaitez interroger avec. Lorsque vous exécutez la requête pour la première fois, une liste déroulante est remplie automatiquement avec les valeurs que vous avez spécifié pour la variable.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 

    Coller cet extrait de code dans un nouveau paragraphe et appuyez sur **MAJ + ENTRÉE**. La capture d’écran suivante affiche la sortie.

    ![Exécution d’une instruction SQL de l’explosion à l’aide de l’ordinateur portable] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Exécution d’une instruction SQL de l’explosion à l’aide de l’ordinateur portable")

    Pour les requêtes ultérieures, vous pouvez sélectionner une nouvelle valeur dans la liste déroulante et réexécutez la requête. Cliquez sur **paramètres** pour choisir quelles constitue la clé et les valeurs dans la sortie. La capture d’écran ci-dessus utilise **buildingID** comme la clé, la moyenne de **temp_diff** comme valeur et **targettemp** en tant que groupe.

7. Redémarrez l’interpréteur Livy pour quitter l’application. Pour ce faire, ouvrez les paramètres de l’interpréteur en cliquant sur le nom de l’utilisateur à partir du coin supérieur droit, puis cliquez sur **interpréteur**.

    ![Lancer l’interpréteur] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie de la ruche")

2. Accédez à paramètres d’interpréteur Livy, puis cliquez sur **redémarrer**.

    ![Redémarrez l’intepreter Livy] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Redémarrez l’intepreter Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Comment utiliser des lots externes avec le bloc-notes ?

Vous pouvez configurer le bloc-notes Zeppelin cluster Apache étincelle sur HDInsight (Linux) pour utiliser les lots externes, contribué à la Communauté qui ne sont pas inclus-de-l’emploi dans le cluster. Vous pouvez rechercher dans le [référentiel de Maven](http://search.maven.org/) pour la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles provenant d’autres sources. Par exemple, une liste complète des packages de contribution à la Communauté est disponible à [l’Allumage Packages](http://spark-packages.org/).

Dans cet article, vous allez apprendre à utiliser le package [d’allumage-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) avec le portable de Jupyter.

1. Ouvrir les paramètres de l’interpréteur. À partir du coin supérieur droit, cliquez sur le nom de l’utilisateur, puis cliquez sur **interpréteur**.

    ![Lancer l’interpréteur] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie de la ruche")

2. Accédez à paramètres d’interpréteur Livy, puis cliquez sur **Modifier**.

    ![Modifier les paramètres d’interpréteur] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Modifier les paramètres d’interpréteur")

3. Ajouter une nouvelle clé, appelée **livy.spark.jars.packages** et définir sa valeur dans le format `group:id:version`. Par conséquent, si vous souhaitez utiliser le package [d’allumage-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , vous devez définir la valeur de la clé à `com.databricks:spark-csv_2.10:1.4.0`.

    ![Modifier les paramètres d’interpréteur] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Modifier les paramètres d’interpréteur")

    Cliquez sur **Enregistrer** , puis redémarrez l’interpréteur Livy.

4. **Conseil**: Si vous souhaitez comprendre comment arriver à la valeur de la clé entrée ci-dessus, voici comment.

    une barre d’outils. Recherchez le package dans le référentiel Maven. Pour ce didacticiel, nous avons utilisé [allumage-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. À partir du référentiel, rassembler les valeurs pour le **GroupId**, **ArtifactId**et **Version**.

    ![Lots externes d’utilisation avec les ordinateurs portables de Jupyter] (./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Lots externes d’utilisation avec les ordinateurs portables de Jupyter")

    c. Concaténer les trois valeurs, séparés par deux-points (****).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Où sont enregistrés les portables Zeppelin ?

Les portables Zeppelin sont enregistrés dans le headnodes du cluster. Ainsi, si vous supprimez le cluster, les ordinateurs portables seront ainsi supprimés. Si vous souhaitez conserver vos blocs-notes pour une utilisation ultérieure sur les autres clusters, vous devez les exporter après avoir terminé les tâches en cours d’exécution. Pour exporter un bloc-notes, cliquez sur l’icône **Exporter** comme indiqué dans l’image ci-dessous.

![Télécharger le bloc-notes] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Télécharger le bloc-notes")

Cela enregistre le bloc-notes en tant que JSON fichier dans l’emplacement de téléchargement.

## <a name="livy-session-management"></a>Gestion de session Livy

Lorsque vous exécutez le premier paragraphe de code dans votre bloc-notes Zeppelin, une nouvelle session Livy est créée dans votre cluster HDInsight Spark. Cette session est partagée entre tous les ordinateurs portables Zeppelin que vous créez par la suite. Si pour une raison quelconque le Livy session est supprimé (redémarrage du cluster, etc.), vous ne serez pas en mesure d’exécuter des tâches à partir de l’ordinateur portable Zeppelin.

Dans ce cas, vous devez effectuer les étapes suivantes avant de commencer l’exécution des travaux à partir d’un ordinateur portable de Zeppelin. 

1. Redémarrez l’interpréteur Livy du bloc-notes Zeppelin. Pour ce faire, ouvrez les paramètres de l’interpréteur en cliquant sur le nom de l’utilisateur à partir du coin supérieur droit, puis cliquez sur **interpréteur**.

    ![Lancer l’interpréteur] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie de la ruche")

2. Accédez à paramètres d’interpréteur Livy, puis cliquez sur **redémarrer**.

    ![Redémarrez l’intepreter Livy] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Redémarrez l’intepreter Zeppelin")

3. Exécutez une cellule de code à partir d’un bloc-notes Zeppelin existant. Cela crée une nouvelle session Livy du cluster HDInsight.

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

* [Noyaux disponibles pour le cluster d’allumage pour HDInsight ordinateur portable Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilisez les lots externes avec les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installez Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache étincelle dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tâches de suivi et de débogage en cours d’exécution sur un cluster Apache étincelle dans HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 







