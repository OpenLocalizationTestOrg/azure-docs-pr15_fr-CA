<properties
 pageTitle="Planificateur de haute disponibilité et de fiabilité"
 description="Planificateur de haute disponibilité et de fiabilité"
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
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>Planificateur de haute disponibilité et de fiabilité

## <a name="azure-scheduler-high-availability"></a>Planificateur Azure haute disponibilité

Comme un cœur de service de la plateforme Azure, Azure planificateur est hautement disponible et comprend le déploiement redondant geo service et réplication du travail de géo-régionales.

### <a name="geo-redundant-service-deployment"></a>Déploiement de service géo-redondants

Le planificateur Azure est disponible via l’interface utilisateur dans presque chaque région géographique dans Azure dès aujourd'hui. La liste des régions qui le planificateur Azure est disponible dans est [répertorié ici](https://azure.microsoft.com/regions/#services). Si un centre de données dans une région hébergée est rendu indisponible, les fonctionnalités de basculement du planificateur d’Azure sont telles que le service est disponible à partir d’un autre centre de données.

### <a name="geo-regional-job-replication"></a>Réplication du travail de géo-régionales

Est le planificateur Azure frontal disponible pour les demandes de gestion, mais votre travail est également répliquée geo. Lorsqu’il existe une panne dans une région, le planificateur Azure bascule et garantit que la tâche est exécutée à partir d’un autre centre de données dans la région géographique apparié.

Par exemple, si vous avez créé un projet dans Sud, Azure planificateur réplique automatiquement cette tâche dans Nord centrale nous. Lorsqu’il existe une erreur dans Sud, Planificateur de Azure permet de s’assurer que le travail est exécuté à partir du Nord centrale nous. 

![][1]

En conséquence, le planificateur Azure garantit que vos données restent dans la même région géographique plus large dans le cas d’un échec d’Azure. Par conséquent, vous ne devez pas dupliquer votre projet pour ajouter la haute disponibilité – planificateur d’Azure fournit automatiquement les fonctions de haute disponibilité pour vos travaux.

## <a name="azure-scheduler-reliability"></a>Fiabilité du planificateur Azure

Le planificateur Azure garantit une haute disponibilité et adopte une approche différente pour les tâches créées par l’utilisateur. Par exemple, votre projet peut invoquer un point de terminaison HTTP qui n’est pas disponible. Néanmoins, Azure planificateur essaie d’exécuter votre projet avec succès, en vous proposant d’autres options pour gérer les défaillances. Planificateur Azure pour cela de deux façons :

### <a name="configurable-retry-policy-via-retrypolicy"></a>Stratégie de nouvelle tentative configurables via « retryPolicy »

Azure planificateur vous permet de configurer une stratégie de nouvelle tentative. Par défaut, si un travail échoue, planificateur essaie la tâche nouveau quatre fois de plus, à des intervalles de 30 secondes. Vous pouvez configurer à nouveau cette stratégie de nouvelle tentative pour être plus agressive (par exemple, dix fois, à des intervalles de 30 secondes) ou plus (par exemple, deux fois, à des intervalles quotidiens.)

Ainsi, lorsque cela peut vous aider, vous pouvez créer une tâche qui s’exécute une fois par semaine et appelle un point de terminaison HTTP. Si le point de terminaison HTTP est arrêté pendant quelques heures lorsque votre tâche s’exécute, il pouvez que vous ne souhaitez pas attendre une semaine de plus pour la tâche à exécuter à nouveau, dans la mesure où même la stratégie de nouvelles tentatives par défaut échoue. Dans ce cas, vous pouvez reconfigurer la stratégie standard de réessayer pour réessayer toutes les trois heures (par exemple) au lieu de toutes les 30 secondes.

Pour savoir comment configurer une stratégie de nouvelles tentatives, reportez-vous à [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Autre point de terminaison une configurabilité via « errorAction »

Si le point de terminaison cible pour votre tâche de planificateur d’Azure reste inaccessible, Azure planificateur revient au point de terminaison de gestion d’erreur autre après sa politique de nouvelle tentative. Si un autre point de terminaison de gestion des erreurs est configuré, le planificateur Azure l’appelle. Avec un autre point de terminaison, vos propres travaux est hautement disponible en cas de défaillance.

Par exemple, dans le schéma ci-dessous, le planificateur Azure suit sa politique de nouvelle tentative pour le positionnement d’un service web de New York. Une fois les tentatives échouent, il vérifie s’il existe un autre. Il poursuit son action et démarre la présentation de demandes à l’autre avec la même stratégie de nouvelle tentative.

![][2]

Notez que la même stratégie de nouvelle tentative s’applique à l’action d’origine et l’action sur erreur de remplacement. Il est également possible d’avoir autre erreur type de l’action action différer de type d’action de l’action principale. Par exemple, tandis que l’action principale peut être appelé à un point de terminaison HTTP, l’action sur erreur à la place est peut-être une file d’attente de stockage, file d’attente de bus de service ou service bus rubrique action qui effectue l’enregistrement des erreurs.

Pour savoir comment configurer un autre point de terminaison, consultez [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Voir aussi

 [Quel est le planificateur ?](scheduler-intro.md)

 [Hiérarchie d’entités, terminologie et des concepts du planificateur Azure](scheduler-concepts-terms.md)

 [Mise en route à l’aide du planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence des API de reste de planificateur Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence d’applets de commande PowerShell de planificateur Azure](scheduler-powershell-reference.md)

 [Limites de planificateur Azure, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)

 [Azure d’authentification sortante du planificateur](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
