<properties
   pageTitle="Permet de travailler avec une ruche sur HDInsight (Hadoop) Beeline | Microsoft Azure"
   description="Apprenez à utiliser le protocole SSH pour se connecter à un cluster Hadoop dans HDInsight et envoyez-le interactivement ruche de requêtes à l’aide de Beeline. Beeline est un utilitaire permettant de travailler avec HiveServer2 sur JDBC."
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
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Utilisez la ruche avec Hadoop dans HDInsight avec Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous apprendrez comment utiliser SSH (Secure Shell) à se connecter à un cluster basé sur Linux de HDInsight et de soumettre interactivement ruche de requêtes à l’aide de l’outil de ligne de commande [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [AZURE.NOTE] Beeline utilise JDBC pour se connecter à la ruche. Pour plus d’informations sur l’utilisation de JDBC avec ruche, voir [se connecter à la ruche sur HDInsight Azure utilisant le pilote JDBC de la ruche](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un Hadoop sur HDInsight cluster fonctionnant sous Linux.

* Un client SSH. Linux, Unix et Mac OS doivent être accompagnée d’un client SSH. Les utilisateurs Windows doivent télécharger un client, tel que [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Se connecter avec SSH

Se connecter au nom de domaine pleinement qualifié (FQDN) de votre cluster de HDInsight à l’aide de la commande SSH. Le nom de domaine complet est le nom que vous avez donné le cluster, puis **. azurehdinsight.net**. Par exemple, les éléments suivants seraient se connecter à un cluster nommé **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lors de la création du cluster HDInsight, vous devrez spécifier l’emplacement de la clé privée sur votre système client :

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l’authentification SSH** lors de la création du cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (clients basés sur Windows)

Windows ne fournit pas un client SSH intégré. Nous vous recommandons d’à l’aide de **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Utilisez la commande Beeline

1. Une fois connecté, utilisez la suivante pour démarrer Beeline :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Cela démarre le client Beeline et se connecter à l’url JDBC. Ici, `localhost` est utilisé dans la mesure où HiveServer2 s’exécute sur les deux nœuds du cluster principal, et nous utilisons Beeline directement sur le headnode principal.
    
    Une fois la commande terminée, vous vous arrivez à un `jdbc:hive2://localhost:10001/>` invite.

3. Beeline de commandes commencent généralement par une `!` de caractère, par exemple `!help` affiche l’aide. Toutefois, le `!` peut souvent être omis. Par exemple, `help` fonctionne également.

    Si vous affichez l’aide, vous remarquerez `!sql`, qui est utilisé pour exécuter des instructions de HiveQL. Cependant, HiveQL est couramment utilisé que vous pouvez omettre le précédent `!sql`. Les deux instructions suivantes ont exactement les mêmes résultats ; afficher les tables actuellement disponibles par le biais de ruche :
    
        !sql show tables;
        show tables;
    
    Un nouveau cluster, une seule table doit être répertoriée : __hivesampletable__.

4. Pour afficher le schéma de la hivesampletable, utilisez ce qui suit :

        describe hivesampletable;
        
    Ceci renverra les informations suivantes :
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Cela affiche les colonnes de la table. Alors que nous pourrions lancer des requêtes sur ces données, nous allons créer à la place une nouvelle table pour montrer comment charger des données dans une ruche et appliquer un schéma.
    
5. Entrez les instructions suivantes pour créer une nouvelle table nommée **log4jLogs** à l’aide d’exemples de données fournis avec le cluster de HDInsight :

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
    * **INPUT__FILE__NAME comme '%.log'** - indique la ruche que nous devons uniquement renvoyer des données à partir de fichiers se terminant par. journal. Normalement, vous n’auront que données possédant le même schéma dans le même dossier lors de l’interrogation de la ruche, mais cet exemple de fichier journal est stocké avec d’autres formats de données.

    > [AZURE.NOTE] Tables externes doivent être utilisées lorsque vous pensez que les données sous-jacentes afin d’être mis à jour par une source externe, tel qu’un processus de téléchargement automatique des données, ou par une autre opération MapReduce, mais souhaitez ruche les requêtes à utiliser les données les plus récentes.
    >
    > Suppression d’une table externe est **pas** supprimer les données, seule la définition de la table.
    
    La sortie de cette commande doit être semblable au suivant :
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Pour quitter Beeline, utilisez `!quit`.

##<a id="file"></a>Exécuter un fichier de HiveQL

Beeline peut également servir à exécuter un fichier qui contient les instructions de HiveQL. Utilisez les étapes suivantes pour créer un fichier, puis les exécuter à l’aide de Beeline.

1. Utilisez la commande suivante pour créer un nouveau fichier nommé __query.hql__:

        nano query.hql
        
2. Une fois l’éditeur s’ouvre, utilisez ce qui suit comme le contenu du fichier. Cette requête crée une nouvelle table « interne » nommée **journaux d’erreurs de**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les actions suivantes :

    * **Créer TABLE IF NOT EXISTS** - crée une table, il n’existe pas déjà. Dans la mesure où le mot clé **externe** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le magasin de données de ruche et est gérée complètement par ruche.
    * **Stockées des ORC comme** - stocke les données au format d’optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement efficace et optimisé pour le stockage des données de la ruche.
    * REMPLACEMENT de **INSERT... Sélectionnez** - sélectionne les lignes dans la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .
    
    > [AZURE.NOTE] À la différence des tables externes, la suppression d’une table interne supprimera les données sous-jacent ainsi.
    
3. Pour enregistrer le fichier, utilisez __Ctrl__+ ___X__, puis entrez __Y__et enfin sur __entrée__.

4. Utilisez ce qui suit pour exécuter le fichier à l’aide de Beeline. Remplacez le __nom d’hôte__ avec le nom obtenu précédemment pour le nœud de tête et le __mot de passe__ avec le mot de passe pour le compte d’administrateur :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] Le `-i` paramètre commence Beeline, exécute les instructions dans le fichier query.hql et demeure dans les Beeline à le `jdbc:hive2://localhost:10001/>` invite. Vous pouvez également exécuter un fichier à l’aide de la `-f` paramètre, ce qui vous permet de revenir à Bash une fois que le fichier a été traité.

5. Pour vérifier que la **journaux d’erreurs de** table a été créée, utilisez l’instruction suivante pour renvoyer toutes les lignes de **journaux d’erreurs**:

        SELECT * from errorLogs;

    Trois lignes de données doit être retourné, contenant tous les **[erreur]** dans la colonne t4 :
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>En savoir plus sur la connectivité des Beeline

Les étapes décrites dans ce document utilisation `localhost` pour se connecter à HiveServer2 en cours d’exécution sur le headnode de cluster. Vous pouvez également utiliser le nom d’hôte ou le nom de domaine complet de l’headnode que ceux requièrent des étapes supplémentaires pour le processus (étapes pour rechercher le nom d’hôte ou le nom de domaine complet). À l’aide de `localhost` est suffisante lors de l’utilisation de Beeline de la headnode.

Si vous avez un nœud dans votre cluster, avec Beeline installé, vous devez utiliser le nom d’hôte ou le nom de domaine complet de l’headnode pour vous connecter.

Si vous avez Beeline installé sur un client à l’extérieur de votre cluster, vous pouvez vous connecter à l’aide de la commande suivante. Remplacez __NOM_CLUSTER__ par le nom de votre cluster HDInsight. Remplacez le __mot de passe__ avec le mot de passe pour le compte d’administrateur (connexion HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Notez que le paramètres ou l’URI est différente de l’exécuter directement sur un headnode ou à partir d’un nœud dans le cluster. C’est parce que la connexion au cluster à partir d’internet utilise une passerelle publique qui achemine le trafic sur le port 443. En outre, plusieurs autres services sont exposés via la passerelle publique sur le port 443, l’URI est différent lors de la connexion directe. Lorsque vous vous connectez à partir d’internet, que vous devez également authentifier la session en fournissant le mot de passe.

##<a id="summary"></a><a id="nextsteps"></a>Étapes suivantes

Comme vous pouvez le voir, la commande Beeline fournit un moyen facile d’exécuter interactivement des requêtes de ruche sur un cluster HDInsight.

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

