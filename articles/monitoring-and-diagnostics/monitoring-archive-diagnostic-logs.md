<properties
    pageTitle="Archiver les journaux de Diagnostic Azure | Microsoft Azure"
    description="Découvrez comment archiver vos journaux de Diagnostic pour la rétention à long terme dans un compte de stockage Azure."
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
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>Archiver les journaux de Diagnostic Azure
Dans cet article, nous montrons comment vous pouvez utiliser le portail Azure, PowerShell Cmdlets, CLI ou API REST pour archiver les [Journaux de Diagnostic Azure](monitoring-overview-of-diagnostic-logs.md) dans un compte de stockage. Cette option est utile si vous souhaitez conserver vos journaux de Diagnostic avec une stratégie de rétention pour audit, analyse statique ou sauvegarde.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer, vous devez [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans lequel vous pouvez archiver les journaux de Diagnostic. Nous vous recommandons vivement de ne pas utiliser un compte de stockage existant qui a d’autres, non-analyse des données qu’il contient afin que vous pouvez mieux contrôler l’accès à l’analyse des données. Toutefois, si vous archivez également votre journal d’activité et les mesures de diagnostics pour un compte de stockage, il peut être judicieux que compte de stockage pour vos journaux de Diagnostic permet de conserver toutes les données de surveillance dans un emplacement central. Le compte de stockage que vous utilisez doit être un compte de stockage polyvalente, pas un compte de stockage blob.

## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Pour archiver vos journaux de Diagnostic à l’aide d’une des méthodes ci-dessous, vous définissez un **Paramètre de Diagnostic** pour une ressource particulière. Un paramètre de diagnostic pour une ressource définit les catégories de journaux qui sont stockés ou transmis et les sorties : concentrateur compte et/ou des événements de stockage. Il définit également la stratégie de rétention (nombre de jours de conservation) pour les événements de chaque catégorie de journaux stockés dans un compte de stockage. Si une stratégie de rétention est définie sur zéro, les événements pour cette catégorie de journal sont stockés indéfiniment (c’est à dire jamais). Une stratégie de rétention peut être sinon de n’importe quel nombre de jours compris entre 1 et 2147483647. [Vous pouvez en savoir plus sur les paramètres de diagnostic ici](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archive des journaux de Diagnostic à l’aide du portail

1. Dans le portail, cliquez sur la lame de ressource pour la ressource sur laquelle vous souhaitez activer l’archivage de journaux de Diagnostic.
2. Dans la section **analyse** du menu Paramètres ressources, sélectionnez les **tests de diagnostic**.

    ![Section du menu de la ressource de surveillance](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. La case à cocher pour **Exporter vers un compte de stockage**, puis sélectionnez un compte de stockage. Si vous le souhaitez, définissez un nombre de jours de conservation de ces journaux à l’aide de la **rétention (jours)** curseurs. Une rétention de zéro jour stocke les journaux indéfiniment.

    ![Diagnostic lame de journaux](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Cliquez sur **Enregistrer**.

Journaux de diagnostic sont archivés à ce compte de stockage dès que les nouvelles données d’événement sont générées.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Archive des journaux de Diagnostic via les applets de commande PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriété         | Obligatoire | Description                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId       | Oui      | ID de ressource de la ressource à laquelle vous souhaitez définir un paramètre de diagnostic.                            |
| StorageAccountId | N°       | ID de ressource du compte de stockage dans lequel les journaux de Diagnostic doivent être enregistrées.                          |
| Catégories       | N°       | Séparées par des virgules la liste des catégories de journal à activer.                                                     |
| Activé          | Oui      | Valeur booléenne indiquant si les tests de diagnostic sont activés ou désactivés sur cette ressource.                  |
| RetentionEnabled | N°       | Valeur booléenne indiquant si une stratégie de rétention sont activées sur cette ressource.                            |
| RetentionInDays  | N°       | Nombre de jours pour lesquels conserver les événements compris entre 1 et 2147483647. Une valeur de zéro stocke les journaux indéfiniment. |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>Archiver le journal d’activité de l’interface CLI multiplates-formes

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propriété         | Obligatoire | Description                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId       | Oui      | ID de ressource de la ressource à laquelle vous souhaitez définir un paramètre de diagnostic.                            |
| storageId        | N°       | ID de ressource du compte de stockage dans lequel les journaux de Diagnostic doivent être enregistrées.                          |
| catégories       | N°       | Séparées par des virgules la liste des catégories de journal à activer.                                                     |
| activé          | Oui      | Valeur booléenne indiquant si les tests de diagnostic sont activés ou désactivés sur cette ressource.                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archive des journaux de Diagnostic via l’API REST
[Consultez ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx) pour plus d’informations sur la façon dont vous pouvez configurer un paramètre de diagnostic à l’aide de l’API REST de moniteur Azure.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schéma des journaux de diagnostics dans le compte de stockage
Une fois que vous avez configuré archivage, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement se produit dans une des catégories de journal que vous avez activé. Les objets BLOB dans le conteneur suivent le même format sur les journaux de Diagnostic et le journal d’activité. La structure de ces objets BLOB est :

> idées - journaux-{nom de la catégorie journal} / resourceId = / abonnements / {ID d’abonnement} /RESOURCEGROUPS/ {nom du groupe de ressources} /PROVIDERS/ {nom du fournisseur de ressources} / {type de ressource} / {nom de ressource} / y = {année à quatre chiffres} / m = {numérique mois sur deux chiffres} / d = {à deux chiffres numérique day} / h = {de hour}/m=00/PT1H.json horloge de 24 heures à deux chiffres

Ou, plus simplement,

> idées - journaux-{nom de la catégorie journal} / resourceId = / {Id de ressource} / y = {année à quatre chiffres} / m = {numérique mois sur deux chiffres} / d = {à deux chiffres numérique day} / h = {de hour}/m=00/PT1H.json horloge de 24 heures à deux chiffres

Par exemple, un nom d’objet blob peut être :

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Chaque blob PT1H.json contient un blob JSON des événements qui se sont produites pendant l’heure spécifiée dans l’URL de blob (par exemple, h = 12). Au cours de l’heure actuelle, les événements sont ajoutés au fichier PT1H.json qu’elles se produisent. La valeur de minute (m = 00) est toujours 00, dans la mesure où les événements du journal de Diagnostic sont répartis en BLOB individuels par heure.

Dans le fichier PT1H.json, chaque événement est stocké dans le tableau « enregistrements », suivant ce format :

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nom de l’élément  | Description                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| heure          | Horodatage lorsque l’événement a été généré par le service Azure, traitement de la demande correspondant de l’événement. |
| resourceId    | ID de ressource de la ressource d’impactés.                                                                       |
| operationName | Nom de l’opération.                                                                                      |
| catégorie      | Catégorie de l’événement du journal.                                                                                  |
| propriétés    | Jeu de `<Key, Value>` paires (c'est-à-dire dictionnaire) décrivant les détails de l’événement.                            |

> [AZURE.NOTE] Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.

## <a name="next-steps"></a>Étapes suivantes
- [Télécharger des objets BLOB pour analyse](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Journaux de Diagnostic de flux de données à des concentrateurs d’événement](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [En savoir plus sur les journaux de Diagnostic](monitoring-overview-of-diagnostic-logs.md)
