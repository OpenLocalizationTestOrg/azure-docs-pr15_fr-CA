<properties
    pageTitle="Vue d’ensemble du journal d’activité Azure | Microsoft Azure"
    description="Découvrez ce qu’est le journal d’activité Azure et comment vous pouvez l’utiliser pour comprendre les événements qui surviennent dans votre abonnement Azure."
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
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Vue d’ensemble du journal d’activité Azure
Le **Journal d’activité Azure** est un journal qui offre une vision les opérations qui ont été effectuées sur les ressources de votre abonnement. Le journal d’activité était précédemment appelé « Audit Logs » ou « Journaux opérationnelles », dans la mesure où elle signale les événements de contrôle-plan pour vos abonnements. Le journal d’activité, vous pouvez déterminer le « quoi, qui, quand et ' pour toutes les opérations (PUT, POST, DELETE) effectuées sur les ressources de votre abonnement d’écriture. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas les opérations de lecture (GET).

Le journal d’activité diffère de [Journaux de Diagnostic](./monitoring-overview-of-diagnostic-logs.md), qui sont émis par une ressource de tous les journaux. Ces journaux fournit des données sur le fonctionnement de cette ressource, plutôt que sur cette ressource des opérations.

Vous pouvez récupérer les événements dans votre journal d’activité de l’utilisation du portail Azure, CLI, les applets de commande PowerShell et l’API REST de moniteur Azure.

Afficher cette [vidéo de présentation du journal d’activité](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>Ce que vous pouvez faire avec le journal d’activité
Voici certaines des choses que vous pouvez faire avec le journal d’activité :

- La requête et l’afficher dans le **portail Azure**.
- Interroger via l’API REST, PowerShell Cmdlet ou l’interface CLI.
- [Créer une alerte e-mail ou webhook qui déclenche la désactivation d’un événement dans le journal d’activité.](./insights-auditlog-to-webhook-email.md)
- [Enregistrer pour un **Compte de stockage** pour inspection manuelle ou d’archivage](./monitoring-archive-activity-log.md). Vous pouvez spécifier le temps de rétention (en jours) à l’aide de **Profils de journal**.
- Analyser dans l’utilisation du [**pack de contenu PowerBI**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)de PowerBI.
- [Diffuser à un **Concentrateur d’événements** ](./monitoring-stream-activity-logs-event-hubs.md) pour l’ingestion par un service tiers ou d’une solution personnalisée analytique PowerBI.

## <a name="export-the-activity-log-with-log-profiles"></a>Exporter le journal avec les profils du journal d’activité
Un **Profil du journal** contrôle la façon dont votre journal d’activité est exporté. À l’aide d’un profil de journal, vous pouvez configurer :

- Lorsque le journal d’activité doit être envoyé (compte de stockage ou concentrateurs d’événement)
- Les catégories d’événements (écriture, suppression, Action) doivent être envoyés.
- Les régions (emplacements) doivent être exportées.
- Combien de temps le journal d’activité doivent être conservé dans un compte de stockage – une rétention de zéro jour signifie que les journaux sont conservés indéfiniment. Dans le cas contraire, la valeur peut être n’importe quel nombre de jours compris entre 1 et 2147483647. Si vous définissez des stratégies de rétention, mais stocker les journaux dans un compte de stockage est désactivée (par exemple, si seul événement concentrateurs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.

Ces paramètres peuvent être configurés à l’aide de l’option « Exporter » dans la lame du journal d’activité dans le portail. Il peuvent également être configuré par programmation [à l’aide de l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx), les applets de commande PowerShell ou l’interface CLI. Un abonnement ne peut avoir qu’un seul profil de journal.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurer les profils du journal d’utilisation du portail Azure
Vous pouvez diffuser le journal d’activité à un concentrateur d’événements ou les stocker dans un compte de stockage à l’aide de l’option « Exporter » dans le portail Azure.

1. Accédez à la lame du **Journal d’activité** à l’aide du menu sur le côté gauche du portail.

    ![Accédez au journal d’activité dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Cliquez sur le bouton **Exporter** dans la partie supérieure de la lame.

    ![Bouton Exporter de portail](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Dans la lame qui s’affiche, vous pouvez sélectionner :  
    - régions pour lequel vous souhaitez exporter des événements
    - le compte de stockage vers lequel vous souhaitez enregistrer les événements
    - le nombre de jours que vous souhaitez conserver ces événements dans le stockage. Un paramètre de 0 jours conserve les journaux pour toujours.
    - le Namespace de Bus de Service dans lequel vous souhaitez un concentrateur d’événements doivent être créés pour ces événements de diffusion en continu.

    ![Exporter la lame du journal d’activité](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Cliquez sur **Enregistrer** pour enregistrer les paramètres. Les paramètres sont immédiatement être appliqués à votre abonnement.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurer des profils de journal à l’aide des applets de commande PowerShell Azure
#### <a name="get-existing-log-profile"></a>Obtenir le profil de journal existant
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Ajouter un profil de journal
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriété         | Obligatoire | Description   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom             | Oui      | Nom du profil de votre journal.                                 |
| StorageAccountId | N°       | ID de ressource du compte de stockage à laquelle le journal d’activité doit être enregistré.                         |
| serviceBusRuleId | N°       | ID de règle de Bus de service pour l’espace de noms du Bus de Service que vous souhaitez disposer de concentrateurs d’événement créés dans. Est une chaîne de ce format : `{service bus resource ID}/authorizationrules/{key name}`. |
| Emplacements        | Oui      | Liste de séparées par des virgules des régions pour lesquelles vous souhaitez collecter des événements du journal d’activité.              |
| RetentionInDays  | Oui      | Nombre de jours pour les événements qui doivent être conservées, comprise entre 1 et 2147483647. Une valeur de zéro stocke les journaux indéfiniment (indéfiniment). |
| Catégories       | N°       | Séparées par des virgules la liste des catégories d’événements qui doivent être collectés. Les valeurs possibles sont l’écriture, de suppression et d’Action.                                 |

#### <a name="remove-a-log-profile"></a>Supprimer un profil de journal
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurer des profils de journal à l’aide de la CLI inter-plateforme Azure
#### <a name="get-existing-log-profile"></a>Obtenir le profil de journal existant
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
Le `name` propriété doit être le nom de votre profil de journal.

#### <a name="add-a-log-profile"></a>Ajouter un profil de journal
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriété         | Obligatoire | Description   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nom             | Oui      | Nom du profil de votre journal.                                 |
| storageId        | N°       | ID de ressource du compte de stockage à laquelle le journal d’activité doit être enregistré.                         |
| serviceBusRuleId | N°       | ID de règle de Bus de service pour l’espace de noms du Bus de Service que vous souhaitez disposer de concentrateurs d’événement créés dans. Est une chaîne de ce format : `{service bus resource ID}/authorizationrules/{key name}`. |
| emplacements        | Oui      | Liste de séparées par des virgules des régions pour lesquelles vous souhaitez collecter des événements du journal d’activité.              |
| retentionInDays  | Oui      | Nombre de jours pour les événements qui doivent être conservées, comprise entre 1 et 2147483647. Une valeur de zéro stocke les journaux indéfiniment (indéfiniment).     |
| catégories       | N°       | Séparées par des virgules la liste des catégories d’événements qui doivent être collectés. Les valeurs possibles sont l’écriture, de suppression et d’Action.                                 |

#### <a name="remove-a-log-profile"></a>Supprimer un profil de journal
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Schéma d’événement
Chaque événement dans le journal d’activité a un blob JSON similaire à cet exemple :

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nom de l’élément         | Description             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| autorisation        | BLOB de propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « rôle » et « étendue ».|
| appelant               | Adresse de messagerie de l’utilisateur qui a effectué l’opération, la revendication de nom UPN ou la revendication de nom principal de service basée sur la disponibilité.|
| canaux             | Une des valeurs suivantes : « Admin », « Opération »|
| ID de corrélation        | Généralement, un GUID dans le format de chaîne. Les événements qui partagent un correlationId appartiennent à la même action colossaux.   |
| Description          | Description de texte statique d’un événement.                              |
| eventDataId          | Identificateur unique d’un événement.    |
| source d’événement          | Nom du service Azure ou infrastructure qui a généré cet événement.    |
| httpRequest          | BLOB décrivant la requête Http. Inclut généralement les « clientRequestId », le « clientIpAddress » et la « méthode » (HTTP. Par exemple, placer).                            |
| niveau                | Niveau de l’événement. Une des valeurs suivantes : « Critique », « Erreur », « Warning » et « Informationnels » et « Verbose »  |
| resourceGroupName    | Nom du groupe de ressource pour la ressource d’impactés.               |
| resourceProviderName | Nom du fournisseur de ressources pour la ressource d’impactés             |
| ResourceURI des services Web          | Id de ressource de la ressource d’impactés.                               |
| operationId          | Un GUID est partagé entre les événements qui correspondent à une opération unique.         |
| operationName        | Nom de l’opération.  |
| propriétés           | Jeu de `<Key, Value>` paires (autrement dit, un dictionnaire) décrivant les détails de l’événement.                                |
| état               | Chaîne décrivant l’état de l’opération. Certaines des valeurs courantes sont : démarré, en cours, réussite, échec, actif, résolu.                                |
| Sous-état            | Généralement le code d’état HTTP du correspondant reste appeler, mais peut également inclure d’autres chaînes décrivant un sous-état, comme ces valeurs communes : OK (Code d’état HTTP : 200), CR (Code d’état HTTP : 201), accepté (Code d’état HTTP : 202), le contenu ne (Code d’état HTTP : 204), demande incorrecte (Code d’état HTTP : 400), introuvable (Code d’état HTTP : 404), conflit (Code d’état HTTP : 409), erreur de serveur interne (Code d’état HTTP : 500), Service non disponible (Code d’état HTTP : 503), délai de la passerelle (Code d’état HTTP : 504). |
| eventTimestamp       | Horodatage lorsque l’événement a été généré par le service Azure, traitement de la demande correspondant de l’événement.     |
| submissionTimestamp  | Horodatage lorsque l’événement est devenue disponible pour les requêtes.             |
| subscriptionId       | ID d’abonnement Azure.  |
| nextLink             | Jeton de continuation pour extraire le jeu de résultats suivant lorsqu’ils sont décomposées en plusieurs réponses. Généralement nécessaires lorsqu’il y a plus de 200 enregistrements.     |

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur le journal d’activité (anciennement les journaux d’Audit)](../resource-group-audit.md)
- [Flux de données du journal d’activité Azure à des concentrateurs d’événement](./monitoring-stream-activity-logs-event-hubs.md)
