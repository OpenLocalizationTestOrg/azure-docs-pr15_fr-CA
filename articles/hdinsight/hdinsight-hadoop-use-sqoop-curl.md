<properties
   pageTitle="Utiliser Hadoop Sqoop bouclé dans HDInsight | Microsoft Azure"
   description="Apprenez à envoyer à distance des tâches de Sqoop à HDInsight à l’aide de roulage."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Exécuter des tâches de Sqoop avec Hadoop dans HDInsight bouclé

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Curl pour exécuter des travaux de Sqoop sur un cluster Hadoop dans HDInsight.

Curl est utilisé pour montrer comment vous pouvez interagir avec les HDInsight à l’aide de demandes HTTP brutes pour exécuter, surveiller et récupérer les résultats des travaux de Sqoop. Cela fonctionne à l’aide de l’API de reste WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs d’Hadoop de basé sur Linux, mais sont nouvelles dans HDInsight, reportez-vous à la section [informations sur l’utilisation d’HDInsight sous Linux](hdinsight-hadoop-linux-information.md).

##<a name="prerequisites"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un Hadoop sur cluster de HDInsight (Linux ou Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>Soumettre des travaux de Sqoop à l’aide de roulage

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

    Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la demande est de retourner un état de WebHCat (également connu sous le nom Templeton) pour le serveur. 

2. Pour soumettre un travail sqoop, utilisez ce qui suit :


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d** - depuis `-G` n’est pas utilisé, la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom** - l’utilisateur qui exécute la commande.

        * **commande** - Sqoop la commande s’exécute.

        * **statusdir** - le statut de cette tâche est écrits dans le répertoire.

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

##<a name="limitations"></a>Limitations

* Exportation en bloc - des HDInsight de base avec Linux, le connecteur de Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données de SQL Azure ne prend pas en charge insertions en bloc.

* Le traitement par lots - avec HDInsight basé sur Linux, lorsque vous utilisez la `-batch` passer lors de l’exécution des insertions, Sqoop effectue plusieurs insertions plutôt que le traitement par lot des opérations d’insertion.

##<a name="summary"></a>Résumé

Comme cela est indiqué dans ce document, vous pouvez utiliser une requête HTTP brute pour exécuter, de surveiller et d’afficher les résultats des travaux de Sqoop sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisé dans cet article, consultez le <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guide des API de reste de Sqoop</a>.

##<a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations générales sur la ruche avec HDInsight :

* [Utilisez Sqoop avec Hadoop sur HDInsight](hdinsight-use-sqoop.md)

Pour plus d’informations sur d’autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

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


