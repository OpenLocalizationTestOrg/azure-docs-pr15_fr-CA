<properties 
   pageTitle="Utilisez Apache Phoenix et non dans des HDInsight | Microsoft Azure" 
   description="Apprenez comment utiliser Apache Phoenix dans HDInsight et installer et configurer un écureuil sur votre station de travail pour vous connecter à un cluster de HBase dans HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Utilisez Apache Phoenix avec les clusters basés sur Linux de HBase dans HDInsight  

Découvrez comment utiliser [Apache Phoenix](http://phoenix.apache.org/) dans HDInsight et comment utiliser SQLUltraLite. Pour plus d’informations sur Phoenix, consultez [Phoenix en 15 minutes ou moins](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). La grammaire Phoenix, consultez [Grammaire de Phoenix](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Les informations de version de Phoenix dans HDInsight, reportez-vous à la section [Nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?] [hdinsight-versions].

##<a name="use-sqlline"></a>Utilisez SQLUltraLite
[SQLUltraLite](http://sqlline.sourceforge.net/) est un utilitaire de ligne de commande pour exécuter SQL. 

###<a name="prerequisites"></a>Conditions préalables
Avant de pouvoir utiliser SQLUltraLite, vous devez disposer des éléments suivants :

- **Cluster A HBase dans HDInsight**. Pour plus d’informations sur la disposition HBase de cluster, consultez [mise en route de HBase Apache dans HDInsight][hdinsight-hbase-get-started].
- **Se connecter au cluster HBase via le protocole de bureau à distance**. Pour obtenir des instructions, reportez-vous à la section [Hadoop de gérer des clusters dans HDInsight en utilisant le portail classique Azure][hdinsight-manage-portal].


Lorsque vous vous connectez à un cluster de HBase, vous devez vous connecter à l’un de l’envieront. Chaque cluster HDInsight a 3 envieront. 

**Pour trouver le nom d’hôte soigneur**

1. Ouvrir les Ambari, accédez à **https://<ClusterName>. azurehdinsight.net**.
2. Entrez le nom d’utilisateur HTTP (cluster) et le mot de passe pour vous connecter.
3. Dans le menu de gauche, cliquez sur **soigneur** . 3 **Soigneur serveur** répertorié s’affiche.
4. Cliquez sur le **Serveur de soigneur** est répertorié. Dans le volet Sommaire, rechercher le **nom d’hôte**. Il est semblable à *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Utiliser SQLUltraLite**

1. La connexion au cluster à l’aide de SSH. Pour obtenir des instructions, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, Unix ou de OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) en fonction de votre ordinateur client du système d’exploitation.

2. À partir de SSH, exécutez les commandes suivantes pour exécuter SQLUltraLite :

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Exécutez les commandes suivantes pour créer une table HBase et insérer des données :

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

Pour plus d’informations, consultez [SQLUltraLite manuel](http://sqlline.sourceforge.net/#manual) et la [Grammaire de Phoenix](http://phoenix.apache.org/language/index.html).


 
##<a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment utiliser Apache Phoenix dans HDInsight.  Pour plus d’informations, reportez-vous à la section.

- [Vue d’ensemble de l’HDInsight HBase][hdinsight-hbase-overview]: HBase est un Apache, open source, base de données NoSQL basé sur Hadoop qui fournit un accès aléatoire et forte cohérence pour de grandes quantités de données non structurées et semi-structurées.
- [Configurer des clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet]: avec l’intégration de réseau virtuel, HBase les clusters peuvent être déployés au même réseau virtuel que vos applications afin que les applications puissent communiquer directement avec HBase.
- [HBase de configurer la réplication dans HDInsight](hdinsight-hbase-geo-replication.md): Apprenez à configurer la réplication HBase sur deux centres de données Azure. 
- [Analyser le sentiment Twitter avec HBase de HDInsight][hbase-twitter-sentiment]: Apprenez à effectuer en temps réel [analyse de sentiment](http://en.wikipedia.org/wiki/Sentiment_analysis) de données à l’aide de HBase dans un cluster Hadoop dans HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
