<properties
    pageTitle="Soumettre des travaux d’allumage commandé à distance à l’aide de Livy | Microsoft Azure"
    description="Apprenez à utiliser des Livy avec des clusters de HDInsight d’envoyer des travaux d’allumage à distance."
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


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Soumettre des travaux d’allumage commandé à distance à un cluster d’allumage d’Apache sous HDInsight Linux à l’aide de Livy

Cluster d’allumage d’Apache sur Azure HDInsight inclut Livy, une interface REST pour soumettre des tâches à distance à un cluster d’allumage. Pour une documentation détaillée, consultez [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Vous pouvez utiliser Livy pour exécuter étincelle interactive de coques ou de soumettre des traitements par lots à exécuter à l’allumage. Cet article traite de l’à l’aide de Livy à soumettre des tâches de traitement par lots. La syntaxe ci-dessous utilise le roulage pour effectuer des appels reste le point de terminaison Livy.

**Conditions préalables :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster d’allumage d’Apache sur HDInsight Linux. Pour obtenir des instructions, consultez [créer un allumage Apache des clusters dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job-the-cluster"></a>Soumettre une tâche de traitement par lots du cluster

Avant de vous soumettez à un traitement par lots, vous devez télécharger le fichier jar d’application sur le stockage de cluster associé au cluster. Vous pouvez utiliser [**AzCopy**](../storage/storage-use-azcopy.md), un utilitaire de ligne de commande, au pour faire. Il existe beaucoup d’autres clients, que vous pouvez utiliser pour transférer des données. Pour en savoir plus à leur sujet à [télécharger des données pour les travaux d’Hadoop dans HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemples**:

* Si le fichier jar est le stockage du cluster (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si vous souhaitez transmettre le nom de fichier jar et la classe dans le cadre d’un fichier d’entrée (dans cet exemple, input.txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Obtenir des informations sur les lots en cours d’exécution sur le cluster

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemples**:

* Si vous souhaitez récupérer tous les lots en cours d’exécution sur le cluster :

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si vous souhaitez récupérer un lot spécifique avec un batchId donné

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>Supprimer un traitement par lots

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemple**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livy et haute disponibilité

Livy fournit à haute disponibilité pour l’allumage travaux s’exécutant sur le cluster. Voici quelques exemples.

* Si le service Livy tombe en panne après que vous avez soumis un travail à distance à un cluster d’allumage, la tâche continue de s’exécuter en arrière-plan. Lorsqu’il est Livy sauvegarder, il restaure l’état des travaux et des rapports de nouveau.

* Livy sont équipés de portables de Jupyter pour HDInsight dans le système principal. Si un ordinateur portable exécute un travail d’allumage et le service Livy obtient redémarré, l’ordinateur portable continue d’exécuter les cellules de code. 

## <a name="show-me-an-example"></a>Afficher un exemple

Dans cette section, nous examinons exemples sur l’utilisation de Livy à présenter une demande d’allumage et surveiller la progression de l’application puis supprimer le travail. L’application que nous utilisons dans cet exemple est celui développé dans l’article [créer un Scala application autonome et à s’exécuter sur un cluster d’allumage de HDInsight](hdinsight-apache-spark-create-standalone-application.md). Les étapes suivantes supposent que les éléments suivants :

* Vous avez déjà copiées sur le fichier jar d’application pour le compte de stockage associé au cluster.
* Vous avez CuRL installé sur l’ordinateur que vous essayez de ces étapes.

Effectuez les opérations suivantes.

1. Nous Vérifiez d’abord que Livy est en cours d’exécution sur le cluster. Pour ce faire, nous pouvons obtenir une liste des lots en cours d’exécution. Si c’est la première fois que vous exécutez une tâche à l’aide de Livy, cela doit retourner zéro.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Vous devez obtenir une sortie semblable à la suivante :

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Notez comment la dernière ligne dans la sortie indique le **total : 0**, ce qui ne suggère aucuns lots en cours d’exécution.

2. Nous à présent envoyer un traitement par lots. L’extrait de code ci-dessous utilise un fichier d’entrée (input.txt) pour transmettre le nom de fichier jar et le nom de classe en tant que paramètres. Il s’agit de l’approche recommandée si vous exécutez ces étapes à partir d’un ordinateur Windows.

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Les paramètres dans fichier **input.txt** sont définis comme suit :

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    Vous devriez voir une sortie semblable à la suivante :

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Notez comment la dernière ligne de la sortie indique **état : démarrage de**. Il stipule aussi, **id : 0**. Il s’agit de l’ID de lot.

3. Vous pouvez maintenant récupérer la l’état de ce lot spécifique à l’aide de l’ID de lot.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Vous devriez voir une sortie semblable à la suivante :

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    La sortie affiche maintenant **: réussite de l’état**, qui indique que la tâche a réussi.

4. Si vous le souhaitez, vous pouvez maintenant supprimer le lot.

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Vous devriez voir une sortie semblable à la suivante :

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    La dernière ligne de la sortie montre que le lot a été supprimé. Si vous supprimez une tâche en cours d’exécution, il va arrêter essentiellement de la tâche. Si vous supprimez un travail a terminé, avec succès ou autrement, il supprime complètement les informations du projet.

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
