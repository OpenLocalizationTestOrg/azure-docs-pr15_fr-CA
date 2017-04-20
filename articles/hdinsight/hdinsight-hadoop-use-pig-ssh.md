<properties
   pageTitle="Utiliser Hadoop porc avec SSH sur un cluster HDInsight | Microsoft Azure"
   description="Découvrez comment connecter à un cluster basé sur Linux de Hadoop avec SSH et puis utiliser la commande porc à exécuter de façon interactive les instructions Latin de porc, ou comme un traitement par lots."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Exécution des tâches de porc sur un cluster Linux avec la commande du porc (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dans ce document vous guidera dans le processus de connexion à un cluster d’HDInsight de Azure basé sur Linux à l’aide de SSH (Secure Shell), puis en utilisant la commande de porc pour exécuter des instructions de porc Latin d’interactivement, ou comme un traitement par lots.

Le langage de programmation de porc Latin vous permet de décrire les transformations qui s’appliquent aux données d’entrée pour produire la sortie de votre choix.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs d’Hadoop de basé sur Linux, mais sont nouvelles dans HDInsight, consultez [Conseils de HDInsight fonctionnant sous Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants.

* Un cluster basé sur Linux HDInsight (Hadoop sur HDInsight).

* Un client SSH. Linux, Unix et Mac OS doivent être accompagnée d’un client SSH. Les utilisateurs Windows doivent télécharger un client, tel que [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Se connecter avec SSH

Se connecter au nom de domaine pleinement qualifié (FQDN) de votre cluster de HDInsight à l’aide de la commande SSH. Le nom de domaine complet est le nom que vous avez donné le cluster, puis **. azurehdinsight.net**. Par exemple, les éléments suivants seraient se connecter à un cluster nommé **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lors de la création du cluster HDInsight, vous devrez peut-être indiquer l’emplacement de la clé privée sur votre système client.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l’authentification SSH** lors de la création du cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (clients basés sur Windows)

Windows ne fournit pas un client SSH intégré. Nous vous recommandons d’à l’aide de **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="pig"></a>Utilisez la commande de porc

2. Une fois connecté, démarrer l’interface de ligne de commande (CLI) porc à l’aide de la commande suivante.

        pig

    Après quelques instants, vous devriez voir un `grunt>` invite.

3. Saisissez l’instruction suivante.

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Cette commande charge le contenu du fichier exemple.log dans les journaux. Vous pouvez afficher le contenu du fichier à l’aide de la commande suivante.

        DUMP LOGS;

4. Ensuite, transformer les données en appliquant une expression régulière pour extraire uniquement le niveau d’enregistrement de chaque enregistrement à l’aide de la commande suivante.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Vous pouvez utiliser **l’image** pour afficher les données après la transformation. Dans ce cas, utilisez `DUMP LEVELS;`.

5. Continuer d’appliquer des transformations à l’aide des instructions suivantes. Utilisez `DUMP` pour afficher le résultat de la transformation après chaque étape.

    <table>
    <tr>
    <th>Instruction</th><th>Ce qu’il fait</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = niveaux de filtrage par LOGLEVEL n’est pas null ;</td><td>Supprime les lignes qui contiennent une valeur null pour le niveau de journalisation et stocke le résultat dans FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS groupe par LOGLEVEL ;</td><td>Regroupe les lignes par niveau de journal et stocke les résultats dans GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FRÉQUENCES = foreach GROUPEDLEVELS générer groupe comme LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL) comme nombre ;</td><td>Crée un nouveau jeu de données qui contient chaque journal unique de la valeur de niveau et combien de fois il se produit. Ces informations sont stockées dans des fréquences.</td>
    </tr>
    <tr>
    <td>RÉSULTAT = ordre de fréquences en nombre, desc ;</td><td>Les niveaux du journal des commandes en fonction du nombre (tri décroissant) et le stocke dans le résultat.</td>
    </tr>
    </table>

6. Vous pouvez également enregistrer les résultats d’une transformation à l’aide de la `STORE` instruction. Par exemple, de la suivante enregistre la `RESULT` dans le répertoire **/example/data/pigout** sur le conteneur de stockage par défaut pour votre cluster.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] Les données sont stockées dans le répertoire spécifié dans les fichiers nommés **nnnnn de la partie**. Si le répertoire existe déjà, vous recevrez une erreur.

7. Pour quitter l’invite grunt, saisissez l’instruction suivante.

        QUIT;

###<a name="pig-latin-batch-files"></a>Fichiers de commandes de porc Latin

Vous pouvez également utiliser la commande de porc pour exécuter Latin de porc contenues dans un fichier.

3. Après avoir quitté l’invite grunt, utilisez la commande suivante pour canal STDIN dans un fichier nommé **pigbatch.pig**. Ce fichier sera créé dans le répertoire de base pour le compte que vous avez ouvert une session pour la session SSH.

        cat > ~/pigbatch.pig

4. Tapez ou collez les lignes suivantes et appuyez sur Ctrl + D, lorsque vous avez terminé.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utilisez ce qui suit pour exécuter le fichier **pigbatch.pig** à l’aide de la commande de porc.

        pig ~/pigbatch.pig

    Une fois le traitement par lots est terminé, vous devriez voir la sortie suivante, qui doit être le même que lorsque vous avez utilisé `DUMP RESULT;` dans les étapes précédentes.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande porc permet à interactivement exécuter MapReduce opérations à l’aide de porc Latin, ainsi qu’exécuter des instructions stockées dans un fichier de commandes.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les porcs dans HDInsight.

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight.

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
