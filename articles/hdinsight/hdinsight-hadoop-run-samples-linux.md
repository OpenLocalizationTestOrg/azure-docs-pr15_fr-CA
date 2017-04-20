<properties
    pageTitle="Exécuter des exemples Hadoop MapReduce sur basé sur Linux de HDInsight | Microsoft Azure"
    description="Mise en route avec les échantillons MapReduce basé sur Linux de HDInsight. Utiliser le protocole SSH pour se connecter au cluster, puis utiliser la commande Hadoop pour exécuter des tâches de l’exemple."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>Exécution des exemples Hadoop dans HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Linux HDInsight grappes de serveurs fournissent un jeu d’échantillons MapReduce qui peut être utilisé pour vous familiariser avec les traitements Hadoop MapReduce. Dans ce document, vous en savoir plus sur les exemples disponibles et vous guident tout au long de l’exécution de certains de ces.

##<a name="prerequisites"></a>Conditions préalables

- **Abonnement d’un Azure**: voir [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **HDInsight de basé sur Linux d’un cluster**: reportez-vous à la section [mise en route à l’aide d’Hadoop avec ruche dans HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Un SSH client**: pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Les exemples ##

**Emplacement**: les exemples se trouvent sur le cluster de HDInsight à **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Contenu**: les exemples suivants sont contenus dans cette archive :

- **aggregatewordcount**: un regroupement en fonction de programme MapReduce qui compte les mots contenus dans les fichiers d’entrée
- **aggregatewordhist**: un regroupement en fonction de programme MapReduce qui calcule l’histogramme des mots dans les fichiers d’entrée
- **BBP**: un programme MapReduce qui utilise Bailey-Borwein-Plouffe pour calculer les chiffres exacts de Pi
- **dbcount**: un projet exemple qui compte les journaux de la page d’affichage stockées dans une une base de données
- **distbbp**: un programme qui utilise une formule de type BBP pour calculer les bits exacts de Pi MapReduce
- **GREP**: un programme MapReduce qui compte les correspondances des expressions régulières dans l’entrée
- **jointure**: un travail qui affecte une jointure plus triés, partitionné également les groupes de données
- **multifilewc**: un travail qui compte les mots à partir de plusieurs fichiers
- **pentomino**: un MapReduce mosaïque portant programme pour trouver des solutions aux problèmes de pentomino
- **pi**: un programme permettant d’estimer à l’aide d’un quasi Monte de Pi MapReduce méthode de Carlo
- **randomtextwriter**: un programme MapReduce qui écrit 10 Go de données textuelles aléatoires par nœud
- **randomwriter**: un programme MapReduce qui écrit 10 Go de données aléatoires par nœud
- **secondarySort**: par exemple définir un tri secondaire pour la réduire.
- **Trier**: un programme MapReduce qui trie les données écrites par le writer aléatoire
- **Sudoku**: un solveur de sudoku
- **teragen**: générer des données pour le terasort
- **terasort**: exécutez le terasort
- **teravalidate**: vérification des résultats de terasort
- **WordCount**: un programme MapReduce qui compte les mots contenus dans les fichiers d’entrée
- **wordmean**: un programme MapReduce qui compte de la longueur moyenne des mots dans les fichiers d’entrée
- **wordmedian**: un programme MapReduce combien médian de mots dans les fichiers d’entrée
- **wordstandarddeviation**: un programme MapReduce qui compte l’écart de la longueur des mots dans les fichiers d’entrée

**Code source**: code Source pour ces exemples est inclus dans le cluster de HDInsight à **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] La `2.2.4.9-1` dans le chemin d’accès est la version de la plate-forme de données Hortonworks pour l’HDInsight du cluster et peut changer dans HDInsight est mis à jour.

## <a name="how-to-run-the-samples"></a>Comment faire pour exécuter les exemples ##

1. Se connecter à HDInsight à l’aide de SSH comme décrit dans les articles suivants :

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À partir de la `username@#######:~$` invite, utilisez la commande suivante pour répertorier les exemples :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Cela génère la liste des exemples de la section précédente de ce document.

3. Utilisez la commande suivante pour obtenir de l’aide sur un exemple spécifique. Dans ce cas, l’exemple **wordcount** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Vous devez recevoir le message suivant :

        Usage: wordcount <in> [<in>...] <out>

    Cela signifie que vous pouvez fournir plusieurs tracés d’entrée pour les documents source. Le chemin d’accès final est l’endroit où est stockée la sortie (nombre de mots dans les documents source,).

4. Pour compter tous les mots dans les ordinateurs portables de Leonardo Da Vinci, qui sont fournies comme exemples de données avec votre cluster, utilisez ce qui suit :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Entrée pour cette tâche est lue à partir de **wasbs:///example/data/gutenberg/davinci.txt**.

    Sortie de cet exemple est stocké dans **wasbs : / / / exemple/données/davinciwordcount**.

    > [AZURE.NOTE] Comme indiqué dans l’aide de l’exemple wordcount, vous pouvez également spécifier plusieurs fichiers d’entrée. Par exemple, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` devrait compter des mots à la fois davinci.txt et ulysses.txt.

5. Une fois la tâche terminée, utilisez la commande suivante pour afficher la sortie :

        hdfs dfs -cat /example/data/davinciwordcount/*

    Il concatène tous les fichiers de sortie produits par le travail et de les afficher. Pour cet exemple de base n'est qu’un seul fichier, cependant s’il y avait plus cette commande serait une itération sur toutes les.

    La sortie est semblable au suivant :

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Chaque ligne représente un mot et le nombre de fois qu’il est survenu dans les données d’entrée.

## <a name="sudoku"></a>Sudoku

L’exemple de Sudoku comporte des instructions d’utilisation quelque peu inutile ; « Comprendre un puzzle sur la ligne de commande. »

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) est un puzzle logique constitué de 3 x neuf 3 grilles. Certaines des cellules de la grille ont des numéros tandis que d’autres sont vides, l’objectif consiste à résoudre pour les cellules vides. Le lien ci-dessus a plus d’informations sur le puzzle, mais l’objectif de cet exemple consiste à résoudre pour les cellules vides. Par conséquent, notre entrée doit être un fichier qui se trouve dans le format suivant :

- Neuf lignes de neuf colonnes

- Chaque colonne peut contenir un nombre ou `?` (qui indique une cellule vide)

- Cellules sont séparées par un espace.

Il existe une certaine manière pour construire les puzzles Sudoku ; Vous ne pouvez pas répéter un nombre dans une colonne ou une ligne. Il existe un exemple sur le cluster de HDInsight qui est correctement construit. Il se trouve dans **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** et contient les éléments suivants :

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] Le `2.2.4.9-1` partie du chemin d’accès peut changer lorsque des mises à jour sont apportées au cluster HDInsight.

Pour exécuter cet exemple à l’exemple de Sudoku, utilisez la commande suivante :

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Les résultats doivent apparaître comme suit :

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>PI (π)

L’exemple de pi utilise une statistique (quasi Monte Carlo) méthode d’estimation de la valeur de pi. Points placés de manière aléatoire à l’intérieur d’une unité relèvent carrée également un cercle inscrit dans ce carré avec une probabilité égale à l’aire du cercle, pi/4. La valeur de pi peut être estimée à partir de la valeur de 4R, où R est le rapport entre le nombre de points à l’intérieur du cercle et le nombre total de points qui sont dans le carré. Plus l’échantillon de points utilisés, plus l’estimation est.

Le mappeur de cet exemple génère un nombre de points au hasard à l’intérieur d’un carré de l’unité et le nombre de ces points situés à l’intérieur du cercle.

Le réducteur accumule des points comptés par les mappeurs de puis et des estimations de la valeur de pi à partir de la formule 4R, où R est le rapport entre le nombre de points comptés à l’intérieur du cercle et le nombre total de points qui sont dans le carré.

Pour exécuter cet exemple, utilisez la commande suivante. Cela utilise 16 cartes avec 10 000 000 échantillons chaque pour estimer la valeur de pi :

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

La valeur retournée par cette doit être similaire à **3.14159155000000000000**. Pour les références, les 10 premières décimales de pi sont 3.1415926535.

##<a name="10gb-greysort"></a>Greysort de 10 Go

GraySort est un tri de banc d’essai dont la métrique est le taux de tri (to/minute) qui est effectuée lors du tri de très grandes quantités de données, généralement un 100 To minimum.

Cet exemple utilise un modeste 10 Go de données afin qu’elle puisse être exécutée relativement rapidement. Il utilise les applications MapReduce développées par Owen O'Malley et Arun Murthy qui a remporté le banc d’essai du tri de téraoctets (« daytona ») à usage général annuel en 2009 avec un taux de 0.578 to/min (100 To 173 minutes). Pour plus d’informations sur cette opération et autres tests de tri, consultez le site [Sortbenchmark](http://sortbenchmark.org/) .

Cet exemple utilise trois ensembles de MapReduce programmes :

- **TeraGen**: un programme MapReduce qui génère des lignes de données à trier

- **TeraSort**: échantillonne les données d’entrée et utilise MapReduce pour trier les données dans un ordre total

    TeraSort est un tri standard de fonctions MapReduce, à l’exception d’un partitionneur personnalisé qui utilise une liste triée des clés N-1 échantillonnée qui définissent la plage de clés pour chaque réduction. En particulier, toutes les clés de ces exemples de [i-1] < = clé < exemple [i] sont envoyés pour réduire i. Cela garantie que les sorties de réduisent i est tous inférieur à la sortie de réduire i + 1.

- **TeraValidate**: un programme MapReduce qui vérifie que la sortie est globalement triée

    Il crée une carte par fichier dans le répertoire de sortie, et chaque mappage garantit que chaque clé est inférieure ou égale à la précédente. La fonction de mappage génère également des enregistrements des première et la dernières clés de chaque fichier, et la fonction de réduction permet de s’assurer que la première clé du fichier i est supérieure à la dernière clé d’i-1 de fichier. Les problèmes sont signalés comme résultat de la réduction avec les touches qui sont désordonnées.

Utilisez le code suivant suit pour générer des données, trier et valider la sortie :

1. Générer 10 Go de données, qui est stockées dans le stockage de par défaut du cluster HDInsight à **wasbs : / / / / données/10 Go-tri-entrée de l’exemple**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    Le `-Dmapred.map.tasks` Hadoop indique le nombre de tâches de mappage à utiliser pour cette tâche. Les deux paramètres finals indiquer à la tâche pour créer 10 Go de données et de les stocker à **wasbs : / / / / données/10 Go-tri-entrée de l’exemple**.

2. Pour trier les données, utilisez la commande suivante :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    Le `-Dmapred.reduce.tasks` indique Hadoop combien réduire les tâches à utiliser pour la tâche. Les deux paramètres finals sont simplement les emplacements d’entrée et de sortie des données.

3. Pour valider les données générées par l’ordre de tri, utilisez les éléments suivants :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Étapes suivantes ##

À partir de cet article, vous avez appris comment exécuter les exemples inclus dans les clusters basés sur Linux de HDInsight. Pour des didacticiels sur l’utilisation de porc, ruche et MapReduce avec HDInsight, consultez les rubriques suivantes :

* [Utilisez des porcs avec Hadoop sur HDInsight][hdinsight-use-pig]
* [Utilisez la ruche avec Hadoop sur HDInsight][hdinsight-use-hive]
* [Utilisez MapReduce avec Hadoop sur HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
