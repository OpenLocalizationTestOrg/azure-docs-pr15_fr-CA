<properties
   pageTitle="IIS consigne dans le journal Analytique | Microsoft Azure"
   description="Internet Information Services (IIS) enregistre l’activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par le journal Analytique.  Cet article décrit comment configurer la collection des journaux IIS et les détails des enregistrements qu’ils créent dans le référentiel de l’OMS."
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

# <a name="iis-logs-in-log-analytics"></a>IIS consigne dans le journal Analytique
Internet Information Services (IIS) enregistre l’activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par le journal Analytique.  

![Journaux IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Journaux de configuration d’IIS
Analytique de journal collecte les entrées des fichiers journaux créés par IIS, afin que vous devez [configurer IIS pour l’enregistrement](https://technet.microsoft.com/library/hh831775.aspx).

Journal Analytique seulement prend en charge les fichiers journaux IIS au format du W3C et ne gère pas les champs personnalisés ou IIS Advanced Logging.  
Analytique de journal ne collecte pas les journaux au format natif de NCSA ou IIS.

Configurer les journaux IIS dans Analytique de journal dans le [menu données de paramètres de journal Analytique](log-analytics-data-sources.md#configuring-data-sources).  Il existe aucune configuration nécessaire autre que **le format W3C de collecte IIS des fichiers journaux**.

Il est recommandé que lorsque vous activez la collection des journaux IIS, vous devez configurer le paramètre de substitution du journal IIS sur chaque serveur.


## <a name="data-collection"></a>Collecte de données

Analytique de journal collecte les entrées du journal IIS à partir de chaque source connecté environ toutes les 15 minutes.  L’agent enregistre sa place dans chaque journal des événements qu’il collecte à partir de.  Si l’agent est mis hors connexion, puis Analytique de journal collecte les événements à partir de l’endroit où il s’était arrêté, même si ces événements ont été créés alors que l’agent était hors ligne.


## <a name="iis-log-record-properties"></a>Propriétés de l’enregistrement journal IIS

Les enregistrements de journal IIS de type **W3CIISLog** et ont les propriétés dans le tableau suivant :

| Propriété | Description |
|:--|:--|
| Ordinateur | Nom de l’ordinateur sur lequel l’événement a été prélevé. |
| virement bancaire | Adresse IP du client. |
| csMethod | Méthode de la demande de GET ou POST. |
| csReferer | Site que l’utilisateur suivi un lien à partir du site actif. |
| csUserAgent | Type de navigateur du client. |
| csUserName | Nom de l’utilisateur authentifié ayant accédé au serveur. Les utilisateurs anonymes sont indiquées par un trait d’union. |
| csUriStem | Cible de la requête telle qu’une page web. |
| csUriQuery | La requête, le cas échéant, que le client tentait d’effectuer. |
| ManagementGroupName | Nom du groupe de gestion pour les agents d’Operations Manager.  Pour les autres agents, il s’agit d’AOI -\<ID de l’espace de travail\> |
| RemoteIPCountry | Pays de l’adresse IP du client. |
| RemoteIPLatitude | Latitude de l’adresse IP du client. |
| RemoteIPLongitude | Longitude de l’adresse IP du client. |
| scStatus | Code d’état HTTP. |
| scSubStatus | Code d’erreur de sous-état. |
| scWin32Status | Code d’état Windows. |
| sIP | Adresse IP du serveur web. |
| SourceSystem  | Operations Manager |
| sPort | Port sur le serveur, le client est connecté à. |
| sSiteName | Nom du site IIS. |
| TimeGenerated | Date et heure de que l’entrée a été enregistrée. |
| TimeTaken | Intervalle de temps pour traiter la demande en millisecondes. |

## <a name="log-searches-with-iis-logs"></a>Recherches de journaux avec les journaux IIS

Le tableau suivant fournit des exemples de requêtes de journaux qui extrait des enregistrements de journal IIS.

| Requête | Description |
|:--|:--|
| Type = IISLog | Tous les enregistrements de journal IIS. |
| Type = IISLog EventLevelName = erreur | Tous les événements de Windows avec la gravité de l’erreur. |
| Type = W3CIISLog & #124 ; Count() mesure par virement bancaire | Entrées de journal de nombre de IIS par l’adresse IP du client. |
| Type = W3CIISLog csHost = « www.contoso.com » & #124 ; Count() mesure par csUriStem | Nombre des services IIS, entrées de journal par URL pour l’hôte www.contoso.com. |
| Type = W3CIISLog & #124 ; Sum(csBytes) de mesure par ordinateur & #124 ; haut 500000| Nombre total d’octets reçu par chaque ordinateur IIS. |

## <a name="next-steps"></a>Étapes suivantes

- Configuration Analytique de journal pour collecter d’autres [sources de données](log-analytics-data-sources.md) pour l’analyse.
- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions.
- Configurer des alertes dans le journal des Analytique pour informer des conditions importantes trouvées dans les journaux IIS.
