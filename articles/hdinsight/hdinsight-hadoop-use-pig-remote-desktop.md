<properties
   pageTitle="Utiliser Hadoop porc avec Bureau à distance dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser la commande de porc pour exécuter des instructions de porc Latin à partir d’une connexion Bureau à distance à un cluster basé sur Windows de Hadoop dans HDInsight."
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

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Exécuter des travaux de porc d’une connexion Bureau à distance

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit une procédure pas à pas de la commande de porc d’exécuter des instructions de porc Latin à partir d’une connexion Bureau à distance à un cluster d’HDInsight de basées sur Windows. Latin de porc vous permet de créer des applications de MapReduce en décrivant les transformations de données, plutôt que de mapper et de réduire les fonctions.

Dans ce document, vous apprenez comment

##<a id="prereq"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devrez les éléments suivants.

* Un cluster basé sur Windows HDInsight (Hadoop sur HDInsight)

* Un ordinateur client exécutant Windows 7, Windows 8 ou Windows 10

##<a id="connect"></a>Connexion Bureau à distance

Activer le Bureau à distance pour le cluster HDInsight, puis de s’y connecter en suivant les instructions de la section [se connecter à des clusters HDInsight utilisant le protocole RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Utilisez la commande de porc

2. Une fois que vous disposez d’une connexion Bureau à distance, démarrer la **ligne de commande Hadoop** à l’aide de l’icône sur le bureau.

2. Utilisez la suivante pour démarrer la commande de porc :

        %pig_home%\bin\pig

    Vous obtenez un `grunt>` invite.

3. Saisissez l’instruction suivante :

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Cette commande charge le contenu du fichier exemple.log dans les fichiers journaux. Vous pouvez afficher le contenu du fichier à l’aide de la commande suivante :

        DUMP LOGS;

4. Transformer les données en appliquant une expression régulière pour extraire uniquement le niveau d’enregistrement de chaque enregistrement :

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Vous pouvez utiliser **l’image** pour afficher les données après la transformation. Dans ce cas, `DUMP LEVELS;`.

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
    <td>FRÉQUENCES = foreach GROUPEDLEVELS générer groupe comme LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL) comme nombre ;</td><td>Crée un nouveau jeu de données qui contient chaque journal unique de la valeur de niveau et combien de fois il se produit. Ces informations sont stockées dans des fréquences</td>
    </tr>
    <tr>
    <td>RÉSULTAT = ordre de fréquences en nombre, desc ;</td><td>Les niveaux du journal des commandes en fonction du nombre (tri décroissant) et le stocke dans le résultat</td>
    </tr>
    </table>

6. Vous pouvez également enregistrer les résultats d’une transformation à l’aide de la `STORE` instruction. Par exemple, la commande suivante enregistre la `RESULT` dans le répertoire **/example/data/pigout** dans le conteneur de stockage par défaut pour votre cluster :

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] Les données sont stockées dans le répertoire spécifié dans les fichiers nommés **nnnnn de la partie**. Si le répertoire existe déjà, vous recevrez un message d’erreur.

7. Pour quitter l’invite grunt, saisissez l’instruction suivante.

        QUIT;

###<a name="pig-latin-batch-files"></a>Fichiers de commandes de porc Latin

Vous pouvez également utiliser la commande de porc pour exécuter Latin de porcs se trouvant dans un fichier.

3. Après avoir quitté l’invite grunt, ouvrez **le bloc-notes** et créez un nouveau fichier nommé **pigbatch.pig** dans le répertoire **% PIG_HOME** .

4. Tapez ou collez les lignes suivantes dans le fichier **pigbatch.pig** et enregistrez-le :

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utilisez ce qui suit pour exécuter le fichier **pigbatch.pig** à l’aide de la commande de porc.

        pig %PIG_HOME%\pigbatch.pig

    Lorsque le traitement par lots est terminé, vous devriez voir la sortie suivante, qui doit être le même que lorsque vous avez utilisé `DUMP RESULT;` dans les étapes précédentes :

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande de porc permet interactivement exécuter des opérations de MapReduce, ou exécuter les tâches de porc Latin qui sont stockées dans un fichier de commandes.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les porcs dans HDInsight :

* [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
