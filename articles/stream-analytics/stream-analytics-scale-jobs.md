<properties
    pageTitle="Mettre à l’échelle des travaux de flux Analytique pour augmenter le débit | Microsoft Azure"
    description="Découvrez comment mettre à l’échelle des tâches de flux de données Analytique par configuration de partitions d’entrée, réglage de la définition de la requête et la définition des unités de flux de travail."
    keywords="données de transmission en continu, diffusion en continu de traitement des données, optimiser analytique"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Mettre à l’échelle les travaux Azure Analytique de flux pour améliorer le débit de traitement des données de flux

Apprenez à optimiser les tâches analytique et calculer les *unités de transmission en continu* pour l’Analytique des flux de données, comment faire évoluer les tâches de flux de données Analytique par configuration de partitions d’entrée, le réglage de la définition de requête analytique et la définition des unités de flux de travail. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quelles sont les parties d’une tâche de flux de données Analytique ?
Une définition de tâche de flux de données Analytique inclut des entrées, une requête et sortie. Entrées proviennent où le travail lit le flux de données, la requête est utilisée pour transformer le flux d’entrée de données, et le résultat est que la tâche envoie les résultats des travaux à.  

Un travail nécessite au moins une source d’entrée pour la diffusion en continu de données. La source d’entrée de flux de données peut être stockée dans un concentrateur d’événements Azure Service Bus ou dans le stockage des objets Blob Azure. Pour plus d’informations, consultez [Introduction aux Azure flux Analytique](stream-analytics-introduction.md) et la [mise en route à l’aide d’Azure flux Analytique](stream-analytics-get-started.md).

## <a name="configuring-streaming-units"></a>La configuration des unités de transmission en continu
Unités de transmission en continu (SUs) représentent les ressources et la puissance de calcul nécessaire à l’exécution d’une tâche d’Azure flux Analytique. SUs fournissent un moyen de décrire l’événement relatif basée sur une mesure combinée de processeur, mémoire, la capacité de traitement et lire et écrire des taux. Chaque unité de diffusion correspond à environ 1 Mo/s de débit. 

Choisir le nombre de SUs sont requis pour une tâche particulière dépend de la configuration de partition pour les entrées et la requête définie pour la tâche. Vous pouvez sélectionner jusqu'à votre quota en continu des unités pour un projet à l’aide du portail classique d’Azure. Chaque abonnement Azure par défaut dispose d’un quota de 50 unités de transmission en continu pour tous les travaux d’analytique dans une région spécifique. Pour augmenter les unités de transmission en continu de vos abonnements, contactez le [Support technique de Microsoft](http://support.microsoft.com).

Le nombre d’unités de transmission en continu un travail pouvant utiliser dépend de la configuration de partition pour les entrées et la requête définie pour la tâche. Notez également qu’une valeur valide pour les unités de flux de données doit être utilisée. Les valeurs valides commencent à 1, 3, 6, puis vers le haut par incréments de 6, comme indiqué ci-dessous.

![Échelle d’unités de flux flux Azure Analytique][img.stream.analytics.streaming.units.scale]

Cet article vous explique comment calculer et régler la requête pour augmenter le débit pour les travaux d’analytique.

## <a name="embarrassingly-parallel-job"></a>Travail excessivement parallèle
La tâche excessivement parallèle est le scénario le plus évolutif, nous disposons d’Azure flux Analytique. Il connecte à une partition de l’entrée à une instance de la requête à une partition de la sortie. Atteindre ce parallélisme nécessite plusieurs choses :

1.  Si votre logique de requête dépend de la même clé en cours de traitement par la même instance de la requête, vous devez vous assurer que les événements passent à la même partition de votre saisie. Dans le cas de concentrateurs d’événement, cela signifie que les données d’événement doit avoir **PartitionKey** ou que vous pouvez utiliser des expéditeurs partitionnées. Pour les Blob, cela signifie que les événements sont envoyés dans le même dossier de la partition. Si votre logique de requête ne nécessite pas de la même touche traitée par la même instance de la requête, vous pouvez ignorer cette exigence. Un exemple serait une requête select / / filtre de projet simple.  
2.  Une fois les données sont disposées comme il faut sur le côté d’entrée, nous avons besoin pour vous assurer que votre requête est partitionnée. Pour cela, vous utilisez **Partition By** dans toutes les étapes. Plusieurs étapes sont autorisés, mais ils doivent être partitionnés par la même clé. Une autre chose à noter est que, actuellement, la clé de partitionnement doit être définie à **IDPartition** pour un travail parallèle entièrement.  
3.  Actuellement uniquement les concentrateurs d’événement et l’objet Blob de prise en charge de sortie partitionnée. Pour la sortie de concentrateurs d’événement, vous devez configurer le champ **PartitionKey** pour être **IDPartition**. Pour les Blob, vous n’avez rien à faire.  
4.  Une autre chose à noter, le nombre de partitions d’entrée doit être égale le nombre de partitions de sortie. Sortie de BLOB ne prend actuellement en charge les partitions, mais c’est OK, car il hérite du modèle de partitionnement de la requête en amont. Exemples de valeurs de partition qui permettraient à un travail totalement parallèle :  
    1.  8 concentrateurs d’événement d’entrée partitions et 8 événements concentrateurs sortie des partitions
    2.  8 concentrateurs d’événement d’entrée de partitions et sortie de Blob  
    3.  8 les partitions blob d’entrée et la sortie de Blob  
    4.  Partitions de sortie 8 partitions d’entrée de blob et les concentrateurs d’événement 8  

Voici quelques exemples de scénarios qui sont excessivement parallèles.

### <a name="simple-query"></a>Requête simple
Concentrateur d’événement d’entrée – événement concentrateurs avec 8 partitions sortie – avec 8 partitions

**Requête :**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Cette requête est un filtre simple, et en tant que tel, nous ne doit pas vous inquiéter sur le partitionnement de l’entrée de que nous envoyer à des concentrateurs d’événement. Vous remarquerez que la requête a **Partition By** **IDPartition**, donc nous répondre à la demande #2 ci-dessus. Pour la sortie, nous devons configurer la sortie de concentrateurs d’événement dans la tâche pour que le champ **PartitionKey** la valeur **IDPartition**. Une dernière vérification, partitions d’entrée == partitions de sortie. Cette topologie est excessivement parallèle.

### <a name="query-with-grouping-key"></a>Requête avec clé de groupement
Blob d’entrée – événement concentrateurs avec 8 partitions de sortie :

**Requête :**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête comporte une clé de groupement et en tant que tel, la même clé doit être traité par la même instance de requête. Cela signifie que nous devons envoyer nos événements pour les événements de concentrateurs de manière partitionnée. Touche qui nous intéressent ? **IDPartition** est un concept logique de travail, que nous intéressent est **TollBoothId**. Cela signifie que nous devons définir la **PartitionKey** des données d’événement nous envoyer à des concentrateurs d’événement à la **TollBoothId** de l’événement. La requête a **Partition By** **IDPartition**, donc nous y a-t-il une bonne. Pour la sortie, étant un Blob, nous n’avez pas besoin à vous soucier de la configuration de **PartitionKey**. Pour condition #4, encore une fois, c’est Blob, nous n’avez pas besoin de vous inquiétez pas. Cette topologie est excessivement parallèle.

### <a name="multi-step-query-with-grouping-key"></a>Requête multi étape avec clé de groupement ###
Concentrateur d’événements d’entrée – concentrateur d’événements avec 8 partitions sortie – avec 8 partitions

**Requête :**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête comporte une clé de groupement et en tant que tel, la même clé doit être traité par la même instance de requête. Nous pouvons utiliser la même stratégie que la requête précédente. La requête comporte plusieurs étapes. Chaque étape a **Partition By** de **IDPartition**? Oui, c’est bons. Pour la sortie, nous devons définir la **PartitionKey** à **IDPartition** comme décrit ci-dessus et nous constatons également qu’il a le même nombre de partitions comme entrée. Cette topologie est excessivement parallèle.


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Exemples de scénarios qui ne sont pas excessivement parallèle

### <a name="mismatched-partition-count"></a>Nombre de partitions ne correspondent pas ###
Concentrateur d’événement d’entrée – événement concentrateurs avec 8 partitions sortie – avec 32 partitions

Peu importe ce que la requête est dans ce cas, car l’entrée de partition count ! = nombre de partitions de sortie.

### <a name="not-using-event-hubs-or-blobs-as-output"></a>Utilisez ne pas événement concentrateurs ou BLOB en tant que sortie
Entrée – événement concentrateurs avec 8 partitions de sortie : PowerBI

PowerBI sortie ne prend actuellement en charge le partitionnement.

### <a name="multi-step-query-with-different-partition-by-values"></a>Requête d’étape multiples avec différentes valeurs de Partition By
Concentrateur d’événements d’entrée – concentrateur d’événements avec 8 partitions sortie – avec 8 partitions

**Requête :**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Comme vous pouvez le voir, la seconde étape utilise **TollBoothId** comme la clé de partitionnement. Ce n’est pas identique à la première étape et donc nous effectuer une lecture aléatoire. 

Voici quelques exemples et une counterexamples des tâches de flux Analytique qui seront en mesure de réaliser une topologie excessivement parallèle et avec lui la possibilité d’échelle maximale. Pour les tâches qui ne tiennent pas un de ces profils, détaillant comment faire évoluer au maximum le parmi les autres scénarios de flux de données Analytique canoniques de mises à jour ultérieures sont effectués.

Pour désormais utiliser des orientations générales ci-dessous :

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calculer le maximum d’unités d’un travail de diffusion en continu
Le nombre total d’unités de transmission en continu qui peut être utilisée par une tâche de flux de données Analytique varie selon le nombre d’étapes dans la requête définie pour le projet et le nombre de partitions pour chaque étape.

### <a name="steps-in-a-query"></a>Étapes dans une requête
Une requête peut comprendre une ou plusieurs étapes. Chaque étape est une sous-requête définie à l’aide du mot clé **WITH** . La requête uniquement qui n’est pas le mot clé **WITH** est également considérée comme une étape, par exemple, l’instruction **SELECT** dans la requête suivante :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

La requête précédente comporte deux étapes.

> [AZURE.NOTE] Cet exemple de requête sera expliqué plus loin dans l’article.

### <a name="partition-a-step"></a>Partitionner une étape

Partitionnement d’une étape requiert les conditions suivantes :

- La source d’entrée doit être partitionnée. Pour plus d’informations, consultez le [Guide de programmation des concentrateurs d’événement](../event-hubs/event-hubs-programming-guide.md).
- L’instruction **SELECT** de la requête doit lire à partir d’une source d’entrée partitionnée.
- La requête de l’étape doit être le mot clé **Par Partition**

Lorsqu’une requête est partitionnée, les événements d’entrée seront les groupes traités et agrégées dans partition distincte, et des événements de sorties sont générés pour chacun des groupes. Si un agrégat combiné est souhaitable, vous devez créer une deuxième étape non partitionné à agréger.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calculer le max. unités d’un projet de diffusion en continu

Toutes les étapes non partitionnée entre eux peuvent mettre à l’échelle jusqu'à six unités de transmission en continu pour une tâche de flux de données Analytique. Pour ajouter plus de diffusion en continu des unités, une étape doit être partitionnée. Chaque partition peut posséder six unités de transmission en continu.

<table border="1">
<tr><th>Requête d’un travail</th><th>Max. unités pour le travail de diffusion en continu</th></td>

<tr><td>
<ul>
<li>La requête contient une étape unique.</li>
<li>L’étape n’est pas partitionnée.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Le flux de données d’entrée est partitionné par 3.</li>
<li>La requête contient une étape unique.</li>
<li>L’étape est partitionnée.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La requête comporte deux étapes.</li>
<li>Aucune des étapes est partitionné.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>L’entrée de flux de données est partitionnée par 3.</li>
<li>La requête comporte deux étapes. L’étape d’entrée est partitionnée et la deuxième étape ne l’est pas.</li>
<li>L’instruction SELECT lit l’entrée partitionnée.</li>
</ul>
</td>
<td>24 (18 pour obtenir la procédure partitionnée) + 6 pour les étapes non partitionnée</td></tr>
</table>

### <a name="examples-of-scale"></a>Exemples d’échelle
La requête suivante calcule le nombre de voitures de passer par un poste de péage avec trois péages dans une fenêtre de trois minutes. Cette requête peut être mise à l’échelle six unités de transmission en continu.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Pour utiliser plusieurs unités de transmission en continu de la requête, à la fois le flux de données d’entrée et que la requête doit être partitionnée. Étant donné que la partition de flux de données est définie sur 3, la requête modifiée suivante peut être mise à l’échelle 18 unités de transmission en continu :

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Lorsqu’une requête est partitionnée, les événements d’entrée sont traitées et agrégées dans les groupes de partition distincte. Événements de sortie sont également générés pour chacun des groupes. Le partitionnement peut provoquer des résultats inattendus lorsque le champ **Group by** n’est pas la clé de Partition dans l’entrée de flux de données. Par exemple, le champ **TollBoothId** dans l’exemple de requête précédent n’est pas la clé de Partition de Input1. Les données de péage #1 peuvent se propager dans plusieurs partitions.

Chacune des partitions Input1 est traité séparément par flux Analytique et plusieurs enregistrements de l’inventaire de voiture-pass-through pour le péage même dans la même fenêtre qui s’écroulent seront créés. Si la clé de partition d’entrée ne peut pas être modifiée, ce problème peut être résolu en ajoutant une étape supplémentaire d’une partition, par exemple :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Cette requête peut être mise à l’échelle à 24 unités de transmission en continu.

>[AZURE.NOTE] Si vous joignez deux flux, s’assurer que les flux de données est partitionnées par la clé de partition de la colonne que vous effectuez les jointures, et avoir le même nombre de partitions dans les deux flux.


## <a name="configure-stream-analytics-job-partition"></a>Configurer la partition de travail Analytique de flux

**Pour ajuster une unité de diffusion en continu d’un projet**

1. Ouvrez une session sur le [portail de gestion](https://manage.windowsazure.com).
2. Dans le volet gauche, cliquez sur **Flux Analytique** .
3. Cliquez sur la tâche de flux Analytique que vous souhaitez mettre à l’échelle.
4. Cliquez sur **échelle** en haut de la page.

![Échelle d’unités de flux flux Azure Analytique][img.stream.analytics.streaming.units.scale]

Dans le portail d’Azure, les paramètres d’échelle est accessible sous Paramètres :

![Configuration des tâches Azure Portal flux Analytique][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>Analyseur de performances de travail

À l’aide du portail de gestion, vous pouvez suivre le débit d’une tâche dans les événements par seconde :

![Surveillance des tâches Azure Analytique de flux][img.stream.analytics.monitor.job]

Permet de calculer le débit de la charge de travail attendu événements par seconde. Si le débit est inférieure à celle attendue, régler la partition d’entrée, optimiser la requête et ajouter d’autres unités de transmission en continu à votre travail.

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>Débit en flux Analytique à grande échelle - scénario de framboises Pi


Pour comprendre comment les tâches de flux de données analytique mise à l’échelle dans un scénario classique en termes de débit de traitement sur plusieurs unités de transmission en continu, voici une expérience qui envoie des données de capteur (clients) à un concentrateur de l’événement, il traite et envoie d’alerte ou des statistiques sous la forme d’une sortie à un autre concentrateur d’événements.

Le client envoie des données de capteur de synthèse à des concentrateurs d’événement au format JSON à flux Analytique et la sortie des données est également au format JSON.  Voici l’aspect de l’échantillon de données similaires :  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Requête : « envoyer une alerte lorsque le voyant est éteint »  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

Mesure de débit : Débit dans ce contexte est la quantité de données d’entrée traitées par flux Analytique dans une quantité fixe de temps (10 minutes). Pour obtenir le meilleur débit de traitement pour les données d’entrée, à la fois le flux de données d’entrée et que la requête doit être partitionnée. Également **COUNT()**est inclus dans la requête pour mesurer le nombre d’événements d’entrée ont été traité. Pour garantir que la tâche est en attente pas simplement pour les événements d’entrée à venir, chaque partition du concentrateur d’événement d’entrée a été préchargée avec les données d’entrée nécessaires (environ 300 Mo).  

Vous trouverez ci-dessous les résultats avec l’augmentation du nombre d’unités de transmission en continu et Partition correspondante compte dans les concentrateurs d’événement.  

<table border="1">
<tr><th>Partitions d’entrée</th><th>Partitions de sortie</th><th>Unités de transmission en continu</th><th>Débit soutenu
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 Mo/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 Mo/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 Mo/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 Mo/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 Mo/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 Mo/s</td>
</tr>
</table>

![IMG.Stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
