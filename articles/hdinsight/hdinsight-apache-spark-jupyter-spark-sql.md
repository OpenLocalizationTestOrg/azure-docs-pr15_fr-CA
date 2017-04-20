<properties
    pageTitle="Créer un cluster à allumage sur HDInsight Linux et utiliser SQL d’allumage de Jupyter pour l’analyse interactive | Microsoft Azure"
    description="Des instructions détaillées sur la façon de créer rapidement une étincelle Apache du cluster dans le HDInsight et utilisent SQL d’allumage à partir d’ordinateurs portables de Jupyter pour exécuter des requêtes interactives."
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
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="get-started-create-apache-spark-cluster-on-hdinsight-linux-and-run-interactive-queries-using-spark-sql"></a>Mise en route : créer le cluster d’allumage d’Apache sur HDInsight Linux et exécuter des requêtes interactives à l’aide de SQL d’allumage

Apprenez à créer un cluster Apache étincelle dans HDInsight et utiliser [Jupyter](https://jupyter.org) portable pour exécuter des requêtes interactives d’allumage SQL sur le cluster d’allumage.

   ![Mise en route à l’aide d’allumage Apache dans HDInsight] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Mise en route à l’aide de Apache étincelle dans didacticiel de HDInsight. Les étapes illustrées : créer un compte de stockage ; Création d’un cluster ; Exécutez les instructions SQL d’allumage")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Conditions préalables

- **Abonnement d’un Azure**. Avant de commencer ce didacticiel, vous devez posséder un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Client d’un SSH (Secure Shell)**: conservées de systèmes Linux, Unix et OS X un SSH client par le biais de la `ssh` commande. Pour les systèmes Windows, nous vous recommandons de [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Clés de SSH (Secure Shell) (facultatifs)**: vous pouvez sécuriser le compte SSH utilisé pour se connecter au cluster à l’aide d’un mot de passe ou une clé publique. À l’aide d’un mot de passe permet de vous familiariser rapidement, et vous devez utiliser cette option si vous souhaitez rapidement créer un cluster et exécuter certaines tâches de test. À l’aide d’une clé est plus sécurisé, mais il nécessite une installation supplémentaire. Vous pouvez, si vous le souhaitez, utiliser cette approche lors de la création d’un cluster de production. Dans cet article, nous utilisons l’approche de mot de passe. Pour savoir comment créer et utiliser des clés SSH avec HDInsight, reportez-vous aux articles suivants :

    -  À partir d’un ordinateur Linux - [Utiliser SSH avec basé sur Linux HDInsight (Hadoop) à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  À partir d’un ordinateur Windows - [Utiliser SSH avec basé sur Linux HDInsight (Hadoop) à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] Cet article utilise un modèle de gestionnaire de ressources Azure pour créer un cluster d’allumage qui utilise des [Objets BLOB de stockage Azure comme le stockage de cluster](hdinsight-hadoop-use-blob-storage.md). Vous pouvez également créer un cluster d’allumage qui utilise un [Magasin de LAC de données Azure](../data-lake-store/data-lake-store-overview.md) sous la forme d’un stockage supplémentaire, en plus des objets BLOB de stockage Azure comme l’espace de stockage par défaut. Pour obtenir des instructions, voir [Création d’un cluster de HDInsight avec le magasin de données lac](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Créer le cluster d’allumage

Dans cette section, vous créez un cluster de version 3.4 HDInsight (allumage version 1.6.1) à l’aide d’un modèle de gestionnaire de ressources Azure. Pour plus d’informations sur les versions de HDInsight et leurs contrats SLA, consultez [Versioning des composants de HDInsight](hdinsight-component-versioning.md). Pour les autres méthodes de création de cluster, consultez le [HDInsight de créer des clusters](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle se trouve dans un conteneur public blob, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. À partir de la blade de paramètres, entrez les informations suivantes :

    - **Nom_Cluster**: entrez un nom pour le cluster Hadoop que vous allez créer.
    - **Mot de passe et le nom de connexion de cluster**: le nom d’ouverture de session par défaut est Admin.
    - **Mot de passe et nom d’utilisateur SSH**.
    
    Veuillez noter que ces valeurs.  Vous en aurez besoin plus tard dans le didacticiel.

    > [AZURE.NOTE] SSH est utilisé pour accéder à distance au cluster de HDInsight en utilisant une ligne de commande. Le nom d’utilisateur et le mot de passe utilisé ici est utilisé lors de la connexion au cluster via SSH. En outre, le nom d’utilisateur SSH doit être unique, qu’il crée un compte d’utilisateur sur tous les nœuds de cluster HDInsight. Les éléments suivants sont les noms de comptes réservés pour une utilisation par les services de cluster et ne peut pas être utilisés comme nom d’utilisateur SSH :
    >
    > racine, hdiuser, tempête, hbase, ubuntu, soigneur, très, fils, mapred, hbase, ruche, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-soigneur.

    > Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

    > * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Cliquez sur **OK** pour enregistrer les paramètres.

4 à partir de la blade de **déploiement de personnalisé** , cliquez sur la liste déroulante **groupe de ressources** et puis cliquez sur **Nouveau** pour créer un nouveau groupe de ressources. Le groupe de ressources est un conteneur qui groupe le cluster, le compte de stockage dépendant et autres ressources liées.

5 Cliquez sur **conditions**, puis cliquez sur **créer**.

6. Cliquez sur **créer**. Vous verrez une nouvelle mosaïque intitulée déploiement d’envoi pour le déploiement du modèle. Il faut environ 20 minutes environ pour créer le cluster et la base de données SQL.



## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Exécuter des requêtes SQL d’allumage à l’aide d’un portable Jupyter

Dans cette section, Jupyter portable vous permet d’exécuter des requêtes SQL du moteur sur le cluster d’allumage. Les clusters HDInsight Spark fournissent deux noyaux que vous pouvez utiliser avec le portable de Jupyter. Il s’agit :

* **PySpark** (pour les applications écrites dans les Python)
* **Allumage** (pour les applications écrites dans Scala)

Dans cet article, vous allez utiliser le noyau PySpark. Dans l’article [disponible sur les ordinateurs portables de Jupyter avec des clusters d’allumage HDInsight les noyaux](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) , vous pouvez lire les détails sur les avantages de l’utilisation du noyau PySpark. Toutefois, deux des principaux avantages de l’utilisation du noyau PySpark est les suivantes :

* Vous n’êtes pas obligé de définir les contextes d’érosion et de la ruche. Elles sont automatiquement définies pour vous.
* Vous pouvez utiliser magics de cellule, tel que `%%sql`, pour exécuter directement vos requêtes SQL ou de la ruche, sans les extraits de code précédents.
* La sortie pour les requêtes SQL ou de la ruche est affichée automatiquement.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Créer un bloc-notes de Jupyter avec PySpark noyau 

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la mosaïque pour votre cluster allumage (si vous l’épinglé à la startboard). Vous pouvez également accéder à votre cluster sous **Parcourir tous les** > **HDInsight Clusters**.   

2. À partir de la blade de cluster étincelle, cliquez sur le **Tableau de bord de Cluster**, puis cliquez sur **Bloc-notes de Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOM_CLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un nouveau bloc-notes de Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Créer un nouveau bloc-notes de Jupyter")

3. Un nouvel ordinateur portable est créé et ouverte avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure et entrez un nom convivial.

    ![Fournir un nom pour l’ordinateur portable] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour l’ordinateur portable")

4. Parce que vous avez créé un bloc-notes à l’aide de la PySpark de noyau, vous n’avez pas besoin créer les contextes explicitement. Les contextes d’allumage et ruche seront automatiquement créés pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Pour cela, collez l’extrait de code suivant dans une cellule et appuyez sur **MAJ + ENTRÉE**.

        from pyspark.sql.types import *
        
    Chaque fois que vous exécutez un travail de Jupyter, titre de fenêtre de votre navigateur web affiche un état **(occupé)** et le nom de l’ordinateur portable. Vous verrez également un cercle plein en regard du texte **PySpark** dans le coin supérieur droit. Une fois la tâche terminée, cela devient un cercle vide.

     ![État d’un travail de portable Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "État d’un travail de portable Jupyter")

4. Charger des exemples de données dans une table temporaire. Lorsque vous créez un cluster d’étincelle dans HDInsight, l’exemple de fichier de données, **hvac.csv**, est copié dans le compte de stockage associé sous **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Dans une cellule vide, collez l’exemple de code suivant, puis appuyez sur **MAJ + ENTRÉE**. Cet exemple de code enregistre les données dans une table temporaire appelée **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Étant donné que vous utilisez un noyau PySpark, vous pouvez maintenant directement exécuter une requête SQL sur la table temporaire **hvac** que vous venez de créer à l’aide de la `%%sql` magique. Pour plus d’informations sur le `%%sql` magic, ainsi que d’autres magics disponibles avec le noyau PySpark, reportez-vous à la section [noyaux disponibles sur les ordinateurs portables de Jupyter avec HDInsight d’érosion clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Une fois la tâche terminée avec succès, le résultat sous forme de tableau suivant s’affiche par défaut.

    ![Sortie de table du résultat de la requête] (./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Sortie de table du résultat de la requête")

    Vous pouvez également voir les résultats dans les autres visualisations ainsi. Par exemple, un graphique en aires pour la même sortie ressemblerait à ce qui suit.

    ![Graphique en aires du résultat de la requête] (./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Graphique en aires du résultat de la requête")


6. Lorsque vous avez terminé l’exécution de l’application, vous devez arrêter l’ordinateur portable pour libérer les ressources. Pour ce faire, dans le menu **fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette va s’arrêter, puis fermez le bloc-notes.

##<a name="delete-the-cluster"></a>Supprimer du cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="see-also"></a>Voir aussi


* [Vue d’ensemble : Allumage commandé de Apache sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Allumage avec BI : effectuer l’analyse interactive des données à l’aide d’étincelle dans HDInsight avec les outils d’analyse Décisionnelle](hdinsight-apache-spark-use-bi-tools.md)

* [Allumage avec apprentissage automatique : allumage d’utilisation dans les HDInsight d’analyse de température de construction à l’aide des données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Allumage avec apprentissage automatique : allumage utilisation de HDInsight pour prédire les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu de l’allumage : Allumage d’utilisation dans HDInsight pour générer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’étincelle dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Analyse des données télémétriques Insight application à l’aide d’étincelle dans HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
