<properties
   pageTitle="Utilisez MapReduce et courbée avec Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à exécuter à distance des travaux de MapReduce avec Hadoop sur HDInsight à l’aide de roulage."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Exécuter des travaux de MapReduce avec Hadoop sur HDInsight à l’aide de roulage

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans ce document, vous apprendrez comment utiliser Curl pour exécuter des travaux de MapReduce sur un Hadoop sur cluster de HDInsight.

Curl est utilisé pour montrer comment vous pouvez interagir avec les HDInsight à l’aide de demandes HTTP brutes pour exécuter les travaux MapReduce. Cela fonctionne à l’aide de l’API de reste WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs d’Hadoop de basé sur Linux, mais vous ne connaissez pas HDInsight, voir [ce que vous devez savoir sur basé sur Linux d’Hadoop sur HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un Hadoop sur cluster de HDInsight (Linux ou Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécution de tâches MapReduce à l’aide de roulage

> [AZURE.NOTE] Lorsque vous utilisez le roulage ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en fournissant le nom d’utilisateur HDInsight cluster administrateur et le mot de passe. Vous devez également utiliser le nom de cluster en tant que partie de l’URI qui est utilisé pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez le **nom d’utilisateur** de l’utilisateur pour authentifier vers le cluster et le **mot de passe** avec le mot de passe pour le compte d’utilisateur. Remplacez **NOM_CLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé à l’aide de [l’authentification d’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous les demandes à l’aide de HTTPS pour vous assurer que vos informations d’identification sont transmises de manière sécurisée sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous devriez recevoir une réponse semblable à la suivante :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u**: indique le nom d’utilisateur et le mot de passe utilisé pour authentifier la demande
    * **-G**: indique qu’il s’agit d’une demande GET

    Début de l’URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est le même pour toutes les demandes.

2. Pour soumettre un travail MapReduce, utilisez la commande suivante :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    La fin de l’URI (jar/mapreduce) indique WebHCat que cette demande démarre un travail MapReduce à partir d’une classe dans un fichier jar. Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d**: `-G` n’est pas utilisée, afin que la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom**: l’utilisateur qui exécute la commande
        * **JAR**: l’emplacement du fichier jar qui contient la classe pour être exécuté
        * **classe**: la classe contenant la logique MapReduce
        * **arg**: les arguments à passer à la tâche MapReduce ; Dans ce cas, le fichier d’entrée de texte et le répertoire, qui sont utilisés pour la sortie

    Cette commande doit renvoyer un ID de tâche qui peut être utilisé pour vérifier l’état de la tâche :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez le **JOBID** avec la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour a été `{"id":"job_1415651640909_0026"}`, puis le JOBID serait `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si la tâche est terminée, l’état doit être « réussi ».

    > [AZURE.NOTE] Cette demande de roulage renvoie un document JSON avec des informations sur la tâche ; jq est utilisé pour récupérer la valeur d’état.

4. Lorsque l’état de la tâche a été modifiée pour **a réussi**, vous pouvez récupérer les résultats de la tâche de stockage des objets Blob Azure. Le `statusdir` paramètre qui est passé avec la requête contient l’emplacement du fichier de sortie ; Dans ce cas, **wasbs : / / exemple/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/curl** dans le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

Vous pouvez afficher et télécharger ces fichiers à l’aide de la [CLI d’Azure](../xplat-cli-install.md). Par exemple, pour répertorier les fichiers d' **exemple/ourlée**, utilisez la commande suivante :

    azure storage blob list <container-name> example/curl

Pour télécharger un fichier, utilisez la commande suivante :

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Vous devez spécifier le nom de compte de stockage qui contient l’objet blob à l’aide de la `-a` et `-k` paramètres, ou un ensemble la **AZURE\_stockage\_compte** et **AZURE\_stockage\_accès\_clé** variables d’environnement. Consultez [comment charger des données à HDInsight](hdinsight-upload-data.md) pour plus d’informations.

##<a id="summary"></a>Résumé

Comme cela est indiqué dans ce document, vous pouvez utiliser la requête brute HTTP à exécuter, de surveiller et d’afficher les résultats des travaux de la ruche dans votre cluster HDInsight.

Pour plus d’informations sur l’interface REST qui est utilisé dans cet article, reportez-vous à la [Référence de le WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales à propos des tâches MapReduce dans HDInsight :

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)
