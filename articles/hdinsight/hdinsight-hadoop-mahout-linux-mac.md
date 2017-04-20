<properties
    pageTitle="Générer des recommandations à l’aide de Mahout et HDInsight de basé sur Linux | Microsoft Azure"
    description="Apprenez à utiliser l’ordinateur Apache Mahout bibliothèque de formation pour générer des recommandations de film avec basé sur Linux HDInsight (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>Générer des recommandations de la vidéo à l’aide de Apache Mahout avec basé sur Linux d’Hadoop dans HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser l’ordinateur [Apache Mahout](http://mahout.apache.org) apprentissage avec Azure HDInsight pour produire des recommandations de film.

Mahout est une [machine de formation] de[ ml] bibliothèque pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, telles que le filtrage, le classement et le clustering. Dans cet article, vous utiliserez un moteur de recommandation pour produire des recommandations de film qui sont basées sur vos amis ont vu des films.

> [AZURE.NOTE] Les étapes de ce document requièrent un Hadoop sur HDInsight cluster fonctionnant sous Linux. Pour plus d’informations sur l’utilisation de Mahout avec un cluster Windows, consultez [recommandations de film de générer à l’aide de Mahout Apache avec basé sur Windows d’Hadoop dans HDInsight](hdinsight-mahout.md)

##<a name="prerequisites"></a>Conditions préalables

* Un Hadoop sur HDInsight cluster fonctionnant sous Linux. Pour plus d’informations sur la création d’une, voir [mise en route de basé sur Linux d’Hadoop dans HDInsight][getstarted]

##<a name="mahout-versioning"></a>Version de mahout

Pour plus d’informations sur la version de Mahout inclus dans votre cluster de HDInsight, consultez [versions de HDInsight et de composants d’Hadoop](hdinsight-component-versioning.md).

> [AZURE.WARNING] S’il est possible de télécharger une version différente de Mahout au cluster HDInsight, uniquement les composants fournis avec le cluster HDInsight sont entièrement supportés et Support de Microsoft vous permet d’isoler et de résoudre les problèmes liés à ces composants.
>
> Composants assistance commercialement raisonnables pour vous aider à résoudre le problème. Cela peut provoquer de résolution du problème ni à vous demander d’engager les canaux disponibles pour les technologies open source où se trouve une grande expertise pour cette technologie. Par exemple, de nombreux sites de la Communauté qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Également les projets Apache ont des sites de projets sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/), [commandé](http://spark.apache.org/).

##<a name="recommendations"></a>Recommandations de présentation

L’une des fonctions fournies par Mahout est un moteur de recommandation. Ce moteur accepte les données dans le format de `userID`, `itemId`, et `prefValue` (la préférence aux utilisateurs pour l’article). Mahout peut ensuite analyser une occurrence pour déterminer : _les utilisateurs qui ont une préférence pour un article ont également une préférence pour ces autres éléments_. Mahout détermine ensuite les utilisateurs avec les préférences de type d’élément, qui peuvent être utilisés pour émettre des recommandations.

Voici un exemple très simple qui utilise des films :

* __Occurrence de collaboration__: Joe, Alice et Bob aimé _Star Wars_, _L’Empire grèves précédent_et le _retour de la Jedi_. Mahout détermine que les utilisateurs qui aiment également de l’un de ces films comme les deux autres.

* __Occurrence de collaboration__: Bob et Alice également été convaincus par _Vengeance de la Sith_, _l’attaque des Clones_et _La Menace du fantôme_. Mahout détermine qui utilisateurs aimé les films de trois précédentes également à ces trois.

* __Recommandation de similarité__: Joe car aimé les trois premiers films, Mahout examine que d’autres personnes avec les préférences similaires aimés films, mais Joe n’a pas observé (aimé/nominale). Dans ce cas, Mahout vous recommande de _Vengeance de la Sith_, _l’attaque des Clones_et _La Menace du fantôme_.

###<a name="understanding-the-data"></a>Présentation des données

Heureusement, [GroupLens recherche] [ movielens] fournit des données de contrôle d’accès pour les films dans un format qui est compatible avec Mahout. Ces données sont disponibles sur le stockage de votre cluster par défaut à `/HdiSamples/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` (informations sur les films,) et `user-ratings.txt`. Le fichier ratings.txt de l’utilisateur est utilisé lors de l’analyse, alors que moviedb.txt est utilisé pour fournir des informations de texte convivial lors de l’affichage des résultats de l’analyse.

Les données contenues dans la ratings.txt de l’utilisateur ont une structure de `userID`, `movieID`, `userRating`, et `timestamp`, qui nous indique comment recommandé de chaque utilisateur avec un film. Voici un exemple des données :


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>Exécuter l’analyse

Utilisez la commande suivante pour exécuter le travail de recommandation :

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] La tâche peut prendre plusieurs minutes et peut exécuter plusieurs tâches MapReduce.

##<a name="view-the-output"></a>Afficher la sortie

1. Une fois la tâche terminée, utilisez la commande suivante pour afficher la sortie générée :

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    La sortie apparaît comme suit :

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    La première colonne est la `userID`. Les valeurs contenues dans ' [' et ']' sont `movieId`:`recommendationScore`.

2. Vous pouvez utiliser la sortie, ainsi que la moviedb.txt, pour afficher des informations plus conviviaux pour l’utilisateur. Tout d’abord, nous devons copier les fichiers localement à l’aide des commandes suivantes :

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    Il copie les données de sortie dans un fichier nommé **recommendations.txt** dans le répertoire en cours, ainsi que les fichiers de données vidéo.

3. Utilisez la commande suivante pour créer un nouveau script de Python qui recherche les noms de film pour les données dans la sortie de recommandations :

        nano show_recommendations.py

    Lorsque l’éditeur s’ouvre, utilisez le code suivant comme le contenu du fichier :

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    Appuyez sur **Ctrl-X**, **Y**et enfin **entrée** pour enregistrer les données.

3. Pour que le fichier exécutable, utilisez la commande suivante :

        chmod +x show_recommendations.py

4. Exécutez le script de Python. La procédure suivante suppose que vous êtes dans le répertoire dans lequel tous les fichiers ont été téléchargés :

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    Cela va examiner les recommandations générées pour utilisateur ID 4.

    * Le fichier **ratings.txt de l’utilisateur** est utilisé pour récupérer des vidéos que l’utilisateur a été
    * Le fichier **moviedb.txt** est utilisé pour récupérer les noms de films
    * Le **recommendations.txt** est utilisé pour récupérer les recommandations du film pour cet utilisateur

    La sortie de cette commande est semblable à la suivante :

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>Supprimer les données temporaires

Mahout tâches ne pas suppriment les données temporaires qui sont créées lors du traitement de la tâche. Le `--tempDir` paramètre est spécifié dans le projet d’exemple pour isoler les fichiers temporaires dans un chemin d’accès spécifique pour la suppression simple. Pour supprimer les fichiers temporaires, utilisez la commande suivante :

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Si vous souhaitez exécuter de nouveau la commande, vous devez également supprimer le répertoire de sortie. Pour supprimer ce répertoire, utilisez ce qui suit :
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser Mahout, découvrez d’autres façons de travailler avec des données sur HDInsight :

* [La ruche avec HDInsight](hdinsight-use-hive.md)
* [Porcs avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
