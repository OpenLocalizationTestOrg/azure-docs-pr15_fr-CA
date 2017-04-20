<properties 
    pageTitle="Disponible avec les ordinateurs portables de Jupyter sur HDInsight Spark de noyaux de clusters sous Linux | Microsoft Azure" 
    description="Obtenir des informations sur les noyaux de bloc-notes Jupyter supplémentaires avec cluster étincelle sur HDInsight Linux." 
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
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="kernels-available-for-jupyter-notebooks-with-apache-spark-clusters-on-hdinsight-linux"></a>Disponible pour les ordinateurs portables de Jupyter avec des clusters de Apache étincelle sur HDInsight Linux des noyaux

Cluster d’allumage d’Apache sur HDInsight (Linux) inclut Jupyter portables que vous pouvez utiliser pour tester vos applications. Un noyau est un programme qui s’exécute et interprète votre code. Les clusters HDInsight Spark fournissent deux noyaux que vous pouvez utiliser avec le portable de Jupyter. Il s’agit :

1. **PySpark** (pour les applications écrites dans les Python)
2. **Allumage** (pour les applications écrites dans Scala)

Dans cet article, vous allez découvrir comment utiliser ces noyaux et quels sont les avantages que vous obtenez à partir de leur utilisation.

**Conditions préalables :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster d’allumage d’Apache sur HDInsight Linux. Pour obtenir des instructions, consultez [créer un allumage Apache des clusters dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-use-the-kernels"></a>Comment pour utiliser les noyaux ? 

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la mosaïque pour votre cluster allumage (si vous l’épinglé à la startboard). Vous pouvez également accéder à votre cluster sous **Parcourir tous les** > **HDInsight Clusters**.   

2. À partir de la blade de cluster étincelle, cliquez sur le **Tableau de bord de Cluster**, puis cliquez sur **Bloc-notes de Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOM_CLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes avec les noyaux de nouveau. Cliquez sur **Nouveau**, puis cliquez sur **Pyspark** ou **explosion**. Vous devez utiliser le noyau d’allumage pour des applications de Scala et d’un noyau de PySpark pour les applications de Python.

    ![Créer un nouveau bloc-notes de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Créer un nouveau bloc-notes de Jupyter") 

3. Il doit s’ouvrir un bloc-notes avec le noyau que vous avez sélectionné.

## <a name="why-should-i-use-the-pyspark-or-spark-kernels"></a>Pourquoi utiliser les noyaux d’allumage ou de PySpark ?

Voici quelques avantages d’en utilisant les noyaux de nouveau.

1. **Prédéfinir les contextes**. Avec le noyaux **d’allumage** qui sont fournis avec les ordinateurs portables de Jupyter ou le **PySpark** , vous n’avez pas besoin de définir explicitement les contextes étincelle ou de la ruche avant de commencer à travailler avec l’application que vous développez ; ils sont disponibles pour vous par défaut. Les contextes sont :

    * **sc** - pour le contexte de l’allumage
    * **sqlContext** - pour le contexte de la ruche


    Par conséquent, n’avez pas à exécuter les instructions comme suit pour définir les contextes :

        ###################################################
        # YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
        ###################################################
        sc = SparkContext('yarn-client')
        sqlContext = HiveContext(sc)

    Au lieu de cela, vous pouvez utiliser directement les contextes prédéfinies dans votre application.
    
2. **Magics de la cellule**. Le noyau PySpark fournit certaines prédéfinis « magics », qui sont des commandes spéciales que vous pouvez appeler avec `%%` (par exemple, `%%MAGIC` <args>). La commande magie doit être le premier mot dans une cellule de code et de plusieurs lignes de contenu. Le mot magique doit être le premier mot de la cellule. Ajout de quoi que ce soit avant la magie, même commentaires, entraîne une erreur.   Pour plus d’informations sur magics, reportez-vous à la section [ici](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

    Le tableau ci-dessous répertorie les différentes magics disponibles via les noyaux.

  	| Magic     | Exemple                         | Description  |
  	|-----------|---------------------------------|--------------|
  	| Aide      | `%%help`                            | Génère une table de tous les magics disponibles avec l’exemple et la description |
  	| Info      | `%%info`                          | Informations de session de sorties pour le point de terminaison Livy actuel |
  	| configurer | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configure les paramètres de création d’une session. L’indicateur de force (-f) est obligatoire si une session a déjà été créée et que la session sera supprimée et recréée. Observez [POST /sessions de Livy corps de demande](https://github.com/cloudera/livy#request-body) pour obtenir une liste des paramètres valides. Paramètres doivent être passés comme une chaîne au format JSON et doivent se trouver sur la ligne suivante après la magie, comme indiqué dans la colonne de l’exemple. |
  	| SQL       |  `%%sql -o <variable name>`<br> `SHOW TABLES`    | Exécute une requête de ruche contre le sqlContext. Si le `-o` paramètre est passé, le résultat de la requête est rendue persistante dans le %% contexte de Python local sous la forme d’un dataframe de [Pandas](http://pandas.pydata.org/) .   |
  	| local     |     `%%local`<br>`a=1`              | Tout le code dans les lignes suivantes sera exécuté localement. Code doit être le code valide de Python. |
  	| journaux      | `%%logs`                        | Renvoie les journaux pour la session en cours Livy.  |
  	| supprimer    | `%%delete -f -s <session number>` | Supprime une session spécifique du point de terminaison Livy en cours. Notez que vous ne pouvez pas supprimer la session est lancée pour le noyau lui-même. |
  	| nettoyage   | `%%cleanup -f`                    | Supprime toutes les sessions pour le point de terminaison de Livy actuel, y compris les session de ce bloc-notes. L’indicateur-f force est obligatoire.  |

    >[AZURE.NOTE] En plus des magics est ajoutés par le noyau de PySpark, vous pouvez également utiliser le [magics de IPython intégrés](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), y compris les `%%sh`. Vous pouvez utiliser la `%%sh` magique pour exécuter des scripts et des blocs de code sur la headnode du cluster. 

3. **Visualisation d’automatique**. Le noyau **Pyspark** visualise automatiquement la sortie de requêtes SQL et de la ruche. Vous pouvez choisir entre plusieurs types de visualisations, y compris le tableau, graphique, ligne, zone, barre.

## <a name="parameters-supported-with-the-sql-magic"></a>Les paramètres pris en charge avec le %% sql magic

Le %% sql magic prend en charge les différents paramètres que vous pouvez utiliser pour contrôler le type de sortie que vous recevez lorsque vous exécutez des requêtes. Le tableau suivant affiche la sortie.

| Paramètre     | Exemple                         | Description  |
|-----------|---------------------------------|--------------|
| -o      | `-o <VARIABLE NAME>`                          | Utilisez ce paramètre pour rendre persistant le résultat de la requête, dans le %% contexte local de Python, sous la forme d’un dataframe de [Pandas](http://pandas.pydata.org/) . Le nom de la variable dataframe est le nom de variable que vous spécifiez. |
| -q      | `-q`                          | Utilisez cette option pour désactiver les visualisations pour la cellule. Si vous ne souhaitez pas auto-visualiser le contenu d’une cellule et souhaitez simplement capturer sous la forme d’un dataframe, puis utilisez `-q -o <VARIABLE>`. Si vous souhaitez désactiver les visualisations sans capturer les résultats (par exemple, pour l’exécution d’une requête SQL avec des effets secondaires, comme une `CREATE TABLE` instruction), il suffit d’utiliser `-q` sans spécifier un `-o` argument. |
| -m       |  `-m <METHOD>`    | Où la **méthode** est **prendre** ou **échantillon** (valeur par défaut est **prendre**). Si la méthode est de **prendre**, le noyau choisit des éléments à partir du haut du jeu de données de résultats spécifié par le nombre maximal de lignes (décrite plus loin dans ce tableau). Si la méthode est un **exemple**, le noyau échantillonne au hasard les éléments de l’ensemble de données en fonction de `-r` paramètre, décrite ci-après dans cette table.   |
| -r     |     `-r <FRACTION>`            | **FRACTION** est un nombre à virgule flottante comprise entre 0.0 et 1.0. Si la méthode de l’exemple pour la requête SQL est `sample`, puis le noyau échantillons au hasard la fraction spécifiée des éléments du jeu de résultats pour vous ; par exemple, si vous exécutez une requête SQL avec les arguments `-m sample -r 0.01`, puis de 1 % des lignes de résultat à échantillonner au hasard. |
| -n      | `-n <MAXROWS>`                        | **Nombre maximal de lignes** est une valeur entière. Le noyau limite le nombre de lignes de sortie pour le **nombre maximal de lignes**. Si le **nombre maximal de lignes** est un nombre négatif tel que **-1**, le nombre de lignes dans le jeu de résultats ne sera pas limité. |

**Exemple :**

    %%sql -q -m sample -r 0.1 -n 500 -o query2 
    SELECT * FROM hivesampletable

L’instruction ci-dessus effectue les opérations suivantes :

* Sélectionne tous les enregistrements à partir de **hivesampletable**.
* Étant donné que nous utilisons - q, il désactive la visualisation automatique.
* Étant donné que nous utilisons `-m sample -r 0.1 -n 500` il échantillonne 10 % des lignes de la hivesampletable et limite la taille de l’ensemble de résultats à 500 lignes de manière aléatoire.
* Enfin, étant donné que nous avons utilisé `-o query2` également, il enregistre la sortie dans un dataframe appelé **query2**.
    

## <a name="considerations-while-using-the-new-kernels"></a>Considérations lors de l’utilisation des nouvelles noyaux

Le noyau que vous utilisez (PySpark ou explosion), en laissant les portables exécutant consomme les ressources de votre cluster.  Ces noyaux, car les contextes sont prédéfinis, simplement quitter les portables d’interrompre le contexte et, par conséquent, les ressources du cluster continue à être en cours d’utilisation. Une bonne pratique avec les noyaux de PySpark et d’allumage serait d’utiliser l’option **Fermer et arrêter** dans le menu **fichier** de l’ordinateur portable. Cela supprime le contexte et puis quitte l’ordinateur portable.    


## <a name="show-me-some-examples"></a>Afficher des exemples

Lorsque vous ouvrez un bloc-notes de Jupyter, vous verrez deux dossiers disponibles au niveau de la racine.

* Le dossier **PySpark** a des exemples d’agendas qui utilisent le nouveau noyau de **Python** .
* Le dossier **Scala** a des exemples d’agendas qui utilisent le nouveau noyau **d’allumage** .

Vous pouvez ouvrir le bloc-notes **00 - [lecture ME premier] fonctionnalités de noyau de Magic étincelle** à partir du dossier **PySpark** ou **d’allumage** pour en savoir plus sur les magics différents disponibles. Vous pouvez également utiliser les autres portables exemples disponibles dans les deux dossiers pour savoir comment obtenir les différents scénarios, à l’aide d’ordinateurs portables de Jupyter avec des clusters de HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Où se trouvent les ordinateurs portables ?

Ordinateurs portables de Jupyter sont enregistrés dans le compte de stockage associé au cluster sous le dossier **/HdiNotebooks** .  Les ordinateurs portables, les fichiers texte et les dossiers que vous créez à partir de Jupyter sera accessibles à partir de WASB.  Par exemple, si vous utilisez Jupyter pour créer un dossier **MonDossier** et un ordinateur portable **myfolder/mynotebook.ipynb**, vous pouvez accéder ce bloc-notes à `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`.  L’inverse est également vrai, c'est-à-dire, si vous téléchargez un portable directement à votre compte de stockage à `/HdiNotebooks/mynotebook1.ipynb`, l’ordinateur portable sera également visible à partir de Jupyter.  Ordinateurs portables restera dans le compte de stockage, même après la suppression de cluster.

La façon dont les ordinateurs portables sont enregistrés dans le compte de stockage est compatible avec très. Par conséquent, si vous SSH dans le cluster, que vous pouvez utiliser le fichier les commandes de gestion comme suit :

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                 # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


En cas de problèmes d’accès du compte de stockage pour le cluster, les ordinateurs portables sont également enregistrés sur le headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Navigateur pris en charge
Blocs-notes Jupyter exécute HDInsight Spark clusters sont pris en charge uniquement sur Google Chrome.

## <a name="feedback"></a>Commentaires

Les noyaux sont en pleine évolution de stade et arrivent au fil du temps. Cela peut également signifier que l’API peut changer que les noyaux pour adultes. Nous aimerions recevoir vos commentaires que vous avez lors de l’utilisation de ces nouveaux noyaux. Ce sera très utile dans la version finale de ces noyaux de mise en forme. Vous pouvez laisser vos commentaires/commentaires sous la section **commentaires** au bas de cet article.


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

* [Utilisez les lots externes avec les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installez Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache étincelle dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tâches de suivi et de débogage en cours d’exécution sur un cluster Apache étincelle dans HDInsight](hdinsight-apache-spark-job-debugging.md)
