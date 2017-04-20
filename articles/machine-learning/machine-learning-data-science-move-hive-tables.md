<properties
    pageTitle="Créer et charger des données dans les tables de ruche depuis le stockage Blob | Microsoft Azure"
    description="Créer des tables de la ruche et de charger des données dans l’objet blob à la ruche de tables"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


#<a name="create-and-load-data-into-hive-tables-from-azure-blob-storage"></a>Créer et charger des données dans les tables de ruche depuis le stockage blob Azure

Cette rubrique présente des requêtes de ruche génériques qui créer des tables de la ruche et de chargent des données depuis le stockage blob Azure. Quelques conseils sont également fourni sur le partitionnement des tables de la ruche et sur l’utilisation de l’optimisé ligne en colonnes (ORC) pour améliorer les performances de la requête de mise en forme.

Ce **menu** liens vers des rubriques qui décrivent comment faire pour ajouter des données dans les environnements où les données peuvent être stockées et traitées pendant le processus de Science de données (TDSP) équipe de cible.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez :

* Créer un compte de stockage Azure. Si vous avez besoin d’instructions, reportez-vous à la section [comptes de stockage sur Azure](../storage/storage-create-storage-account.md). 
* Mise en service d’un cluster d’Hadoop personnalisée avec le service HDInsight.  Si vous avez besoin d’instructions, voir [Personnaliser les Hadoop d’Azure HDInsight clusters analytique avancée](machine-learning-data-science-customize-hadoop-cluster.md).
* L’accès distant activé au cluster, connecté et ouvert la console de ligne de commande Hadoop. Si vous avez besoin d’instructions, reportez-vous à la section [accès le nœud de tête de Hadoop Cluster](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

## <a name="upload-data-to-azure-blob-storage"></a>Télécharger les données vers le stockage blob Azure
Si vous avez créé un ordinateur virtuel Azure en suivant les instructions fournies dans [configurer une machine virtuelle Azure analytique avancé](machine-learning-data-science-setup-virtual-machine.md), ce fichier de script doit ont été téléchargé vers le *C:\\les utilisateurs\\\<nom d’utilisateur\>\\Documents\\données Science Scripts* répertoire sur l’ordinateur virtuel. Ces requêtes de la ruche n’ont besoin que vous branchez votre propre schéma de données et la configuration du stockage blob Azure dans les champs appropriés pour être prêt pour envoi.

Nous supposons que les données des tables de la ruche sont **compressé sous forme de tableau** , et que les données ont été téléchargées à la valeur par défaut (ou plus) conteneur du compte de stockage utilisé par le cluster Hadoop.

Si vous souhaitez des exercices pratiques sur les **Données de voyage de Taxi NYC**, vous devez :

- fichiers de [Données de voyage de Taxi NYC](http://www.andresmh.com/nyctaxitrips) **Télécharger** le 24 (12 fichiers de voyage et des fichiers de tarif 12),
- **décompressez** tous les fichiers dans des fichiers .csv, puis
- **Télécharger** les par défaut (ou conteneur approprié) du compte de stockage Azure qui a été créé par la procédure décrites dans la rubrique de [Personnaliser les Hadoop d’Azure HDInsight clusters avancée des processus Analytique et de la technologie](machine-learning-data-science-customize-hadoop-cluster.md) . Le processus pour télécharger les fichiers .csv pour le conteneur par défaut sur le compte de stockage peut être trouvé sur cette [page](machine-learning-data-science-process-hive-walkthrough.md#upload).


## <a name="submit"></a>Comment soumettre des requêtes de la ruche

Requêtes de ruche peuvent être envoyés à l’aide :

1. [Soumettre des requêtes de ruche depuis la ligne de commande via Hadoop dans headnode d’Hadoop cluster](#headnode)
2. [Soumettre des requêtes de ruche avec l’éditeur de la ruche](#hive-editor)
3. [Soumettre des requêtes de ruche avec les commandes de PowerShell Azure](#ps)

Requêtes de la ruche sont type SQL. Si vous êtes familiarisé avec SQL, vous trouverez la [ruche pour feuille de triche utilisateurs SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) utiles.

Lors de l’envoi d’une requête de la ruche, vous pouvez également contrôler la destination de la sortie à partir de requêtes de la ruche, qu’il s’agisse sur l’écran ou dans un fichier local sur le nœud principal ou à un objet blob Azure.


###<a name="headnode"></a>1. soumettre des requêtes de ruche depuis la ligne de commande via Hadoop dans headnode d’Hadoop cluster

Si la requête de la ruche est complexe, soumettre directement dans le nœud de tête de l’Hadoop cluster généralement conduit à contourner plus rapidement à soumettre avec un éditeur de la ruche ou de Azure PowerShell scripts.

Ouvrir une session sur le nœud principal du cluster Hadoop, ouvrez la ligne de commande Hadoop sur le bureau du nœud principal et entrez la commande `cd %hive_home%\bin`.

Vous avez trois manières de soumettre des requêtes de ruche dans la ligne de commande Hadoop :

* directement
* l’utilisation de fichiers de .hql
* avec la console de commande de ruche

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Soumettre des requêtes de ruche directement dans Hadoop ligne de commande.

Vous pouvez exécuter la commande comme `hive -e "<your hive query>;` à soumettre des requêtes de ruche simples directement dans Hadoop ligne de commande. Voici un exemple, où le cadre rouge indique la commande qui envoie la requête de la ruche et la zone verte présente la sortie de la requête de la ruche.

![Créer l’espace de travail](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Soumettre des requêtes de ruche dans les fichiers de .hql

Lorsque la requête de la ruche est plus complexe et comporte plusieurs lignes, la modification de requêtes dans la ligne de commande ou de la console de commande de ruche n’est pas pratique. Une alternative consiste à utiliser un éditeur de texte dans le nœud de tête du cluster Hadoop pour enregistrer les requêtes de la ruche dans un fichier .hql dans un répertoire local du nœud principal. Puis la requête de la ruche dans le fichier .hql peut être envoyée à l’aide du `-f` argument comme suit :

    hive -f "<path to the .hql file>"

![Créer l’espace de travail](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-3.png)


**Supprimer l’impression d’écran de progression état de requêtes de la ruche**

Par défaut, après que la requête de la ruche est soumis en ligne de commande Hadoop, l’état d’avancement de la tâche de mappage/réduction est imprimé à l’écran. Pour supprimer l’impression de l’écran de la progression de tâche de mappage/réduction, vous pouvez utiliser un argument `-S` (« S » en majuscules) dans la commande de ligne comme suit :

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Soumettre des requêtes de ruche dans la console de commande de ruche.

Vous pouvez également tout d’abord saisir la console de commande de ruche en exécutant la commande `hive` de ligne de commande Hadoop, puis envoient des requêtes de ruche dans la console de commande de ruche. Voici un exemple. Dans cet exemple, les deux cases rouges en surbrillance les commandes utilisées pour accéder à la console de commande de ruche et la requête de la ruche présentée dans la console de commande de ruche, respectivement. La zone verte met en évidence le résultat de la requête de la ruche.

![Créer l’espace de travail](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-2.png)

Les exemples précédents de sortie directement les résultats de la requête ruche à l’écran. Vous pouvez également écrire la sortie dans un fichier local sur le nœud principal, ou à un objet blob Azure. Ensuite, vous pouvez utiliser les autres outils analyser de manière approfondie la sortie de requêtes de la ruche.

**Sortie de ruche de résultats de la requête dans un fichier local.**

Pour sortir des résultats de requête de ruche dans un répertoire local sur le nœud principal, vous devez soumettre la requête de la ruche dans la ligne de commande Hadoop comme suit :

    hive -e "<hive query>" > <local path in the head node>

Dans l’exemple suivant, la sortie de la requête de la ruche est écrit dans un fichier `hivequeryoutput.txt` dans le répertoire `C:\apps\temp`.

![Créer l’espace de travail](./media/machine-learning-data-science-move-hive-tables/output-hive-results-1.png)

**Sortie des résultats de la requête à un blob Azure ruche**

Vous pouvez également exporter les résultats de la requête ruche pour un blob Azure, dans le conteneur par défaut du cluster Hadoop. La requête de la ruche pour cela est la suivante :

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Dans l’exemple suivant, la sortie de la requête de la ruche est écrit dans un répertoire de blob `queryoutputdir` dans le conteneur par défaut du cluster Hadoop. Ici, vous devrez fournir le nom du répertoire, sans le nom de l’objet blob. Une erreur est levée si vous fournissez des noms de répertoire et de blob, tel que `wasb:///queryoutputdir/queryoutput.txt`.

![Créer l’espace de travail](./media/machine-learning-data-science-move-hive-tables/output-hive-results-2.png)

Si vous ouvrez le conteneur par défaut du cluster Hadoop avec Explorateur de stockage Azure, vous pouvez voir le résultat de la requête de ruche, comme illustré dans la figure suivante. Vous pouvez appliquer le filtre (mis en évidence par un cadre rouge) pour récupérer uniquement le blob avec les lettres spécifiées dans les noms de.

![Créer l’espace de travail](./media/machine-learning-data-science-move-hive-tables/output-hive-results-3.png)

###<a name="hive-editor"></a>2. soumettre des requêtes de ruche avec l’éditeur de la ruche

Vous pouvez également utiliser la Console de la requête (éditeur de la ruche) en entrant une URL du formulaire *https://&#60 ; Nom du cluster Hadoop >.azurehdinsight.net/Home/HiveEditor* dans un navigateur web. Vous devez être connecté voir cette console, et vous devez par conséquent vos informations d’identification Hadoop le cluster.

###<a name="ps"></a>3. soumettre des requêtes de ruche avec les commandes de PowerShell Azure

Vous pouvez également utiliser PowerShell pour soumettre des requêtes de la ruche. Pour plus d’informations, voir [soumettre la ruche des travaux à l’aide de PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a>Créer des tables et base de données de la ruche

Les requêtes de la ruche sont partagés dans le [référentiel de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) et peuvent être téléchargées à partir de là.

Voici la requête de la ruche qui crée une table de la ruche.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Voici les descriptions des champs dont vous avez besoin pour brancher et autres configurations :

- **& #60 ; nom de la base de données >**: le nom de la base de données que vous souhaitez créer. Si vous souhaitez uniquement utiliser la base de données par défaut, la requête de *créer la base de données...* peut être omis.
- **& #60 ; nom de la table >**: le nom de la table que vous souhaitez créer dans la base de données spécifiée. Si vous souhaitez utiliser la base de données par défaut, la table peut être directement référencée par *& #60 ; nom de la table >* sans & #60 ; nom de la base de données >.
- **& #60 ; séparateur de champ >**: le séparateur qui délimite les champs dans le fichier de données à télécharger pour la table de la ruche.
- **& #60 ; séparateur de ligne >**: le séparateur qui délimite les lignes dans le fichier de données.
- **& #60 ; l’emplacement de stockage >**: l’emplacement de stockage Azure pour enregistrer les données des tables de la ruche. Si vous ne spécifiez pas *emplacement & #60 ; emplacement de stockage >*, la base de données et les tables sont stockées dans *ruche/magasin/* directory dans le conteneur par défaut du cluster ruche par défaut. Si vous voulez spécifier l’emplacement de stockage, l’emplacement de stockage doit se trouver dans le conteneur par défaut pour la base de données et les tables. Cet emplacement doit être appelé emplacement le conteneur par défaut du cluster dans le format de *' wasb : / / / & #60 ; répertoire 1 > /'* ou *' wasb : / / / & #60 ; répertoire 1 > / & #60 ; répertoire 2 > /'*, etc.. Une fois que la requête est exécutée, les répertoires relatifs sont créés dans le conteneur par défaut.
- **TBLPROPERTIES("Skip.Header.Line.Count"="1")**: si le fichier de données a une ligne d’en-tête, vous devez ajouter cette propriété **à la fin** de la requête *Création de table* . Sinon, la ligne d’en-tête est chargée sous la forme d’un enregistrement à la table. Si le fichier de données ne dispose pas d’une ligne d’en-tête, cette configuration peut être omise dans la requête.

## <a name="load-data"></a>Charger les données dans les tables de la ruche
Voici la requête de la ruche qui charge des données dans une table de la ruche.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **& #60 ; le chemin d’accès à des données blob >**: si le fichier blob à télécharger pour la table de la ruche se trouve dans le conteneur par défaut du cluster HDInsight Hadoop, la *& #60 ; le chemin d’accès à des données blob >* doit être dans le format *' wasb : / / / & #60 ; répertoire dans ce conteneur > / & #60 ; le nom du fichier blob >'*. Le fichier blob peut également être un conteneur supplémentaire du cluster HDInsight Hadoop. Dans ce cas, *& #60 ; le chemin d’accès à des données blob >* doit être dans le format *' wasb : / / & #60 ; conteneur name>@&#60;storage nom de compte >.blob.core.windows.net/ & #60 ; le nom du fichier blob >'*.

    >[AZURE.NOTE] Les données blob à télécharger pour la table de la ruche doit se trouver dans un conteneur supplémentaire du compte de stockage pour le cluster Hadoop ou par défaut. Dans le cas contraire, la requête de *Chargement des données* échoue à se plaindre qu’il ne peut pas accéder aux données.


## <a name="partition-orc"></a>Rubriques avancées : partitionné de table et la banque de données de ruche au format des ORC

Si les données sont volumineuses, le partitionnement de la table est utile pour les requêtes qui doivent analyser plusieurs partitions de la table. Par exemple, il est raisonnable partitionner les données du journal d’un site web en dates.

En plus du partitionnement des tables de la ruche, il est également intéressant de stocker les données de la ruche dans le format optimisé ligne en colonnes (ORC). Pour plus d’informations sur la mise en forme des ORC, consultez <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">les fichiers à l’aide des ORC améliore les performances lorsque la ruche est lecture, l’écriture et traitement des données</a>.

### <a name="partitioned-table"></a>Table partitionnée
Voici la requête de la ruche qui crée une table partitionnée et charge les données.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Lors de l’interrogation de tables partitionnées, il est recommandé d’ajouter la condition de la partition au **début** de la `where` clause que ceci améliore l’efficacité de la recherche de manière significative.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Des données de ruche ORC

Vous ne peut pas charger directement des données depuis le stockage blob dans des tables de ruche qui est stocké dans le format ORC. Voici les étapes que vous devez prendre pour charger les données à partir d’Azure BLOB aux tables de ruche stockées au format des ORC.

Créer une table externe **Stocké le fichier en tant que texte** et charge données depuis le stockage blob à la table.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Créez une table interne avec le même schéma que la table externe à l’étape 1, avec le même séparateur de champs et stocker les données de la ruche dans le format ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Sélectionnez les données à partir de la table externe à l’étape 1 et l’insérer dans la table des ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

>[AZURE.NOTE] Si la table de fichier texte *& #60 ; nom de la base de données >. & #60 ; nom de la table fichier texte externe >* comporte des partitions, à l’étape 3, le `SELECT * FROM <database name>.<external textfile table name>` commande sélectionne la variable partition en tant que champ dans le jeu de données renvoyé. Insérez le *& #60 ; nom de la base de données >. & #60 ; nom de la table ORC >* échoue depuis *& #60 ; nom de la base de données >. & #60 ; nom de la table ORC >* n’a pas la variable de la partition en tant que champ dans le schéma de table. Dans ce cas, vous devez sélectionner les champs à insérer à *& #60 ; nom de la base de données >. & #60 ; nom de la table ORC >* comme suit :

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Il est prudent de supprimer le *& #60 ; nom de la table fichier texte externe >* lorsqu’une fois toutes les données à l’aide de la requête suivante a été inséré dans *& #60 ; nom de la base de données >. & #60 ; nom de la table ORC >*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Après avoir suivi cette procédure, vous devez disposer d’une table avec des données dans le format ORC prêt à l’emploi.  
