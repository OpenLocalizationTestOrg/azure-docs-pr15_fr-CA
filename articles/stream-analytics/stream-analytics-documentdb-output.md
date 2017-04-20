<properties
    pageTitle="Sortie JSON pour le flux de données Analytique | Microsoft Azure"
    description="Découvrez comment Analytique de flux peuvent cibler Azure DocumentDB pour une sortie JSON, pour l’archivage des données et de faible latence des requêtes sur les données JSON non structurées."
    keywords="Sortie JSON"
    documentationCenter=""
    services="stream-analytics,documentdb"
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

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>DocumentDB d’Azure cible pour la sortie JSON à partir de flux de données Analytique

Analytique de flux peut cibler [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) de JSON de sortie, ce qui permet des requêtes de faible latence et d’archivage des données sur les données JSON non structurées. Découvrez comment implémenter au mieux cette intégration.

Pour ceux qui ne sont pas familiarisés avec DocumentDB, examinons [DocumentDB cursus](https://azure.microsoft.com/documentation/learning-paths/documentdb/) pour commencer.

## <a name="basics-of-documentdb-as-an-output-target"></a>Notions de base de DocumentDB sous la forme d’une cible de sortie
La sortie de DocumentDB d’Azure dans le flux de données Analytique permet d’écrire votre flux de traitement des résultats en sortie JSON dans votre DocumentDB de regroupements. Analytique de flux de données ne crée pas de collections dans votre base de données, à la place que vous ayez à les créer dès le départ. Il s’agit de sorte que les coûts de facturation des collections de DocumentDB sont transparentes pour vous, et que vous pouvez optimiser les performances, la cohérence et la capacité de vos collections directement à l’aide de l' [API de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). Nous recommandons d’utiliser une base de données de DocumentDB par le flux de travail à séparer logiquement vos collections pour un travail en continu.

Certaines de ces options de collection DocumentDB sont détaillées ci-dessous.

## <a name="tune-consistency-availability-and-latency"></a>Régler la cohérence, la disponibilité et latence

Pour respecter les exigences de votre application, DocumentDB vous permet de bien régler la base de données et des Collections et des compromis entre le temps de latence, la disponibilité et la cohérence. Selon les niveaux de cohérence de lecture vos besoins du scénario par rapport à lire et écrire des temps de latence, que vous pouvez choisir un niveau de cohérence sur votre compte de base de données. Par défaut, DocumentDB permet également l’indexation synchrone sur chaque opération CRUD à votre collection. Il s’agit d’une autre option utile pour contrôler les performances de lecture/écriture dans DocumentDB. Pour plus d’informations sur ce sujet, consultez l’article de [modifier votre niveau de cohérence de base de données et de requête](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Choisissez un niveau de performances

Collections de DocumentDB peuvent être créées à 3 niveaux de performance différents (S1, S2 ou S3), qui déterminent le débit disponible pour CRUDs à la collection. En outre, les performances est concerné par les niveaux d’indexation/consistency dans votre collection. Reportez-vous à [cet article](../documentdb/documentdb-performance-levels.md) pour comprendre ces performances en détail.

## <a name="upserts-from-stream-analytics"></a>Upserts à partir du flux de données Analytique

Intégration d’Analytique de flux avec DocumentDB vous permet d’insérer ou de mettre à jour des enregistrements dans votre collection de DocumentDB basée sur une colonne d’ID de Document donnée. Il est également appelé un *Upsert*.

Flux de données Analytique utilise une approche Upsert optimiste, où les mises à jour sont effectuées uniquement lors de l’insertion échoue en raison d’un conflit d’ID de Document. Cette mise à jour est effectuée par le flux de données Analytique sous la forme d’un correctif, il permet des mises à jour partielles pour le document, par exemple, l’ajout de nouvelles propriétés ou le remplacement de qu'une propriété existante est effectuée de façon incrémentielle. Notez que les modifications des valeurs de propriétés du tableau dans le résultat de document JSON dans la totalité du tableau lors de l’obtention de remplacement, par exemple, le tableau ne soit pas fusionné.

## <a name="data-partitioning-in-documentdb"></a>Données de partitionnement dans DocumentDB

DocumentDB les collections vous permettent de partitionner vos données basées sur les modèles de requête et des besoins de performances de votre application. Chaque collection peut contenir jusqu'à 10 Go de données (maximum), et il n’existe actuellement aucun moyen d’évoluer (ou de dépassement de capacité) à une collection. Pour faire évoluer, Analytique de flux vous permet d’écrire à plusieurs collections avec un préfixe donné (voir les détails d’utilisation ci-dessous). Analytique de flux de données utilise la stratégie de [Résolution de Partition de hachage](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) cohérente en fonction de l’utilisateur de PartitionKey colonne pour partitionner ses enregistrements de sortie. Le nombre de collections avec le préfixe spécifié lors du démarrage de la tâche de transmission en continu est utilisé comme compteur de partition de la sortie, dans lequel la tâche écrit en parallèle (Collections de DocumentDB = Partitions de sortie). Pour une collection S3 avec cette méthode, l’indexation différée n’insère que, sur 0.4 débit d’écriture Mo/s peut être attendu. À l’aide de plusieurs collections peut vous permettre d’atteindre un débit supérieur et des capacités accrues.

Si vous avez l’intention d’augmenter le nombre de partitions à l’avenir, vous devrez interrompre votre travail, repartitionner les données à partir de vos collections existantes dans les nouvelles collections et redémarrez la tâche de flux de données Analytique. Plus de détails sur l’utilisation de PartitionResolver et de partitionnement avec l’exemple de code, figureront dans un billet de suivi. Le [partitionnement dans DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) de l’article fournit également des détails sur ce.

## <a name="documentdb-settings-for-json-output"></a>Paramètres de DocumentDB pour la sortie JSON

Création de DocumentDB sous la forme d’une sortie dans un flux de données Analytique génère une invite de commandes pour plus d’informations, comme indiqué ci-dessous. Cette section fournit une explication de la définition de propriétés.

![écran de sortie d’analytique de flux documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **Alias de sortie** – un alias pour faire référence à ce résultat dans votre requête ASA  
-   **Nom du compte** : le nom ou le point de terminaison de l’URI du compte DocumentDB.  
-   **Clé de compte** – la clé d’accès partagé pour le compte DocumentDB.  
-   **Base de données** – nom de base de données de la DocumentDB.  
-   **Modèle de nom de collection** – le modèle de nom de collection pour les collections à utiliser. Le format du nom de collection peut être construit en utilisant le jeton facultatif {partition}, où les partitions de démarrer à partir de 0. Suivantes sont des exemples des entrées valides :  
   1\) MyCollection – une collection nommée « MyCollection » doit exister.  
   2\) MyCollection {partition} – ces collections doivent exister – « MyCollection0 », « MyCollection1 », « MyCollection2 » et ainsi de suite.  
-   **Clé de partition** – le nom du champ dans les événements de sortie permet de spécifier la clé de partitionnement de sortie provenant de plusieurs collections. Pour la sortie de la collection unique, une colonne de sortie arbitraire peut être utilisé, par exemple IDPartition.  
-   **ID de document** – facultatif. Le nom du champ dans les événements de sortie utilisé pour spécifier la clé primaire sur lequel insérer ou mettre à jour les opérations sont basées.  
