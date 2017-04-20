<properties
   pageTitle="Affichages dans les solutions de gestion d’Operations Management Suite (OMS) | Microsoft Azure"
   description="Solutions de gestion de Suite de gestion des opérations (OMS) inclura généralement une ou plusieurs vues pour visualiser des données.  Cet article décrit comment exporter une vue créée par le Concepteur de vue et de l’inclure dans une solution de gestion. "
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Affichages dans les solutions de gestion d’Operations Management Suite (OMS) (aperçu)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement dans l’aperçu. Les schémas décrits ci-dessous sont sujettes à modification.    

[Solutions de gestion de Suite de gestion des opérations (OMS)](operations-management-suite-solutions.md) inclura généralement une ou plusieurs vues pour visualiser des données.  Cet article décrit comment exporter une vue créée par le [Concepteur de vues](../log-analytics/log-analytics-view-designer.md) et l’inclure dans une solution de gestion.  

>[AZURE.NOTE]Les exemples de cet article utilisent les paramètres et les variables requis ou communes à des solutions de gestion et décrites dans la [Création de solutions de gestion de Suite de gestion des opérations (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous êtes familiarisé avec la manière de [créer une solution de gestion](operations-management-suite-solutions-creating.md) et de la structure d’un fichier de solution.


## <a name="overview"></a>Vue d’ensemble

Pour inclure un affichage d’une solution de gestion, vous créez une **ressource** pour lui dans le [fichier de solution](operations-management-suite-solutions-creating.md).  Le JSON qui décrit la configuration détaillée de la vue est généralement complexe cependant et pas quelque chose que l’auteur d’un type de solution serait en mesure de créer manuellement.  La méthode la plus courante consiste à les créer la vue de l’utilisation du [Concepteur de vue](../log-analytics/log-analytics-view-designer.md), l’exporter et puis ajouter sa configuration détaillée à la solution. 

Voici les étapes de base pour ajouter une vue à une solution.  Chaque étape est décrite en détail dans les sections ci-dessous.

1. Exporter la vue vers un fichier.
2. Créez la ressource de l’affichage de la solution.
3. Ajoutez les détails de la vue.

## <a name="export-the-view-to-a-file"></a>Exporter la vue vers un fichier
Suivez les instructions dans le [Concepteur de vue Analytique de journal](../log-analytics/log-analytics-view-designer.md) à exporter un affichage dans un fichier.  Le fichier exporté est au format JSON avec les mêmes [éléments que le fichier de solution](operations-management-suite-solutions-creating.md#management-solution-files).  

L’élément de **ressources** du fichier affichage aura une ressource avec un type de **Microsoft.OperationalInsights/workspaces** qui représente l’espace de travail de l’OMS.  Cet élément a un sous-élément avec un type de **vues** qui représente la vue et qui contient sa configuration détaillée.  Copiez les détails de cet élément et puis la copier dans votre solution.


## <a name="create-the-view-resource-in-the-solution"></a>Créez la ressource de l’affichage de la solution
Ajoutez la ressource suivante de la vue à l’élément de **ressources** de votre fichier de solution.  Cet exemple utilise les variables qui sont décrites ci-dessous, vous devez également ajouter.  Notez que les propriétés de **tableau de bord** et **OverviewTile** sont des espaces réservés qui vous remplacera avec les propriétés correspondantes à partir du fichier de la vue exportée.
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

Ajoutez les variables suivantes pour l’élément de [variables](operations-management-suite-solutions-creating.md#variables) du fichier solution et remplacez les valeurs à celles de votre solution.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Notez que vous pouvez copier la ressource de la totalité de la vue à partir de votre fichier de vue exportée, mais vous devrez apporter les modifications suivantes pour qu’il fonctionne dans votre solution.  

- Le **type** de la ressource de l’affichage doit être modifié à partir de **vues** à **Microsoft.OperationalInsights/workspaces**.
- La propriété **name** de la ressource de l’affichage doit être modifié pour inclure le nom de l’espace de travail.
- La dépendance par rapport à l’espace de travail doit être supprimée car la ressource de l’espace de travail n’est pas définie dans la solution.
- Propriété **DisplayName** doit être ajouté à la vue.  Tous les **Id**, **nom**et **DisplayName** doivent correspondre.
- Les noms de paramètre doivent être modifiés pour correspondre à l’ensemble de paramètres.
- Les variables doivent être définies dans la solution et utilisées dans les propriétés correspondantes.

## <a name="add-the-view-details"></a>Ajouter les détails de la vue
Afficher la ressource dans le fichier de vue exportée contiendra les deux éléments dans l’élément de **Propriétés** nommé **OverviewTile** et **tableau de bord** qui contiennent la configuration détaillée de la vue.  Copiez ces deux éléments et leur contenu dans l’élément de **Propriétés** de la ressource de l’affichage dans votre fichier de solution. 

## <a name="example"></a>Exemple
Par exemple, l’exemple suivant montre un fichier de solution simple avec une vue.  Bouton de sélection (...) s’affichent pour le contenu du **tableau de bord** et des **OverviewTile** pour des raisons d’espace.


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>Étapes suivantes

- Découvrez les détails complets de la création de [solutions de gestion](operations-management-suite-solutions-creating.md).
- Inclure des [procédures opérationnelles d’Automation dans votre solution de gestion](operations-management-suite-solutions-resources-automation.md).