<properties
    pageTitle="HBase didacticiel : mise en route avec les clusters basés sur Linux de HBase dans Hadoop | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>HBase didacticiel : mise en route avec Apache HBase basé sur Linux d’Hadoop dans HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Apprenez à créer un cluster de HBase dans HDInsight, créer des tables de HBase et des tables de requêtes à l’aide de ruche. Pour obtenir des informations générales sur HBase, consultez [vue d’ensemble de l’HDInsight HBase][hdinsight-hbase-overview].

Les informations contenues dans ce document sont spécifiques aux clusters de HDInsight de fonctionnant sous Linux. Pour plus d’informations sur les clusters basés sur Windows, utilisez le sélecteur de tabulations en haut de la page pour basculer.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel HBase, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Sécuriser les Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [coin](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Créer le cluster de HBase

La procédure suivante utilise un modèle de gestionnaire de ressources Azure pour créer un cluster basé sur Linux de HBase de version 3.4 et la valeur par défaut dépendante compte de stockage Azure. Pour comprendre les paramètres utilisés dans la procédure et d’autres méthodes de création de cluster, voir [clusters basés sur Linux de créer les Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Le modèle se trouve dans un conteneur blob publique. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la blade de **déploiement de personnalisé** , entrez ce qui suit :

    - **Abonnement**: sélectionnez votre abonnement Azure qui sera utilisé pour créer le cluster.
    - **Groupe de ressources**: créer un nouveau groupe de gestion des ressources Azure ou en utiliser une existante.
    - **Emplacement**: indiquez l’emplacement du groupe de ressources. 
    - **Nom_Cluster**: entrez un nom pour le cluster HBase que vous allez créer.
    - **Mot de passe et le nom de connexion de cluster**: le nom d’ouverture de session par défaut est **admin**.
    - **Mot de passe et nom d’utilisateur SSH**: le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez le renommer.
     
    Autres paramètres sont facultatifs.  
    
    Chaque cluster a une dépendance de compte de stockage Azure Blob. Après avoir supprimé un cluster, les données se présentent dans le compte de stockage. Le nom de compte de stockage cluster par défaut est le nom du cluster avec « banque » est ajouté. Il est codé en dur dans la section variables de modèle.
        
3. Sélectionnez **J’accepte les termes et les conditions mentionnées ci-dessus**, puis cliquez sur **achat**. Il faut environ 20 min pour créer un cluster.


>[AZURE.NOTE] Après la suppression d’un cluster de HBase, vous pouvez créer un autre cluster HBase en utilisant le même conteneur de blob par défaut. Le nouveau cluster reprendra les tables HBase que vous avez créé dans le cluster d’origine. Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

## <a name="create-tables-and-insert-data"></a>Créer des tables et d’insérer des données

Vous pouvez utiliser le protocole SSH pour vous connecter à des clusters de HBase, puis HBase Shell pour créer des tables de HBase, insérer des données et interroger les données. Pour plus d’informations sur l’utilisation du protocole SSH, consultez [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) et [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Pour la plupart des gens, les données s’affichent dans la forme d’un tableau :

![HDInsight HBase les données sous forme de tableau][img-hbase-sample-data-tabular]

Dans HBase qui est une implémentation de BigTable, les mêmes données ressemble à :

![Données de bigtable de HDInsight HBase][img-hbase-sample-data-bigtable]

Il deviendra plus clair lorsque vous avez terminé la procédure suivante.  


**Pour utiliser le shell HBase**

1. À partir de SSH, exécutez la commande suivante :

        hbase shell

4. Créer un HBase avec les familles de deux colonnes :

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

    Pour plus d’informations sur le schéma de la table HBase, consultez [Introduction à la conception de schéma HBase][hbase-schema]. Pour d’autres commandes de HBase, consultez le [guide de référence de Apache HBase][hbase-quick-start].

6. Quitter l’interpréteur de commandes

        exit



**Chargement de données par bloc dans la table HBase de contacts**

HBase inclut plusieurs méthodes de chargement des données dans des tables.  Pour plus d’informations, consultez [chargement de bloc](http://hbase.apache.org/book.html#arch.bulk.load).


Un exemple de fichier de données a été chargé dans un conteneur public blob, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Le contenu du fichier de données est :

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

1. À partir de SSH, exécutez la commande suivante pour transformer le fichier de données de StoreFiles et de la banque d’informations à un chemin d’accès relatif spécifié par Dimporttsv.bulk.output :.  Si vous êtes dans un environnement de HBase, utilisez la commande exit pour quitter.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput à la table HBase :

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Vous pouvez ouvrir le shell HBase et utilisez la commande scan pour répertorier le contenu de la table.



## <a name="use-hive-to-query-hbase"></a>Utilisez la ruche à la requête HBase

Vous pouvez interroger des données dans les tables HBase à l’aide de ruche. Cette section crée une table de ruche qui mappe à la table HBase et l’utilise pour interroger les données de la table HBase.

1. Ouvrez **PuTTY**et connectez-vous au cluster.  Consultez les instructions fournies dans la procédure précédente.
2. Ouvrez le shell de la ruche.

       hive
3. Exécutez le script HiveQL suivant pour créer une Table qui mappe à la table HBase de la ruche. Assurez-vous que vous avez créé la table d’exemple référencée plus haut dans ce didacticiel, à l’aide de l’interpréteur de commandes HBase avant d’exécuter cette instruction.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Exécutez le script HiveQL suivant pour interroger les données de la table HBase :

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Utilisez les API de reste de HBase à l’aide de roulage

> [AZURE.NOTE] Lors de l’utilisation de roulage ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en fournissant le nom d’utilisateur et le mot de passe de l’administrateur de cluster HDInsight. Vous devez également utiliser le nom du cluster dans le cadre de la ressource identificateur URI (Uniform) utilisée pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez le **nom d’utilisateur** de l’utilisateur pour s’authentifier sur le cluster et remplacer le **mot de passe** avec le mot de passe pour le compte d’utilisateur. Remplacez **NOM_CLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé via [l’authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous les demandes à l’aide de HTTPS (HTTP sécurisé) pour vous assurer que vos informations d’identification sont transmises de manière sécurisée sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Vous devriez recevoir une réponse semblable à la suivante :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u** - le nom d’utilisateur et le mot de passe utilisé pour authentifier la demande.
    * **-G** - indique qu’il s’agit d’une demande GET.

2. Utilisez la commande suivante pour lister les tables HBase existants :

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Pour créer une nouvelle table HBase avec les deux familles de colonne, utilisez la commande suivante :

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    Le schéma est fourni au format JSon.

4. Pour insérer des données, utilisez la commande suivante :

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    Vous devez en base64 encoder les valeurs spécifiées dans le commutateur -d.  Dans l’exemple :

    - MTAwMA == : 1000
    - UGVyc29uYWw6TmFtZQ == : nom du personnel :
    - Sm9obiBEb2xl : John Dole

    [false-clés de ligne](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) vous permet d’insérer plusieurs valeur (par lots).

5. Pour obtenir une ligne, utilisez la commande suivante :

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Pour plus d’informations sur le reste de HBase, consultez le [Guide de référence de HBase Apache](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Vérifier l’état du cluster

HBase dans HDInsight est livré avec une interface utilisateur Web pour l’analyse des clusters. À l’aide de l’interface utilisateur Web, vous pouvez demander des statistiques ou des informations sur les régions.

SSH peut également servir pour les demandes locales, telles que des demandes web, au cluster HDInsight du tunnel. La demande est ensuite dirigée vers la ressource demandée comme si elle avait a été créée sur le nœud principal du cluster HDInsight. Pour plus d’informations, consultez [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Pour établir une session de tunneling de SSH**

1. Ouvrez **PuTTY**.  
2. Si vous avez fourni un code SSH lorsque vous avez créé votre compte d’utilisateur pendant le processus de création, vous devez effectuer l’opération suivante pour sélectionner la clé privée à utiliser lors de l’authentification au cluster :

    Dans la **catégorie**, développez la **connexion**, développez **SSH**et sélectionnez **Auth**. Enfin, cliquez sur **Parcourir** et sélectionnez le fichier .ppk qui contient votre clé privée.

3. Dans la **catégorie**, cliquez sur **la Session**.
4. À partir des options de base pour votre écran de session mastic, entrez les valeurs suivantes :

    - **Nom d’hôte**: l’adresse SSH de votre champ de serveur dans le nom d’hôte (ou adresse IP) HDInsight. L’adresse SSH est votre nom de cluster, puis **-ssh.azurehdinsight.net**. Par exemple, *mon_cluster-ssh.azurehdinsight.net*.
    - **Port**: 22. Le port de la headnode principale ssh est 22.  
5. Dans la section **catégories** , à gauche de la boîte de dialogue, développez la **connexion**, développez **SSH**, puis cliquez sur **Tunnels**.
6. Fournissez les informations suivantes sur les Options de contrôle de formulaire de transfert de port SSH :

    - **Port source** : le port sur le client que vous souhaitez transférer. Par exemple, 9876.
    - **Dynamique** - proxy SOCKS dynamique permet le routage.
7. Cliquez sur **Ajouter** pour ajouter les paramètres.
8. Cliquez sur **Ouvrir** en bas de la boîte de dialogue pour ouvrir une connexion SSH.
9. Lorsque vous y êtes invité, connectez-vous au serveur à l’aide d’un compte SSH. Vous établissez une session SSH et activer le tunnel.

**Pour trouver le nom de domaine complet de l’envieront à l’aide de Ambari**

1. Accédez à https://<ClusterName>.azurehdinsight.net/.
2. Entrez vos informations d’identification de cluster utilisateur compte deux fois.
3. Dans le menu de gauche, cliquez sur **soigneur**.
4. Cliquez sur un des trois liens **Soigneur Server** à partir de la liste récapitulative.
5. Copier le **nom d’hôte**. Par exemple, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Pour configurer un programme client (Firefox) et vérifiez l’état du cluster**

1. Ouvrez Firefox.
2. Cliquez sur le bouton de **Menu Ouvrir** .
3. Cliquez sur **Options**.
4. Cliquez sur **Options avancées**, cliquez sur **réseau**, puis cliquez sur **paramètres**.
5. Permet de sélectionner **la configuration manuelle de proxy**.
6. Entrez les valeurs suivantes :

    - **Hôte de SOCKS**: localhost
    - **Port**: utiliser le même port que vous avez configurée dans le SSH Putty tunneling.  Par exemple, 9876.
    - **SOCKS v5**: (sélectionné)
    - **DNS distant**: (sélectionné)
7. Cliquez sur **OK** pour enregistrer les modifications.
8. Accédez à http://&lt;nom de domaine complet d’un soigneur > : 60010/maître-status.

Dans un cluster à haute disponibilité, vous trouverez un lien vers le nœud en cours actif HBase maître qui héberge l’interface utilisateur Web.

##<a name="delete-the-cluster"></a>Supprimer du cluster

Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel HBase pour HDInsight, vous avez appris comment créer un cluster de HBase et comment créer des tables et afficher les données de ces tables à partir du shell HBase. Vous avez également appris comment utiliser une requête de ruche sur les données dans les tables HBase et comment utiliser les API de reste de HBase C# pour créer un tableau de HBase et de récupérer des données à partir de la table.

Pour plus d’informations, voir :

- [Vue d’ensemble de l’HDInsight HBase][hdinsight-hbase-overview]: HBase est un Apache, open source, base de données NoSQL basé sur Hadoop qui fournit un accès aléatoire et forte cohérence pour de grandes quantités de données non structurées et semi-structurées.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
