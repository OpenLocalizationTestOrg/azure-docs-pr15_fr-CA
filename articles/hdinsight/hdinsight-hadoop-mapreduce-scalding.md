<properties
 pageTitle="Développer les travaux d’échaudage de MapReduce avec Maven | Microsoft Azure"
 description="Découvrez comment utiliser Maven pour créer un travail MapReduce d’échaudage, puis de déployer et d’exécuter la tâche sur un Hadoop sur cluster de HDInsight."
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
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Développer les travaux d’échaudage de MapReduce avec Hadoop Apache sur HDInsight

D’échaudage est une bibliothèque de Scala qui permet de facilement créer des travaux Hadoop MapReduce. Il offre une syntaxe concise, ainsi que l’intégration étroite avec Scala.

Dans ce document, découvrez comment utiliser Maven pour créer un travail de MapReduce de nombre de base word écrit dans Scalding. Ensuite, vous apprendrez comment déployer et exécuter cette tâche sur un cluster HDInsight.

## <a name="prerequisites"></a>Conditions préalables

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Hadoop sur cluster de HDInsight la base A Windows ou Linux**. Pour plus d’informations, reportez-vous à la section [basé sur Linux de fourniture d’Hadoop sur HDInsight](hdinsight-hadoop-provision-linux-clusters.md) ou [fonctionnant sous Windows de fourniture d’Hadoop sur HDInsight](hdinsight-provision-clusters.md) .

* **[Maven](http://maven.apache.org/)**

* **[Plate-forme de Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou version ultérieure**

## <a name="create-and-build-the-project"></a>Créez et générez le projet

1. Utilisez la commande suivante pour créer un nouveau projet Maven :

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Cette commande va créer un nouveau répertoire nommé **scaldingwordcount**et créer la génération de modèles automatique pour une application Scala.

2. Dans le répertoire **scaldingwordcount** , ouvrez le fichier **pom.xml** et remplacez le contenu par le texte suivant :

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Ce fichier décrit le projet, les dépendances et les plug-ins. Vous trouverez ici les entrées les plus importantes :

    * **Maven.Compiler.source** et **maven.compiler.target**: définit la version de Java pour ce projet

    * **référentiels**: les référentiels contenant les fichiers de dépendance utilisés par ce projet

    * **core_2.11 d’échaudage** et **hadoop-core**: ce projet dépend à la fois Scalding et Hadoop packages de base

    * **Maven-scala plug-in**: plug-in pour compiler des applications de scala

    * **Maven-ombre plug-in**: plug-in pour créer ombré POTS (fat). Ce plug-in s’applique les filtres et transformations ; specificially :

        * **filtres**: les filtres appliqués modifier les informations de métadonnées incluses avec dans le fichier jar. Pour empêcher la signature des exceptions lors de l’exécution, cela exclut les divers fichiers de signature qui peuvent être inclus dans les dépendances.

        * **exécutions**: la configuration de l’exécution de phase de package spécifie la classe **com.twitter.scalding.Tool** en tant que la classe principale pour le package. Sans cela, vous devez spécifier com.twitter.scalding.Tool, ainsi que la classe qui contient la logique d’application, lors de l’exécution de la tâche avec la commande hadoop.

3. Supprimer le répertoire **src/test** , que vous n’allez pas créer des tests avec cet exemple.

4. Ouvrez le fichier **src/main/scala/com/microsoft/example/App.scala** et remplacez le contenu par le texte suivant :

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Il implémente un travail de nombre de base word.

5. Enregistrez et fermez les fichiers.

6. Pour générer et empaqueter l’application, utilisez la commande suivante à partir du répertoire **scaldingwordcount** :

        mvn package

    Une fois cette tâche terminée, vous pouvez trouver le package qui contient l’application WordCount à **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Exécuter le travail sur un cluster Linux

> [AZURE.NOTE] Les étapes suivantes utilisent le SSH et la commande Hadoop. Pour les autres méthodes d’exécution des travaux de MapReduce, voir [Utilisation des MapReduce dans Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

1. Télécharger le package à votre cluster HDInsight, utilisez la commande suivante :

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Il copie les fichiers sur le système local sur le nœud de tête.

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour sécuriser votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser le `-i` paramètre et le chemin d’accès à la clé privée. Par exemple,`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Pour se connecter au nœud principal de cluster, utilisez la commande suivante :

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour sécuriser votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser le `-i` paramètre et le chemin d’accès à la clé privée. Par exemple,`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Une fois connecté au nœud principal, utilisez la commande suivante pour exécuter la tâche de comptage de word

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    Cette commande exécute la classe WordCount que vous implémentée précédemment. `--hdfs`Indique à la tâche d’utiliser très. `--input`Spécifie le fichier texte d’entrée, tandis que `--output` Spécifie l’emplacement de sortie.

4. Une fois la tâche terminée, utilisez ce qui suit pour afficher la sortie.

        hdfs dfs -text wasbs:///example/wordcountout/*

    Informations semblables au suivant s’affiche :

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Exécuter le travail sur un cluster Windows

Les étapes suivantes utilisent Windows PowerShell. Pour les autres méthodes d’exécution des travaux de MapReduce, voir [Utilisation des MapReduce dans Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Démarrer PowerShell d’Azure et de journal dans votre compte Azure. Après avoir fourni vos informations d’identification, la commande renvoie des informations sur votre compte.

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Si vous disposez de plusieurs abonnements, fournissez l’id d’abonnement que vous souhaitez utiliser pour le déploiement.

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Vous pouvez utiliser `Get-AzureRMSubscription` pour obtenir une liste de tous les abonnements associés à votre compte, ce qui inclut l’Id de l’abonnement pour chacun d’eux.

4. Utilisez le script suivant pour télécharger et exécuter la tâche WordCount. Remplacer `CLUSTERNAME` avec le nom de votre HDInsight de cluster et vérifiez que l’option `$fileToUpload` est le chemin d’accès correct au fichier __1.0-scaldingwordcount-SNAPSHOT.jar__ .

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Lorsque vous exécutez le script, vous êtes invité à entrer le nom d’utilisateur admin et le mot de passe pour votre cluster HDInsight. Toutes les erreurs qui se produisent pendant l’exécution de la tâche sont enregistrées dans la console.
     
6. Une fois le travail terminé, la sortie sera téléchargée dans fichier __output.txt__ dans le répertoire en cours. Utilisez la commande suivante pour afficher les résultats.

        cat output.txt

    Le fichier doit contenir des valeurs similaires à ce qui suit :

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser Scalding pour créer les travaux de MapReduce pour HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec HDInsight d’Azure.

* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)

* [Utiliser des travaux de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
