<properties
   pageTitle="Création de solutions de gestion de Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Solutions de gestion étendent les fonctionnalités de la Suite Gestion des opérations (OMS) en fournissant des scénarios de gestion de package que les clients peuvent ajouter à leur espace de travail de l’OMS.  Cet article fournit des détails sur la façon de créer des solutions de gestion à utiliser dans votre propre environnement ou mis à la disposition de vos clients."
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
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Création de solutions de gestion des opérations Management Suite (OMS) (aperçu)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement dans l’aperçu. Les schémas décrits ci-dessous sont sujettes à modification.  

Solutions de gestion étendent les fonctionnalités de la Suite Gestion des opérations (OMS) en fournissant des scénarios de gestion de package que les clients peuvent ajouter à leur espace de travail de l’OMS.  Cet article fournit des détails sur la création de vos propres solutions de gestion que vous pouvez utiliser dans votre propre environnement ou le rendre disponible aux clients par le biais de la Communauté.

## <a name="planning-your-management-solution"></a>Planification de votre solution de gestion
Solutions de gestion OMS incluent plusieurs ressources de prise en charge d’un scénario de gestion particulier.  Lors de la planification de votre solution, vous devez vous concentrer sur le scénario que vous essayez d’atteindre et de toutes les ressources requises pour prendre en charge.  Chaque solution autonome et définit chaque ressource dont elle a besoin, même si une ou plusieurs ressources sont également définies par d’autres solutions.  Lors de l’installation d’une solution de gestion, chaque ressource est créée, à moins qu’il existe déjà, et vous pouvez définir ce qui se passe aux ressources lorsqu’une solution est supprimée.  

Par exemple, une solution de gestion peut inclure une [procédure opérationnelle d’Azure Automation](../automation/automation-intro.md) qui collecte les données dans le référentiel de journal Analytique à l’aide d’une [planification](../automation/automation-schedules.md) et une [vue](../log-analytics/log-analytics-view-designer.md) qui fournit différentes visualisations de données collectées.  La même planification peut être utilisée par une autre solution.  En tant qu’auteur de la solution de gestion, vous souhaitez définir les trois ressources mais spécifiez que la procédure opérationnelle et la vue doivent être supprimés automatiquement lors de la suppression de la solution.    Vous pouvez également définir la planification mais spécifier qu’elle doit rester en place si la solution ont été supprimée dans le cas où il a été en cours d’utilisation par l’autre solution.

## <a name="management-solution-files"></a>Fichiers de solution de gestion
Solutions de gestion sont implémentées en tant que [modèles de gestion des ressources](../resource-manager-template-walkthrough.md).  La tâche principale à apprendre à créer des solutions de gestion d’apprentissage est [l’auteur d’un modèle](../resource-group-authoring-templates.md).  Cet article fournit des détails uniques des modèles utilisés pour les solutions et comment définir des ressources de la solution par défaut.

La structure d’un fichier de solution de gestion de base est identique à un [Modèle de gestionnaire de ressources](resource-group-authoring-templates.md#template-format) qui est comme suit.  Chacune des sections suivantes décrit les éléments de niveau supérieur et et leur contenu dans une solution.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Paramètres

[Les paramètres](../resource-group-authoring-templates.md#parameters) sont des valeurs dont vous avez besoin de l’utilisateur lors de l’installation de la solution de gestion.  Il existe des paramètres standard ayant à toutes les solutions, et vous pouvez ajouter des paramètres supplémentaires requis pour votre solution.  Comment les utilisateurs fournit les valeurs de paramètre lors de l’installation de votre solution dépend du paramètre particulier et comment la solution est en cours d’installation.


Lorsqu’un utilisateur installe votre solution de gestion à l’aide [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions) ou [modèles d’Azure QuickStart](operations-management-suite-solutions.md#finding-and-installing-solutions) qu'ils sont invités à sélectionner un [espace de travail de l’OMS et le compte de l’Automation](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Ceux-ci sont utilisés pour remplir les valeurs de chacun des paramètres standard.  L’utilisateur n’est pas invité à fournir directement des valeurs pour les paramètres standard, mais ils sont invités à fournir des valeurs pour les paramètres supplémentaires.

Lorsque l’utilisateur installe votre solution [une autre méthode](operations-management-suite-solutions.md#finding-and-installing-solutions), ils doivent fournir une valeur pour tous les paramètres standard et tous les autres paramètres.

Vous trouverez ci-dessous un exemple de paramètre.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Le tableau suivant décrit les attributs d’un paramètre.

| Attribut | Description |
|:--|:--|
| type de        | Type de données pour le paramètre. Le contrôle d’entrée affiché pour l’utilisateur dépend du type de données.<br><br>bool - la zone de liste déroulante<br>chaîne - zone de texte<br>int - zone de texte<br>SecureString - champ de mot de passe<br> |
| catégorie    | Category facultatif pour le paramètre.  Regroupement des paramètres dans la même catégorie. |
| contrôle     | Fonctionnalités supplémentaires pour les paramètres de la chaîne.<br><br>DateTime : contrôle Datetime est affichée.<br>GUID - valeur Guid est généré automatiquement et le paramètre n’est pas affiché. |
| Description | Description facultative pour le paramètre.  Affiche dans une bulle d’informations en regard du paramètre. |


### <a name="standard-parameters"></a>Paramètres standard
Le tableau suivant répertorie les paramètres standard pour toutes les solutions de gestion.  Ces valeurs sont remplies pour l’utilisateur au lieu de solliciter leur lorsque la solution est installée à partir du Azure Marketplace ou les modèles de démarrage rapide.  L’utilisateur doit fournir des valeurs si la solution est installée avec une autre méthode.

>[AZURE.NOTE]L’interface utilisateur dans les modèles de Quickstart Azure Marketplace attend les noms de paramètres dans le tableau.  Si vous utilisez des noms de paramètres différents puis l’utilisateur sera invité pour eux et qu’ils ne sont pas automatiquement renseignées.


| Paramètre | Type de | Description |
|:--|:--|:--|
| nom de compte       | chaîne |  Nom de compte Azure Automation. |
| pricingTier       | chaîne | Niveau de tarification d’espace de travail de journal Analytique et compte d’Automation d’Azure. |
| regionId          | chaîne | Région du compte Azure Automation. |
| solutionName      | chaîne | Nom de la solution. |
| workspaceName     | chaîne | Nom de l’espace journal Analytique. |
| workspaceRegionId | chaîne | Région de l’espace de travail du journal Analytique. |





### <a name="sample"></a>Exemple
Voici une entité de paramètre d’échantillon pour une solution.  Cela inclut tous les paramètres standard et deux paramètres supplémentaires dans la même catégorie.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Vous faites référence aux valeurs de paramètre dans d’autres éléments de la solution avec la syntaxe des **Paramètres ('nom de paramètre')**.  Par exemple, pour accéder au nom d’espace de travail, vous utilisez **parameters('workspaceName')** 

## <a name="variables"></a>Variables

L’élément de **Variables** inclut les valeurs que vous souhaitez utiliser dans le reste de la solution de gestion.  Ces valeurs ne sont pas exposées à l’utilisateur de l’installation de la solution.  Ils sont destinés à fournir un emplacement unique où ils peuvent gérer des valeurs qui peuvent être utilisés plusieurs fois dans l’ensemble de la solution à l’auteur. Vous devez placer toutes les valeurs spécifiques à votre solution dans les variables par opposition au codage en dur les dans l’élément de **ressources** .  Cela rend le code plus lisible et vous permet de modifier facilement ces valeurs dans les versions ultérieures.

Voici un exemple d’un élément de **variables** avec des paramètres standard utilisés dans les solutions.

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Vous faites référence à des valeurs de variable par l’intermédiaire de la solution avec la syntaxe **variables ('nom de la variable')**.  Par exemple, pour accéder à la variable SolutionName, vous utiliseriez **variables('solutionName')** 


## <a name="resources"></a>Ressources

L’élément de **ressources** définit les différentes ressources incluses dans votre solution de gestion.  Il s’agit de la partie plus vastes et les plus complexe du modèle.  Les ressources sont définies avec la structure suivante.  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dépendances
Les éléments **dependsOn** spécifie une [dépendance](../resource-group-define-dependencies.md) sur une autre ressource.  Lorsque la solution est installée, une ressource n’est pas créée tant que toutes ses dépendances ont été créés.  Par exemple, votre solution peut [Démarrer une procédure opérationnelle](operations-management-suite-solutions-resources-automation.md#runbooks) lorsqu’il est installé à l’aide d’une [ressource de travail](operations-management-suite-solutions-resources-automation.md#automation-jobs).  La ressource de travail est dépendante de la ressource de procédure opérationnelle pour vous assurer que la procédure opérationnelle est créé avant la création de la tâche.

### <a name="oms-workspace-and-automation-account"></a>Espace de travail de l’OMS et le compte de l’Automation
Solutions de gestion nécessitent un [espace de travail de l’OMS](../log-analytics/log-analytics-manage-access.md) pour contenir des vues et un [compte d’Automation](../automation/automation-security-overview.md#automation-account-overview) pour contenir des procédures opérationnelles et les ressources associées.  Il doivent être disponibles avant que les ressources de la solution sont créés et ne doivent pas être définies dans la solution elle-même.  L’utilisateur doit [spécifier un espace de travail et un compte](operations-management-suite-solutions.md#oms-workspace-and-automation-account) lorsqu’ils déploient votre solution, mais comme l’auteur, vous devez considérer les points suivants.


## <a name="solution-resource"></a>Ressources de la solution
Chaque solution nécessite une entrée de ressource de l’élément de **ressources** qui définit la solution elle-même.  Cela aura un type de **Microsoft.OperationsManagement/solutions**.  Voici un exemple d’une ressource de la solution.  Les différents éléments sont décrits dans les sections ci-dessous.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nom de la solution
Le nom de la solution doit être unique dans votre abonnement Azure. La valeur recommandée à utiliser est la suivante.  Il utilise une variable appelée **SolutionName** pour le nom de base et le paramètre **workspaceName** pour s’assurer que le nom est unique.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Cela se résoudra en un nom semblable à la suivante.

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>Dépendances
La ressource de la solution doit avoir une [dépendance](../resource-group-define-dependencies.md) sur toutes les autres ressources de la solution dans la mesure où ils doivent exister avant la création de la solution.  Pour cela, en ajoutant une entrée pour chaque ressource dans l’élément **dependsOn** .


### <a name="properties"></a>Propriétés
La ressource de la solution possède les propriétés dans le tableau suivant.  Cela inclut les ressources référencées par et contenu dans la solution qui définit la manière dont la ressource est gérée après l’installation de la solution.  Chaque ressource de la solution doit être répertorié dans la propriété **containedResources** ou le **referencedResources** .

| Propriété | Description |
|:--|:--|
| workspaceResourceId | ID de l’espace de travail OMS sous la forme * <Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nom de l’espace de travail de\>*. |
| referencedResources   | Liste des ressources de la solution ne doit pas être supprimé lorsque la solution est supprimée. |
| containedResources   | Liste des ressources de la solution qui doit être supprimé lorsque la solution est supprimée. |

L’exemple ci-dessus est une solution avec une procédure opérationnelle, une planification et un affichage.  La planification et la procédure opérationnelle sont *référencés* dans l’élément **Propriétés** afin qu’elles ne sont pas supprimés lors de la suppression de la solution.  L’affichage est *contenu* donc elle est supprimée lorsque la solution est supprimée.


### <a name="plan"></a>Plan de
L’entité **plan** de la ressource de la solution possède les propriétés dans le tableau suivant. 

| Propriété | Description |
|:--|:--|
| nom          | Nom de la solution. |
| Version       | Version de la solution, tel que déterminé par l’auteur. |
| produit       | Chaîne unique pour identifier la solution. |
| Publisher     | Éditeur de la solution. |


## <a name="other-resources"></a>Autres ressources
Vous pouvez obtenir les détails et des exemples de ressources qui sont communs aux solutions de gestion dans les articles suivants.

- [Vues et tableaux de bord](operations-management-suite-solutions-resources-views.md)
- [Ressources d’Automation](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Test d’une solution de gestion
Avant de déployer votre solution de gestion, il est recommandé de tester à l’aide de [Test-AzureRmResourceGroupDeployment](../resource-group-template-deploy.md#deploy-with-powershell).  Ceci validera votre fichier solution et à vous aider à qu'identifier les problèmes avant d’essayer de le déployer.



## <a name="next-steps"></a>Étapes suivantes

- Découvrez les détails des [modèles de création d’un gestionnaire de ressources Azure](../resource-group-authoring-templates.md).
- Rechercher des [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates) des exemples des différents modèles de gestionnaire de ressources.
- Afficher les détails pour [l’Ajout d’une solution de gestion des vues](operations-management-suite-solutions-resources-views.md).
- Afficher les détails pour [Ajouter des ressources d’Automation pour une solution de gestion](operations-management-suite-solutions-resources-automation.md).
 