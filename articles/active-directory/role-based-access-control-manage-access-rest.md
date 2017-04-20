<properties
    pageTitle="Gestion du contrôle d’accès basé sur les rôles avec l’API REST"
    description="Gestion du contrôle d’accès basé sur les rôles avec l’API REST"
    services="active-directory"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="multiple"
    ms.tgt_pltfrm="rest-api"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-role-based-access-control-with-the-rest-api"></a>Gestion du contrôle d’accès basé sur les rôles avec l’API REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

Basé sur le rôle de contrôle d’accès (RBAC) dans les API du Gestionnaire de ressources Azure Azure Portal vous permet de gérer l’accès aux ressources à un niveau de granularité fin et abonnement. Grâce à cette fonctionnalité, vous pouvez accorder l’accès pour les utilisateurs, groupes ou entités de service Active Directory en leur attribuant des rôles à une portée particulière.

## <a name="list-all-role-assignments"></a>Liste de toutes les affectations de rôle

Répertorie toutes les affectations de rôle au niveau de la portée spécifiée et subscopes.

Pour les affectations de rôle de liste, vous devez avoir accès à `Microsoft.Authorization/roleAssignments/read` opération au niveau de la portée. Tous les rôles prédéfinis ont accès à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* avec la portée pour laquelle vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{- version de l’api}* 2015-07-01.

3. Remplacez *{filter}* à la condition que vous souhaitez appliquer pour filtrer la liste d’affectation de rôle :

  - Liste des attributions de rôle pour seulement la portée spécifiée, non compris les attributions de rôle à subscopes :`atScope()`    
  - Liste des attributions de rôles pour un utilisateur spécifique, un groupe ou une application :`principalId%20eq%20'{objectId of user, group, or service principal}'`  
  - Liste des attributions de rôles pour un utilisateur spécifique, y compris celles héritées de groupes |`assignedTo('{objectId of user}')`

### <a name="response"></a>Réponse

Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Obtenir des informations sur une affectation de rôle

Obtient des informations sur un rôle unique spécifiée par l’identificateur d’affectation de rôle.

Pour obtenir des informations sur une affectation de rôle, vous devez avoir accès à `Microsoft.Authorization/roleAssignments/read` opération. Tous les rôles prédéfinis ont accès à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* avec la portée pour laquelle vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{id-affectation de rôle}* avec l’identificateur GUID de l’affectation de rôle.

3. Remplacez *{- version de l’api}* 2015-07-01.

### <a name="response"></a>Réponse

Code d’état : 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Créer une affectation de rôle

Créer une affectation de rôle au niveau de la portée spécifiée pour l’entité de sécurité spécifiée l’octroi du rôle spécifié.

Pour créer une affectation de rôle, vous devez avoir accès à `Microsoft.Authorization/roleAssignments/write` opération. Les rôles intégrés, seuls *propriétaire* et *Administrateur d’accès utilisateur* autorisés à accéder à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **PUT** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* l’étendue à laquelle vous souhaitez créer les attributions de rôle. Lorsque vous créez une affectation de rôle dans une étendue parent, toutes les étendues enfant héritent de la même affectation de rôle. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1   
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{id-affectation de rôle}* avec un nouveau GUID, qui devient l’identificateur GUID de la nouvelle attribution de rôle.

3. Remplacez *{- version de l’api}* 2015-07-01.

Pour le corps de la demande, fournir les valeurs au format suivant :

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nom de l’élément     | Obligatoire | Type de   | Description |
|------------------|----------|--------|-------------|
| roleDefinitionId | Oui      | Chaîne | Identificateur du rôle. Le format de l’identificateur est :`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId      | Oui      | Chaîne | objectId de l’entité AD Azure (utilisateur, groupe ou entité de service) à laquelle le rôle est affecté. |

### <a name="response"></a>Réponse

Code d’état : 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Supprimer une affectation de rôle

Supprimer une affectation de rôle au niveau de la portée spécifiée.

Pour supprimer une affectation de rôle, vous devez avoir accès à la `Microsoft.Authorization/roleAssignments/delete` opération. Les rôles intégrés, seuls *propriétaire* et *Administrateur d’accès utilisateur* autorisés à accéder à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **DELETE** à l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* l’étendue à laquelle vous souhaitez créer les attributions de rôle. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{id-affectation de rôle}* avec l’id d’affectation de rôle GUID.

3. Remplacez *{- version de l’api}* 2015-07-01.

### <a name="response"></a>Réponse

Code d’état : 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Lister tous les rôles

Répertorie tous les rôles qui sont disponibles pour l’assignation à la portée spécifiée.

Dans la liste des rôles, vous devez avoir accès à `Microsoft.Authorization/roleDefinitions/read` opération au niveau de la portée. Tous les rôles prédéfinis ont accès à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* avec la portée pour laquelle vous souhaitez répertorier les rôles. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressources /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{- version de l’api}* 2015-07-01.

3. Remplacez *{filter}* à la condition que vous souhaitez appliquer pour filtrer la liste des rôles :

  - Liste des rôles disponibles pour l’assignation à la portée spécifiée et une de ses étendues enfant :`atScopeAndBelow()`
  - Recherche d’un rôle à l’aide du nom complet exact : `roleName%20eq%20'{role-display-name}'`. Utilisez le formulaire codé URL du nom complet exact du rôle. Par exemple,`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Réponse

Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Obtenir des informations sur un rôle

Obtient des informations sur un rôle unique spécifiée par l’identificateur de définition de rôle. Pour obtenir des informations sur un rôle unique à l’aide de son nom complet, voir [Lister tous les rôles](role-based-access-control-manage-access-rest.md#list-all-roles).

Pour obtenir des informations sur un rôle, vous devez avoir accès à `Microsoft.Authorization/roleDefinitions/read` opération. Tous les rôles prédéfinis ont accès à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **GET** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* avec la portée pour laquelle vous souhaitez répertorier les attributions de rôle. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{id de la définition de rôle}* avec l’identificateur GUID de la définition de rôle.

3. Remplacez *{- version de l’api}* 2015-07-01.

### <a name="response"></a>Réponse

Code d’état : 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé
Créer un rôle personnalisé.

Pour créer un rôle personnalisé, vous devez avoir accès à `Microsoft.Authorization/roleDefinitions/write` opération sur tous les `AssignableScopes`. Les rôles intégrés, seuls *propriétaire* et *Administrateur d’accès utilisateur* autorisés à accéder à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **PUT** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* par première *AssignableScope* du rôle personnalisé. Les exemples suivants montrent comment spécifier la portée de différents niveaux.

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{id de la définition de rôle}* avec un nouveau GUID, qui devient l’identificateur GUID du nouveau rôle personnalisé.

3. Remplacez *{- version de l’api}* 2015-07-01.

Pour le corps de la demande, fournir les valeurs au format suivant :

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nom de l’élément | Obligatoire | Type de | Description |
|--------------|----------|------|-------------|
| nom         | Oui | Chaîne   | Identificateur GUID du rôle personnalisé.    |
| properties.roleName               | Oui | Chaîne   | Afficher le nom du rôle personnalisé. Maximum 128 caractères.                        |
| Properties.Description            | N°  | Chaîne   | Description du rôle personnalisé. Maximum 1024 caractères.                                               |
| Properties.type                   | Oui | Chaîne   | La valeur « CustomRole ».                                         |
| Properties.Permissions.actions    | Oui | String] | Un tableau de chaînes d’action indiquant les opérations accordées par le rôle personnalisé.             |
| properties.permissions.notActions | N°  | String] | Un tableau de chaînes d’action indiquant les opérations à exclure les opérations accordées par le rôle personnalisé. |
| properties.assignableScopes       | Oui | String] | Un tableau des portées dans laquelle le rôle personnalisé peut être utilisé.   |

### <a name="response"></a>Réponse

Code d’état : 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Mise à jour d’un rôle personnalisé

Modifier un rôle personnalisé.

Pour modifier un rôle personnalisé, vous devez avoir accès à `Microsoft.Authorization/roleDefinitions/write` opération sur tous les `AssignableScopes`. Les rôles intégrés, seuls *propriétaire* et *Administrateur d’accès utilisateur* autorisés à accéder à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **PUT** avec l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* par première *AssignableScope* du rôle personnalisé. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{id de la définition de rôle}* avec l’identificateur GUID du rôle personnalisé.

3. Remplacez *{- version de l’api}* 2015-07-01.

Pour le corps de la demande, fournir les valeurs au format suivant :

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nom de l’élément | Obligatoire | Type de | Description |
|--------------|----------|------|-------------|
| nom         | Oui      | Chaîne | Identificateur GUID du rôle personnalisé. |
| properties.roleName | Oui | Chaîne | Afficher le nom du rôle personnalisé mis à jour. |
| Properties.Description | N° | Chaîne | Description du rôle personnalisé mis à jour. |
| Properties.type | Oui | Chaîne | La valeur « CustomRole ». |
| Properties.Permissions.actions | Oui | String] | Un tableau de chaînes d’action indiquant les opérations à laquelle le rôle personnalisé mis à jour accorde l’accès. |
| properties.permissions.notActions | N° | String] | Un tableau de chaînes d’action indiquant les opérations à exclure les opérations qui autorise le détenteur du rôle personnalisé mis à jour. |
| properties.assignableScopes | Oui | String] | Un tableau des portées dans laquelle le rôle personnalisé mis à jour peut être utilisé. |

### <a name="response"></a>Réponse

Code d’état : 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

Supprimer un rôle personnalisé.

Pour supprimer un rôle personnalisé, vous devez avoir accès à `Microsoft.Authorization/roleDefinitions/delete` opération sur tous les `AssignableScopes`. Les rôles intégrés, seuls *propriétaire* et *Administrateur d’accès utilisateur* autorisés à accéder à cette opération. Pour plus d’informations sur les affectations de rôles et gère l’accès aux ressources d’Azure, consultez [Contrôle d’accès Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Demande

Utilisez la méthode **DELETE** à l’URI suivant :

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dans l’URI, apportez les substitutions suivantes pour personnaliser votre demande :

1. Remplacez *{étendue}* l’étendue à laquelle vous souhaitez supprimer la définition de rôle. Les exemples suivants montrent comment spécifier la portée de différents niveaux :

  - Abonnement : /subscriptions/ de {id d’abonnement}  
  - Groupe de ressources : /subscriptions/ {id d’abonnement} / resourceGroups/myresourcegroup1  
  - Ressource : /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Remplacez *{id de la définition de rôle}* avec l’id de définition de rôle GUID du rôle personnalisé.

3. Remplacez *{- version de l’api}* 2015-07-01.

### <a name="response"></a>Réponse

Code d’état : 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```


[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
