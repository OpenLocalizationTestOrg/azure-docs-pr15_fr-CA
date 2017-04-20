<properties
    pageTitle="Gérer le contrôle d’accès basé sur les rôles (RBAC) avec PowerShell Azure | Microsoft Azure"
    description="Comment gérer le RBAC avec PowerShell Azure, y compris la liste des rôles, attribution de rôles et la suppression des affectations de rôle."
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
    ms.date="07/22/2016"
    ms.author="kgremban"/>

# <a name="manage-role-based-access-control-with-azure-powershell"></a>Gérer le contrôle d’accès basé sur les rôles avec PowerShell Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)


Vous pouvez utiliser le contrôle d’accès basée sur les rôles (RBAC) dans le portail d’Azure et d’Azure API de gestion des ressources pour gérer l’accès à votre abonnement à un niveau de granularité fin. Grâce à cette fonctionnalité, vous pouvez accorder l’accès pour les utilisateurs, groupes ou entités de service Active Directory en leur attribuant des rôles à une portée particulière.

Avant de pouvoir utiliser PowerShell pour gérer RBAC, vous devez disposer des éléments suivants :

- Azure PowerShell version 0.8.8 ou une version ultérieure. Pour installer la version la plus récente et l’associer à votre abonnement Azure, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

- Azure applets de commande Gestionnaire de ressources. Installer le [Gestionnaire de ressources Azure applets de commande](https://msdn.microsoft.com/library/mt125356.aspx) PowerShell.

## <a name="list-roles"></a>Liste des rôles

### <a name="list-all-available-roles"></a>Lister tous les rôles disponibles
Pour les rôles RBAC liste qui sont disponibles pour l’assignation et d’inspecter les opérations auxquelles ils accordent l’accès, utilisent `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![Capture d’écran de PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Liste des actions d’un rôle
Pour répertorier les actions pour un rôle spécifique, utilisez `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![Capture d’écran de PowerShell-Get AzureRmRoleDefinition pour un rôle spécifique - RBAC](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Voir qui a accès
Liste d’affectations d’accès RBAC, utilisez `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Liste des affectations de rôle dans une étendue spécifique
Vous pouvez voir toutes les affectations d’accès pour une ressource, un groupe de ressources ou un abonnement spécifié. Par exemple, pour voir les toutes les actives affectations pour un groupe de ressources, utilisez `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![Capture d’écran RBAC PowerShell-Get AzureRmRoleAssignment pour un groupe de ressources-](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Liste des rôles attribués à un utilisateur
Pour répertorier tous les rôles assignés à un utilisateur spécifié et les rôles qui sont affectés aux groupes auxquels appartient l’utilisateur, utilisez `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![Capture d’écran de PowerShell-Get AzureRmRoleAssignment pour un utilisateur - RBAC](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Liste des attributions de rôles administrateur et coadmin de service classique
Pour les affectations d’accès de liste pour l’administrateur d’abonnement classique et coadministrators, utilisez :

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Autoriser l’accès
### <a name="search-for-object-ids"></a>Recherche d’ID d’objet
Pour affecter un rôle, vous devez identifier l’objet (utilisateur, groupe ou application) et la portée.

Si vous ne connaissez pas l’ID d’abonnement, vous pouvez le trouver dans la blade **d’abonnements** sur le portail Azure. Pour savoir comment rechercher l’ID d’abonnement, consultez [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) sur MSDN.

Pour obtenir l’ID d’objet pour un groupe d’annonces d’Azure, utilisez :

    Get-AzureRmADGroup -SearchString <group name in quotes>

Pour obtenir l’ID d’objet pour une entité de sécurité AD Azure service ou une application, utilisez :

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Attribuer un rôle à une application au niveau de la portée de l’abonnement
Pour accorder l’accès à une application au niveau de la portée de l’abonnement, utilisez :

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![Capture d’écran de PowerShell-nouvelle AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Attribuer un rôle à un utilisateur au niveau de la portée de groupe de ressources
Pour accorder l’accès à un utilisateur dans la portée de groupe de ressources, utilisez :

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![Capture d’écran de PowerShell-nouvelle AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Attribuer un rôle à un groupe au niveau de la portée de ressource
Pour accorder l’accès à un groupe au niveau de la portée de ressource, utilisez :

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![Capture d’écran de PowerShell-nouvelle AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Supprimer l’accès
Pour supprimer l’accès d’utilisateurs, des groupes et des applications, utilisez :

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![Capture d’écran de PowerShell-supprimer AzureRmRoleAssignment - RBAC](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé
Pour créer un rôle personnalisé, vous devez utiliser le `New-AzureRmRoleDefinition` commande.

Lorsque vous créez un rôle personnalisé à l’aide de PowerShell, vous devez démarrer avec l’un des [rôles intégrés](role-based-access-built-in-roles.md). Modifiez les attributs pour ajouter les *Actions*, les *notActions*ou les *étendues* que vous voulez et puis enregistrez les modifications sous un nouveau rôle.

L’exemple suivant démarre avec le rôle de *Collaborateur de la Machine virtuelle* et l’utilise pour créer un rôle personnalisé appelé *Opérateur de Machine virtuelle*. Le nouveau rôle accorde l’accès à toutes les opérations de lecture de fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage*et *Microsoft.Network* et accorde les accès pour démarrer, redémarrer et contrôler les ordinateurs virtuels. Le rôle personnalisé peut être utilisé dans deux abonnements.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![Capture d’écran de PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## <a name="modify-a-custom-role"></a>Modifier un rôle personnalisé
Pour modifier un rôle personnalisé, commencez par utiliser le `Get-AzureRmRoleDefinition` commande pour récupérer la définition de rôle. Ensuite, apportez les modifications souhaitées à la définition de rôle. Enfin, utilisez la `Set-AzureRmRoleDefinition` commande pour enregistrer la définition de rôle.

L’exemple suivant ajoute les `Microsoft.Insights/diagnosticSettings/*` opération au rôle *Opérateur de Machine virtuelle* personnalisée.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![Capture d’écran de PowerShell-Set AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

L’exemple suivant ajoute un abonnement Azure aux étendues du rôle *Opérateur de Machine virtuelle* personnalisé peut être assignés.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2"
Set-AzureRmRoleDefinition -Role $role)
```

![Capture d’écran de PowerShell-Set AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, vous devez utiliser le `Remove-AzureRmRoleDefinition` commande.

L’exemple suivant supprime le rôle personnalisé *d’Opérateur de Machine virtuelle* .

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![Capture d’écran de PowerShell-supprimer AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Liste des rôles personnalisés
Pour répertorier les rôles qui sont disponibles pour l’assignation à un champ d’application, utilisez la `Get-AzureRmRoleDefinition` commande.

L’exemple suivant répertorie tous les rôles qui sont disponibles pour l’assignation de l’abonnement sélectionné.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![Capture d’écran de PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

Dans l’exemple suivant, le rôle personnalisé *d’Opérateur de Machine virtuelle* n’est pas disponible dans l’abonnement *Production4* car cet abonnement n’est pas dans **AssignableScopes** du rôle.

![Capture d’écran de PowerShell-Get AzureRmRoleDefinition - RBAC](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Voir aussi
- [À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md)
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
