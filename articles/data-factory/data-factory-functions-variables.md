<properties 
    pageTitle="Les Variables système et les fonctions d’usine de données | Microsoft Azure" 
    description="Fournit une liste des variables système et les fonctions d’Azure Data Factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - fonctions et Variables système
Cet article fournit des informations sur les fonctions et les variables prises en charge par l’usine de données Azure.
  
## <a name="data-factory-system-variables"></a>Variables de système de données en usine

Nom de la variable | Description | Portée de l’objet | Portée JSON et les cas d’utilisation
------------- | ----------- | ------------ | ------------------------
WindowStart | Début de l’intervalle de temps pour l’activité en cours, exécutée la fenêtre | activité | <ol><li>Spécifier des requêtes de sélection de données. Consultez les articles de lien référencés dans l’article [d’Activités de déplacement des données](data-factory-data-movement-activities.md) .</li><li>Passer des paramètres au script de ruche (exemple ci-dessus).</li>
WindowEnd | Fin de l’intervalle de temps pour l’activité en cours, exécutée la fenêtre | activité | comme ci-dessus
SliceStart | Début de l’intervalle de temps pour la tranche de données en cours de production | activité<br/>groupe de données | <ol><li>Spécifiez les noms de fichiers et les chemins d’accès de dossier dynamique pendant que vous travaillez avec [Azure Blob](data-factory-azure-blob-connector.md) et des [jeux de données de système de fichiers](data-factory-onprem-file-system-connector.md).</li><li>Spécifier les dépendances d’entrée avec les fonctions de fabrique de données dans la collection d’entrées d’activité.</li></ol>
SliceEnd | Fin de l’intervalle de temps pour la tranche de données actuel en cours de production | activité<br/>groupe de données | même que ci-dessus. 

> [AZURE.NOTE] Usine de données exige actuellement que le calendrier spécifié dans l’activité exactement correspondre à la planification spécifiée dans la disponibilité du groupe de données de sortie. Cela signifie que WindowStart, WindowEnd et SliceStart et SliceEnd sont toujours mappent à la même période et une tranche de sortie unique.
 
## <a name="data-factory-functions"></a>Fonctions de données en usine 

Vous pouvez utiliser des fonctions dans la fabrique de données avec au-dessus de variables système mentionnées aux fins suivantes :

1.  Spécification de requêtes de sélection de données (voir les articles de connecteur référencées par l’article [d’Activités de déplacement des données](data-factory-data-movement-activities.md) .

    La syntaxe pour appeler une fonction de fabrique de données est : ** $$ ** pour les requêtes de sélection de données et d’autres propriétés de l’activité et les groupes de données.  
2. Collection des entrées spécifiant les dépendances d’entrée avec les fonctions de données en usine dans l’activité (voir l’exemple ci-dessus).

    $$ n’est pas nécessaire de spécifier des expressions de dépendance d’entrée.   

Dans l’exemple suivant, la propriété **sqlReaderQuery** dans un fichier JSON est affectée à une valeur renvoyée par la fonction **Text.Format** . Cet exemple utilise également une variable système nommée **WindowStart**, qui représente l’heure de début de l’activité à exécuter la fenêtre.
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>Fonctions

Les tableaux suivants répertorient toutes les fonctions d’Azure Data Factory :

Catégorie | Fonction | Paramètres | Description
-------- | -------- | ---------- | ----------- 
Heure | AddHours(X,Y) | X: DateTime <br/><br/>Y : int | Ajoute des heures de Y à la fois X. <br/><br/>Exemple : 9/5/2013 12:00:00 PM + 2 heures = 9/5/2013 2:00:00 PM
Heure | AddMinutes(X,Y) | X: DateTime <br/><br/>Y : int | Ajoute les minutes de Y à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 15 minutes = 9/15/2013 12:15:00 PM
Heure | StartOfHour(X) | X: Datetime | Obtient l’heure de début de l’heure représentée par le composant "heure" de X. <br/><br/>Exemple : StartOfHour de 9/15/2013 05:10:23 PM est 9/15/2013 05:00:00 PM
Date | AddDays(X,Y) | X: DateTime<br/><br/>Y : int | Ajoute des jours de Y à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 2 jours = 9/17/2013 12:00:00 PM
Date | AddMonths(X,Y) | X: DateTime<br/><br/>Y : int | Ajoute des mois de Y à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 mois = 10/15/2013 12:00:00 PM 
Date | AddQuarters(X,Y) | X: DateTime <br/><br/>Y : int | Ajoute les Y * 3 mois à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 trimestre = 12/15/2013 12:00:00 PM
Date | AddWeeks(X,Y) | X: DateTime<br/><br/>Y : int | Ajoute les Y * X 7 jours<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 semaine = 9/22/2013 12:00:00 PM
Date | AddYears(X,Y) | X: DateTime<br/><br/>Y : int | Ajoute des années de Y à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 an = 9/15/2014 12:00:00 PM
Date | Day(X) | X: DateTime | Obtient le composant "jour" de X.<br/><br/>Exemple : Jour 9/15/2013 12:00:00 PM est 9. 
Date | DayOfWeek(X) | X: DateTime | Obtient le jour de la semaine du composant de X.<br/><br/>Exemple : DayOfWeek 9/15/2013 12:00:00 PM est dimanche.
Date | DayOfYear(X) | X: DateTime | Obtient le jour de l’année représenté par le composant "année" de X.<br/><br/>Exemples :<br/>12/1/2015 : jour 335 de 2015<br/>12/31/2015 : jour 365 de 2015<br/>12/31/2016 : jour 366 de 2016 (année bissextile)
Date | DaysInMonth(X) | X: DateTime | Obtient les jours du mois représenté par le composant "mois" du paramètre X.<br/><br/>Exemple : JoursDansLeMois de 9/15/2013 sont 30 puisqu’il y a 30 jours dans le mois de septembre.
Date | EndOfDay(X) | X: DateTime | Obtient l’heure-date représentant la fin de la journée (composant « jour ») de X.<br/><br/>Exemple : EndOfDay de 9/15/2013 05:10:23 PM est 9/15/2013 11:59:59 PM.
Date | EndOfMonth(X) | X: DateTime | Obtient la fin du mois représenté par le composant "mois" du paramètre X. <br/><br/>Exemple : EndOfMonth de 9/15/2013 05:10:23 PM est 9/30/2013 11:59:59 PM (heure date représentant la fin du mois de septembre)
Date | StartOfDay(X) | X: DateTime | Obtient le début du jour représenté par le composant « jour » du paramètre X.<br/><br/>Exemple : StartOfDay de 9/15/2013 05:10:23 PM est 9/15/2013 12:00:00 AM.
DateTime | FROM(X) | X : chaîne de | Analyser chaîne X à une date heure.
DateTime | Ticks(X) | X: DateTime | Obtient les graduations de la propriété du paramètre X. Une graduation est égale à 100 nanosecondes. La valeur de cette propriété représente le nombre de graduations qui se sont écoulés depuis 12:00:00 minuit, le 1 er janvier 0001. 
Texte | Format(X) | X : variable de type chaîne de | Met en forme le texte.

#### <a name="textformat-example"></a>Exemple de Text.Format

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

Voir [Custom Date and Time Format Strings](https://msdn.microsoft.com/library/8kb3ddd4.aspx) rubrique qui décrit les différentes options de mise en forme que vous pouvez utiliser (par exemple : yy / aaaa). 

> [AZURE.NOTE] Lorsque vous utilisez une fonction dans une autre fonction, vous n’avez pas besoin d’utiliser **$$** le préfixe de la fonction interne. Par exemple : $$Text.Format (« PartitionKey eq \\' my_pkey_filter_value\\' et RowKey ge \\' {0 : yyyy-MM-JJ HH : mm :}\\», Time.AddHours (SliceStart, -6)). Dans cet exemple, notez que **$$** préfixe n’est pas utilisé pour la fonction **Time.AddHours** . 
  

