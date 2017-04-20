<properties
    pageTitle="HBase didacticiel : mise en route de HBase dans Hadoop | Microsoft Azure"
    description="Suivez ce didacticiel HBase mise en route avec Apache HBase Hadoop dans HDInsight. Créer des tables à partir du shell HBase et les interroger à l’aide de la ruche."
    keywords="Apache, hbase, hbase, hbase, shell, didacticiel de hbase"
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
    ms.date="10/21/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>HBase didacticiel : mise en route avec Apache HBase basée sur Windows la Hadoop dans HDInsight

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Apprenez à créer des clusters de HBase dans HDInsight, créer des tables HBase et interroger les tables à l’aide de Apache de la ruche. Pour obtenir des informations générales sur HBase, consultez [vue d’ensemble de l’HDInsight HBase][hdinsight-hbase-overview].

Les informations contenues dans ce document sont spécifiques aux clusters de HDInsight de basées sur Windows. Pour plus d’informations sur les clusters basés sur Windows, utilisez le sélecteur de tabulations en haut de la page pour basculer.

> [AZURE.NOTE] HBase (version 0.98.0) sur HDInsight de fonctionnant sous Windows n’est disponible pour une utilisation avec des clusters de HDInsight 3.1 (en se basant sur Apache Hadoop et fils 2.4.0). Pour plus d’informations sur la version, consultez [Nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?][hdinsight-versions]

## <a name="before-you-begin"></a>Avant de commencer

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer ce didacticiel HBase, vous devez disposer des éléments suivants :

- **Abonnement A Microsoft Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Une station de travail** avec Visual Studio 2013 ou supérieur : pour obtenir des instructions, consultez [Installation de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Créer le cluster de HBase

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Pour créer un cluster de HBase à l’aide du portail Azure**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **Nouveau** ou **+** dans le coin supérieur gauche d’angle, puis cliquez sur **données + Analytique**, **HDInsight**.
3. Entrez les valeurs suivantes :

    - **Nom de cluster** - Entrez un nom pour identifier ce cluster.
    - **Type de cluster** - Select **HBase**.
    - **Système d’exploitation en cluster** , sélectionnez **Windows**.  Pour la création de HBase de basé sur Linux cluster, voir [HBase didacticiel : mise en route avec Apache HBase Hadoop dans HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
    - **Version** - sélectionnez une version de HBase.
    - **Abonnement** - Sélectionnez votre abonnement Azure utilisé pour la création de ce cluster.
    - **Groupe de ressources** - créer un nouveau groupe de ressources Azure ou sélectionnez-en une existante. Pour plus d’informations, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)
    - **Informations d’identification** - pour les clusters basés sur Windows, vous pouvez créer un utilisateur de cluster (ou HTTP utilisateur, utilisateur du service HTTP web) et un utilisateur du Bureau à distance. Cliquez sur **Activer le Bureau à distance** pour ajouter les informations d’identification de l’utilisateur de bureau à distance. La section suivante requiert le protocole RDP.
    - La **Source de données** - créer un nouveau compte de stockage Azure ou sélectionnez un compte de stockage Azure existant à utiliser comme système de fichiers par défaut pour le cluster. L’emplacement par défaut du compte de stockage détermine l’emplacement de l’emplacement du cluster. Le compte de stockage par défaut et le cluster doivent localiser conjointement dans le même centre de données.
    - **Niveaux de tarification de nœud** - sélectionnez le nombre de serveurs de la région pour le cluster HBase

        > [AZURE.WARNING] Pour une disponibilité élevée des services de HBase, vous devez créer un cluster qui contient au moins **trois** nœuds. Cela garantit que, si un nœud tombe en panne, les régions de données HBase sont disponibles sur les autres nœuds.

        > Si vous apprenez HBase, toujours choisir 1 pour la taille de cluster et supprimez le cluster après chaque utilisation, à réduire les coûts.

    - **Configuration facultative** - Azure de configuration réseau virtuel, et configurer les actions de Script et ajouter des comptes de stockage supplémentaires.

4. Cliquez sur **créer**.

>[AZURE.NOTE] Après la suppression d’un cluster de HBase, vous pouvez créer un autre cluster HBase en utilisant le même compte de stockage par défaut et le conteneur de blob par défaut. Le nouveau cluster reprendra les tables HBase que vous avez créé dans le cluster d’origine. Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

## <a name="create-tables-and-insert-data"></a>Créer des tables et d’insérer des données

Actuellement, il existe deux façon d’accéder aux HBase. Cette section décrit l’utilisation de l’interpréteur de commandes HBase. La section suivante traite de l’utilisation du Kit de développement .NET.

Pour la plupart des gens, les données s’affichent dans la forme d’un tableau :

![données tabulaires de hbase hdinsight][img-hbase-sample-data-tabular]

Dans HBase qui est une implémentation de BigTable, les mêmes données ressemble à :

![données de bigtable de hbase de hdinsight][img-hbase-sample-data-bigtable]

Il vous paraîtront plus claires lorsque vous avez terminé la procédure suivante.  

**Pour utiliser le shell HBase**

1. Utiliser le protocole RDP pour se connecter à votre cluster HBase dans HDInsight. Pour les instructions RDP, consultez [Hadoop de gérer des clusters dans HDInsight en utilisant le portail Azure][hdinsight-manage-portal].
2. Au sein de votre session RDP, cliquez sur le raccourci de la **ligne de commande Hadoop** situé sur le bureau.
3. Ouvrez le shell HBase :

        cd %HBASE_HOME%\bin
        hbase shell

4. Créer un HBase avec les deux familles de colonne :

        create 'Contacts', 'Personal', 'Office'
        list
5. Insérer des données :

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Obtenir une ligne unique

        get 'Contacts', '1000'

    Vous verrez les mêmes résultats que l’utilisation de la commande d’analyse étant donné qu’une seule ligne.

    Pour plus d’informations sur le schéma de la table Hbase, consultez [Introduction à la conception de schéma HBase][hbase-schema]. Pour d’autres commandes de HBase, consultez le [guide de référence de Apache HBase][hbase-quick-start].


6. Quitter l’interpréteur de commandes

        exit

**Chargement de données par bloc dans la table HBase de contacts**

HBase inclut plusieurs méthodes de chargement des données dans des tables. Pour plus d’informations, consultez [chargement de bloc](http://hbase.apache.org/book.html#arch.bulk.load).


Un exemple de fichier de données a été chargé dans un conteneur public blob, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. Le contenu du fichier de données est :

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Vous pouvez créer un fichier texte et télécharger le fichier à votre compte de stockage si vous le souhaitez. Pour les instructions, voir [télécharger les données pour les projets de Hadoop de HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Cette procédure utilise la table de HBase de Contacts que vous avez créé dans la dernière procédure.

1. Au sein de votre session RDP, cliquez sur le raccourci de la **ligne de commande Hadoop** situé sur le bureau.
2. Changer de répertoire :

        cd %HBASE_HOME%\bin

3. Exécutez la commande suivante pour transformer le fichier de données de StoreFiles et de la banque d’informations à un chemin d’accès relatif spécifié par Dimporttsv.bulk.output :

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput à la table HBase :

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Vous pouvez ouvrir le shell HBase et utilisez la commande scan pour répertorier le contenu de la table.



## <a name="use-hive-to-query-hbase-tables"></a>Utilisez la ruche d’interroger des tables HBase

Vous pouvez interroger des données stockées dans HBase à l’aide de la ruche. Cette section crée une table de ruche qui mappe à la table HBase et l’utilise pour interroger les données de la table HBase.

**Pour ouvrir le panneau de commandes de cluster**

1. Accédez à **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Entrez le nom d’utilisateur Hadoop utilisateur et le mot de passe. Le nom d’utilisateur par défaut est **admin** et le mot de passe que vous avez saisi au cours du processus de création. Un nouvel onglet de navigateur s’ouvre.
6. Cliquez sur **Éditeur de la ruche** en haut de la page. L’éditeur de la ruche ressemble à ceci :

    ![Tableau de bord du cluster HDInsight.][img-hdinsight-hbase-hive-editor]

**Pour exécuter des requêtes de la ruche**

1. Entrez le script HiveQL suivant dans l’éditeur de la ruche et cliquez sur **Envoyer** pour créer une Table qui mappe à la table HBase de la ruche. Assurez-vous que vous avez créé la table d’exemple référencée plus haut dans ce didacticiel, à l’aide de l’interpréteur de commandes HBase avant d’exécuter cette instruction.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    Patientez jusqu'à ce que les mises à jour de **l’état** **terminé**.

2. Entrez le script HiveQL suivant dans l’éditeur de la ruche, puis cliquez sur **Envoyer**. La requête de la ruche interroge les données de la table HBase :

        SELECT count(*) FROM hbasecontacts;

4. Pour récupérer les résultats de la requête de la ruche, cliquez sur le lien **Afficher les détails** dans la fenêtre de la **Session de travail** lorsque la tâche est terminée. Il y aura qu’un seul fichier de sortie de projet dans la mesure où vous placez un enregistrement dans la table HBase.




**Pour rechercher le fichier de sortie**

1. Dans la Console de la requête, cliquez sur **Explorateur de fichiers**.
2. Cliquez sur le compte de stockage Azure qui est utilisé comme système de fichiers par défaut pour l’HBase du cluster.
3. Cliquez sur le nom du cluster HBase. Le conteneur de compte de stockage Azure par défaut utilise le nom du cluster.
4. Cliquez sur **utilisateur**, puis cliquez sur **administration**. (Ceci est le nom d’utilisateur Hadoop.)
6. Cliquez sur le nom de la tâche à l’heure **De dernière modification** qui correspond à l’exécution lorsque la requête sélectionnez la ruche.
4. Cliquez sur **stdout**. Enregistrez le fichier et ouvrez le fichier avec le bloc-notes. Il y a un fichier de sortie.

    ![Éditeur de HBase de HDInsight ruche Explorateur][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>Utilisation de la bibliothèque de client API REST de HBase .NET

Vous devez télécharger la bibliothèque client API REST de HBase pour .NET de GitHub et générez le projet de sorte que vous pouvez utiliser le Kit de développement .NET HBase. La procédure suivante inclut les instructions pour cette tâche.

1. Créez une application C# Visual Studio Desktop Console Windows.
2. Ouvrez la Console du Gestionnaire de package NuGet en cliquant sur **Outils** > **Du Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.
3. Exécutez la commande suivante de NuGet dans la console :

        Install-Package Microsoft.HBase.Client

5. Ajoutez les instructions **using** suivantes en haut du fichier :

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Remplacez la fonction **Main** par les éléments suivants :

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Définissez les trois premières variables dans la fonction **Main** .
8. Appuyez sur **F5** pour exécuter l’application.

## <a name="check-cluster-status"></a>Vérifier l’état du cluster

HBase dans HDInsight est livré avec une interface utilisateur Web pour l’analyse des clusters. À l’aide de l’interface utilisateur Web, vous pouvez demander des statistiques ou des informations sur les régions.

Pour ouvrir l’interface utilisateur Web, vous devez RDP dans le cluster et puis cliquez sur le raccourci de l’interface utilisateur Web de HMaster les informations sur votre bureau ou utilisez l’URL suivante dans un navigateur web :

    http://zookeeper[0-2]:60010/master-status

Dans un cluster à haute disponibilité, vous trouverez un lien vers le nœud en cours actif HBase maître qui héberge l’interface utilisateur Web.

##<a name="delete-the-cluster"></a>Supprimer du cluster
Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>Quel est l’avenir ?
Dans ce didacticiel HBase pour HDInsight, vous avez appris comment créer un cluster de HBase et comment créer des tables et afficher les données de ces tables à partir du shell HBase. Vous l’avez également appris comment utiliser une requête de ruche sur les données dans les tables HBase et comment utiliser les API de reste de HBase C# pour créer un tableau de HBase et de récupérer des données à partir de la table.

Pour plus d’informations, voir :

- [Vue d’ensemble de l’HDInsight HBase][hdinsight-hbase-overview].
HBase est un Apache, open source, base de données NoSQL basé sur Hadoop qui fournit un accès aléatoire et forte cohérence pour de grandes quantités de données non structurées et semi-structurées.
- [Créer des clusters de HBase sur un réseau virtuel d’Azure][hdinsight-hbase-provision-vnet].
Avec l’intégration de réseau virtuel, HBase les clusters peuvent être déployés au même réseau virtuel que vos applications afin que les applications puissent communiquer directement avec HBase.
- [HBase de configurer la réplication dans HDInsight](hdinsight-hbase-geo-replication.md). Apprenez à configurer la réplication HBase sur deux centres de données Azure.
- [Analyser le sentiment Twitter avec HBase de HDInsight][hbase-twitter-sentiment].
Apprenez à effectuer en temps réel [analyse de sentiment](http://en.wikipedia.org/wiki/Sentiment_analysis) de données à l’aide de HBase dans un cluster Hadoop dans HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png
