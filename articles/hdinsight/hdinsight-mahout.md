<properties
    pageTitle="Générer des recommandations à l’aide de Mahout et fonctionnant sous WIndows de HDInsight | Microsoft Azure"
    description="Apprenez à utiliser l’ordinateur Apache Mahout bibliothèque de formation pour générer des recommandations de film avec basé sur Windows HDInsight (Hadoop)."
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
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Générer des recommandations de la vidéo à l’aide de Apache Mahout avec Hadoop dans HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser l’ordinateur [Apache Mahout](http://mahout.apache.org) apprentissage avec Azure HDInsight pour produire des recommandations de film.

> [AZURE.NOTE] Les étapes décrites dans ce document nécessitent un client Windows et un cluster basé sur Windows de HDInsight. Pour plus d’informations sur l’utilisation de Mahout d’un Linux, OS X ou client Unix, avec un cluster basé sur Linux de HDInsight, consultez [recommandations de film de générer à l’aide de Mahout Apache avec basé sur Linux d’Hadoop dans HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>Vous apprendrez

Mahout est une [machine de formation] de[ ml] bibliothèque pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, telles que le filtrage, le classement et le clustering. Dans cet article, vous utiliserez un moteur de recommandation pour produire des recommandations de film qui sont basées sur vos amis ont vu des films. Vous allez également apprendre à effectuer des classifications par une forêt de la décision. Il vous apprendra les éléments suivants :

* Comment exécuter les tâches de Mahout à l’aide de Windows PowerShell

* Comment exécuter les tâches de Mahout à partir de la ligne de commande Hadoop

* Comment faire pour installer Mahout sur les clusters HDInsight 3.0 et 2.0 de HDInsight

    > [AZURE.NOTE] Mahout est fourni avec la version 3.1 de HDInsight des clusters. Si vous utilisez une version antérieure de HDInsight, reportez-vous à la section [Mahout d’installation](#install) avant de continuer.

##<a name="prerequisites"></a>conditions préalables

- **Cluster basé sur un Windows de Hadoop dans HDInsight**. Pour plus d’informations sur la création d’une, voir [mise en route à l’aide d’Hadoop dans HDInsight][getstarted]
- **Une station de travail avec PowerShell d’Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Générer des recommandations à l’aide de Windows PowerShell

> [AZURE.NOTE] Bien que la tâche est utilisée dans cette section fonctionne à l’aide de Windows PowerShell, de nombreuses classes fournies avec Mahout ne fonctionnent pas avec Windows PowerShell, et elles doivent s’exécuter à l’aide de la ligne de commande Hadoop. Pour obtenir une liste des classes qui ne fonctionnent pas avec Windows PowerShell, reportez-vous à la section de [dépannage](#troubleshooting) .
>
> Pour obtenir un exemple d’utilisation de la ligne de commande Hadoop pour exécuter des tâches de Mahout, reportez-vous à la section [classifier les données en utilisant la ligne de commande Hadoop](#classify).

L’une des fonctions fournies par Mahout est un moteur de recommandation. Ce moteur accepte les données dans le format de `userID`, `itemId`, et `prefValue` (la préférence aux utilisateurs pour l’article). Mahout peut ensuite analyser une occurrence pour déterminer : _les utilisateurs qui ont une préférence pour un article ont également une préférence pour ces autres éléments_. Mahout détermine ensuite les utilisateurs avec les préférences de type d’élément, qui peuvent être utilisés pour émettre des recommandations.

Voici un exemple très simple qui utilise des films :

* __Occurrence de collaboration__: Joe, Alice et Bob aimé _Star Wars_, _L’Empire grèves précédent_et le _retour de la Jedi_. Mahout détermine que les utilisateurs qui aiment également de l’un de ces films comme les deux autres.

* __Occurrence de collaboration__: Bob et Alice également été convaincus par _Vengeance de la Sith_, _l’attaque des Clones_et _La Menace du fantôme_. Mahout détermine qui utilisateurs aimé les films de trois précédentes également à ces trois.

* __Recommandation de similarité__: Joe car aimé les trois premiers films, Mahout examine que d’autres personnes avec les préférences similaires aimés films, mais Joe n’a pas observé (aimé/nominale). Dans ce cas, Mahout vous recommande de _Vengeance de la Sith_, _l’attaque des Clones_et _La Menace du fantôme_.

###<a name="understanding-the-data"></a>Présentation des données

Heureusement, [GroupLens recherche] [ movielens] fournit des données de contrôle d’accès pour les films dans un format qui est compatible avec Mahout. Ces données sont disponibles sur le stockage de votre cluster par défaut à `/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` (informations sur les films,) et `user-ratings.txt`. Le fichier ratings.txt de l’utilisateur est utilisé lors de l’analyse, alors que moviedb.txt est utilisé pour fournir des informations de texte convivial lors de l’affichage des résultats de l’analyse.

Les données contenues dans la ratings.txt de l’utilisateur ont une structure de `userID`, `movieID`, `userRating`, et `timestamp`, qui nous indique comment recommandé de chaque utilisateur avec un film. Voici un exemple des données :


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>Exécution de la tâche

Utiliser le script Windows PowerShell suivant pour exécuter une tâche qui utilise le moteur de recommandation Mahout avec les données vidéo :

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
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
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Mahout tâches ne pas suppriment les données temporaires qui sont créées lors du traitement de la tâche. Le `--tempDir` paramètre est spécifié dans le projet d’exemple pour isoler les fichiers temporaires dans un répertoire spécifique.

Le travail Mahout ne renvoie pas la sortie dans STDOUT. Au lieu de cela, elle est stockée dans le répertoire de sortie spécifié en tant que __partie-r-00000__. Le script transfère ce fichier vers __output.txt__ dans le répertoire en cours sur votre station de travail.

Voici un exemple du contenu de ce fichier :

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La première colonne est la `userID`. Les valeurs contenues dans ' [' et ']' sont `movieId`:`recommendationScore`.

###<a name="view-the-output"></a>Afficher la sortie

Bien que la sortie générée OK pour une utilisation dans une application, il n’est pas très lisible. Le `moviedb.txt` à partir du serveur peut être utilisé pour résoudre les `movieId` à un film nom, mais vous devez d’abord télécharger le fichier de contrôle d’accès du serveur à l’aide du script suivant :

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Une fois que vous avez téléchargé les fichiers, utilisez le script PowerShell suivant pour afficher des recommandations avec des noms de film :

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

Voici un exemple de l’exécution du script :

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

La sortie doit ressembler à ce qui suit :

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Classer des données en utilisant la ligne de commande Hadoop

Une des méthodes de classification disponibles avec Mahout consiste à créer une [forêt aléatoire][forest]. Il s’agit d’un processus en plusieurs étapes qui implique l’utilisation de données d’apprentissage pour générer des arbres de décision, qui sont ensuite utilisés pour classer les données. Cet exemple utilise la classe __org.apache.mahout.classifier.df.tools.Describe__ fournie par Mahout. Il doit actuellement être exécuté à l’aide de la ligne de commande Hadoop.

###<a name="load-the-data"></a>Charger les données

1. Téléchargez les fichiers suivants à partir de [l’ensemble de données NSL-KDD](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): le fichier de la formation

  * [KDDTest +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): les données de test

2. Ouvrez chaque fichier et supprimez les lignes dans la partie supérieure qui commencent par '@', , puis enregistrez les fichiers. Si elles ne sont pas supprimés, vous recevrez des messages d’erreur lors de l’utilisation de ces données avec Mahout.

2. Télécharger les fichiers __d’exemple de données__. Pour cela, à l’aide du script suivant. Remplacez __CLUSTERNAME__ par le nom du cluster HDInsight. Remplacez le nom de fichier par le nom fo le fichier à télécharger.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
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

###<a name="run-the-job"></a>Exécution de la tâche

1. Cette tâche nécessite de la ligne de commande Hadoop. Activer le Bureau à distance pour le cluster HDInsight, puis de s’y connecter en suivant les instructions de la section [se connecter à des clusters HDInsight utilisant le protocole RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Après la connexion, utilisez l’icône de la __ligne de commande Hadoop__ pour ouvrir la ligne de commande Hadoop :

    ![Hadoop cli][hadoopcli]

3. Utilisez la commande suivante pour générer le descripteur de fichier (__KDDTrain + .info__), qui utilise Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    Le `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` décrit les attributs des données dans le fichier. Par exemple, L indique une étiquette.

4. Créer une forêt d’arborescences de décision à l’aide de la commande suivante :

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Le résultat de cette opération est stocké dans le répertoire __nsl inter-forêts__ , qui se trouve dans le stockage de votre cluster de HDInsight à wasbs://user/ de __&lt;nom d’utilisateur > / nsl-forêt/nsl-forest.seq. Le &lt;nom d’utilisateur > est le nom d’utilisateur que vous avez utilisé pour votre session Bureau à distance. Ce fichier n’est pas lisible par l’homme.

5. Test de la forêt par classer le dataset __KDDTest + .arff__ . Utilisez la commande suivante :

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Cette commande renvoie le résumé du processus de classification des informations similaires à ce qui suit :

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  Cette tâche génère également un fichier situé dans __wasbs:///example/data/predictions/KDDTest+.arff.out__. Toutefois, ce fichier n’est pas lisible par l’homme.

> [AZURE.NOTE] Les tâches mahout ne pas remplacent les fichiers. Si vous souhaitez réexécuter ces tâches, vous devez supprimer les fichiers qui ont été créés par les tâches précédentes.

##<a name="troubleshooting"></a>Résolution des problèmes

###<a name="install"></a>Installation de Mahout

Mahout est installé sur des clusters de HDInsight 3.1, et il peut être installé manuellement sur des clusters HDInsight 3.0 ou HDInsight 2.1 en procédant comme suit :

1. La version de Mahout à utiliser dépend de la version HDInsight de votre cluster. Vous trouverez la version de cluster en affichant les propriétés du cluster dans le portail Azure.

  * __Pour le 2.1 de HDInsight__, vous pouvez télécharger un fichier d’Archive Java (JAR) contenant des [Mahout de 0,9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Pour 3.0 de HDInsight__, vous devez [créer Mahout à partir de la source de] [ build] et spécifier la version d’Hadoop fournie par HDInsight. Installer les composants requis répertoriés dans la page Générer, téléchargez le code source et puis utiliser la commande suivante pour créer les fichiers jar de Mahout :

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] Lorsque Mahout 1.0 est lancé, vous devez être en mesure d’utiliser les packages prégénérées avec HDInsight 3.0.

2. Télécharger le fichier jar dans __exemple/fichiers JAR__ dans le stockage par défaut pour votre cluster. Remplacez NOM_CLUSTER dans le script suivant avec le nom de votre cluster HDInsight et nom de fichier avec le chemin d’accès au fichier __mahout-cours-0,9-job.jar__ ...

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
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
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>Ne peut pas remplacer les fichiers

Mahout do de travaux pas nettoyer les fichiers temporaires créés au cours du traitement. En outre, les travaux n’écrasera pas un fichier de sortie existant.

Pour éviter des erreurs lors de l’exécution des tâches de Mahout, supprimer les fichiers temporaires et de sortie entre les exécutions, ou utiliser des noms de répertoire de temporaire et de sortie unique.

###<a name="cannot-find-the-jar-file"></a>Impossible de trouver le fichier JAR

Les clusters HDInsight 3.1 incluent Mahout. Le chemin d’accès et nom de fichier comprennent le numéro de version de Mahout qui est installé sur le cluster. L’exemple de script de Windows PowerShell dans ce didacticiel utilise un chemin d’accès valide à partir de novembre 2015, mais le numéro de version change dans les futures mises à jour à HDInsight. Pour déterminer le chemin d’accès actuel au fichier JAR de Mahout pour votre cluster, utilisez la commande Windows PowerShell suivante et puis modifier le script pour référencer le chemin d’accès du fichier qui est renvoyé :

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Classes qui ne fonctionnent pas avec Windows PowerShell

Mahout les travaux qui utilisent les classes suivantes renvoient les divers messages d’erreur s’ils sont utilisés à partir de Windows PowerShell :

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Pour exécuter les tâches qui utilisent ces classes, connectez-vous au cluster HDInsight et exécuter les tâches à l’aide de la ligne de commande Hadoop. Pour obtenir un exemple, reportez-vous à la section [classifier les données à l’aide de la ligne de commande Hadoop](#classify) .

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser Mahout, découvrez d’autres façons de travailler avec des données sur HDInsight :

* [La ruche avec HDInsight](hdinsight-use-hive.md)
* [Porcs avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
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
 
