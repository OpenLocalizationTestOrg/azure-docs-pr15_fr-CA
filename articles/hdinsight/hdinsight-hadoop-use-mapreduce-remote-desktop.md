<properties
   pageTitle="MapReduce et Bureau à distance avec Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser le Bureau à distance à se connecter à Hadoop sur HDInsight et d’exécuter des travaux de MapReduce."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Utilisez MapReduce dans Hadoop sur HDInsight avec Bureau à distance

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre de se connecter à un Hadoop sur cluster de HDInsight à l’aide du Bureau à distance et d’exécuter les travaux MapReduce à l’aide de la commande Hadoop.

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un cluster basé sur Windows HDInsight (Hadoop sur HDInsight)

* Un ordinateur client exécutant Windows 7, Windows 8 ou Windows 10

##<a id="connect"></a>Connexion Bureau à distance

Activer le Bureau à distance pour le cluster HDInsight, puis de s’y connecter en suivant les instructions de la section [se connecter à des clusters HDInsight utilisant le protocole RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Utilisez la commande Hadoop

Lorsque vous êtes connecté sur le bureau pour le cluster HDInsight, procédez comme suit pour exécuter un travail MapReduce à l’aide de la commande Hadoop :

1. À partir du bureau de HDInsight, démarrer la **ligne de commande Hadoop**. Cela ouvre une nouvelle invite de commande dans le **c:\apps\dist\hadoop-&lt;numéro de version >** directory.

    > [AZURE.NOTE] Le numéro de version change à mesure que Hadoop est mis à jour. La variable d’environnement **HADOOP_HOME** peut être utilisée pour rechercher le chemin d’accès. Par exemple, `cd %HADOOP_HOME%` modifie les répertoires pour le répertoire Hadoop, sans que vous ayez besoin de connaître le numéro de version.

2. Pour utiliser la commande **Hadoop** pour exécuter une tâche de MapReduce exemple, utilisez la commande suivante :

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Démarre la classe **wordcount** , ce qui est contenue dans le fichier **hadoop-mapreduce-examples.jar** dans le répertoire en cours. Comme entrée, il utilise le document **wasbs://example/data/gutenberg/davinci.txt** et la sortie est stockée à : **wasbs : / / / exemple/données/WordCountOutput**.

    > [AZURE.NOTE] Pour plus d’informations sur cette tâche MapReduce et les données d’exemple, consultez <a href="hdinsight-use-mapreduce.md">MapReduce utilisation dans HDInsight Hadoop</a>.

2. Le travail émet des détails comme il est traité, et il retourne des informations semblables à ce qui suit lorsque la tâche est terminée :

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Lorsque la tâche est terminée, utilisez la commande suivante pour répertorier les fichiers de sortie stockés dans **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Cela doit afficher deux fichiers, **_SUCCESS** et **partie-r-00000**. Le fichier de la **partie-r-00000** contient le résultat de ce travail.

    > [AZURE.NOTE] Certaines tâches MapReduce peuvent fractionner les résultats dans plusieurs fichiers de **partie-r-###** . Si tel est le cas, utilisez le ### suffixe pour indiquer l’ordre des fichiers.

4. Pour afficher les résultats, utilisez la commande suivante :

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Cette option affiche une liste des mots qui sont contenus dans le fichier **wasbs://example/data/gutenberg/davinci.txt** , ainsi que le nombre d’occurrences de chaque mot. Voici un exemple des données contenues dans le fichier :

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande Hadoop fournit un moyen simple pour exécuter des travaux de MapReduce sur un cluster HDInsight et affichez le résultat de la tâche.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales à propos des tâches MapReduce dans HDInsight :

* [Utilisez MapReduce Hadoop de HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)
