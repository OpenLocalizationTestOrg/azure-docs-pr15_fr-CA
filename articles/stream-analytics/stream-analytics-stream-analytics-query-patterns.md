<properties
    pageTitle="Exemples de modèles d’utilisation courants dans le flux de données Analytique de requête | Microsoft Azure"
    description="Modèles de requête Analytique Azure flux communs "
    keywords="exemples de requêtes"
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
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemples de modes d’utilisation courants Analytique de flux de données de requête

## <a name="introduction"></a>Introduction

Requêtes dans Azure flux Analytique sont exprimées dans un langage de requête de type SQL, qui est décrite dans le guide de [Référence de langage de requête Analytique flux](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  Cet article présente des solutions pour plusieurs modèles de requête commun en fonction de scénarios du monde réel.  Il est en cours et continuera à être mis à jour avec les nouveaux modèles de manière continue.

## <a name="query-example-data-type-conversions"></a>Exemple de requête : les conversions de type de données
**Description**: définir les types des propriétés sur le flux d’entrée.
Par exemple, les poids de voiture vient sur le flux d’entrée comme chaînes et doit être converti en INT pour effectuer de synthèse.

**Entrée**:

| Rendre | Heure | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | « 1000 » |
| Honda | 2015-01-01T00:00:02.0000000Z | « 2000 » |

**Sortie**:

| Rendre | Poids |
| --- | --- |
| Honda | 3000 |

**Solution**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explication**: utilisez une instruction de conversion dans le champ poids pour spécifier son type (voir la liste des Types de données pris en charge [ici](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Exemple de requête : à l’aide de Like, non comme à d’appariement
**Description**: vérifier que la valeur d’un champ sur l’événement correspond à un certain modèle, par exemple, les plaques d’immatriculation retour qui commencent par A et se terminant à 9

**Entrée**:

| Rendre | LicensePlate | Heure |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Sortie**:

| Rendre | LicensePlate | Heure |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Solution**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Explication**: permet de vérifier que la valeur du champ LicensePlate commence par A puis a toute chaîne de zéro caractère ou plus et il se termine par 9 l’instruction LIKE. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemple de requête : spécifient la logique pour différents cas/valeurs (instructions CASE)
**Description**: fournir un calcul différent pour un champ en fonction de certains critères.
Par exemple, fournir une description de chaîne pour le nombre de voitures est passé de la même marque avec un cas spécial pour 1.

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Sortie**:

| CarsPassed | Heure |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Solution**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explication**: clause CASE le nous permet de fournir un calcul différent en fonction de certains critères (dans notre cas, le nombre de voitures dans la fenêtre d’agrégation).

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemple de requête : envoyer des données à plusieurs sorties
**Description**: envoyer des données à partir d’une seule tâche à plusieurs cibles de sortie.
Par exemple, analyser les données de base de seuils d’alerte et archiver tous les événements pour le stockage des objets blob

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| Rendre | Heure | Nombre |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solution**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Explication**: dans la clause indique le flux Analytique qui des sorties pour écrire les données de cette instruction.
La première requête est un point de transit des données que nous avons reçu vers une sortie que nous avons appelé ArchiveOutput.
La deuxième requête est un simple regroupement et le filtrage et envoie le résultat vers un système d’alerte en aval.
*Remarque*: vous pouvez également réutiliser les résultats de la CTE (c'est-à-dire avec des instructions) dans plusieurs instructions de sortie – cela présente l’avantage d’ouvrir moins de lecteurs de la source d’entrée.
Par exemple, 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Exemple de requête : comptage des valeurs uniques
**Description**: le nombre de valeurs de champ uniques qui apparaissent dans le flux de données dans une fenêtre de temps.
Par exemple, une marque unique le nombre de voitures passées via le stand numéro payant dans une deuxième fenêtre 2 ?

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Sortie :**

| Nombre | Heure |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solution :**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**Explication :** Nous faisons une agrégation initiale pour obtenir rend unique avec leur nombre au-dessus de la fenêtre.
Nous avons ensuite effectuer une agrégation de marques combien nous étions – donné unique de toutes les valeurs dans une fenêtre obtiennent le même horodatage, puis la deuxième fenêtre d’agrégation doit être minimale ne pas agréger 2 windows à partir de la première étape.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemple de requête : déterminer si une valeur a changé.#
**Description**: regardez une valeur précédente afin de déterminer si elle est différente de la valeur actuelle est, par exemple, la voiture précédente sur la route de péage la même que la voiture en cours ?

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Sortie**:

| Rendre | Heure |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Solution**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explication**: retard à lire en entrée diffuser un événement précédent et obtenir la valeur de la marque. Comparer à la marque de l’événement en cours, puis l’événement de sortie s’ils sont différents.

## <a name="query-example-find-first-event-in-a-window"></a>Exemple de requête : premier événement dans une fenêtre de recherche
**Description**: recherche de première voiture dans chaque intervalle de 10 minutes ?

**Entrée**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| ENLV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Sortie**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solution**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Maintenant nous allons modifier le problème et rechercher première voiture de notamment effectuer dans chaque intervalle de 10 minutes.

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Exemple de requête : dernier événement de rechercher dans une fenêtre
**Description**: recherche de dernière voiture dans chaque intervalle de 10 minutes.

**Entrée**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| ENLV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Sortie**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Explication**: il y a deux étapes dans la requête – détecte que l’horodatage le plus récent dans windows de 10 minutes. La deuxième étape joint les résultats de la première requête avec flux de données d’origine pour rechercher les événements correspondants des estampilles de derniers dans chaque fenêtre. 

## <a name="query-example-detect-the-absence-of-events"></a>Exemple de requête : détecter l’absence d’événements
**Description**: Vérifiez qu’un flux de données n’a aucune valeur qui correspond à certains critères.
Par exemple, 2 voitures consécutives de la même marque saisi la route numéro payant dans les 90 secondes ?

**Entrée**:

| Rendre | LicensePlate | Heure |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | 987-DEF | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI-345 | 2015-01-01T00:00:04.0000000Z |

**Sortie**:

| Rendre | Heure | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Solution**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explication**: retard à lire en entrée diffuser un événement précédent et obtenir la valeur de la marque. Comparer à la marque de l’événement en cours, puis l’événement de sortie s’ils sont identiques et retard permet d’obtenir des données sur la voiture précédente.

## <a name="query-example-detect-duration-between-events"></a>Exemple de requête : détecter la durée entre les événements
**Description**: recherche de la durée d’un événement donné. Par exemple, étant donné un parcours web déterminer temps passé sur une fonctionnalité.

**Entrée**:  
  
| Utilisateur | Fonctionnalité | Événement | Heure |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Début | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Fin | 2015-01-01T00:00:08.0000000Z |
  
**Sortie**:  
  
| Utilisateur | Fonctionnalité | Durée |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Solution**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Explication**: fonction de dernière utilisation pour récupérer la dernière valeur d’heure lors de type d’événement 'Start'. Notez que la dernière fonction utilise PARTITION BY [utilisateur] pour indiquer le résultat calculé par utilisateur unique.  La requête a un seuil maximal de 1 heure pour une différence de temps entre les événements 'Start' et 'Stop', mais peut être configurable en tant que nécessaire (limite de DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Exemple de requête : détecter la durée d’une condition
**Description**: savoir combien de temps s’est produite une condition.
Par exemple, supposons qu’un bogue qui entraînait toutes les voitures ayant un poids incorrect (supérieure à 20 000 des livres) – nous voulons calculer la durée du bogue.

**Entrée**:

| Rendre | Heure | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Sortie**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Solution**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Explication**: utilisation de retard pour afficher le flux d’entrée de 24 heures et recherchez les instances où StartFault et StopFault sont fractionnés en poids < 20000.

## <a name="query-example-fill-missing-values"></a>Exemple de requête : remplir les valeurs manquantes
**Description**: pour le flux d’événements qui ont des valeurs manquantes, produire un flux d’événements à intervalles réguliers.
Par exemple, générer des événements toutes les 5 secondes qui indique le point de données plus récemment utilisé.

**Entrée**:

| t | valeur |
|--------------------------|-------|
| « 2014-01-01T06:01:00 » | 1 |
| « 2014-01-01T06:01:05 » | 2 |
| « 2014-01-01T06:01:10 » | 3 |
| « 2014-01-01T06:01:15 » | 4 |
| « 2014-01-01T06:01:30 » | 5 |
| « 2014-01-01T06:01:35 » | 6 |

**Sortie (10 premières lignes)**:

| windowend | lastevent.t | lastevent.Value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Solution**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explication**: cette requête génère les événements toutes les 5 secondes et affiche le dernier événement a été reçu avant. [Saut de fenêtre] Durée de (https://msdn.microsoft.com/library/dn835041.aspx "Fenêtre saut - Azure flux Analytique") détermine comment fond la requête recherche pour trouver le dernier événement (300 secondes dans cet exemple).


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
