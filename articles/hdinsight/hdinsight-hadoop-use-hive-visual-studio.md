<properties
   pageTitle="Requête avec des outils d’Hadoop la ruche pour Visual Studio | Microsoft Azure"
   description="Apprenez à utiliser la ruche avec Hadoop dans HDInsight à l’aide des outils de Visual Studio Hadoop."
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Exécuter des requêtes de ruche à l’aide des outils HDInsight pour Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous apprendrez à soumettre des requêtes de ruche à un cluster d’HDInsight à l’aide des outils HDInsight pour Visual Studio.

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL utilisées dans les exemples. Pour plus d’informations sur le HiveQL qui est utilisée dans cet exemple, consultez [Utilisation de la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants.

* Un cluster Azure HDInsight (Hadoop sur HDInsight) (Linux ou Windows)

* Visual Studio (une des versions suivantes) :

    Visual Studio 2013 Communauté/Professionnel/Premium/Édition intégrale avec [mise à jour 4](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 (Communauté/Enterprise)

- Outils d’HDInsight pour Visual studio. Pour plus d’informations sur l’installation et la configuration des outils, consultez [mise en route à l’aide des outils Visual Studio Hadoop pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) .

##<a id="run"></a>Exécuter des requêtes de ruche à l’aide des outils HDInsight pour Visual Studio

1. Ouvrez **Visual Studio** et sélectionnez **Nouveau** > **projet** > **HDInsight** > **Application de la ruche**. Fournissez un nom pour ce projet.

2. Ouvrez le fichier **Script.hql** qui est créé avec ce projet et collez dans les instructions HiveQL suivantes :

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
    * **Sélectionner**: sélectionner un nombre de toutes les lignes où la colonne **t4** contiennent la valeur **[erreur]**. Cela doit retourner une valeur de **3** , car il y a trois lignes contenant cette valeur.
    * **INPUT__FILE__NAME comme '%.log'** - indique la ruche que nous devons uniquement renvoyer des données à partir de fichiers se terminant par. journal. Cela limite la recherche au fichier exemple.log qui contient les données et empêche de renvoyer des données à partir de l’autre exemple de fichiers de données qui ne correspondent pas le schéma que nous avons défini.

3. À partir de la barre d’outils, sélectionnez le **Cluster de HDInsight** que vous souhaitez utiliser pour cette requête, puis sélectionnez **Envoyer à WebHCat** pour exécuter les instructions sous la forme d’une tâche de la ruche à l’aide de WebHCat. Vous pouvez également envoyer la tâche à l’aide du bouton __exécuter via HiveServer2__ si HiveServer2 est disponible dans votre version de cluster. Le **Récapitulatif de la ruche** apparaît et affiche des informations sur la tâche en cours d’exécution. Le lien **Actualiser** permet d’actualiser les informations de travail, jusqu'à ce que le **Statut de la tâche** devient **terminé**.

4. Cliquez sur le lien de **Sortie des tâches** pour afficher le résultat de ce travail. Il doit afficher `[ERROR] 3`, qui est la valeur retournée par l’instruction SELECT.

5. Vous pouvez également exécuter des requêtes de ruche sans créer de projet. À l’aide de l' **Explorateur de serveurs**, développez **Azure** > **HDInsight**, avec le bouton droit de votre serveur HDInsight et sélectionnez **écrire une requête de la ruche**.

6. Dans le document **temp.hql** qui s’affiche, ajoutez les instructions suivantes de la HiveQL :

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les actions suivantes :

    * **Créer TABLE IF NOT EXISTS**: crée une table, s’il n’existe pas déjà. Étant donné que le mot clé **externe** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le magasin de données de ruche et est gérée complètement par ruche.

        > [AZURE.NOTE] À la différence des tables **externes** , suppression d’une table interne supprime également les données sous-jacentes.

    * **Stockées des ORC comme**: stocke les données de ligne optimisée (ORC) sous forme de colonnes. Il s’agit d’un format hautement efficace et optimisé pour le stockage des données de la ruche.
    * REMPLACEMENT de **INSERT... Sélectionnez**: sélectionne les lignes à partir de la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

7. Dans la barre d’outils, sélectionnez **Envoyer** pour exécuter la tâche. Le **Statut de la tâche** permet de déterminer que la tâche est terminée.

8. Pour vérifier que le travail effectué et créé une nouvelle table, utilisez **l’Explorateur de serveurs** et développez **Azure** > **HDInsight** > votre cluster HDInsight > **Bases de données de la ruche** > et **par défaut**. Vous devriez voir la table des **journaux d’erreurs** et la table **log4jLogs** .

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, les outils HDInsight pour Visual Studio offrent un moyen simple pour exécuter des requêtes de la ruche sur un cluster HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur la ruche dans HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les outils de HDInsight pour Visual Studio :

* [Mise en route avec les outils de HDInsight pour Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
