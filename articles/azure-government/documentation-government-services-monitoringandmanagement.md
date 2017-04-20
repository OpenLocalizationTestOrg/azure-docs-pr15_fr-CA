<properties
    pageTitle="Documentation de gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des conseils sur le développement d’applications pour Azure gouvernement."
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Gestion et surveillance de gouvernement Azure

Cet article présente la surveillance et les services de gestion des variations et des considérations sur l’environnement du gouvernement d’Azure.

## <a name="automation"></a>Automation

Automation est généralement disponible dans Azure.

### <a name="variations"></a>Variations

Les fonctionnalités d’automatisation suivantes ne sont pas actuellement disponibles dans Azure.

+ Création d’une information d’identification de SPN pour l’authentification

Pour plus d’informations, consultez la [documentation publique d’Automation](../automation/automation-intro.md).

## <a name="log-analytics"></a>Analytique de journal

Analytique de journal est généralement disponible dans Azure.

### <a name="variations"></a>Variations

Les fonctionnalités du journal Analytique et les solutions suivantes ne sont pas actuellement disponibles dans Azure.

+ Solutions en aperçu dans Microsoft Azure, y compris :
  - Solution de surveillance de réseau
  - Solution de surveillance des dépendances d’application
  - Solution Office 365
  - Solutions d’Analytique de mise à niveau Windows 10
  - Solution d’idées d’applications
  - Solution de mise en réseau d’Analytique Azure
  - Solution d’automatisation Analytique Azure
  - Solution de clé coffre-fort Analytique
+ Les solutions et les fonctionnalités qui nécessitent des mises à jour sur site, logiciel, y compris :
  - Intégration avec le système Centre Operations Manager 2016 (les versions antérieures d’Operations Manager sont pris en charge)
  - Groupes d’ordinateurs à partir de System Center Configuration Manager.
  - Solution de concentrateur de surface
+ Fonctionnalités dans Aperçu dans Azure public, y compris :
  - Exportation de données vers l’alimentation BI
+ Mesures Azure et les diagnostics de Windows Azure
+ Applications mobiles Operations Management Suite

Toutes les URL d’Analytique de journal sont différents dans Azure gouvernement :

| Public Azure | Gouvernement Azure | Notes |
|--------------|------------------|-------|
| MMS.Microsoft.com | OMS.Microsoft.us | Portail d’Analytique de journal |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Collecteur de données API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |


Les fonctionnalités suivantes de journal Analytique se comportent différemment dans Azure :

+ L’Agent Windows doivent être téléchargé à partir du [portail de journal Analytique](https://oms.microsoft.us) pour Azure gouvernement.
+ Pour vous connecter à votre serveur de gestion de System Center Operations Manager pour l’Analytique de journal, vous devez télécharger et importer les packs d’administration mis à jour.
  1. Téléchargez et enregistrez la [mise à jour des packs d’administration](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Décompressez le fichier que vous avez téléchargé.
  3. Importer les packs d’administration dans Operations Manager. Pour plus d’informations sur l’importation d’un pack d’administration à partir d’un disque, voir [Comment faire pour importer un Pack d’administration Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) sur le site Web TechNet de Microsoft.
  4. Pour vous connecter à Operations Manager au journal Analytique, suivez les étapes de [Connexion Operations Manager pour l’Analytique de journal](../log-analytics/log-analytics-om-agents.md).


## <a name="frequently-asked-questions"></a>Forum aux questions

+ Puis-je migrer les données d’Analytique de journal dans Microsoft Azure au gouvernement d’Azure ?
  - N° Il n’est pas possible de déplacer des données ou votre espace de travail de Microsoft Azure au gouvernement d’Azure.
+ Puis-je passer Microsoft Azure et d’Azure gouvernement espaces de travail à partir du portail Operations Management Suite journal Analytique ?
  - N° Les portails pour Microsoft Azure et d’Azure gouvernement sont distincts et ne partagent pas d’informations.

Pour plus d’informations, consultez la [documentation de public de journal Analytique](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, abonnez-vous à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure gouvernement Blog.</a>
