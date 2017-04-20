<properties
 pageTitle="Référence d’applets de commande PowerShell de planificateur"
 description="Référence d’applets de commande PowerShell de planificateur"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-powershell-cmdlets-reference"></a>Référence d’applets de commande PowerShell de planificateur

Le tableau suivant décrit et pointe vers la page de référence de chacun des applets de commande principales dans le planificateur d’Azure.

Pour installer PowerShell d’Azure et l’associer à votre abonnement Azure, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md). 

Pour plus d’informations sur [les applets de commande Azure le Gestionnaire de ressources](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx), reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).

|Applet de commande|Description de l’applet de commande|
|---|---|
[Désactiver-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Désactive une collection de tâches. 
[Activer-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Permet à une collection de tâches.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Obtient les travaux du planificateur.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Obtient la tâche collections.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Obtient de l’historique des travaux.
[Nouvelle-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Crée une tâche HTTP.
[Nouvelle-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Crée une collection de tâches.
[Nouvelle-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Crée une tâche de file d’attente de bus de service.
[Nouvelle-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Crée un travail de rubrique de bus de service.
[Nouvelle-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Crée une tâche de file d’attente de stockage. 
[Supprimer-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Supprime une tâche du planificateur.  
[Supprimer-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Supprime une collection de tâches. 
[Ensemble-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Modifie une tâche du planificateur HTTP.
[Ensemble-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Modifie un ensemble de tâches. 
[Ensemble-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Modifie une tâche de file d’attente de bus de service.  
[Ensemble-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Modifie un travail de rubrique de bus de service. 
[Ensemble-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Modifie une tâche de file d’attente de stockage.   

Pour des informations plus détaillées, vous pouvez exécuter les applets de commande suivantes : 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Hiérarchie d’entités, terminologie et des concepts du planificateur Azure](scheduler-concepts-terms.md)

 [Mise en route à l’aide du planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Référence des API de reste de planificateur Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure planificateur haute disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)

 [Azure d’authentification sortante du planificateur](scheduler-outbound-authentication.md)
