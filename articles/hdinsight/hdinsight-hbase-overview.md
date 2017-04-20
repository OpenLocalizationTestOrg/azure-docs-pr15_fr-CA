<properties
    pageTitle="Quel est HBase dans HDInsight ? | Microsoft Azure"
    description="Une introduction à HBase Apache dans HDInsight, une base de données NoSQL reposent sur Hadoop. En savoir plus sur les cas d’usage et de comparer des HBase aux autres clusters Hadoop."
    keywords="bigtable, nosql, ce qui est des hbase"
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
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Quel est HBase dans HDInsight : une base de données NoSQL de A qui fournit des fonctionnalités de type BigTable pour Hadoop

Apache HBase est une base de données NoSQL d’open source, qui est bâti sur Hadoop et basé sur Google BigTable. HBase fournit un accès aléatoire et forte cohérence pour de grandes quantités de données non structurées et semi-structurées dans une base de données schemaless organisé en familles de la colonne.

Données sont stockées dans les lignes d’une table, et de regroupement des données dans une ligne par la famille de la colonne. HBase est une base de données schemaless en ce sens que les colonnes, ni le type des données stockées dans les doivent être définies avant de les utiliser. Le code open source linéairement pour gérer plusieurs pétaoctets de données sur des milliers de nœuds. Il peut reposer sur la redondance des données, traitement par lots et d’autres fonctionnalités fournies par les applications distribuées dans l’écosystème Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>L’implémentation de HBase dans HDInsight d’Azure ?

HDInsight HBase est proposée comme un cluster managé qui est intégré dans l’environnement Azure. Les clusters sont configurés pour stocker les données directement dans le stockage Blob d’Azure, qui offre une faible latence et l’élasticité accrue dans le choix de performances et de coûts. Cela permet aux clients de créer des sites Web interactifs qui fonctionnent avec grands volumes de données, à créer des services qui stockent des données de capteur et de télémétrie provenant de millions de points de terminaison et d’analyser ces données de travaux d’Hadoop. HBase et Hadoop sont bons points de départ pour projet de données volumineuses dans Azure ; en particulier, il peut activer des applications en temps réel manipuler de grands ensembles de données.

La mise en oeuvre HDInsight exploite l’architecture de l’horizontale de HBase pour fournir automatique ont des tables, cohérence renforcée pour les lectures et écritures et basculement automatique. Les performances sont améliorées par la mise en cache en mémoire pour la lecture et haut débit en continu pour les écritures. Il est également disponible pour HDInsight HBase de configuration de réseau virtuel. Pour plus d’informations, voir [clusters de disposition HDInsight sur Azure Virtual Network] [hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>La gestion de données dans HDInsight HBase ?

Données qui peuvent être gérées dans HBase à l’aide de la `create`, `get`, `put`, et `scan` les commandes à partir du shell HBase. Données sont écrites dans la base de données à l’aide de `put` et de la lire à l’aide de `get`. Le `scan` commande est utilisée pour obtenir les données provenant de plusieurs lignes dans une table. Données peuvent également être gérées à l’aide de l’HBase C# API qui fournit une bibliothèque cliente sur l’API REST de HBase. Une base de données HBase peut également être interrogé à l’aide de ruche. Pour une introduction à ces modèles de programmation, reportez-vous à la section [mise en route de HBase avec Hadoop dans HDInsight][hbase-get-started]. Processeurs sont également disponibles, qui permettent de traitement des données dans les nœuds hébergeant la base de données.


## <a name="scenarios-use-cases-for-hbase"></a>Scénarios : Exemples d’utilisation pour HBase
Cas d’usage canonique pour le BigTable (et par extension, HBase) a été créé a été de recherche sur le web. Les moteurs de recherche créer des index qui mappent des conditions pour les pages web qui les contiennent. Mais il existe de nombreux autres cas d’usage HBase est adapté, dont plusieurs sont détaillées dans cette section.

- Valeur de la clé de banque

    HBase peut être utilisé comme magasin de clé-valeur, et qu’il est approprié pour la gestion des systèmes de messagerie. Facebook utilise HBase pour leur système de messagerie, et est idéal pour stocker et gérer les communications Internet. WebTable utilise HBase pour rechercher et gérer les tables extraites à partir des pages Web.

- Données de capteur

    HBase est utile pour capturer des données qui sont collectées de façon incrémentielle provenant de diverses sources. Cela inclut les analytique social, les séries temporelles, conserver les tableaux de bord interactifs et des tendances des compteurs et de gestion des systèmes de journal d’audit. Opérateur de Bloomberg Terminal Server et le temps série de base de données ouverte (OpenTSDB), qui stocke et fournit l’accès aux mesures recueillies sur le fonctionnement des systèmes de serveur sont des exemples.

- Requête en temps réel

    [Phoenix](http://phoenix.apache.org/) est un moteur de requête SQL pour Apache HBase. Elle est accessible sous la forme d’un pilote JDBC, et il permet d’interroger et de gérer les tables de HBase à l’aide de SQL.

- HBase en tant que plate-forme

    En l’utilisant comme un magasin de données, les applications peuvent exécuter sur HBase. Phoenix, OpenTSDB, Kiji et Titan sont des exemples. Les applications peuvent également intégrer avec HBase. Exemples ruche, porc, le mode série sur LAN.r, tempête, Flume, Impala, étincelle, ganglions rachidiens et Drill.


##<a name="next-steps"></a>Étapes suivantes

- [Mise en route de HBase avec Hadoop dans HDInsight][hbase-get-started]
- [Configurer des clusters HDInsight sur un réseau virtuel d’Azure] [hbase-provision-vnet]
- [Configurer la réplication de l’HBase de HDInsight](hdinsight-hbase-geo-replication.md)
- [Analyser le sentiment Twitter avec HBase dans HDInsight][hbase-twitter-sentiment]
- [Permet de créer des applications Java qui utilisent HBase avec HDInsight (Hadoop) Maven][hbase-build-java-maven]

##<a name="see-also"></a>Voir aussi

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable : Un système de stockage distribué de données structurées](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
