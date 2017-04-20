<properties
    pageTitle="Analyser les données Twitter avec Hadoop dans HDInsight | Microsoft Azure"
    description="Apprenez à utiliser la ruche pour analyser les données Twitter sur Hadoop dans HDInsight pour trouver la fréquence d’utilisation d’un mot particulier."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analyser les données de Twitter à l’aide de la ruche dans HDInsight

Sites Web sociaux sont parmi les forces majeures conduites pour adoption de données volumineuses. Les API publiques fournies par des sites tels que Twitter sont une source utile de données d’analyse et de présentation de tendances les plus courants. Dans ce didacticiel, vous obtenez un Tweet à l’aide d’un API de transmission en continu de Twitter et puis utilisez la ruche Apache Azure HDInsight pour obtenir la liste des utilisateurs de Twitter qui a envoyé le plus tweet contenant un certain mot.

> [AZURE.NOTE] Les étapes de ce document requièrent un cluster basé sur Windows de HDInsight. Pour connaître les étapes spécifiques à un cluster basé sur Linux, voir [analyser Twitter de données à l’aide de la ruche dans HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).


##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Une station de travail** avec Azure PowerShell installé et configuré. 

    Pour exécuter les scripts Windows PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et la valeur de la stratégie d’exécution *RemoteSigned*. Consultez [les scripts de Windows PowerShell exécuter][powershell-script].

    Avant d’exécuter les scripts Windows PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Login-AzureRmAccount

    Si vous avez plusieurs abonnements d’Azure, utilisez l’applet de commande suivante pour définir l’abonnement actuel :

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **HDInsight d’Azure un cluster**. Pour obtenir des instructions sur la configuration de cluster, consultez [mise en route de HDInsight] [ hdinsight-get-started] ou [clusters de disposition HDInsight] [hdinsight-provision]. Vous devez le nom de cluster plus loin dans le didacticiel.

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

Fichiers|Description
---|---
/Tutorials/Twitter/Data/tweets.txt|Les données de la source de la tâche de la ruche.
/Tutorials/Twitter/Output|Le dossier de sortie pour le travail de la ruche. Le nom de fichier de sortie par défaut ruche de travail est **000000_0**.
Tutorials/Twitter/Twitter.hql|Le fichier de script HiveQL.
/Tutorials/Twitter/JobStatus|L’état du travail Hadoop.


##<a name="get-twitter-feed"></a>Twitter Get d’alimentation

Dans ce didacticiel, vous allez utiliser l' [API de transmission en continu de Twitter][twitter-streaming-api]. Spécifique Twitter de diffusion en continu des API que vous utilisez est [statuts/filtre][twitter-statuses-filter].

>[AZURE.NOTE] Un fichier contenant les 10 000 tweet et le fichier de script de ruche (traité dans la section suivante) ont été téléchargés dans un conteneur d’objet Blob public. Vous pouvez ignorer cette section si vous souhaitez utiliser les fichiers téléchargés.

[Données de tweet](https://dev.twitter.com/docs/platform-objects/tweets) sont stockées dans le format de Notation JSON (JavaScript Object) qui contient une structure imbriquée complexe. Au lieu d’écrire le nombre de lignes de code à l’aide d’un langage de programmation conventionnel, vous pouvez transformer cette structure imbriquée dans une table de la ruche, afin qu’il peut être interrogé par un de langage SQL (SQL)-comme langage appelé HiveQL.

Twitter utilise OAuth pour fournir un accès autorisé à son API. OAuth est un protocole d’authentification qui permet aux utilisateurs d’approuver des applications d’agir en leur nom, sans partager leur mot de passe. Plus d’informations vous trouverez à [oauth.net](http://oauth.net/) ou dans le [Guide du débutant pour OAuth](http://hueniverse.com/oauth/) d’excellente qualité à partir de Hueniverse.

La première étape pour utiliser OAuth est de créer une nouvelle application sur le site des développeurs de Twitter.

**Pour créer une application Twitter**

1. Se connecter à [https://apps.twitter.com/](https://apps.twitter.com/). Si vous n’avez pas un compte Twitter, cliquez sur le lien **Je veux m’inscrire maintenant** .
2. Cliquez sur **créer une nouvelle application**.
3. Entrez le **nom**, la **Description**, **site Web**. Vous pouvez compléter une champ URL pour le **site Web** . Le tableau suivant montre des exemples de valeurs à utiliser :

    Champ|Valeur
    ---|---
    Nom|MyHDInsightApp
    Description|MyHDInsightApp
    Site Web|http://www.myhdinsightapp.com

4. Cochez **Oui, j’accepte**, puis cliquez sur **créer votre application Twitter**.
5. Cliquez sur l’onglet **autorisations** . L’autorisation par défaut est **en lecture seule**. Cela est suffisant pour ce didacticiel.
6. Cliquez sur l’onglet **clés et jetons d’accès** .
7. Cliquez sur **créer mon jeton d’accès**.
8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.
9. Notez la **clé de consommateur**, **secret de consommateur**, **jeton d’accès**et **secret de jeton d’accès**. Vous aurez besoin des valeurs plus loin dans le didacticiel.

Dans ce didacticiel, vous allez utiliser Windows PowerShell pour appeler le service web. Pour un exemple .NET C#, consultez [analyse en temps réel sentiment de Twitter avec HBase de HDInsight][hdinsight-hbase-twitter-sentiment]. L’autre outil populaire pour effectuer des appels de service web est le [*coin*][curl]. Curl peut être téléchargé à partir [d’ici][curl-download].

>[AZURE.NOTE] Lorsque vous utilisez la commande curl dans Windows, utilisez des guillemets doubles plutôt que des guillemets simples pour les valeurs d’option.

**Pour obtenir d’un Tweet**

1. Ouvrez le Windows PowerShell script environnement intégré (ISE). (Sur l’écran de démarrage de Windows 8, tapez **PowerShell_ISE** , puis sur **Windows PowerShell ISE**. Voir [démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start].)

2. Copiez le script suivant dans la fenêtre de script :

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. Définissez des variables de cinq à huit le premier dans le script :


    Variable|Description
    ---|---
    $clusterName|C’est le nom du cluster HDInsight où vous souhaitez exécuter l’application.
    oauth_consumer_key de $|Il s’agit de la de l’application Twitter **clé de consommateur** que vous avez noté précédemment lorsque vous avez créé l’application Twitter.
    oauth_consumer_secret de $|Il s’agit de la d’application Twitter **secret de consommateur** notés précédemment.
    oauth_token de $|Il s’agit d' application Twitter **jeton d’accès** que vous avez indiqué plus haut.
    oauth_token_secret de $|Il s’agit de la d’application Twitter **secret de jeton d’accès** que vous avez indiqué précédemment.
    $destBlobName|Il s’agit du nom du blob de sortie. La valeur par défaut est **tutorials/twitter/data/tweets.txt**. Si vous modifiez la valeur par défaut, vous devez mettre à jour les scripts Windows PowerShell en conséquence.
    $trackString|Le service web retourne tweet liés à ces mots-clés. La valeur par défaut est **Azure, nuage, HDInsight**. Si vous modifiez la valeur par défaut, vous mettrez à jour les scripts Windows PowerShell en conséquence.
    $lineMax|La valeur détermine combien de tweet lit le script. Il prend environ trois minutes pour lire 100 Tweet. Vous pouvez définir un plus grand nombre, mais il faudra plus de temps pour la télécharger.

5. Appuyez sur **F5** pour exécuter le script. Si vous rencontrez des problèmes, comme une solution de contournement, sélectionnez toutes les lignes et appuyez sur la touche **F8**.
6. Vous veillent « Terminé ! » à la fin de la sortie. Des messages d’erreur seront affichera en rouge.

Comme une procédure de validation, vous pouvez vérifier le fichier de sortie, **/tutorials/twitter/data/tweets.txt**, sur votre stockage Azure Blob à l’aide d’un Explorateur de stockage Azure ou Azure PowerShell. Pour un exemple de script de Windows PowerShell pour la liste de fichiers, consultez [stockage Blob d’utilisation avec HDInsight][hdinsight-storage-powershell].



##<a name="create-hiveql-script"></a>Créer un script de HiveQL

À l’aide de PowerShell d’Azure, vous pouvez exécuter plusieurs instructions HiveQL un à la fois, ou l’instruction HiveQL dans un fichier de script du package. Dans ce didacticiel, vous allez créer un script de HiveQL. Le fichier de script doit être téléchargé pour le stockage des objets Blob Azure. Dans la section suivante, vous exécuterez le fichier de script à l’aide de PowerShell d’Azure.

>[AZURE.NOTE] Le fichier de script de ruche et un fichier contenant les 10 000 tweet ont été téléchargés dans un conteneur d’objet Blob public. Vous pouvez ignorer cette section si vous souhaitez utiliser les fichiers téléchargés.

Le script HiveQL effectuera les opérations suivantes :

1. **Supprimer la table de tweets_raw** dans le cas de la table existe déjà.
2. **Créer la table de la ruche tweets_raw**. Cette ruche temporaire structurée de tableau conserve les données d’autres extraire, transformer et charger le traitement (ETL). Pour plus d’informations sur les partitions, consultez le [didacticiel de la ruche][apache-hive-tutorial].  
3. **Charger les données** à partir du dossier source, /tutorials/twitter/data. Le dataset tweet grand format de JSON imbriqués a maintenant été transformé en une structure de table temporaire ruche.
3. **Supprimer la table tweet** dans le cas de la table existe déjà.
4. **Créer la table tweet**. Avant de pouvoir interroger contre tweet dataset à l’aide de ruche, vous devez exécuter un autre processus ETL. Ce processus ETL définit un schéma de table plus détaillé pour les données que vous avez stockés dans la table « twitter_raw ».  
5. **Insérer une table de remplacement**. Ce script complexe de la ruche va lancer un ensemble de travaux de MapReduce long par le cluster Hadoop. En fonction de votre groupe de données et la taille de votre cluster, cela peut prendre 10 minutes environ.
6. **Insert remplace le répertoire**. Exécuter une requête et le groupe de données dans un fichier de sortie. Cette requête retourne une liste d’utilisateurs Twitter qui a envoyé la plupart des tweet contenant le mot « Azure ».

**Pour créer un script de la ruche et le télécharger vers Azure**

1. Ouvrez Windows PowerShell ISE.
2. Copiez le script suivant dans la fenêtre de script :

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. Définissez les deux premières variables dans le script :

    Variable|Description
    ---|---
    $clusterName|Entrez le nom du cluster HDInsight où vous souhaitez exécuter l’application.
    $subscriptionID|Entrez votre ID d’abonnement Azure.
    $sourceDataPath|L’emplacement de stockage Azure Blob où les requêtes de ruche lit les données à partir de. Vous n’avez pas besoin de modifier cette variable.
    $outputPath|L’emplacement de stockage Azure Blob où les requêtes de ruche aura pour résultat les résultats. Vous n’avez pas besoin de modifier cette variable.
    $hqlScriptFile|L’emplacement et le nom de fichier du fichier de script HiveQL. Vous n’avez pas besoin de modifier cette variable.

5. Appuyez sur **F5** pour exécuter le script. Si vous rencontrez des problèmes, comme une solution de contournement, sélectionnez toutes les lignes et appuyez sur la touche **F8**.
6. Vous veillent « Terminé ! » à la fin de la sortie. Des messages d’erreur seront affichera en rouge.

Comme une procédure de validation, vous pouvez vérifier le fichier de sortie, **/tutorials/twitter/twitter.hql**, sur votre stockage Azure Blob à l’aide d’un Explorateur de stockage Azure ou Azure PowerShell. Pour un exemple de script de Windows PowerShell pour la liste de fichiers, consultez [stockage Blob d’utilisation avec HDInsight][hdinsight-storage-powershell].  


##<a name="process-twitter-data-by-using-hive"></a>Traiter les données à l’aide de ruche Twitter

Vous avez terminé tout le travail de préparation. Maintenant, vous pouvez appeler le script de la ruche et vérifier les résultats.

### <a name="submit-a-hive-job"></a>Soumettre une tâche de la ruche

Utiliser le script Windows PowerShell suivant pour exécuter le script de la ruche. Vous devez définir la première variable.

>[AZURE.NOTE] Pour utiliser le tweet et le script HiveQL que vous avez chargé dans les deux dernières sections, définissez $hqlScriptFile sur « / tutorials/twitter/twitter.hql ». Pour utiliser ceux qui ont été téléchargés dans un blob public pour vous, la valeur $hqlScriptFile "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>Vérifier les résultats

Utiliser le script Windows PowerShell suivant pour vérifier la sortie de projet ruche. Vous devez définir les deux premières variables.

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] La table de la ruche utilise \001 comme délimiteur de champ. Le délimiteur n’est pas visible dans la sortie.

Une fois les résultats d’analyse ont été placés dans le stockage Azure Blob, vous pouvez exporter les données vers un serveur de base de données/SQL Azure SQL, exporter les données vers Excel à l’aide de requêtes de puissance ou connecter votre application aux données à l’aide du pilote ODBC de la ruche. Pour plus d’informations, consultez [Utilisation des Sqoop avec HDInsight][hdinsight-use-sqoop], [analyse des données de retard vol à l’aide de HDInsight][hdinsight-analyze-flight-delay-data], [Excel de se connecter à HDInsight avec alimentation requête][hdinsight-power-query]et [Excel de se connecter à HDInsight avec le pilote ODBC de Microsoft la ruche][hdinsight-hive-odbc].

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons vu comment transformer un groupe de données non structurée JSON dans une table de ruche structurée à interroger, Explorer et analyser les données à partir de Twitter sur Azure à l’aide de HDInsight. Pour plus d’informations, voir :

- [Mise en route de HDInsight][hdinsight-get-started]
- [Analyse en temps réel sentiment Twitter avec HBase dans HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analyser des données de retard de vol avec HDInsight][hdinsight-analyze-flight-delay-data]
- [Excel de se connecter à HDInsight avec la requête de l’alimentation][hdinsight-power-query]
- [Excel de se connecter à HDInsight avec le pilote ODBC de Microsoft ruche][hdinsight-hive-odbc]
- [Utilisez Sqoop avec HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
