<properties 
   pageTitle="Sources de données dans le journal Analytique | Microsoft Azure"
   description="Sources de données permet de définir les données que collecte Analytique de journal à partir des agents et d’autres sources connectées.  Cet article décrit le concept de comment journal Analytique utilise des sources de données, explique comment les configurer et fournit un résumé des différentes sources de données disponibles."
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

# <a name="data-sources-in-log-analytics"></a>Sources de données dans le journal Analytique

Analytique de journal collecte les données à partir des Sources connectées dans votre espace de travail de l’OMS et la stocke dans le référentiel de l’OMS.  Les données collectées à partir de chacun sont définies par les Sources de données que vous configurez.  Données dans le référentiel de l’OMS sont stockées sous la forme d’un jeu d’enregistrements.  Chaque source de données crée des enregistrements d’un type particulier avec chaque type d’avoir son propre ensemble de propriétés.

![Journal de collecte de données Analytique](./media/log-analytics-data-sources/overview.png)

Sources de données sont différents de ceux des Solutions OMS également collecter des données à partir de Sources connectées et créer des enregistrements dans le référentiel de l’OMS.  Les solutions peuvent être ajoutées à votre espace de travail à partir de la galerie de Solutions et fourniront généralement des outils d’analyse supplémentaires dans le portail de l’OMS.  

## <a name="summary-of-data-sources"></a>Résumé des sources de données

Les sources de données qui sont actuellement disponibles dans le journal Analytique sont répertoriés dans le tableau suivant.  Chacun possède un lien vers un article distinct de fournir les détails de cette source de données.

| Source de données | Type d’événement | Description |
|:--|:--|:--|
| [Journaux personnalisés](log-analytics-data-sources-custom-logs.md) | \<LogName\>_CL | Fichiers de texte sur les agents Windows ou Linux qui contient des informations de journal. |
| [Journaux des événements Windows](log-analytics-data-sources-windows-events.md) | Événement | Les événements sont collectés dans le journal des événements sur des ordinateurs Windows. |
| [Compteurs de performances de Windows](log-analytics-data-sources-performance-counters.md) | Perf | Les compteurs de performance collectées à partir des ordinateurs Windows. |
| [Compteurs de Performance de Linux](log-analytics-data-sources-performance-counters.md) | Perf | Les compteurs de performance collectées à partir des ordinateurs de Linux. |
| [Journaux IIS](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Internet Information Services enregistre au format du W3C. |
| [Journal système](log-analytics-data-sources-syslog.md) | Journal système | Événements du journal système sur les ordinateurs Windows ou Linux. |

## <a name="configuring-data-sources"></a>Configuration des sources de données

Vous configurez des sources de données dans le menu **données** dans journal Analytique **paramètres**.  N’importe quelle configuration est remise à toutes les sources connectées dans votre espace de travail de l’OMS.  Actuellement impossible d’exclure tous les agents de cette configuration.

![Configurer des événements de Windows](./media/log-analytics-data-sources/configure-events.png)

2. Dans la console de l’OMS, sélectionnez la fenêtre de **paramètres** .
3. Sélectionner des **données**.
4. Cliquez sur Configurer la source de données.
5. Suivez le lien vers la documentation pour chaque source de données dans le tableau ci-dessus pour plus d’informations sur leur configuration.

## <a name="data-collection"></a>Collecte de données

Configurations de source de données sont fournies aux agents qui sont directement connectés à OMS dans quelques minutes.  Les données spécifiées sont collectées à partir de l’agent et livrées directement au journal Analytique à des intervalles spécifiques à chaque source de données.  Consultez la documentation pour chaque source de données pour ces caractéristiques.

Pour les agents de System Center Operations Manager (SCOM) dans un groupe d’administration connecté, configurations de source de données sont traduites dans les packs d’administration et remies au groupe d’administration toutes les 5 minutes par défaut.  L’agent télécharge le pack de gestion comme tout autre et collecte les données spécifiées. En fonction de la source de données les données seront que soit envoyé à un serveur de gestion, qui transmet les données à l’Analytique de journal ou l’agent enverra les données au journal Analytique sans passer par le serveur d’administration. [Détails de collecte de données pour les solutions et fonctionnalités OMS](log-analytics-add-solutions.md#data-collection-details-for-oms-features-and-solutions) pour plus d’informations, reportez-vous à la section.  Vous pouvez lire les détails de connexion SCOM et l’OMS et la modification de la fréquence de cette configuration est livrée à [Configurer l’intégration avec System Center Operations Manager](log-analytics-om-agents.md).

## <a name="log-analytics-records"></a>Enregistrements de journal Analytique

Toutes les données collectées par Analytique du journal est stocké dans le référentiel de l’OMS sous forme d’enregistrements.  Enregistrements collectées par différentes sources de données ont leur propre jeu de propriétés et être identifiés par leur **Type** de propriété.  Consultez la documentation pour chaque source de données et solution pour plus d’informations sur chaque type d’enregistrement.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [solutions](log-analytics-add-solutions.md) qui ajoutent des fonctionnalités au journal Analytique et également collecter des données dans le référentiel de l’OMS.
- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions.  
- Configurer des [alertes](log-analytics-alerts.md) pour informer des données critiques collectées à partir de sources de données et des solutions.
