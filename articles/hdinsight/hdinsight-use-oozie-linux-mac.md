<properties
    pageTitle="Utiliser Hadoop Oozie workflows dans HDInsight de basé sur Linux | Microsoft Azure"
    description="Utilisez Hadoop Oozie de HDInsight de fonctionnant sous Linux. Découvrez comment définir un flux de travail Oozie et soumettent un travail Oozie."
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


# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Utiliser Oozie avec Hadoop pour définir et exécuter un flux de travail sur HDInsight de basé sur Linux

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Apprenez à Apache Oozie pour définir un workflow qui utilise la ruche et Sqoop et d’exécuter le flux de travail sur un cluster HDInsight de fonctionnant sous Linux.

Apache Oozie est un système de flux de travail/coordination qui gère les travaux d’Hadoop. Il est intégré à la pile d’Hadoop, et il prend en charge les travaux Hadoop pour Apache MapReduce, Apache porc, la ruche Apache et Apache Sqoop. Il peut également servir à planifier des travaux qui sont spécifiques à un système, comme les programmes Java ou des scripts de shell

> [AZURE.NOTE] Une autre option pour définir le flux de travail avec HDInsight est Azure Data Factory. Pour en savoir plus sur le Factory de données Azure, reportez-vous à la section [utilisation porc et ruche avec Data Factory][azure-data-factory-pig-hive].

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**: voir [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **CLI Azure**: reportez-vous à la section [installation et configuration de l’interface CLI Azure](../xplat-cli-install.md)
    
    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- **HDInsight un cluster**: reportez-vous à la section [Mise en route de HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Base de données d’un Azure SQL**: sera créé à l’aide de la procédure dans ce document

##<a name="example-workflow"></a>Exemple de workflow

Le flux de travail que vous allez implémenter en suivant les instructions de ce document contient deux actions. Les actions sont des définitions de tâches, telles que la ruche, Sqoop, MapReduce ou autres processus en cours d’exécution :

![Diagramme de flux de travail][img-workflow-diagram]

1. Une action de la ruche s’exécute un script HiveQL pour extraire des enregistrements à partir de la **hivesampletable** , inclus dans HDInsight. Chaque ligne de données décrit une visite à partir d’un périphérique mobile spécifique. Le format d’enregistrement s’affiche comme suit :

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Le script de la ruche utilisé dans ce document compte le nombre total de visites pour chaque plate-forme (par exemple, Android ou iPhone) et stocke les nombres dans une nouvelle table de la ruche.

    Pour plus d’informations sur la ruche, voir [Utilisation de la ruche avec HDInsight][hdinsight-use-hive].

2.  Une action Sqoop exporte le contenu de la nouvelle table de la ruche à une table dans une base de données SQL d’Azure. Pour plus d’informations sur Sqoop, reportez-vous à la section [Sqoop d’Hadoop utilisation avec HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Pour les versions Oozie prises en charge sur les clusters de HDInsight, consultez [Nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?] [hdinsight-versions].

##<a name="create-the-working-directory"></a>Créer le répertoire de travail

Oozie attend des ressources requises d’un travail à stocker dans le même répertoire. Cet exemple utilise **wasbs : / / didacticiels/useoozie**. Pour créer ce répertoire et le répertoire de données qui contiendra la nouvelle table de ruche créée par ce flux de travail, utilisez la commande suivante :

    hdfs dfs -mkdir -p /tutorials/useoozie/data

> [AZURE.NOTE] Le `-p` paramètre origine de tous les répertoires dans le chemin d’accès doit être créé si elles n’existent pas déjà. Le répertoire de **données** permet de conserver les données utilisées par le script **useooziewf.hql** .

Également exécuter la commande suivante, ce qui permet de s’assurer que Oozie peut emprunter l’identité de votre compte d’utilisateur lors de l’exécution des travaux de la ruche et Sqoop. Remplacez le **nom d’utilisateur** avec votre nom de connexion :

    sudo adduser USERNAME users

Si vous recevez une erreur que l’utilisateur est déjà membre du groupe utilisateurs, vous pouvez simplement l’ignorer.

##<a name="add-a-database-driver"></a>Ajouter un pilote de base de données

Étant donné que ce flux de travail utilise Sqoop pour exporter des données de la base de données SQL, vous devez fournir une copie du pilote JDBC utilisé pour communiquer avec SQL de base de données. Utilisez la commande suivante pour le copier dans le répertoire de travail :

    hdfs dfs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/

Si votre flux de travail utilisés les autres ressources, comme un fichier jar contenant une application MapReduce, vous devez également les ajouter.

##<a name="define-the-hive-query"></a>Définissez la requête de la ruche

Utilisez les étapes suivantes pour créer un script HiveQL qui définit une requête qui sera utilisée dans un workflow Oozie plus loin dans ce document.

1. Utiliser le protocole SSH pour se connecter au cluster basé sur Linux de HDInsight :

    * **Les clients Linux, Unix ou OS X**: reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Les clients Windows**: reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilisez la commande suivante pour créer un nouveau fichier :

        nano useooziewf.hql

1. Une fois l’éditeur nano s’ouvre, utilisez ce qui suit comme le contenu du fichier :

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
        FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

    Il existe deux variables utilisées dans le script :

    - **${hiveTableName}**: contient le nom de la table à créer
    - **${hiveDataFolder}**: contiendra l’emplacement où stocker les fichiers de données pour la table

    Le fichier de définition de flux de travail (workflow.xml dans ce didacticiel) transmet ces valeurs à ce script HiveQL au moment de l’exécution.

2. Appuyez sur Ctrl + X pour quitter l’éditeur. Lorsque vous y êtes invité, sélectionnez **Y** pour enregistrer le fichier, puis utilisez **entrée** pour utiliser le nom de fichier **useooziewf.hql** .

3. Utilisez les commandes suivantes pour copier des **useooziewf.hql** à **wasbs:///tutorials/useoozie/useooziewf.hql**:

        hdfs dfs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

    Ces commandes de stockent le fichier **useooziewf.hql** sur le compte de stockage Azure associé à ce cluster, ce qui permet de conserver le fichier même si le cluster est supprimé. Cela vous permet de réaliser des économies en supprimant les clusters lorsqu’ils ne sont pas en cours d’utilisation, tout en conservant vos tâches et les workflows.

##<a name="define-the-workflow"></a>Définir le flux de travail

Définitions de workflows Oozie sont écrits en hPDL (un XML Process Definition Language). Utilisez les étapes suivantes pour définir le flux de travail :

1. Pour créer et modifier un fichier, utilisez l’instruction suivante :

        nano workflow.xml

1. Une fois l’éditeur nano s’ouvre, entrez les informations suivantes en tant que le contenu du fichier :

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
                <arg>${hiveDataFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>sqljdbc41.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
            </action>
            <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>
            <end name="end"/>
        </workflow-app>

    Il existe deux actions définies dans le workflow :

    - **RunHiveScript**: il s’agit de l’action de démarrage et exécute le script de la ruche **useooziewf.hql**

    - **RunSqoopExport**: cette commande exporte les données créées à partir du script de la ruche à la base de données SQL à l’aide de Sqoop. Il ne s’exécute que si l’action de **RunHiveScript** a réussi.

        > [AZURE.NOTE] Pour plus d’informations sur les flux de travail Oozie et l’utilisation des actions de flux de travail, consultez la [documentation Apache Oozie 4.0] [ apache-oozie-400] (pour HDInsight version 3.0) ou la [documentation d’Apache Oozie 3.3.2] [ apache-oozie-332] (pour HDInsight version 2.1).

    Notez que le flux de travail a plusieurs entrées, tel que `${jobTracker}`, qui seront remplacées par les valeurs que vous utilisez dans la définition de travail plus loin dans ce document.

    Notez également la `<archive>sqljdbc4.jar</arcive>` dans la section Sqoop. Cela indique au Oozie à disposition cette archive pour Sqoop lors de l’exécution de cette action.

2. Utilisez Ctrl-X, puis **o** et sur **entrée** pour enregistrer le fichier.

3. Utilisez la commande suivante pour copier le fichier **workflow.xml** vers **wasbs:///tutorials/useoozie/workflow.xml**:

        hdfs dfs -copyFromLocal workflow.xml /tutorials/useoozie/workflow.xml

##<a name="create-the-database"></a>Créer la base de données

Suivez les étapes dans le document de [créer une base de données SQL](../sql-database/sql-database-get-started.md) pour créer une nouvelle base de données. Lors de la création de la base de données, utilisez __oozietest__ comme nom de base de données. Notez également le nom utilisé pour le serveur de base de données, tel qu’il sera nécessaire dans la section suivante.

###<a name="create-the-table"></a>Créer la table

> [AZURE.NOTE] Il existe de nombreuses façons de se connecter à la base de données SQL pour créer une table. Les étapes suivantes utilisent les [FreeTDS](http://www.freetds.org/) du cluster HDInsight.

3. Utilisez la commande suivante pour installer le FreeTDS sur le cluster de HDInsight :

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Une fois FreeTDS a été installé, utilisez la commande suivante pour vous connecter sur le serveur de base de données SQL que vous avez créé précédemment :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest

    Vous recevez une sortie similaire à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

5. À la `1>` invite, entrez les lignes suivantes :

        CREATE TABLE [dbo].[mobiledata](
        [deviceplatform] [nvarchar](50),
        [count] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
        GO

    Lors de la `GO` instruction est entrée, les instructions précédentes seront évaluées. Cela créera une nouvelle table nommée **mobiledata** qui sera écrit par Sqoop.

    Pour vérifier que la table a été créée, utilisez ce qui suit :

        SELECT * FROM information_schema.tables
        GO

    Vous devriez voir une sortie similaire à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

8. Entrez `exit` à la `1>` invite pour quitter l’utilitaire tsql.

##<a name="create-the-job-definition"></a>La définition du travail Création

La définition de travail décrit où trouver workflow.xml, ainsi que d’autres fichiers utilisés par le flux de travail (par exemple, useooziewf.hql.) Il définit également les valeurs des propriétés utilisées dans le flux de travail et les fichiers associés.

1. Utilisez la commande suivante pour obtenir l’adresse complète de la WASB de stockage par défaut. Il sera utilisé dans le fichier de configuration dans un moment :

        sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml

    Il doit retourner d’informations semblable au suivant :

        <name>fs.defaultFS</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>

    Enregistrer le **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** valeur, car il sera utilisé dans les étapes suivantes.

2. Utilisez la commande suivante pour obtenir le nom de domaine complet de l’headnode du cluster. Il sera utilisé pour l’adresse de JobTracker pour le cluster. Il sera utilisé dans le fichier de configuration dans un moment :

        hostname -f

    Ceci renvoie des informations semblables à ce qui suit :

        hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net

    Le port utilisé pour la JobTracker est 8050, tout l’adresse complète à utiliser pour la JobTracker **hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050**.

1. Pour créer la configuration de définition de travail de Oozie, utilisez ce qui suit :

        nano job.xml

2. Une fois l’éditeur nano s’ouvre, utilisez ce qui suit comme le contenu du fichier :

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

          <property>
            <name>nameNode</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
          </property>

          <property>
            <name>jobTracker</name>
            <value>JOBTRACKERADDRESS</value>
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
            <name>hiveScript</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
          </property>

          <property>
            <name>hiveTableName</name>
            <value>mobilecount</value>
          </property>

          <property>
            <name>hiveDataFolder</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
          </property>

          <property>
            <name>sqlDatabaseConnectionString</name>
            <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
          </property>

          <property>
            <name>sqlDatabaseTableName</name>
            <value>mobiledata</value>
          </property>

          <property>
            <name>user.name</name>
            <value>YourName</value>
          </property>

          <property>
            <name>oozie.wf.application.path</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
          </property>
        </configuration>

    * Remplacez toutes les instances de **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** avec la valeur que vous avez précédemment reçu.

    > [AZURE.WARNING] Vous devez utiliser le chemin d’accès complet de WASB, avec le compte de conteneur et de stockage dans le chemin d’accès. En utilisant le format court (wasbs : / / /) entraînent l’action RunHiveScript Échec de démarrage de la tâche.

    * Remplacez **JOBTRACKERADDRESS** par l’adresse de JobTracker/ResourceManager reçu précédemment.

    * Remplacez **votrenom** par votre nom de connexion pour le cluster HDInsight.

    * Remplacez **nom_serveur**, **adminLogin**et **adminPassword** avec les informations de la base de données SQL Azure.

    La plupart des informations de ce fichier est utilisé pour remplir les valeurs utilisées dans les fichiers workflow.xml ou ooziewf.hql (par exemple, ${nameNode}.)

    > [AZURE.NOTE] L’entrée **oozie.wf.application.path** définit où trouver le fichier workflow.xml, qui contient le flux de travail exécuté par ce travail.

2. Utilisez Ctrl-X, puis **o** et sur **entrée** pour enregistrer le fichier.

##<a name="submit-and-manage-the-job"></a>Soumettre et de gérer le travail

Les étapes suivantes utilisent la commande Oozie de soumettre et de gérer les workflows de Oozie sur le cluster. La commande Oozie est une interface conviviale sur l' [API REST de Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [AZURE.IMPORTANT] Lorsque vous utilisez la commande Oozie, vous devez utiliser le nom de domaine complet pour le headnode de HDInsight. Ce nom de domaine complet est uniquement accessible à partir du cluster, ou si le cluster est sur un réseau virtuel Azure, à partir d’autres ordinateurs sur le même réseau.

1. Pour obtenir l’URL du service de Oozie, utilisez ce qui suit :

        sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml

    Cela renverra une valeur semblable à la suivante :

        <name>oozie.base.url</name>
        <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>

    La partie **http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie** est l’URL à utiliser avec la commande Oozie.

2. Pour créer une variable d’environnement pour l’URL, afin que vous n’êtes pas obligé de le taper pour chaque commande, utilisez ce qui suit :

        export OOZIE_URL=http://HOSTNAMEt:11000/oozie

    Remplacez l’URL par celle que vous avez précédemment reçu.

3. Pour soumettre la tâche, utilisez ce qui suit :

        oozie job -config job.xml -submit

    Charge des informations sur les tâche à partir de **job.xml** et soumet au Oozie, ce service ne s’exécute pas.

    Une fois la commande terminée, elle doit retourner l’ID de la tâche. Par exemple, `0000005-150622124850154-oozie-oozi-W`. Cela servira à gérer le travail.

4. Permet d’afficher le statut de la tâche à l’aide de la commande suivante. Entrez l’ID de tâche retourné par la commande précédente :

        oozie job -info <JOBID>

    Cela renvoie des informations semblables à ce qui suit.

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasbs:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Cette tâche a le statut `PREP`, qui indique qu’il a été envoyé, mais n’a pas encore été démarré.

4. Pour démarrer le travail, utilisez les éléments suivants :

        oozie job -start JOBID

    Si vous vérifiez l’état après cette commande, il sera dans un état d’exécution et informations sont renvoyées pour les actions au sein de la tâche.

5. Une fois la tâche terminée, vous pouvez vérifier que les données a été générées et exportées vers la table de base de données SQL à l’aide des commandes suivantes :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

    À la `1>` invite, entrez la commande suivante :

        SELECT * FROM mobiledata
        GO

    Vous devez recevoir des informations similaires à ce qui suit :

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Pour plus d’informations sur la commande de Oozie, consultez [l’Outil de ligne de commande Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

##<a name="oozie-rest-api"></a>API REST de Oozie

L’API REST de Oozie vous permettent de créer vos propres outils qui fonctionnent avec Oozie. HDInsight des informations spécifiques sur l’utilisation de l’API REST de Oozie sont les suivantes :

* **URI**: l’API reste accessible depuis l’extérieur du cluster à`https://CLUSTERNAME.azurehdinsight.net/oozie`

* **Authentification**: vous devez vous authentifier à l’API en utilisant le compte de cluster HTTP (admin) et le mot de passe. Par exemple :

        curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Pour plus d’informations sur l’utilisation de l’API REST de Oozie, consultez [l’API de Services Web Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

##<a name="oozie-web-ui"></a>Interface utilisateur Web de Oozie

L’interface utilisateur Web de Oozie fournit une vue basée sur le web dans l’état des travaux de Oozie sur le cluster. Il vous permet de vous permet d’afficher le statut de la tâche, la définition de travail, configuration, un graphique des actions dans les tâches et les journaux de la tâche. Vous pouvez également afficher les détails pour les actions d’un projet.

Pour accéder à l’interface utilisateur Web de Oozie, procédez comme suit :

1. Créer un tunnel SSH pour le cluster HDInsight. Pour plus d’informations sur la procédure à suivre, reportez-vous à la section [Utiliser SSH Tunneling pour accéder à l’interface utilisateur web de Ambari, ResourceManager, JobHistory, NameNode, Oozie et autre web, l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md).

2. Une fois qu’un tunnel a été créé, ouvrez l’interface utilisateur de Ambari web dans votre navigateur web. L’URI du site de Ambari est **https://CLUSTERNAME.azurehdinsight.net**. Remplacez **NOM_CLUSTER** avec le nom de votre cluster HDInsight de fonctionnant sous Linux.

3. Dans la partie gauche de la page, sélectionnez **Oozie**, **Liens rapides**et **L’interface utilisateur Web de Oozie**.

    ![image des menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Par défaut, l’interface utilisateur Web de Oozie afficher les tâches de Workflow en cours d’exécution. Pour afficher toutes les tâches de workflow, sélectionnez **Toutes les tâches**.

    ![Toutes les tâches affichées](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Sélectionnez un projet pour afficher plus d’informations sur la tâche.

    ![Infos travail](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. À partir de l’onglet Info de la tâche, vous pouvez voir des informations sur l’impression de base, ainsi que les actions individuelles au sein de la tâche. En utilisant les onglets en haut, vous pouvez visualiser l’accès de la définition de travail, Configuration des tâches, du journal de travaux ou afficher un dirigé acycliques graphique (DAG) de la tâche.

    * **Journal de tâche**: cliquez sur le bouton **GetLogs** pour obtenir tous les journaux pour le travail ou le champ **Permet d’entrer un filtre de recherche** permet de filtrer les journaux

        ![Journal de tâche](./media/hdinsight-use-oozie-linux-mac/joblog.png)

    * **JobDAG**: le DAG est une vue d’ensemble graphique des chemins de données effectuée par le biais du flux de travail

        ![Travail DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Sélectionnez une des actions à partir de l’onglet **Infos travail** apportera des informations sur l’action. Par exemple, sélectionnez l’action **RunHiveScript** .

    ![Info de l’action](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Vous pouvez afficher les détails de l’action, y compris un lien vers l' **URL de la Console**, qui peut être utilisé pour afficher des informations de JobTracker pour la tâche.

##<a name="scheduling-jobs"></a>Planification des travaux

Le coordinateur vous permet de spécifier un début, fin et fréquence de l’occurrence pour les tâches afin qu’elles puissent être planifiées pour certaines heures.

Pour définir un planning pour le flux de travail, procédez comme suit :

1. Utilisez ce qui suit pour créer un nouveau fichier nommé **coordinator.xml**:

        nano coordinator.xml

    Comme le contenu du fichier, utilisez ce qui suit :

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
          <action>
            <workflow>
              <app-path>${workflowPath}</app-path>
            </workflow>
          </action>
        </coordinator-app>

    Notez que cet exemple utilise `${...}` les variables seront remplacées par les valeurs de la définition de travail. Les variables sont les suivantes :

    * **${coordFrequency}**: délai entre les instances de la tâche en cours d’exécution
    * **${coordStart}**: heure de début de la tâche
    * **${coordEnd}**: l’heure de fin de travail
    * **${coordTimezone}**: tâches du coordinateur sont dans un fuseau horaire fixe aucune heure d’été (ce qui est généralement représentée à l’aide d’UTC). Ce fuseau horaire est appelé le « Oozie traitement de fuseau horaire »
    * **${wfPath}**: le chemin d’accès workflow.xml

2. Utilisez Ctrl-X, puis **o** et sur **entrée** pour enregistrer le fichier.

3. Pour le copier dans le répertoire de travail pour cette tâche, utilisez ce qui suit :

        hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml

4. Pour modifier le fichier **job.xml** , utilisez ce qui suit :

        nano job.xml

    Apportez les modifications suivantes :

    * Modification de `<name>oozie.wf.application.path</name>` à `<name>oozie.coord.application.path</name>`. Cette option demande à Oozie pour exécuter le fichier coordinateur au lieu du fichier de flux de travail

    * Ajoutez, qui définit une variable utilisée dans le coordinator.xml pour pointer vers l’emplacement de workflow.xml :

            <property>
              <name>workflowPath</name>
              <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
            </property>

        Remplacez les valeurs de **mycontainer** et **mystorageaccount** avec les valeurs utilisées dans d’autres entrées dans le fichier job.xml.

    * Ajoutez ce qui suit, qui définissent le début, la fin et la fréquence à utiliser pour le fichier coordinator.xml :

            <property>
              <name>coordStart</name>
              <value>2015-06-25T12:00Z</value>
            </property>

            <property>
              <name>coordEnd</name>
              <value>2015-06-27T12:00Z</value>
            </property>

            <property>
              <name>coordFrequency</name>
              <value>1440</value>
            </property>

            <property>
              <name>coordTimezone</name>
              <value>UTC</value>
            </property>

        Ces définie à 12:00 PM le 25 juin 2015, l’heure de début, heure de fin le 27 juin 2015 et l’intervalle d’exécution de cette tâche quotidienne (la fréquence est exprimée en minutes, par conséquent, 24 heures x 60 minutes = 1440 minutes.) Enfin, le fuseau horaire est défini en heure UTC.

5. Utilisez Ctrl-X, puis **o** et sur **entrée** pour enregistrer le fichier.

6. Pour exécuter la tâche, utilisez la commande suivante :

        oozie job -config job.xml -run

    Cela va soumettre et démarrer la tâche.

7. Si vous visitez l’interface utilisateur Web de Oozie et sélectionnez l’onglet **Tâches de coordinateur** , vous devez similaire à ce qui suit :

    ![onglet tâches de coordinateur](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Notez l’entrée **Suivante de matérialisation** . C’est lors de l’exécution de la tâche suivante.

8. Comme pour le projet de workflow antérieur, la sélection de l’écriture dans l’interface utilisateur web affiche des informations sur la tâche :

    ![Infos de travail coordinateur](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    Notez que cela ne montre que d’exécution réussie du travail, pas des actions dans le flux de travail planifié. Pour afficher, sélectionnez une des entrées de **l’Action** . Cela affichera des informations similaires à celles extraites pour la tâche de flux de travail antérieure.

    ![Info de l’action](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

##<a name="troubleshooting"></a>Résolution des problèmes

Lors de la résolution des problèmes avec les travaux d’Oozie, le Oozie UI est très utile car elle vous permet de visualiser facilement les deux journaux Oozie permet, ainsi que des liens vers les journaux de JobTracker pour des tâches telles que les requêtes de ruche MapReduce. En général, le modèle de résolution des problèmes doit être :

1. Affichez la tâche dans l’interface utilisateur Web de Oozie.

2. S’il existe une erreur ou échec pour une action spécifique, sélectionnez l’action pour voir si le champ du **Message d’erreur** fournit plus d’informations sur l’échec.

3. S’il est disponible, vous pouvez utiliser l’URL de l’action pour afficher des détails supplémentaires (tels que les journaux de JobTracker,) pour l’action.

Voici les erreurs spécifiques que vous pouvez rencontrer et leur résolution.

###<a name="ja009-cannot-initialize-cluster"></a>JA009 : Impossible d’initialiser le cluster

**Symptômes**: l’état du travail devient **interrompue**. Détails de la tâche affiche l’état de RunHiveScript en tant que **START_MANUAL**. En sélectionnant l’action permettra de connaître le message d’erreur suivant :

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Cause**: les adresses de la WASB utilisés dans le fichier **job.xml** ne contiennent pas le conteneur de stockage ou le nom de compte de stockage. Le format d’adresse WASB doit être `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Résolution**: modification des adresses WASB utilisés par la tâche.

###<a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002 : Oozie ne peut pas emprunter l’identité &lt;utilisateur >

**Symptômes**: l’état du travail devient **interrompue**. Détails de la tâche affiche l’état de RunHiveScript en tant que **START_MANUAL**. En sélectionnant l’action permettra de connaître le message d’erreur suivant :

    JA002: User: oozie is not allowed to impersonate <USER>

**Cause**: paramètres d’autorisation actuels ne permettent pas de Oozie d’emprunter l’identité du compte d’utilisateur spécifié.

**Résolution**: Oozie n’est autorisé à emprunter l’identité des utilisateurs dans le groupe **d’utilisateurs** . Utilisez le `groups USERNAME` pour afficher les groupes dont le compte d’utilisateur est membre de. Si l’utilisateur n’est pas un membre du groupe **d’utilisateurs** , utilisez la commande suivante pour ajouter l’utilisateur au groupe :

    sudo adduser USERNAME users

> [AZURE.NOTE] Elle peut prendre plusieurs minutes avant d’HDInsight reconnaît que l’utilisateur a été ajouté au groupe.

###<a name="launcher-error-sqoop"></a>Lanceur d’erreur (Sqoop)

**Symptômes**: va de l’état du travail devient **KILLED**. Détails de la tâche affiche l’état de RunSqoopExport en tant **qu’erreur**. En sélectionnant l’action permettra de connaître le message d’erreur suivant :

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Cause**: Sqoop ne peut pas charger le pilote de base de données nécessaire pour accéder à la base de données.

**Résolution**: lors de l’utilisation de Sqoop à partir d’un projet Oozie, vous devez inclure le pilote de base de données avec les autres ressources (telles que workflow.xml), utilisé par la tâche.

Vous devez également référencer l’archive contenant le pilote de base de données à partir de la `<sqoop>...</sqoop>` section de workflow.xml.

Par exemple, pour le travail de ce document, vous utilisez les étapes suivantes :

1. Copiez le fichier sqljdbc4.1.jar dans le répertoire /tutorials/useoozie :

         hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar

2. Modifier le workflow.xml pour ajouter les éléments suivants sur une nouvelle ligne ci-dessus `</sqoop>`:

        <archive>sqljdbc41.jar</archive>

##<a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à définir un flux de travail Oozie et comment exécuter une tâche Oozie. Pour en savoir plus sur l’utilisation de HDInsight, consultez les articles suivants :

- [Utiliser un coordinateur Oozie temporels avec HDInsight][hdinsight-oozie-coordinator-time]
- [Téléchargement des données pour les projets de Hadoop de HDInsight][hdinsight-upload-data]
- [Utilisez Sqoop avec Hadoop dans HDInsight][hdinsight-use-sqoop]
- [Utilisez la ruche avec Hadoop sur HDInsight][hdinsight-use-hive]
- [Utilisez des porcs avec Hadoop sur HDInsight][hdinsight-use-pig]
- [Développer les programmes Java MapReduce pour HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
