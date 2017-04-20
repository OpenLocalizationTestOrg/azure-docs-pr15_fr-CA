<properties
    pageTitle="Utilisez Hadoop Sqoop basé sur Linux de HDInsight | Microsoft Azure"
    description="Découvrez comment exécuter Sqoop d’importation et d’exportation entre un Hadoop basé sur Linux sur cluster de HDInsight et d’une base de données SQL d’Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Utilisez Sqoop avec Hadoop dans HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Sqoop pour importer et exporter entre un HDInsight de basé sur Linux cluster et la base de données de base de données de SQL Azure ou SQL Server.

> [AZURE.NOTE] Les étapes décrites dans cet article utilisent le protocole SSH pour se connecter à un cluster basé sur Linux de HDInsight. Les clients Windows permet également Azure PowerShell et Kit de développement .NET HDInsight pour travailler avec Sqoop sur les clusters basés sur Linux. Utilisez le sélecteur de tabulations pour ouvrir ces articles.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Cluster A Hadoop dans HDInsight** et une __Base de données de SQL Azure__: les étapes décrites dans ce document sont basés sur le cluster et la base de données créée à l’aide du document de [base de données SQL et de créer un cluster](hdinsight-use-sqoop.md#create-cluster-and-sql-database) . Si vous disposez d’un cluster d’HDInsight et de la base de données de SQL, vous pouvez remplacer celles pour les valeurs utilisées dans ce document.
- **Station de travail**: un ordinateur avec un client SSH.

##<a name="install-freetds"></a>Installation de FreeTDS

1. Utiliser le protocole SSH pour se connecter au cluster HDInsight de fonctionnant sous Linux. L’adresse à utiliser lors de la connexion est `CLUSTERNAME-ssh.azurehdinsight.net` et le port est `22`.

    Pour plus d’informations sur l’utilisation du protocole SSH pour se connecter à HDInsight, consultez les documents suivants :

    * **Les clients Linux, Unix ou OS X**: voir [se connecter à un cluster basé sur Linux de HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Les clients Windows**: voir [se connecter à un cluster basé sur Linux de HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Utilisez la commande suivante pour installer le FreeTDS :

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    Servira de FreeTDS en plusieurs étapes pour vous connecter à la base de données de SQL.

##<a name="create-the-table-in-sql-database"></a>Créez la table dans la base de données de SQL

> [AZURE.IMPORTANT] Si vous utilisez un cluster de HDInsight et de la base de données de SQL créés à l’aide de la procédure dans la [base de données SQL et de créer un cluster](hdinsight-use-sqoop.md), ignorez les étapes de cette section comme la base de données et la table ont été créés dans le cadre de la procédure décrite dans ce document.

1. À partir de la connexion SSH HDInsight, utilisez la commande suivante pour vous connecter au serveur de base de données de SQL et Crète la table qui sera utilisée dans le reste de ces étapes :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Vous recevez une sortie similaire à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. À la `1>` invite, entrez les lignes suivantes :

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Lors de la `GO` instruction est entrée, les instructions précédentes seront évaluées. Tout d’abord, la table **mobiledata** est créée, puis un index ordonné en clusters est ajouté à celle-ci (requis par la base de données de SQL).

    Pour vérifier que la table a été créée, utilisez ce qui suit :

        SELECT * FROM information_schema.tables
        GO

    Vous devriez voir une sortie similaire à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Entrez `exit` à la `1>` invite pour quitter l’utilitaire tsql.

##<a name="sqoop-export"></a>Exportation de Sqoop

3. À partir de la connexion SSH à HDInsight, se, la commande suivante pour vérifier que les Sqoop peuvent voir votre base de données SQL :

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Cela doit retourner une liste de bases de données, y compris la base de données **sqooptest** que vous avez créé précédemment.

4. Utilisez la commande suivante pour exporter des données à partir de **hivesampletable** vers la table **mobiledata** :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Cette option indique à Sqoop pour vous connecter à la base de données SQL pour la base de données **sqooptest** et d’exporter des données à partir de la **wasbs : / / / ruche/magasin/hivesampletable** (fichiers physiques pour la *hivesampletable*,) dans la table **mobiledata** .

5. Une fois la commande terminée, utilisez les éléments suivants pour vous connecter à la base de données à l’aide de TSQL :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Une fois connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées vers la table **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Vous devez voir une liste des données de la table. Type de `exit` pour quitter l’utilitaire tsql.

##<a name="sqoop-import"></a>Importation de Sqoop

1. Utilisez ce qui suit pour importer des données à partir de la table **mobiledata** dans la base de données SQL de la **wasbs : / / / didacticiels/usesqoop/importeddata** répertoire HDInsight :

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Les données importées aura les champs sont séparés par un caractère de tabulation et les lignes vont être fermées par un caractère de nouvelle ligne.

2. Une fois l’importation terminée, utilisez la commande suivante pour lister les données dans le nouveau répertoire :

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>À l’aide de SQL Server

Vous pouvez également utiliser Sqoop pour importer et exporter des données à partir de SQL Server, dans votre centre de données ou sur une Machine virtuelle hébergée dans Azure. Les différences entre l’utilisation de la base de données SQL et SQL Server sont :

* Les HDInsight et les SQL Server doivent être sur le même réseau virtuel Azure

    > [AZURE.NOTE] HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement, et il ne fonctionne pas actuellement avec l’affinité du groupe de réseaux virtuels.

    Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme *site à site* ou *point-à-site*.

    > [AZURE.NOTE] Pour les réseaux virtuels de **point-à-site** , SQL Server doit exécuter le client VPN application de configuration, qui est disponible à partir du **tableau de bord** de votre configuration Azure réseau virtuel.

    Pour plus d’informations réseau virtuel d’Azure, consultez [Vue d’ensemble de réseau virtuel](../virtual-network/virtual-networks-overview.md).

* SQL Server doit être configuré pour permettre l’authentification SQL. Pour plus d’informations, consultez [Choisir un Mode d’authentification](https://msdn.microsoft.com/ms144284.aspx)

* Il se peut que vous deviez configurer SQL Server pour accepter les connexions à distance. Pour plus d’informations, reportez-vous à la section [comment résoudre les problèmes liés à la connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

* Vous devez créer la base de données de **sqooptest** dans SQL Server à l’aide d’un utilitaire tel que **SQL Server Management Studio** ou **tsql** - les étapes d’utilisation de l’infrastructure du langage commun Azure ne fonctionnent que pour la base de données de SQL Azure

    Les instructions de TSQL pour créer la table **mobiledata** sont similaires ceux utilisés pour SQL de base de données, à l’exception de la création d’un clusterd index - ce n’est pas requis pour SQL Server :

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Lors de la connexion pour le SQL Server à partir de HDInsight, vous devrez peut-être utiliser l’adresse IP de la de SQL Server, sauf si vous avez configuré un système DNS (Domain Name System) pour résoudre les noms sur le réseau virtuel Azure. Par exemple :

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>Limitations

* Exportation en bloc - des HDInsight de base avec Linux, le connecteur de Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données de SQL Azure ne prend pas en charge insertions en bloc.

* Le traitement par lots - avec HDInsight basé sur Linux, lorsque vous utilisez la `-batch` passer lors de l’exécution des insertions, Sqoop effectue plusieurs insertions plutôt que le traitement par lot des opérations d’insertion.

##<a name="next-steps"></a>Étapes suivantes

Maintenant, vous avez appris comment utiliser Sqoop. Pour plus d’informations, voir :

- [Utiliser Oozie avec HDInsight][hdinsight-use-oozie]: action de Sqoop d’utilisation dans un flux de travail Oozie.
- [Analyser des données de retard de vol avec HDInsight][hdinsight-analyze-flight-data]: utilisez la ruche pour analyser le vol retarder les données et utilisez Sqoop pour exporter des données vers une base de données SQL d’Azure.
- [Téléchargement des données vers HDInsight][hdinsight-upload-data]: rechercher d’autres méthodes de chargement des données de stockage des objets Blob HDInsight/Azure.



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
