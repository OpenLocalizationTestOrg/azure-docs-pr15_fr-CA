<properties
    pageTitle="Vue d’ensemble des alertes dans Microsoft Azure | Microsoft Azure"
    description="Alertes permettent de surveiller les mesures de ressources Azure, les événements ou les journaux et d’être averti lorsqu’une condition spécifiée est remplie."
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
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Vue d’ensemble des alertes dans Microsoft Azure


Cet article décrit quelles sont les alertes, leurs avantages et comment se familiariser avec leur utilisation.  

## <a name="what-are-alerts"></a>Que sont les alertes ?
Les alertes sont une méthode des audits de surveillance des ressources Azure, événements, ou les journaux et puis d’être averti lorsqu’une condition spécifiée est remplie.

Vous pouvez recevoir des alertes en fonction de :

- **Valeurs métriques**: cette alerte se déclenche lorsque la valeur d’une mesure spécifiée dépasse un seuil que vous affectez dans les deux sens. En d’autres termes, il déclenche à la fois lorsque la condition est remplie tout d’abord, et ensuite par la suite que lorsque la condition est n’est plus remplie.
- **Événements de journal d’activité**: cette alerte peut déclencher tous les événements ou uniquement lorsqu’un certain nombre d’événements se produire.

## <a name="alerts-in-different-azure-services"></a>Alertes de différents services Azure

Les alertes sont disponibles dans différents services, y compris :

- **Idées d’application**: active web alertes de test et de mesure. Reportez-vous à la section [définir les alertes dans les conseils d’Application](../application-insights/app-insights-alerts.md) et [disponibilité du moniteur et la réactivité de n’importe quel site Web](../application-insights/app-insights-monitor-web-app-availability.md).
- **Analytique de journal (Suite de gestion des opérations)**: permet le routage des journaux de diagnostic au journal Analytique. Suite de gestion des opérations permet la métrique, journaux et autres types d’alerte. Pour plus d’informations, consultez [les alertes dans le journal Analytique](../log-analytics/log-analytics-alerts.md).  
- **Moniteur d’Azure**: Active les alertes basées sur les valeurs métriques et les événements de journal d’activité. Le moniteur Azure inclut l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/dn931943.aspx).  Pour plus d’informations, consultez [utilisation du portail Azure, PowerShell ou l’interface de ligne de commande pour créer des alertes](insights-alerts-portal.md).

## <a name="alert-actions"></a>Actions d’alerte
Vous pouvez configurer une alerte pour effectuer les opérations suivantes :

- Envoyer des notifications par courrier électronique à l’administrateur de service, à des administrateurs ou d’autres adresses e-mail que vous spécifiez.
- Appeler un webhook, ce qui vous permet de lancer des actions d’une automatisation supplémentaire.

 ![Alertes expliqués](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>Étapes suivantes

Obtenir des informations sur les règles d’alerte et les configurer à l’aide de :

- [Azure portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
- [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
