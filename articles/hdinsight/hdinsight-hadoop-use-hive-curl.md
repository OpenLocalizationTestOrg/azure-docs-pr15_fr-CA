<properties
   pageTitle="Utilisez la ruche d’Hadoop bouclé dans HDInsight | Microsoft Azure"
   description="Apprenez à envoyer à distance des tâches de porc à HDInsight à l’aide de roulage."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-with-hadoop-in-hdinsight-with-curl"></a>Exécuter des requêtes de la ruche avec Hadoop dans HDInsight bouclé

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans ce document, vous apprendrez comment utiliser Curl pour exécuter des requêtes de la ruche sur un Hadoop sur Azure HDInsight cluster.

Curl est utilisé pour montrer comment vous pouvez interagir avec les HDInsight à l’aide de demandes HTTP brutes pour exécuter, surveiller et récupérer les résultats de requêtes de la ruche. Cela fonctionne à l’aide de l’API de reste WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs d’Hadoop de basé sur Linux, mais sont nouvelles dans HDInsight, voir [ce que vous devez savoir autour d’Hadoop sur HDInsight de fonctionnant sous Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un Hadoop sur cluster de HDInsight (Linux ou Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécuter des requêtes de la ruche à l’aide de roulage

> [AZURE.NOTE] Lors de l’utilisation de roulage ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en fournissant le nom d’utilisateur et le mot de passe de l’administrateur de cluster HDInsight. Vous devez également utiliser le nom du cluster dans le cadre de la ressource identificateur URI (Uniform) utilisée pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez le **nom d’utilisateur** de l’utilisateur pour s’authentifier sur le cluster et remplacer le **mot de passe** avec le mot de passe pour le compte d’utilisateur. Remplacez **NOM_CLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé via [l’authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous les demandes à l’aide de HTTPS (HTTP sécurisé) pour vous assurer que vos informations d’identification sont transmises de manière sécurisée sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous devriez recevoir une réponse semblable à la suivante :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u** - le nom d’utilisateur et le mot de passe utilisé pour authentifier la demande.
    * **-G** - indique qu’il s’agit d’une demande GET.

    Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la demande est de retourner un état de WebHCat (également connu sous le nom Templeton) pour le serveur. Vous pouvez également demander la version de la ruche à l’aide de la commande suivante :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Ceci doit renvoyer une réponse semblable à la suivante :

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Pour créer une nouvelle table nommée **log4jLogs**, utilisez ce qui suit :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d** - depuis `-G` n’est pas utilisé, la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom** - l’utilisateur qui exécute la commande.

        * **exécution** - le HiveQL des instructions à exécuter.

        * **statusdir** - le statut de cette tâche est écrits dans le répertoire.

    Ces instructions effectuent les actions suivantes :

    * **DROP TABLE** - supprime la table et le fichier de données, si la table existe déjà.

    * **Créer une TABLE externe** - crée une nouvelle table « externe » dans la ruche. Tables externes stockent uniquement la définition de table dans la ruche. Les données restent dans leur emplacement d’origine.

        > [AZURE.NOTE] Tables externes doivent être utilisées lorsque vous pensez que les données sous-jacentes afin d’être mis à jour par une source externe, tel qu’un processus de téléchargement automatique des données, ou par une autre opération MapReduce, mais souhaitez ruche les requêtes à utiliser les données les plus récentes.
        >
        > Suppression d’une table externe est **pas** supprimer les données, seule la définition de la table.

    * **FORMAT de ligne** indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparés par un espace.

    * **Emplacement de fichier texte en tant que stockage** - indique la ruche où les données sont stockées (le répertoire de données d’exemple) et qu’il est stocké en tant que texte.

    * **Sélectionnez** - sélectionne un décompte de toutes les lignes où la colonne **t4** contient la valeur **[erreur]**. Cela doit retourner une valeur de **3** qu’il sont a trois lignes contenant cette valeur.

    > [AZURE.NOTE] Notez que les espaces entre les instructions HiveQL sont remplacés par le `+` de caractère utilisé avec Curl. Les valeurs entre guillemets qui contient un espace, par exemple le délimiteur, ne devraient pas être remplacés par `+`.

    * **INPUT__FILE__NAME comme '% 25.log'** - cette option limite la recherche aux seuls utilisent les fichiers se terminant par. journal. Si ce n’est pas présent, ruche va tenter de rechercher tous les fichiers dans ce répertoire et ses sous-répertoires, y compris les fichiers qui ne correspondent pas au schéma de colonne défini pour cette table.

    > [AZURE.NOTE] Remarque 25 % est l’URL codée formulaire %, donc la condition réelle est `like '%.log'`. Le % doit être l’URL codée, comme il est traité comme un caractère spécial dans les URL.

    Cette commande doit renvoyer un ID de tâche qui peut être utilisé pour vérifier l’état de la tâche.

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Avec la valeur retournée à l’étape précédente, remplacez **JOBID** . Par exemple, si la valeur de retour a été `{"id":"job_1415651640909_0026"}`, **JOBID** est `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si la tâche est terminée, l’état sera **terminé**.

    > [AZURE.NOTE] Cette demande de roulage renvoie un document JavaScript Objet Notation (JSON) des informations sur la tâche ; jq est utilisé pour récupérer la valeur d’état.

4. Une fois que l’état de la tâche a changé à **a réussi**, vous pouvez récupérer les résultats de la tâche de stockage des objets Blob Azure. Le `statusdir` paramètre passé avec la requête contient l’emplacement du fichier de sortie ; Dans ce cas, **wasbs : / / exemple/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/ENROULE** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

    Vous pouvez afficher et télécharger ces fichiers à l’aide de la [CLI d’Azure](../xplat-cli-install.md). Par exemple, pour répertorier les fichiers d' **exemple/ourlée**, utilisez la commande suivante :

        azure storage blob list <container-name> example/curl

    Pour télécharger un fichier, utilisez la commande suivante :

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] Vous devez spécifier le nom du compte de stockage qui contient l’objet blob à l’aide de la `-a` et `-k` paramètres, ou un ensemble la **AZURE\_stockage\_compte** et **AZURE\_stockage\_accès\_clé** variables d’environnement. Reportez-vous à la section < un href = « hdinsight-téléchargement-data.md » target = « _blank » pour plus d’informations.

6. Pour créer une nouvelle table « interne » nommée **journaux d’erreurs**, utilisez les instructions suivantes :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Ces instructions effectuent les actions suivantes :

    * **Créer TABLE IF NOT EXISTS** - crée une table, il n’existe pas déjà. Dans la mesure où le mot clé **externe** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le magasin de données de ruche et est gérée complètement par ruche.

        > [AZURE.NOTE] À la différence des tables externes, la suppression d’une table interne supprimera les données sous-jacent ainsi.

    * **Stockées des ORC comme** - stocke les données au format d’optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement efficace et optimisé pour le stockage des données de la ruche.
    * REMPLACEMENT de **INSERT... Sélectionnez** - sélectionne les lignes dans la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .
    * **Sélectionnez** - de sélectionne toutes les lignes de la nouvelle table de **journaux d’erreurs** .

7. Utilisez l’ID de travail retourné pour vérifier l’état de la tâche. Une fois qu’il a réussi, utilisez Azure CLI pour Mac, Linux et Windows comme décrit précédemment pour télécharger et afficher les résultats. La sortie doit contenir trois lignes, qui contiennent toutes des **[erreur]**.


##<a id="summary"></a>Résumé

Comme cela est indiqué dans ce document, vous pouvez utiliser une requête HTTP brute pour exécuter, de surveiller et d’afficher les résultats des travaux de la ruche sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisé dans cet article, reportez-vous à la <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Référence de le WebHCat</a>.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur la ruche avec HDInsight :

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




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


