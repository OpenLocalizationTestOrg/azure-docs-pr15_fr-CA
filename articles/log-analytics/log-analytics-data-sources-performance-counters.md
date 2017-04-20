<properties 
   pageTitle="Des compteurs de performance Windows et Linux dans journal Analytique | Microsoft Azure"
   description="Les compteurs de performance sont collectées par Analytique de journaux pour analyser les performances sur les agents Windows et Linux.  Cet article explique comment configurer la collecte des compteurs de performances pour les deux fenêtres et agents Linux, détails de qu’ils sont stockés dans le référentiel de l’OMS et la façon de les analyser dans le portail de l’OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Sources de données de performance Windows et Linux dans journal Analytique 

Compteurs de performance dans Windows et Linux indiquent les performances des composants matériels, des systèmes d’exploitation et des applications.  Analytique de journal peut collecter les compteurs de performances à intervalles réguliers pour l’analyse de près en temps réel (NRT) en plus de la consolidation des données de performances pour une analyse plus longue terme et de reporting.

![Compteurs de performance](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configuration des compteurs de performances

Configurer les compteurs de performances à partir du [menu données dans paramètres Analytique du journal](log-analytics-data-sources.md#configuring-data-sources).

Lorsque vous configurez tout d’abord les compteurs Windows ou Linux performances pour un nouvel espace de travail de l’OMS, vous disposez de la possibilité de créer rapidement plusieurs compteurs courants.  Ils sont répertoriés avec une case à cocher en regard de chacune.  Vérifiez que les compteurs que vous souhaitez créer initialement sont activées et puis cliquez sur **Ajouter des compteurs de performance sélectionnés**.

![Configurer les compteurs de performances de Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Suivez cette procédure pour ajouter un nouveau compteur de performance Windows à collecter.

1. Tapez le nom du compteur dans la zone de texte dans le format d' *objet (instance) \counter*.  Lorsque vous commencez à taper, vous sont présentées avec une liste correspondante de compteurs courants.  Vous pouvez sélectionner un compteur dans la liste ou du type de celle de votre choix.  Vous pouvez également renvoyer toutes les instances d’un compteur particulier en spécifiant *l’objet/compteur*. 
2. Cliquez sur **+** ou appuyez sur **entrée** pour ajouter le compteur à la liste.
3. Lorsque vous ajoutez un compteur, il utilise la valeur par défaut de 10 secondes pour l' **Intervalle d’échantillonnage**.  Vous pouvez modifier cette valeur supérieure à 1800 secondes (30 minutes) si vous souhaitez réduire les besoins de stockage des données de performances collectées.
4. Lorsque vous avez terminé l’ajout de compteurs, cliquez sur le bouton **Enregistrer** en haut de l’écran pour enregistrer la configuration.

![Configurer les compteurs de Performance de Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Suivez cette procédure pour ajouter un nouveau compteur de performance de Linux à collecter.

1. Par défaut, toutes les modifications de configuration sont automatiquement envoyées à tous les agents.  Pour les agents de Linux, un fichier de configuration est envoyé pour le collecteur de données Fluentd.  Si vous souhaitez modifier ce fichier manuellement sur chaque agent de Linux, puis désactivez la case *appliquer sous configuration de mes machines Linux*.
2. Tapez le nom du compteur dans la zone de texte dans le format d' *objet (instance) \counter*.  Lorsque vous commencez à taper, vous sont présentées avec une liste correspondante de compteurs courants.  Vous pouvez sélectionner un compteur dans la liste ou du type de celle de votre choix.  
2. Cliquez sur **+** ou appuyez sur **entrée** pour ajouter le compteur à la liste des autres compteurs pour l’objet.
3. Tous les compteurs pour un objet utilisent le même **Intervalle d’échantillonnage**.  La valeur par défaut est de 10 secondes.  Vous modifiez une valeur supérieure à 1800 secondes (30 minutes) si vous souhaitez réduire les besoins de stockage des données de performances collectées.
4. Lorsque vous avez terminé l’ajout de compteurs, cliquez sur le bouton **Enregistrer** en haut de l’écran pour enregistrer la configuration.

## <a name="data-collection"></a>Collecte de données

Analytique de journal collecte tous les compteurs de performances spécifiés à leur intervalle d’échantillonnage spécifié sur tous les agents qui ont installé compteur.  Les données ne sont pas regroupées, et les données brutes sont disponibles dans tous les modes de recherche de journal pendant la durée spécifiée par votre abonnement OMS.


## <a name="performance-record-properties"></a>Propriétés d’enregistrement de performance

Les enregistrements de performances ont un type de **performances** et ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Ordinateur         | Ordinateur sur lequel l’événement a été collecté à partir de. |
| CounterName      | Nom du compteur de performance |
| CounterPath      | Chemin d’accès complet du compteur sous la forme de \\ \\ \<ordinateur >\\object(instance)\\compteur. |
| Contre-valeur     | Valeur numérique du compteur.  |
| InstanceName     | Nom de l’instance d’événement.  Vide si aucune instance. |
| ObjectName       | Nom de l’objet de performance |
| SourceSystem  | Type de données collectées à partir de l’agent. <br> OpsManager – l’agent Windows, soit directement connecter ou SCOM <br> Linux – tous les agents de Linux  <br> AzureStorage – les Diagnostics de Windows Azure |
| TimeGenerated       | Date et heure de que l’échantillonnage de données. |


## <a name="sizing-estimates"></a>Estimations de dimensionnement

 Une estimation approximative de la collection d’un compteur particulier, à des intervalles de 10 secondes est d’environ 1 Mo par jour et par instance.  Vous pouvez estimer les besoins de stockage d’un compteur particulier, avec la formule suivante.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Recherche de journal avec les enregistrements de performances

Le tableau suivant fournit des exemples de recherche de journal qui extrait des enregistrements de performances.

| Requête | Description |
|:--|:--|
| Type = Perf | Toutes les données de performances |
| Type = Perf ordinateur = « Monordinateur » | Toutes les données de performances à partir d’un ordinateur particulier |
| Type = Perf CounterName = « Longueur de file d’attente de disque en cours » | Toutes les données de Performance d’un compteur particulier |
| Type = Perf (NomObjet = processeur) CounterName = « % temps de processeur » InstanceName = _Total & #124 ; mesure Avg(Average) en tant que AVGCPU par ordinateur | Moyenne de l’UC sur tous les ordinateurs |
| Type = Perf (CounterName = « temps de processeur % ») & #124 ;  max(Max) de mesure par ordinateur | Utilisation de l’UC maximale sur tous les ordinateurs |
| Type = Perf NomObjet = disque logique CounterName = « Longueur de la file d’attente de disque actuel » l’ordinateur = « Nom_mon_ordinateur » & #124 ; mesure Avg(Average) par InstanceName | Longueur moyenne de file d’attente du disque actuelle entre toutes les instances d’un ordinateur donné |
| Type = Perf CounterName = « DiskTransfers/s » & #124 ; percentile95(Average) de mesure par ordinateur | 95e centile de transferts disque/s sur tous les ordinateurs |
| Type = Perf CounterName = « % temps de processeur » InstanceName = « _Total » & #124 ; mesure avg(CounterValue) par intervalle d’ordinateur 1 heure | Moyenne horaire d’utilisation du processeur sur tous les ordinateurs |
| Type = Perf ordinateur = « Monordinateur » CounterName = % * InstanceName = _Total & #124 ; percentile70(CounterValue) de mesure par intervalle de CounterName 1 heure | Horaire centile 70 de chaque compteur de pourcentage % pour un ordinateur particulier |
| Type = Perf CounterName = « % temps de processeur » InstanceName = « _Total » (ordinateur = « Monordinateur ») & #124 ; min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) de mesurer par intervalle d’ordinateur 1 heure | Horaire moyen, minimum, maximum et 75-centile l’utilisation du processeur pour un ordinateur spécifique |

## <a name="viewing-performance-data"></a>Affichage des données de performances

Lorsque vous exécutez une recherche de journal de données de performances, l’affichage du **journal** est affiché par défaut.  Pour afficher les données sous forme graphique, cliquez sur **mesures**.  Pour une vue graphique détaillée, cliquez sur le **+** à côté d’un compteur.  

![Affichage des mesures réduit](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Si vous avez sélectionné la plage de temps est de 6 heures ou moins, le graphique est actualisé toutes les quelques secondes.  Les données réelles s’affiche sur le côté droit du graphique en bleu clair.

![Mesures de l’affichage développé avec des données en direct](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Pour agréger des données de performances dans une recherche, reportez-vous à la section [agrégation de mesure à la demande et visualisation dans OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions.  
- Exporter les données collectées à [l’Analyse Décisionnelle de puissance](log-analytics-powerbi.md) pour l’analyse et les visualisations supplémentaires.