<properties
    pageTitle="Vue d’ensemble de la surveillance de Microsoft Azure | Microsoft Azure"
    description="Présentation de niveau supérieure de surveillance et de diagnostic de Microsoft Azure, y compris les alertes, webhooks, échelle et plus encore."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Vue d’ensemble de la surveillance de Microsoft Azure

Cet article fournit une vue d’ensemble conceptuelle de la surveillance des ressources Azure. Il fournit des pointeurs vers les informations sur les types de ressources spécifiques.  Pour obtenir des informations détaillées sur votre application Azure non point de vue de la surveillance, consultez [les conseils de surveillance et de diagnostic](../best-practices-monitoring.md).

Applications en nuage sont complexes avec de nombreuses pièces mobiles. Surveillance fournit des données pour vous assurer que votre application reste opérationnel et en cours d’exécution dans un état sain. Il vous aide également à conjurer de problèmes potentiels ou résolution au-delà de celles. En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations fouillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou la facilité de maintenance, ou automatiser des actions qui, sinon, nécessiteraient l’intervention manuelle.

Le diagramme suivant montre une vue conceptuelle de surveillance Azure, y compris le type de journaux, que vous pouvez recueillir et ce que vous pouvez faire avec ces données.   

![Modèle logique de surveillance et de diagnostic pour les ressources de calcul non](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figure 1 : Le modèle conceptuel de surveillance et de diagnostic pour les ressources de calcul non

<br/>

![Modèle logique de surveillance et de diagnostic pour les ressources de calcul](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figure 2 : Le modèle conceptuel de surveillance et de diagnostic pour les ressources de calcul


## <a name="monitoring-sources"></a>Sources de surveillance
### <a name="activity-logs"></a>Journaux d’activité
Vous pouvez rechercher le journal d’activité (précédemment appelé opérationnel ou des journaux d’Audit) pour plus d’informations sur la ressource, comme indiqué par l’infrastructure Azure. Le journal contient des informations telles que des heures lorsque les ressources sont créées ou détruites.  

### <a name="host-vm"></a>Hôte d’ordinateur virtuel
**Calculer uniquement**


Certaines ressources telles que les Services en nuage, les ordinateurs virtuels, de calcul et tissu de Service ont une machine virtuelle hôte dédié qu’ils interagissent avec. La machine virtuelle d’hôte est l’équivalent de la racine virtuelle dans le modèle d’hyperviseur Hyper-V. Dans ce cas, vous pouvez collecter des mesures sur tout l’ordinateur hôte virtuel en plus du système d’exploitation invité.  

Pour d’autres services Azure, il n’est pas nécessairement un mappage 1:1 entre vos ressources et un VM hôte particulier afin que les mesures de machine virtuelle hôte ne sont pas disponibles.


### <a name="resource---metrics-and-diagnostics-logs"></a>Ressources - des audits et des journaux de Diagnostics
Mesures collectable varient en fonction du type de ressource. Par exemple, les Machines virtuelles fournit des statistiques sur les e/s disque et % de l’UC. Mais ces statistiques n’existent pas pour une file d’attente du Bus des services, qui fournit à la place des mesures telles que le débit de message et de la taille de file d’attente.

Pour les ressources de calcul, vous pouvez obtenir les métriques sur les modules du système d’exploitation invité et de diagnostics tels que les tests de diagnostic Azure. Diagnostics de Windows Azure permet de rassembler et achemine collectent des données de diagnostic vers d’autres emplacements, y compris le stockage Azure.

Une liste des mesures actuellement collectable est disponible au niveau de la [prise en charge des mesures](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Application - les journaux de diagnostic, de journaux d’Application et de mesures
**Calculer uniquement**

Applications peuvent s’exécuter sur le système d’exploitation invité dans le modèle de calcul. Ils émettent leur propre ensemble de journaux et de mesures.

Incluent les types de mesures

- Compteurs de performance
- Journaux d’application
- Journaux des événements Windows
- Source de l’événement .NET
- Journaux IIS
- Manifeste de base ETW
- Vidages sur incident
- Journaux d’erreurs du client


## <a name="uses-for-monitoring-data"></a>Utilise pour la surveillance de données

### <a name="visualize"></a>Visualiser
Visualisation de vos données dans les graphiques et les diagrammes d’analyse vous permet de trouver les tendances beaucoup plus rapidement qu’en parcourant les données elles-mêmes.  

Quelques méthodes de visualisation :

- Utiliser le portail Azure
- Données d’itinéraire d’idées d’Application Azure
- Données d’itinéraire de Microsoft PowerBI
- Acheminer les données vers un outil de visualisation de tiers à l’aide de la diffusion en continu live ou grâce à l’outil de lecture à partir d’une archive de stockage Azure

### <a name="archive"></a>Archivage
Surveillance des données est en général écrite dans le stockage Azure et conserve jusqu'à ce que vous les supprimiez.

Manières d’utiliser ces données :

- Une fois écrite, vous pouvez avoir des autres outils au sein ou en dehors d’Azure lire et traiter.
- Vous téléchargez les données localement pour un archivage local ou modifiez votre stratégie de rétention dans le nuage pour conserver les données pendant de longues périodes de temps.  
- Vous laissez les données dans le stockage Azure indéfiniment, si vous n’avez à payer pour le stockage Azure en fonction de la quantité de données que vous conservez.
-

### <a name="query"></a>Requête
Vous pouvez utiliser l’API REST de moniteur de Azure, les commandes d’Interface de ligne de commande (CLI) de plate-forme, les applets de commande PowerShell ou le Kit de développement .NET pour accéder aux données dans le système ou le stockage Azure

Voici quelques exemples :

-  L’obtention de données pour une application de surveillance personnalisée que vous avez écrit
-  Création de requêtes personnalisées et envoyer ces données à une application tierce.

### <a name="route"></a>Itinéraire
Vous pouvez diffuser des données d’analyse vers d’autres emplacements en temps réel.

Voici quelques exemples :

- Envoyer aux analyses d’Application afin de pouvoir utiliser les outils de visualisation il.
- Envoyer à des concentrateurs d’événement afin que vous pouvez acheminer vers des outils tiers pour effectuer une analyse en temps réel.

### <a name="automate"></a>Automatiser
Vous pouvez utiliser les données de surveillance pour déclencher des événements ou processus tout de même quelques exemples :

- Utiliser des données pour les instances de calcul autoscale vers le haut ou vers le bas en fonction de la charge de l’application.
- Envoyer des messages électroniques lorsqu’une mesure dépasse un seuil prédéterminé.
- Appeler une URL web (webhook) pour exécuter une action dans un système en dehors d’Azure
- Démarrer une procédure opérationnelle dans Azure automation pour effectuer les différentes tâches

## <a name="methods-of-use"></a>Procédés d’utilisation
En général, vous pouvez manipuler à l’aide d’une des méthodes suivantes, de routage et de suivi des données. Toutes les méthodes sont disponibles pour toutes les actions ou les types de données.

- [Azure portal](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [Interface de ligne de commande (CLI) multiplates-formes](insights-cli-samples.md)
- [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [KIT DE DÉVELOPPEMENT .NET](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Offres de surveillance d’Azure
Azure a des offres disponibles pour la surveillance de vos services d’infrastructure de la vierge de télémétrie de l’application. La meilleure stratégie de surveillance combine l’utilisation de trois pour bien comprendre le fonctionnement de vos services complet et détaillé.

- [Moniteur d’azure](http://aka.ms/azmondocs) – offres visualisation, requête, routage, d’alerte, autoscale et automatisation des données à la fois de l’infrastructure Azure (journal d’activité) et de chaque ressource Azure (journaux de Diagnostic). Cet article fait partie de la documentation du moniteur d’Azure. Le nom de moniteur d’Azure a été publié le 25 septembre à Ignite 2016.  Le nom précédent a été « Perspectives Azure ».  
- [Idées d’application](https://azure.microsoft.com/documentation/services/application-insights/) – fournit la détection riche et diagnostics pour les problèmes liés à la couche application de votre service, bien intégrée sur les données de surveillance d’Azure. C’est la plate-forme de diagnostic par défaut pour l’application de Service Web Apps.  Vous pouvez router des données provenant d’autres services.  
- Partie de [Journal Analytique](https://azure.microsoft.com/documentation/services/log-analytics/) d' [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – fournit une solution de gestion informatique globale pour à la fois sur site et en nuage infrastructure tierce (par exemple, AWS) en plus des ressources d’Azure.  Données du moniteur d’Azure peuvent être acheminées directement vers Analytique du journal afin de voir les mesures et les journaux pour l’ensemble de votre environnement à un seul endroit.     


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur

- [Moniteur Azure dans une vidéo à partir de Ignite 2016](https://myignite.microsoft.com/videos/4977)
- [Mise en route avec moniteur Azure](monitoring-get-started.md)
- [Les tests de diagnostic azure](../azure-diagnostics.md) si vous tentez de diagnostiquer les problèmes dans votre application de Service en nuage, Machine virtuelle ou Service de Fabric.
- [Idées d’application](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de problèmes de diagnostic dans votre application Web de Service d’application.
- [Résolution des problèmes le stockage Azure](../storage/storage-e2e-troubleshooting.md) lors de l’utilisation de files d’attente, des tableaux ou des Blobs de stockage
- [Analytique de journal](https://azure.microsoft.com/documentation/services/log-analytics/) et de la [Suite de gestion des opérations](https://www.microsoft.com/cloud-platform/operations-management-suite)
