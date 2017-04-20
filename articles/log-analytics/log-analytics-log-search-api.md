<properties
    pageTitle="Analytique de journal journal reste API de recherche | Microsoft Azure"
    description="Ce guide propose un didacticiel de base qui décrivent comment vous pouvez utiliser l’API REST de recherche Analytique de journal dans la Suite de gestion des opérations (OMS) et il fournit des exemples qui montrent comment utiliser les commandes."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="log-analytics-log-search-rest-api"></a>Recherche de journal de journal Analytique API REST

Ce guide propose un didacticiel de base qui décrivent comment vous pouvez utiliser l’API REST de journal Analytique recherche dans la Suite de gestion des opérations (OMS) et il fournit des exemples qui montrent comment utiliser les commandes. Parmi les exemples de cet article font référence à des perspectives opérationnelles, qui est le nom de la version précédente du journal Analytique.

## <a name="overview-of-the-log-search-rest-api"></a>Vue d’ensemble de la recherche du journal API REST

L’API REST de journal Analytique recherche est RESTful et sont accessibles via l’API du Gestionnaire de ressources Azure. Dans ce document vous trouverez des exemples où l’API est accessible par le biais de [ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source qui simplifie l’appel de l’API du Gestionnaire de ressources Azure. L’utilisation de ARMClient et de PowerShell est une des nombreuses options pour accéder à l’API de recherche de journal Analytique. Une autre option consiste à utiliser le module PowerShell de Azure pour OperationalInsights qui contient des applets de commande pour l’accès à la recherche. Grâce à ces outils, vous pouvez utiliser l’API du Gestionnaire de ressources Azure RESTful pour effectuer des appels vers des espaces de travail OMS et exécuter des commandes de recherche qu’ils contiennent. L’API aura pour résultat les résultats de recherche pour vous au format JSON, ce qui vous permet d’utiliser les résultats de la recherche de différentes façons par programme.

Le Gestionnaire de ressources Azure peut être utilisé via une [bibliothèque pour .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) ainsi qu’une [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Passez en revue les pages web liées pour en savoir plus.

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Didacticiel de base API REST de journal Analytique recherche

### <a name="to-use-the-arm-client"></a>Pour utiliser le Client ARM

1. Installez [Chocolatey](https://chocolatey.org/), qui est un de Gestionnaire de package Open Source pour Windows. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur, puis exécutez la commande suivante :

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Installer le ARMClient en exécutant la commande suivante :

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>Pour effectuer une recherche simple à l’aide de la ARMClient

1. Connectez-vous à votre compte Microsoft ou OrgID :

    ```
    armclient login
    ```

    Connexion réussie répertorie tous les abonnements liés pour le compte donné :

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtenir des espaces de travail Microsoft Operations Management Suite :

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Un appel Get affiche tous les espaces de travail liés à l’abonnement :

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Créez votre variable de recherche :

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Rechercher à l’aide de votre nouvelle variable de recherche :

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Exemples de référence API REST de recherche Analytique de journal
Les exemples suivants vous montrent comment vous pouvez utiliser l’API de recherche.

### <a name="search---actionread"></a>Recherche - Action/lecture

**Url de l’exemple :**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Demande de :**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
Le tableau suivant décrit les propriétés qui sont disponibles.

|**Propriété**|**Description**|
|---|---|
|Retour au début|Le nombre maximal de résultats à retourner.|
|Mettez en surbrillance|Contient des paramètres pre et post, utilisées généralement pour mettre en surbrillance les champs correspondants|
|versions antérieures|La chaîne donnée à vos champs de correspondance de préfixes.|
|Publier|Ajoute la chaîne donnée à vos champs associés.|
|requête|La requête de recherche utilisée pour recueillir et retourner des résultats.|
|début|Début de la période que vous souhaitez que les résultats se trouve dans.|
|fin|La fin de la période que vous souhaitez que les résultats se trouve dans.|


**Réponse :**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Rechercher / {ID} - Action/lecture

**Demander le contenu d’une recherche enregistrée :**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Si la recherche renvoie le statut « En attente », puis en interrogeant les résultats mis à jour est possible par le biais de cette API. Après 6 min, le résultat de la recherche est supprimé du cache et HTTP supprimé sera renvoyée. Si la demande de recherche initiale a renvoyé un état de « Réussi » immédiatement, il ne sera pas ajouté au cache à l’origine de cette API retourner HTTP supprimé si interrogée. Le contenu d’un résultat HTTP 200 sera dans le même format que la demande de recherche initiale uniquement avec les valeurs mises à jour.

### <a name="saved-searches---rest-only"></a>Enregistrées recherches - reste uniquement

**Demander la liste des recherches enregistrées :**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Méthodes prises en charge : GET, PUT supprimer

Prise en charge des méthodes de collection : obtenir

Le tableau suivant décrit les propriétés qui sont disponibles.

|Propriété|Description|
|---|---|
|ID|L’identificateur unique.|
|ETag|**Requis pour le correctif**. Mise à jour par le serveur sur chaque écriture. Valeur doit être égale à la valeur stockée en cours ou ' *' pour mettre à jour. 409 est retournée pour les valeurs anciennes ou non valide.|
|Properties.Query|**Requis**. La requête de recherche.|
|properties.displayName|**Requis**. Le nom d’utilisateur défini d’affichage de la requête. Si modélisé comme une ressource d’Azure, il s’agit d’une balise.|
|Properties.Category|**Requis**. Catégorie de la requête définis par l’utilisateur. Modélisé comme une ressource Azure c’est si une balise.|

>[AZURE.NOTE] L’API de recherche de journal Analytique actuellement renvoie créés par l’utilisateur lors de l’interrogation pour les recherches enregistrées dans un espace de travail de recherches enregistrées. L’API ne renvoie pas les recherches enregistrées, fournis par les solutions pour l’instant. Cette fonctionnalité va être ajoutée à une date ultérieure.

### <a name="create-saved-searches"></a>Créer des recherches enregistrées

**Demande de :**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Recherches enregistrée de suppression

**Demande de :**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Recherche de mise à jour de l’enregistrement

 **Demande de :**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Métadonnées - JSON uniquement

Voici un moyen de voir les champs pour tous les types de journaux pour les données collectées dans votre espace de travail. Par exemple, si vous souhaitez que vous savez si le type d’événement possède un champ nommé ordinateur, ceci est une façon de chercher et de confirmer.

**Demande de champs :**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Réponse :**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

Le tableau suivant décrit les propriétés qui sont disponibles.

|**Propriété**|**Description**|
|---|---|
|nom|Nom du champ.|
|displayName|Le nom complet du champ.|
|type de|Le Type de la valeur du champ.|
|facetable|Combinaison en cours 'indexée', ' stockées » et les propriétés de 'facette'.|
|afficher|Propriété de 'afficher' en cours. True si le champ est visible dans la recherche.|
|ownerType|Réduit uniquement les types qui appartiennent à l’adresse IP d’onboarded.|


## <a name="optional-parameters"></a>Paramètres facultatifs
Les informations suivantes décrivent les paramètres facultatifs disponibles.

### <a name="highlighting"></a>Mise en surbrillance

Le paramètre « Surbrillance » est un paramètre facultatif que vous pouvez utiliser pour demander le sous-système de recherche incluent un ensemble de marques dans sa réponse.

Ces marqueurs indiquent le début et de fin de texte mis en surbrillance qui répond aux critères dans votre requête de recherche.
Vous pouvez spécifier les marqueurs de début et de fin qui seront utilisés par search pour encapsuler le terme mis en surbrillance.

**Exemple de requête de recherche**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Résultat de l’exemple :**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Notez que le résultat ci-dessus contienne un message d’erreur qui a été préfixé et ajouté.

## <a name="computer-groups"></a>Groupes d’ordinateurs

Groupes d’ordinateurs sont des recherches enregistrées spéciales qui retourne un ensemble d’ordinateurs.  Vous pouvez utiliser un groupe d’ordinateurs dans d’autres requêtes pour limiter les résultats aux ordinateurs dans le groupe.  Un groupe d’ordinateurs est implémenté comme une recherche enregistrée avec une balise de groupe avec une valeur de l’ordinateur.

Voici un exemple de réponse pour un groupe d’ordinateurs.

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>Récupération des groupes d’ordinateurs

Utilisez la méthode Get avec l’ID de groupe pour récupérer un groupe d’ordinateurs.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Création ou mise à jour d’un groupe d’ordinateurs

Pour utiliser la méthode Put avec un seul enregistrement ID de recherche pour créer un nouveau groupe d’ordinateurs. Si vous utilisez un ID de groupe d’ordinateur existant, puis qu’il sera modifié. Lorsque vous créez un groupe d’ordinateurs dans la console de l’OMS, l’ID est créé à partir du groupe et le nom.

La requête utilisée pour la définition de groupe doit renvoyer un ensemble d’ordinateurs pour le groupe fonctionner correctement.  Il est recommandé que vous terminez votre requête avec *| Ordinateur distinct* pour vérifier les données retournées sont correctes.

La définition de la recherche enregistrée doit inclure une balise nommée Group avec une valeur de l’ordinateur pour la recherche à classer sous la forme d’un groupe d’ordinateurs.

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>Suppression de groupes d’ordinateurs

Utilisez la méthode Delete avec l’ID de groupe pour supprimer un groupe d’ordinateurs.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) à créer des requêtes à l’aide des champs personnalisés pour des critères.
