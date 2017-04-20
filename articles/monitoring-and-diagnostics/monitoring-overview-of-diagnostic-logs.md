<properties
    pageTitle="Vue d’ensemble des journaux de Diagnostic Azure | Microsoft Azure"
    description="Découvrez ce que sont les journaux de Diagnostic Azure et comment vous pouvez les utiliser pour comprendre les événements qui se produisent dans une ressource d’Azure."
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
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Vue d’ensemble des journaux de Diagnostic Azure
**Journaux de Diagnostic Azure** sont des journaux émises par une ressource de fournissent des données riches et fréquentes sur le fonctionnement de cette ressource. Le contenu de ces journaux varie selon le type de ressource (par exemple, journaux des événements système de Windows sont une catégorie du journal de Diagnostic pour les machines virtuelles et blob, table, et les journaux de file d’attente sont les catégories de journaux de Diagnostic pour les comptes de stockage) et différer le [journal d’activité (anciennement appelé journal d’Audit ou de journal opérationnel)](monitoring-overview-activity-logs.md), ce qui offre une vision les opérations qui ont été effectuées sur les ressources de votre abonnement. Pas toutes les ressources prend en charge le nouveau type de journaux de Diagnostic décrits ici. La liste des Services de prise en charge ci-dessous montre quels types de ressources prennent en charge les nouveaux journaux de Diagnostic.

![Emplacement logique des journaux de Diagnostic](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>Ce que vous pouvez faire avec les journaux de Diagnostic
Voici certaines des choses que vous pouvez faire avec les journaux de Diagnostic :

- Enregistrer dans un **Compte de stockage** pour inspection manuelle ou audit. Vous pouvez spécifier le temps de rétention (en jours) en utilisant les **Paramètres de Diagnostic**.
- [Les **Concentrateurs d’événement** de flux](monitoring-stream-diagnostic-logs-to-event-hubs.md) pour l’ingestion par un service tiers ou d’une solution personnalisée analytique PowerBI.
- Les analyser avec [OMS journal Analytique](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Journaux de diagnostic pour les ressources sans calcul sont configurés à l’aide des paramètres de Diagnostic. **Les paramètres de diagnostic** pour un contrôle de ressource :

- Où les journaux de Diagnostic sont envoyés (compte de stockage, événement concentrateurs et/ou OMS journal Analytique).
- Les catégories de journal sont envoyés.
- Combien de temps chaque catégorie d’enregistrement doit être conservé dans un compte de stockage – une rétention de zéro jour signifie que les journaux sont conservés indéfiniment. Dans le cas contraire, cette valeur peut être comprise entre 1 et 2147483647. Si les stratégies de rétention sont définies mais le stockage des journaux dans un compte de stockage est désactivé (par exemple si un seul événement concentrateurs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.

Ces paramètres sont facilement configurés via la lame de Diagnostics pour une ressource dans le portail Azure, via Azure PowerShell et les commandes CLI ou via l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Journaux de diagnostic et les métriques pour les ressources de calcul (par exemple, les ordinateurs virtuels ou Service Fabric) utilisent [un mécanisme distinct pour la configuration et la sélection de sorties](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Comment faire pour activer la collecte des journaux de Diagnostic
Collection de journaux de Diagnostic peut être activée dans le cadre de la création d’une ressource ou une ressource est créée via la lame de la ressource dans le portail. Vous pouvez également activer les journaux de Diagnostic à tout moment à l’aide de commandes CLI ou PowerShell d’Azure, ou à l’aide de l’API REST de moniteur Azure.

> [AZURE.TIP] Ces instructions ne peuvent pas s’appliquer directement à toutes les ressources. Consultez les liens de schéma en bas de cette page pour comprendre les procédures spéciales qui peuvent s’appliquer à certains types de ressources.

[Cet article explique comment vous pouvez utiliser un modèle de ressource pour activer les paramètres de Diagnostic lors de la création d’une ressource](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Activer les journaux de Diagnostic dans le portail
Lorsque vous créez des types de ressources de calcul en activant l’extension de Windows ou Linux Azure Diagnostics, vous pouvez activer les journaux de Diagnostic dans le portail Azure :

1.  Accédez à **Nouveau** et choisissez la ressource qui que vous intéressez.
2.  Après la configuration des paramètres de base, puis en sélectionnant une taille dans la lame de **paramètres** , sous **surveillance**, sélectionnez **activé** , puis choisissez un compte de stockage où vous souhaitez stocker les journaux de Diagnostic. Vous êtes chargé de débit normal pour le stockage et les transactions lorsque vous envoyez des diagnostics pour un compte de stockage.

    ![Activer les journaux de Diagnostic lors de la création de la ressource](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  Cliquez sur **OK** et créer la ressource.

Pour les ressources sans calcul, vous pouvez activer les journaux de Diagnostic dans le portail Azure après la création d’une ressource de la manière suivante :

1.  Accédez à la lame pour la ressource et ouvrir la lame de **Diagnostics** .
2.  Cliquez **sur** et choisissez un compte de stockage et/ou le concentrateur d’événements.

    ![Activer les journaux de Diagnostic après la création de la ressource](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  **Journaux**, sélectionnez les **Catégories de journaux** que vous souhaitez collecter ou un flux.
4.  Cliquez sur **Enregistrer**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Activer les journaux de Diagnostic via PowerShell
Pour activer les journaux de Diagnostic via les applets de commande PowerShell Azure, utilisez les commandes suivantes.

Pour permettre le stockage des journaux de diagnostics dans un compte de stockage, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

L’ID de compte de stockage est l’id de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu de journaux de Diagnostic à un concentrateur d’événements, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

L’ID de règle de Bus de Service est une chaîne de ce format : `{service bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi de journaux de Diagnostic dans un espace de travail Analytique de journal, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] Le paramètre WorkspaceId n’est pas disponible dans la version d’octobre. Il sera disponible dans la version de novembre.

Vous pouvez obtenir votre nom d’espace de travail Analytique de journal dans le portail Azure.

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-diagnostic-logs-via-cli"></a>Activer les journaux de Diagnostic via l’interface CLI
Pour activer les journaux de Diagnostic via l’interface CLI d’Azure, utilisez les commandes suivantes :

Pour permettre le stockage des journaux de diagnostics dans un compte de stockage, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

L’ID de compte de stockage est l’id de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu de journaux de Diagnostic à un concentrateur d’événements, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

L’ID de règle de Bus de Service est une chaîne de ce format : `{service bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi de journaux de Diagnostic dans un espace de travail Analytique de journal, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] Le paramètre workspaceId n’est pas disponible dans la version d’octobre. Il sera disponible dans la version de novembre.

Vous pouvez obtenir votre nom d’espace de travail Analytique de journal dans le portail Azure.

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Activer les journaux de Diagnostic via l’API REST
Pour modifier les paramètres de Diagnostic à l’aide de l’API REST de moniteur Azure, consultez [ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gérer les paramètres de Diagnostic dans le portail

Afin de garantir que toutes vos ressources sont correctement configurés avec les paramètres de diagnostic, vous pouvez atteindre la blade **d’analyse** dans le portail et ouvrez la lame de **Journaux de Diagnostic** .

![Lame de journaux de diagnostic dans le portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Il se peut que vous deviez cliquez sur « Services plus » pour trouver la blade d’analyse.

Dans cette carte, vous pouvez afficher et filtrer toutes les ressources qui prennent en charge les journaux de Diagnostic pour voir s’ils ont activé de diagnostic et les compte de stockage, concentrateur d’événements et/ou espace de travail Analytique du journal ces journaux sont transmis à.

![Résultats du diagnostic journaux lame de portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Cliquez sur une ressource pour afficher tous les journaux qui ont été enregistrés dans le compte de stockage et de vous permettre de désactiver ou de modifier les paramètres de Diagnostics. Cliquez sur l’icône de téléchargement pour télécharger les journaux pour une période donnée.

![Diagnostic une ressource lame de journaux](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] Journaux de diagnostic uniquement apparaissent dans cette vue et être disponible pour le téléchargement si vous avez configuré les paramètres de diagnostic pour les enregistrer dans un compte de stockage.

En cliquant sur le lien pour **Les paramètres de Diagnostic** vous amènera à la lame de paramètres de Diagnostic, où vous pouvez activer, désactiver ou modifier vos paramètres de diagnostic pour la ressource sélectionnée.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Services pris en charge et le schéma pour les journaux de Diagnostic
Le schéma pour les journaux de Diagnostic varie en fonction de la catégorie de ressource et le journal. Vous trouverez ci-dessous les services pris en charge et leur schéma.

| Service                       | Schéma et documentation                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    Équilibrage de la charge     |    [Analytique de journal pour l’équilibreur de charge Azure (aperçu)](../load-balancer/load-balancer-monitor-log.md)             |
|    Groupes de sécurité de réseau    |    [Analytique de journal pour les groupes de sécurité réseau (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    Passerelles d’application       |    [Enregistrement des diagnostics pour la passerelle d’Application](../application-gateway/application-gateway-diagnostics.md)     |
|    Chambre forte de clé                  |    [Enregistrement de coffre-fort de clé Azure](../key-vault/key-vault-logging.md)                                                 |
|    Recherche Azure               |    [L’activation et à l’aide de la recherche du trafic Analytique](../search/search-traffic-analytics.md)                         |
|    Magasin de données lac            |    [Accès aux journaux de diagnostics pour la banque de LAC de données Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    Données lac Analytique        |    [L’accès aux journaux de diagnostic pour Azure données lac Analytique](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    Applications de logique                 |    Aucun schéma n’est disponible.                                                                                         |
|    Lot Azure                |    [Enregistrement des diagnostics par lots Azure](../batch/batch-diagnostics.md)                                              |
|    Automation Azure           |    [Analytique de journal pour l’Automation d’Azure](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    Concentrateur d’événements                  |    Aucun schéma n’est disponible.                                                                                         |
|    Bus des services                |    Aucun schéma n’est disponible.                                                                                         |
|    Analytique de flux           |    Aucun schéma n’est disponible.                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>Prise en charge de catégories de journaux par type de ressource

|Type de ressource|Catégorie|Nom complet de catégorie|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Journaux de projet|
|Microsoft.Automation/automationAccounts|JobStreams|Flux de travail|
|Microsoft.Batch/batchAccounts|ServiceLog|Journaux de service|
|Microsoft.DataLakeAnalytics/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeAnalytics/accounts|Demandes|Journaux de demandes|
|Microsoft.DataLakeStore/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeStore/accounts|Demandes|Journaux de demandes|
|Microsoft.EventHub/namespaces|ArchiveLogs|Journaux d’archivage|
|Microsoft.EventHub/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.KeyVault/vaults|AuditEvent|Journaux d’audit|
|Microsoft.Logic/workflows|WorkflowRuntime|Événements de diagnostic de runtime de workflow|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Événement du groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Compteurs de règles de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Événements de flux de règle de groupe de sécurité réseau|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Événements d’alerte équilibreur de charge|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|État de santé sonde équilibrage de la charge|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Journal de passerelle d’accès d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Journal de Performance Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Journal du pare-feu Application Gateway|
|Microsoft.Search/searchServices|OperationLogs|Journaux d’opération|
|Microsoft.ServerManagement/nodes|RequestLogs|Journaux de demandes|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.StreamAnalytics/streamingjobs|Exécution de|Exécution de|
|Microsoft.StreamAnalytics/streamingjobs|Création|Création|

## <a name="next-steps"></a>Étapes suivantes
- [Journaux de Diagnostic de flux de données à des **concentrateurs d’événement**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Modifier les paramètres de Diagnostic à l’aide de l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [Analysez les journaux avec OMS journal Analytique](../log-analytics/log-analytics-azure-storage-json.md)
