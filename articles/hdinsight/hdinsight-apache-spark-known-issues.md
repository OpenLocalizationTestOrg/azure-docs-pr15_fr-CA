<properties 
    pageTitle="Problèmes d’allumage Apache dans HDInsight | Microsoft Azure" 
    description="Problèmes connus d’allumage Apache dans HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>Problèmes connus pour le cluster d’allumage d’Apache sur HDInsight Linux

Ce document conserve une trace de tous les problèmes connus pour la présentation publique de HDInsight Spark.  

##<a name="livy-leaks-interactive-session"></a>Livy perd de la session interactive
 
Lors du redémarrage Livy avec une session interactive (à partir de Ambari ou d’headnode 0 machine virtuelle redémarrer) vie, perdrez une session interactive des tâches. Par conséquent, les nouveaux travaux peuvent être bloqué à l’état accepté et ne peut pas être démarrés.

**Réduction des risques :**

Utilisez la procédure suivante pour résoudre ce problème :

1. SSH dans headnode. 
2. Exécutez la commande suivante pour rechercher l’ID d’application des travaux commencées via Livy interactives. 

        yarn application –list

    Le projet par défaut sera spécifié par Livy si les travaux ont démarré avec une session interactive de Livy avec aucun des noms explicites, session de pour le Livy démarrée par Jupyter bloc-notes, le nom de la tâche démarrera avec remotesparkmagics_ *. 

3. Exécutez la commande suivante pour supprimer ces travaux. 

        yarn application –kill <Application ID>

Nouveaux travaux commence à s’exécuter. 

##<a name="spark-history-server-not-started"></a>Serveur de l’historique d’allumage n’a ne pas démarré 

Serveur de l’historique d’allumage n’est pas démarré automatiquement après la création d’un cluster.  

**Réduction des risques :** 

Démarrer manuellement le serveur de l’historique à partir de Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problème d’autorisation dans le répertoire de journal d’allumage 

Lorsque hdiuser soumet un travail avec spark-submit, il existe une java.io.FileNotFoundException d’erreur : /var/log/spark/sparkdriver_hdiuser.log (autorisation refusée) et le journal du pilote n’est pas écrit. 

**Réduction des risques :**
 
1. Ajouter hdiuser au groupe Hadoop. 
2. Fournir les 777 autorisations sur /var/log/spark après la création du cluster. 
3. Mettre à jour l’emplacement du journal allumage par Ambari à un répertoire disposant de 777 autorisations.  
4. Exécuter allumage-soumettre comme sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Problèmes liés aux ordinateurs portables de Jupyter

Voici certains problèmes connus relatifs aux ordinateurs portables de Jupyter.


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Les ordinateurs portables avec des caractères non ASCII dans les noms de fichiers

Ordinateurs portables Jupyter qui peuvent être utilisés dans des clusters d’allumage HDInsight ne doivent pas avoir de caractères non-ASCII dans les noms de fichiers. Si vous essayez de télécharger un fichier via l’UI Jupyter qui a un nom de fichier non-ASCII, il échoue silencieusement (c'est-à-dire, Jupyter ne vous permettent pas de télécharger le fichier, mais il ne renverra une erreur visible soit). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erreur lors du chargement des ordinateurs portables de plus grande taille

Vous pouvez voir une erreur **`Error loading notebook`** lorsque vous chargez des ordinateurs portables qui sont de plus grandes taille.  

**Réduction des risques :**

Si vous obtenez cette erreur, cela ne signifie pas que vos données sont endommagées ou perdues.  Vos blocs-notes sont toujours sur le disque de `/var/lib/jupyter`, et vous pouvez SSH dans le cluster pour y accéder. Vous pouvez copier vos blocs-notes à partir de votre cluster sur votre ordinateur local (à l’aide de SCP ou WinSCP) en tant que sauvegarde pour éviter la perte de toutes les données importantes dans le bloc-notes. Vous pouvez ensuite SSH tunnel dans votre headnode port 8001 pour accéder à Jupyter sans passer la passerelle.  À partir de là, vous pouvez désactiver la sortie de votre ordinateur portable et l’enregistrer de nouveau pour réduire la taille de l’ordinateur portable.

Pour empêcher cette erreur à l’avenir, vous devez appliquer certaines meilleures pratiques :

* Il est important de conserver une petite taille du portable. Toute sortie de vos travaux d’allumage qui est envoyé à Jupyter est rendue persistante dans le bloc-notes.  Il est en général conseillé avec Jupyter pour éviter de `.collect()` sur RDD volumineux ou dataframes ; Si vous souhaitez lire de contenu d’un RDD, envisagez plutôt d’exécution `.take()` ou `.sample()` afin que votre sortie n’obtient pas trop grande.
* Également, lorsque vous enregistrez un ordinateur portable, désactivez tous les sortie cellules pour réduire la taille.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Démarrage de l’ordinateur portable prend plus longtemps que prévu 

Première instruction code portable Jupyter à l’aide de la magie d’allumage peut prendre plus d’une minute.  

**Explication :**
 
Cela se produit car lorsque la première cellule de code est exécutée. En arrière-plan Cela lance la configuration de la session et allumage, SQL et contextes de la ruche sont définies. Une fois les contextes sont définies, la première instruction est exécutée, et cela donne l’impression que l’instruction a pris beaucoup de temps.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Délai d’attente de Jupyter ordinateur portable lors de la création de la session

Lorsque le cluster d’allumage manque de ressources, les noyaux d’allumage et Pyspark dans le bloc-notes de Jupyter expireront tente de créer la session. 

**Réductions :** 

1. Libérez des ressources de votre cluster d’allumage par :

    - Arrêt d’autres blocs-notes étincelle allant dans le menu fermer et arrêter ou en cliquant sur arrêt dans l’Explorateur de l’ordinateur portable.
    - L’arrêt d’autres applications d’allumage à partir de fils.

2. Redémarrez l’ordinateur portable que vous avez essayé de démarrer. Suffisamment de ressources doit être disponibles pour créer une session maintenant.

##<a name="see-also"></a>Voir aussi

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

* [Installez Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache étincelle dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tâches de suivi et de débogage en cours d’exécution sur un cluster Apache étincelle dans HDInsight](hdinsight-apache-spark-job-debugging.md)
