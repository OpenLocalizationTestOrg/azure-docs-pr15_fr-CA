<properties 
    pageTitle="Verrouiller les ressources avec le Gestionnaire de ressources | Microsoft Azure" 
    description="Empêcher les utilisateurs de la mise à jour ou la suppression de certaines ressources en appliquant une restriction à tous les utilisateurs et les rôles." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>Verrouiller les ressources avec le Gestionnaire de ressources Azure

En tant qu’administrateur, vous devrez peut-être verrouiller un abonnement, un groupe de ressources ou une ressource pour empêcher d’autres utilisateurs de votre organisation accidentellement supprimer ou modifier des ressources critiques. Vous pouvez définir le niveau de verrouillage pour **CanNotDelete** ou **en lecture seule**. 

- **CanNotDelete** signifie que les utilisateurs autorisés peuvent toujours lire et modifier une ressource, mais ils ne peut le supprimer. 
- **En lecture seule** signifie que les utilisateurs autorisés peuvent lire à partir d’une ressource, mais ils ne peuvent pas supprimer ou exécuter des actions sur celle-ci. L’autorisation sur la ressource est limitée au rôle de **lecteur** . 

Application de **ReadOnly** peut entraîner des résultats inattendus parce que certaines opérations semblent lire réellement les opérations requièrent des actions supplémentaires. Par exemple, placer un verrou **en lecture seule** sur un compte de stockage empêche tous les utilisateurs listant les clés. La liste opération clés est gérée par une demande POST, car les clés retournées sont disponibles pour les opérations d’écriture. Pour un autre exemple, placer un verrou **en lecture seule** sur une ressource de Service d’application empêche l’Explorateur de serveurs Visual Studio à partir de l’affichage des fichiers de la ressource car cette interaction requiert un accès en écriture.

Contrairement au contrôle d’accès basé sur les rôles, les verrous de gestion vous permet d’appliquer une restriction entre tous les utilisateurs et les rôles. Pour en savoir plus sur la définition des autorisations pour les utilisateurs et les rôles, consultez [Contrôle d’accès basé sur les rôles d’Azure](./active-directory/role-based-access-control-configure.md).

Lorsque vous appliquez un verrou à une portée parent, toutes les ressources enfants héritent le même verrou. Même les ressources que vous ajoutez ultérieurement héritent le verrou du parent. Le verrou plus restrictif dans l’héritage est prioritaire.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Qui peut créer ou supprimer des verrous dans votre organisation

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès à **Microsoft.Authorization/\* ** ou **Microsoft.Authorization/locks/\* ** actions. Les rôles intégrés, seuls **propriétaire** et **Administrateur d’accès utilisateur** sont accordées à ces actions.

## <a name="creating-a-lock-through-the-portal"></a>Création d’un verrou via le portail

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Création d’un verrou dans un modèle

L’exemple suivant montre un modèle qui crée un verrou sur un compte de stockage. Le compte de stockage sur lequel appliquer le verrouillage est fourni en tant que paramètre. Le nom du verrou est créé en concaténant le nom de la ressource avec **/Microsoft.Authorization/** et le nom du verrou, dans ce cas **myLock**.

Le type fourni est spécifique au type de ressource. Pour le stockage, définissez le type sur « Microsoft.Storage/storageaccounts/providers/locks ».

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Création d’un verrou avec l’API REST

Vous pouvez verrouiller les ressources déployées avec l' [API REST pour les verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx). L’API REST vous permet de créer et de supprimer les verrous et de récupérer des informations sur les verrous existants.

Pour créer un verrou, exécutez :

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

La portée peut être un abonnement, le groupe de ressources ou la ressource. Le nom de verrou est tout ce que vous souhaitez appeler le verrou. Pour la version de l’api, utilisez **2015-01-01**.

Dans la demande, inclure un objet JSON qui spécifie les propriétés du verrou.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Pour obtenir des exemples, consultez [l’API REST pour les verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## <a name="creating-a-lock-with-azure-powershell"></a>Création d’un verrou avec Azure PowerShell

Vous pouvez verrouiller les ressources déployées avec Azure PowerShell à l’aide de **Nouvelle-AzureRmResourceLock** comme indiqué dans l’exemple suivant.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

PowerShell Azure fournit des autres commandes permettant d’utiliser des verrous, tels que les **AzureRmResourceLock de l’ensemble** d’un verrou de mise à jour et **Supprimer-AzureRmResourceLock** pour supprimer un verrou.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation de verrous sur les ressources, consultez [Verrouillage vers le bas de vos ressources de Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Pour en savoir plus sur l’organisation logique de vos ressources, reportez-vous à la section [en utilisant des balises pour organiser vos ressources](resource-group-using-tags.md)
- Pour modifier une ressource se trouve dans quel groupe de ressources, voir [déplacer des ressources vers le nouveau groupe de ressources](resource-group-move-resources.md)
- Vous pouvez appliquer des conventions et des restrictions sur votre abonnement avec des stratégies personnalisées. Pour plus d’informations, voir [Utiliser une stratégie pour la gestion des ressources et de contrôler l’accès](resource-manager-policy.md).
