<properties
    pageTitle="Azure solution clé coffre-fort dans journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la solution Azure clé coffre-fort dans journal Analytique de consulter les journaux de la chambre forte de clé Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="richrund"/>

# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Solution de chambre forte de la clé (aperçu) Azure dans journal Analytique

>[AZURE.NOTE] Il s’agit d’une [solution d’aperçu](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Vous pouvez utiliser la solution Azure clé coffre-fort dans journal Analytique de consulter les journaux d’Azure clé coffre-fort AuditEvent.

Vous pouvez activer l’enregistrement des événements d’audit pour Azure clé coffre-fort. Ces journaux sont écrits dans le stockage Blob d’Azure où ils peuvent ensuite être indexées par Analytique du journal pour la recherche et l’analyse.

## <a name="install-and-configure-the-solution"></a>Installation et configuration de la solution

Suivez les instructions ci-dessous pour installer et configurer la solution Azure clé coffre-fort :

1.  Activer [l’enregistrement des diagnostics pour le coffre-fort de la clé de](../key-vault/key-vault-logging.md) ressources que vous souhaitez surveiller
2.  Configuration Analytique de journal pour lire les journaux depuis le stockage blob à l’aide du processus décrit dans les [fichiers JSON dans le stockage blob](../log-analytics/log-analytics-azure-storage-json.md).
3.  Activer la solution Azure clé coffre-fort à l’aide du processus décrit dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Examinez les détails de collection de données en chambre forte de la clé Azure

Azure solution de stockage en chambre forte de clé collecte des journaux de diagnostics depuis le stockage blob Azure pour Azure clé coffre-fort.
Aucun agent n’est requis pour la collecte des données.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour Azure clé coffre-fort.

| Plate-forme | Agent direct | Agent de Systems Center Operations Manager (SCOM) | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | Fréquence de collection |
|---|---|---|---|---|---|---|
|Azure|![N°](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![N°](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Oui](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![N°](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![N°](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10 minutes|

## <a name="use-azure-key-vault"></a>Utilisez le coffre-fort de clé Azure

Après l’installation de la solution, vous pouvez consulter le résumé d’états dans le temps pour vos coffres-forts clé surveillé à l’aide de la **Chambre forte de clé Azure** mosaïque de demande sur la page de **vue d’ensemble** de journal Analytique.

![image de mosaïque d’Azure clé coffre-fort](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Une fois que vous cliquez sur la vignette de la **vue d’ensemble** , vous pouvez afficher des synthèses de vos journaux et puis Explorer les détails pour les catégories suivantes :

- Volume de toutes les opérations de clé coffre-fort dans le temps
- Échec de volumes de l’opération dans le temps
- Latence de fonctionnement moyenne par opération
- Qualité de service pour les opérations dont le nombre d’opérations qui prennent plus de 1 000 ms et une liste des opérations qui prennent plus de 1 000 ms

![image du tableau de bord Azure clé coffre-fort](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![image du tableau de bord Azure clé coffre-fort](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Pour afficher les détails pour toutes les opérations

1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque **Azure clé coffre-fort** .
2. Sur le tableau de bord **Azure clé coffre-fort** , passez en revue les informations résumées de l’une des lames et puis cliquez sur une option pour afficher des informations détaillées le concernant dans la page de recherche de journal.

    Sur toutes les pages de recherche de journal, vous pouvez afficher les résultats en temps, des résultats détaillés et votre historique de recherche de journal. Vous pouvez également filtrer par facettes pour limiter les résultats.

## <a name="log-analytics-records"></a>Enregistrements de journal Analytique

La solution Azure clé coffre-fort analyse les enregistrements qui ont un type de **KeyVaults** qui sont collectées à partir des [journaux de AuditEvent](../key-vault/key-vault-logging.md) dans le diagnostic d’Azure.  Les propriétés de ces enregistrements sont dans le tableau suivant.  

| Propriété | Description |
|:--|:--|
| Type de | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| CallerIpAddress | Adresse IP du client qui a effectué la demande. |
| Catégorie      | Pour les journaux de la chambre forte de clé, AuditEvent est la seule valeur disponible.|
| ID de corrélation | Un GUID facultatif qui peut s’écouler le client pour mettre en corrélation des journaux de côté client avec les journaux de service côté (coffre-fort de clé). |
| DurationMs | Temps que nécessaire pour traiter la demande de l’API REST, en millisecondes. Cela n’inclut pas de latence du réseau, afin que l’heure à laquelle vous mesurez côté client peut ne pas correspond cette fois-ci. |
| HttpStatusCode_d | Code d’état HTTP retourné par la demande |
| Id_s       | ID unique de la demande |
| Identity_o | Identité du jeton qui ont été présentées lors de la création de la demande de l’API REST. Il s’agit généralement d’un « utilisateur », « service principal » ou une combinaison « utilisateur + appId » comme dans le cas d’une demande résultant d’une applet de commande PowerShell d’Azure. |
| OperationName      | Nom de l’opération, comme indiqué dans [L’enregistrement du coffre-fort Azure clé](../key-vault/key-vault-logging.md)|
| OperationVersion      | Version des API REST demandée par le client|
| RemoteIPLatitude | Latitude du client qui a effectué la demande. |
| RemoteIPLongitude | Longitude du client qui a effectué la demande. |
| RemoteIPCountry | Pays du client qui a effectué la demande.  |
| RequestUri_s | URI de la demande |
| Ressources   | Nom de la chambre forte de clé |
| ResourceGroup | Groupe de ressources de la chambre forte de clé |
| ResourceId | ID de ressource de gestionnaire de ressources Azure. Pour les journaux de la chambre forte de clé, il s’agit toujours l’ID de ressource de stockage en chambre forte de clé. |
| ResourceProvider | *MICROSOFT. KEYVAULT* |
| ResultSignature  | État HTTP|
| ResultType      | Résultat de la demande de l’API REST|
| SubscriptionId | ID de l’abonnement Azure de l’abonnement contenant le coffre-fort de la clé |


## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher les données détaillées de coffre-fort de clé Azure.
