<properties
   pageTitle="Twitter rubriques tendances avec tempête Apache sur HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser Trident pour créer une topologie de tempête de Apache qui détermine les rubriques des tendances sur Twitter basée sur hashtags."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Déterminer les rubriques des tendances Twitter tempête Apache sur HDInsight

Découvrez comment utiliser Trident pour créer une topologie de tempête qui détermine les rubriques des tendances (balises de hachage) sur Twitter.

Trident est une abstraction de haut niveau qui fournit des outils tels que les jointures, les agrégations, regroupement, fonctions et filtres. En outre, Trident ajoute des primitives pour effectuer le traitement dynamique et incrémentielle. Cet exemple montre comment vous pouvez créer une topologie à l’aide d’un BEC VERSEUR personnalisé, fonction et plusieurs fonctions intégrées fournies par Trident.

> [AZURE.NOTE] Cet exemple est largement basé sur l’exemple [Trident tempête](https://github.com/jalonsoramos/trident-storm) par Juan Alonso.

##<a name="requirements"></a>Configuration requise

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java et du JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">GIT</a>

* Un compte de développeur Twitter

##<a name="download-the-project"></a>Télécharger le projet

Le code suivant permet de cloner le projet localement.

    git clone https://github.com/Blackmist/TwitterTrending

##<a name="topology"></a>Topologie

La topologie dans cet exemple est la suivante :

![topologie](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Il s’agit d’une vue simplifiée de la topologie. Plusieurs instances des composants seront distribuées entre les nœuds du cluster.

Le code de Trident qui implémente la topologie est la suivante :

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Ce code effectue les opérations suivantes :

1. Crée un flux de données à partir du bec. BEC extrait tweet Twitter et les filtres de mots clés spécifiques (amour, musique et coffee dans cet exemple).

2. HashtagExtractor, une fonction personnalisée, est utilisé pour extraire des balises de hachage de chaque tweet. Ils sont émis vers le flux.

3. Le flux est regroupé par balise de hachage et passé à un agrégateur. Cette agrégation crée un décompte du nombre de fois où chaque balise de hachage s’est produite. Ces données sont rendu persistant dans la mémoire. Enfin, un flux de données qui contient la balise de hachage et le nombre est émise.

4. Étant donné que nous sommes uniquement intéressés par les balises de hachage les plus populaires d’un lot donné de tweet, l’assembly **FirstN** est appliqué pour retourner uniquement les 10 premières valeurs, en fonction du champ nombre.

> [AZURE.NOTE] Bec et HashtagExtractor, nous utilisons des fonctionnalités intégrées de Trident.
>
> Pour plus d’informations sur les opérations intégrées, reportez-vous à la section <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin du Package</a>.
>
> Pour les implémentations de Trident-état autre que MemoryMapState, consultez les rubriques suivantes :
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Recherche de tempête Trident élastique</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">Trident-redis</a>

###<a name="the-spout"></a>BEC

BEC, **TwitterSpout**, utilise <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> pour extraire tweet Twitter. Un filtre est créé (amour, musique et coffee dans cet exemple) et le tweet entrant (état) qui correspondent au filtre est stockés dans une file d’attente de blocage lié. (Pour plus d’informations, consultez la <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Classe LinkedBlockingQueue</a>.) Enfin, les éléments sont extraites de la file d’attente et émis dans la topologie.

###<a name="the-hashtagextractor"></a>La HashtagExtractor

Pour extraire les balises de hachage, <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> est utilisé pour récupérer toutes les balises de hachage qui sont contenus dans celui-ci. Puis, ils sont émis vers le flux.

##<a name="enable-twitter"></a>Activer Twitter

Pour enregistrer une nouvelle application Twitter et obtenir les informations de jeton grand public et l’accès nécessaires pour lire à partir de Twitter, utilisez les étapes suivantes :

1. Accédez à <a href="https://apps.twitter.com" target="_blank">Twitter les applications</a> et cliquez sur le bouton **créer une nouvelle application** . Lors du remplissage du formulaire, laissez vide le champ **URL de rappel** .

2. Lors de la création de l’application, cliquez sur l’onglet **clés et jetons d’accès** .

3. Copier les informations de **Clé de consommateur** et le **Secret de consommateur** .

4. En bas de la page, sélectionnez **créer mon jeton d’accès** si aucun jeton n’existe. Lorsque les jetons ont été créés, copiez les informations de **Jeton d’accès** et le **Secret de jeton d’accès** .

5. Dans le projet **TwitterSpoutTopology** que vous le cloné précédemment, ouvrez le fichier **resources/twitter4j.properties** , ajoutez les informations recueillies lors des étapes précédentes et puis enregistrez le fichier.

##<a name="build-the-topology"></a>Génération de la topologie

Pour générer le projet, utilisez le code suivant :

        cd [directoryname]
        mvn compile

##<a name="test-the-topology"></a>Test de la topologie

Utilisez la commande suivante pour tester localement la topologie :

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Après le démarrage de la topologie, vous devez voir les informations de débogage qui contient la valeur de hachage des balises et compte émises par la topologie. La sortie doit ressembler à ce qui suit :

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez testé la topologie localement, découvrez comment déployer la topologie : [déployer et gérer des topologies de tempête de Apache sur HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Peut également vous intéresser dans les rubriques suivantes de Storm :

* [Développer des topologies de Java pour tempête sur HDInsight à l’aide de Maven](hdinsight-storm-develop-java-topology.md)

* [Développer des topologies de C# pour tempête sur HDInsight à l’aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Pour obtenir d’autres exemples de Storm pour HDinsight :

* [Exemples de topologies pour tempête sur HDInsight](hdinsight-storm-example-topology.md)
