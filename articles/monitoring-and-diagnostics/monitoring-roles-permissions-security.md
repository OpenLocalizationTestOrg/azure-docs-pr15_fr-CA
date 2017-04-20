<properties
    pageTitle="Mise en route avec les rôles, les autorisations et la sécurité avec le moniteur d’Azure | Microsoft Azure"
    description="Apprenez à utiliser les rôles intégrés du moniteur Azure et des autorisations pour restreindre l’accès à l’analyse des ressources."
    authors="johnkemnetz"
    manager="rboucher"
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
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Mise en route avec les rôles, les autorisations et la sécurité avec le moniteur d’Azure

De nombreuses équipes ont besoin réguler strictement l’accès à l’analyse des données et des paramètres. Par exemple, si vous avez des membres de l’équipe qui travaillent exclusivement sur l’analyse (ingénieurs du support technique, les ingénieurs devops), ou si vous utilisez un fournisseur de services managés, vous souhaiterez peut-être leur accorder l’accès aux données d’analyse uniquement en limitant leurs possibilités pour créer, modifier ou supprimer des ressources. Cet article explique comment appliquer un rôle RBAC de surveillance intégré à un utilisateur dans Azure rapidement ou de créer vos propres rôles personnalisés pour un utilisateur qui a besoin des autorisations de contrôle limitées. Il décrit ensuite les considérations sur la sécurité de vos ressources de moniteur d’Azure et comment vous pouvez limiter l’accès aux données qu’ils contiennent.

## <a name="built-in-monitoring-roles"></a>Rôles de surveillance intégrés

Les rôles intégrés du moniteur Azure sont conçus pour aider à limiter l’accès aux ressources dans un abonnement tout en permettant au responsable de la surveillance de l’infrastructure pour obtenir et configurer les données dont ils ont besoin. Moniteur Azure fournit deux rôles de-l’emploi : lecteur de surveillance A et un collaborateur de surveillance.

### <a name="monitoring-reader"></a>Analyse du lecteur

Personnes affectées au rôle de surveillance de lecteur pour afficher toutes les données de surveillance dans un abonnement mais ne peut pas modifier n’importe quelle ressource ou modifier les paramètres relatifs à la surveillance des ressources. Ce rôle est approprié pour les utilisateurs dans une organisation, tels que les ingénieurs de support ou des opérations, qui doivent être en mesure de :

- Afficher les tableaux de bord de surveillance dans le portail et créer leurs propres tableaux de bord de surveillance privé.
- Requête pour les mesures à l’aide de [l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931930.aspx), [applets de commande PowerShell](insights-powershell-samples.md)ou [multiplates - CLI](insights-cli-samples.md).
- Interroger le journal d’activité à l’aide du portail, API REST de moniteur Azure, applets de commande PowerShell ou multiplates-CLI.
- Afficher les [paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) pour une ressource.
- Permet d’afficher le [profil du journal](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) pour un abonnement.
- Afficher les paramètres d’échelle.
- Afficher l’activité alerte et les paramètres.
- Accéder aux données de l’aperçu de l’Application et d’afficher des données dans AI Analytique.
- Rechercher des données d’espace de travail de journal Analytique (OMS), y compris les données d’utilisation de l’espace de travail.
- Afficher les groupes de gestion Analytique du journal (OMS).
- Extraire le schéma de recherche de journal Analytique (OMS).
- Liste des packs d’intelligence journal Analytique (OMS).
- Récupérer et exécuter le journal Analytique (OMS) les recherches enregistrées.
- Récupérer la configuration de stockage du journal Analytique (OMS).

> [AZURE.NOTE] Ce rôle ne donne pas d’accès en lecture aux données de journal qui a été transmises à un concentrateur d’événements ou stockées dans un compte de stockage. [Voir ci-dessous](#security-considerations-for-monitoring-data) pour plus d’informations sur la configuration de l’accès à ces ressources.

### <a name="monitoring-contributor"></a>Surveillance des collaborateurs

Les personnes du rôle Collaborateur de surveillance peuvent afficher toutes les données de surveillance dans un abonnement et créer ou modifier des paramètres d’analyse, mais pas d’autres ressources. Ce rôle est un sur-ensemble du rôle lecteur de surveillance et il est approprié pour les membres de l’équipe de surveillance d’une entreprise ou les fournisseurs de services gérés qui, outre les autorisations ci-dessus, également être en mesure de :

- Publier des tableaux de bord de surveillance sous la forme d’un tableau de bord partagé.
- Définir [les paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) pour un resource.*
- Définir le [profil du journal](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) pour une subscription.*
- Définir l’activité d’alerte et les paramètres.
- Créer des composants et des perspectives de l’Application des tests web.
- Espace de travail de liste journal Analytique (OMS) des clés partagées.
- Activer ou désactiver des packs d’intelligence journal Analytique (OMS).
- Créer, supprimer et exécuter le journal Analytique (OMS) les recherches enregistrées.
- Créer et supprimer la configuration de stockage du journal Analytique (OMS).

* utilisateur doit également séparément être autorisé ListKeys sur la ressource cible (stockage compte ou événement concentrateur namespace) pour définir un profil de journal ou d’un paramètre de diagnostic.

> [AZURE.NOTE] Ce rôle ne donne pas d’accès en lecture aux données de journal qui a été transmises à un concentrateur d’événements ou stockées dans un compte de stockage. [Voir ci-dessous](#security-considerations-for-monitoring-data) pour plus d’informations sur la configuration de l’accès à ces ressources.

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Surveillance des autorisations et des rôles RBAC personnalisés
Si les rôles intégrés ci-dessus ne répondent pas à des besoins de votre équipe, vous pouvez [créer un rôle RBAC personnalisé](../active-directory/role-based-access-control-custom-roles.md) avec des autorisations plus précises. Vous trouverez ci-dessous les opérations Azure moniteur RBAC courantes avec leur description.

| Opération                                                   | Description                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Suppression de Microsoft.Insights/AlertRules/[Read, écriture]         | Règles d’alerte en lecture/écriture/suppression.                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | Liste des incidents (historique de la règle d’alerte déclenchée) pour les règles d’alerte. Cela s’applique uniquement au portail.                                              |
| Suppression de Microsoft.Insights/AutoscaleSettings/[Read, écriture]  | Paramètres de lecture/écriture/suppression autoscale.                                                                                                                     |
| Suppression de Microsoft.Insights/DiagnosticSettings/[Read, écriture] | Paramètres de diagnostic en lecture/écriture/suppression.                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail.                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | Liste des événements du journal d’activité (les événements de gestion) dans un abonnement. Cette autorisation s’applique aux accès portail et par programmation dans le journal d’activité. |
| Microsoft.Insights/LogDefinitions/Read                      | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail.                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | Définitions de mesure lecture (liste de types de mesure disponibles pour une ressource).                                                                                  |
| Microsoft.Insights/Metrics/Read                             | Lire la métrique pour une ressource.                                                                                                                              |

> [AZURE.NOTE] L’accès aux alertes, les paramètres de diagnostic et les mesures d’une ressource nécessite que l’utilisateur a accès en lecture pour le type de ressource et de la portée de la ressource. Un profil de paramètre ou le journal de diagnostic qui archive à un compte de stockage ou de flux de données à des concentrateurs d’événement création (« écriture ») requiert que l’utilisateur ait également ListKeys autorisation sur la ressource cible.

Par exemple, en utilisant le tableau ci-dessus, vous pouvez créer un rôle RBAC personnalisé pour un « lecteur de journal d’activité » comme suit :

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considérations sur la sécurité pour les données d’analyse
Analyse des données, notamment les fichiers journaux : peut contenir des informations sensibles, telles que les adresses IP ou des noms d’utilisateur. Analyse des données à partir d’Azure est disponible sous trois formes de base :
1. Le journal d’activité, qui décrit toutes les actions du plan de contrôle sur votre abonnement Azure.
2. Journaux de diagnostic, qui sont des journaux émises par une ressource.
3. Mesures, qui sont émis par des ressources.

Trois de ces types de données peuvent être stockées dans un compte de stockage ou diffusé en continu vers le concentrateur d’événements, qui sont tous deux des ressources Azure à usage général. Parce que ce sont les ressources à usage général, création, de suppression et d’y accéder sont une opération privilégiée est généralement réservée à un administrateur. Nous vous suggérons d’utiliser les méthodes suivantes pour les ressources liées à la surveillance pour prévenir les abus :

- Utiliser un compte de stockage unique, dédié pour l’analyse des données. Si vous devez séparer les données de surveillance dans plusieurs comptes de stockage, jamais partager l’utilisation d’un compte de stockage entre la surveillance et des données non-surveillance, que ce peuvent par inadvertance donner ceux qui n’ont besoin d’accéder à l’analyse des données (par exemple. un SIEM tiers) accès aux non-surveillance de données.
- Utiliser un unique, dédié Bus de Service ou d’un concentrateur d’événements sur tous les paramètres de diagnostics pour la même raison que ci-dessus.
- Limiter l’accès aux comptes de relatif à l’analyse de stockage ou les concentrateurs d’événements en les conservant dans un groupe de ressources distinct et [utiliser la portée](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) de vos rôles de surveillance pour limiter l’accès à ce groupe de ressources uniquement.
- Jamais accorder l’autorisation ListKeys pour les comptes de stockage ou de concentrateurs d’événement à la portée de l’abonnement lorsqu’un utilisateur a uniquement besoin d’accéder à l’analyse des données. À la place, accorder ces autorisations à l’utilisateur à une ressource ou un groupe de ressources, (si vous avez un groupe de ressources de surveillance dédiée) étendue.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitation de l’accès pour les comptes liés à la surveillance de stockage
Lorsqu’un utilisateur ou une application doit accéder à l’analyse des données dans un compte de stockage, vous devez [Générer un SAS compte](https://msdn.microsoft.com/library/azure/mt584140.aspx) sur le compte de stockage qui contient les données de surveillance avec accès en lecture seule de niveau de service pour le stockage des objets blob. Dans PowerShell, cela peut ressembler à :

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Vous pouvez ensuite donner le jeton à l’entité que doivent pour lire à partir de ce stockage compte et il peut répertorier et lire à partir de tous les objets BLOB dans ce compte de stockage.

Si vous avez besoin contrôler cette autorisation avec RBAC, vous pouvez également accorder cette entité l’autorisation Microsoft.Storage/storageAccounts/listkeys/action sur ce compte de stockage particulier. Cela est nécessaire pour les utilisateurs qui ont besoin de pouvoir définir un paramètre de diagnostic ou d’ouvrir une session sur un compte de stockage profil d’archivage. Par exemple, vous pouvez créer le rôle RBAC personnalisé suivant pour un utilisateur ou une application qui a uniquement besoin de lire à partir d’un compte de stockage :

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] L’autorisation de ListKeys permet à l’utilisateur de répertorier les clés de compte de stockage principal et secondaire. Ces clés accordent à l’utilisateur tous les signés autorisations (lecture, écriture, créer des objets BLOB, supprimer des objets BLOB, etc.) sur l’ensemble signé services (blob, file d’attente, table, fichier) dans ce compte de stockage. Nous vous recommandons d’utiliser un SAS compte décrits ci-dessus, lorsque cela est possible.

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitation de l’accès à des concentrateurs de relatif à l’analyse des événements
Un modèle similaire peut être suivi avec les concentrateurs d’événement, mais vous devez d’abord créer une règle d’autorisation écoute dédiée. Si vous souhaitez accorder l’accès à une application qui n’a besoin d’écouter les concentrateurs relatif à l’analyse des événements, effectuez les opérations suivantes :

1. Créer une stratégie d’accès partagé sur les concentrateurs d’événements qui ont été créés pour la diffusion en continu des données de surveillance avec uniquement les demandes d’écoute. Pour cela, dans le portail. Par exemple, vous pouvez appeler il « monitoringReadOnly ». Si possible, vous devez donner cette clé directement au consommateur et ignorez l’étape suivante.
2. Si le consommateur doit être en mesure d’obtenir la clé d’ad hoc, accordez à l’utilisateur l’action ListKeys de ce concentrateur d’événements. Il est également nécessaire pour les utilisateurs qui ont besoin de pouvoir définir un paramètre de diagnostic ou de journaux de profil dans le flux à des concentrateurs de l’événement. Par exemple, vous pouvez créer une règle RBAC :

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les autorisations dans le Gestionnaire de ressources et de RBAC](../active-directory/role-based-access-control-what-is.md)
- [Lire la présentation de l’analyse dans Azure](monitoring-overview.md)
