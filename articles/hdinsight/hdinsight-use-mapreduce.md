<properties
   pageTitle="MapReduce avec Hadoop sur HDInsight | Microsoft Azure"
   description="Apprenez à exécuter des tâches de MapReduce sur Hadoop dans les clusters de HDInsight. Vous allez exécuter une opération de nombre de base word implémentée sous la forme d’un travail de Java MapReduce."
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

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Utilisez MapReduce dans Hadoop sur HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous apprendrez comment exécuter des tâches de MapReduce sur Hadoop dans les clusters de HDInsight. Nous s’exécuter d’une opération de nombre de base word implémentée sous la forme d’un travail de Java MapReduce.

##<a id="whatis"></a>Nouveautés MapReduce ?

Hadoop MapReduce est une infrastructure de logiciel pour l’écriture de tâches qui traitent de grandes quantités de données. Données d’entrée sont divisées en segments indépendants, qui sont ensuite traitées en parallèle sur les nœuds de votre cluster. Un travail MapReduce se composent de deux fonctions :

* **Mappeur**: utilise des données d’entrée, il analyse (généralement avec les opérations de tri et de filtre) et émet les tuples (paires clé-valeur)
* **Réducteur**: consomme les tuples émise par le Mappeur et effectue une opération de synthèse qui crée un résultat plus petit, combiné à partir des données du Mappeur

Un exemple de tâche de base word count MapReduce est illustré dans le diagramme suivant :

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

Le résultat de ce travail est un décompte du nombre de fois où chaque mot s’est produite dans le texte qui a été analysé.

* Le Mappeur prend chaque ligne du texte d’entrée en tant qu’entrée et la divise en mots. Il émet une clé/valeur paire chaque fois un mot se produit du mot est suivi par un 1. La sortie est triée avant de l’envoyer sur reducer.

* Réducteur de la somme de ces nombres individuels pour chaque mot et émet une paire clé/valeur unique qui contient le mot suivi par la somme de ses occurrences.

MapReduce peut être implémenté dans une variété de langues. Java est l’implémentation la plus courante et est utilisé à des fins de démonstration dans ce document.

### <a name="hadoop-streaming"></a>Hadoop en continu

Langues ou des infrastructures qui sont basés sur Java et la Machine virtuelle Java (par exemple, Scalding ou en cascade,) peut être exécuté directement comme MapReduce, similaire à une application Java. D’autres, tels que C# ou Python ou des exécutables autonomes, doivent utiliser Hadoop de diffusion en continu.

Hadoop streaming communique avec le Mappeur et le réducteur de STDIN et STDOUT - le Mappeur réducteur de lire les données une ligne à la fois de STDIN et écrire la sortie dans STDOUT. Chaque ligne lire ou émise par le Mappeur et le réducteur doit être sous la forme d’une paire clé/valeur, délimitée par une charaacter d’onglet :

    [key]/t[value]

Pour plus d’informations, reportez-vous à la section [Hadoop de diffusion en continu](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Pour obtenir des exemples d’utilisation d’Hadoop en continu avec HDInsight, consultez les rubriques suivantes :

* [Développer les Python MapReduce travaux](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>À propos de l’exemple de données

Dans cet exemple, pour les données d’exemple, vous allez utiliser les ordinateurs portables de Leonardo Da Vinci, qui sont fournis sous la forme d’un document texte dans votre cluster HDInsight.

Les exemples de données sont stockées dans le stockage Azure Blob HDInsight utilise comme système de fichiers par défaut pour les clusters d’Hadoop. HDInsight peut accéder aux fichiers stockés dans le stockage Blob en utilisant le préfixe **wasb** . Par exemple, pour accéder au fichier exemple.log, vous utiliseriez la syntaxe suivante :

    wasbs:///example/data/gutenberg/davinci.txt

Stockage des objets Blob Azure étant le stockage par défaut pour les HDInsight, vous pouvez également accéder le fichier à l’aide de **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] Dans la syntaxe précédente, **wasbs : / / /** est utilisée pour accéder aux fichiers qui sont stockés dans le conteneur de stockage par défaut pour votre cluster HDInsight. Si vous avez spécifié des comptes de stockage supplémentaires lorsque vous configuré votre cluster, et que vous souhaitez accéder aux fichiers stockés dans ces comptes, vous pouvez accéder aux données en spécifiant l’adresse compte du stockage et du nom de conteneur. Par exemple, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>À propos de l’exemple MapReduce

Le travail de MapReduce est utilisé dans cet exemple se trouve dans **wasbs://example/jars/hadoop-mapreduce-examples.jar**, et il est fourni avec votre cluster HDInsight. Contient un exemple de nombre de word que vous exécuterez par rapport à **davinci.txt**.

> [AZURE.NOTE] Sur les clusters de HDInsight 2.1, l’emplacement du fichier est **wasbs:///example/jars/hadoop-examples.jar**.

Pour référence, voici le code Java pour le travail de MapReduce word count :

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

      public static class TokenizerMapper
           extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
          StringTokenizer itr = new StringTokenizer(value.toString());
          while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
          }
        }
      }

      public static class IntSumReducer
           extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
                           ) throws IOException, InterruptedException {
          int sum = 0;
          for (IntWritable val : values) {
            sum += val.get();
          }
          result.set(sum);
          context.write(key, result);
        }
      }

      public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
          System.err.println("Usage: wordcount <in> <out>");
          System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
      }
    }

Pour obtenir des instructions écrire votre propre tâche MapReduce, voir [programmes de développer les MapReduce Java pour HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Exécuter le MapReduce

HDInsight pouvez exécuter des tâches de HiveQL à l’aide de diverses méthodes. Utilisez le tableau suivant pour décider quelle méthode vous consiste, puis cliquez sur le lien pour une procédure pas à pas.

| **Utilisez cette option**...                                                    | **.. .pour cela**                                       | .. .avec ce **système d’exploitation en cluster** | ... contre ce **système d’exploitation client** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | Utilisez la commande Hadoop via **SSH**                  | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md)                     | Soumettre de la tâche à distance à l’aide de **repos**               | Linux ou Windows                          | Linux, Unix, Mac OS X ou Windows        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Envoi de la tâche à distance à l’aide **De Windows PowerShell** | Linux ou Windows                          | Windows                                  |
| [Bureau à distance](hdinsight-hadoop-use-mapreduce-remote-desktop)    | Utilisez la commande Hadoop via **Bureau à distance**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>Étapes suivantes

Bien que MapReduce offre de puissantes fonctionnalités de diagnostics, il peut être un peu difficile au masque. Il existe plusieurs infrastructures basée sur Java qui vous permet de définir les applications MapReduce, ainsi que des technologies de porcs et de ruche, qui fournissent un moyen plus simple de travailler avec des données dans HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Développer les programmes Java MapReduce pour HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Développer les Python MapReduce programmes de diffusion en continu de HDInsight](hdinsight-hadoop-streaming-python.md)

* [Développer les travaux d’échaudage de MapReduce avec Hadoop Apache sur HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Utilisez la ruche avec HDInsight][hdinsight-use-hive]

* [Utilisez des porcs avec HDInsight][hdinsight-use-pig]

* [Exécuter les exemples de HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
