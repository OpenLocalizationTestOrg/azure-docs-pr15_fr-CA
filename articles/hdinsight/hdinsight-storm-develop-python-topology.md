<properties
   pageTitle="Utiliser des composants de Python dans une topologie de tempête sur HDinsight | Microsoft Azure"
   description="Découvrez comment vous pouvez utiliser des composants de Python à partir avec tempête Apache sur Azure HDInsight. Vous apprendrez à utiliser les composants de Python d’à la fois un Java en fonction, et Clojure en fonction de la topologie de tempête."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Développer des topologies de tempête de Apache à l’aide de Python sur HDInsight

Tempête de Apache prend en charge plusieurs langues, même ce qui vous permet de combiner des composants de plusieurs langues dans une topologie. Dans ce document, vous apprendrez comment utiliser des composants de Python dans vos topologies de Java et tempête de base de Clojure sur HDInsight.

##<a name="prerequisites"></a>Conditions préalables

* Python 2.7 ou ultérieure

* Java JDK 1.7 ou supérieur

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>Prise en charge multilingue de tempête

Tempête a été conçu pour fonctionner avec des composants écrits à l’aide de n’importe quel langage de programmation, mais cela nécessite que les composants de comprennent comment fonctionnent avec la [définition Thrift tempête](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Pour les Python, un module est fourni dans le cadre du projet Apache tempête qui vous permet de facilement interagir avec tempête. Vous trouverez ce module à [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Tempête de Apache étant un processus Java qui s’exécute sur la Machine virtuelle Java (JVM), les composants écrits dans d’autres langues sont exécutées en tant que sous-processus. Les bits de tempête en cours d’exécution dans la machine virtuelle Java communique avec ces sous-processus à l’aide de messages JSON envoyées via stdin/stdout. Vous trouverez plus de détails sur la communication entre les composants dans la documentation du [protocole de Multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

###<a name="the-storm-module"></a>Le module de tempête

Le module de storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), fournit les bits nécessaires pour créer des composants de Python qui fonctionnent avec la tempête.

Ainsi, par exemple `storm.emit` pour émettre des tuples, et `storm.logInfo` pour écrire dans les journaux. Je vous encourage à lire sur le fichier et comprendre ce qu’il fournit.

##<a name="challenges"></a>Défis

À l’aide du module __storm.py__ , vous pouvez créer des becs verseurs les Python qui consomment les données et les boulons qui traitent les données, mais la définition de topologie tempête globale qui associe la communication entre les composants est toujours écrit à l’aide de Java ou Clojure. En outre, si vous utilisez Java, vous devez également créer des composants Java qui agissent comme une interface avec les composants de Python.

En outre, puisque les clusters tempête exécutent en mode distribué, vous devez vous assurer que tous les modules requis par vos composants de Python sont disponibles sur tous les nœuds du travailleur dans le cluster. Tempête ne fournit aucun moyen simple pour effectuer cette opération pour les ressources de multi-lang, vous devez soit inclure toutes les dépendances dans le fichier jar de la topologie, ou installer manuellement les dépendances sur chaque nœud du travailleur dans le cluster.

###<a name="java-vs-clojure-topology-definition"></a>Définition de topologie de Java et Clojure

Les deux méthodes de définition d’une topologie, Clojure est de loin le plus simple/tâche que vous pouvez directement des composants de python une référence dans la définition de la topologie. Pour les définitions de la topologie basée sur Java, vous devez également définir les composants Java qui gèrent les choses que vous déclarez les champs dans les tuples retournée à partir des composants Python.

Les deux méthodes sont décrites dans ce document, ainsi que des exemples de projets.

##<a name="python-components-with-a-java-topology"></a>Composants de Python avec une topologie de Java

> [AZURE.NOTE] Cet exemple est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) dans le répertoire __JavaTopology__ . Il s’agit d’un projet de base de Maven. Si vous ne connaissez pas Maven, consultez [basée sur Java de développer les topologies avec tempête Apache sur HDInsight](hdinsight-storm-develop-java-topology.md) pour plus d’informations sur la création d’un projet Maven pour une topologie de tempête.

Une topologie basée sur Java qui utilise les Python (ou autres composants de langage JVM,) s’affiche initialement pour utiliser des composants Java ; mais si vous regardez dans chacun des becs verseurs Java/boulons, vous verrez le code semblable au suivant :

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Il s’agit où Java appelle les Python et exécute le script qui contient la logique réelle de boulon. Becs verseurs/boulons Java (pour cet exemple), déclarez simplement les champs dans le tuple qui sera émise par le composant sous-jacent de Python.

Les fichiers de Python réels sont stockés dans le `/multilang/resources` répertoire dans cet exemple. Le `/multilang` le répertoire est référencé dans le __pom.xml__:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

Cela inclut tous les fichiers dans le `/multilang` dossier dans le fichier jar qui est généré à partir de ce projet.

> [AZURE.IMPORTANT] Notez que cette option spécifie seulement les `/multilang` répertoire et pas `/multilang/resources`. Tempête attend des ressources non-JVM dans un `resources` répertoire, donc il est recherché en interne déjà. Placement des composants dans ce dossier vous permet de simplement référence par un nom dans le code Java. Par exemple, `super("python", "countbolt.py");`. Une autre façon de penser qu’il est que Storm voit le `resources` répertoire racine (/) lors de l’accès aux ressources de multi-lang.
>
> Pour cet exemple de projet, les `storm.py` module est inclus dans le `/multilang/resources` répertoire.

###<a name="build-and-run-the-project"></a>Générer et exécuter le projet

Pour exécuter ce projet localement, utilisez simplement la commande Maven suivante pour générer et exécuter en mode local :

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Utilisez ctrl + c pour arrêter le processus.

Pour déployer le projet sur un cluster HDInsight Apache tempête en cours d’exécution, procédez comme suit :

1. Générer un jar colossaux :

        mvn package

    Cela créera un fichier nommé __WordCount--1.0-SNAPSHOT.jar__ dans les `/target` répertoire de ce projet.

2. Télécharger le fichier jar dans le cluster d’Hadoop en utilisant l’une des méthodes suivantes :

    * Pour les clusters de HDInsight __basé sur Linux__ : utilisation `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` pour copier le fichier jar au cluster, en remplaçant le nom d’utilisateur et votre nom d’utilisateur SSH NOM_CLUSTER avec le nom du cluster HDInsight.

        Une fois le fichier de téléchargement est terminé, la connexion au cluster à l’aide de SSH et démarrer la topologie à l’aide`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Pour les clusters de HDInsight __basée sur Windows__ : se connecter au tableau de bord tempête en accédant à HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez NOM_CLUSTER avec votre nom de cluster HDInsight et fournir le nom d’administrateur et le mot de passe lorsque vous y êtes invité.

        À l’aide de l’écran, effectuez les actions suivantes :

        * __Fichier JAR__: sélectionnez __Parcourir__, puis sélectionnez le fichier __WordCount-1.0-SNAPSHOT.jar__
        * __Nom de classe__: entrez`com.microsoft.example.WordCount`
        * __Paramètres supplémentaires__: entrez un nom convivial tel que `wordcount` pour identifier la topologie

        Enfin, sélectionnez __Envoyer__ à démarrer la topologie.

> [AZURE.NOTE] Une fois démarré, une topologie de tempête fonctionne jusqu'à l’arrêt (tués). Pour arrêter la topologie, utilisez soit le `storm kill TOPOLOGYNAME` commande à partir de la ligne de commande (session SSH à un cluster Linux par exemple,) ou à l’aide de l’interface utilisateur de tempête, sélectionnez la topologie et cliquez sur le bouton __Supprimer__ .

##<a name="python-components-with-a-clojure-topology"></a>Composants de Python avec une topologie de Clojure

> [AZURE.NOTE] Cet exemple est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) dans le répertoire __ClojureTopology__ .

Cette topologie a été créée à l’aide de [Leiningen](http://leiningen.org) pour [créer un nouveau projet Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Après cela, les modifications suivantes au projet avec génération de modèles ont été apportées :

* __Project.CLJ__: ajouter des dépendances de tempête et des exclusions pour les éléments qui peuvent provoquer un problème lors du déploiement sur le serveur HDInsight.
* __ressources/ressources__: Leiningen crée une valeur par défaut `resources` répertoire, mais les fichiers stockés ici semblent sont ajoutés à la racine du fichier jar créé à partir de ce projet et tempête attend des fichiers dans un sous-répertoire nommé `resources`. Pour un sous-répertoire a été ajouté et les Python de fichiers est stockés dans `resources/resources`. Au moment de l’exécution, ce est considéré comme étant la racine (/) pour accéder aux composants de Python.
* __src/WordCount/Core.CLJ__: ce fichier contient la définition de la topologie et est référencé à partir du fichier __project.clj__ . Pour plus d’informations sur l’utilisation de Clojure pour définir une topologie de tempête, consultez [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###<a name="build-and-run-the-project"></a>Générer et exécuter le projet

__Pour générer et exécuter le projet localement__, utilisez la commande suivante :

    lein clean, run

Pour arrêter la topologie, utilisez __Ctrl + C__.

__Pour créer un uberjar et le déployer vers HDInsight__, procédez comme suit :

1. Créer un uberjar contenant de la topologie et les dépendances obligatoires :

        lein uberjar

    Ceci créera un nouveau fichier nommé `wordcount-1.0-SNAPSHOT.jar` dans les `target\uberjar+uberjar` répertoire.
    
2. Pour déployer et exécuter la topologie d’un cluster de HDInsight, utilisez une des méthodes suivantes :

    * __Basé sur Linux de HDInsight__
    
        1. Copiez le fichier à l’aide de nœud principal de cluster HDInsight `scp`. Par exemple :
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Remplacez le nom d’utilisateur avec un utilisateur SSH pour votre cluster et NOM_CLUSTER avec votre nom de cluster HDInsight.
            
        2. Une fois que le fichier a été copié sur le cluster, utiliser le protocole SSH pour se connecter au cluster et de soumettre la tâche. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez une des opérations suivantes :
        
            * [Utiliser le protocole SSH avec basé sur Linux de HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Utiliser le protocole SSH avec basé sur Linux de HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Une fois connecté, utilisez la suivante pour démarrer la topologie :
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight de basées sur Windows__
    
        1. Se connecter au tableau de bord tempête en accédant à HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez NOM_CLUSTER avec votre nom de cluster HDInsight et fournir le nom d’administrateur et le mot de passe lorsque vous y êtes invité.

        2. À l’aide de l’écran, effectuez les actions suivantes :

            * __Fichier JAR__: sélectionnez __Parcourir__, puis sélectionnez le fichier __wordcount-1.0-SNAPSHOT.jar__
            * __Nom de classe__: entrez`wordcount.core`
            * __Paramètres supplémentaires__: entrez un nom convivial tel que `wordcount` pour identifier la topologie

            Enfin, sélectionnez __Envoyer__ à démarrer la topologie.

> [AZURE.NOTE] Une fois démarré, une topologie de tempête fonctionne jusqu'à l’arrêt (tués). Pour arrêter la topologie, utilisez soit le `storm kill TOPOLOGYNAME` commande à partir de la ligne de commande (session SSH à un cluster Linux,) ou à l’aide de l’interface utilisateur de tempête, sélectionnez la topologie et cliquez sur le bouton __Supprimer__ .

##<a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris comment utiliser des composants de Python à partir d’une topologie de tempête. Consultez les documents suivants pour d’autres façons d’utiliser les Python avec HDInsight :

* [Comment faire pour utiliser les Python pour la diffusion des travaux de MapReduce](hdinsight-hadoop-streaming-python.md)
* [Comment utiliser les Python utilisateur définie par fonctions (UDF) de porcs et de la ruche](hdinsight-python.md)
