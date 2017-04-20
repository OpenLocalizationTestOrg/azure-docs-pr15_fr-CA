<properties
    pageTitle="Solution d’évaluation de configuration de journal Analytique | Microsoft Azure"
    description="La solution d’évaluation de la Configuration dans le journal Analytique vous fournit des informations détaillées sur l’état actuel de votre infrastructure de serveur de System Center Operations Manager lors de l’utilisation d’agents d’Operations Manager ou d’un groupe d’administration Operations Manager."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="configuration-assessment-solution-in-log-analytics"></a>Solution d’évaluation de configuration de journal Analytique

La solution d’évaluation de la Configuration dans le journal Analytique vous aide à détecter les problèmes potentiels de configuration serveur via des alertes et des recommandations de connaissances.

Cette solution nécessite de System Center Operations Manager. Évaluation de la configuration n’est pas disponible si vous utilisez uniquement les agents directement connecté.

Affichage des informations dans une solution d’évaluation de la Configuration de nécessite le plug-in Silverlight pour votre navigateur.

>[AZURE.NOTE] Début du 5 juillet 2016, la solution d’évaluation de la Configuration n’est plus peut être ajoutée qu’aux espaces de travail de journal Analytique et cette solution ne sera plus disponible pour les utilisateurs existants après le 1 août 2016. Pour les clients à l’aide de cette solution pour SQL Server ou Active Directory, nous vous recommandons de que vous utilisez à la place de [l’Évaluation de SQL Server](log-analytics-sql-assessment.md), les solutions [d’Évaluation de Active Directory](log-analytics-ad-assessment.md) et [l’État de la réplication Active Directory](log-analytics-ad-replication-status.md) . Pour les clients à l’aide d’évaluation de la configuration de Windows, Hyper-V et System Center Virtual Machine Manager, nous vous recommandons d’utiliser la collection d’événements et de fonctionnalités pour obtenir une vue globale des problèmes dans votre environnement de suivi des modifications.

![Mosaïque d’évaluation de configuration](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Collectées à partir des serveurs analysés et ensuite envoyées au service OMS dans le nuage pour le traitement de données. La logique est appliquée pour les données reçues et le service en nuage enregistre les données. Les données traitées pour les serveurs sont affichées dans les domaines suivants :

- **Alertes :** Affiche les alertes liées à la configuration et proactives qui ont été générées pour les serveurs surveillés. Ces produits par les règles créées par Microsoft Customer et l’organisation de Support (CSS) avec les meilleures pratiques à partir du champ.
- **Recommandations de connaissances :** Affiche les articles de la Base de connaissances Microsoft qui sont recommandés pour les charges de travail qui sont trouvent dans votre infrastructure. ceux-ci sont automatiquement suggérés en fonction de votre configuration via l’utilisation de l’apprentissage de l’ordinateur.
- **Analysés des charges de travail et serveurs :** Affiche les serveurs et les charges de travail qui sont analysés par l’OMS.

![Tableau de bord de configuration évaluation](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### <a name="technologies-you-can-analyze-with-configuration-assessment"></a>Vous pouvez analyser avec évaluation de la Configuration des technologies

Évaluation de Configuration OMS analyse les charges de travail suivants :

- Windows Server 2012 et Microsoft Hyper-V Server 2012
- Windows Server 2008 et Windows Server 2008 R2, y compris :
    - Active Directory
    - Hôte Hyper-V
    - Système d’exploitation général
- SQL Server 2008 et versions ultérieur
    - Moteur de base de données SQL Server
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 et Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 et Lync Server 2010
- System Center 2012 SP1 – Virtual Machine Manager

Pour SQL Server, les éditions 32 bits et 64 bits suivantes sont pris en charge pour l’analyse :

- SQL Server 2016 - toutes les éditions
- SQL Server 2014 - toutes les éditions
- 2008 et 2008 R2 - toutes les éditions de SQL Server

Le moteur de base de données SQL Server est analysé sur toutes les éditions prises en charge. En outre, l’édition 32 bits de SQL Server est pris en charge lors de l’exécution de la mise en oeuvre de WOW64.

## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Operations Manager est requis pour la solution d’évaluation de la Configuration.
- Vous devez disposer d’un agent Operations Manager sur chaque ordinateur où vous souhaitez évaluer sa configuration.
- Ajouter la solution d’évaluation de la Configuration de votre espace de travail de l’OMS à l’aide de la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.

## <a name="configuration-assessment-data-collection-details"></a>Détails de la collection évaluation des données de configuration

Évaluation de la configuration collecte des données de configuration, les métadonnées et les données d’état à l’aide d’agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour l’évaluation de la Configuration.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows|![N°](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![Oui](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![N°](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|            ![Oui](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Oui](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| deux fois par jour|

Le tableau suivant montre des exemples de types de données collectées par l’évaluation de la Configuration :

|**Type de données**|**Champs**|
|---|---|
|Configuration de|CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate|
|Métadonnées|BaseManagedEntityId, ObjectStatus, unité d’organisation, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, Nom_ordinateur_netbios, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, adresse IP, NetbiosDomainName, LogicalProcessors, NomDNS, DisplayName, DomainDnsName, ActiveDirectorySite, au PrincipalName, OffsetInMinuteFromGreenwichTime|
|État|StateChangeEventId, StateId, NewHealthState, OldHealthState, contexte, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="configuration-assessment-alerts"></a>Alertes de configuration d’évaluation
Vous pouvez afficher et gérer les alertes à partir de l’évaluation de la Configuration avec la page alertes. Alertes vous indiquent le problème a été détecté, la cause et comment résoudre le problème. Elles fournissent également des informations sur les paramètres de configuration de votre environnement qui peut engendrer des problèmes de performances.

![affichage des alertes](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] Les alertes d’évaluation de Configuration sont différents des autres alertes dans le journal Analytique. Affichage des alertes de nécessite un plug-in Silverlight pour votre navigateur.

Lorsque vous sélectionnez un article ou une catégorie d’éléments sur la page alertes, vous verrez une liste de serveurs ou des charges de travail avec des alertes qui s’appliquent à chaque élément.

![liste d’alertes](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Chaque alerte inclut un lien vers un article de la Base de connaissances Microsoft. Ces articles fournissent des informations supplémentaires sur l’alerte.

>[AZURE.TIP] Par défaut, le nombre maximal d’alertes affichées est de 2 000. Pour afficher plus d’alertes, cliquez sur la barre de notification au-dessus de la liste des alertes.

Vous pouvez cliquer sur n’importe quel élément dans la liste pour afficher l’article de la base de connaissances qui peut-être vous aider à résoudre la cause du problème qui a généré l’alerte.

![Article de la base de connaissances](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Vous pouvez gérer les règles d’alerte pour ignorer les règles spécifiques ou une classe de règles.

![gérer les règles d’alerte](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## <a name="knowledge-recommendations"></a>Recommandations de connaissances
Lorsque vous affichez les recommandations de connaissances, vous propose des résultats de recherche de journal répertoriant les articles Microsoft KB recommandés pour les charges de travail et les ordinateurs qui fournissent des informations supplémentaires sur l’alerte.

![résultats de la recherche pour les recommandations de connaissances](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## <a name="servers-and-workloads-analyzed"></a>Analysé des charges de travail et serveurs
Lorsque vous affichez les recommandations de connaissances, vous propose des résultats de recherche de journal répertoriant tous les serveurs et les charges de travail qui sont connus pour OMS d’Operations Manager.

![Les charges de travail et les serveurs](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher les données d’évaluation de configuration détaillées.
