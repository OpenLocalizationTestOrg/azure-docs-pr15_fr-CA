<properties
    pageTitle="Connecter les fonctionnalités Analytique pour les fournisseurs de Service | Microsoft Azure"
    description="Analytique de journal peut aider les fournisseurs de services gérés (MSP), grandes entreprises, fournisseurs de logiciel indépendants (ISV) et les fournisseurs d’hébergement service gérer et contrôler les serveurs sur site du client ou infrastructure de cloud."
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
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>Fonctionnalités d’Analytique de journal pour les fournisseurs de Service

Analytique de journal peut aider à managed service Provider (MSP), grandes entreprises, fournisseurs de logiciels indépendants (ISV) et fournisseurs d’hébergement service gérer et contrôler les serveurs sur site du client ou infrastructure de nuage. 

Grandes entreprises partagent beaucoup de similitudes avec les fournisseurs de services, particulièrement lorsqu’il existe une équipe informatique centralisée qui est responsable de la gestion informatique de nombreuses divisions différentes. Pour plus de simplicité, ce document utilise le terme de *fournisseur de service* , mais la même fonctionnalité est également disponible pour les entreprises et autres clients.

## <a name="cloud-solution-provider"></a>Fournisseur de solutions de cloud

Pour les partenaires et les fournisseurs de services qui font partie du programme [Fournisseur de solutions de Cloud (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , journal Analytique est un des services Azure disponibles sur un abonnement du fournisseur de services cryptographiques. 

Pour journal Analytique, les fonctionnalités suivantes sont activées dans le *Fournisseur de solutions de Cloud* abonnements.

En tant que *Fournisseur de solutions de Cloud* , vous pouvez :

+ Créer des espaces de travail de journal Analytique dans l’abonnement d’un client (client).
+ Les espaces de travail Access créés par les locataires. 
+ Ajouter et supprimer l’accès utilisateur à l’espace de travail à l’aide de la gestion des utilisateurs Azure. Lorsque dans l’espace de travail d’un client dans le portail de l’OMS, la gestion des utilisateurs page sous paramètres n’est pas disponible
  - Analytique de journal ne gère pas les accès par rôle encore - attribution à un utilisateur `reader` dans le portail Azure permet à leur apporter des modifications de configuration dans le portail de l’OMS

Pour vous connecter à l’abonnement d’un client, vous devez spécifier l’identificateur du client. L’identificateur client est souvent de cette dernière partie de l’adresse de messagerie utilisée pour vous connecter.

+ Dans le portail de l’OMS, ajoutez `?tenant=contoso.com` dans l’URL pour le portail. Par exemple,`mms.microsoft.com/?tenant=contoso.com`
+ Dans PowerShell, utiliser la `-Tenant contoso.com` paramètre lors de l’utilisation `Add-AzureRmAccount` applet de commande
+ L’identificateur de client est ajouté automatiquement lorsque vous utilisez la `OMS portal` lien à partir du portail Azure pour ouvrir et ouvrir une session sur le portail de l’OMS pour l’espace de travail sélectionné

En tant que *client* d’un fournisseur de solutions de Cloud, vous pouvez :

+ Créer des espaces de travail analytique de journal dans un abonnement du fournisseur de services cryptographiques
+ Espaces de travail Access créés par le fournisseur de services cryptographiques
  -  Utilisez le `OMS portal` lien à partir du portail Azure pour ouvrir et ouvrir une session sur le portail de l’OMS pour l’espace de travail sélectionné
+ Permet d’afficher et d’utiliser la page de gestion des utilisateurs sous paramètres dans le portail de l’OMS

>[AZURE.NOTE] Les solutions de sauvegarde et de restauration de Site pour l’Analytique de journal ne sont pas en mesure de se connecter à un coffre-fort de Services de récupération et ne peut pas être configurées dans un abonnement du fournisseur de services cryptographiques.

## <a name="managing-multiple-customers-using-log-analytics"></a>Gestion de plusieurs clients à l’aide du journal Analytique 

Il est recommandé de créer un espace de travail Analytique de journal pour chaque client que vous gérez. Un espace de travail du journal Analytique fournit :

+ Un emplacement géographique pour le stockage des données. 
+ Niveau de granularité pour la facturation 
+ Isolation des données 
+ Configuration unique

En créant un espace de travail par client, vous pouvez également effectuer le suivi de l’utilisation de chaque client et de séparer les données de chaque client.

Plus d’informations sur quand et pourquoi créer plusieurs espaces de travail décrit dans [gérer l’accès aux journaux analytique] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Création et configuration des espaces de travail de client peuvent être automatisées à l’aide de [PowerShell](log-analytics-powershell-workspace-configuration.md), [modèles du Gestionnaire de ressources](log-analytics-template-workspace-configuration.md), ou à l’aide de l' [API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

L’utilisation des modèles du Gestionnaire de ressources pour la configuration de l’espace de travail vous permet d’avoir une configuration maître qui peut être utilisée pour créer et configurer des espaces de travail. Vous pouvez être certain que, comme les espaces de travail sont créés pour les clients qu’ils sont configurés automatiquement à vos besoins. Lorsque vous mettez à jour vos besoins, le modèle est mis à jour et appliqué de nouveau les espaces de travail existants. Ce processus garantit que les espaces de travail existants même répondent à vos nouvelles normes.    

Lorsque vous gérez plusieurs espaces de travail Analytique du journal, nous vous recommandons d’intégrer chaque espace de travail avec votre système de gestion des tickets existant / console d’opérations à l’aide de la fonctionnalité [alertes](log-analytics-alerts.md) . Grâce à l’intégration avec vos systèmes existants, personnel du support technique peut continuer à suivre leurs processus familiers. Analytique de journal vérifie chaque espace de travail contre les critères d’alerte que vous spécifiez régulièrement et génère une alerte lorsqu’une action est nécessaire.

Pour les rapports de niveau exécutifs qui synthétisent les données entre les espaces de travail, vous pouvez utiliser l’intégration entre le journal Analytique et [PowerBI](log-analytics-powerbi.md). Si vous avez besoin intégrer avec un autre système de création de rapports, vous pouvez utiliser l’API de recherche (via PowerShell ou [autres](log-analytics-log-search-api.md)) pour exécuter des requêtes et d’exporter les résultats de la recherche.

## <a name="next-steps"></a>Étapes suivantes

+ Automatiser la création et la configuration des espaces de travail à l’aide des [modèles du Gestionnaire de ressources](log-analytics-template-workspace-configuration.md)
+ Automatiser la création des espaces de travail à l’aide de [PowerShell](log-analytics-powershell-workspace-configuration.md) 
+ Utiliser des [alertes](log-analytics-alerts.md) pour les intégrer aux systèmes existants
+ Générer des rapports de synthèse à l’aide de [PowerBI](log-analytics-powerbi.md)
