<properties 
   pageTitle="Affichage des journaux de diagnostics pour la banque de LAC de données Azure | Microsoft Azure" 
   description="Comprendre comment configurer et accéder aux journaux de diagnostics pour la banque de LAC de données Azure " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Accès aux journaux de diagnostics pour la banque de LAC de données Azure

Découvrez comment activer l’enregistrement des diagnostics pour votre compte de banque de données LAC et comment afficher les journaux de collecte pour votre compte.

Organisations peuvent activer l’enregistrement des diagnostics pour leur compte Azure Data Store de LAC collecter les traces d’audit de données access qui fournit des informations telles que la liste des utilisateurs d’accéder aux données, la fréquence à laquelle les données sont accessibles, la quantité de données est stockée dans le compte, etc..

## <a name="prerequisites"></a>Conditions préalables

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Compte de banque de LAC de données azure**. Suivez les instructions de la section [mise en route de magasin de LAC de données Azure via le portail d’Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Activer l’enregistrement des diagnostics pour votre compte de banque de données lac

1. Ouvrez une session sur le nouveau [Portail Azure](https://portal.azure.com).

2. Ouvrez votre compte de banque de données LAC et à partir de la blade de compte de banque de données lac, cliquez sur **paramètres**, puis cliquez sur **Paramètres de Diagnostic**.

3. De la lame de **Diagnostic** , apportez les modifications suivantes pour configurer l’enregistrement des Diagnostics.

    ![Activer l’enregistrement des diagnostics] (./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Activer les journaux de diagnostic")

    * Définir le **statut** **sur** pour activer la journalisation des Diagnostics.
    * Vous pouvez choisir de banque/traiter les données de deux façons différentes.
        * Sélectionnez l’option **d’exportation pour concentrateur d’événements** aux données de journal de flux à un concentrateur d’événements Azure. Vous utiliserez probablement cette option si vous disposez d’un pipeline de traitement en aval pour analyser les journaux entrants en temps réel. Si vous sélectionnez cette option, vous devez fournir les détails pour le concentrateur d’événements Azure vous souhaitez utiliser.
        * Sélectionnez l’option **d’exportation pour le compte de stockage** pour stocker des journaux à un compte de stockage Azure. Vous utilisez cette option si vous souhaitez archiver les données qui seront traitées par lots à une date ultérieure. Si vous sélectionnez cette option, vous devez fournir un compte de stockage Azure pour enregistrer les journaux.
    * Spécifiez si vous souhaitez obtenir les journaux d’audit ou les journaux de requêtes ou les deux.
    * Spécifier le nombre de jours pour lesquels les données doivent être conservées.
    * Cliquez sur **Enregistrer**.

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez surveiller les journaux dans l’onglet **Journaux de Diagnostic** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Afficher les journaux de diagnostic pour votre compte de banque de données lac

Il existe deux manières d’afficher les données du journal de votre compte de banque de données lac.

* Afficher les paramètres du compte de la banque de données lac
* À partir du compte de stockage Azure où les données sont stockées.

### <a name="using-the-data-lake-store-settings-view"></a>À l’aide de l’affichage des paramètres de banque de données lac

1. À partir de la blade de **paramètres** de compte de magasin lac de données, cliquez sur **Journaux de Diagnostic**.

    ![Enregistrement du mode diagnostic] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Afficher les journaux de diagnostic") 

2. De la lame de **Journaux de Diagnostic** , vous devez voir les journaux classés par les **Journaux d’Audit** et de **Journaux de requêtes**.
    * Les journaux de demandes de capture chaque demande d’API effectué sur le compte de la banque de données lac.
    * Journaux d’audit sont similaires aux journaux de demandes, mais fournissent une ventilation plus détaillée des opérations en cours d’exécution sur le compte de la banque de données lac. Par exemple, un appel d’API de téléchargement unique dans les journaux de demandes peut entraîner dans plusieurs opérations « Ajouter » dans les journaux d’audit.

3. Cliquez sur le lien **Télécharger** par rapport à chaque entrée de journal pour les journaux de télécharger.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>À partir du compte de stockage Azure qui contient les données du journal

1. Ouvrez la lame du compte de stockage Azure associée avec le magasin de données LAC pour la journalisation, puis cliquez sur objets BLOB. La lame de **service d’objet Blob** répertorie deux conteneurs.

    ![Enregistrement du mode diagnostic] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Afficher les journaux de diagnostic")

    * conteneur **d’idées-journaux-audit** contient les journaux d’audit.
    * Les **perspectives-journaux-demandes** du conteneur contient les journaux de la demande.

2. Ces conteneurs, les journaux sont stockés dans la structure suivante.

    ![Enregistrement du mode diagnostic] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Afficher les journaux de diagnostic")

    Par exemple, le chemin d’accès complet à un journal d’audit peut être`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Similary, le chemin d’accès complet à un journal des demandes peut être`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Comprendre la structure des données du journal

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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName   | Chaîne | Nom de l’opération qui a ouvert une session. Par exemple, getfilestatus.              |
| resultType      | Chaîne | L’état de l’opération, par exemple, 200.                                 |
| callerIpAddress | Chaîne | L’adresse IP du client qui effectue la demande.                                |
| ID de corrélation   | Chaîne | L’id du journal pouvant être utilisé pour regrouper un ensemble d’entrées de journal associées |
| identité        | Objet | L’identité qui a généré le journal.                                            |
| propriétés      | JSON   | Voir ci-dessous pour plus de détails                                                          |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName   | Chaîne | Nom de l’opération qui a ouvert une session. Par exemple, getfilestatus.              |
| resultType      | Chaîne | L’état de l’opération, par exemple, 200.                                 |
| ID de corrélation   | Chaîne | L’id du journal pouvant être utilisé pour regrouper un ensemble d’entrées de journal associées |
| identité        | Objet | L’identité qui a généré le journal.                                            |
| propriétés      | JSON   | Voir ci-dessous pour plus de détails                                                          |

#### <a name="audit-log-properties-schema"></a>Schéma de propriétés de journal d’audit

| Nom       | Type de   | Description                              |
|------------|--------|------------------------------------------|
| StreamName | Chaîne | Le chemin d’accès de l’opération a été effectuée sur  |


## <a name="samples-to-process-the-log-data"></a>Exemples pour traiter les données du journal

Magasin de LAC de données Azure fournit un exemple sur la manière de traiter et analyser les données du journal. Vous trouverez l’exemple à [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure lac de magasin de données](data-lake-store-overview.md)
- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)

