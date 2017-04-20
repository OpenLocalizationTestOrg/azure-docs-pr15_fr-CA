<properties
    pageTitle="Mémoire insuffisante (OOM) - paramètres de la ruche | Microsoft Azure"
    description="Corriger une erreur de mémoire insuffisante (OOM) à partir d’une requête de la ruche dans Hadoop dans HDInsight. Le scénario de client est une requête sur plusieurs tables de grande taille."
    keywords="paramètres de mémoire insuffisante, erreur ruche hors"
    services="hdinsight"
    documentationCenter=""
    authors="rashimg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="rashimg;jgao"/>

# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Corriger une erreur de mémoire insuffisante avec les paramètres de mémoire de ruche de Hadoop dans Azure HDInsight

Un des problèmes courants nos clients sont confrontés consiste à obtenir une erreur de mémoire insuffisante lors de l’utilisation de la ruche. Cet article décrit un scénario de client et les paramètres de la ruche que nous vous recommandons de résoudre le problème.

## <a name="scenario-hive-query-across-large-tables"></a>Scénario : Ruche de requête sur des tables volumineuses

Un client a exécuté la requête ci-dessous à l’aide de la ruche.

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Certaines nuances de cette requête :

* T1 est un alias pour une grande table, TABLE1, ce qui a de nombreux types de colonne de chaîne.
* Autres tables pas ce qui sont important, mais disposent d’un grand nombre de colonnes.
* Toutes les tables, rejoignez dans certains cas avec plusieurs colonnes dans TABLE1 et à d’autres personnes.

Lorsque le client a exécuté la requête à l’aide de ruche sur MapReduce sur un nœud de cluster de A3 24, la requête est exécuté dans environ 26 minutes. Le client remarqué des messages d’avertissement suivants lorsque la requête a été exécutée à l’aide de la ruche sur MapReduce :

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Étant donné que la requête terminée dans environ 26 minutes, le client ignoré ces avertissements et démarré à la place de se concentrer sur l’amélioration de l’encore davantage les performances de la requête.

Le client consulté [la ruche optimiser des requêtes pour Hadoop dans HDInsight](hdinsight-hadoop-optimize-hive-query.md)et a décidé d’utiliser le moteur d’exécution Tez. Une fois que la même requête a été exécutée avec le paramètre Tez activé la requête s’est exécutée pendant 15 minutes et puis a retourné l’erreur suivante :

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Le client puis décidé d’utiliser un plus grand VM (c'est-à-dire D12) envisage une machine virtuelle plus importante aurait plus d’espace de segment de mémoire. Même alors, le client suite à l’erreur. Le client atteint à l’équipe de HDInsight pour vous aider à déboguer ce problème.

## <a name="debug-the-out-of-memory-oom-error"></a>Déboguer l’erreur de mémoire insuffisante

Notre prise en charge et les équipes d’ingénierie ensemble trouvés un des problèmes à l’origine de l’erreur de mémoire insuffisante a été un [problème décrit dans la JIRA Apache](https://issues.apache.org/jira/browse/HIVE-8306). À partir de la description dans la JIRA :

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Nous avons confirmé que **hive.auto.convert.join.noconditionaltask** a été en effet la valeur **true** , regardez sous fichier de ruche-site.xml :

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
    </property>

Selon l’avertissement et la JIRA, selon notre hypothèse était carte joindre la cause de l’erreur Java tas espace insuffisante. Afin que nous avons cherché plus profonds dans ce problème.

Comme expliqué dans le billet de blog [Hadoop fils les paramètres de mémoire dans HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), lorsque Tez moteur d’exécution est utilisé l’espace de tas utilisé réellement auquel appartient le conteneur Tez. Voir l’image ci-dessous décrivant la mémoire de conteneur Tez.

![Diagramme de mémoire tez conteneur : erreur de mémoire insuffisante de la ruche](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Comme l’indique le billet de blog, les paramètres de mémoire de deux suivants définissent la conteneur de mémoire pour le segment de mémoire : **hive.tez.container.size** et **hive.tez.java.opts**. À partir de notre expérience, l’exception OOM ne signifie pas que la taille du conteneur est trop petite. Cela signifie que la taille du tas Java (hive.tez.java.opts) est trop petite. Afin que chaque fois que vous voyez OOM, vous pouvez essayer d’augmenter les **hive.tez.java.opts**. Si nécessaire vous devrez peut-être augmenter les **hive.tez.container.size**. Le paramètre **java.opts** doit être d’environ 80 % de **container.size**.

> [AZURE.NOTE]  Le paramètre **hive.tez.java.opts** doit toujours être inférieure à **hive.tez.container.size**.

Depuis une machine D12 a 28 Go de mémoire, nous avons décidé d’utiliser une taille de conteneur de 10 Go (10240 Mo) et d’affecter des 80 % à java.opts. Cela a été fait sur la console de la ruche à l’aide du paramètre ci-dessous :

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

En fonction de ces paramètres, la requête a réussi en moins de dix minutes.

## <a name="conclusion-oom-errors-and-container-size"></a>Conclusion : Erreurs OOM et taille du conteneur

Une erreur se produit OOM n’implique pas nécessairement que la taille du conteneur est trop petite. Au lieu de cela, vous devez configurer les paramètres de mémoire afin que la taille du tas est augmentée et est d’au moins 80 % de la taille de mémoire de conteneur.
