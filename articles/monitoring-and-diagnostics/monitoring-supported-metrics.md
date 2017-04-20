<properties
    pageTitle="Les mesures de moniteur Azure - des mesures prises en charge par le type de ressource | Microsoft Azure"
    description="Liste des mesures disponibles pour chaque type de ressource avec le moniteur d’Azure."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="supported-metrics-with-azure-monitor"></a>Mesures prises en charge avec le moniteur d’Azure

Moniteur Azure fournit plusieurs façons d’interagir avec des mesures, y compris les graphiques dans le portail, y accéder par le biais de l’API REST ou les requêtes à l’aide de PowerShell ou l’interface CLI. Voici une liste complète de toutes les mesures actuellement disponibles au pipeline de métrique du moniteur Azure.

> [AZURE.NOTE] Autres mesures peuvent être disponibles dans le portail ou à l’aide des API héritées. Cette liste inclut uniquement les mesures de version d’évaluation disponibles à l’aide de la version d’évaluation du pipeline métrique Azure moniteur consolidée.

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CoreCount|Nombre de base|Nombre|Total|Nombre total de noyaux dans le compte de traitement par lots|
|TotalNodeCount|Nombre de nœuds|Nombre|Total|Nombre total de nœuds dans le compte de traitement par lots|
|CreatingNodeCount|Nombre de nœuds de création|Nombre|Total|Nombre de nœuds en cours de création|
|StartingNodeCount|Numéro du nœud de démarrage|Nombre|Total|Nombre de nœuds à partir de|
|WaitingForStartTaskNodeCount|En attente pour le nombre de nœuds de tâche de début|Nombre|Total|Nombre de nœuds en attente pour le démarrer la tâche à effectuer|
|StartTaskFailedNodeCount|Nombre de nœuds d’échec de tâche de début|Nombre|Total|Nombre de nœuds où démarrer la tâche a échoué|
|IdleNodeCount|Nombre de noeuds inactifs|Nombre|Total|Nombre de nœuds inactifs|
|OfflineNodeCount|Nombre de noeuds en mode hors connexion|Nombre|Total|Nombre de nœuds hors connexion|
|RebootingNodeCount|Nombre de nœuds de redémarrage|Nombre|Total|Nombre de nœuds de redémarrage|
|ReimagingNodeCount|Nombre de nœuds disponibles|Nombre|Total|Nombre de nœuds disponibles|
|RunningNodeCount|Nombre de nœuds en cours d’exécution|Nombre|Total|Nombre de nœuds en cours d’exécution|
|LeavingPoolNodeCount|En laissant le nombre de nœuds de Pool|Nombre|Total|Nombre de nœuds en laissant le Pool|
|UnusableNodeCount|Nombre de nœuds inutilisable|Nombre|Total|Nombre de nœuds inutilisables|
|TaskStartEvent|Événements de début de tâche|Nombre|Total|Nombre total de tâches qui ont démarré|
|TaskCompleteEvent|Événements Complete de tâche|Nombre|Total|Nombre total de tâches terminées|
|TaskFailEvent|Événements d’échec de tâche|Nombre|Total|Nombre total de tâches terminées en état d’échec|
|PoolCreateEvent|Pool de créer des événements|Nombre|Total|Nombre total de pools qui ont été créés.|
|PoolResizeStartEvent|Événements de début de redimensionnement de pool|Nombre|Total|Nombre total de redimensionnement de pool qui ont démarré|
|PoolResizeCompleteEvent|Événements du pool complète de redimensionnement|Nombre|Total|Nombre total de redimensionne pool terminées|
|PoolDeleteStartEvent|Supprimer les événements début du pool|Nombre|Total|Nombre total de suppressions de pool qui ont démarré|
|PoolDeleteCompleteEvent|Événements du pool complète de suppression|Nombre|Total|Nombre total de suppressions de pool terminées|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|connectedclients|Clients connectés|Nombre|Maximum||
|totalcommandsprocessed|Total des opérations|Nombre|Total||
|CacheHits|Accès au cache|Nombre|Total||
|cachemisses|Absences dans le cache|Nombre|Total||
|GetCommands|Obtient|Nombre|Total||
|setcommands|Jeux|Nombre|Total||
|evictedkeys|Clés supprimés|Nombre|Total||
|totalkeys|Nombre total de clés|Nombre|Maximum||
|expiredkeys|Clés ayant expirés|Nombre|Total||
|usedmemory|Mémoire utilisée|Octets|Maximum||
|usedmemoryRss|Mémoire utilisée RSS|Octets|Maximum||
|serverLoad|Charge du serveur|Pour cent|Maximum||
|cacheWrite|Cache d’écriture|BytesPerSecond|Maximum||
|cacheRead|Lecture du cache|BytesPerSecond|Maximum||
|percentProcessorTime|UNITÉ CENTRALE|Pour cent|Maximum||
|connectedclients0|Clients connectés (partagé 0)|Nombre|Maximum||
|totalcommandsprocessed0|Total des opérations (partagé 0)|Nombre|Total||
|cachehits0|Présences dans le cache (partagé 0)|Nombre|Total||
|cachemisses0|Absences dans le cache (partagé 0)|Nombre|Total||
|getcommands0|Obtient (partagé 0)|Nombre|Total||
|setcommands0|Jeux (partagé 0)|Nombre|Total||
|evictedkeys0|Clés supprimés (partagé 0)|Nombre|Total||
|totalkeys0|Nombre total de clés (nœud 0)|Nombre|Maximum||
|expiredkeys0|Clés a expiré (partagé 0)|Nombre|Total||
|usedmemory0|Mémoire utilisée (partagé 0)|Octets|Maximum||
|usedmemoryRss0|Mémoire utilisée RSS (partagé 0)|Octets|Maximum||
|serverLoad0|Charge du serveur (partagé 0)|Pour cent|Maximum||
|cacheWrite0|Cache d’écriture (partagé 0)|BytesPerSecond|Maximum||
|cacheRead0|Cache en lecture (partagé 0)|BytesPerSecond|Maximum||
|percentProcessorTime0|Processeur (partagé 0)|Pour cent|Maximum||
|connectedclients1|Clients connectés (partagé 1)|Nombre|Maximum||
|totalcommandsprocessed1|Total des opérations (partagé 1)|Nombre|Total||
|cachehits1|Présences dans le cache (partagé 1)|Nombre|Total||
|cachemisses1|Absences dans le cache (partagé 1)|Nombre|Total||
|getcommands1|Obtient (partagé 1)|Nombre|Total||
|setcommands1|Jeux (partagé 1)|Nombre|Total||
|evictedkeys1|Clés supprimés (partagé 1)|Nombre|Total||
|totalkeys1|Nombre total de clés (nœud 1)|Nombre|Maximum||
|expiredkeys1|Clés a expiré (partagé 1)|Nombre|Total||
|usedmemory1|Mémoire utilisée (partagé 1)|Octets|Maximum||
|usedmemoryRss1|Mémoire utilisée RSS (partagé 1)|Octets|Maximum||
|serverLoad1|Charge du serveur (partagé 1)|Pour cent|Maximum||
|cacheWrite1|Cache d’écriture (partagé 1)|BytesPerSecond|Maximum||
|cacheRead1|Cache en lecture (partagé 1)|BytesPerSecond|Maximum||
|percentProcessorTime1|Processeur (partagé 1)|Pour cent|Maximum||
|connectedclients2|Clients connectés (partagé 2)|Nombre|Maximum||
|totalcommandsprocessed2|Total des opérations (partagé 2)|Nombre|Total||
|cachehits2|Présences dans le cache (partagé 2)|Nombre|Total||
|cachemisses2|Absences dans le cache (partagé 2)|Nombre|Total||
|getcommands2|Obtient (partagé 2)|Nombre|Total||
|setcommands2|Jeux (partagé 2)|Nombre|Total||
|evictedkeys2|Clés supprimés (partagé 2)|Nombre|Total||
|totalkeys2|Nombre total de clés (nœud 2)|Nombre|Maximum||
|expiredkeys2|Clés a expiré (partagé 2)|Nombre|Total||
|usedmemory2|Mémoire utilisée (partagé 2)|Octets|Maximum||
|usedmemoryRss2|Mémoire utilisée RSS (partagé 2)|Octets|Maximum||
|serverLoad2|Charge du serveur (partagé 2)|Pour cent|Maximum||
|cacheWrite2|Cache d’écriture (partagé 2)|BytesPerSecond|Maximum||
|cacheRead2|Cache en lecture (partagé 2)|BytesPerSecond|Maximum||
|percentProcessorTime2|Processeur (partagé 2)|Pour cent|Maximum||
|connectedclients3|Clients connectés (partagé 3)|Nombre|Maximum||
|totalcommandsprocessed3|Total des opérations (partagé 3)|Nombre|Total||
|cachehits3|Présences dans le cache (partagé 3)|Nombre|Total||
|cachemisses3|Absences dans le cache (partagé 3)|Nombre|Total||
|getcommands3|Obtient (partagé 3)|Nombre|Total||
|setcommands3|Jeux (partagé 3)|Nombre|Total||
|evictedkeys3|Clés supprimés (partagé 3)|Nombre|Total||
|totalkeys3|Nombre total de clés (nœud 3)|Nombre|Maximum||
|expiredkeys3|Clés a expiré (partagé 3)|Nombre|Total||
|usedmemory3|Mémoire utilisée (partagé 3)|Octets|Maximum||
|usedmemoryRss3|Mémoire utilisée RSS (partagé 3)|Octets|Maximum||
|serverLoad3|Charge du serveur (partagé 3)|Pour cent|Maximum||
|cacheWrite3|Cache d’écriture (partagé 3)|BytesPerSecond|Maximum||
|cacheRead3|Cache en lecture (partagé 3)|BytesPerSecond|Maximum||
|percentProcessorTime3|Processeur (partagé 3)|Pour cent|Maximum||
|connectedclients4|Clients connectés (partagé 4)|Nombre|Maximum||
|totalcommandsprocessed4|Total des opérations (partagé 4)|Nombre|Total||
|cachehits4|Présences dans le cache (partagé 4)|Nombre|Total||
|cachemisses4|Absences dans le cache (partagé 4)|Nombre|Total||
|getcommands4|Obtient (partagé 4)|Nombre|Total||
|setcommands4|Jeux (partagé 4)|Nombre|Total||
|evictedkeys4|Clés supprimés (partagé 4)|Nombre|Total||
|totalkeys4|Nombre total de clés (nœud 4)|Nombre|Maximum||
|expiredkeys4|Clés a expiré (partagé 4)|Nombre|Total||
|usedmemory4|Mémoire utilisée (partagé 4)|Octets|Maximum||
|usedmemoryRss4|Mémoire utilisée RSS (partagé 4)|Octets|Maximum||
|serverLoad4|Charge du serveur (partagé 4)|Pour cent|Maximum||
|cacheWrite4|Cache d’écriture (partagé 4)|BytesPerSecond|Maximum||
|cacheRead4|Cache en lecture (partagé 4)|BytesPerSecond|Maximum||
|percentProcessorTime4|Processeur (partagé 4)|Pour cent|Maximum||
|connectedclients5|Clients connectés (partagé 5)|Nombre|Maximum||
|totalcommandsprocessed5|Total des opérations (partagé 5)|Nombre|Total||
|cachehits5|Présences dans le cache (partagé 5)|Nombre|Total||
|cachemisses5|Absences dans le cache (partagé 5)|Nombre|Total||
|getcommands5|Obtient (partagé 5)|Nombre|Total||
|setcommands5|Jeux (partagé 5)|Nombre|Total||
|evictedkeys5|Clés supprimés (partagé 5)|Nombre|Total||
|totalkeys5|Nombre total de clés (nœud 5)|Nombre|Maximum||
|expiredkeys5|Clés a expiré (partagé 5)|Nombre|Total||
|usedmemory5|Mémoire utilisée (partagé 5)|Octets|Maximum||
|usedmemoryRss5|Mémoire utilisée RSS (partagé 5)|Octets|Maximum||
|serverLoad5|Charge du serveur (partagé 5)|Pour cent|Maximum||
|cacheWrite5|Cache d’écriture (partagé 5)|BytesPerSecond|Maximum||
|cacheRead5|Cache en lecture (partagé 5)|BytesPerSecond|Maximum||
|percentProcessorTime5|Processeur (partagé 5)|Pour cent|Maximum||
|connectedclients6|Clients connectés (partagé 6)|Nombre|Maximum||
|totalcommandsprocessed6|Total des opérations (partagé 6)|Nombre|Total||
|cachehits6|Présences dans le cache (partagé 6)|Nombre|Total||
|cachemisses6|Absences dans le cache (partagé 6)|Nombre|Total||
|getcommands6|Obtient (partagé 6)|Nombre|Total||
|setcommands6|Jeux (partagé 6)|Nombre|Total||
|evictedkeys6|Clés supprimés (partagé 6)|Nombre|Total||
|totalkeys6|Nombre total de clés (Node 6)|Nombre|Maximum||
|expiredkeys6|Clés a expiré (partagé 6)|Nombre|Total||
|usedmemory6|Mémoire utilisée (partagé 6)|Octets|Maximum||
|usedmemoryRss6|Mémoire utilisée RSS (partagé 6)|Octets|Maximum||
|serverLoad6|Charge du serveur (partagé 6)|Pour cent|Maximum||
|cacheWrite6|Cache d’écriture (partagé 6)|BytesPerSecond|Maximum||
|cacheRead6|Cache en lecture (partagé 6)|BytesPerSecond|Maximum||
|percentProcessorTime6|Processeur (partagé 6)|Pour cent|Maximum||
|connectedclients7|Clients connectés (partagé 7)|Nombre|Maximum||
|totalcommandsprocessed7|Total des opérations (partagé 7)|Nombre|Total||
|cachehits7|Présences dans le cache (partagé 7)|Nombre|Total||
|cachemisses7|Absences dans le cache (partagé 7)|Nombre|Total||
|getcommands7|Obtient (partagé 7)|Nombre|Total||
|setcommands7|Jeux (partagé 7)|Nombre|Total||
|evictedkeys7|Clés supprimés (partagé 7)|Nombre|Total||
|totalkeys7|Nombre total de clés (nœud 7)|Nombre|Maximum||
|expiredkeys7|Clés a expiré (partagé 7)|Nombre|Total||
|usedmemory7|Mémoire utilisée (partagé 7)|Octets|Maximum||
|usedmemoryRss7|Mémoire utilisée RSS (partagé 7)|Octets|Maximum||
|serverLoad7|Charge du serveur (partagé 7)|Pour cent|Maximum||
|cacheWrite7|Cache d’écriture (partagé 7)|BytesPerSecond|Maximum||
|cacheRead7|Cache en lecture (partagé 7)|BytesPerSecond|Maximum||
|percentProcessorTime7|Processeur (partagé 7)|Pour cent|Maximum||
|connectedclients8|Clients connectés (partagé 8)|Nombre|Maximum||
|totalcommandsprocessed8|Total des opérations (partagé 8)|Nombre|Total||
|cachehits8|Présences dans le cache (partagé 8)|Nombre|Total||
|cachemisses8|Absences dans le cache (partagé 8)|Nombre|Total||
|getcommands8|Obtient (partagé 8)|Nombre|Total||
|setcommands8|Jeux (partagé 8)|Nombre|Total||
|evictedkeys8|Clés supprimés (partagé 8)|Nombre|Total||
|totalkeys8|Nombre total de clés (nœud de 8)|Nombre|Maximum||
|expiredkeys8|Clés a expiré (partagé 8)|Nombre|Total||
|usedmemory8|Mémoire utilisée (partagé 8)|Octets|Maximum||
|usedmemoryRss8|Mémoire utilisée RSS (partagé 8)|Octets|Maximum||
|serverLoad8|Charge du serveur (partagé 8)|Pour cent|Maximum||
|cacheWrite8|Cache d’écriture (partagé 8)|BytesPerSecond|Maximum||
|cacheRead8|Cache en lecture (partagé 8)|BytesPerSecond|Maximum||
|percentProcessorTime8|Processeur (partagé 8)|Pour cent|Maximum||
|connectedclients9|Clients connectés (partagé 9)|Nombre|Maximum||
|totalcommandsprocessed9|Total des opérations (partagé 9)|Nombre|Total||
|cachehits9|Présences dans le cache (partagé 9)|Nombre|Total||
|cachemisses9|Absences dans le cache (partagé 9)|Nombre|Total||
|getcommands9|Obtient (partagé 9)|Nombre|Total||
|setcommands9|Jeux (partagé 9)|Nombre|Total||
|evictedkeys9|Clés supprimés (partagé 9)|Nombre|Total||
|totalkeys9|Nombre total de clés (nœud 9)|Nombre|Maximum||
|expiredkeys9|Clés a expiré (partagé 9)|Nombre|Total||
|usedmemory9|Mémoire utilisée (partagé 9)|Octets|Maximum||
|usedmemoryRss9|Mémoire utilisée RSS (partagé 9)|Octets|Maximum||
|serverLoad9|Charge du serveur (partagé 9)|Pour cent|Maximum||
|cacheWrite9|Cache d’écriture (partagé 9)|BytesPerSecond|Maximum||
|cacheRead9|Cache en lecture (partagé 9)|BytesPerSecond|Maximum||
|percentProcessorTime9|Processeur (partagé 9)|Pour cent|Maximum||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|NumberOfCalls|Nombre total d’appels API|Nombre|Total|Nombre total d’appels API.|
|NumberOfFailedCalls|Nombre total d’appels de l’API a échoué|Nombre|Total|Nombre total d’appels de l’API a échoué.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Pourcentage de CPU|Pourcentage de CPU|Pour cent|Moyenne|Le pourcentage d’unités allouées compute qui sont actuellement utilisés par les ordinateurs virtuels|
|Réseau dans|Réseau dans|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Sortie du réseau|Sortie du réseau|Octets|Total|Le nombre d’octets sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|
|Octets de lecture de disque|Octets de lecture de disque|Octets|Total|Nombre total d’octets lus à partir du disque au cours de la période d’analyse|
|Octets d’écriture disque|Octets d’écriture disque|Octets|Total|Nombre total d’octets écrit sur le disque au cours de la période d’analyse|
|Opérations de lecture/s de disque|Opérations de lecture/s de disque|CountPerSecond|Moyenne|Disque en lecture e/s|
|Opérations d’écriture disque/s|Opérations d’écriture disque/s|CountPerSecond|Moyenne|Ops ES/s d’écriture sur disque|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Pourcentage de CPU|Pourcentage de CPU|Pour cent|Moyenne|Le pourcentage d’unités allouées compute qui sont actuellement utilisés par les ordinateurs virtuels|
|Réseau dans|Réseau dans|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Sortie du réseau|Sortie du réseau|Octets|Total|Le nombre d’octets sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|
|Octets de lecture de disque|Octets de lecture de disque|Octets|Total|Nombre total d’octets lus à partir du disque au cours de la période d’analyse|
|Octets d’écriture disque|Octets d’écriture disque|Octets|Total|Nombre total d’octets écrit sur le disque au cours de la période d’analyse|
|Opérations de lecture/s de disque|Opérations de lecture/s de disque|CountPerSecond|Moyenne|Disque en lecture e/s|
|Opérations d’écriture disque/s|Opérations d’écriture disque/s|CountPerSecond|Moyenne|Ops ES/s d’écriture sur disque|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Pourcentage de CPU|Pourcentage de CPU|Pour cent|Moyenne|Le pourcentage d’unités allouées compute qui sont actuellement utilisés par les ordinateurs virtuels|
|Réseau dans|Réseau dans|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Sortie du réseau|Sortie du réseau|Octets|Total|Le nombre d’octets sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|
|Octets de lecture de disque|Octets de lecture de disque|Octets|Total|Nombre total d’octets lus à partir du disque au cours de la période d’analyse|
|Octets d’écriture disque|Octets d’écriture disque|Octets|Total|Nombre total d’octets écrit sur le disque au cours de la période d’analyse|
|Opérations de lecture/s de disque|Opérations de lecture/s de disque|CountPerSecond|Moyenne|Disque en lecture e/s|
|Opérations d’écriture disque/s|Opérations d’écriture disque/s|CountPerSecond|Moyenne|Ops ES/s d’écriture sur disque|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Tentatives d’envoi de télémétrie Message|Nombre|Total|Nombre de périphérique vers le Cloud messages de télémétrie a tenté d’envoyer à votre concentrateur IoT|
|d2c.telemetry.Ingress.Success|Messages de télémétrie envoyés|Nombre|Total|Nombre de périphérique pour le nuage télémétrie messages envoyés avec succès à votre concentrateur IoT|
|c2d.Commands.egress.Complete.Success|Commandes terminées|Nombre|Total|Nombre de Cloud pour les commandes de périphérique s’est terminée correctement par le périphérique|
|c2d.Commands.egress.Abandon.Success|Commandes abandonnées|Nombre|Total|Numéro de nuage de commandes de périphérique abandonnée par le périphérique|
|c2d.Commands.egress.Reject.Success|Commandes refusées|Nombre|Total|Numéro de nuage de commandes de périphérique rejeté par le périphérique|
|devices.totalDevices|Totale unités|Nombre|Total|Nombre d’unités enregistrées à votre concentrateur IoT|
|devices.connectedDevices.allProtocol|Périphériques connectés|Nombre|Total|Nombre de périphériques connectés à votre concentrateur IoT|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|INREQS|Demandes entrantes|Nombre|Total|Événement concentrateur entrant débit des messages pour un espace de noms|
|SUCCREQ|Demandes réussies|Nombre|Total|Nombre total de demandes réussi pour un espace de noms|
|FAILREQ|Demandes ayant échoué|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|
|SVRBSY|Erreurs de disponibilité du serveur|Nombre|Total|Erreurs de disponibilité totale du serveur pour un espace de noms|
|INTERR|Erreur interne au serveur|Nombre|Total|Erreurs de total de serveur interne pour un espace de noms|
|MISCERR|Autres erreurs|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|
|INMSGS|Messages entrants|Nombre|Total|Nombre total de messages entrant pour un espace de noms|
|OUTMSGS|Messages sortants|Nombre|Total|Total messages pour un espace de noms sortants|
|EHINMBS|Octets entrants par seconde|BytesPerSecond|Total|Événement concentrateur entrant débit des messages pour un espace de noms|
|EHOUTMBS|Octets sortants par seconde|BytesPerSecond|Total|Total messages pour un espace de noms sortants|
|EHABL|Archiver les messages de file d’attente|Nombre|Total|Événement concentrateur archiver les messages dans la file d’attente pour un espace de noms|
|EHAMSGS|Archivage des messages|Nombre|Total|Concentrateur d’événements archivés les messages dans un espace de noms|
|EHAMBS|Débit archive des messages|BytesPerSecond|Total|Concentrateur d’événements archivé le débit des messages dans un espace de noms|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|RunsStarted|Séries de démarrage|Nombre|Total|Nombre de flux de travail s’exécute démarré.|
|RunsCompleted|Traitements terminés|Nombre|Total|Nombre de flux de travail est exécuté.|
|RunsSucceeded|Exécution réussie|Nombre|Total|Nombre de flux de travail s’exécute a réussi.|
|RunsFailed|Exécution Impossible|Nombre|Total|Nombre de flux de travail s’exécute a échoué.|
|RunsCancelled|Exécution annulée|Nombre|Total|Nombre de flux de travail s’exécute annulée.|
|RunLatency|Exécution de latence|Secondes|Moyenne|Latence du flux de travail terminé s’exécute.|
|RunSuccessLatency|Exécuter la latence de la réussite|Secondes|Moyenne|Latence du flux de travail a réussi s’exécute.|
|RunThrottledEvents|Exécution accélérée des événements|Nombre|Total|Numéro de l’action de workflow ou un déclencheur limité des événements.|
|RunFailurePercentage|Exécuter le pourcentage d’échec|Pour cent|Total|Pourcentage de flux de travail s’exécute a échoué.|
|ActionsStarted|Démarré des actions |Nombre|Total|Nombre d’actions de flux de travail a démarré.|
|ActionsCompleted|Actions terminées |Nombre|Total|Nombre d’actions de flux de travail terminé.|
|ActionsSucceeded|Actions a réussi. |Nombre|Total|Nombre d’actions de flux de travail a réussi.|
|ActionsFailed|Actions a échoué|Nombre|Total|Nombre d’actions de flux de travail a échoué.|
|ActionsSkipped|Actions ignorées |Nombre|Total|Nombre d’actions de workflow est ignoré.|
|ActionLatency|Latence de l’action |Secondes|Moyenne|Latence des actions de flux de travail terminé.|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Moyenne|Latence des actions de flux de travail a réussi.|
|ActionThrottledEvents|Action limitée des événements|Nombre|Total|Nombre d’actions de flux de travail limité des événements...|
|TriggersStarted|Déclencheurs démarrés |Nombre|Total|Nombre de déclencheurs de flux de travail a démarré.|
|TriggersCompleted|Déclencheurs terminées |Nombre|Total|Nombre de déclencheurs de flux de travail terminé.|
|TriggersSucceeded|Déclencheurs a réussi. |Nombre|Total|Nombre de déclencheurs de flux de travail a réussi.|
|TriggersFailed|Échoué de déclencheurs |Nombre|Total|Nombre de déclencheurs de flux de travail a échoué.|
|TriggersSkipped|Ignoré les déclencheurs|Nombre|Total|Nombre de déclencheurs de flux de travail est ignoré.|
|TriggersFired|Triggers déclenchés |Nombre|Total|Nombre de déclencheurs de flux de travail est déclenché.|
|TriggerLatency|Latence de déclencheur |Secondes|Moyenne|Latence des déclencheurs de workflow terminées.|
|TriggerFireLatency|Latence d’incendie de déclencheur |Secondes|Moyenne|Latence de déclenche déclencheurs de flux de travail.|
|TriggerSuccessLatency|Latence de réussite de déclencheur |Secondes|Moyenne|Latence des déclencheurs de flux de travail a réussi.|
|TriggerThrottledEvents|Déclencher des événements accélérées|Nombre|Total|Nombre de déclencheur de flux de travail limité des événements.|
|BillableActionExecutions|Exécutions d’Action facturable|Nombre|Total|Nombre d’exécutions d’action de flux de travail lors de l’obtention de facturation.|
|BillableTriggerExecutions|Exécutions du déclencheur facturable|Nombre|Total|Nombre d’exécutions du déclencheur du flux de travail lors de l’obtention de facturation.|
|TotalBillableExecutions|Nombre total d’exécutions facturable|Nombre|Total|Nombre d’exécutions de flux de travail lors de l’obtention de facturation.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Débit|Débit|BytesPerSecond|Moyenne||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|SearchLatency|Latence de recherche|Secondes|Moyenne|Latence moyenne de recherche du service de recherche|
|SearchQueriesPerSecond|Requêtes de recherche par seconde|CountPerSecond|Moyenne|Requêtes de recherche par seconde pour le service de recherche|
|ThrottledSearchQueriesPercentage|Pourcentage de requêtes de recherche accélérée|Pour cent|Moyenne|Pourcentage de requêtes de recherche qui ont été accélérés pour le service de recherche|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CPUXNS|Utilisation de l’UC par espace de noms|Pour cent|Maximum|Métrique d’utilisation du processeur de l’espace de noms de service bus premium|
|WSXNS|Utilisation de taille de la mémoire par espace de noms|Pour cent|Maximum|Métrique d’utilisation mémoire de service bus premium espace de noms|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage de l’UC|Pour cent|Moyenne|Pourcentage de l’UC|
|physical_data_read_percent|Pourcentage d’e/s de données|Pour cent|Moyenne|Pourcentage d’e/s de données|
|log_write_percent|Pourcentage d’e/s de journal|Pour cent|Moyenne|Pourcentage d’e/s de journal|
|dtu_consumption_percent|Pourcentage DTU|Pour cent|Moyenne|Pourcentage DTU|
|stockage|Taille totale de base de données|Octets|Maximum|Taille totale de base de données|
|connection_successful|Connexions réussies|Nombre|Total|Connexions réussies|
|connection_failed|Échec de connexion|Nombre|Total|Échec de connexion|
|blocked_by_firewall|Bloqué par le pare-feu|Nombre|Total|Bloqué par le pare-feu|
|blocage|Blocages|Nombre|Total|Blocages|
|storage_percent|Pourcentage de taille de base de données|Pour cent|Maximum|Pourcentage de taille de base de données|
|xtp_storage_percent|Percent(Preview) de stockage en mémoire OLTP|Pour cent|Moyenne|Percent(Preview) de stockage en mémoire OLTP|
|workers_percent|Pourcentage de travailleurs|Pour cent|Moyenne|Pourcentage de travailleurs|
|sessions_percent|Pourcentage de sessions|Pour cent|Moyenne|Pourcentage de sessions|
|dtu_limit|Limite DTU|Nombre|Moyenne|Limite DTU|
|dtu_used|DTU utilisé|Nombre|Moyenne|DTU utilisé|
|service_level_objective|Objectif de niveau de service de la base de données|Nombre|Total|Objectif de niveau de service de la base de données|
|dwu_limit|limite de dwu|Nombre|Maximum|limite de dwu|
|dwu_consumption_percent|Pourcentage DWU|Pour cent|Moyenne|Pourcentage DWU|
|dwu_used|DWU utilisé|Nombre|Moyenne|DWU utilisé|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage de l’UC|Pour cent|Moyenne|Pourcentage de l’UC|
|physical_data_read_percent|Pourcentage d’e/s de données|Pour cent|Moyenne|Pourcentage d’e/s de données|
|log_write_percent|Pourcentage d’e/s de journal|Pour cent|Moyenne|Pourcentage d’e/s de journal|
|dtu_consumption_percent|Pourcentage DTU|Pour cent|Moyenne|Pourcentage DTU|
|storage_percent|Pourcentage de stockage|Pour cent|Moyenne|Pourcentage de stockage|
|workers_percent|Pourcentage de travailleurs|Pour cent|Moyenne|Pourcentage de travailleurs|
|sessions_percent|Pourcentage de sessions|Pour cent|Moyenne|Pourcentage de sessions|
|eDTU_limit|limite d’eDTU|Nombre|Moyenne|limite d’eDTU|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|
|eDTU_used|eDTU utilisé|Nombre|Moyenne|eDTU utilisé|
|storage_used|Espace de stockage utilisé|Octets|Moyenne|Espace de stockage utilisé|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|ResourceUtilization|Utilisation de % SU|Pour cent|Maximum|Utilisation de % SU|
|InputEvents|Événements d’entrée|Nombre|Total|Événements d’entrée|
|InputEventBytes|Octets de l’événement d’entrée|Octets|Total|Octets de l’événement d’entrée|
|LateInputEvents|Événements d’entrée tardives|Nombre|Total|Événements d’entrée tardives|
|OutputEvents|Événements de sortie|Nombre|Total|Événements de sortie|
|ConversionErrors|Erreurs de Conversion de données|Nombre|Total|Erreurs de Conversion de données|
|Erreurs|Erreurs d’exécution|Nombre|Total|Erreurs d’exécution|
|DroppedOrAdjustedEvents|Événements de|Nombre|Total|Événements de|
|AMLCalloutRequests|Demandes de fonctions|Nombre|Total|Demandes de fonctions|
|AMLCalloutFailedRequests|Demandes de fonction qui a échoué|Nombre|Total|Demandes de fonction qui a échoué|
|AMLCalloutInputEvents|Événements de fonction|Nombre|Total|Événements de fonction|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CpuPercentage|Pourcentage de l’UC|Pour cent|Moyenne|Pourcentage de l’UC|
|MemoryPercentage|Pourcentage de mémoire|Pour cent|Moyenne|Pourcentage de mémoire|
|DiskQueueLength|Longueur de file d’attente du disque|Nombre|Total|Longueur de file d’attente du disque|
|HttpQueueLength|Longueur de la file d’attente de http|Nombre|Total|Longueur de la file d’attente de http|
|BytesReceived|Données dans|Octets|Total|Données dans|
|Octets envoyés|Données|Octets|Total|Données|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Heure CPU|Temps processeur|Secondes|Total|Temps processeur|
|Demandes|Demandes|Nombre|Total|Demandes|
|BytesReceived|Données dans|Octets|Total|Données dans|
|Octets envoyés|Données|Octets|Total|Données|
|Http2xx|HTTP 2xx|Nombre|Total|HTTP 2xx|
|Http3xx|HTTP 3xx|Nombre|Total|HTTP 3xx|
|Http401|HTTP 401|Nombre|Total|HTTP 401|
|Http403|HTTP 403|Nombre|Total|HTTP 403|
|Http404|HTTP 404|Nombre|Total|HTTP 404|
|Http406|HTTP 406|Nombre|Total|HTTP 406|
|Http4xx|4xx de http|Nombre|Total|4xx de http|
|Http5xx|Erreurs de serveur HTTP|Nombre|Total|Erreurs de serveur HTTP|
|MemoryWorkingSet|Jeu de travail de mémoire|Octets|Total|Jeu de travail de mémoire|
|AverageMemoryWorkingSet|Jeu de travail de mémoire moyenne|Octets|Moyenne|Jeu de travail de mémoire moyenne|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Heure CPU|Temps processeur|Secondes|Total|Temps processeur|
|Demandes|Demandes|Nombre|Total|Demandes|
|BytesReceived|Données dans|Octets|Total|Données dans|
|Octets envoyés|Données|Octets|Total|Données|
|Http2xx|HTTP 2xx|Nombre|Total|HTTP 2xx|
|Http3xx|HTTP 3xx|Nombre|Total|HTTP 3xx|
|Http401|HTTP 401|Nombre|Total|HTTP 401|
|Http403|HTTP 403|Nombre|Total|HTTP 403|
|Http404|HTTP 404|Nombre|Total|HTTP 404|
|Http406|HTTP 406|Nombre|Total|HTTP 406|
|Http4xx|4xx de http|Nombre|Total|4xx de http|
|Http5xx|Erreurs de serveur HTTP|Nombre|Total|Erreurs de serveur HTTP|
|MemoryWorkingSet|Jeu de travail de mémoire|Octets|Total|Jeu de travail de mémoire|
|AverageMemoryWorkingSet|Jeu de travail de mémoire moyenne|Octets|Moyenne|Jeu de travail de mémoire moyenne|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les mesures dans le moniteur d’Azure](./monitoring-overview.md#monitoring-sources)
- [Créer des alertes sur des métriques](./insights-receive-alert-notifications.md)
- [Exporter des métriques de stockage, concentrateur d’événements ou Analytique de journal](./monitoring-overview-of-diagnostic-logs.md)
