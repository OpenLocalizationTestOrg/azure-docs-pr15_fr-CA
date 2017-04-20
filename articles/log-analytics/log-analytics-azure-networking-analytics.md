<properties
    pageTitle="Azure solution Analytique de mise en réseau dans un journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la solution Analytique de réseau Azure dans journal Analytique pour examiner les journaux du groupe de sécurité réseau Azure et journaux de passerelle d’Application Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="richrund"/>

# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Solution de réseau Analytique (aperçu) Azure dans journal Analytique

>[AZURE.NOTE] Il s’agit d’une [solution d’aperçu](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

La solution Analytique de réseau Azure dans journal Analytique vous permet de passer en revue les journaux de passerelle d’Application Azure et des journaux du groupe de sécurité réseau Azure.

Vous pouvez activer l’enregistrement pour les journaux de la passerelle d’Application Azure et les groupes de sécurité de réseau Azure. Ces journaux sont écrits de stockage où ils peuvent ensuite être indexées par Analytique du journal pour la recherche et l’analyse de la tache.

Les fichiers journaux suivants sont pris en charge pour les passerelles d’Application :

+ ApplicationGatewayAccessLog
+ ApplicationGatewayPerformanceLog

Les fichiers journaux suivants sont pris en charge pour les groupes de sécurité réseau :

+ NetworkSecurityGroupEvent
+ NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Installation et configuration de la solution

Suivez les instructions ci-dessous pour installer et configurer la solution Analytique de réseau Azure :

1.  Activer la journalisation de diagnostics pour les ressources que vous souhaitez surveiller :
  + [Passerelle d’application](../application-gateway/application-gateway-diagnostics.md)
  + [Groupe de sécurité réseau](../virtual-network/virtual-network-nsg-manage-log.md)
2.  Configuration Analytique de journal pour lire les journaux depuis le stockage Blob à l’aide du processus décrit dans les [fichiers JSON dans le stockage blob](../log-analytics/log-analytics-azure-storage-json.md).
3.  Activer la solution Analytique de réseau Azure à l’aide du processus décrit dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  

Si vous n’activez pas l’enregistrement des diagnostics pour un type de ressource particulier, les lames de tableau de bord de cette ressource sera vides.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Examinez les détails de collection de données Analytique de réseau Azure

La solution Analytique de réseau Azure collecte des journaux de diagnostics de stockage des objets Blob Azure pour les groupes de sécurité de réseau et les passerelles d’Application Azure.
Aucun agent n’est requis pour la collecte des données.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la façon dont les données sont collectées pour Analytique de réseau Azure.

| Plate-forme | Agent direct | Agent de Systems Center Operations Manager (SCOM) | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | Fréquence de collection |
|---|---|---|---|---|---|---|
|Azure|![N°](./media/log-analytics-azure-networking/oms-bullet-red.png)|![N°](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Oui](./media/log-analytics-azure-networking/oms-bullet-green.png)|            ![N°](./media/log-analytics-azure-networking/oms-bullet-red.png)|![N°](./media/log-analytics-azure-networking/oms-bullet-red.png)| 10 minutes|

## <a name="use-azure-networking-analytics"></a>Utilisez Azure Analytique de mise en réseau

Après l’installation de la solution, vous pouvez afficher le résumé du client et des erreurs de serveur pour vos passerelles d’Application contrôlée à l’aide de l' **Analytique de réseau Azure** en mosaïque sur la page de **vue d’ensemble** de journal Analytique.

![image de mosaïque d’Analytique de réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Une fois que vous cliquez sur la vignette de la **vue d’ensemble** , vous pouvez afficher des synthèses de vos journaux et puis Explorer les détails pour les catégories suivantes :

+ Application passerelle Access connecte
  - Erreurs de client et le serveur de passerelle d’Application accéder aux journaux
  - Nombre de requêtes par heure pour chaque Application Gateway
  - Échec de demandes par heure pour chaque Application Gateway
  - Erreurs commises par l’agent utilisateur pour les passerelles d’Application
+ Performances de passerelle d’application
  - Santé pour la passerelle d’Application hôte
  - Demandes qui ont échoué maximale et 95ème centile pour la passerelle d’Application
+ Groupe de sécurité réseau bloqué de flux
  - Règles de groupe de sécurité de réseau avec des flux bloqués
  - Adresses MAC avec flux bloqués
+ Groupe de sécurité réseau autorisé de flux
  - Règles de groupe de sécurité de réseau avec des flux autorisés
  - Adresses MAC avec flux autorisés


![image du tableau de bord Analytique de réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![image du tableau de bord Analytique de réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![image du tableau de bord Analytique de réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![image du tableau de bord Analytique de réseau Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Pour afficher les détails de tout résumé des journaux

1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque **Analytique de mise en réseau d’Azure** .
2. Sur le tableau de bord **Analytique de mise en réseau d’Azure** , consultez les informations résumées de l’une des lames et puis cliquez sur une option pour afficher des informations détaillées le concernant dans la page de recherche de journal.

    Sur toutes les pages de recherche de journal, vous pouvez afficher les résultats en temps, des résultats détaillés et votre historique de recherche de journal. Vous pouvez également filtrer par facettes pour limiter les résultats.

## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher les données d’Analytique de réseau Azure détaillées.
