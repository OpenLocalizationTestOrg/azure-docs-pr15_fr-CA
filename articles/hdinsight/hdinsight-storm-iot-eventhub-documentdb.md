<properties
 pageTitle="Traiter les données de capteur de véhicule avec tempête Apache sur HDInsight | Microsoft Azure"
 description="Découvrez comment traiter les données de capteur de véhicule de concentrateurs d’événement à l’aide de Apache tempête sur HDInsight. Ajouter des données de modèle à partir de DocumentDB et de stocker la sortie vers le stockage."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Traiter des données de capteur de véhicule de concentrateurs d’événement Azure à l’aide de Apache tempête sur HDInsight

Découvrez comment traiter les données de capteur de véhicule de concentrateurs d’événement Azure à l’aide de Apache tempête sur HDInsight. Cet exemple lit des données de capteur de concentrateurs d’événement Azure, enrichit les données en faisant référence à des données stockées dans les DocumentDB d’Azure et enfin de stocker les données dans le stockage Azure en utilisant le système de fichier Hadoop (très).

![HDInsight et le schéma d’architecture Internet des objets (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##<a name="overview"></a>Vue d’ensemble

Ajout de capteurs pour véhicules vous permet de prédire les problèmes de matériel en fonction des tendances des données historiques, ainsi que d’apporter des améliorations aux versions futures en fonction de l’analyse de modèle de l’utilisation. Pendant le traitement de lot MapReduce traditionnel peut être utilisé pour cette analyse, vous devez être en mesure de rapidement et efficacement charger les données de tous les véhicules Hadoop avant MapReduce traitement peut se produire. En outre, vous pouvez souhaiter effectuer des analyses pour les chemins d’erreur critique (température du moteur, freins, etc.) en temps réel.

Azure concentrateurs d’événement sont générés pour gérer le volume massif des données générées par des capteurs et tempête Apache sur HDInsight peut être utilisé pour charger et traiter les données avant de les stocker dans le très (assorties de stockage Azure) pour tout traitement supplémentaire MapReduce.

##<a name="solution"></a>Solution

Les données de télémétrie pour la température du moteur, la température ambiante et la vitesse du véhicule sont enregistrées par des capteurs, puis envoyés à des concentrateurs d’événements ainsi que Identification numéro du véhicule de la voiture et un horodatage. À partir de là, une topologie de tempête s’exécutant sur une vague d’Apache sur HDInsight cluster lit les données, traite et stocke dans très.

Pendant le traitement, la VIN est utilisée pour récupérer des informations sur le modèle de DocumentDB d’Azure. Il est ajouté au flux de données avant d’être stocké.

Les composants utilisés dans la topologie de la tempête sont les suivantes :

* **EventHubSpout** - lit les données de concentrateurs d’événement Azure

* **TypeConversionBolt** - convertit la chaîne JSON à partir de l’événement concentrateurs en un tuple qui contient les valeurs de données individuelles pour la température du moteur, la température ambiante, vitesse, VIN et timestamp

* **DataReferencBolt** - recherche le modèle de véhicule à partir de DocumentDB à l’aide de l’identification du véhicule

* **WasbStoreBolt** - stocke les données à très (stockage Azure)

Voici un diagramme de cette solution :

![topologie de tempête](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] Il s’agit d’un diagramme simplifié, et chaque composant de la solution peut avoir plusieurs instances. Par exemple, plusieurs instances de chaque composant de la topologie sont distribuées entre les nœuds de la tempête sur cluster de HDInsight.

##<a name="implementation"></a>Mise en œuvre

Complète, solution automatisée pour ce scénario est disponible dans le cadre du référentiel [HDInsight-tempête-exemples](https://github.com/hdinsight/hdinsight-storm-examples) sur GitHub. Pour utiliser cet exemple, suivez les étapes de la [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plusieurs exemples de topologies tempête, consultez [exemples de topologies pour tempête sur HDInsight](hdinsight-storm-example-topology.md).
