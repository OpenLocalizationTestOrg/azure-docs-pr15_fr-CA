<properties
   pageTitle="Utiliser Hadoop porc bouclé dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser Curl pour exécuter des travaux de porc Latin sur un cluster Hadoop dans Azure HDInsight."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Exécuter des travaux de porc avec Hadoop sur HDInsight à l’aide de roulage

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dans ce document, vous apprendrez comment utiliser Curl pour exécuter des travaux de porc Latin sur un cluster HDInsight d’Azure. Le langage de programmation de porc Latin vous permet de décrire les transformations qui s’appliquent aux données d’entrée pour produire la sortie de votre choix.

Curl est utilisé pour montrer comment vous pouvez interagir avec les HDInsight à l’aide de demandes HTTP brutes pour exécuter, surveiller et récupérer les résultats des travaux de porc. Cela fonctionne à l’aide de l’API, reste WebHCat (anciennement Templeton) qui est fourni par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs d’Hadoop de basé sur Linux, mais sont nouvelles dans HDInsight, consultez [Conseils de HDInsight fonctionnant sous Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight) (Linux ou Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécuter des travaux de porc à l’aide de roulage

> [AZURE.NOTE] Lors de l’utilisation de roulage ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en fournissant le nom d’utilisateur administrateur et le mot de passe pour l’HDInsight du cluster. Vous devez également utiliser le nom du cluster dans le cadre de la ressource identificateur URI (Uniform) qui est utilisé pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez le **nom d’utilisateur** de l’utilisateur pour s’authentifier sur le cluster et remplacer le **mot de passe** avec le mot de passe pour le compte d’utilisateur. Remplacez **NOM_CLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé via [l’authentification de l’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous les demandes à l’aide de HTTPS (HTTP sécurisé) pour vous assurer que vos informations d’identification sont transmises de manière sécurisée sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous devriez recevoir une réponse semblable à la suivante :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u**: le nom d’utilisateur et le mot de passe utilisé pour authentifier la demande
    * **-G**: indique qu’il s’agit d’une demande GET

    Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la requête est pour renvoyer l’état de WebHCat (également connu sous le nom Templeton) pour le serveur.

2. Pour soumettre un travail Latin de porc sur le cluster, utilisez le code suivant :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d**: car `-G` n’est pas utilisé, la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom**: l’utilisateur qui exécute la commande
        * **exécuter**: le porc Latin des instructions à exécuter
        * **statusdir**: le statut de cette tâche est écrits dans le répertoire

    > [AZURE.NOTE] Notez que les espaces dans les instructions de porc Latin sont remplacés par le `+` de caractère utilisé avec Curl.

    Cette commande doit renvoyer un code de travail qui peut être utilisé pour vérifier le statut de la tâche, par exemple :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Avec la valeur retournée à l’étape précédente, remplacez **JOBID** . Par exemple, si la valeur de retour a été `{"id":"job_1415651640909_0026"}`, **JOBID** est `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si la tâche est terminée, l’état sera **terminé**.

    > [AZURE.NOTE] Cette demande de roulage renvoie un document JavaScript Objet Notation (JSON) des informations sur la tâche, et jq est utilisé pour récupérer la valeur d’état.

##<a id="results"></a>Afficher les résultats

Lorsque l’état de la tâche a été modifiée pour **a réussi**, vous pouvez récupérer les résultats de la tâche de stockage des objets Blob Azure. Le `statusdir` paramètre passé avec la requête contient l’emplacement du fichier de sortie ; Dans ce cas, **wasbs : / / exemple/pigcurl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/pigcurl** dans le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

Vous pouvez afficher et télécharger ces fichiers à l’aide de la [CLI d’Azure](../xplat-cli-install.md). Par exemple, pour répertorier les fichiers d' **exemple/pigcurl**, utilisez la commande suivante :

    azure storage blob list <container-name> example/pigcurl

Pour télécharger un fichier, utilisez la commande suivante :

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Vous devez spécifier le nom de compte de stockage qui contient l’objet blob à l’aide de la `-a` et `-k` paramètres, ou un ensemble la **AZURE\_stockage\_compte** et **AZURE\_stockage\_accès\_clé** variables d’environnement.

##<a id="summary"></a>Résumé

Comme cela est indiqué dans ce document, vous pouvez utiliser une requête HTTP brute à exécuter, de surveiller et d’afficher les résultats des travaux de porc sur votre cluster de HDInsight.

Pour plus d’informations sur l’interface REST utilisé dans cet article, reportez-vous à la [Référence de le WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les porcs sur HDInsight :

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
