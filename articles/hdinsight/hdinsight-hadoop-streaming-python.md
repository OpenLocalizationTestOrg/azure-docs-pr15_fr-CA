<properties
   pageTitle="Développer les Python MapReduce des travaux avec HDInsight | Microsoft Azure"
   description="Découvrez comment créer et exécuter des travaux de Python MapReduce sur les clusters basés sur Linux de HDInsight."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="develop-python-streaming-programs-for-hdinsight"></a>Développer les Python de diffusion en continu de programmes pour HDInsight

Hadoop fournit une API de flux pour MapReduce qui vous permet d’écrire la carte et de réduire les fonctions dans les langages autres que Java. Dans cet article, vous allez apprendre à utiliser les Python pour effectuer des opérations de MapReduce.

> [AZURE.NOTE] Si le code Python de ce document peut être utilisé avec un cluster d’HDInsight de basés sur Windows, les étapes décrites dans ce document sont spécifiques aux clusters basés sur Linux.

Cet article est basé sur les informations et d’exemples, publiés par Michael Noll à [l’écriture d’un programme de MapReduce Hadoop dans Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##<a name="prerequisites"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un Hadoop sur cluster d’HDInsight sous Linux

* Un éditeur de texte

    > [AZURE.IMPORTANT] L’éditeur de texte doit utiliser LF comme la fin de la ligne. Si elle utilise CRLF, cela provoquera des erreurs lors de l’exécution de la tâche MapReduce sur les clusters basés sur Linux de HDInsight. Si vous n’êtes pas sûr, utilisez l’étape facultative dans la section [Exécution de MapReduce](#run-mapreduce) pour convertir les CRLF LF.

* Pour les clients Windows, PuTTY et PSCP. Ces utilitaires sont disponibles à partir de la <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Page de téléchargement de PuTTY</a>.

##<a name="word-count"></a>Nombre de mots

Pour cet exemple, vous allez implémenter une base de statistiques à l’aide d’un réducteur et un mappeur. Le mappeur de sauts de phrases en mots individuels et le réducteur rassemble les mots et compte pour produire la sortie.

Le diagramme suivant illustre ce qui se produit au cours de la carte et de réduire les phases.

![illustration du plan de réduction](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>Pourquoi Python ?

Python est un langage de programmation à caractère général, de haut niveau qui vous permet de concepts express dans moins de lignes de code que de nombreuses autres langues. Il a récemment est devenu populaire auprès des chercheurs de données sous la forme d’un langage de prototypage car sa nature interprété tapant dynamique et élégante syntaxe rendent compatible pour le développement rapide d’applications.

Python est installé sur tous les clusters HDInsight.

##<a name="streaming-mapreduce"></a>MapReduce en continu

Hadoop vous permet de spécifier un fichier qui contient le mappage et de réduire la logique utilisée par une tâche. Les exigences spécifiques de la carte et de réduire la logique sont :

* **D’entrée**: la carte et de réduire les composants doivent lire des données d’entrée à partir de STDIN.

* **Sortie**: la carte et de réduire les composants doivent écrire les données de sortie dans STDOUT.

* **Format de données**: les données consommées et produites doivent être une paire clé/valeur, séparée par un caractère de tabulation.

Python peut facilement gérer ces exigences en utilisant le module **sys** pour lire à partir de STDIN et **Imprimer** pour imprimer sur STDOUT. La tâche restante est simplement mise en forme de données avec un onglet (`\t`) caractère entre la clé et la valeur.

##<a name="create-the-mapper-and-reducer"></a>Créer le Mappeur et le réducteur

Le Mappeur et le réducteur sont des fichiers texte dans ce cas **mapper.py** et **reducer.py** (pour indiquer clairement qui fait quoi). Vous pouvez créer à l’aide de l’éditeur de votre choix.

###<a name="mapperpy"></a>Mapper.py

Créer un nouveau fichier nommé **mapper.py** et utilisez le code suivant comme le contenu :

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Prenez quelques instants pour lire le code afin de vous aider à comprendre ce qu’il fait.

###<a name="reducerpy"></a>REDUCER.py

Créer un nouveau fichier nommé **reducer.py** et utilisez le code suivant comme le contenu :

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##<a name="upload-the-files"></a>Télécharger les fichiers

À la fois **mapper.py** et **reducer.py** doivent être sur le nœud principal du cluster avant de pouvoir les exécuter. La façon la plus simple pour les charger consiste à utiliser des **scp** (**pscp** si vous utilisez un client Windows).

À partir du client, dans le même répertoire que **mapper.py** et **reducer.py**, utilisez la commande suivante. Remplacez le **nom d’utilisateur** avec un utilisateur SSH et **Nom_Cluster** avec le nom de votre cluster.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Il copie les fichiers sur le système local sur le nœud de tête.

> [AZURE.NOTE] Si vous avez utilisé un mot de passe pour sécuriser votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser le `-i` paramètre et le chemin d’accès à la clé privée, par exemple, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##<a name="run-mapreduce"></a>Exécutez MapReduce

1. Connectez-vous au cluster à l’aide de SSH :

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour sécuriser votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser le `-i` paramètre et le chemin d’accès à la clé privée, par exemple, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Facultatif) Si vous avez utilisé un éditeur de texte qui utilise les CRLF comme la ligne de fin lors de la création des fichiers mapper.py et reducer.py, ou ne pas savoir ce que votre éditeur de fin de ligne utilise, utilisez la commande suivante pour convertir les occurrences de CRLF dans mapper.py et reducer.py LF des commandes.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Utilisez la commande suivante pour démarrer le travail MapReduce.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    Cette commande comporte les éléments suivants :

    * **Hadoop-streaming.jar**: utilisé lors de l’exécution des opérations de flux MapReduce. Il sert d’interface Hadoop avec le code MapReduce externe que vous fournissez.

    * **-fichiers**: Hadoop indique que les fichiers spécifiés sont nécessaires pour cette tâche MapReduce et ils doivent être copiés vers tous les nœuds du travailleur.

    * **-Mappeur**: indique le fichier à utiliser comme le Mappeur Hadoop.

    * **-réducteur**: indique le fichier à utiliser comme le réducteur Hadoop.

    * **-d’entrée**: le fichier d’entrée que nous devons compter des mots à partir de.

    * **-sortie**: le répertoire sera écrit dans la sortie.

        > [AZURE.NOTE] Ce répertoire sera créé par la tâche.

Vous devez voir un certain nombre de relevés **d’informations** en tant que la tâche démarre et enfin voir l’opération de **mappage** et **réduire** affichée sous forme de pourcentages.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Vous recevrez des informations de statut de la tâche lorsqu’elle est terminée.

##<a name="view-the-output"></a>Afficher la sortie

Lorsque la tâche est terminée, utilisez la commande suivante pour afficher la sortie :

    hdfs dfs -text /example/wordcountout/part-00000

Il doit afficher une liste de mots et combien de fois le mot est survenue. Voici un exemple des données de sortie :

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser des tâches de MapRedcue en continu avec HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec HDInsight d’Azure.

* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)
* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)
* [Utiliser des travaux de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
