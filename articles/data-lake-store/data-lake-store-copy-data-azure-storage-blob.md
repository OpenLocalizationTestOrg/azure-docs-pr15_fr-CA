<properties
   pageTitle="Copier des données d’objets BLOB de stockage Azure dans le magasin de données lac | Microsoft Azure"
   description="Utilisez l’outil AdlCopy pour copier les données d’objets BLOB de stockage Azure au magasin de données lac"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copier des données d’objets BLOB de stockage Azure au magasin de données lac

> [AZURE.SELECTOR]
- [À l’aide de DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [À l’aide de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Magasin de LAC de données Azure fournit un outil de ligne de commande, [AdlCopy](http://aka.ms/downloadadlcopy), pour copier des données à partir des sources suivantes :

* À partir du BLOB de stockage Azure dans le magasin de données lac. Vous ne pouvez pas utiliser AdlCopy pour copier les données de magasin de données LAC pour stockage Azure BLOB.

* Entre deux comptes de banque de LAC de données Azure. 

En outre, vous pouvez utiliser l’outil AdlCopy dans deux modes différents :

* **Autonome**où l’outil utilise les ressources de la banque de données LAC pour effectuer la tâche.

* **À l’aide d’un compte Analytique lac de données**, dans laquelle les unités affectées à votre compte Analytique lac de données sont utilisées pour effectuer l’opération de copie. Vous pouvez, si vous le souhaitez, utilisez cette option lorsque vous souhaitez pour effectuer la copie d’une manière prévisible.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- Conteneur **d’Objets BLOB de stockage Azure** avec des données.

- **Compte d’azure données lac Analytique (facultatif)** - en voir [mise en route avec Azure données lac Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pour obtenir des instructions sur la façon de créer un compte de banque de données lac.

- **Outil de AdlCopy**. Installez l’outil AdlCopy à partir de [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxe de l’outil AdlCopy

Utilisez la syntaxe suivante pour utiliser l’outil AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Les paramètres dans la syntaxe sont décrits ci-dessous :

| Option    | Description                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| Source    | Spécifie l’emplacement de la source de données dans le blob de stockage Azure. La source peut être un conteneur blob, un blob ou un autre magasin de données lac compte.                                                                                                                                                                                                                                                                                                 |
| Dest      | Spécifie la destination de la banque de données LAC pour copier vers.                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | Spécifie la clé d’accès de stockage pour la source de blob de stockage Azure. Cela est nécessaire uniquement si la source est un conteneur blob ou un objet blob.                                                                                                                                                                                                                                                                                                                                                 |
| Compte   | **Facultatif**. Utilisez cette option si vous souhaitez les compte Azure données lac Analytique permet d’exécuter le travail de copie. Si vous utilisez l’option/Account dans la syntaxe mais que vous ne spécifiez pas un compte de données lac Analytique, AdlCopy utilise un compte par défaut pour exécuter la tâche. En outre, si vous utilisez cette option, vous devez ajouter (Blob de stockage Azure) de source et de destination (banque de LAC de données Azure) comme sources de données pour votre compte de données lac Analytique.  |
| Unités     |  Spécifie le nombre d’unités de données lac Analytique qui sera utilisé pour le travail de copie. Cette option est obligatoire si vous utilisez **l’option/Account** pour spécifier le compte de données lac Analytique.
| Modèle   | Spécifie un modèle d’expression régulière qui indique les objets BLOB ou les fichiers à copier. AdlCopy utilise la mise en correspondance qui respecte la casse. Le modèle par défaut utilisé lorsque aucun modèle ne spécifié doit copier tous les éléments. Spécification de plusieurs modèles de fichier n’est pas pris en charge.                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utiliser des AdlCopy (comme autonome) pour copier des données à partir d’un blob de stockage Azure

1. Ouvrez une invite de commandes et accédez au répertoire dans lequel AdlCopy est installé, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un blob spécifique du conteneur source vers un magasin de LAC de données :

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Par exemple :

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] La syntaxe ci-dessus spécifie le fichier à copier vers un dossier dans le compte de la banque de données lac. Outil de AdlCopy crée un dossier si le nom de dossier spécifié n’existe pas.

    Vous devrez entrer les informations d’identification de l’abonnement Azure sous lequel vous avez votre compte de banque de données lac. Vous verrez une sortie semblable à la suivante :

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. Vous pouvez également copier tous les objets BLOB à partir d’un conteneur pour le compte de la banque de données lac à l’aide de la commande suivante :

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Par exemple :

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Utiliser des AdlCopy (comme autonome) pour copier des données à partir d’un autre compte de banque de données lac

Vous pouvez également utiliser AdlCopy pour copier des données entre deux comptes de banque de données lac.

1. Ouvrez une invite de commandes et accédez au répertoire dans lequel AdlCopy est installé, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un fichier spécifique à partir d’un seul compte de banque de données lac à un autre.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Par exemple :

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] La syntaxe ci-dessus spécifie le fichier à copier vers un dossier dans le compte de la banque de données lac de destination. Outil de AdlCopy crée un dossier si le nom de dossier spécifié n’existe pas.

    Vous devrez entrer les informations d’identification de l’abonnement Azure sous lequel vous avez votre compte de banque de données lac. Vous verrez une sortie semblable à la suivante :

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. La commande suivante copie tous les fichiers d’un dossier spécifique dans la source de données lac compte vers un dossier dans le compte de banque lac de données de destination.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilisez AdlCopy (avec le compte de données lac Analytique) pour copier les données

Vous pouvez également utiliser votre compte Analytique lac de données pour exécuter la tâche AdlCopy pour copier les données d’objets BLOB de stockage Azure au magasin de données lac. Vous utilisez généralement cette option lorsque les données à déplacer dans la plage de gigaoctets et téraoctets, et vous souhaitez que le débit des performances améliorées et prévisibles.

Pour utiliser votre compte Analytique du lac données avec AdlCopy pour copier à partir d’un Blob de stockage Azure, la source (Blob de stockage Azure) doit être ajoutée en tant que source de données pour votre compte de données lac Analytique. Pour obtenir des instructions sur l’ajout de sources de données supplémentaires à votre compte Analytique lac de données, voir [gestion de données lac Analytique du compte des sources de données](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Si vous copiez à partir d’un compte Azure données lac magasin comme source à l’aide d’un compte Analytique lac de données, vous n’avez pas besoin à associer au compte de données lac Analytique du compte de la banque de données lac. La nécessité d’associer la banque source au compte de données lac Analytique est uniquement lorsque la source est un compte de stockage Azure.

Exécutez la commande suivante pour copier à partir d’un blob Azure Storage dans un compte de banque lac de données à l’aide du compte de données lac Analytique :

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Par exemple :

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


De même, exécutez la commande suivante pour copier à partir d’un blob Azure Storage dans un compte de banque lac de données à l’aide du compte de données lac Analytique :

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Permet de copier des données à l’aide de critères spéciaux AdlCopy

Dans cette section, vous apprendrez comment utiliser AdlCopy pour copier les données d’une source (dans notre exemple ci-dessous nous utilisons Azure stockage Blob) pour un compte de banque lac de données de destination à l’aide de critères spéciaux. Par exemple, vous pouvez utiliser la procédure ci-dessous pour copier tous les fichiers avec l’extension .csv à partir du blob de la source vers la destination.

1. Ouvrez une invite de commandes et accédez au répertoire dans lequel AdlCopy est installé, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier tous les fichiers avec l’extension de *.csv à partir d’un blob spécifique du conteneur source vers un magasin de LAC de données :

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Par exemple :

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>Facturation

* Si vous utilisez l’outil AdlCopy comme autonome vous seront facturés des frais de sortie pour le déplacement des données, si la source du compte de stockage Azure n’est pas dans la même région comme magasin lac de données.

* Si vous utilisez l’outil AdlCopy avec votre compte de données lac Analytique, les [taux de facturation de données lac Analytique](https://azure.microsoft.com/pricing/details/data-lake-analytics/) standard s’appliquent.

## <a name="considerations-for-using-adlcopy"></a>Considérations sur l’utilisation de AdlCopy

* AdlCopy (pour la version 1.0.5), prend en charge la copie de données à partir de sources qui collectivement ont plusieurs milliers de fichiers et de dossiers. Toutefois, si vous rencontrez des problèmes de copie d’un groupe de données volumineux, vous pouvez distribuer les fichiers/dossiers dans des sous-dossiers différents et utiliser à la place le chemin d’accès à ces sous-dossiers comme source.

## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
