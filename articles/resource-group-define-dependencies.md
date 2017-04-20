<properties
   pageTitle="Dépendances dans le Gestionnaire de ressources de modèles | Microsoft Azure"
   description="Décrit comment définir une ressource comme dépendant d’une autre ressource au cours du déploiement afin de déployer des ressources dans l’ordre correct."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Définition des dépendances dans les modèles du Gestionnaire de ressources Azure

Pour une ressource donnée, il peut exister des autres ressources qui doivent exister avant le déploiement de la ressource. Par exemple, un serveur SQL doit exister avant d’essayer de déployer une base de données SQL. Vous définissez cette relation en marquant d’une ressource en tant que dépendant de l’autre ressource. En général, vous définissez une dépendance avec l’élément **dependsOn** , mais vous pouvez également le définir via la fonction de **référence** . 

Le Gestionnaire de ressources évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Lorsque les ressources ne sont pas interdépendantes, Gestionnaire de ressources les déploie en parallèle.

## <a name="dependson"></a>dependsOn

Dans votre modèle, l’élément dependsOn vous permet de définir une ressource en tant que dépendant sur une ou plusieurs ressources. Sa valeur peut être une liste séparée par des virgules de noms de ressources. 

L’exemple suivant montre un ensemble d’échelle de machine virtuelle qui dépend d’un équilibreur de charge, le réseau virtuel et une boucle qui crée plusieurs comptes de stockage. Ces autres ressources ne sont pas affichés dans l’exemple suivant, mais ils doivent exister ailleurs dans le modèle.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Pour définir une dépendance entre une ressource et les ressources qui sont créés dans une boucle de copie, définissez l’élément dependsOn au nom de la boucle. Pour obtenir un exemple, reportez-vous à la section [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md).

Pendant que vous pouvez être incliné dependsOn permet de mapper des relations entre vos ressources, il est important de comprendre pourquoi votre travail, car elle peut avoir un impact sur les performances de votre déploiement. Par exemple, pour documenter la manière dont les ressources sont reliés, dependsOn n’est pas la bonne approche. Vous ne peut pas interroger les ressources qui ont été définis dans l’élément dependsOn après le déploiement. À l’aide de dependsOn, vous avoir un impact sur le temps de déploiement, car le Gestionnaire de ressources ne déploie pas en parallèles deux ressources qui ont une dépendance. Pour documenter les relations entre les ressources, à la place utiliser la [liaison de la ressource](resource-group-link-resources.md).

## <a name="child-resources"></a>Ressources de l’enfant

La propriété de ressources vous permet de vous permet de spécifier les ressources enfants associés à la ressource en cours de définition. Ressources de l’enfant peuvent uniquement être défini avec une profondeur de cinq niveaux. Il est important de noter qu’une dépendance implicite n’est pas créée entre une ressources enfant et parent. Si vous avez besoin de la ressource enfant pour être déployé après la ressource parente, vous devez indiquer explicitement cette dépendance avec la propriété dependsOn. 

Chaque ressource parent accepte uniquement certains types de ressources en tant que ressources de l’enfant. Les types de ressource acceptés sont spécifiés dans le [schéma de modèle](https://github.com/Azure/azure-resource-manager-schemas) de la ressource parent. Le nom du type de ressource enfant inclut le nom du type de ressource parent, tel que **Microsoft.Web/sites/config** et **Microsoft.Web/sites/extensions** sont les deux ressources enfants de la **Microsoft.Web/sites**.

L’exemple suivant montre un serveur SQL et la base de données SQL. Notez qu’une dépendance explicite est définie entre les de la base de données SQL server SQL, même si la base de données est un enfant du serveur.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>fonction de référence

La [fonction de référence](resource-group-template-functions.md#reference) permet une expression à sa valeur dérivent d’autre nom JSON et les paires de valeur ou les ressources d’exécution. Expressions de référence déclarent implicitement qu’une ressource dépend d’un autre. 

    reference('resourceName').propertyPath

Vous pouvez utiliser cet élément ou l’élément dependsOn pour spécifier des dépendances, mais vous n’avez pas besoin d’utiliser les deux pour la même ressource dépendante. Si possible, utilisez une référence implicite à éviter l’ajout d’une dépendance inutile par inadvertance.

Pour plus d’informations, consultez la [fonction de référence](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles du Gestionnaire de ressources Azure, consultez [Création de modèles](resource-group-authoring-templates.md). 
- Pour obtenir une liste des fonctions disponibles dans un modèle, voir [fonctions de modèle](resource-group-template-functions.md).

