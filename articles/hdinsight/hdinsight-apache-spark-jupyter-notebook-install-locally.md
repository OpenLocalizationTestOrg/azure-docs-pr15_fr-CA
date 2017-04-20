<properties 
    pageTitle="Installer Jupyter bloc-notes sur votre ordinateur et le connecter à un cluster HDInsight Spark | Microsoft Azure" 
    description="Découvrez comment installer le Jupyter portable localement sur votre ordinateur et le connecter à un cluster d’allumage d’Apache sur Azure HDInsight." 
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
    ms.date="09/26/2016" 
    ms.author="nitinme"/>


# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Installer Jupyter bloc-notes sur votre ordinateur et de se connecter au cluster d’allumage d’Apache sur HDInsight Linux

Dans cet article, vous apprendrez comment installer Jupyter portable, avec les noyaux d’allumage (par Scala) avec magic d’allumage PySpark personnalisée (pour les Python) et connectez l’ordinateur portable à un cluster d’HDInsight. Il peut y avoir un certain nombre de bonnes raisons d’installer Jupyter sur votre ordinateur local, et il peut y avoir quelques défis ainsi. Pour obtenir une liste des raisons et des défis, consultez la section [Pourquoi dois-je installer des Jupyter sur mon ordinateur](#why-should-i-install-jupyter-on-my-computer) à la fin de cet article.

Il existe trois principales étapes liées à l’installation de Jupyter et la magie d’allumage sur votre ordinateur.

* Installer Jupyter portable
* Installer les noyaux de PySpark et d’allumage avec la magie d’allumage
* Configurer magic d’allumage pour accéder à cluster étincelle sur HDInsight

Pour plus d’informations sur les noyaux personnalisés et la magie d’allumage disponible pour les ordinateurs portables de Jupyter avec HDInsight cluster, consultez [noyaux disponibles pour les ordinateurs portables de Jupyter avec les clusters Linux d’allumage Apache sur HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##<a name="prerequisites"></a>Conditions préalables

Les composants requis répertoriés ici ne sont pas pour l’installation de Jupyter. Il s’agit pour la connexion du carnet de Jupyter à un cluster d’HDInsight une fois que l’ordinateur portable est installé.

- Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster d’allumage d’Apache sur HDInsight Linux. Pour obtenir des instructions, consultez [créer un allumage Apache des clusters dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installer Jupyter bloc-notes sur votre ordinateur

Vous devez installer les Python avant de pouvoir installer les portables Jupyter. Les Python et Jupyter sont disponibles dans le cadre de la [distribution de Ananconda](https://www.continuum.io/downloads). Lorsque vous installez Anaconda, vous installez en réalité une distribution de Python. Une fois que Anaconda est installé, vous ajoutez l’installation de Jupyter en exécutant une commande. Cette section fournit des instructions que vous devez suivre.

1. Télécharger [programme d’installation de Anaconda](https://www.continuum.io/downloads) pour votre plate-forme et exécuter le programme d’installation. Lors de l’exécution de l’Assistant d’installation, assurez-vous que vous sélectionnez l’option Ajouter Anaconda à la variable du chemin d’accès.

2. Exécutez la commande suivante pour installer le Jupyter.

        conda install jupyter

    Pour plus d’informations sur les éléments Jupyter, reportez-vous à la section [Jupyter de l’installation à l’aide de Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Installer les noyaux et la magie d’allumage

Pour obtenir des instructions sur l’installation de la magie d’allumage, les noyaux de PySpark et d’allumage, reportez-vous à la [documentation de sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) sur GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Configurer la magie d’allumage pour accéder au cluster HDInsight Spark

Dans cette section, vous configurez la magie d’allumage que vous avez installé précédemment pour vous connecter à un cluster d’allumage d’Apache qui vous devez déjà avoir créé dans Azure HDInsight.

1. Les informations de configuration de Jupyter sont généralement stockées dans le répertoire de base des utilisateurs. Pour localiser votre répertoire de base sur n’importe quelle plate-forme de système d’exploitation, tapez les commandes suivantes.

    Démarrer l’interface de Python. Dans une fenêtre de commande, tapez ce qui suit :

        python

    Sur la coque Python, entrez la commande suivante pour déterminer le répertoire de base.

        import os
        print(os.path.expanduser('~'))

2. Accédez au répertoire de base et créez un dossier appelé **.sparkmagic** , s’il n’existe pas déjà.

3. Dans le dossier, créez un fichier appelé **config.json** et ajouter l’extrait suivant de JSON qu’elle contient.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Remplacez **{nom_utilisateur}**, **{CLUSTERDNSNAME}**et **{BASE64ENCODEDPASSWORD}** avec les valeurs appropriées. Vous pouvez utiliser un certain nombre d’utilitaires dans votre langage de programmation favori ou en ligne pour générer un mot de passe encodé en base 64 de votre mot de passe actualy. Un extrait de code Python simple à exécuter à partir de l’invite de commande serait la suivante :

        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Lancez Jupyter. Utilisez la commande suivante à partir de l’invite de commande.

        jupyter notebook

6. Vérifiez que vous pouvez vous connecter au cluster en utilisant le bloc-notes de Jupyter et que vous pouvez utiliser la magie d’allumage disponible avec les noyaux. Effectuez les opérations suivantes.

    1. Créer un nouveau bloc-notes. À partir de la droite, cliquez sur **Nouveau**. Vous devez voir le noyau par défaut **Python2** et les deux nouveaux noyaux que vous installez, **PySpark** et **allumage**.

        ![Créer un nouveau bloc-notes de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Créer un nouveau bloc-notes de Jupyter")

    
        Cliquez sur **PySpark**.


    2. Exécutez l’extrait de code suivant.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Si vous pouvez récupérer avec succès de la sortie, votre connexion au cluster HDInsight est testée.

    >[AZURE.TIP] Si vous souhaitez mettre à jour la configuration de l’ordinateur portable pour se connecter à un cluster différent, mettre à jour la config.json avec le nouveau jeu de valeurs, comme indiqué à l’étape 3 ci-dessus. 

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Pourquoi dois-je installer des Jupyter sur mon ordinateur ?

Il peut y avoir un certain nombre de raisons pourquoi vous souhaiterez peut-être installer Jupyter sur votre ordinateur et connectez-le à un cluster d’allumage sur HDInsight.

* Bien que les ordinateurs portables de Jupyter sont déjà disponibles sur le cluster d’étincelle dans Azure HDInsight, l’installation de Jupyter sur votre ordinateur fournit vous permet de créer vos blocs-notes localement, testez votre application sur un cluster en cours d’exécution et de Téléchargez les portables au cluster. Pour charger les portables sur le cluster, vous pouvez les télécharger à l’aide de le Jupyter ordinateur portable est en cours d’exécution ou le cluster, ou les enregistrer dans le dossier /HdiNotebooks dans le compte de stockage associé au cluster. Pour plus d’informations sur comment les blocs-notes sont stockés sur le cluster, consultez [où sont stockés les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Avec les ordinateurs portables disponibles localement, vous pouvez vous connecter à des clusters d’allumage différents en fonction de votre application.
* Vous pouvez utiliser GitHub pour implémenter un système de contrôle de code source et que le contrôle de version pour les ordinateurs portables. Vous pouvez également avoir un environnement collaboratif dans lequel plusieurs utilisateurs peuvent travailler avec le même bloc-notes.
* Vous pouvez travailler avec des ordinateurs portables localement sans même avoir un cluster. Il vous suffit d’un cluster pour tester vos blocs-notes, ne pas à la pour gérer manuellement vos blocs-notes ou un environnement de développement.
* Il peut être plus facile de configurer votre propre environnement de développement local plutôt que de configurer l’installation de Jupyter sur le cluster.  Vous pouvez profiter de tous les logiciels que vous avez installé localement sans configurer un ou plusieurs clusters distants.

>[AZURE.WARNING] Avec Jupyter installé sur votre ordinateur local, plusieurs utilisateurs peuvent exécuter le même bloc-notes sur le même cluster d’allumage en même temps. Dans une telle situation, plusieurs sessions de Livy sont créées. Si vous rencontrez un problème et que vous souhaitez déboguer qui, il est une tâche complexe pour effectuer le suivi de session Livy appartient à l’utilisateur.




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

* [Utilisez les lots externes avec les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache étincelle dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tâches de suivi et de débogage en cours d’exécution sur un cluster Apache étincelle dans HDInsight](hdinsight-apache-spark-job-debugging.md)
