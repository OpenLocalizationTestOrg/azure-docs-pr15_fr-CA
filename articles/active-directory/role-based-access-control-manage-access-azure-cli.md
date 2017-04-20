<properties
    pageTitle="Gérer le contrôle d’accès basé sur les rôles (RBAC) avec l’interface CLI Azure | Microsoft Azure"
    description="Découvrez comment gérer les rôles-contrôle d’accès (RBAC) avec l’interface de ligne de commande Azure en répertoriant les rôles et actions de rôle et en attribuant des rôles aux étendues d’abonnement et de l’application."
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

# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gérer le contrôle d’accès basée sur les rôles avec l’interface de ligne de commande Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

Vous pouvez utiliser le contrôle d’accès basée sur les rôles (RBAC) dans le portail d’Azure et d’Azure le Gestionnaire de ressources API pour gérer l’accès à votre abonnement et les ressources à un niveau de granularité fin. Grâce à cette fonctionnalité, vous pouvez accorder l’accès pour les utilisateurs, groupes ou entités de service Active Directory en leur attribuant des rôles à une portée particulière.

Avant de pouvoir utiliser l’interface de ligne de commande (CLI) Azure gestion RBAC, vous devez disposer des éléments suivants :

- Azure CLI version 0.8.8 ou une version ultérieure. Pour installer la version la plus récente et l’associer à votre abonnement Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../xplat-cli-install.md).
- Gestionnaire de ressources Azure dans Azure CLI. Pour plus d’informations, consultez [à l’aide de la CLI d’Azure avec le Gestionnaire de ressources](../xplat-cli-azure-resource-manager.md) .

## <a name="list-roles"></a>Liste des rôles

### <a name="list-all-available-roles"></a>Lister tous les rôles disponibles
Pour répertorier tous les rôles disponibles, utilisez :

        azure role list

L’exemple suivant affiche la liste de *tous les rôles disponibles*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Capture d’écran de la ligne de commande - liste rôle azure - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Liste des actions d’un rôle
Pour répertorier les actions d’un rôle, utilisez :

    azure role show "<role name>"

L’exemple suivant montre les actions des rôles *collaborateur* et *Collaborateur de Machine virtuelle* .

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Capture d’écran de la ligne de commande - show rôle azure - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##  <a name="list-access"></a>Accès à la liste
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Liste des attributions de rôle efficace sur un groupe de ressources
Pour afficher les attributions de rôle qui existent dans un groupe de ressources, utilisez :

    azure role assignment list --resource-group <resource group name>

L’exemple suivant montre les affectations de rôle dans le groupe *pharma-ventes-projecforcast* .

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Ligne de commande RBAC Azure - liste des affectations de rôle azure par groupe-capture d’écran](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Liste des attributions de rôles pour un utilisateur
Pour afficher les attributions de rôle pour un utilisateur spécifique et les affectations qui sont affectées aux groupes de l’utilisateur, utilisez :

    azure role assignment list --signInName <user email>

Vous pouvez également afficher les attributions de rôles qui sont héritées de groupes en modifiant la commande :

    azure role assignment list --expandPrincipalGroups --signInName <user email>

L’exemple suivant indique les affectations de rôle sont accordées à la *sameert@aaddemo.com* utilisateur. Cela inclut les rôles qui sont attribuées directement à l’utilisateur et les rôles qui sont héritées de groupes.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Capture d’écran de la ligne de commande - liste d’affectation de rôle azure par utilisateur - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##  <a name="grant-access"></a>Autoriser l’accès
Pour accorder l’accès une fois que vous avez identifié le rôle que vous souhaitez affecter, utilisez :

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Attribuer un rôle à un groupe au niveau de la portée de l’abonnement
Pour affecter un rôle à un groupe au niveau de la portée de l’abonnement, utilisez :

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L’exemple suivant assigne le rôle de *lecteur* à *équipe de Christine Koch* à la portée de *l’abonnement* .


![Ligne de commande Azure RBAC - affectation de rôle azure créer par groupe-capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Attribuer un rôle à une application au niveau de la portée de l’abonnement
Pour affecter un rôle à une application au niveau de la portée de l’abonnement, utilisez :

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L’exemple suivant accorde au rôle de *collaborateur* pour une application *Azure AD* sur l’abonnement sélectionné.

 ![Ligne de commande Azure RBAC - affectation de rôle azure créer par application](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Attribuer un rôle à un utilisateur au niveau de la portée de groupe de ressources
Pour affecter un rôle à un utilisateur au niveau de la portée de groupe de ressources, utilisez :

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

L’exemple suivant accorde au rôle de *Contributeur de la Machine virtuelle* à *samert@aaddemo.com* utilisateur au niveau de l’étendue du groupe ressources *Pharma-ventes-ProjectForcast* .

![Ligne de commande Azure RBAC - affectation de rôle azure créer par utilisateur-capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Attribuer un rôle à un groupe au niveau de la portée de ressource
Pour affecter un rôle à un groupe au niveau de la portée de ressource, utilisez :

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

L’exemple suivant accorde au rôle de *Collaborateur de l’ordinateur virtuel* à un groupe *d’annonces Azure* sur un *sous-réseau*.

![Ligne de commande Azure RBAC - affectation de rôle azure créer par groupe-capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##  <a name="remove-access"></a>Supprimer l’accès
Pour supprimer une affectation de rôle, utilisez :

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

L’exemple suivant supprime l’affectation de rôle *Contributeur de la Machine virtuelle* à partir de la *sammert@aaddemo.com* l’utilisateur sur le groupe de ressources *Pharma-ventes-ProjectForcast* .
Puis, l’exemple supprime l’affectation de rôle à partir d’un groupe sur l’abonnement.

![Capture d’écran de la ligne de commande - Supprimer le devoir rôle azure - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé
Pour créer un rôle personnalisé, utilisez :

    azure role create --inputfile <file path>

L’exemple suivant crée un rôle personnalisé appelé *Opérateur de Machine virtuelle*. Le rôle personnalisé accorde l’accès à toutes les opérations de lecture de fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage*et *Microsoft.Network* et accorde les accès pour démarrer, redémarrer et contrôler les ordinateurs virtuels. Le rôle personnalisé peut être utilisé dans deux abonnements. Cet exemple utilise un fichier JSON en tant qu’entrée.

![Capture d’écran de JSON - définition de rôle personnalisé :](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Ligne de commande Azure RBAC - créer un rôle azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Modifier un rôle personnalisé

Pour modifier un rôle personnalisé, utilisez d’abord la `azure role show` commande pour récupérer la définition de rôle. Ensuite, apportez les modifications souhaitées dans le fichier de définition de rôle. Enfin, utilisez `azure role set` pour enregistrer la définition de rôle.

    azure role set --inputfile <file path>

L’exemple suivant ajoute l’opération *Microsoft.Insights/diagnosticSettings/* pour les **Actions**et un abonnement Azure à **AssignableScopes** du rôle personnalisé opérateur de Machine virtuelle.

![JSON - modifier la définition de rôle personnalisé - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Capture d’écran de la ligne de commande - jeu de rôle azure - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, utilisez d’abord la `azure role show` commande pour déterminer l' **ID** du rôle. Utilisez ensuite le `azure role delete` pour supprimer le rôle en spécifiant l' **ID de**commande.

L’exemple suivant supprime le rôle personnalisé *d’Opérateur de Machine virtuelle* .

![Capture d’écran de la ligne de commande - Suppression de rôle azure - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Liste des rôles personnalisés

Pour répertorier les rôles qui sont disponibles pour l’assignation à un champ d’application, utilisez la `azure role list` commande.

L’exemple suivant répertorie tous les rôles qui sont disponibles pour l’assignation de l’abonnement sélectionné.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Capture d’écran de la ligne de commande - liste rôle azure - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

Dans l’exemple suivant, le rôle personnalisé *d’Opérateur de Machine virtuelle* n’est pas disponible dans l’abonnement *Production4* car cet abonnement n’est pas dans **AssignableScopes** du rôle.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Capture d’écran de la ligne de commande - liste rôle azure pour des rôles personnalisés - RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## <a name="rbac-topics"></a>Rubriques RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
