<properties 
   pageTitle="Affichage des journaux de diagnostics pour Azure données lac Analytique | Microsoft Azure" 
   description="Comprendre comment configurer et accéder aux journaux de diagnostic pour l’analytique du lac de données Azure " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/11/2016"
   ms.author="larryfr"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>L’accès aux journaux de diagnostic pour Azure données lac Analytique

Découvrez comment activer l’enregistrement des diagnostics pour votre compte Analytique lac de données et comment afficher les journaux de collecte pour votre compte.

Organisations peuvent activer l’enregistrement des diagnostics pour leur compte Azure données lac Analytique collecter les données des accès aux pistes d’audit. Ces journaux fournit des informations telles que :

* Une liste d’utilisateurs ayant accédé aux données.
* La fréquence à laquelle les données sont accessibles.
* La quantité de données est stockée dans le compte.

## <a name="prerequisites"></a>Conditions préalables

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Activer votre abonnement Azure** pour données lac Analytique Public Preview. Consultez les [instructions](data-lake-analytics-get-started-portal.md#signup).
- **Compte d’azure données lac Analytique**. Suivez les instructions à la [mise en route d’Analytique de LAC de données Azure via le portail Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Activer la journalisation

1. Ouvrez une session sur le nouveau [portail Azure](https://portal.azure.com).

2. Ouvrez votre compte Analytique lac de données et à partir de la blade de compte Analytique lac de données, cliquez sur **paramètres**, puis cliquez sur **Paramètres de Diagnostic**.

3. De la lame de **Diagnostic** , apportez les modifications suivantes pour configurer l’enregistrement des Diagnostics.

    ![Activer l’enregistrement des diagnostics] (./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Activer les journaux de diagnostic")

    * Définir le **statut** **sur** pour activer la journalisation des Diagnostics.
    * Vous pouvez choisir de banque/traiter les données de deux façons différentes.
        * Pour répartir des données de journal à un concentrateur d’événements Azure, sélectionnez **Exporter vers le concentrateur d’événements** . Utilisez cette option si vous disposez d’un pipeline de traitement en aval pour analyser les journaux entrants en temps réel. Si vous sélectionnez cette option, vous devez fournir les détails pour le concentrateur d’événements Azure vous souhaitez utiliser.
        * Sélectionnez **Exporter vers le compte de stockage** pour stocker des journaux à un compte de stockage Azure. Utilisez cette option si vous souhaitez archiver les données. Si vous sélectionnez cette option, vous devez fournir un compte de stockage Azure pour enregistrer les journaux.
    * Spécifiez si vous souhaitez obtenir les journaux d’audit ou les journaux de requêtes ou les deux.
    * Spécifier le nombre de jours pour lesquels les données doivent être conservées.
    * Cliquez sur **Enregistrer**.

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez surveiller les journaux dans l’onglet **Journaux de Diagnostic** .

## <a name="view-logs"></a>Afficher les journaux

Il existe deux manières d’afficher les données du journal de votre compte de données lac Analytique.

* Dans les paramètres du compte Analytique lac de données
* À partir du compte de stockage Azure où les données sont stockées.

### <a name="using-the-data-lake-analytics-settings-view"></a>À l’aide de l’affichage des paramètres de données lac Analytique

1. À partir de la blade de **paramètres** de compte de données lac Analytique, cliquez sur **Journaux de Diagnostic**.

    ![Enregistrement du mode diagnostic] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Afficher les journaux de diagnostic") 

2. De la lame de **Journaux de Diagnostic** , vous devez voir les journaux classés par les **Journaux d’Audit** et de **Journaux de requêtes**.
    * Les journaux de demandes de capture chaque demande d’API effectuée dans le compte de données lac Analytique.
    * Journaux d’audit sont similaires aux journaux de demandes, mais fournissent une ventilation plus détaillée des opérations en cours d’exécution sur le compte de données lac Analytique. Par exemple, un appel d’API de téléchargement unique dans les journaux de demandes peut entraîner dans plusieurs opérations « Ajouter » dans les journaux d’audit.

3. Cliquez sur le lien de **téléchargement** pour une entrée de journal pour les journaux de télécharger.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>À partir du compte de stockage Azure qui contient les données du journal

1. Ouvrez la lame de compte de stockage Azure associée données lac Analytique pour l’enregistrement, puis cliquez sur objets BLOB. La lame de **service d’objet Blob** répertorie deux conteneurs.

    ![Enregistrement du mode diagnostic] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Afficher les journaux de diagnostic")

    * conteneur **d’idées-journaux-audit** contient les journaux d’audit.
    * Les **perspectives-journaux-demandes** du conteneur contient les journaux de la demande.

2. Ces conteneurs, les journaux sont stockés dans la structure suivante.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] Le `##` entrées dans le chemin d’accès contient l’année, mois, jour et heure dans laquelle le journal a été créé. Données lac Analytique crée un seul fichier toutes les heures, donc `m=` contient toujours une valeur de `00`.

    Par exemple, le chemin d’accès complet à un journal d’audit peut être :
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    De même, le chemin d’accès complet à un journal des demandes peut être :
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Structure d’un journal

Les journaux d’audit et de la demande sont au format JSON. Dans cette section, nous voir la structure de JSON pour la demande et des journaux d’audit.

### <a name="request-logs"></a>Journaux de demandes

Voici un exemple d’entrée dans le journal des demandes d’au format JSON. Chaque objet blob a un objet racine sont appelé **enregistrements** qui contient un tableau d’objets du journal.

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schéma des journaux de demande

| Nom            | Type de   | Description                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| heure            | Chaîne | L’horodatage (en UTC) du journal                                              |
| resourceId      | Chaîne | L’ID de la ressource à laquelle l’opération de se placer sur                            |
| catégorie        | Chaîne | La catégorie de journal. Par exemple, **les demandes**.                                   |
| operationName   | Chaîne | Nom de l’opération qui a ouvert une session. Par exemple, GetAggregatedJobHistory.              |
| resultType      | Chaîne | L’état de l’opération, par exemple, 200.                                 |
| callerIpAddress | Chaîne | L’adresse IP du client qui effectue la demande.                                |
| ID de corrélation   | Chaîne | Id du journal. Cette valeur peut être utilisée pour regrouper un ensemble d’entrées de journal associés |
| identité        | Objet | L’identité qui a généré le journal.                                            |
| propriétés      | JSON   | Consultez la section suivante (schéma de propriétés d’un journal de demande), pour plus de détails |

#### <a name="request-log-properties-schema"></a>Schéma de propriétés de journal de requête

| Nom                 | Type de   | Description                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | Chaîne | La méthode HTTP utilisée pour l’opération. Par exemple, obtenir. |
| Chemin d’accès                 | Chaîne | Le chemin d’accès de l’opération a été effectuée sur                   |
| RequestContentLength | int    | La longueur du contenu de la demande HTTP                    |
| ClientRequestId      | Chaîne | L’Id qui identifie de manière unique cette demande              |
| Heure de début            | Chaîne | L’heure à laquelle le serveur a reçu la demande         |
| Heure de fin              | Chaîne | L’heure à laquelle le serveur a envoyé une réponse              |

### <a name="audit-logs"></a>Journaux d’audit

Voici un exemple d’entrée dans le journal d’audit d’au format JSON. Chaque objet blob a un objet racine sont appelé **enregistrements** qui contient un tableau d’objets de journal

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schéma des journaux d’audit

| Nom            | Type de   | Description                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| heure            | Chaîne | L’horodatage (en UTC) du journal                                              |
| resourceId      | Chaîne | L’ID de la ressource à laquelle l’opération de se placer sur                            |
| catégorie        | Chaîne | La catégorie de journal. Par exemple, il s’agit **d’Audit**.                                      |
| operationName   | Chaîne | Nom de l’opération qui a ouvert une session. Par exemple, JobSubmitted.              |
| resultType | Chaîne | Un sous-état de l’état du travail (NomOpération). |
| resultSignature | Chaîne | Détails supplémentaires sur l’état du travail (NomOpération). |
| identité      | Chaîne | L’utilisateur qui a demandé l’opération. Par exemple, susan@contoso.com.                                 |
| propriétés      | JSON   | Consultez la section suivante (schéma de propriétés de journal d’Audit) pour plus de détails |

> [AZURE.NOTE] __resultType__ __resultSignature__ fournissent des informations sur le résultat d’une opération et contient une valeur si une opération est terminée. Par exemple, qui contiennent une valeur lorsque __NomOpération__ contient la valeur __JobStarted__ ou __JobEnded__.

#### <a name="audit-log-properties-schema"></a>Schéma de propriétés de journal d’audit

| Nom       | Type de   | Description                              |
|------------|--------|------------------------------------------|
| ID de travail | Chaîne | L’ID affecté à la tâche  |
| JobName | Chaîne | Le nom qui a été fourni pour le travail |
| JobRunTime | Chaîne | Le runtime permet de traiter le travail |
| SubmitTime | Chaîne | L’heure (UTC) que la tâche a été soumise |
| Heure de début | Chaîne | Le démarrage du travail en cours d’exécution après la présentation (en heure UTC). |
| Heure de fin | Chaîne | Heure de fin de la tâche. |
| Parallélisme | Chaîne | Le nombre d’unités de données lac Analytique demandée pour cette tâche lors de l’envoi. |

> [AZURE.NOTE] __SubmitTime__, __heure de début__, __heure de fin__ et le __parallélisme__ fournissent des informations sur une opération et contient une valeur si une opération a commencé ou terminé. Par exemple, __SubmitTime__ contient une valeur après __NomOpération__ indique __JobSubmitted__.

## <a name="process-the-log-data"></a>Traiter les données du journal

Analytique de LAC données Azure fournit un exemple sur la manière de traiter et analyser les données du journal. Vous trouverez l’exemple à [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de données Azure lac Analytique](data-lake-analytics-overview.md)


