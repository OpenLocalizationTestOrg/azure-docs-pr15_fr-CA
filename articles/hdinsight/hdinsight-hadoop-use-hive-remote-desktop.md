<properties
   pageTitle="Utilisez la ruche d’Hadoop et Bureau à distance dans HDInsight | Microsoft Azure"
   description="Apprendre à vous connecter au cluster Hadoop dans HDInsight à l’aide de bureau à distance et exécutez ruche de requêtes à l’aide de l’Interface de ligne de commande de la ruche."
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Utilisez la ruche avec Hadoop sur HDInsight avec Bureau à distance

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous apprendre à vous connecter à un cluster de HDInsight à l’aide de bureau à distance et puis exécutez les requêtes de la ruche à l’aide de l’Interface de ligne de commande de la ruche (CLI).

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL sont utilisées dans les exemples. Pour plus d’informations sur le HiveQL qui est utilisée dans cet exemple, consultez [Utilisation de la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un cluster basé sur Windows HDInsight (Hadoop sur HDInsight)

* Un ordinateur client exécutant Windows 10, 8 de la fenêtre ou Windows 7

##<a id="connect"></a>Connexion Bureau à distance

Activer le Bureau à distance pour le cluster HDInsight, puis de s’y connecter en suivant les instructions de la section [se connecter à des clusters HDInsight utilisant le protocole RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hive"></a>Utilisez la commande de la ruche

Lorsque vous êtes connecté sur le bureau pour le cluster HDInsight, procédez comme suit pour fonctionner avec la ruche :

1. À partir du bureau de HDInsight, démarrer la **ligne de commande Hadoop**.

2. Entrez la commande suivante pour démarrer l’interface CLI de la ruche :

        %hive_home%\bin\hive

    Au démarrage de l’interface CLI, vous verrez l’invite CLI de la ruche : `hive>`.

3. À l’aide de l’interface CLI, entrez les instructions suivantes pour créer une nouvelle table nommée **log4jLogs** à l’aide d’exemples de données :

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les actions suivantes :

    * **DROP TABLE**: supprime le fichier de données et de la table si la table existe déjà.

    * **Créer une TABLE externe**: crée une nouvelle table « externe » dans la ruche. Tables externes stockent uniquement la définition de table dans la ruche (les données restent dans leur emplacement d’origine).

        > [AZURE.NOTE] Tables externes doivent être utilisées lorsque vous pensez que les données sous-jacentes afin d’être mis à jour par une source externe (par exemple, un processus de téléchargement automatique des données) ou par une autre opération MapReduce, mais vous souhaitez que les requêtes de ruche à utiliser les données les plus récentes.
        >
        > Suppression d’une table externe est **pas** supprimer les données, seule la définition de la table.

    * **FORMAT de ligne**: indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparés par un espace.

    * **Emplacement de fichier texte en tant que stocké**: indique la ruche où les données sont stockées (le répertoire de données d’exemple) et qu’il est stocké en tant que texte.

    * **Sélectionner**: sélectionne un décompte de toutes les lignes où la colonne **t4** contient la valeur **[erreur]**. Cela doit retourner une valeur de **3** , car il y a trois lignes contenant cette valeur.

    * **INPUT__FILE__NAME comme '%.log'** - indique la ruche que nous devons uniquement renvoyer des données à partir de fichiers se terminant par. journal. Cela limite la recherche au fichier exemple.log qui contient les données et empêche de renvoyer des données à partir de l’autre exemple de fichiers de données qui ne correspondent pas le schéma que nous avons défini.


4. Pour créer une nouvelle table « interne » nommée **journaux d’erreurs**, utilisez les instructions suivantes :

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les actions suivantes :

    * **Créer TABLE IF NOT EXISTS**: crée une table, s’il n’existe pas déjà. Étant donné que le mot clé **externe** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le magasin de données de ruche et est gérée complètement par ruche.

        > [AZURE.NOTE] À la différence des tables **externes** , suppression d’une table interne supprime également les données sous-jacentes.

    * **Stockées des ORC comme**: stocke les données de ligne optimisée (ORC) sous forme de colonnes. Il s’agit d’un format hautement efficace et optimisé pour le stockage des données de la ruche.

    * REMPLACEMENT de **INSERT... Sélectionnez**: sélectionne les lignes à partir de la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

    Pour vérifier qu’uniquement les lignes qui contiennent des **[erreur]** dans la colonne t4 ont été stockées dans la table des **journaux d’erreurs** , utilisez l’instruction suivante pour renvoyer toutes les lignes de **journaux d’erreurs**:

        SELECT * from errorLogs;

    Trois lignes de données doit être retourné, contenant tous les **[erreur]** dans la colonne t4.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande de la ruche est un moyen simple pour exécuter des requêtes de la ruche sur un cluster HDInsight, surveiller l’état du travail et récupérer la sortie de façon interactive.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur la ruche dans HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec ruche, consultez les documents suivants pour les informations de débogage :

* [Utiliser l’interface utilisateur de Tez basée sur Windows la HDInsight](hdinsight-debug-tez-ui.md)

* [Utilisez l’affichage Ambari Tez sur HDInsight de basé sur Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

