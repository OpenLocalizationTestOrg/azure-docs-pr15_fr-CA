<properties
   pageTitle="Optimisez vos requêtes de ruche pour une exécution plus rapide de HDInsight | Microsoft Azure"
   description="Découvrez comment optimiser vos requêtes de ruche Hadoop dans HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>Optimiser les requêtes de la ruche pour Hadoop dans HDInsight

Par défaut, les clusters d’Hadoop ne sont pas optimisées pour les performances. Cet article décrit quelques-unes des méthodes optimisation de performances ruche plus courantes que vous pouvez appliquer à nos requêtes.

##<a name="scale-out-worker-nodes"></a>Mettre à l’échelle des nœuds du travailleur

Augmentation du nombre de nœuds de traitement d’un cluster peut exploiter plus REDUCTEURS à exécuter en parallèle et les mappeurs. Il existe deux façons vous pouvez augmenter la mise à l’échelle dans HDInsight :

- Au moment de la disposition, vous pouvez spécifier le nombre de nœuds de travailleur à un portail Azure, Azure PowerShell ou interface de ligne de commande multiplateforme.  Pour plus d’informations, voir [clusters de disposition HDInsight](hdinsight-provision-clusters.md). L’écran suivantes affichent la configuration du nœud de travailleur sur le portail Azure :

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- Au moment de l’exécution, vous pouvez également déployer un cluster sans avoir à recréer un. Cela est indiqué ci-dessous.
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Pour plus de détails sur les différents ordinateurs virtuels pris en charge par HDInsight, reportez-vous à la section [HDInsight de tarification](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="enable-tez"></a>Activer Tez

[Tez de Apache](http://hortonworks.com/hadoop/tez/) est un moteur d’exécution alternatifs au moteur MapReduce :

![tez_1][image-hdi-optimize-hive-tez_1]


Tez est plus rapide car :

- Exécution graphique acyclique dirigé (DAG) sous la forme d’une seule tâche dans le moteur MapReduce, le DAG exprimé nécessite des chaque ensemble de mappeurs à suivre par un ensemble des réducteurs. Ainsi, plusieurs tâches MapReduce à être tourner pour chaque requête de la ruche. Tez ne possède pas de telles contraintes et peut traiter des DAG complexe sous la forme d’une tâche, ce qui réduit les frais de démarrage du projet.
- **Évite inutile écrit** En raison de plusieurs tâches, qui est lancés pour la même requête de ruche dans le moteur MapReduce, la sortie de chaque travail est destinée à très données intermédiaires. Dans la mesure où Tez réduit le nombre de tâches pour chaque requête de ruche qu’il est possible d’éviter l’écriture inutile.
- **Délais de démarrage réduit** Tez est mieux en mesure de réduire le délai de démarrage en réduisant le nombre de mappeurs dont il a besoin pour démarrer et en améliorant l’optimisation dans l’ensemble.
- **Réutilise les conteneurs** Chaque fois que possible Tez est capable de réutiliser les conteneurs pour vous assurer que la latence dus au démarrage des conteneurs est réduite.
- **Techniques d’optimisation continue** Traditionnellement, l’optimisation a été effectuée au cours de la phase de compilation. Toutefois, vous trouverez plus d’informations sur les entrées qui permettent une meilleure optimisation lors de l’exécution. Tez utilise des techniques d’optimisation continue qui lui permet d’optimiser davantage le planning dans la phase d’exécution.

Pour plus d’informations sur ces concepts, cliquez [ici](http://hortonworks.com/hadoop/tez/)

Vous pouvez apporter n’importe quelle requête ruche Tez activé en faisant précéder la requête avec le paramètre ci-dessous :

    set hive.execution.engine=tez;

Pour les clusters de HDInsight de basés sur Windows, Tez doit être activée lors de la disposition. Voici un exemple de script PowerShell d’Azure pour la mise en service d’un cluster d’Hadoop avec Tez activé :

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Basé sur Linux des clusters HDInsight ont Tez activé par défaut.
    

## <a name="hive-partitioning"></a>Partitionnement de la ruche

L’opération d’e/s est le goulot d’étranglement de performances pour exécuter des requêtes de la ruche. Les performances peuvent être améliorées si la quantité de données qui doivent être lue peut être réduite. Par défaut, les requêtes de ruche balayer les tables de ruche entières. C’est formidable pour les requêtes que les analyses de tables, toutefois pour les requêtes qui doivent analyser une petite quantité de données (par exemple, les requêtes avec filtrage), cela crée une surcharge superflue. Partitionnement de la ruche permet de requêtes ruche à accéder uniquement la quantité nécessaire de données dans les tables de la ruche.

Partitionnement de la ruche est implémenté en réorganisant les données brutes dans les nouveaux répertoires avec chaque partition à son propre répertoire - où la partition est définie par l’utilisateur. Le diagramme suivant illustre le partitionnement d’une table de la ruche en fonction de la colonne *année*. Un nouveau répertoire est créé pour chaque année.

![partitionnement][image-hdi-optimize-hive-partitioning_1]

Certaines considérations de cloisonnement :

- **Ne pas incomplète partition** - partitionnement sur des colonnes dont seules quelques valeurs peut provoquer des partitions très peu. Par exemple, sur le genre de partitionnement sera uniquement créer deux partitions à créer (mâle et femelle), ainsi que réduire la latence d’un maximum de moitié.

- **Ne pas trop partition** - sur l’autre extrême, création d’une partition sur une colonne avec une valeur unique (par exemple, userid) entraîne plusieurs partitions à l’origine de beaucoup de stress sur le namenode de cluster tel qu’il est nécessaire de traiter la grande quantité de répertoires.

- **Évitez de données incliner** - Choisissez votre clé de partitionnement avec soin, afin que toutes les partitions soient de même taille. Un exemple est le partitionnement sur *état* peut provoquer le nombre d’enregistrements sous Californie soit presque 30 x qui du Vermont en raison de la différence de la population.

Pour créer une table de partition, utilisez la clause *Partition By* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Une fois la table partitionnée est créée, vous pouvez soit créer un partitionnement statique ou le partitionnement dynamique.

- **Partitionnement statique** signifie que les données déjà sharded dans les répertoires appropriés, et vous pouvez demander des partitions de ruche manuellement en fonction de l’emplacement du répertoire. Ceci est illustré dans l’extrait de code ci-dessous.

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Le partitionnement dynamique** signifie que la ruche pour créer automatiquement des partitions pour vous. Dans la mesure où nous avons déjà créé la table de partitionnement de la table intermédiaire, il nous suffit est d’insérer des données dans la table partitionnée comme indiqué ci-dessous :

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Pour plus d’informations, consultez [Les Tables partitionnées](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##<a name="use-the-orcfile-format"></a>Utilisez le format ORCFile

Prend en charge différents formats de fichier de la ruche. Par exemple :

- **Texte**: il s’agit du format de fichier par défaut et fonctionne avec la plupart des scénarios
- **Avro**: fonctionne bien pour les scénarios d’interopérabilité
- **ORC/Parquet**: mieux aux performances

Format des ORC (optimisé ligne en colonnes) est un moyen très efficace pour stocker des données de la ruche. Par rapport aux autres formats, ORC présente les avantages suivants :

- prise en charge pour les types complexes, y compris les types DateTime et complexes structurées et semi-structurées
- jusqu'à 70 % compression
- indexe tous les 10 000 lignes permettant d’ignorer des lignes
- une baisse conséquente de l’exécution

Pour activer le format des ORC, vous créez une table avec la clause *stocké comme ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Ensuite, vous insérez des données dans la table ORC à partir de la table intermédiaire. Par exemple :

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Vous pouvez en savoir plus sur le format ORC [ici](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##<a name="vectorization"></a>Vectorisation

Vectorisation permet la ruche traiter un lot de lignes 1024 ensemble au lieu d’une ligne de traitement à la fois. Cela signifie que les simples opérations sont effectuent plus rapidement car moins de code interne doit s’exécuter.

Pour activer la vectorisation préfixe votre requête ruche avec le paramètre suivant :

    set hive.vectorized.execution.enabled = true;

Pour plus d’informations, consultez [exécution de la requête Vectorized](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##<a name="other-optimization-methods"></a>Autres méthodes d’optimisation

Il existe plusieurs méthodes d’optimisation que vous pouvez prendre en compte, par exemple :

- **La création de compartiments la ruche :** une technique qui permet à un cluster ou segment grands ensembles de données pour optimiser les performances de la requête.
- **L’optimisation de jointure :** l’optimisation de l’exécution de la requête de ruche planification améliorer l’efficacité de jointures et de réduire la nécessité d’indications de l’utilisateur. Pour plus d’informations, reportez-vous à [l’optimisation de jointure](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **augmenter les réducteurs**

##<a id="nextsteps"></a>Étapes suivantes
Dans cet article, vous avez appris plusieurs méthodes de l’optimisation de requête communs de ruche. Pour plus d’informations, consultez les articles suivants :

- [Utilisez la ruche Apache dans HDInsight](hdinsight-use-hive.md)
- [Analyser des données de retard de vol à l’aide de ruche dans HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analyser les données de Twitter à l’aide de la ruche dans HDInsight](hdinsight-analyze-twitter-data.md)
- [Analyser des données de capteur à l’aide de la Console de la ruche de requête sur Hadoop dans HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Utilisez la ruche avec HDInsight pour analyser les journaux à partir de sites Web](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
