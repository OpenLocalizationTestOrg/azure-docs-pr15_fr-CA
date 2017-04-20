<properties
    pageTitle="Vue d’ensemble des mesures dans Microsoft Azure | Microsoft Azure"
    description="Vue d’ensemble des mesures et leurs utilisations dans Microsoft Azure"
    authors="kamathashwin"
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
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Vue d’ensemble des mesures dans Microsoft Azure 

Cet article décrit les mesures de Microsoft Azure, leurs avantages et comment commencer à les utiliser.  

## <a name="what-are-metrics"></a>Quelles sont les mesures ?

Azure moniteur vous permet de consommer de télémétrie pour la visibilité sur les performances et l’intégrité de vos charges de travail sur Azure. Le type le plus important de données télémétriques Azure sont les métriques (également appelées compteurs de performance) émis par les ressources plus Azure. Moniteur Azure fournit plusieurs façons de configurer et d’utiliser ces mesures de surveillance et de dépannage.


## <a name="what-can-you-do-with-metrics"></a>Que pouvez-vous faire avec des mesures ?

Les mesures sont une source précieuse de télémétrie et vous permettent d’effectuer les opérations suivantes :

- **Suivre les performances** de la ressource (par exemple, un ordinateur virtuel, un site Web ou une logique d’application) par un traçage de ses mesures sur un portail graphique et l’épinglage de ce graphique à un tableau de bord.
- **Être averti d’un problème** ayant un impact sur les performances de vos ressources lorsqu’une mesure dépasse un certain seuil.
- **Configurer les actions automatique**, comme autoscaling, une ressource ou le déclenchement d’une procédure opérationnelle lorsqu’une mesure dépasse un certain seuil.
- **Effectuer avancée analytique** ou création de rapports sur les tendances de performances ou d’utilisation de vos ressources.
- **Archive** l’historique de performances ou l’intégrité de vos ressources **de conformité/audit** à des fins.

##  <a name="metric-characteristics"></a>Caractéristiques de métriques
Les mesures ont les caractéristiques suivantes :

- Toutes les mesures ont de **fréquence de 1 minute**. Vous recevez une valeur métrique chaque minute de votre ressource, pratiquement une visibilité en temps réel de l’état de santé de votre ressource.
- Les métriques sont **disponible - de-l’emploi sans avoir besoin de participer** ou de la configuration des diagnostics supplémentaires.
- Vous pouvez accéder à **30 jours d’historique** pour chaque mesure. Vous pouvez rapidement consulter les tendances récentes et mensuelles dans les performances ou l’intégrité de votre ressource.

Vous pouvez :

- Découvrir facilement, accès et **Afficher tous les paramètres** via le portail Azure lorsque vous sélectionnez une ressource et les tracer sur un graphique. 
- Configurer une **règle d’alerte envoie une notification ou exécute une action automatisée** de métriques lors de la mesure dépasse le seuil que vous avez défini. Échelle automatique est une action automatique spéciale qui vous permet de faire évoluer votre ressource afin de répondre à des demandes ou de charger sur votre site web ou de ressources de calcul. Vous pouvez configurer une règle de paramètre échelle automatique à l’échelle en/hors basé sur une mesure de franchissement d’un seuil.
- Mesures de **l’archive** pour plus ou les utiliser pour le rapport en mode hors connexion. Vous pouvez router vos mesures de stockage BLOB lorsque vous configurez les paramètres de diagnostic pour votre ressource.
- Mesures de **flux** à un concentrateur d’événements, ce qui vous permet ensuite de les acheminer vers Azure flux Analytique ou des applications personnalisées pour l’analyse de presque en temps réel. Vous pouvez effectuer à l’aide de la paramètres de diagnostic.
- **Gamme de** toutes les mesures pour l’Analytique de journal OMS pour déverrouiller instantanée analytique, de recherche et d’alerte personnalisés sur les données de métrique de vos ressources.
- **Consommer** les métriques via les nouvelles API de reste de moniteur Azure.
- Indicateurs de **requête** utilisant les PowerShell Cmdlets ou l’API REST de multiplates-formes.

 ![Routage des mesures dans le moniteur Azure](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Mesures d’accès via le portail
Voici un bref aperçu de la création d’un graphique métrique via le portail Azure

### <a name="view-metrics-after-creating-a-resource"></a>Affichage des mesures après la création d’une ressource
1. Ouvrir portail Azure
2. Créer un nouveau App Service - Site Web.
3. Après avoir créé un site Web, accédez à la lame de vue d’ensemble du site web.
4. Vous pouvez afficher les nouvelles mesures sous forme de mosaïque « Surveillance ». Vous pouvez modifier la mosaïque et sélectionnez plus de mesures

 ![Mesures sur une ressource dans un moniteur de Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Accéder à toutes les mesures dans un seul emplacement
1. Ouvrir le portail Azure 
2. Accédez à l’onglet Moniteur, puis sélectionnez l’option mesures sous elle 
3. Vous pouvez sélectionner votre abonnement, groupe de ressources et le nom de la ressource dans la liste déroulante. 
4. Vous pouvez maintenant afficher la liste mesures disponibles. 
5. Sélectionnez la mesure qui vous intéressez et que vous tracez. 
6. Vous pouvez épingler au tableau de bord en cliquant sur le code confidentiel dans le coin supérieur droit.

 ![Accéder à toutes les mesures en un seul endroit dans le moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] Vous pouvez accéder de mesures au niveau de l’hôte à partir d’ordinateurs virtuels (le Gestionnaire de ressources Azure basé) et jeux d’échelle de machine virtuelle sans aucun paramétrage de diagnostic supplémentaires. Ces nouvelles mesures au niveau de l’hôte sont disponibles pour les instances de Windows et Linux. Ces mesures ne doivent ne pas être confondus avec les mesures de niveau-système d’exploitation invité auxquelles vous avez accès à lorsque vous allumez Diagnostics Azure sur vos ordinateurs virtuels ou les VMSS. Pour plus d’informations sur la configuration des Diagnostics d’Azure, consultez [Microsoft Azure Diagnostics](../azure-diagnostics.md).

## <a name="access-metrics-via-rest-api"></a>Mesures d’accès via l’API REST
Mesures Azure sont accessibles via les API du moniteur Azure. Il existe deux API qui vous aide à découverte et mesures. Utilisez le : 

- [Définitions de mesure du moniteur azure API REST](https://msdn.microsoft.com/library/mt743621.aspx) pour accéder à la liste de mesures disponibles pour un service.
- [Azure moniteur métriques reste API](https://msdn.microsoft.com/library/mt743622.aspx) pour accéder aux données métriques réelles

>[AZURE.NOTE] Cet article décrit les mesures via [nouvelle API pour les mesures](https://msdn.microsoft.com/library/dn931930.aspx) de ressources Azure. La version de l’API pour les nouvelles API de définitions métriques est 2016-03-01 et que la version pour les mesures de l’API est 2016-09-01. Les anciennes définitions de métriques et la métrique sont accessibles avec la version api 2014-04-01.

Pour une procédure pas à pas plus détaillées à l’aide de l’API reste du moniteur Azure, consultez [Procédure pas à pas de Azure moniteur reste API](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Options d’exportation pour les mesures
Vous pouvez accéder à la lame de journaux de Diagnostics sous l’onglet Moniteur et afficher les options d’exportation pour les mesures. Vous pouvez sélectionner des mesures (et les journaux de diagnostic) à être acheminés vers le stockage Blob, événement concentrateurs ou Analytique de journal OMS pour utilisations mentionnées précédemment dans cet article. 

 ![Options d’exportation pour les mesures dans le moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Vous pouvez configurer cette fonction via le Gestionnaire de ressources des modèles, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) ou [Autres API](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Entreprendre des actions sur les mesures
Vous pouvez recevoir des notifications ou entreprenez automatiquement des actions sur les données de métrique. Vous pouvez configurer des règles d’alerte ou des paramètres d’échelle pour le faire.

### <a name="alert-rules"></a>Règles d’alerte
Vous pouvez configurer des règles d’alerte sur les mesures. Ces règles d’alerte peuvent vérifier si une valeur métrique a atteint un certain seuil et vous avertir par e-mail ou déclencher un webhook qui peut ensuite être utilisé pour exécuter tout script personnalisé. Vous pouvez également utiliser le webhook pour configurer les intégrations produit 3.

 ![Les mesures et les règles d’alerte dans le moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Échelle automatique
Certaines ressources Azure prend en charge plusieurs instances pour mettre à l’échelle ou pour gérer vos charges de travail. Échelle automatique s’applique aux Services d’application (applications Web), jeux d’échelle d’ordinateur virtuel (VMSS) et les Services en nuage classique. Vous pouvez configurer des règles d’autoscale mise à l’échelle ou lorsqu’une mesure de certaine perturber votre charge de travail dépasse un seuil que vous spécifiez. Pour plus d’informations, consultez [vue d’ensemble d’autoscaling](monitoring-overview-autoscale.md).

 ![Métriques et échelle dans Azure moniteur](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Les mesures et les Services pris en charge
Moniteur Azure est une nouvelle infrastructure de mesures. Il prend en charge les services suivants Azure dans le portail Azure et la nouvelle version de l’API de moniteur Azure :

- Ordinateurs virtuels (le Gestionnaire de ressources Azure en fonction)
- Jeux d’échelle de machine virtuelle
- Traitement par lots
- Événement concentrateur namespace 
- Bus de service, espace de noms (référence SKU premium uniquement)
- SQL (version 12)
- Pool SQL élastique
- Sites Web
- Batteries de serveurs Web
- Applications de logique
- Concentrateurs IoT
- Redis de Cache
- Mise en réseau : Passerelles d’Application
- Recherche

Vous pouvez afficher une liste détaillée de tous les services pris en charge et leurs mesures au [Moniteur d’Azure métriques - des mesures prises en charge par le type de ressource](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Étapes suivantes

Reportez-vous aux liens dans cet article. En outre, découvrez :  

- à propos [des mesures communes pour autoscaling](insights-autoscale-common-metrics.md)
- Comment faire pour [créer des règles d’alerte](insights-alerts-portal.md)




