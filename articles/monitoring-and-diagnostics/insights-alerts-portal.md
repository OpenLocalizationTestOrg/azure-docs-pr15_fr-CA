<properties
    pageTitle="Permet de créer des alertes pour les services Azure portal Azure | Microsoft Azure"
    description="Le portail Azure permet de créer des alertes Azure, qui peuvent déclencher des notifications ou automation lorsque les conditions spécifiées sont remplies."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="robb"/>

# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Permet de créer des alertes pour les services Azure portal Azure

> [AZURE.SELECTOR]
- [Portail](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Vue d’ensemble

Cet article vous indique comment configurer les alertes Azure via le portail Azure.   

Vous pouvez recevoir une alerte basée sur des mesures de surveillance pour ou d’événements sur vos services Azure.

- **Valeurs métriques** - l’alert se déclenche lorsque la valeur d’une mesure spécifiée dépasse un seuil que vous affectez dans les deux sens. En d’autres termes, il déclenche à la fois lorsque la condition est remplie tout d’abord, et ensuite par la suite que lorsque la condition est n’est plus remplie.    
- **Événements de journal d’activité** - une alerte peut déclencher sur *chaque* événement, et uniquement lorsqu’un certain nombre d’événements se produire.


Vous pouvez configurer une alerte pour effectuer les opérations suivantes lorsqu’il déclenche :

- envoyer des notifications par courrier électronique à l’administrateur de service et des administrateurs
- envoyer les courriels supplémentaires que vous spécifiez.
- appeler un webhook
- lancer l’exécution d’une procédure Azure opérationnelle (uniquement à partir du portail Azure)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide de

- [Azure portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
- [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Créer une règle d’alerte sur une mesure avec le portail Azure

1. Dans le [portail](https://portal.azure.com/), localisez la ressource que vous voulez contrôler et sélectionnez-le.

2. Sous la section surveillance, sélectionnez **alertes** ou des **règles d’alerte** . Le texte et l’icône peuvent varier légèrement pour les différentes ressources.  

    ![Surveillance](./media/insights-alerts-portal/AlertRulesButton.png)


3. Sélectionnez la commande **Ajouter une alerte** et renseignez les champs.

    ![Ajout d’alerte](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nom de** l’alerte de la règle et une **Description**, qui se présente également dans les e-mails de notification.
5. Sélectionnez la **métrique** que vous souhaitez surveiller, puis cliquez sur une valeur de **Condition** et de **seuil** pour la mesure. A également choisi la **période** pendant laquelle la règle de mesure doit être satisfaite avant l’alertes déclencheurs. Par exemple, si vous utilisez la période « PT5M » et votre alerte recherche UC supérieure à 80 %, l’alerte se déclenche lorsque l’UC a été constamment ci-dessus 80 % pendant 5 minutes. Une fois le premier déclencheur se produit, il déclenche à nouveau lors de l’UC reste inférieur à 80 % pendant 5 minutes. La mesure de l’unité centrale se produit toutes les minutes.   

6. Vérifiez les **propriétaires de messagerie...** si vous souhaitez que les administrateurs et les administrateurs de convoyeur pour être envoyé par courrier électronique lorsque l’alerte se déclenche.

7. Si vous souhaitez que d’autres messages électroniques pour recevoir une notification lorsque l’alerte se déclenche, ajoutez-les dans le champ **administrateur supplémentaires email(s)** . Séparez plusieurs e-mails par des points-virgules :*email@contoso.com;email2@contoso.com*

8. Si vous souhaitez qu’elle appelée lorsque l’alerte se déclenche, placer dans un URI valide dans le champ **Webhook** .

9. Si vous utilisez Automation d’Azure, vous pouvez sélectionner une procédure opérationnelle à exécuter lorsque l’alerte se déclenche.

10. Cliquez sur **OK** lorsque vous avez terminé de créer l’alerte.   

Dans quelques minutes, l’alerte est active et déclenche comme décrit précédemment.

## <a name="managing-your-alerts"></a>Gestion de vos alertes

Une fois que vous avez créé une alerte, vous pouvez le sélectionner et :

- Permet d’afficher un graphique illustrant le seuil de métrique et les valeurs réelles du jour précédent.
- Modifier ou le supprimer.
- **Désactiver** ou **Activer** il si vous voulez temporairement arrêter ou reprendre reçoit les notifications de cette alerte.



## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une vue d’ensemble de la surveillance Azure](monitoring-overview.md) y compris les types d’informations, vous pouvez collecter et analyser.
* Pour en savoir plus sur la [configuration de webhooks dans les alertes](insights-webhooks-alerts.md).
* Pour en savoir plus sur les [Procédures opérationnelles de Automation Azure](..\automation\automation-starting-a-runbook.md).
* Obtenir une [vue d’ensemble des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) et recueillir des métriques de haute fréquence détaillées sur votre service.
* Obtenez une [vue d’ensemble de la collection de mesures](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et la réactivité.
