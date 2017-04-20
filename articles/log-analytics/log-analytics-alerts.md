<properties 
   pageTitle="Les alertes dans le journal Analytique | Microsoft Azure"
   description="Alertes dans le journal Analytique identifier des informations importantes dans votre référentiel de l’OMS et peuvent proactive vous informent des problèmes ou appellent des actions pour tenter de les corriger.  Cet article explique comment créer une règle d’alerte et les détails des différentes actions qu’ils peuvent prendre."
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
   ms.date="08/22/2016"
   ms.author="bwren" />

# <a name="alerts-in-log-analytics"></a>Alertes dans le journal Analytique

Les alertes dans le journal Analytique identifient des informations importantes dans votre référentiel de l’OMS.  Règles d’alerte automatiquement exécutent selon une planification des recherches de journaux et de créer un enregistrement d’alerte si les résultats correspondent à des critères particuliers.  La règle exécute alors automatiquement une ou plusieurs actions pour vous avertir de l’alerte proactive ou d’appeler un autre processus.   

![Alertes d’Analytique de journal](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Création d’une règle d’alerte
Pour créer une règle d’alerte, commencez par créer un journal pour les enregistrements qui doit appeler l’alerte.  Le bouton **alerte** sera ensuite disponible pour vous permettre de créer et de configurer la règle d’alerte.

1.  Dans la page Vue d’ensemble de l’OMS, cliquez sur **Recherche**.
2.  Créez une nouvelle requête de recherche de journal ou sélectionnez une recherche enregistrée. 
3.  Cliquez sur l' **alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte** .
4. Reportez-vous aux tableaux ci-dessous pour plus d’informations sur les options de configuration de l’alerte.
5. Lorsque vous fournissez la durée de validité de la règle d’alerte, le nombre des enregistrements qui correspondent aux critères de recherche pour cette fenêtre s’affiche.  Cela peut vous aider à déterminer la fréquence à laquelle vous donnera le nombre de résultats que vous attendez.
4.  Cliquez sur **Enregistrer** pour terminer la règle d’alerte.  Il commence à s’exécuter immédiatement.


![Ajouter une règle d’alerte](media/log-analytics-alerts/add-alert-rule.png)

| Propriété | Description |
|:--|:--|
| **Informations d’alerte** | |
| Nom |  Nom unique pour identifier la règle d’alerte. |
| Gravité | Gravité de l’alerte créée par cette règle. |
| Requête de recherche | Sélectionnez **utiliser la recherche en cours** pour utiliser la requête en cours ou sélectionnez une recherche enregistrée existante dans la liste.  La syntaxe de requête est fournie dans la zone de texte où vous pouvez les modifier si nécessaire.  |
| Fenêtre de temps | Spécifie la plage de temps pour la requête.  La requête renvoie uniquement les enregistrements qui ont été créés dans cette plage de l’heure actuelle.  Cela peut être toute valeur comprise entre 5 minutes et 24 heures.  Elle doit être supérieure ou égale à la fréquence de l’alerte.  <br><br> Par exemple, si la fenêtre de temps est définie sur 60 minutes et l’exécution de la requête à 13:15 h 00, uniquement les enregistrements créés entre 12 h 15 et 1:15 PM apparaîtront. |
| **Planification** |     
| Seuil | Critères de création d’une alerte.  Une alerte est créée si le nombre d’enregistrements renvoyés par la requête correspond à ce critère. |
| Fréquence de l’alerte | Spécifie la fréquence à laquelle la requête doit être exécutée.  Peut être toute valeur comprise entre 5 minutes et 24 heures.  Doit être égale ou inférieure à la durée de validité. |
| Supprimer les alertes | Lorsque vous activez la suppression de la règle d’alerte, les actions de la règle sont désactivées pour une durée définie après la création d’une nouvelle alerte.  La règle est toujours en cours d’exécution et créera des enregistrements d’alerte si les critères sont remplis.  C’est pour permettre à que temps pour corriger le problème sans exécuter des actions en double. |
| **Actions** | |
| Notification par courrier électronique | Spécifiez **Oui** si vous souhaitez qu’un e-mail soit envoyé lorsque l’alerte est déclenchée. |
| Objet    | L’objet de l’e-mail.  Vous ne pouvez pas modifier le corps du message. |
| Destinataires | Adresses de tous les destinataires de courrier électronique.  Si vous spécifiez plus d’une adresse, séparez les adresses par un point-virgule ( ;). |
| Webhook | Spécifiez **Oui** si vous souhaitez appeler une webhook lorsque l’alerte est déclenchée. |
| URL de Webhook | L’URL de la webhook. |
| Inclure la charge utile JSON personnalisé | Sélectionnez cette option si vous souhaitez remplacer la charge utile par défaut avec une charge personnalisée. |
| Entrez votre charge JSON personnalisé | La charge personnalisée pour la webhook.  Reportez-vous à la section précédente pour plus de détails. |
| Procédure opérationnelle | Spécifiez **Oui** si vous souhaitez démarrer une procédure opérationnelle d’Azure Automation lorsque l’alerte est déclenchée. |
| Sélectionnez une procédure opérationnelle | Sélectionnez la procédure opérationnelle pour démarrer à partir de la procédures opérationnelles dans le compte de l’automation configuré dans votre solution d’automatisation. |
| Exécuter sur | Sélectionnez **Azure** pour exécuter la procédure opérationnelle dans le nuage Azure.  Permet de sélectionner le **Travailleur d’hybride** pour exécuter la procédure opérationnelle sur un [Travailleur de procédure opérationnelle hybride](..\automation\automation-hybrid-runbook-worker.md) dans votre environnement local. |


## <a name="manage-alert-rules"></a>Gérer les règles d’alerte
Vous pouvez obtenir une liste de toutes les règles d’alerte dans le menu **alertes** dans le journal Analytique **paramètres**.  

![Gérer les alertes](./media/log-analytics-alerts/configure.png)

1. Dans la console de l’OMS, sélectionnez la fenêtre de **paramètres** .
2. Sélectionnez **alertes**.

Vous pouvez effectuer plusieurs actions à partir de cette vue.

- Désactiver une règle en sélectionnant **désactiver** en regard de celui-ci.
- Modifier une règle d’alerte en cliquant sur l’icône de crayon.
- Supprimer une règle d’alerte en cliquant sur l’icône **X** . 


## <a name="setting-time-windows"></a>Définition de périodes 

### <a name="event-alerts"></a>Alertes d’événements

Sources de données telles que les journaux des événements Windows, Syslog, incluent des événements et journaux personnalisés.  Vous souhaiterez peut-être créer une alerte lorsqu’un événement d’erreur particulier est créé, ou lorsque plusieurs événements d’erreurs sont créés au sein d’une fenêtre de temps spécifique.

Pour signaler un événement, la valeur du nombre de résultats supérieur à 0 et la fréquence et la durée de validité de 5 minutes.  Qui exécutera la requête toutes les 5 minutes et vérifiez l’occurrence d’un événement unique qui a été créé depuis la dernière exécution de la requête.  Une fréquence plus longue peut retarder le temps entre les événements collectés et l’alerte en cours de création.

Certaines applications peuvent enregistrer une erreur occasionnelle ne doit pas nécessairement de déclencher une alerte.  Par exemple, l’application peut recommencer le processus qui a créé l’événement d’erreur et puis réussir la prochaine fois.  Dans ce cas, il pouvez que vous ne souhaitez pas créer l’alerte à moins que plusieurs événements sont créés au sein d’une fenêtre de temps spécifique.  

Dans certains cas, vous souhaiterez créer une alerte en l’absence d’un événement.  Par exemple, un processus peut enregistrer les événements normaux pour indiquer qu’il fonctionne correctement.  Si elle ne connecte pas un de ces événements dans un laps de temps donné, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil *inférieur à*1.

### <a name="performance-alerts"></a>Alertes de performances

[Les données de performance](log-analytics-data-sources-performance-counters.md) sont stockées sous forme d’enregistrements dans le référentiel d’OMS semblable aux événements.  La valeur de chaque enregistrement est la moyenne mesurée au cours des 30 dernières minutes.  Si vous souhaitez avertir lorsqu’un compteur de performance dépasse un seuil particulier, ce seuil doit être inclus dans la requête.

Par exemple, si vous souhaitez avertir lorsque le processeur s’exécute plus de 90 % pendant 30 minutes, vous devez utiliser une requête comme *Type = Perf ObjectName = processeur CounterName = « % temps processeur » contre-valeurs > 90* et le seuil de la règle d’alerte *supérieur à 0*.  

 Étant donné que les [fiches de performance](log-analytics-data-sources-performance-counters.md) sont regroupés indépendamment de la fréquence que vous collectez chaque compteur toutes les 30 minutes, une fenêtre de temps inférieure à 30 minutes ne peut renvoyer à aucun enregistrement.  Définition de la fenêtre de temps de 30 minutes vous garantit qu’un seul enregistrement pour chaque source connecté qui représente la moyenne par rapport à ce moment.

## <a name="alert-actions"></a>Actions d’alerte

En plus de créer un enregistrement d’alerte, vous pouvez configurer la règle d’alerte pour exécuter automatiquement une ou plusieurs actions.  Actions peuvent vous avertir de l’alerte de façon proactive ou appeler un processus qui essaie de corriger le problème a été détecté.  Les sections suivantes décrivent les actions qui sont actuellement disponibles.

### <a name="email-actions"></a>Actions de messagerie
Les actions de messagerie électronique Envoyer un message électronique avec les détails de l’alerte à un ou plusieurs destinataires.  Vous pouvez spécifier l’objet du message, mais son contenu est un format standard construit par journal Analytique.  Il inclut des informations récapitulatives telles que le nom de l’alerte en plus des dix enregistrements renvoyés par la recherche du journal.  Il inclut également un lien vers une recherche de journaux dans journal Analytique qui retournera l’ensemble des enregistrements à partir de cette requête.   L’expéditeur du message est *Microsoft Operations Management Suite équipe &lt; noreply@oms.microsoft.com *. 


### <a name="webhook-actions"></a>Actions de Webhook

Actions de Webhook permettent d’appeler un processus externe via une demande HTTP POST unique.  Le service actuellement appelé doit prendre en charge de webhooks et déterminer comment il utilisera une charge utile qu’il reçoit.  Vous pouvez également appeler une API REST qui ne prennent en charge spécifiquement webhooks tant que la demande est dans un format qui comprend de l’API.  Exemples d’utilisation d’un webhook en réponse à une alerte utilisent un service comme [marge](http://slack.com) pour envoyer un message avec les détails de l’alerte ou de la création d’un incident dans un service tel que [PagerDuty](http://pagerduty.com/).  

Une procédure complète de création d’une règle d’alerte avec un webhook pour appeler un exemple de service est disponible à l’adresse [Webhooks dans les alertes de journal Analytique](log-analytics-alerts-webhooks.md).

Webhooks inclure une URL et une charge utile au format JSON qui correspond aux données envoyées au service externe.  Par défaut, la charge utile inclut les valeurs dans le tableau suivant.  Vous pouvez choisir de remplacer cette charge utile avec un personnalisé de votre choix.  Dans ce cas vous pouvez utiliser les variables dans le tableau pour chacun des paramètres à inclure leur valeur dans votre charge personnalisée.


| Paramètre | Variable | Description |
|:--|:--|:--|
| AlertRuleName | #alertrulename | Nom de la règle d’alerte. |
| AlertThresholdOperator | #thresholdoperator | Opérateur de seuil pour la règle d’alerte.  *Supérieur à* ou *inférieur à*. |
| AlertThresholdValue | #thresholdvalue | Valeur seuil pour la règle d’alerte. |
| LinkToSearchResults | #linktosearchresults | Créer un lien vers la recherche de journal de journal Analytique qui renvoie les enregistrements de la requête qui a créé l’alerte. |
| ResultCount  | #searchresultcount | Nombre d’enregistrements dans les résultats de recherche. |
| SearchIntervalEndtimeUtc  | #searchintervalendtimeutc | Heure de fin de la requête au format UTC. |
| SearchIntervalInSeconds | #searchinterval | Fenêtre de temps pour la règle d’alerte. |
| SearchIntervalStartTimeUtc  | #searchintervalstarttimeutc | Heure de début de la requête au format UTC. |
| SearchQuery | #SearchQuery | Requête de recherche de journal utilisé par la règle d’alerte. |
| SearchResults | Consultez la section ci-dessous | Enregistrements renvoyés par la requête au format JSON.  Limité aux 5 000 premiers enregistrements. |
| WorkspaceID | #workspaceid | ID de votre espace de travail de l’OMS. |


Par exemple, vous pouvez spécifier la charge personnalisée suivante qui inclut un paramètre unique appelé *texte*.  Le service pour que cette webhook appelle devrait être attendu ce paramètre.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Cette charge utile d’exemple se résoudra en quelque chose comme ce qui suit lors de l’envoi à le webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Pour inclure les résultats de la recherche dans une charge personnalisée, ajoutez la ligne suivante sous la forme d’une propriété de niveau supérieur dans la charge utile json.  

    "IncludeSearchResults":true

Par exemple, pour créer une charge personnalisée qui inclut uniquement le nom de l’alerte et les résultats de recherche, vous pouvez utiliser les éléments suivants. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Vous pouvez étudier un exemple complet de création d’une règle d’alerte avec un webhook pour démarrer un service externe au [journal Analytique webhook exemple d’alerte](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Actions de procédure opérationnelle

Actions de procédure opérationnelle démarrer une procédure opérationnelle dans Azure Automation.  Pour utiliser ce type d’action, vous devez disposer de la [solution d’automatisation](log-analytics-add-solutions.md) installés et configurés dans votre espace de travail de l’OMS.  Si vous ne l’avez pas installé lorsque vous créez une nouvelle règle d’alerte, un lien vers son installation s’affiche.  Vous pouvez sélectionner les procédures opérationnelles dans le compte d’automation que vous avez configuré dans la solution d’automatisation.

Actions de procédure opérationnelle de démarrer la procédure opérationnelle à l’aide d’un [webhook](../automation/automation-webhooks.md).  Lorsque vous créez la règle d’alerte, il crée automatiquement une nouvelle webhook pour la procédure opérationnelle avec le nom de **Conversion d’alerte OMS** suivi d’un GUID.  

Vous ne pouvez pas remplir directement de tous les paramètres de la procédure opérationnelle, mais le [paramètre de $WebhookData](../automation/automation-webhooks.md) inclut les détails de l’alerte, y compris les résultats de la recherche du journal qui l’a créé.  La procédure opérationnelle devez définir **$WebhookData** comme un paramètre pour qu’il puisse accéder aux propriétés de l’alerte.  Les données d’alerte sont disponibles au format json dans une propriété unique appelée **SearchResults** dans la propriété **RequestBody** de le **$WebhookData**.  Cela aura avec les propriétés dans le tableau suivant.


| Nœud | Description |
|:--|:--|
| ID         | Chemin d’accès et le GUID de la recherche. |
| __metadata | Informations sur l’alerte, y compris le nombre d’enregistrements et l’état des résultats de recherche. |
|  valeur     |  Entrée distincte pour chaque enregistrement dans les résultats de recherche.  Les détails de l’entrée correspondra les propriétés et les valeurs de l’enregistrement.   |

Par exemple, la procédure opérationnelle suivant serait extrait les enregistrements renvoyés par la recherche du journal et attribuer des propriétés différentes selon le type de chaque enregistrement.  Notez que la procédure opérationnelle commence par convertir **RequestBody** à partir de json afin qu’il peut être manipulé en tant qu’objet dans PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value
    
    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer
        
        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }
        
        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Enregistrements d’alerte

Enregistrements d’alerte créées par les règles d’alerte dans le journal Analytique ont un **Type** **d’alerte** et un **SourceSystem** de **l’OMS**.  Ils ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Type de          | *Alerte* |
| SourceSystem  | *OMS* |
| AlertSeverity | Niveau de gravité de l’alerte. |
| Nom_alerte     | Nom de l’alerte. |
| Requête         | Texte de la requête a été exécutée.  |
| QueryExecutionEndTime   | Fin de la plage de temps pour la requête. |
| QueryExecutionStartTime | Début de la plage de temps pour la requête.  |
| TimeGenerated | Date et heure de que création de l’alerte. |

Il existe d’autres types d’enregistrements d’alerte créés par la [solution de gestion d’alerte](log-analytics-solution-alert-management.md) et [d’alimentation des exportations](log-analytics-powerbi.md).  Ils ont tous un **Type** **d’alerte** mais sont distinguent par leur **SourceSystem**.




## <a name="next-steps"></a>Étapes suivantes

- Installer la [solution de gestion des alertes](log-analytics-solution-alert-management.md) pour analyser les alertes créées dans le journal Analytique ainsi que des alertes collectées à partir de System Center Operations Manager (SCOM).
- Apprenez-en plus sur les [recherches de journaux](log-analytics-log-searches.md) qui peut générer des alertes.
- Effectuez une procédure pas à pas pour la [configuration d’un webook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
- Découvrez comment rédiger des [procédures opérationnelles dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par les alertes.