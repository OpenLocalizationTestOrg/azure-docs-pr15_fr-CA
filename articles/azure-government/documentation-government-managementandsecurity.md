<properties
    pageTitle="Documentation de gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des conseils sur le développement d’applications pour Azure gouvernement"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="scooxl"/>
#  <a name="azure-government-management-and-security"></a>Sécurité et gestion de gouvernement azure

## <a name="automation"></a>Automation

Automation est généralement disponible dans Azure.

### <a name="variations"></a>Variations

Les fonctionnalités d’automatisation suivantes ne sont pas actuellement disponibles dans Azure.

+ Création d’une information d’identification de SPN pour l’authentification

Pour plus d’informations, consultez la [documentation publique d’Automation](../automation/automation-intro.md).


##  <a name="key-vault"></a>Chambre forte de clé
Pour plus d’informations sur ce service et son utilisation, consultez la <a href="https://azure.microsoft.com/documentation/services/key-vault">documentation publique d’Azure clé coffre-fort.</a>
### <a name="data-considerations"></a>Considérations sur les données
Les informations suivantes identifient la frontière Azure gouvernement pour Azure clé coffre-fort :

| Données réglementé/contrôlé autorisé | Données réglementé/contrôlé non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données cryptées avec une clé de coffre-fort de clé Azure peuvent contenir des données/réglementés. | Les métadonnées de stockage en chambre forte de clé Azure ne sont pas autorisée à contenir des données d’exportation contrôlée. Ces métadonnées incluent toutes les données de configuration saisies lors de la création et la gestion de votre stockage en chambre forte de clé.  N’entrez pas réglementés/contrôle des données dans les champs suivants : noms de groupes de ressources, les noms de clé coffre-fort, nom de l’abonnement |

Clé de stockage en chambre forte est généralement disponible dans Azure gouvernement. Comme dans le public, est sans extension, coffre-fort de clé est uniquement disponible par le biais de PowerShell et CLI.
## <a name="log-analytics"></a>Analytique de journal
Analytique de journal est généralement disponible dans Azure. 

### <a name="variations"></a>Variations

Les fonctionnalités du journal Analytique et les solutions suivantes ne sont pas actuellement disponibles dans Azure. Cette liste est mise à jour lorsque l’état des fonctions / solutions de modifications.

+ Solutions en aperçu dans Azure public, y compris :
  - Solution de surveillance de réseau
  - Contrôle de dépendance des applications
  - Solution Office 365
  - Solutions d’Analytique de mise à niveau Windows 10
  - Aperçu de l’application
  - Solution de mise en réseau d’Analytique Azure
  - Automation Azure Analytique
  - Chambre forte de clé Analytique
+ Les solutions et les fonctionnalités qui nécessitent des mises à jour sur site, logiciel, y compris
  - Intégration avec le système Centre Operations Manager 2016 (les versions antérieures d’Operations Manager sont pris en charge)
  - Groupes d’ordinateurs à partir de System Center Configuration Manager
  - Solution de concentrateur de surface
+ Fonctionnalités dans Aperçu dans Azure public, y compris
  - Exportation de données vers PowerBI
+ Mesures Azure et les diagnostics de Windows Azure
+ Applications mobiles de l’OMS

Toutes les URL d’Analytique de journal sont différents dans Azure gouvernement :

| Public Azure | Gouvernement Azure | Notes |
|--------------|------------------|-------|
| MMS.Microsoft.com | OMS.Microsoft.us | Portail d’Analytique de journal |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Collecteur de données API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Communication de l’agent - [Configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |


Les fonctionnalités d’Analytique de journal suivantes ont un comportement différent dans Azure :

+ L’agent Windows doit être téléchargé à partir du [portail de journal Analytique](https://oms.microsoft.us) pour Azure gouvernement.
+ Pour vous connecter à votre serveur de gestion de System Center Operations Manager pour l’Analytique de journal, vous devez télécharger et importer les Packs d’administration mis à jour.
  1. Téléchargez et enregistrez la [mise à jour des packs d’administration](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Décompressez le fichier que vous avez téléchargé
  3. Importer les packs d’administration dans Operations Manager. Pour plus d’informations sur l’importation d’un pack d’administration à partir d’un disque, voir la rubrique [Comment faire pour importer un Pack d’administration Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) sur le site Web TechNet de Microsoft.
  4. Pour vous connecter à Operations Manager au journal Analytique, suivez les étapes de [Connexion Operations Manager pour journal Analytique](../log-analytics/log-analytics-om-agents.md) 



### <a name="frequently-asked-questions"></a>Forum aux questions

+ Puis-je migrer les données d’Analytique de journal public Azure pour Azure administrations ?
  - N° Il n’est pas possible de déplacer des données ou votre espace de travail public Azure pour Azure gouvernement.
+ Puis-je passer publics Azure et Azure gouvernement les espaces de travail à partir du portail de l’OMS journal Analytique ?
  - N° Les portails pour les publics de Azure et d’Azure gouvernement sont distincts et ne partagent pas d’informations. 

Pour plus d’informations, consultez la [documentation de public de journal Analytique](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, abonnez-vous à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure gouvernement Blog.</a>
 
