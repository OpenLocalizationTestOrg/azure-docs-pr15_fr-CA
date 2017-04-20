<properties
   pageTitle="Intégration à la Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="En plus d’utiliser les fonctionnalités standard de l’OMS, vous pouvez l’intégrer avec d’autres applications de gestion et de services pour fournir un environnement de gestion hybride, afin de fournir des scénarios de gestion personnalisées uniques à votre environnement, ou pour fournir une gestion personnalisée de l’expérience pour vos clients.  Cet article fournit une vue d’ensemble de vos différentes options pour l’intégration avec l’OMS et les liens vers les articles fournissant des informations techniques détaillées."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>Intégration à la Suite de gestion des opérations (OMS)

Suite de gestion des opérations est en nuage solution de gestion informatique de Microsoft qui vous aide à gérer et protéger vos locaux et cloud d’infrastructure.  En plus d’utiliser les fonctionnalités standard de l’OMS, vous pouvez l’intégrer avec d’autres applications de gestion et de services pour fournir un environnement de gestion hybride, afin de fournir des scénarios de gestion personnalisées uniques à votre environnement, ou pour fournir une gestion personnalisée de l’expérience pour vos clients.  Cet article fournit une vue d’ensemble de vos différentes options pour l’intégration avec les services de l’OMS et les liens vers les articles fournissant des informations techniques détaillées. 



## <a name="log-analytics"></a>Analytique de journal
Les données de gestion collectées par Analytique du journal sont stockées dans un référentiel qui est hébergé dans Azure.  Toutes les données stockées dans le référentiel est disponible dans les recherches de journaux qui fournissent une analyse rapide sur très grandes quantités de données.  Vos besoins d’intégration peuvent être pour alimenter le référentiel avec de nouvelles données, ce qui la rend disponible pour l’analyse, ou pour extraire des données dans le référentiel afin de fournir une nouvelle visualisation ou pour intégrer avec un autre outil de gestion.

Chaque élément de données dans le référentiel est stocké sous la forme d’un enregistrement.  Lorsque vous remplissez le référentiel, vous devez fournir aux utilisateurs le type d’enregistrement par votre solution et une description de ses propriétés.  Lorsque vous récupérez des données, vous avez besoin de ces informations sur les données que vous manipulez.

![Remplir le référentiel de l’OMS](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>Remplir le référentiel Analytique du journal
Il existe plusieurs méthodes permettant de remplir le référentiel de l’OMS.  La méthode que vous utilisez dépend de facteurs tels qu’où se trouve la source de données, le format des données et dont vous avez besoin pour prendre en charge des clients.  Une fois que les données sont stockées dans le référentiel, il ne fait aucune différence comment elles ont été collectées.

Les sections suivantes décrivent les différentes options permettant de remplir le référentiel de l’OMS.

#### <a name="connected-sources-and-data-sources"></a>Sources de données et de Sources connectées 
Sources connectées sont les emplacements où les données peuvent être extraites du référentiel de l’OMS.  Les Solutions et les Sources de données exécutent sur Sources connectées et définissent les données collectées.  Si votre application écrit des données dans une de ces sources de données, puis vous pouvez collecter par la configuration de la source de données.  Par exemple, si votre application crée un événement Syslog, puis elles peuvent être collectées par la source de données Syslog sur un agent de Linux.

- [Sources de données dans le journal Analytique](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Solutions

Solutions étendent les capacités de l’OMS.  Une solution peut collecter des données à partir de la source connectée ou elle peut effectuer une analyse sur les enregistrements déjà collectés dans le référentiel.  Chaque solution fournie par Microsoft a un article particulier qui fournit des détails sur les données qu’il collecte.

- [Solutions de journal Analytique](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>Collecteur de données HTTP API

L’API de collecteur de données de journal Analytique HTTP est une API REST qui vous permet d’ajouter des données JSON au référentiel Analytique du journal.  Vous pouvez tirer parti de cette API lorsque vous avez une application qui ne fournit pas de données à l’aide d’une des autres sources de données ou solutions.  Il peut être utilisé pour alimenter le référentiel à partir de n’importe quel client peut appeler l’API qui ne repose pas sur la planification de la collecte d’une source de données ou d’une solution.

- [Collecteur de données HTTP Analytique journal API](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>Récupérer des données à partir du référentiel Analytique du journal

Il existe plusieurs méthodes pour récupérer des données à partir du référentiel de l’OMS.  Vous pouvez souhaitez que les utilisateurs à récupérer des données à l’aide de la console de l’OMS et leur fournir différents types de visualisation et d’analyse.  Vous pouvez également récupérer les données à partir d’un processus externe, tel qu’une autre solution de gestion.

#### <a name="log-searches"></a>Recherches de journaux

Toutes les données stockées dans le référentiel de l’OMS est disponible par le biais de recherches de journaux.  Les utilisateurs peuvent effectuer leur propre analyse ad hoc dans la console de l’OMS ou créer un tableau de bord avec une visualisation pour une recherche de journal spécifique.  Les solutions peuvent contenir des affichages personnalisés avec des visualisations en fonction de critères de recherche prédéfinis.  Vous pouvez utiliser l’API de recherche de journal pour accéder aux données dans le référentiel de l’OMS, à partir d’un outil de gestion ou d’application externe.  

- [Recherche de journal de journal Analytique](../log-analytics/log-analytics-log-searches.md)
- [Recherche de journal de journal Analytique API REST](../log-analytics/log-analytics-log-search-api.md)
- [Applets de commande de journal Analytique](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>Affichages personnalisés 
Le Concepteur de vue vous permet de créer des affichages personnalisés dans la console de l’OMS qui fournissent aux utilisateurs avec la visualisation et l’analyse des données dans votre solution.  Chaque vue contient une mosaïque qui s’affiche sur la page principale de la console et n’importe quel nombre de parties de visualisation qui sont basés sur des recherches de journaux que vous définissez.
  
- [Concepteur de vue Analytique de journal](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Alimentation BI

Analytique de journal peut exporter automatiquement des données à partir du référentiel de l’OMS en puissance BI pour valoriser ses visualisations et les outils d’analyse.  Il effectue cette exportation selon un planning pour le maintien des données à jour. 

- [Exporter des données d’Analytique de journal BI de puissance](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>Automation

OMS peuvent automatiser le processus pour réagir aux données collectées ou pour exécuter d’autres fonctions de gestion.  Il peut également collecter des données à partir de votre application et l’insérer dans le référentiel de l’OMS, ou vous pouvez l’automatiser la correction d’un problème connu en réponse aux données dans le référentiel. 

![Automation](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Procédures opérationnelles

Procédures opérationnelles dans Azure Automation exécutent des scripts PowerShell et les flux de travail dans le cloud Azure.  Vous pouvez les utiliser pour gérer les ressources dans Azure ou autres ressources qui sont accessibles à partir du cloud.  Procédures opérationnelles peut également être exécuté dans un centre de données local à l’aide de travailleur de procédure opérationnelle hybride.  Vous pouvez démarrer une procédure opérationnelle à partir du portail Azure, ou à partir de processus externes à l’aide d’un certain nombre de méthodes de PowerShell ou l’API d’automatisation.

- [Démarrage d’une procédure opérationnelle dans Azure Automation](../automation/automation-starting-a-runbook.md)
- [Applets de commande Automation Azure](https://msdn.microsoft.com/library/dn690262.aspx)
- [API d’automatisation d’autres](https://msdn.microsoft.com/library/mt662285.aspx)
- [Automation .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertes

Règles d’alerte exécutent automatiquement selon une planification des recherches de journaux.  Si les résultats correspondent à des critères particuliers l’alerte qui en résulte peut démarrer une procédure opérationnelle dans Azure Automation ou appeler un webhook qui peut démarrer un processus externe.  Deux de ces réponses peuvent inclure des détails de l’alerte, y compris les données renvoyées dans les journaux.

- [Alertes dans le journal Analytique](../log-analytics/log-analytics-alerts.md)
- [API alerte Analytique de journal](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>Sauvegarde et récupération de Site

Azure de sauvegarde et de restauration de Site fournissent des services de protection de vos données d’entreprise et de veiller à la disponibilité des serveurs et des applications.  Vous pouvez tirer parti de ces services pour exécuter des scénarios tels que la fourniture de services de sauvegarde pour votre application ou l’initiation du basculement d’un ordinateur virtuel.

- [Applets de commande de sauvegarde Azure](https://msdn.microsoft.com/library/mt619253.aspx)
- [Récupération de Site Azure API REST](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Applets de commande de récupération Site Azure](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Solutions personnalisées

Vous pouvez encapsuler la logique d’intégration dans une solution personnalisée pour s’exécuter dans votre espace de travail ou dans l’espace de travail du client.  Votre solution peut inclure une des méthodes d’intégration dans cet article, en plus des autres ressources afin de fournir un scénario de gestion complète.  Les ressources de la solution sont fournies telles que lorsque la solution est supprimée, toutes les ressources qu’il a créé sont supprimés de l’espace de travail de l’OMS et l’abonnement Azure.

Par exemple, votre solution peut inclure une procédure d’automatisation opérationnelle pour collecter et traiter les données et puis de remplir le référentiel Analytique du journal à l’aide de l’API de collecteur de données HTTP.  Vous pouvez également inclure un affichage personnalisé qui présente et analyse les données collectées.  

- Création de solutions personnalisées (prochainement)    

## <a name="next-steps"></a>Étapes suivantes
- Référence du [Kit de développement logiciel OMS](operations-management-suite-sdk.md) pour des informations techniques sur l’automatisation des services de l’OMS.  
