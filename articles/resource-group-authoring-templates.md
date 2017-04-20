<properties
   pageTitle="Création de modèles de gestionnaire de ressources Azure | Microsoft Azure"
   description="Créer des modèles d’Azure le Gestionnaire de ressources à l’aide de la syntaxe déclarative de JSON pour déployer des applications Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>Création de modèles du Gestionnaire de ressources Azure

Cette rubrique décrit la structure d’un modèle de gestionnaire de ressources Azure. Il présente les différentes sections d’un modèle et les propriétés qui sont disponibles dans ces sections. Le modèle est composé de JSON et des expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. 

Pour afficher le modèle pour les ressources que vous avez déjà déployé, consultez [exportation d’un modèle de gestionnaire de ressources Azure à partir de ressources existantes](resource-manager-export-template.md). Pour obtenir des instructions sur la création d’un modèle, consultez [Procédure pas à pas de gestionnaire de ressources du modèle](resource-manager-template-walkthrough.md). Pour obtenir des recommandations sur la création de modèles, consultez [méthodes conseillées pour la création de modèles du Gestionnaire de ressources Azure](resource-manager-template-best-practices.md).

Un bon éditeur de JSON peut simplifier la tâche de création de modèles. Pour plus d’informations sur l’utilisation de Visual Studio avec vos modèles, consultez [Création et le déploiement de groupes de ressources Azure via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Pour plus d’informations sur l’utilisation de Code de VS, consultez [utilisation des modèles du Gestionnaire de ressources Azure dans le Code de Visual Studio](resource-manager-vs-code.md).

Limiter la taille de votre modèle de 1 Mo et chaque fichier de paramètre à 64 Ko. La limite de 1 Mo s’applique à l’état final du modèle après que qu’il a été développé avec des définitions de ressource itératif et des valeurs pour les variables et les paramètres. 

## <a name="template-format"></a>Format modèle

Dans la structure la plus simple, un modèle contient les éléments suivants :

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nom de l’élément   | Obligatoire | Description
| :------------: | :------: | :----------
| $schema        |   Oui    | Emplacement du fichier de schéma JSON qui décrit la version de la langue du modèle. Utiliser l’URL indiqué dans l’exemple précédent.
| contentVersion |   Oui    | Version du modèle (par exemple 1.0.0.0). Vous pouvez fournir une valeur pour cet élément. Lorsque vous déployez des ressources à l’aide du modèle, cette valeur peut être utilisée pour s’assurer que le modèle est utilisé.
| paramètres     |   N°     | Valeurs fournies lorsque le déploiement est exécutée pour personnaliser le déploiement de la ressource.
| variables      |   N°     | Valeurs qui sont utilisées en tant que fragments JSON dans le modèle pour simplifier des expressions de langage de modèle.
| ressources      |   Oui    | Types de ressources qui sont déployées ou mises à jour dans un groupe de ressources.
| sorties        |   N°     | Valeurs qui sont retournées après le déploiement.

Nous allons examiner les sections du modèle plus en détail plus loin dans cette rubrique. Pour le moment, nous étudierons certains de la syntaxe qui composent le modèle.

## <a name="expressions-and-functions"></a>Les expressions et fonctions

La syntaxe de base du modèle est JSON. Toutefois, les expressions et fonctions étendent le JSON qui est disponible dans le modèle. Avec des expressions, vous créez des valeurs qui ne sont pas des valeurs littérales stricts. Les expressions sont placées entre crochets [et] et sont évaluées lorsque le modèle est déployé. Les expressions peuvent apparaître n’importe où dans une valeur de chaîne JSON et toujours retourner une autre valeur JSON. Si vous devez utiliser une chaîne littérale qui commence par un crochet [, vous devez utiliser deux crochets [[.

Généralement, vous utilisez des expressions avec des fonctions pour effectuer des opérations de configuration du déploiement. Comme dans JavaScript, les appels de fonction sont mis en forme en tant que **functionName(arg1,arg2,arg3)**. Vous référencez les propriétés en utilisant les opérateurs point et [index].

L’exemple suivant montre comment utiliser plusieurs fonctions lors de la construction de valeurs :
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Pour obtenir la liste complète des fonctions de modèle, reportez-vous à la section [fonctions de modèle de gestionnaire de ressources Azure](resource-group-template-functions.md). 


## <a name="parameters"></a>Paramètres

Dans la section Paramètres du modèle, vous spécifiez les valeurs que vous pouvez entrer lorsque vous déployez des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement en fournissant des valeurs qui sont conçues pour un environnement particulier (par exemple, développement, test et production). Vous n’êtes pas obligé de fournir des paramètres de votre modèle de, mais sans les paramètres de votre modèle serait toujours déployer les mêmes ressources, avec les mêmes noms, emplacements et propriétés.

Vous pouvez utiliser ces valeurs de paramètre dans le modèle pour définir des valeurs pour les ressources déployées. Uniquement les paramètres qui sont déclarés dans la section de paramètres peuvent être utilisés dans d’autres sections du modèle.

Vous définissez les paramètres avec la structure suivante :

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| Nom de l’élément   | Obligatoire | Description
| :------------: | :------: | :----------
| parameterName  |   Oui    | Nom du paramètre. Doit être un identificateur valide de JavaScript.
| type de           |   Oui    | Type de la valeur du paramètre. Consultez la liste ci-dessous contient les types autorisés.
| defaultValue   |   N°     | Valeur par défaut pour le paramètre, si aucune valeur n’est fournie pour le paramètre.
| allowedValues  |   N°     | Tableau de valeurs autorisées pour le paramètre afin de vous assurer que la valeur correcte est fournie.
| minValue       |   N°     | La valeur minimale pour les paramètres de type int, cette valeur est incluse.
| maxValue       |   N°     | La valeur maximale pour les paramètres de type int, cette valeur est incluse.
| minLength      |   N°     | La longueur minimale de la chaîne secureString et les paramètres de type de tableau, cette valeur est incluse.
| maxLength      |   N°     | La longueur maximale de chaîne secureString et les paramètres de type de tableau, cette valeur est incluse.
| Description    |   N°     | Description du paramètre, qui est affiché aux utilisateurs du modèle par le biais de l’interface du portail de modèle personnalisé.

Les valeurs et les types autorisés sont :

- **chaîne**
- **secureString**
- **int**
- **bool**
- **objet** 
- **secureObject**
- **tableau**

Pour spécifier un paramètre facultatif, fournir une valeur par défaut (il peut s’agir d’une chaîne vide). 

Si vous spécifiez un nom de paramètre qui correspond à l’un des paramètres de la commande pour déployer le modèle, vous êtes invité à fournir une valeur pour un paramètre avec le suffixe de **modèle**. Par exemple, si vous incluez un paramètre nommé **ResourceGroupName** dans le modèle qui est le même que le paramètre **ResourceGroupName** dans [Nouveau-AzureRmResourceGroupDeployment] [ deployment2cmdlet] l’applet de commande, vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion par ne pas nommer les paramètres avec le même nom que les paramètres utilisés pour les opérations de déploiement.

>[AZURE.NOTE] Tous les mots de passe, les clés et autres secrets doivent utiliser le type **secureString** . Impossible de lire les paramètres de modèle avec le type secureString après le déploiement de la ressource. 

L’exemple suivant montre comment définir les paramètres :

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Pour savoir comment entrer les valeurs des paramètres au cours du déploiement, consultez [déploiement d’une application avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md#parameter-file). 

## <a name="variables"></a>Variables

Dans la section variables, vous construisez des valeurs qui peuvent être utilisées tout au long de votre modèle. En règle générale, les variables sont basées sur les valeurs fournies dans les paramètres. Vous n’avez pas besoin de définir des variables, mais ils simplifient souvent votre modèle en réduisant des expressions complexes.

Vous définissez des variables avec la structure suivante :

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

L’exemple suivant montre comment définir une variable qui est construite à partir de deux valeurs de paramètre :

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

L’exemple suivant illustre une variable qui est un type complexe de JSON et des variables qui sont construits à partir d’autres variables :

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>Ressources

Dans la section ressources, vous définissez les ressources qui sont déployées ou mises à jour. Cette section se révèle parfois compliquée parce que vous devez comprendre les types que vous déployez pour fournir les valeurs de droite. 

Vous définissez des ressources avec la structure suivante :

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nom de l’élément             | Obligatoire | Description
| :----------------------: | :------: | :----------
| apiVersion               |   Oui    | Version de l’API REST à utiliser pour la création de la ressource.
| type de                     |   Oui    | Type de la ressource. Cette valeur est une combinaison de l’espace de noms du fournisseur de ressources et le type de ressource (par exemple, **Microsoft.Storage/storageAccounts**).
| nom                     |   Oui    | Nom de la ressource. Le nom doit respecter les restrictions de composant URI définies dans RFC3986. En outre, des services Azure qui exposent le nom de la ressource à l’extérieur des parties valider le nom pour vous assurer qu’il n’est pas une tentative d’usurpation d’identité d’une autre. Reportez-vous à la section [vérifier le nom de la ressource](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| emplacement                 |   Varie  | Prise en charge de géo-emplacement de la ressource fournie. Vous pouvez sélectionner les emplacements disponibles, mais en général il est logique de choisir celui qui est proche de vos utilisateurs. En règle générale, il est également judicieux de placer les ressources qui interagissent entre eux dans la même région. La plupart des types de ressources nécessitent un emplacement, mais certains types (par exemple, une attribution de rôle) ne nécessitent pas un emplacement.
| balises                     |   N°     | Balises qui sont associés à la ressource.
| commentaires                 |   N°     | Vos notes pour documenter les ressources dans votre modèle.
| dependsOn                |   N°     | Ressources dont dépend la ressource en cours de définition. Les dépendances entre les ressources sont évaluées et déployer des ressources dans leur ordre dépendant. Lorsque les ressources ne sont pas interdépendantes, ils sont déployés en parallèle. La valeur peut être une liste séparée par des virgules, d’une ressource de noms ou des identificateurs de ressource uniques.
| propriétés               |   N°     | Paramètres de configuration spécifiques à la ressource. Les valeurs pour les propriétés sont les mêmes que les valeurs que vous fournissez dans le corps de la demande pour l’opération de l’API REST (méthode PUT) créer la ressource. Pour des liens vers la documentation du schéma de ressources ou d’autres API, consultez [fournisseurs du Gestionnaire de ressources, des régions, des versions de l’API et des schémas](resource-manager-supported-services.md).
| copie                     |   N°     | Si plus d’une instance est nécessaire, le nombre de ressources à créer. Pour plus d’informations, consultez [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md). |
| ressources                |   N°     | Ressources enfants qui dépendent de la ressource en cours de définition. Vous pouvez fournir uniquement les types de ressources qui sont autorisés par le schéma de la ressource parent. Le nom qualifié complet du type de ressource enfant comprend le type de ressource parent, par exemple **Microsoft.Web/sites/extensions**. Dépendance sur la ressource parente n’est pas impliquée ; Vous devez définir explicitement cette dépendance. 

En sachant quelles valeurs spécifier pour **apiVersion**, **type**et **emplacement** n’est pas immédiatement évident. Heureusement, vous pouvez déterminer ces valeurs à l’aide de PowerShell d’Azure ou Azure CLI.

Pour obtenir tous les fournisseurs de ressources avec **PowerShell**, utilisez :

    Get-AzureRmResourceProvider -ListAvailable

À partir de la liste retournée, trouver les fournisseurs de ressources que vous intéressez. Pour obtenir les types de ressources pour un fournisseur de ressources (par exemple, stockage), utilisez :

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

Pour obtenir les versions de l’API pour un type de ressource (ces comptes de stockage), utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

Pour obtenir tous les fournisseurs de ressources avec la **CLI d’Azure**, utilisez :

    azure provider list

À partir de la liste retournée, trouver les fournisseurs de ressources que vous intéressez. Pour obtenir les types de ressources pour un fournisseur de ressources (par exemple, stockage), utilisez :

    azure provider show Microsoft.Storage

Pour obtenir les emplacements pris en charge et les versions de l’API, utilisez :

    azure provider show Microsoft.Storage --details --json

Pour plus d’informations sur les fournisseurs de ressources, consultez les [fournisseurs du Gestionnaire de ressources, des régions, des versions de l’API et des schémas](resource-manager-supported-services.md).

La section de ressources contient un tableau des ressources pour le déploiement. Au sein de chaque ressource, vous pouvez également définir un ensemble de ressources de l’enfant. Par conséquent, la section de ressources peut avoir une structure comme :

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


L’exemple suivant montre une ressource de **Microsoft.Web/serverfarms** et une ressource de **Microsoft.Web/sites** avec un enfant **Extensions de** ressource. Notez que le site est marqué comme dépendant de la batterie de serveurs dans la mesure où la batterie de serveurs doit exister avant de déployer le site. Notez également que la ressource **d’Extensions** est un enfant du site.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>Sorties

Dans la section des sorties, vous spécifiez des valeurs qui sont retournées à partir de déploiement. Par exemple, vous pourriez retourner l’URI utilisé pour accéder à une ressource déployée.

L’exemple suivant montre la structure d’une définition de sortie :

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nom de l’élément   | Obligatoire | Description
| :------------: | :------: | :----------
| nomsortie     |   Oui    | Nom de la valeur de sortie. Doit être un identificateur valide de JavaScript.
| type de           |   Oui    | Type de la valeur de sortie. Les valeurs de sortie prend en charge les mêmes types de paramètres d’entrée du modèle.
| valeur          |   Oui    | Expressions de langage de modèle qui sont évaluée et retournée en tant que valeur de sortie.


L’exemple suivant affiche une valeur qui est renvoyée dans la section des sorties.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Pour plus d’informations sur l’utilisation de la sortie, consultez [partage l’état dans les modèles du Gestionnaire de ressources Azure](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour afficher des modèles complets pour de nombreux types de solutions, consultez les [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/).
- Pour plus d’informations sur les fonctions que vous pouvez utiliser à partir d’un modèle, voir [Fonctions de modèle de gestionnaire de ressources Azure](resource-group-template-functions.md).
- Pour combiner plusieurs modèles au cours du déploiement, reportez-vous [à l’aide de modèles liés avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).
- Vous devrez peut-être utiliser des ressources qui existent au sein d’un groupe de ressources différent. Ce scénario est courant lorsque vous travaillez avec des comptes de stockage ou des réseaux virtuels qui sont partagées entre plusieurs groupes de ressources. Pour plus d’informations, reportez-vous à la [fonction de resourceId](resource-group-template-functions.md#resourceid).


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
