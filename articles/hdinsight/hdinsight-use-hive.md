<properties
    pageTitle="Découvrez ce qu’est la ruche et comment utiliser HiveQL | Microsoft Azure"
    description="Découvrez la ruche Apache et comment l’utiliser avec Hadoop dans HDInsight. Choisissez comment exécuter votre opération de ruche et HiveQL permettent d’analyser un exemple de fichier log4j Apache."
    keywords="hiveql, quelle est la ruche"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>Utilisez ruche et HiveQL avec Hadoop dans HDInsight pour analyser un exemple de fichier log4j Apache

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Dans ce didacticiel, vous allez apprendre à utiliser la ruche Apache dans Hadoop sur HDInsight et choisissez comment exécuter votre opération de la ruche. Vous découvrirez également comment analyser un exemple de fichier log4j Apache et de HiveQL.

##<a id="why"></a>Quelle est la ruche et pourquoi l’utiliser ?
[La ruche Apache](http://hive.apache.org/) est un système d’entrepôt de données pour Hadoop, ce qui permet une synthèse des données, l’interrogation et l’analyse des données à l’aide de HiveQL (un langage de requête semblable à SQL). Ruche peut être utilisé pour Explorer de manière interactive vos données ou pour créer des tâches de traitement des commandes réutilisables.

Ruche vous permet à la structure de projet sur des données en grande partie non structurées. Après avoir défini la structure, vous pouvez utiliser la ruche pour interroger ces données sans connaissance de Java ou MapReduce. **HiveQL** (le langage de requête ruche) vous permet d’écrire des requêtes avec des instructions qui sont similaires à T-SQL.

Ruche comprend comment travailler avec des données structurées et semi-structurées, tels que des fichiers texte dans lequel les champs sont délimités par des caractères spécifiques. Ruche prend également en charge personnalisée de **sérialiseur/désérialiseurs (SerDe)** pour les données structurées de manière irrégulière ou complexes. Pour plus d’informations, voir [comment utiliser un SerDe de JSON personnalisé avec HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Fonctions de défini par l’utilisateur (UDF)

Ruche peut également être étendu par le biais de **fonctions définies par l’utilisateur (UDF)**. Un fichier UDF vous permet d’implémenter des fonctionnalités ou la logique qui n’est pas facilement être modelée dans HiveQL. Pour obtenir un exemple de l’utilisation de fichiers UDF avec ruche, consultez les rubriques suivantes :

* [Utilisez un utilisateur Java définies fonction avec ruche](hdinsight-hadoop-hive-java-udf.md)

* [À l’aide de Python avec la ruche et de HDInsight de porc](hdinsight-python.md)

* [Utiliser C# avec la ruche et porc dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [L’ajout d’un fichier UDF personnalisé de la ruche à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Exemple de FDU de la ruche personnalisé pour convertir les formats de date/heure à l’horodatage de la ruche](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Tables externes de vs tables internes de la ruche

Il y a quelques choses à savoir sur la ruche des tables internes et externes :

- La commande **CREATE TABLE** crée une table interne. Le fichier de données doit se trouver dans le conteneur par défaut.
- La commande **CREATE TABLE** déplace le fichier de données vers le /hive/magasin/<TableName> dossier.
- La commande **Créer une TABLE externe** crée une table externe. Le fichier de données peut se trouver en dehors du conteneur par défaut.
- La commande **Créer une TABLE externe** ne déplace pas le fichier de données.
- La commande **Créer une TABLE externe** n’autorise pas les dossiers de l’emplacement. C’est la raison pourquoi le didacticiel est une copie du fichier exemple.log.

Pour plus d’informations, consultez [HDInsight : la ruche interne et externe Intro de Tables][cindygross-hive-tables].


##<a id="data"></a>Sur les exemples de données, un fichier de log4j Apache

Cet exemple utilise un exemple de fichier *log4j* , qui est stocké à **/example/data/sample.log** dans votre conteneur de stockage blob. Chaque journal dans le fichier se compose d’une ligne de champs contenant un `[LOG LEVEL]` champ afin d’afficher le type et la gravité, par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l’exemple précédent, le niveau de journalisation est erreur.

> [AZURE.NOTE] Vous pouvez également générer un fichier log4j à l’aide de l’outil d’enregistrement de [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) et puis chargez ce fichier vers le conteneur de l’objet blob. Pour obtenir des instructions, voir [Télécharger les données à HDInsight](hdinsight-upload-data.md) . Pour plus d’informations sur l’utilisation de stockage des objets Blob Azure avec HDInsight, reportez-vous à la section [Utiliser le stockage Blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

Les exemples de données sont stockées dans le stockage Azure Blob HDInsight utilise comme système de fichiers par défaut. HDInsight peut accéder aux fichiers stockés dans les blobs en utilisant le préfixe **wasb** . Par exemple, pour accéder au fichier exemple.log, vous utiliseriez la syntaxe suivante :

    wasbs:///example/data/sample.log

Stockage des objets Blob Azure étant le stockage par défaut pour les HDInsight, vous pouvez également accéder le fichier à l’aide de **/example/data/sample.log** de HiveQL.

> [AZURE.NOTE] La syntaxe de la **wasbs : / / /**, est utilisé pour accéder aux fichiers stockés dans le conteneur de stockage par défaut pour votre cluster HDInsight. Si vous avez spécifié des comptes de stockage supplémentaires lorsque vous configuré votre cluster, et que vous souhaitez accéder aux fichiers stockés dans ces comptes, vous pouvez accéder aux données en spécifiant le conteneur nom et stockage compte l’adresse, par exemple, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Projet d’exemple : projection colonnes de données délimité

Les instructions suivantes de HiveQL de projet seront colonnes délimitée par des données qui sont stockées dans la **wasbs : / / / / données** directory :

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

Dans l’exemple précédent, les instructions de HiveQL effectuent les actions suivantes :

* __la valeur hive.execution.engine=tez ;__: définit le moteur d’exécution à utiliser Tez. À l’aide de Tez au lieu de MapReduce permettent une augmentation des performances de la requête. Pour plus d’informations sur Tez, reportez-vous à la section [Utilisation des Tez de Apache pour améliorer les performances](#usetez) .

    > [AZURE.NOTE] Cette instruction n’est requis lors de l’utilisation d’un cluster basé sur Windows de HDInsight ; Tez est le moteur d’exécution par défaut pour les HDInsight de fonctionnant sous Linux.

* **DROP TABLE**: supprime le fichier de données et de la table si la table existe déjà.
* **Créer une TABLE externe**: crée une nouvelle table **externe** dans la ruche. Tables externes stockent uniquement la définition de table dans la ruche ; les données restent dans l’emplacement d’origine et dans le format d’origine.
* **FORMAT de ligne**: indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparés par un espace.
* **Emplacement de fichier texte en tant que stocké**: indique la ruche où les données sont stockées (le répertoire de données d’exemple) et qu’il est stocké en tant que texte. Les données peuvent être dans un seul fichier ou étendues à plusieurs fichiers dans le répertoire.
* **Sélectionner**: sélectionne un décompte de toutes les lignes où la colonne **t4** contient la valeur **[erreur]**. Cela doit retourner une valeur de **3** , car il y a trois lignes contenant cette valeur.
* **INPUT__FILE__NAME comme '%.log'** - indique la ruche que nous devons uniquement renvoyer des données à partir de fichiers se terminant par. journal. Cela limite la recherche au fichier exemple.log qui contient les données et empêche de renvoyer des données à partir de l’autre exemple de fichiers de données qui ne correspondent pas le schéma que nous avons défini.

> [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous prévoyez des données sous-jacentes afin d’être mis à jour par une source externe, tel qu’un processus de téléchargement automatique des données, ou par une autre opération MapReduce et si vous souhaitez toujours ruche les requêtes à utiliser les données les plus récentes.
>
> Suppression d’une table externe est **pas** supprimer les données, mais uniquement la définition de table.

Après avoir créé la table externe, les instructions suivantes permettent de créer une table **interne** .

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Ces instructions effectuent les actions suivantes :

* **Créer TABLE IF NOT EXISTS**: crée une table, s’il n’existe pas déjà. Étant donné que le mot clé **externe** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le magasin de données de ruche et est gérée complètement par ruche.
* **Stockées des ORC comme**: stocke les données dans un format d’optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement efficace et optimisé pour le stockage des données de la ruche.
* REMPLACEMENT de **INSERT... Sélectionnez**: sélectionne les lignes dans la table **log4jLogs** qui contient **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

> [AZURE.NOTE] À la différence des tables externes, suppression d’une table interne supprime également les données sous-jacentes.

##<a id="usetez"></a>Utilisez Apache Tez pour améliorer les performances

[Apache Tez](http://tez.apache.org) est une infrastructure qui permet des applications gourmandes en données, telles que de la ruche, s’exécute beaucoup plus efficacement à l’échelle. Dans la dernière version de HDInsight, ruche prend en charge en cours d’exécution sur Tez. Tez est activée par défaut pour les clusters basés sur Linux de HDInsight.

> [AZURE.NOTE] Tez est actuellement désactivée par défaut pour les clusters de HDInsight de basées sur Windows et il doit être activé. Pour tirer parti de Tez, la valeur suivante doit être définie pour une requête de ruche :
>
> ```set hive.execution.engine=tez;```
>
>Cela peut être soumise sur une base par-requête en la plaçant au début de votre requête. Vous pouvez également définir cette sur par défaut sur un cluster en définissant la valeur de configuration lors de la création du cluster. Vous trouverez plus de détails dans la [Mise en service des Clusters HDInsight](hdinsight-provision-clusters.md).

La [ruche sur les documents de conception Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiennent un nombre de plus d’informations sur la mise en œuvre et le réglage des configurations.

Pour aider au débogage de projets exécuté à l’aide de Tez, HDInsight fournit des interfaces qui vous permettent d’afficher les détails des travaux de Tez web suivant :

* [Utiliser l’interface utilisateur de Tez basée sur Windows la HDInsight](hdinsight-debug-tez-ui.md)

* [Utilisez l’affichage Ambari Tez sur HDInsight de basé sur Linux](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Choisissez comment exécuter la tâche HiveQL

HDInsight pouvez exécuter des tâches de HiveQL à l’aide de diverses méthodes. Utilisez le tableau suivant pour décider quelle méthode vous consiste, puis cliquez sur le lien pour une procédure pas à pas.

| **Utilisez cette option** si vous souhaitez...                                                     | .. .an les shell **interactif** | ... traitement **par lots** | .. .avec ce **système d’exploitation en cluster** | ... contre ce **système d’exploitation client** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Affichage de la ruche](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Tout (navigateur) |
| [Commande de beeline (à partir d’une session SSH)](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [La ruche de commande (à partir d’une session SSH)](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Curl](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux ou Windows                          | Linux, Unix, Mac OS X ou Windows        |
| [Console de requête](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | Tout (navigateur)                            |
| [Outils d’HDInsight pour Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Exécution des travaux de la ruche sur Azure HDInsight à l’aide du serveur local SQL Server Integration Services

Vous pouvez également utiliser SQL Server Integration Services (SSIS) pour exécuter une tâche de la ruche. Le Pack de fonctionnalité Azure pour SSIS fournit les composants suivants qui fonctionnent avec les travaux de la ruche sur HDInsight.


- [Tâche d’Azure ruche de HDInsight][hivetask]
- [Gestionnaire de connexion d’abonnement Azure][connectionmanager]


En savoir plus sur le Pack de fonctionnalités Azure pour SSIS [ici][ssispack].


##<a id="nextsteps"></a>Étapes suivantes

Maintenant que vous avez appris ce qu’est la ruche et comment l’utiliser avec Hadoop dans HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec HDInsight d’Azure.


- [Téléchargement des données vers HDInsight][hdinsight-upload-data]
- [Utilisez des porcs avec HDInsight][hdinsight-use-pig]
- [Utilisez Sqoop avec HDInsight](hdinsight-use-sqoop.md)
- [Utiliser Oozie avec HDInsight](hdinsight-use-oozie.md)
- [Utiliser des travaux de MapReduce avec HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
