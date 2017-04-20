<properties
    pageTitle="Collecte de données de stockage Azure dans vue d’ensemble du journal Analytique | Microsoft Azure"
    description="Ressources Azure peuvent écrire les journaux et les mesures à un compte de stockage Azure, souvent à l’aide des Diagnostics d’Azure. Analytique de journal peut ces données d’index et le rendre disponible pour la recherche."
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

# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>La collecte de données de stockage Azure dans vue d’ensemble du journal Analytique

De nombreuses ressources Azure sont en mesure d’écrire des journaux et des mesures à un compte de stockage Azure. Analytique du journal peut utiliser ces données et vous permet de surveiller vos ressources Azure.

Pour écrire dans le stockage Azure une ressource peut utiliser les diagnostics de Windows Azure, ou avoir sa propre manière d’écrire des données. Ces données peuvent être écrits dans divers formats à l’un des emplacements suivants :

+ Table Azure
+ Blob Azure
+ EventHub

Analytique de journal prend en charge des services Azure qui écrivent des données à l’aide de [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). En outre, journal Analytique prend en charge les autres services qui génèrent des journaux et dans différents formats et emplacements.  

>[AZURE.NOTE] Vous êtes chargé de taux de données Azure normales pour le stockage et les transactions lorsque vous envoyez des diagnostics pour un compte de stockage et pour lorsque le journal Analytique lit les données à partir de votre compte de stockage.

![Schéma de stockage Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Prise en charge de ressources Azure

Analytique de journal peut collecter des données pour les ressources Azure suivantes :

| Type de ressource | Journaux (catégories de Diagnostic) | Solutions d’Analytique de journal |
| --------------------------------------- | -------------------------------- | --------------- |
| Aperçu de l’application | Disponibilité <br> Événements personnalisés <br> Exceptions <br> Demandes <br> | Aperçu de l’application (aperçu) |
| Gestion de l’API | | *Aucun* (Aperçu) |
| Automation <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation (aperçu) |
| Chambre forte de clé <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault (aperçu) |
| Passerelle d’application <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking (aperçu) |
| Groupe de sécurité réseau <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking (aperçu) |
| TISSU de service                          | ETWEvent <br> Événements opérationnels <br> Événement d’acteur fiable <br> Événements de Service fiable| ServiceFabric (aperçu) |
| Ordinateurs virtuels | Linux Syslog <br> Événements Windows <br> Journal IIS <br> Windows ETWEvent | *Aucun* |
| Rôles Web <br> Rôles Worker | Linux Syslog <br> Événements Windows <br> Journal IIS <br> Windows ETWEvent | *Aucun* |

>[AZURE.NOTE] Pour la surveillance des machines virtuelles Azure (Linux et Windows), nous vous recommandons d’installer l' [extension de la machine virtuelle de journal Analytique](log-analytics-azure-vm-extension.md). L’agent fournit des analyses plus profonds sur vos machines virtuelles que si vous utilisez les tests de diagnostic écrite dans le stockage.

Vous pouvez nous aider à hiérarchiser les journaux supplémentaires pour OMS à analyser par vote sur notre [page de commentaires](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).


- Pour en savoir plus sur comment journal Analytique peut lire les journaux des services Azure qui prennent en charge les [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), reportez-vous à la section [Azure d’analyser les journaux de diagnostic à l’aide du journal Analytique](log-analytics-azure-storage-json.md) :
  - Chambre forte de clé Azure
  - Automation Azure
  - Passerelle d’application
  - Groupes de sécurité de réseau
- Consultez [stockage de blob utiliser pour IIS et le stockage de table pour les événements](log-analytics-azure-storage-iis-table.md) pour en savoir plus sur comment journal Analytique peut lire les journaux pour cette écriture de diagnostic pour stockage de table ou écrites dans le blob de stockage, les journaux IIS des services Azure, y compris :
  - TISSU de service
  - Rôles Web
  - Rôles Worker
  - Ordinateurs virtuels


Idées d’application est aperçu privé et exporter continuous utilise stockage blob. Pour joindre l’aperçu privée, contactez votre équipe de Account Microsoft ou consultez les détails sur le [site de commentaires](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Étapes suivantes

- Cette écriture de diagnostic dans le stockage blob au format JSON des services [Azure d’analyser les journaux de diagnostic à l’aide du journal Analytique](log-analytics-azure-storage-json.md) pour lire les journaux d’Azure.
- [Stockage de blob utiliser pour IIS et le stockage de table pour les événements](log-analytics-azure-storage-iis-table.md) pour lire les journaux pour Azure services que diagnostics d’écriture dans les journaux IIS écrits dans le stockage blob ou de stockage de table.
- [Activer les Solutions](log-analytics-add-solutions.md) pour fournir des données.
- [Utilisation des requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.
