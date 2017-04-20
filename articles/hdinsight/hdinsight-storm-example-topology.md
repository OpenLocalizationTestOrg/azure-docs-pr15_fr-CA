<properties
 pageTitle="Exemples de topologies Apache tempête sur HDInsight | Microsoft Azure"
 description="Une liste d’exemples de topologies tempête créé et testé avec tempête Apache sur HDInsight, y compris des topologies de base C# et Java et l’utilisation de concentrateurs de l’événement."
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

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Exemple tempête toplogies et les composants de tempête Apache sur HDInsight

Voici une liste d’exemples créées et maintenues par Microsoft pour une utilisation avec la tempête Apache sur HDInsight. Ces exemples couvrent une variété de sujets, de création de base de C# et de topologies de Java à l’utilisation des services Azure, tels que les concentrateurs d’événement, DocumentDB, BI de puissance, de la base de données SQL, HBase de HDInsight et le stockage Azure. Certains exemples montrent également comment utiliser des technologies non-Azure, ou même non Microsoft, telles que SignalR et Socket.IO

| Description                                                                                             | Montre                                         | Langage ou la structure         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Écrire dans le magasin de LAC de données Azure à partir de la tempête d’Apache](hdinsight-storm-write-data-lake-store.md) | Écriture dans la banque de LAC de données Azure | Java |
| [Source d’événement bec de concentrateur et boulon](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Source de l’événement concentrateur bec et boulon | Java |
| [Développer des topologies de basée sur Java pour tempête Apache sur HDInsight][5797064f]                                 | Maven                                                | Java                       |
| [Développer des topologies de C# pour tempête Apache sur HDInsight à l’aide de Visual Studio][16fce2d1]                     | Outils d’HDInsight pour Visual Studio                    | C#, Java                   |
| [Créer des flux de données multiples dans une topologie de tempête C#][ec5a4064]                                         | Plusieurs flux de données                                     | C#                         |
| [Déterminer les rubriques des tendances Twitter tempête sur HDInsight][3c86c7c8]                                   | Trident                                              | Java, Trident              |
| [Traiter les événements de concentrateurs d’événement Azure avec tempête sur HDInsight (C#)][844d1d81]                                | Concentrateurs d’événement                                           | C# et Java                |
| [Traiter les événements de concentrateurs d’événement Azure avec tempête sur HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Concentrateurs d’événement | Java |
| [Alimentation Bi permet de visualiser les données d’une topologie de tempête][94d15238]                              | Alimentation BI                                             | C#                         |
| [Analyse des données de capteur avec tempête et HBase dans HDInsight][ab894747]                                     | Événement concentrateurs, HBase, Socket.IO, tableau de bord Web          | C#, Java, JavaScript, HTML |
| [Traiter des données de capteur de véhicule de concentrateurs d’événement à l’aide de la tempête sur HDInsight][246ee964]                        | Événement concentrateurs, DocumentDb, stockage Azure Blob (WASB)    | C#, Java                   |
| [Extraction, transformation et chargement (ETL) de concentrateurs d’événement Azure à HBase, à l’aide de la tempête sur HDInsight][b4b68194] | Événement concentrateurs, HBase                                    | C#                         |
| [Modèle de projet topologie tempête C# pour travailler avec des services Azure de tempête sur HDInsight][ce0c02a2]  | Événement concentrateurs, DocumentDb, SQL de la base de données, HBase, SignalR | C#, Java                   |
| [Tests d’évolutivité pour la lecture de concentrateurs d’événement Azure à l’aide de la tempête sur HDInsight][d6c540e3]           | Débit des messages, concentrateurs d’événement, de la base de données SQL         | C#, Java                   |
| [Mettre en corrélation des événements à l’aide de la tempête et HBase sur HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Utilisez les Python avec tempête sur HDInsight](hdinsight-storm-develop-python-topology.md) | Composants de Python avec Java et Clojure en fonction des topologies de tempête | Python |

## <a name="next-steps"></a>Étapes suivantes

* [Mise en route de tempête Apache sur HDInsight][2b8c3488]

* [Apprenez à déployer et à gérer des topologies de tempête avec tempête sur HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Apprenez à créer une tempête sur cluster de HDInsight et utiliser le tableau de bord de Storm pour déployer les exemples de topologies."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Apprenez à déployer et à gérer des topologies en utilisant le tableau de bord tempête basée sur le web et l’interface utilisateur de la tempête ou les outils de HDInsight pour Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Apprenez à créer des topologies de tempête C# pour Visual Studio à l’aide des outils HDInsight."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Apprenez à créer des topologies de tempête dans Java, à l’aide de Maven, en créant une topologie de base wordcount."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Montre comment écrire des données à la veille stratégique de puissance d’une topologie de C#, puis créer un graphique et le tableau de bord à partir des données."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Montre une topologie tempête de base effectue un wordcount, implémenté dans C#. Il montre également comment créer un flux de données multiples au sein d’une topologie de C#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Découvrez comment lire et écrire des données à partir de concentrateurs d’événement Azure avec tempête sur HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Découvrez comment utiliser Apache tempête sur HDInsight pour traiter des données de capteur de concentrateurs d’événement Azure, de visualiser à l’aide de D3.js et (éventuellement), stockez-les HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Découvrez comment utiliser Trident pour créer une topologie de tempête qui détermine les rubriques des tendances (basés sur hashtags,) sur Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Apprenez à utiliser une topologie de Storm pour lire les messages de concentrateurs d’événement Azure, lire des documents à partir d’Azure DocumentDB pour faire référence à des données et enregistrer les données dans le stockage Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Plusieurs topologies pour montrer le débit lors de la lecture de concentrateurs d’événement Azure et stockage à base de données SQL à l’aide de Apache tempête sur HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Apprenez à lire des données à partir de concentrateurs d’événement d’Azure, agrégation et transformer les données, puis de la stocker à HBase sur HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ce projet contient des modèles de becs verseurs, les boulons et les topologies pour interagir avec les différents services Azure comme événement concentrateurs, DocumentDB et de la base de données SQL."
 
