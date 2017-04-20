<properties 
   pageTitle="Journaux des événements Windows dans le journal Analytique | Microsoft Azure"
   description="Journaux d’événements Windows sont une des sources de données plus courantes utilisés par le journal Analytique.  Cet article décrit comment configurer la collection des journaux des événements de Windows et les détails des enregistrements qu’ils créent dans le référentiel de l’OMS."
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
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="windows-event-log-data-sources-in-log-analytics"></a>Sources de données de journal des événements Windows dans le journal Analytique

Journaux d’événements Windows sont parmi les plus courantes [des sources de données](log-analytics-data-sources.md) utilisées pour les agents de Windows dans la mesure où il s’agit de la méthode utilisée par la plupart des applications pour enregistrer les informations et les erreurs.  Vous pouvez collecter des événements à partir des journaux standard tels que des applications et du système en plus de spécifier des journaux personnalisés créés par les applications que vous devez surveiller.

![Événements Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Journaux des événements de Windows Configuration

Configurer les journaux d’événements Windows dans le [menu données de paramètres de journal Analytique](log-analytics-data-sources.md#configuring-data-sources).

Analytique de journal sera uniquement collecter des événements à partir des journaux des événements Windows qui sont spécifiés dans les paramètres.  Vous pouvez ajouter un nouveau journal en tapant le nom du journal en cliquant sur **+**.  Pour chaque journal, uniquement les événements dont le niveau de gravité sélectionné seront collectées.  Vérifiez les niveaux de gravité pour le journal particulier que vous souhaitez collecter.  Vous ne pouvez pas fournir des critères supplémentaires pour filtrer les événements.

![Configurer des événements de Windows](media/log-analytics-data-sources-windows-events/configure.png)


## <a name="data-collection"></a>Collecte de données

Analytique de journal collecte chaque événement qui correspond à un niveau de gravité sélectionné à partir d’un journal des événements surveillé lors de la création de l’événement.  L’agent enregistre sa place dans chaque journal des événements qu’il collecte à partir de.  Si l’agent est mis hors connexion pendant une période de temps, puis journal Analytique sera collecter des événements à partir de l’endroit où il s’était arrêté, même si ces événements ont été créés alors que l’agent était hors ligne.


## <a name="windows-event-records-properties"></a>Événements Windows enregistre des propriétés

Les enregistrements d’événement Windows ont un type **d’événement** et ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Ordinateur            | Nom de l’ordinateur sur lequel l’événement a été prélevé. |
| EventCategory       | Catégorie de l’événement. |
| EventData           | Toutes les données d’événement au format raw. |
| ID de l’événement             | Numéro de l’événement. |
| EventLevel          | Gravité de l’événement sous forme numérique. |
| EventLevelName      | Gravité de l’événement sous forme de texte. |
| Journal des événements            | Nom du journal des événements que l’événement a été prélevé. |
| ParameterXml        | Valeurs de paramètre d’événement au format XML. |
| ManagementGroupName | Nom du groupe de gestion pour les agents SCOM.  Pour les autres agents, il s’agit d’AOI-<workspace ID> |
| RenderedDescription | Description de l’événement avec les valeurs de paramètre |
| Source              | Source de l’événement. |
| SourceSystem  | Type de l’événement ont été collectée à partir de l’agent. <br> OpsManager – l’agent Windows, soit directement connecter ou SCOM <br> Linux – tous les agents de Linux  <br> AzureStorage – les Diagnostics de Windows Azure |
| TimeGenerated       | Date et heure de que l’événement a été créé dans Windows. |
| Nom d’utilisateur            | Nom d’utilisateur du compte qui a enregistré l’événement. |



## <a name="log-searches-with-windows-events"></a>Recherche du journal des événements de Windows

Le tableau suivant fournit des exemples de recherche de journal qui extraient des enregistrements d’événements de Windows.

| Requête | Description |
|:--|:--|
| Type = événement | Tous les événements de Windows. |
| Type = EventLevelName de l’événement = Erreur | Tous les événements de Windows avec la gravité de l’erreur. |
| Type = événement & #124 ; Count() mesure par Source | Événements de décompte de Windows par source. |
| Type = EventLevelName de l’événement = erreur & #124 ; Count() mesure par Source | Événements d’erreur de décompte de Windows par source. |

## <a name="next-steps"></a>Étapes suivantes

- Configuration Analytique de journal pour collecter d’autres [sources de données](log-analytics-data-sources.md) pour l’analyse.
- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions.  
- Utilisez les [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les événements consignés dans des champs.
- Configurer la [collection de compteurs de performance](log-analytics-data-sources-performance-counters.md) à partir de vos agents de Windows.