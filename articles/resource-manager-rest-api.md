<properties
   pageTitle="Le Gestionnaire de ressources reste API | Microsoft Azure"
   description="Une vue d’ensemble des exemples de l’authentification et l’utilisation des API reste de gestionnaire de ressources"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>Le Gestionnaire de ressources reste API

> [AZURE.SELECTOR]
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [Portail](./azure-portal/resource-group-portal.md) 
- [API REST](resource-manager-rest-api.md)

Derrière chaque appel au Gestionnaire de ressources Azure, derrière chaque modèle déployé, derrière chaque compte de stockage configuré est un ou plusieurs appels à l’API RESTful du Gestionnaire de ressources Azure. Cette rubrique est consacrée à ces API et comment vous pouvez les appeler sans utiliser un kit de développement logiciel du tout. Cela peut être très utile si vous souhaitez que le contrôle total de toutes les demandes pour Azure ou si le Kit de développement pour votre langage par défaut n’est pas disponible ou ne prend pas en charge les opérations à effectuer.

Cet article ne passera pas par le biais de chaque API est exposée dans Azure, mais utilise plutôt quelques-uns à titre d’exemple comment vous continuez et que vous y connecter. Si vous comprenez les notions de base vous pouvez puis continuez et la [Référence de l’API reste Azure le Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour trouver des informations détaillées sur la façon d’utiliser le reste de l’API de lecture.

## <a name="authentication"></a>Authentification
Authentification pour ARM est gérée par Azure Active Directory (AD). Pour vous connecter à n’importe quelle API, vous devez tout d’abord s’authentifier avec AD Azure à recevoir un jeton d’authentification, que vous pouvez passer à chaque demande. Comme nous sommes décrivant un pur appel directement aux API reste, nous supposerons également que vous ne voulez pas authentifier avec un mot de passe nom d’utilisateur normal dans lequel un pop-haut-écran peut vous demandera un nom d’utilisateur et le mot de passe et peut-être même les autres mécanismes d’authentification utilisés dans deux scénarios d’authentification de facteur. Par conséquent, nous allons créer ce qu’on appelle une Application AD Azure et un Service Principal qui sera utilisé pour la connexion avec. Mais n’oubliez pas que AD Azure prend en charge plusieurs procédures d’authentification et chacun d'entre eux pourrait servir pour récupérer ce jeton d’authentification dont nous avons besoin pour les demandes suivantes API.
Suivez les [Application de AD Azure de créer et de principal du Service](./resource-group-create-service-principal-portal.md) pour obtenir des instructions étape par étape.

### <a name="generating-an-access-token"></a>Génération d’un jeton d’accès 
Authentification par rapport à une publicité Azure est effectuée en appelant Azure AD, situé à login.microsoftonline.com. Pour s’authentifier, vous devez disposer des informations suivantes :

* ID de clients Azure AD (le nom de cette publicité Azure que vous utilisez pour ouvrir une session, souvent la même que celle de votre société, mais pas nécessaire)
* ID de l’application (prise au cours de l’étape de création d’application Azure AD)
* Mot de passe (que vous avez sélectionnée lors de la création de l’Application AD Azure)

Dans la sous requête HTTP veillez à remplacer « Azure AD Client ID », « ID d’Application » et « Password » avec les valeurs correctes.

**Requête HTTP générique :**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... est (si l’authentification réussit) génère une réponse similaire à ceci :

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(L’access_token dans la réponse ci-dessus ont été raccourcies pour améliorer la lisibilité)

**Génération du jeton d’accès à l’aide de Bash :**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Génération du jeton d’accès à l’aide de PowerShell :**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La réponse contient un jeton d’accès, des informations sur la durée pendant laquelle ce jeton est valide et informations sur la ressource que vous pouvez utiliser ce jeton pour.
Le jeton d’accès que vous avez reçu de l’appel précédent de HTTP doit être passé dans pour toutes les demandes à l’API ARM sous la forme d’un en-tête nommé « Autorisation » avec la valeur « Support YOUR_ACCESS_TOKEN ». Notez l’espace entre « Porteur » et votre jeton d’accès.

Comme vous pouvez le voir dans le résultat de HTTP ci-dessus, le jeton est valide pour une période donnée de temps pendant laquelle vous devez mettre en cache et réutilisation de ce même jeton. Même s’il est possible de s’authentifier par rapport à une publicité Azure pour chaque appel de l’API, il serait très inefficace.

## <a name="calling-arm-rest-apis"></a>Appel ARM reste API

[Azure Resource Manager reste API sont décrits ici](https://msdn.microsoft.com/library/azure/dn790568.aspx) et il l’hors de la portée de ce didacticiel documenter l’utilisation de chaque. Cette documentation utilise uniquement quelques API pour expliquer l’utilisation de base des API et une fois que nous vous reportez-vous à la documentation officielle.

### <a name="list-all-subscriptions"></a>Liste de tous les abonnements

Une des opérations plus simples que vous pouvez faire est de répertorier les abonnements disponibles auxquels vous pouvez accéder. Dans la demande, vous pouvez voir comment le jeton d’accès est transmis comme un en-tête ci-dessous.

(Remplacez YOUR_ACCESS_TOKEN par votre jeton d’accès réel.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... et par conséquent, vous obtiendrez une liste d’abonnements autorisé pour accéder à cette entité de sécurité du Service

(ID d’abonnement ci-dessous ont été raccourcies pour des raisons de lisibilité)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Liste de tous les groupes de ressources pour un abonnement spécifique

Toutes les ressources disponibles avec les API ARM sont imbriqués à l’intérieur d’un groupe de ressources. Nous allons requête ARM pour les groupes de ressources existants dans notre abonnement à l’aide du au-dessous de la demande HTTP GET. Notez comment l’ID d’abonnement est passé en tant que partie de l’URL cette fois-ci.

(Remplacez YOUR_ACCESS_TOKEN et un subscription_id donnés par votre véritable jeton d’accès et ID d’abonnement)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La réponse dépend de si vous avez des groupes de ressources définis et dans l’affirmative, combien.

(ID d’abonnement ci-dessous ont été raccourcies pour des raisons de lisibilité)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Jusqu’ici nous avons seulement été interrogeant les API ARM pour plus d’informations, il est temps de nous créer à la place de certaines ressources et nous allons commencer par la plus simple de toutes les, un groupe de ressources. La requête HTTP suivante crée un nouveau groupe de ressources dans une région de votre choix et y ajouter une ou plusieurs balises (l’exemple ci-dessous en réalité ajoute une balise).

(Remplacez YOUR_ACCESS_TOKEN, ID_ABONNEMENT, nom_groupe_ressource avec votre réel jeton d’accès, ID d’abonnement et le nom du groupe de ressources que vous voulez créer)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

En cas de réussite, vous obtiendrez une réponse similaire à ce

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Vous avez créé un groupe de ressources dans Azure. Félicitations !

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Déployer des ressources à un groupe de ressources à l’aide d’un modèle de ARM

Avec ARM, vous pouvez déployer vos ressources à l’aide de modèles de ARM. Un modèle ARM définit plusieurs ressources et leurs dépendances. Pour cette section nous supposerons que vous êtes familiarisé avec les modèles ARM et nous vous montrera simplement comment faire appel à l’API pour démarrer le déploiement d’un. Une documentation détaillée des modèles de ARM se trouve ici.

Déploiement d’un modèle ARM ne diffère beaucoup comment vous appeler d’autres API. Un aspect important est que le déploiement d’un modèle peut prendre beaucoup de temps, en fonction de ce qui est à l’intérieur du modèle et l’appel de l’API retourne juste et c’est à vous en tant que développeur pour demander l’état du déploiement afin de déterminer lorsque le déploiement est terminé.

Pour cet exemple, nous allons utiliser un modèle de ARM exposée publiquement disponible sur [GitHub](https://github.com/Azure/azure-quickstart-templates). Le modèle que nous allons utiliser déploiera une VM Linux pour la région Ouest. Bien que ce modèle aura le modèle disponible dans un référentiel public tels que GitHub, vous pouvez également sélectionner pour passer du modèle complet dans le cadre de la demande. Notez que nous fournir des valeurs de paramètre dans le cadre de la demande qui sera utilisée dans le modèle utilisé.

(Remplacez SUBSCRIPTION_ID, nom_groupe_ressource, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD et DNS_NAME_FOR_PUBLIC_IP pour les valeurs appropriées pour votre demande)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La réponse JSON assez longue pour cette demande ont été omis pour améliorer la lisibilité de cette documentation. La réponse contient des informations sur le déploiement basé sur un modèle que vous venez de créer.

