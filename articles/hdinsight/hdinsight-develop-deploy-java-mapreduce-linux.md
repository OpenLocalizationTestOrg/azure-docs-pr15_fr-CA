<properties
    pageTitle="Développer des programmes de Java MapReduce pour basé sur Linux de HDInsight | Microsoft Azure"
    description="Apprenez à développer des programmes Java MapReduce et déployez-les sur HDInsight de fonctionnant sous Linux."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Développer les programmes Java MapReduce pour Hadoop sur HDInsight Linux

Ces documents vous guide tout au long d’à l’aide de Apache Maven pour créer une application MapReduce, puis déployer et exécuter sur un Hadoop sur HDInsight cluster fonctionnant sous Linux.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou version ultérieure (ou un équivalent, par exemple, OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Un abonnement Azure**

- **CLI Azure**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>Configurer les variables d’environnement

Les variables d’environnement peuvent être définies lors de l’installation de Java et du JDK. Toutefois, vous devez vérifier qu’ils existent et qu’ils contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** - doit pointer vers le répertoire où est installé l’environnement d’exécution Java (JRE). Par exemple, dans un système OS X, Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Dans Windows, il aurait une valeur semblable à`c:\Program Files (x86)\Java\jre1.7`

* **Chemin d’accès** : doit contenir les chemins d’accès suivants :

    * **JAVA_HOME** (ou le chemin d’accès équivalent)

    * **JAVA_HOME\bin** (ou le chemin d’accès équivalent)

    * Le répertoire d’installation de Maven

##<a name="create-a-new-maven-project"></a>Créez un nouveau projet Maven

1. À partir d’une session Terminal Server, ou de la ligne de commande dans votre environnement de développement, accédez à l’emplacement que vous souhaitez stocker ce projet.

3. Utilisez la commande __mvn__ , qui est installée avec Maven, pour générer de la génération de modèles automatique pour le projet.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Cela créera un nouveau répertoire dans le répertoire en cours, avec le nom spécifié par le paramètre __artifactID__ (**wordcountjava** dans cet exemple). Ce répertoire contiendra les éléments suivants :

    * __pom.xml__ - le [Modèle d’objet projet (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) qui contient les détails de configuration et les informations utilisées pour générer le projet.

    * __src__ - le répertoire qui contient le répertoire __java/main/org/hadoop/apache/exemples__ , vous allez créer l’application.

3. Supprimez le fichier __src/test/java/org/apache/hadoop/examples/apptest.java__ , qu’il ne sera pas utilisé dans cet exemple.

##<a name="add-dependencies"></a>Ajouter des dépendances

1. Modifiez le fichier __pom.xml__ et ajoutez le code suivant à l’intérieur de la `<dependencies>` section :

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    Cette option indique à Maven que le projet requiert les bibliothèques (répertoriés dans &lt;artifactId\>) avec une version spécifique (répertoriés dans &lt;version\>). Au moment de la compilation, il sera téléchargé à partir du référentiel de Maven par défaut. Vous pouvez utiliser la [recherche de référentiel Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) pour afficher d’autres.

    Le `<scope>provided</scope>` indique à Maven que ces dépendances ne doivent pas être empaquetés avec l’application, comme ils sont fournis par le cluster HDInsight au moment de l’exécution.

2. Ajoutez le code suivant du fichier __pom.xml__ . Il doit être à l’intérieur de la `<project>...</project>` balises dans le fichier ; par exemple, entre `</dependencies>` et `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    Le plug-in premier configure le [Plug-in de teinte Maven](http://maven.apache.org/plugins/maven-shade-plugin/), qui est utilisé pour créer un uberjar (parfois appelé un fatjar), qui contient les dépendances requises par l’application. Il empêche également la duplication des licences dans le package jar, ce qui peut provoquer des problèmes sur certains systèmes.

    Le plug-in deuxième configure le compilateur de Maven, qui est utilisé pour définir la version de Java requis par cette application vers la version utilisée sur le cluster de HDInsight.

3. Enregistrez le fichier __pom.xml__ .

##<a name="create-the-mapreduce-application"></a>Créer l’application MapReduce

1. Accédez au répertoire __wordcountjava/src/main / / org/apache/hadoop/exemples java__ et renommez le fichier __App.java__ __WordCount.java__.

2. Ouvrez le fichier __WordCount.java__ dans un éditeur de texte et remplacez le contenu par le texte suivant :

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

    Notez le nom du package est **org.apache.hadoop.examples** et le nom de classe est **WordCount**. Vous allez utiliser ces noms lorsque vous soumettez le travail MapReduce.

3. Enregistrez le fichier.

##<a name="build-the-application"></a>Générer l’application

1. Accédez au répertoire __wordcountjava__ , si vous n’avez pas encore fait.

2. Utilisez la commande suivante pour générer un fichier JAR contenant l’application :

        mvn clean package

    Cela va nettoyer tout artefact de génération précédente, télécharger toutes les dépendances qui n’ont pas déjà été installés, puis générer et empaqueter l’application.

3. Une fois la commande terminée, le répertoire __wordcountjava/cible__ contient un fichier nommé __wordcountjava-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] Le fichier __wordcountjava-1.0-SNAPSHOT.jar__ est un uberjar, qui contient non seulement la WordCount travail, mais également les dépendances requises par la tâche lors de l’exécution.


##<a id="upload"></a>Télécharger le fichier jar.

Pour télécharger le fichier jar à l’headnode de HDInsight, utilisez la commande suivante :

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Il copie les fichiers sur le système local sur le nœud de tête.

> [AZURE.NOTE] Si vous avez utilisé un mot de passe pour sécuriser votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser le `-i` paramètre et le chemin d’accès à la clé privée. Par exemple, `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Exécuter la tâche MapReduce

1. Se connecter à HDInsight à l’aide de SSH comme décrit dans les articles suivants :

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À partir de la session SSH, utilisez la commande suivante pour exécuter l’application MapReduce :

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    Cela permet de compter les mots dans le fichier davinci.txt l’application WordCount MapReduce et stocker les résultats de __wasbs : / / / exemple/données/wordcountout__. Le fichier d’entrée et sortie sont stockés sur le stockage par défaut pour le cluster.

3. Une fois la tâche terminée, utilisez ce qui suit pour afficher les résultats :

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    Vous devriez recevoir une liste de mots et de nombres, incluant des valeurs similaires à ce qui suit :

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>Étapes suivantes

Dans ce document, vous avez appris comment développer un projet Java MapReduce. Consultez les documents suivants pour les autres façons de travailler avec HDInsight.

- [Utilisez la ruche avec HDInsight][hdinsight-use-hive]
- [Utilisez des porcs avec HDInsight][hdinsight-use-pig]
- [Utilisez MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations, consultez également le [Centre pour développeurs Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

