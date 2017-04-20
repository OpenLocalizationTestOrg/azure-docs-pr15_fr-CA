<properties
    pageTitle="Exécution d’une tâche d’Hadoop à l’aide de DocumentDB et HDInsight | Microsoft Azure"
    description="Découvrez comment exécuter un travail de ruche, porc et MapReduce simple avec DocumentDB et HDInsight d’Azure."
    services="documentdb"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>


<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="denlee"/>

#<a name="DocumentDB-HDInsight"></a>Exécution d’une tâche d’Hadoop à l’aide de DocumentDB et HDInsight

Ce didacticiel vous montre comment exécuter [La ruche Apache][apache-hive], [Apache porc][apache-pig]et [Apache Hadoop] [ apache-hadoop] travaux de MapReduce sur Azure HDInsight avec connecteur d’Hadoop de DocumentDB. Connecteur d’Hadoop de DocumentDB permet de DocumentDB en tant qu’à la fois une source et un récepteur pour les travaux de ruche, porc et MapReduce. Ce didacticiel utilise DocumentDB comme la source de données et la destination pour les travaux d’Hadoop.

À la fin de ce didacticiel, vous serez en mesure de répondre aux questions suivantes :

- Comment charger des données à partir de DocumentDB à l’aide d’une tâche de la ruche, porc ou MapReduce ?
- Comment stocker des données dans DocumentDB à l’aide d’une tâche de la ruche, porc ou MapReduce ?

Nous vous recommandons de mise en route en regardant la vidéo suivante, dans laquelle nous exécutons via une tâche de la ruche à l’aide de DocumentDB et HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Ensuite, revenez à cet article, où vous recevrez tous les détails sur l’exécution de travaux d’analytique sur vos données de DocumentDB.

> [AZURE.TIP] Ce didacticiel suppose que vous disposez d’une expérience préalable à l’aide de Apache Hadoop, ruche et/ou de porc. Si vous ne connaissez pas Apache Hadoop ruche et porc, nous vous recommandons de consulter la [documentation d’Apache Hadoop][apache-hadoop-doc]. Ce didacticiel suppose également que vous avez une connaissance préalable de DocumentDB et que vous disposez d’un compte DocumentDB. Si vous ne connaissez pas DocumentDB ou vous ne disposez pas d’un compte DocumentDB, veuillez consulter notre [Mise en route] [ getting-started] page.

N’avez le temps de terminer le didacticiel et vous voulez simplement obtenir des exemples complets de scripts PowerShell de ruche, de porcs et MapReduce ? Pas de problème, les obtenir [ici][documentdb-hdinsight-samples]. Le téléchargement contient également les fichiers hql, porc et java pour ces exemples.

## <a name="NewestVersion"></a>Version la plus récente

<table border='1'>
    <tr><th>Connecteur d’Hadoop Version</th>
        <td>1.2.0</td></tr>
    <tr><th>Uri du script</th>
        <td>https://portalcontent.BLOB.Core.Windows.NET/scriptaction/documentdb-Hadoop-installer-v04.ps1</td></tr>
    <tr><th>Date de modification</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versions prises en charge HDInsight</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Journal des modifications</th>
        <td>Mise à jour DocumentDB Java SDK pour 1.6.0</br>
            Prise en charge pour les collections partitionnées à la fois en tant que source et récepteur</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Conditions préalables
Avant de suivre les instructions de ce didacticiel, vous assurer que vous disposez des éléments suivants :

- Un compte de DocumentDB, une base de données et une collection de documents à l’intérieur. Pour plus d’informations, consultez [Mise en route de la DocumentDB][getting-started]. Importer des exemples de données dans votre compte de DocumentDB avec l' [outil d’importation de DocumentDB][documentdb-import-data].
- Débit. Lit et écrit à partir de HDInsight sera comptée vers vos unités de demande alloué pour vos collections. Pour plus d’informations, consultez [Provisioned débit, les unités de la demande et les opérations de base de données][documentdb-manage-throughput].
- Capacité pour une procédure stockée supplémentaire au sein de chaque collection de sortie. Les procédures stockées sont utilisées pour le transfert de documents qui en résultent. Pour plus d’informations, consultez [Collections et mis en service le débit][documentdb-manage-document-storage].
- Capacité pour les documents obtenus à partir des ruche, porc ou MapReduce travaux. Pour plus d’informations, consultez les [performances et la capacité de gérer les DocumentDB][documentdb-manage-collections].
- [*Facultatif*] Capacité d’une collection supplémentaire. Pour plus d’informations, consultez [stockage de document Provisioned et surcharge][documentdb-manage-document-storage].

> [AZURE.WARNING] Afin d’éviter la création d’une nouvelle collection au cours d’une des tâches, vous pouvez imprimer les résultats à stdout, enregistrer le résultat dans votre conteneur WASB ou spécifiez un regroupement existant déjà. En cas de spécification d’une collection existante, nouveaux documents seront créés à l’intérieur de la collection et des documents déjà existants ne seront supprimés s’il existe un conflit dans les *identificateurs*. **Le connecteur va remplacer automatiquement les documents existants avec des conflits d’id**. Vous pouvez désactiver cette fonctionnalité en définissant l’option upsert sur false. Si upsert a la valeur false et si un conflit se produit, la tâche d’Hadoop échouent ; signale une erreur de conflit de code.


## <a name="ProvisionHDInsight"></a>Étape 1 : Créer un nouveau cluster HDInsight
Ce didacticiel utilise une Action de Script à partir du portail Azure pour personnaliser votre cluster HDInsight. Dans ce didacticiel, nous utiliserons le portail Azure pour créer votre cluster HDInsight. Pour obtenir des instructions sur l’utilisation des applets de commande PowerShell ou le Kit de développement .NET HDInsight, extraire les [clusters HDInsight de personnaliser à l’aide de Script Action] [ hdinsight-custom-provision] l’article.

1. Connectez-vous au [portail Azure][azure-portal].

2. Cliquez sur **+ Nouveau** en haut de la navigation de gauche, recherchez **HDInsight** dans la barre de recherche sur la nouvelle blade.

3. **HDInsight** publié par **Microsoft** s’affiche en haut des résultats. Cliquez dessus, puis sur **créer**.

4. Sur le nouveau HDInsight Cluster créer des lames, entrez votre **Nom de Cluster** et sélectionner l' **abonnement** que vous souhaitez mettre en service cette ressource sous.

    <table border='1'>
        <tr><td>Nom du cluster</td><td>Nom du cluster.<br/>
   Le nom DNS doit commencer et se terminer par un caractère alphanumérique et peut contenir des tirets.<br/>
   Le champ doit être une chaîne comprise entre 3 et 63 caractères.</td></tr>
        <tr><td>Nom de l’abonnement</td>
            <td>Si vous avez plus d’un abonnement Azure, sélectionnez l’abonnement qui hébergera votre cluster HDInsight. </td></tr>
    </table>

5. Cliquez sur **Sélectionner le Type de Cluster** et définissez les propriétés suivantes avec les valeurs spécifiées.

    <table border='1'>
        <tr><td>Type de cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Cluster de niveau</td><td><strong>Standard</strong></td></tr>
        <tr><td>Système d'exploitation</td><td><strong>Windows</strong></td></tr>
        <tr><td>Version</td><td>version la plus récente</td></tr>
    </table>

    Maintenant, cliquez sur **Sélectionner**.

    ![Fournir des détails de cluster initial Hadoop HDInsight][image-customprovision-page1]

6. Cliquez sur les **informations d’identification** pour configurer votre connexion d’accès et les informations d’identification d’accès distant. Cliquez sur votre **nom de connexion de Cluster** et d’un **mot de passe de connexion de Cluster**.

    Si vous souhaitez distant dans votre cluster, sélectionnez *Oui* au bas de la lame et fournir un nom d’utilisateur et le mot de passe.

7. Cliquez sur pour définir votre emplacement principal pour accéder aux données de la **Source de données** . Choisir la **Méthode de sélection** et spécifiez un compte de stockage déjà existant ou créez-en un nouveau.

8. Sur la même lame, spécifiez un **Conteneur par défaut** et un **emplacement**. Puis, cliquez sur **Sélectionner**.

    > [AZURE.NOTE] Sélectionnez un emplacement proche de votre zone de compte DocumentDB pour améliorer les performances

8. Cliquez sur **tarification** pour sélectionner le nombre et le type de nœuds. Vous pouvez conserver la configuration par défaut et les mettre à l’échelle le nombre de nœuds de travail ultérieurement.

9. Cliquez sur **Configuration facultative**, puis sur **Actions de Script** dans la lame de Configuration facultatives.

    Dans les Actions de Script, entrez les informations suivantes pour personnaliser votre cluster HDInsight.

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Spécifiez un nom pour l’action du script.</td></tr>
        <tr><td>URI de script</td>
            <td>Spécifiez l’URI du script qui est appelée pour personnaliser le cluster.</br></br>
   Veuillez entrer : </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
        <tr><td>Tête</td>
            <td>Cliquez sur la case à cocher pour exécuter le script PowerShell sur le nœud de tête.</br></br>
            <strong>Activez cette case à cocher</strong>.</td></tr>
        <tr><td>Travailleur</td>
            <td>Cliquez sur la case à cocher pour exécuter le script PowerShell sur le nœud du travailleur.</br></br>
            <strong>Activez cette case à cocher</strong>.</td></tr>
        <tr><td>Soigneur</td>
            <td>Cliquez sur la case à cocher pour exécuter le script PowerShell sur le soigneur.</br></br>
            <strong>Pas nécessaire</strong>.
            </td></tr>
        <tr><td>Paramètres</td>
            <td>Spécifiez les paramètres, si nécessaire par le script.</br></br>
            <strong>Les paramètres non nécessaires</strong>.</td></tr>
    </table>

10. Créer un nouveau **Groupe de ressources** ou utiliser un groupe de ressources existant sous votre abonnement Azure.

11. À présent, les **Ajouter au tableau de bord** pour effectuer le suivi de son déploiement, cliquez sur **créer**!

## <a name="InstallCmdlets"></a>Étape 2 : Installer et configurer Azure PowerShell

1. Installer PowerShell Azure. Vous pouvez trouver des instructions [ici][powershell-install-configure].

    > [AZURE.NOTE] Uniquement pour les requêtes de la ruche, vous pouvez également utiliser l’éditeur de la ruche en ligne de HDInsight. Pour ce faire, connectez-vous au [Portail Azure][azure-portal], cliquez sur **HDInsight** dans le volet gauche pour afficher une liste des clusters HDInsight. Cliquez sur le cluster que vous souhaitez exécuter des requêtes de ruche sur, puis cliquez sur **Console de requête**.

2. Ouvrez l’environnement de script intégré PowerShell Azure :
    - Sur un ordinateur exécutant Windows 8 ou Windows Server 2012 ou une version ultérieure, vous pouvez utiliser la recherche intégrée. À partir de l’écran de démarrage, tapez **powershell ise** et appuyez sur **entrée**.
    - Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le menu Démarrer. Dans le menu Démarrer, tapez **invite de commandes** dans la zone Rechercher, puis dans la liste des résultats, cliquez sur **invite de commandes**. À l’invite de commandes, tapez **powershell_ise** et appuyez sur **entrée**.

3. Ajoutez votre compte Azure.
    1. Dans le volet de la Console, tapez **Add-AzureAccount** et appuyez sur **entrée**.
    2. Saisissez l’adresse e-mail associée à votre abonnement Azure et cliquez sur **Continuer**.
    3. Tapez le mot de passe pour votre abonnement Azure.
    4. Cliquez sur **connexion**.

4. Le diagramme suivant identifie les parties importantes de votre environnement de script PowerShell Azure.

    ![Diagramme de PowerShell Azure][azure-powershell-diagram]

## <a name="RunHive"></a>Étape 3 : Exécution d’un travail de la ruche à l’aide de DocumentDB et HDInsight

> [AZURE.IMPORTANT] Toutes les variables indiquées par < > doivent être renseignés à l’aide de vos paramètres de configuration.

1. Définir les variables suivantes dans votre volet de PowerShell Script.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"

2. <p>Nous allons commencer à construire votre chaîne de requête. Nous écrirons une requête de ruche qui prend les estampilles (DTS) de tous les documents générés par le système et les ID uniques (_rid) à partir d’une collection de DocumentDB, comptabilise tous les documents à la minute et puis stocke les résultats de la sauvegarde dans une nouvelle collection DocumentDB.</p>

    <p>Tout d’abord, nous allons créer une table de la ruche à partir de notre collection de DocumentDB. Ajouter l’extrait de code suivant pour le PowerShell Script volet <strong>une fois</strong> l’extrait de code à partir de #1. Vérifiez que vous incluez facultatif DocumentDB.query paramètre t trim nos documents à DTS seulement et _rid.</p>

    > [AZURE.NOTE]**D’attribution de noms de DocumentDB.inputCollections n’a pas une erreur.** Oui, nous permettre l’ajout de plusieurs collections en tant qu’entrée : </br>

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.


        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Ensuite, nous allons créer une table de la ruche pour la collection de sortie. Les propriétés de document de sortie sera le mois, jour, heure, minute et le nombre total d’occurrences.

    > [AZURE.NOTE]**Encore, d’affectation de noms DocumentDB.outputCollections n’était pas une erreur.** Oui, nous permettre l’ajout de plusieurs collections sous la forme d’une sortie : </br>
'*DocumentDB.outputCollections*'='*\<nom de la Collection DocumentDB sortie 1\>*,*\<nom de la Collection DocumentDB sortie 2\>*' </br> Les noms de collection sont séparés sans espaces, en utilisant uniquement une seule virgule. </br></br>
Documents seront distribuée alternée sur plusieurs collections. Un lot de documents est stocké dans une collection, puis un deuxième lot de documents est stocké dans la collection suivante et ainsi de suite.

        # Create a Hive table for the output data to DocumentDB.
        $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Enfin, nous allons calculer les documents par mois, jour, heure et minute et réinsérez les résultats dans la sortie de table de la ruche.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Ajouter l’extrait de script suivant pour créer une définition de travail ruche à partir de la requête précédente.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Vous pouvez également utiliser le fichier commutateur - pour spécifier un fichier de script de HiveQL de très.

6. Ajouter l’extrait suivant pour enregistrer l’heure de début et de soumettre la tâche de la ruche.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Ajoutez le code suivant pour attendre la fin du travail de ruche.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Exécutez** votre nouveau script ! **Cliquez sur** le bouton vert d’exécuter.

10. Vérifier les résultats. Connexion au [portail Azure][azure-portal].
    1. Dans le panneau de gauche, cliquez sur <strong>Parcourir</strong> . </br>
    2. Cliquez sur <strong>tout</strong> en haut à droite du Panneau de navigation. </br>
    3. Recherchez et cliquez sur <strong>Comptes de DocumentDB</strong>. </br>
    4. Ensuite, recherchez votre <strong>Compte de DocumentDB</strong>, puis <strong>DocumentDB de base de données</strong> et de votre <strong>Collection de DocumentDB</strong> associé à la collection de sortie spécifiée dans la requête de la ruche.</br>
    5. Enfin, cliquez sur <strong>Explorateur de documents</strong> , sous <strong>Outils de développement</strong>.</br></p>

    Vous verrez les résultats de votre requête de la ruche.

    ![Résultats de la requête de la ruche][image-hive-query-results]

## <a name="RunPig"></a>Étape 4 : Exécution d’un travail de porc à l’aide de DocumentDB et HDInsight

> [AZURE.IMPORTANT] Toutes les variables indiquées par < > doivent être renseignés à l’aide de vos paramètres de configuration.

1. Définir les variables suivantes dans votre volet de PowerShell Script.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Nous allons commencer à construire votre chaîne de requête. Nous écrirons une requête de porc qui prend les estampilles (DTS) de tous les documents générés par le système et les ID uniques (_rid) à partir d’une collection de DocumentDB, comptabilise tous les documents à la minute et puis stocke les résultats de la sauvegarde dans une nouvelle collection DocumentDB.</p>
    <p>Tout d’abord charger les documents à partir de DocumentDB dans HDInsight. Ajouter l’extrait de code suivant pour le PowerShell Script volet <strong>une fois</strong> l’extrait de code à partir de #1. N’oubliez pas d’ajouter une requête DocumentDB pour le paramètre de requête DocumentDB facultatif à découper nos documents à DTS seulement et _rid.</p>

    > [AZURE.NOTE]Oui, nous permettre l’ajout de plusieurs collections en tant qu’entrée : </br>
'*\<Nom de la Collection d’entrée de DocumentDB 1\>*,*\<nom de la Collection d’entrée de DocumentDB 2\>*'</br> Les noms de collection sont séparés sans espaces, en utilisant uniquement une seule virgule. </b>

    Documents seront distribuée alternée sur plusieurs collections. Un lot de documents est stocké dans une collection, puis un deuxième lot de documents est stocké dans la collection suivante et ainsi de suite.

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Ensuite, nous allons calculer les documents par mois, jour, heure, minute et le nombre total d’occurrences.

        # GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Enfin, nous allons stocker les résultats dans notre nouvelle collection de sortie.

    > [AZURE.NOTE]Oui, nous permettre l’ajout de plusieurs collections sous la forme d’une sortie : </br>
'\<Nom de la Collection DocumentDB sortie 1\>,\<nom de la Collection DocumentDB sortie 2\>'</br> Les noms de collection sont séparés sans espaces, en utilisant uniquement une seule virgule.</br>
Documents seront distribuée alternée dans plusieurs collections. Un lot de documents est stocké dans une collection, puis un deuxième lot de documents est stocké dans la collection suivante et ainsi de suite.

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Ajouter l’extrait de script suivant pour créer une définition de travail de porcs à partir de la requête précédente.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Vous pouvez également utiliser le fichier commutateur - pour spécifier un fichier de script de porc sur très.

6. Ajouter l’extrait suivant pour enregistrer l’heure de début et de soumettre le travail de porc.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Ajoutez le code suivant pour attendre la fin du travail de porc.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Exécutez** votre nouveau script ! **Cliquez sur** le bouton vert d’exécuter.

10. Vérifier les résultats. Connexion au [portail Azure][azure-portal].
    1. Dans le panneau de gauche, cliquez sur <strong>Parcourir</strong> . </br>
    2. Cliquez sur <strong>tout</strong> en haut à droite du Panneau de navigation. </br>
    3. Recherchez et cliquez sur <strong>Comptes de DocumentDB</strong>. </br>
    4. Ensuite, recherchez votre <strong>Compte de DocumentDB</strong>, puis <strong>DocumentDB de base de données</strong> et de votre <strong>Collection de DocumentDB</strong> associé à la collection de sortie spécifiée dans votre requête de porc.</br>
    5. Enfin, cliquez sur <strong>Explorateur de documents</strong> , sous <strong>Outils de développement</strong>.</br></p>

    Vous verrez les résultats de votre requête de porc.

    ![Résultats de la requête porc][image-pig-query-results]

## <a name="RunMapReduce"></a>Étape 5 : Exécution d’un travail MapReduce à l’aide de DocumentDB et HDInsight

1. Définir les variables suivantes dans votre volet de PowerShell Script.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

2. Nous allons exécuter un travail MapReduce qui comptabilise le nombre d’occurrences de chaque propriété de Document à partir de votre collection de DocumentDB. Ajouter ce script extrait **après que** l’extrait de code ci-dessus.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

    > [AZURE.NOTE] TallyProperties-v01.jar est fourni avec l’installation personnalisée du connecteur Hadoop DocumentDB.

3. Ajoutez la commande suivante pour soumettre la tâche MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    En plus de la définition de travail MapReduce, vous fournissez également le nom du cluster HDInsight où vous souhaitez exécuter la tâche MapReduce et les informations d’identification. Le début-AzureHDInsightJob est un appel asynchrone. Pour vérifier la fin de la tâche, utilisez l’applet de commande *Wait-AzureHDInsightJob* .

4. Ajoutez la commande suivante pour vérifier les erreurs à l’exécution de la tâche MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. **Exécutez** votre nouveau script ! **Cliquez sur** le bouton vert d’exécuter.

6. Vérifier les résultats. Connexion au [portail Azure][azure-portal].
    1. Dans le panneau de gauche, cliquez sur <strong>Parcourir</strong> .
    2. Cliquez sur <strong>tout</strong> en haut à droite du Panneau de navigation.
    3. Recherchez et cliquez sur <strong>Comptes de DocumentDB</strong>.
    4. Ensuite, recherchez votre <strong>Compte de DocumentDB</strong>, puis <strong>DocumentDB de base de données</strong> et de votre <strong>Collection de DocumentDB</strong> associé à la collection de sortie spécifiée dans votre travail MapReduce.
    5. Enfin, cliquez sur <strong>Explorateur de documents</strong> , sous <strong>Outils de développement</strong>.

    Vous verrez les résultats de votre travail MapReduce.

    ![Résultats de la requête MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Étapes suivantes

Félicitations ! Vous venez d’exécuter votre première ruche, porc, MapReduce travaux et à l’aide d’Azure DocumentDB et HDInsight.

Nous avons ouvert provenant de notre connecteur Hadoop. Si vous êtes intéressé, vous pouvez contribuer sur [GitHub][documentdb-github].

Pour plus d’informations, consultez les articles suivants :

- [Développement d’une application Java avec Documentdb][documentdb-java-application]
- [Développer les programmes Java MapReduce pour Hadoop dans HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Mise en route avec Hadoop ruche dans HDInsight pour analyser l’utilisation d’un combiné mobile][hdinsight-get-started]
- [Utilisez MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utilisez la ruche avec HDInsight][hdinsight-use-hive]
- [Utilisez des porcs avec HDInsight][hdinsight-use-pig]
- [Personnaliser des clusters HDInsight à l’aide des actions de Script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
