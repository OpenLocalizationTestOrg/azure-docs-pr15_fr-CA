<properties
    pageTitle="RBAC : Les rôles intégrés | Microsoft Azure"
    description="Cette rubrique décrit l’intégré dans les rôles de contrôle d’accès basé sur les rôles (RBAC)."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC : Les rôles intégrés

Azure contrôle d’accès basée sur les rôles (RBAC) est fourni avec les rôles intégrés suivants qui peuvent être affectés à des utilisateurs, des groupes et des services. Vous ne pouvez pas modifier les définitions de rôles intégrés. Toutefois, vous pouvez créer des [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md) pour répondre aux besoins spécifiques de votre organisation.

## <a name="roles-in-azure"></a>Rôles dans Azure

Le tableau suivant décrit brièvement les rôles intégrés. Cliquez sur le nom de rôle pour afficher la liste détaillée des **actions** et des **notactions** pour le rôle. La propriété **actions** spécifie les actions autorisées sur les ressources d’Azure. Chaînes d’action peuvent utiliser des caractères génériques. La propriété **notactions** spécifie les actions qui sont exclues les actions autorisées.

>[AZURE.NOTE] Les définitions de rôle Azure évoluent constamment. Cet article est maintenu aussi à jour que possible, mais vous pouvez toujours trouver les dernières définitions de rôles dans Azure PowerShell. Utiliser les applets de commande `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` selon le cas.

| Nom de rôle | Description |
| --------- | ----------- |
| [Collaborateurs de Service Management API](#api-management-service-contributor) | Permet de gérer les services de gestion de l’API |
| [Application Insights composant contributeur](#application-insights-component-contributor) | Peut gérer les composants de l’aperçu de l’Application |
| [Opérateur d’Automation](#automation-operator) | Capable de démarrer, d’arrêter, de suspendre et de reprendre des travaux |
| [Collaborateurs de BizTalk](#biztalk-contributor) | Permet de gérer les services BizTalk |
| [ClearDB MySQL DB collaborateur](#cleardb-mysql-db-contributor) | Peut gérer des bases de données MySQL de ClearDB |
| [Collaborateur](#contributor) | Permet de gérer tout à l’exception d’access. |
| [Collaborateur en usine](#data-factory-contributor) | Pouvez de créer et de gérer des fabriques de données et les ressources enfants qu’ils contiennent. |
| [Utilisateur de laboratoires DevTest](#devtest-labs-user) | Permet d’afficher tous les éléments et de se connecter, Démarrer, de redémarrage et d’arrêt des machines virtuelles |
| [Collaborateurs de Zone DNS](#dns-zone-contributor) | Peut gérer les enregistrements et zones DNS |
| [Collaborateur du compte de DocumentDB](#documentdb-account-contributor) | Peut gérer les comptes de DocumentDB |
| [Collaborateur du compte des systèmes intelligents](#intelligent-systems-account-contributor) | Peut gérer les comptes de systèmes intelligents |
| [Collaborateur du réseau](#network-contributor) | Peut gérer toutes les ressources réseau |
| [Nouveau collaborateur de compte Relic APM](#new-relic-apm-account-contributor) | Peut gérer des applications et des comptes de gestion des performances nouvelle Relic Application |
| [Propriétaire](#owner) | Permet de gérer tous les éléments, y compris l’accès |
| [Lecteur](#reader) | Peuvent afficher tous les éléments, mais ne peut pas apporter des modifications |
| [Redis Cache collaborateur](#redis-cache-contributor) | Peut gérer des caches de Redis |
| [Collaborateurs de Collections de tâche du planificateur](#scheduler-job-collections-contributor) | Peut gérer des collections de tâche du planificateur |
| [Collaborateurs de Service de recherche](#search-service-contributor) | Gérer des services de recherche |
| [Gestionnaire de sécurité](#security-manager) | Peut gérer des composants de sécurité, les stratégies de sécurité et les machines virtuelles |
| [Contributor de base de données SQL](#sql-db-contributor) | Permet de gérer les bases de données SQL, mais pas leurs stratégies de sécurité |
| [Gestionnaire de sécurité SQL](#sql-security-manager) | Peut gérer les stratégies liées à la sécurité des bases de données et les serveurs SQL |
| [Collaborateurs de SQL Server](#sql-server-contributor) | Permet de gérer les serveurs SQL et bases de données, mais pas leurs stratégies de sécurité |
| [Collaborateurs de compte de stockage classique](#classic-storage-account-contributor) | Peut gérer des comptes de stockage classique |
| [Collaborateurs de compte de stockage](#storage-account-contributor) | Peut gérer des comptes de stockage |
| [Administrateur d’accès utilisateur](#user-access-administrator) | Peuvent gérer l’accès utilisateur aux ressources Azure |
| [Les machines virtuelles classique collaborateur](#classic-virtual-machine-contributor) | Permet de gérer les machines virtuelles classiques, mais pas le réseau ou stockage compte virtuel auquel ils sont connectés |
| [Collaborateurs de Machine virtuelle](#virtual-machine-contributor) | Peut gérer des ordinateurs virtuels, mais pas le réseau ou stockage compte virtuel auquel ils sont connectés |
| [Collaborateurs de réseau classique](#classic-network-contributor) | Peut gérer les réseaux virtuels classiques et IPs réservés |
| [Contributeur du Plan Web](#web-plan-contributor) | Peut gérer des plans de web |
| [Collaborateur du site Web](#website-contributor) | Permet de gérer des sites Web, mais pas les plans de web auquel ils sont connectés |

## <a name="role-permissions"></a>Autorisations de rôle
Les tableaux suivants décrivent les autorisations spécifiques accordées à chaque rôle. Il peut s’agir d' **Actions**qui donnent des autorisations et **NotActions**, qui limitent les.

### <a name="api-management-service-contributor"></a>Collaborateurs de Service Management API
Permet de gérer les services de gestion de l’API

| **Actions** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Créer et gérer des Services de gestion d’API |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="application-insights-component-contributor"></a>Application Insights composant contributeur
Peut gérer les composants de l’aperçu de l’Application

| **Actions** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.Insights/components/* | Créer et gérer des composants de perspectives |
| Microsoft.Insights/webtests/* | Créer et gérer des tests web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="automation-operator"></a>Opérateur d’Automation
Capable de démarrer, d’arrêter, de suspendre et de reprendre des travaux

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Automation/automationAccounts/jobs/read | Lire automation des tâches de compte |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reprendre une tâche de compte automation |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrêter un travail de compte automation |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Lire des flux de travail compte automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspendre un travail du compte automation |
| Microsoft.Automation/automationAccounts/jobs/write | Écrire des automatisations de travaux de compte |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Lire une planification de travail compte automation |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Lire une planification de travail compte automation |
| Microsoft.Automation/automationAccounts/read | Lire les comptes d’automation |
| Microsoft.Automation/automationAccounts/runbooks/read | Lisez les procédures opérationnelles d’automation |
| Microsoft.Automation/automationAccounts/schedules/read | Lecture des tableaux d’analyse automation |
| Microsoft.Automation/automationAccounts/schedules/write | Écrire des automatisations de tableaux d’analyse |
| Microsoft.Insights/components/* | Créer et gérer des composants de perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="biztalk-contributor"></a>Collaborateurs de BizTalk
Permet de gérer les services BizTalk

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.BizTalkServices/BizTalk/* | Créer et gérer des services BizTalk |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB collaborateur
Peut gérer des bases de données MySQL de ClearDB

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |
| successbricks.cleardb/Databases/* | Créer et gérer des bases de données MySQL de ClearDB |

### <a name="contributor"></a>Collaborateur
Permet de gérer tout à l’exception de l’accès

| **Actions** ||
| ------- | ------ |
| * | Créer et gérer les ressources de tous les types |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | Impossible de supprimer les rôles et les attributions de rôles |  
| Microsoft.Authorization/*/Write | Impossible de créer les rôles et les attributions de rôles |

### <a name="data-factory-contributor"></a>Collaborateur en usine
Créer et gérer des fabriques de données et les ressources enfants qu’ils contiennent.

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.DataFactory/dataFactories/* | Créer et gérer des fabriques de données et les ressources enfants qu’ils contiennent. |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="devtest-labs-user"></a>Utilisateur de laboratoires DevTest
Permet d’afficher tous les éléments et de se connecter, Démarrer, de redémarrage et d’arrêt des machines virtuelles

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Compute/availabilitySets/read | Lire les propriétés des jeux de disponibilité |
| Microsoft.Compute/virtualMachines/*/read | Lire les propriétés d’un ordinateur virtuel (tailles de machine virtuelle, état d’exécution, extensions de machine virtuelle, etc.). |
| Microsoft.Compute/virtualMachines/deallocate/action | Désallouer des machines virtuelles |
| Microsoft.Compute/virtualMachines/read | Lire les propriétés d’un ordinateur virtuel |
| Microsoft.Compute/virtualMachines/restart/action | Redémarrez les ordinateurs virtuels |
| Microsoft.Compute/virtualMachines/start/action | Démarrer les ordinateurs virtuels |
| Microsoft.DevTestLab/*/read | Lire les propriétés d’un laboratoire |
| Microsoft.DevTestLab/labs/createEnvironment/action | Créer un environnement de laboratoire |
| Microsoft.DevTestLab/labs/formulas/delete | Suppression de formules |
| Microsoft.DevTestLab/labs/formulas/read | Lire les formules |
| Microsoft.DevTestLab/labs/formulas/write | Ajouter ou modifier des formules |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Évaluer les stratégies d’atelier |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Participez à un pool d’adresses équilibrage de la charge back-end |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joindre un équilibreur de charge règle NAT de trafic entrant |
| Microsoft.Network/networkInterfaces/*/read | Lire les propriétés d’une interface de réseau (par exemple, tous les équilibreurs de charge faisant partie de l’interface réseau) |
| Microsoft.Network/networkInterfaces/join/action | Joindre un ordinateur virtuel à une interface réseau |
| Microsoft.Network/networkInterfaces/read | Lire des interfaces réseau |
| Microsoft.Network/networkInterfaces/write | Écrire des interfaces réseau |
| Microsoft.Network/publicIPAddresses/*/read | Lire les propriétés d’une adresse IP publique |
| Microsoft.Network/publicIPAddresses/join/action | Participez à une adresse IP publique |
| Microsoft.Network/publicIPAddresses/read | Lire les adresses IP du réseau publics |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joindre un réseau virtuel |
| Microsoft.Resources/deployments/operations/read | Les opérations de déploiement de lecture |
| Microsoft.Resources/deployments/read | Lire les déploiements |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/listKeys/action | Liste les touches de compte de stockage |

### <a name="dns-zone-contributor"></a>Collaborateurs de Zone DNS
Gérer les enregistrements et zones DNS.

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/\*/lecture | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/\* | Créer et gérer des règles d’alerte |
| Microsoft.Network/dnsZones/\* | Créer et gérer des enregistrements et zones DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/\* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/\* | Créer et gérer des tickets de Support |


### <a name="documentdb-account-contributor"></a>Collaborateur du compte de DocumentDB
Peut gérer les comptes de DocumentDB

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.DocumentDb/databaseAccounts/* | Créer et gérer des comptes de DocumentDB |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="intelligent-systems-account-contributor"></a>Collaborateur du compte des systèmes intelligents
Peut gérer les comptes de systèmes intelligents

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.IntelligentSystems/accounts/* | Créer et gérer des comptes de systèmes intelligents |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="network-contributor"></a>Collaborateur du réseau
Peut gérer toutes les ressources réseau

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.Network/* | Créer et gérer des réseaux |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="new-relic-apm-account-contributor"></a>Nouveau collaborateur de compte Relic APM
Peut gérer des applications et des comptes de gestion des performances nouvelle Relic Application

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |
| NewRelic.APM/accounts/* | Créer et gérer des comptes de gestion de performance Relic nouvelle application |

### <a name="owner"></a>Propriétaire
Permet de gérer tous les éléments, y compris l’accès

| **Actions** ||
| ------- | ------ |
| * | Créer et gérer les ressources de tous les types |

### <a name="reader"></a>Lecteur
Peuvent afficher tous les éléments, mais ne peut pas apporter des modifications

| **Actions** ||
| ------- | ------ |
| * / lecture | Lire les ressources de tous types, à l’exception des secrets. |

### <a name="redis-cache-contributor"></a>Redis Cache collaborateur
Peut gérer des caches de Redis

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Cache/redis/* | Créer et gérer des caches de Redis |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="scheduler-job-collections-contributor"></a>Collaborateurs de Collections de tâche du planificateur
Peut gérer des collections de tâche du planificateur

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Scheduler/jobcollections/* | Créer et gérer des collections de travail |
| Microsoft.Support/* | Créer et gérer des tickets de support  |

### <a name="search-service-contributor"></a>Collaborateurs de Service de recherche
Gérer des services de recherche

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Search/searchServices/* | Créer et gérer des services de recherche |
| Microsoft.Support/* | Créer et gérer des tickets de support  |

### <a name="security-manager"></a>Gestionnaire de sécurité
Peut gérer des composants de sécurité, les stratégies de sécurité et les machines virtuelles

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.ClassicCompute/*/read | Lire les informations de configuration classique calculer des machines virtuelles |
| Microsoft.ClassicCompute/virtualMachines/*/write | Écrire la configuration des machines virtuelles |
| Microsoft.ClassicNetwork/*/read | Lire les informations de configuration de réseau classique  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Security/* | Créer et gérer des stratégies et des composants de sécurité |
| Microsoft.Support/* | Créer et gérer des tickets de support  |

### <a name="sql-db-contributor"></a>Contributor de base de données SQL
Permet de gérer les bases de données SQL, mais pas leurs stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lecture des rôles et des affectations de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/servers/databases/* | Créer et gérer des bases de données SQL |
| Microsoft.Sql/servers/read | Serveurs SQL en lecture |
| Microsoft.Support/* | Créer et gérer des tickets de support |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit |
| Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit |
| Microsoft.Sql/servers/databases/auditRecords/read  | Impossible de lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier des stratégies de masquage des données |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies alerte de sécurité |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité |

### <a name="sql-security-manager"></a>Gestionnaire de sécurité SQL
Peut gérer les stratégies liées à la sécurité des bases de données et les serveurs SQL

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture Microsoft |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/servers/auditingPolicies/* | Créer et gérer des stratégies d’audit de SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Créer et gérer des paramètres d’audit de serveur SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Créer et gérer des stratégies d’audit de la base de données SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Créer et gérer des paramètres de l’audit de base de données SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read | Lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Créer et gérer des stratégies de connexion de base de données SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Créer et gérer des stratégies de masquage de données de base de données du serveur SQL |
| Microsoft.Sql/servers/databases/read | Bases de données SQL de lecture |
| Microsoft.Sql/servers/databases/schemas/read | Schémas de base de données en lecture SQL server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Colonnes de table de base de données de serveur SQL de lecture |
| Microsoft.Sql/servers/databases/schemas/tables/read | Tables de base de données de serveur SQL de lecture |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Créer et gérer des stratégies de sécurité de base de données serveur SQL alertes |
| Microsoft.Sql/servers/databases/securityMetrics/* | Créer et gérer des mesures de sécurité de base de données SQL server |
| Microsoft.Sql/servers/read | Serveurs SQL en lecture |
| Microsoft.Sql/servers/securityAlertPolicies/* | Créer et gérer des stratégies de l’alertes de sécurité SQL server |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="sql-server-contributor"></a>Collaborateurs de SQL Server
Permet de gérer les serveurs SQL et bases de données mais pas leurs stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture|
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/servers/* | Créer et gérer les serveurs SQL |
| Microsoft.Support/* | Créer et gérer des tickets de support |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Impossible de modifier des stratégies d’audit SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Ne peut pas modifier les paramètres d’audit SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Ne peut pas modifier les stratégies d’audit de la base de données SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Ne peut pas modifier les paramètres de l’audit de base de données SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read  | Impossible de lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion de base de données SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Ne peut pas modifier les données de base de données SQL server des stratégies de masquage |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Ne peut pas modifier les stratégies de sécurité de base de données serveur SQL alertes |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité de base de données SQL server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Ne peut pas modifier les stratégies de l’alertes de sécurité SQL server |

### <a name="classic-storage-account-contributor"></a>Collaborateurs de compte de stockage classique
Peut gérer des comptes de stockage classique

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicStorage/storageAccounts/* | Créer et gérer des comptes de stockage |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="storage-account-contributor"></a>Collaborateurs de compte de stockage
Peut gérer des comptes de stockage, mais pas d’accès.

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Toutes les autorisations de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.Insights/diagnosticSettings/* | Gérer les paramètres de diagnostic |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Storage/storageAccounts/* | Créer et gérer des comptes de stockage |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="user-access-administrator"></a>Administrateur d’accès utilisateur
Peuvent gérer l’accès utilisateur aux ressources Azure

| **Actions** ||
| ------- | ------ |
| * / lecture | Lire les ressources de tous Types, à l’exception des secrets. |
| Microsoft.Authorization/* | Gérer l’autorisation |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="classic-virtual-machine-contributor"></a>Les machines virtuelles classique collaborateur
Permet de gérer les machines virtuelles classiques mais pas le réseau ou stockage compte virtuel auquel ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicCompute/domainNames/* | Créer et gérer des noms de domaine de calcul classique |
| Microsoft.ClassicCompute/virtualMachines/* | Créer et gérer des machines virtuelles |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Rejoindre les groupes de sécurité réseau |
| Microsoft.ClassicNetwork/reservedIps/link/action | Lier des adresses IP réservée |
| Microsoft.ClassicNetwork/reservedIps/read | Lecture réservé les adresses IP |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Rejoindre des réseaux virtuels |
| Microsoft.ClassicNetwork/virtualNetworks/read | Lecture des réseaux virtuels |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Lire des disques de stockage en compte |
| Microsoft.ClassicStorage/storageAccounts/images/read | Lire des images de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Liste les touches de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/read | Lecture des comptes de stockage classique |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="virtual-machine-contributor"></a>Collaborateurs de Machine virtuelle
Peut gérer des ordinateurs virtuels, mais pas le réseau ou stockage compte virtuel auquel ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Compute/availabilitySets/* | Créer et gérer des ensembles de disponibilité de calcul |
| Microsoft.Compute/locations/* | Créer et gérer des emplacements de calcul |
| Microsoft.Compute/virtualMachines/* | Créer et gérer des machines virtuelles |
| Microsoft.Compute/virtualMachineScaleSets/* | Créer et gérer des ensembles d’échelle de machine virtuelle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joindre des pools d’adresses de réseau application passerelle back-end |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joindre les pools d’adresses back-end équilibrage de la charge |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joindre l’équilibreur de charge entrante pools NAT |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joindre l’équilibreur de charge NAT règles de trafic entrant |
| Microsoft.Network/loadBalancers/read | Équilibreurs de charge en lecture |
| Microsoft.Network/locations/* | Créer et gérer des emplacements de réseau |
| Microsoft.Network/networkInterfaces/* | Créer et gérer des interfaces réseau |
| Microsoft.Network/networkSecurityGroups/join/action | Rejoindre les groupes de sécurité réseau |
| Microsoft.Network/networkSecurityGroups/read | Lire les groupes de sécurité de réseau |
| Microsoft.Network/publicIPAddresses/join/action | Joindre les adresses IP du réseau publics |
| Microsoft.Network/publicIPAddresses/read | Lire les adresses IP du réseau publics |
| Microsoft.Network/virtualNetworks/read | Lecture des réseaux virtuels |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joindre les sous-réseaux du réseau virtuel |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Storage/storageAccounts/listKeys/action | Liste les touches de compte de stockage |
| Microsoft.Storage/storageAccounts/read | Lecture des comptes de stockage |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="classic-network-contributor"></a>Collaborateurs de réseau classique
Peut gérer les réseaux virtuels classiques et IPs réservés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicNetwork/* | Créer et gérer des réseaux classiques |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="web-plan-contributor"></a>Contributeur du Plan Web
Peut gérer des plans de web

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |
| Microsoft.Web/serverFarms/* | Créer et gérer des batteries de serveurs |

### <a name="website-contributor"></a>Collaborateur du site Web
Permet de gérer des sites Web mais pas les plans de web auquel ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.Insights/components/* | Créer et gérer des composants de perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |
| Microsoft.Web/certificates/* | Créer et gérer des certificats de site Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Lire les sites attribués à un nom d’hôte |
| Microsoft.Web/serverFarms/join/action | Joindre des batteries de serveurs |
| Microsoft.Web/serverFarms/read | Lire les batteries de serveurs |
| Microsoft.Web/sites/* | Créer et gérer des sites Web |

## <a name="see-also"></a>Voir aussi
- [Contrôle d’accès basé sur rôle](role-based-access-control-configure.md): mise en route de RBAC dans Azure portal.
- [Les rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md): Apprenez à créer des rôles personnalisés pour répondre à vos besoins d’accès.
- [Créer un accès de rapport historique des modifications](role-based-access-control-access-change-history-report.md): effectuer le suivi de modification des attributions de rôle dans RBAC.
- [Résolution des problèmes de contrôle d’accès basé sur rôle](role-based-access-control-troubleshooting.md): obtenir des suggestions pour résoudre les problèmes courants.
