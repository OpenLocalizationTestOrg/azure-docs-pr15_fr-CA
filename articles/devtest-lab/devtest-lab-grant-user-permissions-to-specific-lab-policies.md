<properties
    pageTitle="Accorder des autorisations d’utilisateur aux stratégies de laboratoire spécifique | Microsoft Azure"
    description="Apprenez à accorder des autorisations d’utilisateur aux stratégies de laboratoire spécifique dans les laboratoires DevTest en fonction des besoins de chaque utilisateur"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>Accorder des autorisations d’utilisateur aux stratégies de laboratoire spécifique

## <a name="overview"></a>Vue d’ensemble

Cet article illustre comment utiliser PowerShell pour accorder des autorisations d’utilisateurs à une stratégie particulière de laboratoire. De cette façon, autorisations peuvent être appliquées selon les besoins de chaque utilisateur. Par exemple, vous pouvez souhaiter accorder à un utilisateur particulier permet de modifier les paramètres de stratégie d’ordinateur virtuel, mais pas les stratégies de coût.

## <a name="policies-as-resources"></a>Stratégies en tant que ressources

Comme expliqué dans l’article de [Contrôle d’accès basé sur les rôles d’Azure](../active-directory/role-based-access-control-configure.md) , RBAC permet la gestion d’accès à granularité fine des ressources pour Azure. L’utilisation de RBAC, vous pouvez séparer les droits au sein de votre équipe DevOps et accorder uniquement la quantité de l’accès aux utilisateurs dont ils ont besoin pour effectuer leur travail.

Dans les ateliers de DevTest, une stratégie est un type de ressource qui permet l’action RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Chaque stratégie de laboratoire est une ressource dans le type de ressource de stratégie et peut être assigné comme une portée d’un rôle RBAC.

Par exemple, pour accorder aux utilisateurs en lecture-écriture autorisation à la stratégie de **Tailles autorisées de machine virtuelle** , vous devez créer un rôle personnalisé qui fonctionne avec le **Microsoft.DevTestLab/labs/policySets/policies/** * action, puis affectez les utilisateurs appropriés à ce rôle personnalisé dans la portée du * *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Pour en savoir plus sur les rôles personnalisés dans RBAC, consultez le [contrôle d’accès personnalisée rôles](../active-directory/role-based-access-control-custom-roles.md).

##<a name="creating-a-lab-custom-role-using-powershell"></a>Création d’un rôle personnalisé de laboratoire à l’aide de PowerShell
Pour commencer, vous devez lire l’article suivant qui explique comment installer et configurer les applets de commande PowerShell de Azure : [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Une fois que vous avez configuré les applets de commande PowerShell d’Azure, vous pouvez effectuer les tâches suivantes :

- Liste de toutes les opérations et les actions pour un fournisseur de ressources
- Liste des actions dans un rôle particulier :
- Créer un rôle personnalisé

Le script PowerShell suivant illustre des exemples montrant comment effectuer ces tâches :

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Attribution d’autorisations à un utilisateur pour une stratégie spécifique à l’aide de rôles personnalisés
Une fois que vous avez défini vos rôles personnalisés, vous pouvez les affecter aux utilisateurs. Pour assigner un rôle personnalisé à un utilisateur, vous devez d’abord obtenir l' **ObjectId** représentant de cet utilisateur. Pour ce faire, utilisez l’applet de commande **Get-AzureRmADUser** .

Dans l’exemple suivant, **ObjectId** de l’utilisateur de *SomeUser* est 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Une fois que vous avez **ObjectId** de l’utilisateur et d’un nom de rôle personnalisé, vous pouvez attribuer ce rôle à l’utilisateur avec l’applet de commande **New-AzureRmRoleAssignment** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

Dans l’exemple précédent, la stratégie **AllowedVmSizesInLab** est utilisée. Vous pouvez utiliser les caractères suivants des stratégies :

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois vous avez accordé des autorisations utilisateur pour les stratégies de laboratoire spécifique, voici quelques étapes à prendre en compte :

- [Sécuriser l’accès à un laboratoire](devtest-lab-add-devtest-user.md).

- [Définir des stratégies de l’atelier](devtest-lab-set-lab-policy.md).

- [Créer un modèle de laboratoire](devtest-lab-create-template.md).

- [Créer des artefacts personnalisés pour vos ordinateurs virtuels](devtest-lab-artifact-author.md).

- [Ajouter un ordinateur virtuel avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md).
