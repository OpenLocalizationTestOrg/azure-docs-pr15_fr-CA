<properties
   pageTitle="Une connexion SSH et MapReduce avec Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser le protocole SSH pour exécuter les travaux MapReduce à l’aide d’Hadoop sur HDInsight."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Utilisez MapReduce avec Hadoop sur HDInsight avec SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous apprendrez comment utiliser SSH (Secure Shell) se connecter à un Hadoop sur cluster de HDInsight et l’envoyer à l’aide de commandes d’Hadoop MapReduce travaux.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs d’Hadoop de basé sur Linux, mais vous ne connaissez pas HDInsight, consultez [les conseils de HDInsight de fonctionnant sous Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un cluster basé sur Linux HDInsight (Hadoop sur HDInsight)

* Un client SSH. Les systèmes d’exploitation Unix, Linux et Mac doit être accompagnée d’un client SSH. Les utilisateurs Windows doivent télécharger un client, tel que [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Se connecter avec SSH

Se connecter au nom de domaine pleinement qualifié (FQDN) de votre cluster de HDInsight à l’aide de la commande SSH. Le nom de domaine complet est le nom que vous avez donné le cluster, suivi de **. azurehdinsight.net**. Par exemple, les éléments suivants seraient se connecter à un cluster nommé **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lors de la création du cluster HDInsight, vous devrez peut-être spécifier de l’emplacement de la clé privée sur votre système client, par exemple :

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni un mot de passe pour l’authentification SSH** lors de la création du cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-clients"></a>PuTTY (clients Windows)

Windows ne fournit pas un client SSH intégré. Nous vous recommandons d’à l’aide de **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Utilisez les commandes Hadoop

1. Après que vous être connecté au cluster HDInsight, utilisez la commande **Hadoop** suivante pour démarrer un travail MapReduce :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Démarre la classe **wordcount** , ce qui est contenue dans le fichier **hadoop-mapreduce-examples.jar** . Comme entrée, il utilise le document **wasbs://example/data/gutenberg/davinci.txt** et la sortie est stockée à **wasbs : / / / exemple/données/WordCountOutput**.

    > [AZURE.NOTE] Pour plus d’informations sur cette tâche MapReduce et les données d’exemple, consultez [Utilisation des MapReduce dans Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

2. Le travail émet des détails comme il traite et il renvoie des informations similaires à ce qui suit lorsque la tâche est terminée :

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Une fois la tâche terminée, utilisez la commande suivante pour répertorier les fichiers de sortie qui sont stockés dans **wasbs://example/data/WordCountOutput**:

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    Cela doit afficher deux fichiers, **_SUCCESS** et **partie-r-00000**. Le fichier de la **partie-r-00000** contient le résultat de ce travail.

    > [AZURE.NOTE] Certaines tâches MapReduce peuvent fractionner les résultats dans plusieurs fichiers de **partie-r-###** . Si tel est le cas, utilisez le ### suffixe pour indiquer l’ordre des fichiers.

4. Pour afficher les résultats, utilisez la commande suivante :

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Cette option affiche une liste des mots qui figurent dans le fichier **wasbs://example/data/gutenberg/davinci.txt** et le nombre d’occurrences de chaque mot. Voici un exemple des données contenues dans le fichier :

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, les commandes d’Hadoop fournissent un moyen simple pour exécuter des travaux de MapReduce dans un cluster HDInsight et affichez le résultat de la tâche.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales à propos des tâches MapReduce dans HDInsight :

* [Utilisez MapReduce Hadoop de HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)
