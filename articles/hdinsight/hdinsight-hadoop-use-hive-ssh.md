<properties
   pageTitle="Utiliser l’interpréteur de commandes de ruche dans HDInsight (Hadoop) | Microsoft Azure"
   description="Apprenez à utiliser l’interpréteur de commandes de ruche avec un cluster basé sur Linux de HDInsight. Vous allez apprendre à vous connecter au cluster HDInsight à l’aide de SSh, puis utiliser l’interpréteur de commandes de la ruche pour exécuter des requêtes de manière interactive."
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>Utilisez la ruche avec Hadoop dans HDInsight avec SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous allez apprendre à utiliser SSH (Secure Shell) pour se connecter à un Hadoop sur Azure HDInsight cluster puis soumettre interactivement ruche de requêtes à l’aide de l’interface de ligne de commande (CLI) ruche.

> [AZURE.IMPORTANT] Alors que la commande de la ruche est disponible sur les clusters basés sur Linux de HDInsight, vous devez envisager d’utiliser Beeline. Beeline est un client plus récent pour l’utilisation de la ruche et est inclus dans votre cluster HDInsight. Pour plus d’informations sur l’utilisation de cette, consultez [Utilisation de la ruche avec Hadoop dans HDInsight avec Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un Hadoop sur HDInsight cluster fonctionnant sous Linux.

* Un client SSH. Linux, Unix et Mac OS doivent être accompagnée d’un client SSH. Les utilisateurs Windows doivent télécharger un client, tel que [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Se connecter avec SSH

Se connecter au nom de domaine pleinement qualifié (FQDN) de votre cluster de HDInsight à l’aide de la commande SSH. Le nom de domaine complet est le nom que vous avez donné le cluster, puis **. azurehdinsight.net**. Par exemple, les éléments suivants seraient se connecter à un cluster nommé **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lors de la création du cluster HDInsight, vous devrez spécifier l’emplacement de la clé privée sur votre système client :

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni un mot de passe pour l’authentification SSH** lors de la création du cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (clients basés sur Windows)

Windows ne fournit pas un client SSH intégré. Nous vous recommandons d’à l’aide de **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Utilisez la commande de la ruche

2. Une fois connecté, démarrer l’interface CLI de la ruche à l’aide de la commande suivante :

        hive

3. À l’aide de l’interface CLI, entrez les instructions suivantes pour créer une nouvelle table nommée **log4jLogs** en utilisant les exemples de données :

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les actions suivantes :

    * **DROP TABLE** - supprime la table et le fichier de données, si la table existe déjà.
    * **Créer une TABLE externe** - crée une nouvelle table « externe » dans la ruche. Tables externes stockent uniquement la définition de table dans la ruche. Les données restent dans leur emplacement d’origine.
    * **FORMAT de ligne** indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparés par un espace.
    * **Emplacement de fichier texte en tant que stockage** - indique la ruche où les données sont stockées (le répertoire de données d’exemple) et qu’il est stocké en tant que texte.
    * **Sélectionnez** - sélectionne un décompte de toutes les lignes où la colonne **t4** contient la valeur **[erreur]**. Cela doit retourner une valeur de **3** qu’il sont a trois lignes contenant cette valeur.
    * **INPUT__FILE__NAME comme '%.log'** - indique la ruche que nous devons uniquement renvoyer des données à partir de fichiers se terminant par. journal. Cela limite la recherche au fichier exemple.log qui contient les données et empêche de renvoyer des données à partir de l’autre exemple de fichiers de données qui ne correspondent pas le schéma que nous avons défini.

    > [AZURE.NOTE] Tables externes doivent être utilisées lorsque vous pensez que les données sous-jacentes afin d’être mis à jour par une source externe, tel qu’un processus de téléchargement automatique des données, ou par une autre opération MapReduce, mais souhaitez ruche les requêtes à utiliser les données les plus récentes.
    >
    > Suppression d’une table externe est **pas** supprimer les données, seule la définition de la table.

4. Pour créer une nouvelle table « interne » nommée **journaux d’erreurs**, utilisez les instructions suivantes :

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les actions suivantes :

    * **Créer TABLE IF NOT EXISTS** - crée une table, il n’existe pas déjà. Dans la mesure où le mot clé **externe** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le magasin de données de ruche et est gérée complètement par ruche.
    * **Stockées des ORC comme** - stocke les données au format d’optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement efficace et optimisé pour le stockage des données de la ruche.
    * REMPLACEMENT de **INSERT... Sélectionnez** - sélectionne les lignes dans la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

    Pour vérifier qu’uniquement les lignes contenant **[erreur]** dans la colonne t4 ont été stockées dans la table des **journaux d’erreurs** , utilisez l’instruction suivante pour renvoyer toutes les lignes de **journaux d’erreurs**:

        SELECT * from errorLogs;

    Trois lignes de données doit être retourné, contenant tous les **[erreur]** dans la colonne t4.

    > [AZURE.NOTE] À la différence des tables externes, la suppression d’une table interne supprimera les données sous-jacent ainsi.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande de ruche fournit un moyen simple pour exécuter des requêtes de la ruche sur un cluster HDInsight, surveiller l’état du travail et récupérer la sortie de façon interactive.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur la ruche dans HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec ruche, consultez les documents suivants pour les informations de débogage :

* [Utiliser l’interface utilisateur de Tez basée sur Windows la HDInsight](hdinsight-debug-tez-ui.md)

* [Utilisez l’affichage Ambari Tez sur HDInsight de basé sur Linux](hdinsight-debug-ambari-tez-view.md)

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

