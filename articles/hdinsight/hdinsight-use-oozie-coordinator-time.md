<properties
    pageTitle="Utiliser en temps un coordinateur Hadoop Oozie dans HDInsight | Microsoft Azure"
    description="Utiliser en temps un coordinateur Hadoop Oozie dans HDInsight, un service de données. Apprenez à définir les coordinateurs et les flux de travail Oozie et soumettre des travaux."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Utilisez coordinateur de Oozie en temps avec Hadoop dans HDInsight pour définir le flux de travail et coordonner les travaux

Dans cet article, vous apprendrez comment définir des workflows et des coordinateurs et déclencher les tâches du coordinateur, en fonction du temps. Il est utile de passer par [Oozie d’utilisation avec HDInsight] [ hdinsight-use-oozie] avant de lire cet article. En plus de la Oozie, vous pouvez également planifier des tâches à l’aide de la fabrique de données Azure. Pour savoir Azure Data Factory, voir [utilisation porc et ruche avec Data Factory](../data-factory/data-factory-data-transformation-activities.md).

> [AZURE.NOTE] Cet article requiert un cluster basé sur Windows de HDInsight. Pour plus d’informations sur l’utilisation de Oozie, y compris des travaux basés sur un cluster basé sur Linux, reportez-vous à la section [Oozie utilisation avec Hadoop pour définir et exécuter un flux de travail sur HDInsight de basé sur Linux](hdinsight-use-oozie-linux-mac.md)

##<a name="what-is-oozie"></a>Quel est Oozie

Apache Oozie est un système de flux de travail/coordination qui gère les travaux d’Hadoop. Il est intégré à la pile d’Hadoop, et il prend en charge les travaux Hadoop pour Apache MapReduce, Apache porc, la ruche Apache et Apache Sqoop. Il peut également être utilisé pour planifier des tâches qui sont spécifiques à un système, comme les programmes Java ou des scripts de shell.

L’image suivante montre le flux de travail que vous allez implémenter :

![Diagramme de flux de travail][img-workflow-diagram]

Le flux de travail contient deux actions :

1. Une action de la ruche s’exécute un script HiveQL pour compter les occurrences de chaque type au niveau du journal dans un fichier de journal log4j. Chaque journal log4j se compose d’une ligne de champs qui contient un champ [niveau de journal] pour afficher le type et la gravité, par exemple :

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    La sortie du script ruche est semblable à :

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Pour plus d’informations sur la ruche, voir [Utilisation de la ruche avec HDInsight][hdinsight-use-hive].

2.  Une action Sqoop exporte le résultat de l’action HiveQL à une table dans une base de données SQL d’Azure. Pour plus d’informations sur Sqoop, reportez-vous à la section [Sqoop utilisation avec HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Pour les versions Oozie prises en charge sur les clusters de HDInsight, consultez [Nouveautés dans les versions de cluster fournies par HDInsight ?] [hdinsight-versions].


##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Une station de travail avec PowerShell d’Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **HDInsight d’un cluster**. Pour plus d’informations sur la création d’un cluster d’HDInsight, voir [clusters de créer de HDInsight][hdinsight-provision], ou de la [mise en route de HDInsight][hdinsight-get-started]. Vous devez les données suivantes pour parcourir le didacticiel :

    <table border = "1">
    <tr><th>Propriété de cluster</th><th>Nom de la variable de Windows PowerShell</th><th>Valeur</th><th>Description</th></tr>
    <tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Le cluster HDInsight sur lequel vous allez exécuter ce didacticiel.</td></tr>
    <tr><td>Nom d’utilisateur du cluster HDInsight</td><td>$clusterUsername</td><td></td><td>Le nom d’utilisateur HDInsight de cluster. </td></tr>
    <tr><td>Mot de passe utilisateur de cluster HDInsight </td><td>$clusterPassword</td><td></td><td>Le mot de passe utilisateur de cluster d’HDInsight.</td></tr>
    <tr><td>Nom du compte de stockage Azure</td><td>$storageAccountName</td><td></td><td>Un compte de stockage Azure disponible pour le cluster HDInsight. Pour ce didacticiel, utilisez le compte de stockage par défaut que vous avez spécifiée au cours du processus de fourniture de cluster.</td></tr>
    <tr><td>Nom de conteneur Blob Azure</td><td>$containerName</td><td></td><td>Pour cet exemple, utilisez le conteneur de stockage Azure Blob qui est utilisé pour le système de fichiers de cluster par défaut HDInsight. Par défaut, elle a le même nom que le cluster HDInsight.</td></tr>
    </table>

- **Base de données d’un Azure SQL**. Vous devez configurer une règle de pare-feu pour le serveur de base de données SQL autoriser l’accès à partir de votre station de travail. Pour obtenir des instructions sur la création d’un SQL Azure des bases de données et la configuration du pare-feu, reportez-vous à la section [mise en route de la base de données Azure SQL][sqldatabase-get-started]. Cet article fournit un script Windows PowerShell pour la création de la table de base de données Azure SQL dont vous avez besoin pour ce didacticiel.

    <table border = "1">
    <tr><th>Propriété de base de données SQL</th><th>Nom de la variable de Windows PowerShell</th><th>Valeur</th><th>Description</th></tr>
    <tr><td>Nom du serveur de base de données SQL</td><td>$sqlDatabaseServer</td><td></td><td>Le serveur de base de données SQL vers lequel Sqoop exportera les données. </td></tr>
    <tr><td>Nom de connexion de base de données SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nom de connexion de base de données SQL.</td></tr>
    <tr><td>Mot de passe de connexion SQL de base de données</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Mot de passe de la base de données SQL.</td></tr>
    <tr><td>Nom de base de données SQL</td><td>$sqlDatabaseName</td><td></td><td>La base de données Azure SQL vers lequel Sqoop exportera les données. </td></tr>
    </table>

    > [AZURE.NOTE] Par défaut une base de données Azure SQL autorise les connexions à partir des Services Azure, tels que les HDInsight d’Azure. Si ce paramètre de pare-feu est désactivé, vous devez l’activer à partir du portail Azure. Pour obtenir des instructions sur la création d’une base de données SQL et la configuration des règles de pare-feu, reportez-vous à la section [créer et configurer une base de données SQL][sqldatabase-get-started].


> [AZURE.NOTE] Remplir les valeurs dans les tables. Il est utile de passer par ce didacticiel.


##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Définir les flux de travail Oozie et le script de HiveQL connexe

Définitions de workflows Oozie sont écrits en hPDL (un processus définition du langage XML). Le nom de fichier de flux de travail par défaut est *workflow.xml*.  Vous enregistrez le fichier de flux de travail localement et puis le déployer sur le cluster de HDInsight à l’aide de PowerShell d’Azure plus loin dans ce didacticiel.

L’action de la ruche dans le flux de travail appelle un fichier de script HiveQL. Ce fichier de script contient trois instructions de HiveQL :

1. **Instruction du DROP TABLE** supprime la table de ruche de log4j si elle existe.
2. **Instruction de création de la TABLE** crée une table externe de ruche log4j qui pointe vers l’emplacement du fichier journal log4j ;
3.  **L’emplacement du fichier journal log4j**. Le délimiteur de champ est «, ». Le séparateur de ligne par défaut est « \n ». Une table externe ruche est utilisée pour éviter le fichier de données en cours de suppression à partir de l’emplacement d’origine, dans le cas où vous souhaitez exécuter le flux de travail Oozie plusieurs fois.
3. **L’insérer remplacer l’instruction** compte les occurrences de chaque type de journal au niveau de la table de la ruche log4j et il enregistre le résultat dans un emplacement de stockage Azure Blob.

**Remarque**: il existe un problème de chemin d’accès de ruche connu. Vous rencontrerez ce problème lors de l’envoi d’un travail de Oozie. Les instructions permettant de résoudre le problème, vous pouvez trouver sur le TechNet Wiki : [erreur de la ruche de HDInsight : Impossible de renommer][technetwiki-hive-error].

**Pour définir le fichier de script de HiveQL doit être appelée par le flux de travail**

1. Créez un fichier texte avec le contenu suivant :

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Il existe trois variables utilisées dans le script :

    - ${hiveTableName}
    - ${hiveDataFolder}
    - ${hiveOutputFolder}

    Le fichier de définition de flux de travail (workflow.xml dans ce didacticiel) transmet ensuite ces valeurs à ce script HiveQL au moment de l’exécution.

2. Enregistrez le fichier en tant que **C:\Tutorials\UseOozie\useooziewf.hql** en utilisant le codage ANSI (ASCII). (Utilisez le bloc-notes si votre éditeur de texte ne fournit pas cette option.) Ce fichier de script sera déployé vers le cluster HDInsight plus loin dans le didacticiel.



**Pour définir un flux de travail**

1. Créez un fichier texte avec le contenu suivant :

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveOutputFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    Il existe deux actions définies dans le workflow. L’action de début est *RunHiveScript*. Si l’action s’exécute *OK*, l’action suivante est *RunSqoopExport*.

    Le RunHiveScript a plusieurs variables. Vous allez passer les valeurs lors de l’envoi du travail de Oozie à partir de votre station de travail à l’aide de PowerShell d’Azure.

    <table border = "1">
    <tr><th>Variables de flux de travail</th><th>Description</th></tr>
    <tr><td>${jobTracker}</td><td>Spécifiez l’URL de la mise hors tension du travail Hadoop. Utiliser <strong>jobtrackerhost:9010</strong> sur la version de cluster HDInsight 3.0 et 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Spécifiez l’URL du nœud nom Hadoop. Utiliser le wasbs de système de fichier par défaut : / / adresse, par exemple, <i>wasbs : / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${Nom}</td><td>Spécifie le nom de la file d’attente qui est soumise à la tâche. Utilisez la <strong>valeur par défaut</strong>.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variable d’action de la ruche</th><th>Description</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Le répertoire source de la commande de la ruche Create Table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Le dossier de sortie de l’instruction INSERT remplacer.</td></tr>
    <tr><td>${hiveTableName}</td><td>Le nom de la table de la ruche qui fait référence aux fichiers de données log4j.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variable d’action Sqoop</th><th>Description</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Chaîne de connexion de base de données SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>La table de base de données Azure SQL où les données vont être exportées.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Le dossier de sortie de l’instruction de la ruche insérer remplacer. C’est le même dossier pour l’exportation de Sqoop (exportation-dir).</td></tr>
    </table>

    Pour plus d’informations sur les Oozie de flux de travail et en utilisant les actions de workflow, consultez la [documentation Apache Oozie 4.0] [ apache-oozie-400] (pour la version de cluster HDInsight 3.0) ou la [documentation d’Apache Oozie 3.3.2] [ apache-oozie-332] (pour la version de cluster HDInsight 2.1).

2. Enregistrez le fichier en tant que **C:\Tutorials\UseOozie\workflow.xml** en utilisant le codage ANSI (ASCII). (Utilisez le bloc-notes si votre éditeur de texte ne fournit pas cette option.)

**Pour définir le coordinateur**

1. Créez un fichier texte avec le contenu suivant :

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    Il existe cinq variables utilisées dans le fichier de définition :

  	| Variable          | Description |
  	| ------------------|------------ |
  	| ${coordFrequency} | Temps de pause de travail. Fréquence est toujours exprimée en minutes. |
  	| ${coordStart}     | Heure de début du projet. |
  	| ${coordEnd}       | Heure de fin de tâche. |
  	| ${coordTimezone}  | Oozie traite les tâches de coordinateur dans un fuseau horaire fixe avec aucune heure (ce qui est généralement représentée à l’aide d’UTC). Ce fuseau horaire est appelé « timezone de traitement de Oozie. » |
  	| ${wfPath}         | Le chemin d’accès pour workflow.xml.  Si le nom du fichier du flux de travail n’est pas le nom de fichier par défaut (workflow.xml), vous devez la spécifier. |

2. Enregistrez le fichier en tant que **C:\Tutorials\UseOozie\coordinator.xml** à l’aide de l’encodage ANSI (ASCII). (Utilisez le bloc-notes si votre éditeur de texte ne fournit pas cette option.)

##<a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Déployer le projet Oozie et préparer le didacticiel

Vous allez exécuter un script PowerShell d’Azure pour effectuer les opérations suivantes :

- Copiez le script de HiveQL (useoozie.hql) dans le stockage Blob d’Azure, wasbs:///tutorials/useoozie/useoozie.hql.
- Copiez workflow.xml vers wasbs:///tutorials/useoozie/workflow.xml.
- Copiez coordinator.xml dans wasbs:///tutorials/useoozie/coordinator.xml.
- Copiez le fichier de données (/ example/data/sample.log) à wasbs:///tutorials/useoozie/data/sample.log.
- Créer une table de base de données Azure SQL pour stocker les données d’exportation de Sqoop. Le nom de table est *log4jLogCount*.

**Comprendre le stockage de HDInsight**

HDInsight utilise le stockage Azure Blob pour le stockage de données. wasbs : / / est l’implémentation Microsoft du système de fichiers distribué de Hadoop (très) dans le stockage des objets Blob Azure. Pour plus d’informations, consultez [stockage Blob Azure d’utilisation avec HDInsight][hdinsight-storage].

Lorsque vous configurez un cluster d’HDInsight, un compte de stockage Azure Blob et un conteneur spécifique à partir de ce compte est désigné comme le système de fichiers par défaut, comme dans le très. En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage supplémentaires dans l’abonnement Azure même ou dans différents abonnements Azure pendant le processus de déploiement. Pour des instructions sur l’ajout de comptes d’espace de stockage supplémentaire, voir [clusters de disposition HDInsight][hdinsight-provision]. Pour simplifier le script PowerShell d’Azure utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur système de fichier par défaut situé à */tutorials/useoozie*. Par défaut, ce conteneur a le même nom que le nom du cluster HDInsight.
La syntaxe est la suivante :

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Seulement la *wasb : / /* syntaxe est pris en charge dans la version de cluster HDInsight 3.0. L’ancien *asv : / /* syntaxe est pris en charge dans HDInsight 2.1 et 1,6 clusters, mais il n’est pas pris en charge dans les clusters HDInsight 3.0.

> [AZURE.NOTE] Le wasb : / / chemin d’accès est un chemin d’accès virtuel. Pour plus d’informations, consultez [stockage Blob Azure d’utilisation avec HDInsight][hdinsight-storage].

Un fichier qui est stocké dans le conteneur système de fichier par défaut est accessible à partir de HDInsight à l’aide de l’URI suivant (j’utilise workflow.xml à titre d’exemple) :

    wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasbs:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Si vous souhaitez accéder au fichier directement à partir du compte de stockage, le nom du fichier blob est :

    tutorials/useoozie/workflow.xml

**Comprendre les tables internes et externes de ruche**

Il y a quelques choses à savoir sur la ruche des tables internes et externes :

- La commande CREATE TABLE crée une table interne, également connu sous le nom d’un tableau managé. Le fichier de données doit se trouver dans le conteneur par défaut.
- La commande CREATE TABLE déplace le fichier de données vers le /hive/magasin/<TableName> dossier dans le conteneur par défaut.
- La commande Créer une TABLE externe crée une table externe. Le fichier de données peut se trouver en dehors du conteneur par défaut.
- La commande Créer une TABLE externe ne déplace pas le fichier de données.
- La commande Créer une TABLE externe n’autorise pas les sous-dossiers sous le dossier qui est spécifié dans la clause de l’emplacement. C’est la raison pourquoi le didacticiel est une copie du fichier exemple.log.

Pour plus d’informations, consultez [HDInsight : la ruche interne et externe Intro de Tables][cindygross-hive-tables].

**Pour préparer le didacticiel**

1. Ouvrez l’environnement de Windows PowerShell ISE (dans l’écran de démarrage de Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Pour plus d’informations, consultez [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous devrez entrer vos informations d’identification de compte Azure. Cette méthode d’ajout d’un abonnement arrive à expiration, et après 12 heures, vous devrez réexécuter l’applet de commande.

    > [AZURE.NOTE] Si vous disposez de plusieurs abonnements Azure et l’abonnement par défaut n’est pas celui que vous souhaitez utiliser, utilisez l’applet de commande <strong>Select-AzureSubscription</strong> pour sélectionner un abonnement.

3. Copiez le script suivant dans la fenêtre de script, puis définissez les six premières variables :

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here


    Pour obtenir une description plus des variables, consultez la section [conditions préalables](#prerequisites) dans ce didacticiel.

3. Ajoutez le code suivant pour le script dans la fenêtre de script :

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

4. Cliquez sur **Exécuter le Script** ou appuyez sur **F5** pour exécuter le script. Le résultat sera semblable à :

    ![Sortie de préparation de didacticiel][img-preparation-output]

##<a name="run-the-oozie-project"></a>Exécutez le projet Oozie

Azure PowerShell ne fournit actuellement des applets de commande pour définir des tâches de Oozie. Vous pouvez utiliser l’applet de commande **Invoke-RestMethod** pour appeler des services web de Oozie. L’API de services web Oozie est une API de JSON HTTP REST. Pour plus d’informations sur l’API de services web Oozie, consultez la [documentation Apache Oozie 4.0] [ apache-oozie-400] (pour la version de cluster HDInsight 3.0) ou la [documentation d’Apache Oozie 3.3.2] [ apache-oozie-332] (pour la version de cluster HDInsight 2.1).

**Pour soumettre un travail Oozie**

1. Ouvrez l’environnement de Windows PowerShell ISE (dans l’écran de démarrage de Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Pour plus d’informations, consultez [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).

3. Copiez le script suivant dans la fenêtre de script et définissez les variables d’abord quatorze (Toutefois, ignorez **$storageUri**).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Pour obtenir une description plus des variables, consultez la section [conditions préalables](#prerequisites) dans ce didacticiel.

    $coordstart et $coordend sont le flux de travail heure début et fin. Pour déterminer l’heure UTC/GMT, recherchez « GMT » sur bing.com. Le $coordFrequency est l’intervalle en minutes que vous souhaitez exécuter le flux de travail.

3. Ajoutez le code suivant pour le script. Cette partie définit la charge utile de Oozie :

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    >[AZURE.NOTE] La principale différence par rapport à du fichier de charge de présentation de flux de travail est variable **oozie.coord.application.path**. Lorsque vous envoyez une tâche de flux de travail, vous utilisez **oozie.wf.application.path** à la place.

4. Ajoutez le code suivant pour le script. Cette partie vérifie l’état du service web Oozie :

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5. Ajoutez le code suivant pour le script. Cette partie crée une tâche de Oozie :

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [AZURE.NOTE] Lorsque vous envoyez une tâche de workflow, vous devez appeler un autre service web pour démarrer le travail une fois la tâche créée. Dans ce cas, le travail de coordinateur est déclenché par heure. La tâche démarrera automatiquement.

6. Ajoutez le code suivant pour le script. Cette partie vérifie l’état du travail Oozie :

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7. (Facultatif) Ajoutez le code suivant pour le script.

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

7. Ajoutez le code suivant pour le script :

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    Supprimez le signe # si vous souhaitez exécuter les fonctions supplémentaires.

7. Si votre cluster HDinsight est la version 2.1, remplacer « https://$clusterName.azurehdinsight.net:443/oozie/v2/ » avec « https://$clusterName.azurehdinsight.net:443/oozie/v1/ ». Version de cluster HDInsight 2.1 ne pas prend en charge version 2 des services web.

7. Cliquez sur **Exécuter le Script** ou appuyez sur **F5** pour exécuter le script. Le résultat sera semblable à :

    ![Didacticiel exécuter la sortie du flux de travail][img-runworkflow-output]

8. Se connecter à votre base de données SQL pour afficher les données exportées.

**Pour vérifier le journal des erreurs de projet**

Pour résoudre les problèmes liés à un flux de travail, le fichier journal Oozie trouverez C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log à partir de la headnode du cluster. Pour plus d’informations sur le protocole RDP, voir [clusters de HDInsight d’administration via le portail Azure][hdinsight-admin-portal].

**Exécuter à nouveau le didacticiel**

Pour réexécuter le flux de travail, vous devez effectuer les tâches suivantes :

- Supprimez le fichier de sortie script ruche.
- Supprimer les données dans la table log4jLogsCount.

Voici un exemple de script de Windows PowerShell que vous pouvez utiliser :

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()


##<a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à définir un flux de travail de Oozie et d’un coordinateur de Oozie et comment exécuter une tâche de coordinateur de Oozie à l’aide de PowerShell d’Azure. Pour plus d’informations, consultez les articles suivants :

- [Mise en route de HDInsight][hdinsight-get-started]
- [Utilisez le stockage des objets Blob Azure avec HDInsight][hdinsight-storage]
- [Administrer les HDInsight à l’aide de PowerShell d’Azure][hdinsight-admin-powershell]
- [Téléchargement des données vers HDInsight][hdinsight-upload-data]
- [Utilisez Sqoop avec HDInsight][hdinsight-use-sqoop]
- [Utilisez la ruche avec HDInsight][hdinsight-use-hive]
- [Utilisez des porcs avec HDInsight][hdinsight-use-pig]
- [Développer les programmes Java MapReduce pour HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
