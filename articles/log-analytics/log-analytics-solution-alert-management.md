<properties
   pageTitle="Alerte de la solution de gestion de Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="La solution de gestion des alertes dans le journal Analytique vous permet d’analyser toutes les alertes dans votre environnement.  Outre la consolidation des alertes générées au sein de l’OMS, il importe les alertes à partir de groupes de gestion de System Center Operations Manager (SCOM) connectés dans journal Analytique."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="bwren" />

# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Solution de gestion des alerte dans la Suite Gestion des opérations (OMS)

![Icône de gestion des alerte](media/log-analytics-solution-alert-management/icon.png) La solution de gestion des alertes vous permet d’analyser toutes les alertes dans votre environnement.  Outre la consolidation des alertes générées au sein de l’OMS, il importe les alertes à partir de groupes de gestion de System Center Operations Manager (SCOM) connectés dans journal Analytique.  Dans les environnements avec plusieurs groupes d’administration, la solution de gestion des alertes fournira une vue consolidée des alertes dans tous les groupes de gestion.

## <a name="prerequisites"></a>Conditions préalables

- Pour importer des alertes depuis SCOM, cette solution nécessite une connexion entre votre espace de travail de l’OMS et un groupe de gestion de SCOM à l’aide de la procédure décrite dans le [Gestionnaire des opérations se connecter au journal Analytique](log-analytics-om-agents.md).  


## <a name="configuration"></a>Configuration de

Ajouter la solution de gestion des alertes dans votre espace de travail OMS en utilisant la procédure décrite dans les [solutions d’ajouter](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.

## <a name="management-packs"></a>Packs d’administration

Si votre groupe de gestion de SCOM est connecté à votre espace de travail de l’OMS, les packs d’administration suivant seront installés dans SCOM lorsque vous ajoutez cette solution.  Il n’y a aucune configuration ou la maintenance de ces packs d’administration requis.  

- Gestion des alertes Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Pour plus d’informations sur la manière dont les packs d’administration solution sont mis à jour, voir [Se connecter Operations Manager pour l’Analytique de journal](log-analytics-om-agents.md).

## <a name="data-collection"></a>Collecte de données

### <a name="agents"></a>Agents

Le tableau suivant décrit les sources connectées sont pris en charge par cette solution.

| Source connecté | Prise en charge | Description |
|:--|:--|:--|
| [Agents Windows](log-analytics-windows-agents.md) | N° | Les agents Windows directs ne génèrent pas d’alertes SCOM. |
| [Agents de Linux](log-analytics-linux-agents.md) | N° | Les agents Linux directs ne génèrent pas d’alertes SCOM. |
| [Groupe de gestion SCOM](log-analytics-om-agents.md) | Oui | Alertes générées sur agents SCOM sont remis au groupe d’administration et ensuite transmis au journal Analytique.<br><br>Une connexion directe à partir de l’agent SCOM au journal Analytique n’est pas nécessaire. Données d’alerte sont transférées du groupe d’administration dans le référentiel de l’OMS. |
| [Compte de stockage Azure](log-analytics-azure-storage.md) | N° | Alertes SCOM ne sont pas stockés dans les comptes de stockage Azure. |

### <a name="collection-frequency"></a>Fréquence de collection

Alertes générées au sein de l’OMS sont immédiatement disponibles pour la solution.  Données d’alerte sont envoyées à partir du groupe de gestion de SCOM à Analytique de journal toutes les trois minutes.  

## <a name="using-the-solution"></a>À l’aide de la solution

Lorsque vous ajoutez la solution de gestion des alertes à votre espace de travail de l’OMS, la mosaïque de la **Gestion des alertes** est ajoutée à votre tableau de bord OMS.  Cette mosaïque affiche un nombre et une représentation graphique du nombre d’alertes actives qui ont été générées dans les 24 dernières heures.  Vous ne pouvez pas modifier ce laps de temps.

![Mosaïque de gestion des alertes](media/log-analytics-solution-alert-management/tile.png)

Cliquez sur la mosaïque pour ouvrir le tableau de bord de **Gestion des alertes** de **Gestion des alertes** .  Le tableau de bord inclut les colonnes dans le tableau suivant.  Chaque colonne répertorie les dix principales alertes en nombre correspondant aux critères de cette colonne pour la portée spécifiée et la plage de temps.  Vous pouvez exécuter une recherche de journal qui fournit la liste complète en cliquant sur **Afficher tout** en bas de la colonne ou en cliquant sur l’en-tête de colonne.

| Colonne| Description |
|:--|:--|
| Alertes critiques | Toutes les alertes dont la gravité est critique, regroupées par nom de l’alerte.  Cliquez sur un nom d’alerte pour exécuter une recherche de journal de retourner tous les enregistrements pour cette alerte. |
| Alertes d’avertissement | Toutes les alertes dont la gravité de l’avertissement, regroupé par nom de l’alerte.  Cliquez sur un nom d’alerte pour exécuter une recherche de journal de retourner tous les enregistrements pour cette alerte. |
| Alertes SCOM Active | Toutes les alertes SCOM avec n’importe quel état autre que *fermé* , groupées par source ayant généré l’alerte. |
| Toutes les alertes actives | Toutes les alertes avec n’importe quel degré de gravité regroupés par le nom de l’alerte. Inclut uniquement les alertes SCOM avec tout état autre que *fermé*.|

Si vous faites défiler vers la droite, le tableau de bord répertorie plusieurs requêtes communes que vous pouvez cliquer sur pour effectuer une [recherche dans un journal](log-analytics-log-searches.md) pour les données d’alerte.

![Tableau de bord de gestion des alertes](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>Portée et la plage horaire

Par défaut, la portée des alertes dans la solution de gestion des alertes d’analyse est de tous les groupes de gestion connectés générés dans les 7 derniers jours.  

![Portée de la gestion des alertes](media/log-analytics-solution-alert-management/scope.png)

- Pour modifier les groupes d’administration inclus dans l’analyse, cliquez sur **étendue** dans la partie supérieure du tableau de bord.  Vous pouvez sélectionner **Global** pour tous les groupes de gestion connectés ou **Par groupe d’administration** sélectionner un seul groupe d’administration.

- Pour modifier la plage de temps des alertes, sélectionnez les **données basées sur** en haut du tableau de bord.  Vous pouvez sélectionner les alertes générées dans les 7 derniers jours, 1 jour ou 6 heures.  Ou bien, vous pouvez sélectionner **personnalisée** et spécifier une plage de dates personnalisée.

## <a name="log-analytics-records"></a>Enregistrements de journal Analytique

La solution de gestion des alertes analyse un enregistrement avec un type **d’alerte**.  Il sera également importer les alertes de SCOM et créer un enregistrement correspondant pour chaque type **d’alerte** et un SourceSystem de **OpsManager**.  Ces enregistrements ont les propriétés dans le tableau suivant.  

| Propriété | Description |
|:--|:--|
| Type de | *Alerte* |
| SourceSystem | *OpsManager* |
| AlertContext | Détails de l’élément de données qui a provoqué l’alerte à générer au format XML. |
| AlertDescription | Description détaillée de l’alerte. |
| AlertId | GUID de l’alerte. |
| Nom_alerte | Nom de l’alerte. |
| AlertPriority | Niveau de priorité de l’alerte. |
| AlertSeverity | Niveau de gravité de l’alerte. |
| AlertState | Dernier état de résolution de l’alerte. |
| LastModifiedBy | Nom de l’utilisateur qui a modifié l’alerte. |
| ManagementGroupName | Nom du groupe d’administration où l’alerte a été générée. |
| RepeatCount | Nombre de fois que le même message d’alerte a été générée pour la même surveillé objet depuis en cours de résolution. |
| Résolu par | Nom de l’utilisateur ayant résolu l’alerte. Vide si l’alerte n’a pas encore été résolu. |
| SourceDisplayName | Nom complet de l’objet de surveillance qui a généré l’alerte. |
| SourceFullName | Nom complet de l’objet de surveillance qui a généré l’alerte. |
| ID du ticket du | ID du ticket de l’alerte si l’environnement SCOM est intégré à un processus d’attribution des tickets pour les alertes.  Vide, aucun ticket d’ID est attribué. |
| TimeGenerated | Date et heure auxquelles l’alerte a été créée. |
| TimeLastModified | Date et heure de la dernière modification de l’alerte. |
| Heure de déclenchement | Date et heure auxquelles l’alerte a été générée. |
| TimeResolved | Date et heure auxquelles l’alerte a été résolue. Vide si l’alerte n’a pas encore été résolu. |

## <a name="sample-log-searches"></a>Recherches de journaux d’exemple

Le tableau suivant fournit des exemples des recherches de journaux pour les enregistrements d’alerte collectées par cette solution.  

| Requête | Description |
|:--|:--|
| Type = alerte SourceSystem = OpsManager AlertSeverity = heure de déclenchement d’erreur > maintenant - 24 heures sur 24 | Alertes critiques déclenchés pendant les dernières 24 heures |
| Type = alerte AlertSeverity = heure de déclenchement d’avertissement > maintenant - 24 heures sur 24 | Alertes d’avertissement déclenchés pendant les dernières 24 heures  |
| Type = alerte SourceSystem = OpsManager AlertState ! = heure de déclenchement fermé > maintenant - 24 heures sur 24 & #124 ; count() de mesure en tant que nombre par SourceDisplayName | Sources avec les alertes actives déclenchés pendant les dernières 24 heures |
| Type = alerte SourceSystem = OpsManager AlertSeverity = heure de déclenchement d’erreur > maintenant 24 heures AlertState ! = fermé | Alertes critiques déclenchés pendant les dernières 24 heures qui sont toujours actives. |
| Type = alerte SourceSystem = heure de déclenchement OpsManager > maintenant 24 heures AlertState = fermé | Alertes générées au cours des dernières 24 heures qui est maintenant fermées |
| Type = alerte SourceSystem = heure de déclenchement OpsManager > maintenant - 1 jour & #124 ; count() de mesure en tant que nombre par AlertSeverity | Déclenché pendant la journée 1 dernier regroupée par leur degré de gravité des alertes |
| Type = alerte SourceSystem = heure de déclenchement OpsManager > maintenant - 1 jour & #124 ; Trier RepeatCount desc | Alertes générées pendant la journée 1 cours triée par leur valeur de répétition |

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur [les alertes dans le journal Analytique](log-analytics-alerts.md) pour plus d’informations sur la génération d’alertes de journal Analytique.
