<properties
    pageTitle="Créer un rapport de l’historique de modification access | Microsoft Azure"
    description="Générer un rapport qui répertorie toutes les modifications apportées à l’accès à vos abonnements Azure avec contrôle d’accès basé sur les 90 derniers jours."
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
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="create-an-access-change-history-report"></a>Créer un rapport de l’historique de modification access

Chaque fois que quelqu'un accorde ou révoque l’accès de vos abonnements, les modifications sont enregistrées dans les événements Azure. Vous pouvez créer des rapports sur l’historique accès Modifier pour afficher toutes les modifications apportées au cours des 90 derniers jours.

## <a name="create-a-report-with-azure-powershell"></a>Créer un rapport avec PowerShell d’Azure
Pour créer un rapport de l’historique de modification access dans PowerShell, utiliser la `Get-AzureRMAuthorizationChangeLog` commande. Plus de détails sur cette applet de commande sont disponibles dans la [Galerie de PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Lorsque vous appelez cette commande, vous pouvez spécifier quelle propriété des affectations répertoriées, y compris les suivantes :

| Propriété | Description |
| -------- | ----------- |
| **Action** | Si l’accès a été accordé ou révoqué |
| **Appelant** | Le propriétaire responsable de la modification de l’accès |
| **Date** | La date et l’heure à laquelle l’accès a été modifié. |
| **Nom du répertoire** | L’annuaire Active Directory de Azure |
| **Au PrincipalName** | Le nom de l’utilisateur, un groupe ou une application |
| **PrincipalType** | Si l’affectation a été pour une application, un groupe ou un utilisateur |
| **RoleId** | Le GUID du rôle qui vous a été accordé ou refusé |
| **RoleName** | Le rôle qui vous a été accordé ou refusé |
| **ScopeName** | Le nom de l’abonnement, le groupe de ressources ou la ressource |
| **Type d’étendue** | Si l’affectation a été à l’abonnement, le groupe de ressources ou la portée de la ressource |
| **SubscriptionId** | Le GUID de l’abonnement Azure |
| **SubscriptionName** | Le nom de l’abonnement Azure |

Cet exemple de commande répertorie toutes les modifications de l’accès de l’abonnement pour les sept derniers jours :

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - capture d’écran](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Créer un rapport avec la CLI d’Azure
Pour créer un rapport de l’historique des accès modification dans l’interface de ligne de commande (CLI) Azure, utilisez le `azure role assignment changelog list` commande.

## <a name="export-to-a-spreadsheet"></a>Exporter vers une feuille de calcul
Pour enregistrer le rapport, ou manipuler les données, exporter les modifications d’accès vers un fichier .csv. Vous pouvez ensuite afficher le rapport dans une feuille de calcul pour la révision.

![ChangeLog affiché en tant que feuille de calcul - capture d’écran](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="see-also"></a>Voir aussi
- Mise en route de [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md)
- Utiliser les [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md)
