<properties
    pageTitle="Flux de données du journal d’activité Azure à des concentrateurs d’événement | Microsoft Azure"
    description="Apprenez à diffuser le journal d’activité Azure à des concentrateurs de l’événement."
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
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Flux de données du journal d’activité Azure à des concentrateurs d’événement
Le [**Journal d’activité Azure**](./monitoring-overview-activity-logs.md) peuvent être diffusées dans quasiment en temps réel à n’importe quelle application à l’aide de l’option « Exporter » intégrée dans le portail, ou en activant l’Id de règle de Bus de Service dans un profil de journal via les applets de commande PowerShell Azure Azure CLI.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Ce que vous pouvez faire avec le journal d’activité et les concentrateurs d’événement
Voici quelques méthodes que vous pouvez utiliser la fonctionnalité de diffusion en continu du journal d’activité :

- **Flux de données pour les systèmes d’enregistrement et de télémétrie tiers** – au fil du temps, les concentrateurs d’événements de diffusion en continu devient le mécanisme de tuyau votre journal d’activité dans les SIEMs de tiers et des solutions d’analytique de journaux.
- **Créer une plate-forme de la journalisation et de télémétrie personnalisé** – si ou est déjà une plateforme de télémétrie personnalisés sont simplement penser à un projet, hautement évolutive publication-abonnement nature de concentrateurs d’événements vous permet d’acquisition avec souplesse le journal d’activité. [Consultez le guide de Dan Rosanova à l’aide de concentrateurs d’événement dans une plate-forme de télémétrie d’échelle mondiale ici.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Activer la diffusion en continu du journal d’activité
Vous pouvez activer la lecture en continu du journal d’activité, soit par programme ou via le portail. Quoi qu’il en soit, vous choisissez un Namespace de Bus de Service et d’une stratégie d’accès partagé de cet espace de noms et un concentrateur d’événements est créé dans cet espace de noms lorsque le premier nouvel événement de journal d’activité se produit. Si vous ne disposez pas d’un Namespace de Bus de Service, vous devez d’abord créer un. Si vous avez précédemment diffusées en continu des événements du journal d’activité pour ce Namespace de Bus de Service, le concentrateur d’événements créé précédemment sera réutilisé. La stratégie d’accès partagé définit les autorisations le mécanisme de transmission en continu. Aujourd'hui, de diffusion en continu à un événement de concentrateurs nécessite des autorisations **Gérer**, **lire**et **Envoyer** . Vous pouvez créer ou modifier des stratégies d’accès Service Namespace de Bus partagé dans le portail classique sous l’onglet « Configuration » pour votre Namespace de Bus de Service. Pour mettre à jour le profil du journal journal d’activité pour inclure la diffusion en continu, l’utilisateur effectuant la modification doit disposer de l’autorisation de ListKey sur cette règle de l’autorisation de Bus de Service.

### <a name="via-azure-portal"></a>Via le portail Azure 
1. Accédez à la lame du **Journal d’activité** à l’aide du menu sur le côté gauche du portail.

    ![Accédez au journal d’activité dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Cliquez sur le bouton **Exporter** dans la partie supérieure de la lame.

    ![Bouton Exporter de portail](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. De la lame qui s’affiche, vous pouvez sélectionner les régions pour lesquelles vous souhaitez le Namespace du Bus de Service dans lequel vous souhaitez un concentrateur d’événements doivent être créés pour ces événements de diffusion en continu et les événements de flux.

    ![Exporter la lame du journal d’activité](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Cliquez sur **Enregistrer** pour enregistrer les paramètres. Les paramètres sont immédiatement être appliqués à votre abonnement.


### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Si un profil de journal existe déjà, vous devez d’abord supprimer ce profil.

1. Utilisez `Get-AzureRmLogProfile` pour identifier si un profil de journal existe
2. Si tel est le cas, utilisez `Remove-AzureRmLogProfile` pour le supprimer.
3. Utilisez `Set-AzureRmLogProfile` pour créer un profil :

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

L’ID de règle de Bus de Service est une chaîne de format : {ID de ressource de bus de service} /authorizationrules/ {nom de clé}, par exemple 

### <a name="via-azure-cli"></a>Via l’interface CLI Azure
Si un profil de journal existe déjà, vous devez d’abord supprimer ce profil.

1. Utilisez `azure insights logprofile list` pour identifier si un profil de journal existe
2. Si tel est le cas, utilisez `azure insights logprofile delete` pour le supprimer.
3. Utilisez `azure insights logprofile add` pour créer un profil :

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

L’ID de règle de Bus de Service est une chaîne de ce format : `{service bus resource ID}/authorizationrules/{key name}`.
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Comment consommer les données du journal de concentrateurs d’événement ?
[Le schéma pour le journal d’activité est disponible ici](./monitoring-overview-activity-logs.md). Chaque événement est dans un tableau d’objets BLOB JSON appelée « enregistrements ».

## <a name="next-steps"></a>Étapes suivantes
- [Archiver le journal d’activité à un compte de stockage](./monitoring-archive-activity-log.md)
- [Lisez la vue d’ensemble du journal d’activité Azure](./monitoring-overview-activity-logs.md)
- [Définition d’une alerte basée sur un événement de journal d’activité](./insights-auditlog-to-webhook-email.md)
