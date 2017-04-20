<properties
    pageTitle="Les rôles personnalisés dans Azure RBAC | Microsoft Azure"
    description="Apprenez à définir des rôles personnalisés avec contrôle d’accès Azure Role-Based pour la gestion d’identité plus précise dans votre abonnement Azure."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="kgremban"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/25/2016"
    ms.author="kgremban"/>


# <a name="custom-roles-in-azure-rbac"></a>Rôles personnalisés dans Azure RBAC


Créer un rôle personnalisé de contrôle d’accès dans Azure Role-Based (RBAC) si aucun des rôles intégrés répond à vos besoins d’accès spécifique. Des rôles personnalisés peuvent être créés à l’aide de [PowerShell d’Azure](role-based-access-control-manage-access-powershell.md), [Azure Command-Line Interface](role-based-access-control-manage-access-azure-cli.md) (CLI) et l' [API REST](role-based-access-control-manage-access-rest.md). Tout comme les rôles intégrés, les rôles personnalisés peuvent être affectés à des utilisateurs, des groupes et des applications au niveau d’abonnement, le groupe de ressources et portées de ressource. Des rôles personnalisés sont stockés dans un locataire AD Azure et peuvent être partagées entre tous les abonnements qui utilisent ces clients comme répertoire Azure AD pour l’abonnement.

Voici un exemple d’un rôle personnalisé pour la surveillance et le redémarrage des ordinateurs virtuels :

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Actions
La propriété **Actions** d’un rôle personnalisé spécifie les opérations Azure auquel le rôle accorde l’accès. Il s’agit d’une collection de chaînes d’opération qui identifient les opérations sécurisables de fournisseurs de ressources Azure. Chaînes d’opération qui contiennent des caractères génériques (\*) accorder l’accès à toutes les opérations qui correspondent à la chaîne d’opération. Par exemple :

-   `*/read`attribution de l’accès aux opérations de lecture pour tous les types de ressources de tous les fournisseurs de ressources Azure.
-   `Microsoft.Network/*/read`attribution de l’accès aux opérations de lecture pour tous les types de ressources dans le fournisseur de ressources Microsoft.Network d’Azure.
-   `Microsoft.Compute/virtualMachines/*`accorde l’accès à toutes les opérations des machines virtuelles et de son enfant des types de ressource.
-   `Microsoft.Web/sites/restart/Action`attribution de l’accès à des sites Web de redémarrer.

Utilisez `Get-AzureRmProviderOperation` (dans PowerShell) ou `azure provider operations show` (dans l’infrastructure du langage commun Azure) pour les opérations de la liste de fournisseurs de ressources Azure. Vous pouvez également utiliser ces commandes pour vérifier qu’une chaîne d’opération est valide et pour développer les chaînes d’opération générique.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Capture d’écran de PowerShell - Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | Opération de texte intégral, NomOpération](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Afficher d’opérations fournisseur de capture d’écran - azure CLI Azure » Microsoft.Compute/virtualMachines/\*/action » ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Utilisez la propriété **NotActions** si l’ensemble des opérations que vous souhaitez autoriser est plus facilement défini par exclusion des opérations restreintes. L’accès accordé par un rôle personnalisé est calculé en soustrayant les opérations **NotActions** dans les opérations **d’Actions** .

> [AZURE.NOTE] Si un utilisateur est affecté à un rôle qui exclut une opération dans **NotActions**et un second rôle qui accorde l’accès à la même opération est attribué, l’utilisateur pourra effectuer cette opération. **NotActions** n’est pas une règle de refus – il s’agit simplement d’un moyen pratique de créer un jeu d’opérations autorisées lors d’opérations spécifiques doivent être exclus.

## <a name="assignablescopes"></a>AssignableScopes
La propriété **AssignableScopes** du rôle personnalisé spécifie les étendues (abonnements, groupes de ressources ou ressources) dans lequel le rôle personnalisé est disponible pour l’affectation. Vous pouvez rendre le rôle personnalisé d’affectation uniquement les abonnements ou les groupes de ressources qui en ont besoin et n’encombrent pas expérience de l’utilisateur pour le reste des abonnements ou des groupes de ressources.

Voici quelques exemples d’étendues assignables valides :

-   « abonnements/c276fc76-9cd4-44c9-99a7-4fd71546436e », « / abonnements/e91d47c4-76f3-4271-a796-21b4ecfe3624 » - rend le rôle disponibles pour l’assignation dans deux abonnements.
-   « abonnements/c276fc76-9cd4-44c9-99a7-4fd71546436e » - rend le rôle disponibles pour l’assignation dans un seul abonnement.
-  « / abonnements/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/réseau » - rend le rôle disponibles pour l’assignation que dans le groupe de ressources réseau.

> [AZURE.NOTE] Vous devez utiliser au moins un abonnement, groupe de ressources ou ID de ressource.

## <a name="custom-roles-access-control"></a>Contrôle d’accès des rôles personnalisés
La propriété **AssignableScopes** du rôle personnalisé contrôle également qui peut afficher, modifier et supprimer le rôle.

- Qui peut créer un rôle personnalisé ?
    Les propriétaires (et administrateurs de l’accès utilisateur) des abonnements, des ressources et des groupes de ressources peuvent créer des rôles personnalisés pour une utilisation dans ces zones.
    L’utilisateur qui a créé le rôle doit être en mesure d’effectuer `Microsoft.Authorization/roleDefinition/write` opération sur tous les **AssignableScopes** du rôle.

- Personnes autorisées à modifier un rôle personnalisé ?
    Les propriétaires (et administrateurs de l’accès utilisateur) des abonnements, des ressources et des groupes de ressources peuvent modifier les rôles personnalisés dans ces zones. Les utilisateurs doivent être en mesure d’effectuer la `Microsoft.Authorization/roleDefinition/write` opération sur tous les **AssignableScopes** d’un rôle personnalisé.

- Qui peut afficher des rôles personnalisés ?
    Tous les rôles intégrés dans Azure RBAC autorisent l’affichage des rôles qui sont disponibles pour l’assignation. Les utilisateurs qui peuvent effectuer la `Microsoft.Authorization/roleDefinition/read` opération avec une portée peut afficher les rôles RBAC qui sont disponibles pour l’assignation à cette portée.

## <a name="see-also"></a>Voir aussi
- [Contrôle d’accès rôle](role-based-access-control-configure.md): mise en route de RBAC dans Azure portal.
- Découvrez comment gérer l’accès à :
    - [PowerShell](role-based-access-control-manage-access-powershell.md)
    - [CLI Azure](role-based-access-control-manage-access-azure-cli.md)
    - [API REST](role-based-access-control-manage-access-rest.md)
- [Rôles intégrés](role-based-access-built-in-roles.md): obtenir des informations sur les rôles qui sont fournis par défaut dans RBAC.
