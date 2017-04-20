<properties
   pageTitle="Ressources d’automatisation dans les solutions de l’OMS | Microsoft Azure"
   description="Solutions de OMS implique généralement des procédures opérationnelles dans Automation Azure pour automatiser le processus de collecte et de traitement de données d’analyse.  Cet article explique comment inclure des procédures opérationnelles et leurs ressources associées dans une solution."
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
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>Ressources d’automatisation dans les solutions de l’OMS (aperçu)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement dans l’aperçu. Les schémas décrits ci-dessous sont sujettes à modification.   

[Solutions de gestion OMS](operations-management-suite-solutions.md) implique généralement des procédures opérationnelles dans Automation Azure pour automatiser le processus de collecte et de traitement de données d’analyse.  En plus des procédures opérationnelles, les comptes Automation inclut des actifs tels que des variables et des tableaux qui prennent en charge les procédures opérationnelles utilisées dans la solution.  Cet article explique comment inclure des procédures opérationnelles et leurs ressources associées dans une solution.

>[AZURE.NOTE]Les exemples de cet article utilisent les paramètres et les variables requis ou communes à des solutions de gestion et décrites dans la [Création de solutions de gestion de Suite de gestion des opérations (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous êtes familiarisé avec la manière de [créer une solution de gestion](operations-management-suite-solutions-creating.md) et de la structure d’un fichier de solution.

## <a name="samples"></a>Exemples
Vous pouvez obtenir des exemples de modèles de gestionnaire de ressources pour les ressources de l’Automation à partir des [modèles de démarrage rapide dans GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Compte d’Automation
Toutes les ressources dans Azure Automation sont contenus dans un [compte d’Automation](../automation/automation-security-overview.md#automation-account-overview).  Comme indiqué dans [l’espace de travail de l’OMS et le compte de l’automatisation](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) le compte Automation n’est pas inclus dans la solution de gestion mais doit exister avant l’installation de la solution.  Si elle n’est pas disponible, l’installation de la solution échoue.

Le nom de son compte d’Automation est le nom de chaque ressource d’Automation.  Pour ce faire de la solution avec le paramètre de **nom de compte** comme dans l’exemple suivant d’une procédure opérationnelle de ressource.
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Procédures opérationnelles
Ressources de [procédure opérationnelle d’Azure Automation](../automation/automation-runbook-types.md) ont un type de **Microsoft.Automation/automationAccounts/runbooks** et ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| runbookType | Spécifie les types de la procédure opérationnelle. <br><br> Script - script PowerShell <br>PowerShell - PowerShell de flux de travail <br> GraphPowerShell - procédure opérationnelle de script PowerShell graphique <br> GraphPowerShellWorkflow - procédure opérationnelle de PowerShell graphique du flux de travail   |
| logProgress | Spécifie si les [enregistrements de progression](../automation/automation-runbook-output-and-messages.md) doit être généré pour la procédure opérationnelle. |
| logVerbose  | Spécifie si les [enregistrements détaillés](../automation/automation-runbook-output-and-messages.md) doivent être générés pour la procédure opérationnelle. |
| Description | Description facultative de la procédure opérationnelle. |
| publishContentLink | Spécifie le contenu de la procédure opérationnelle. <br><br>URI - Uri pour le contenu de la procédure opérationnelle.  Il s’agit d’un fichier .ps1 pour PowerShell et le Script de procédures opérationnelles et un fichier de procédure opérationnelle de graphique exporté pour une procédure opérationnelle du graphique.  <br> version - Version de la procédure opérationnelle pour votre propre suivi. |

Un exemple d’une ressource de procédure opérationnelle est inférieur.  Dans ce cas, il récupère la procédure opérationnelle à partir de la [Galerie de PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Démarrage d’une procédure opérationnelle
Il existe deux méthodes pour démarrer une procédure opérationnelle dans une solution de gestion.

- Pour démarrer la procédure opérationnelle lorsque la solution est installée, créez une [ressource de travail](#automation-jobs) , comme décrit ci-dessous.
- Pour démarrer la procédure opérationnelle sur une planification, créer une [ressource Planning](#schedules) , comme décrit ci-dessous. 


## <a name="automation-jobs"></a>Tâches d’automatisation
Avant de lancer une procédure opérationnelle lors de l’installation de la solution de gestion, vous créez une ressource de **travail** .  Les ressources de travail de type **Microsoft.Automation/automationAccounts/jobs** et ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| procédure opérationnelle    | Seul le **nom de** l’entité avec le nom de la procédure opérationnelle.  |
| paramètres | Entité pour chaque paramètre requis par la procédure opérationnelle. |


Le projet comprend le nom de procédure opérationnelle et des valeurs de paramètre à transmettre à la procédure opérationnelle.  La tâche doit [dépendre](operations-management-suite-solutions-creating.md#resources) la procédure opérationnelle qui il démarre depuis la procédure opérationnelle doit être créé avant la tâche.  Vous également créez des dépendances sur d’autres tâches de procédures opérationnelles qui doivent être exécutées avant celle en cours.

Le nom d’une ressource de travail doit contenir un GUID qui est généralement attribué par un paramètre.  Vous pouvez en savoir plus sur les paramètres GUID dans la [Création de solutions dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Voici un exemple d’une ressource de travail qui démarre une procédure opérationnelle lors de l’installation de la solution de gestion.  Un autres procédures opérationnelles doivent être exécutées avant cette une démarre, elle a des dépendances sur les tâches de cette procédure opérationnelle.  Les détails de la tâche sont fournis via paramètres et variables plutôt que d’être spécifié directement.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificats
[Les certificats d’Azure Automation](../automation/automation-certificates.md) ont un type de **Microsoft.Automation/automationAccounts/certificates** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| base64Value   | Valeur de base 64 pour le certificat. |
| empreinte numérique    | Empreinte numérique du certificat. |

Un exemple d’une ressource de certificat est inférieur.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Informations d’identification
[Informations d’identification de l’automatisation d’Azure](../automation/automation-credentials.md) ont un type de **Microsoft.Automation/automationAccounts/credentials** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| nom d’utilisateur   | Nom d’utilisateur pour les informations d’identification. |
| mot de passe   | Mot de passe pour les informations d’identification. |

Il est un exemple d’une ressource d’informations d’identification ci-dessous.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Planifications
[Planifications d’Azure Automation](../automation/automation-schedules.md) ont un type de **Microsoft.Automation/automationAccounts/schedules** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Description | Description facultative de la planification. |
| heure de début   | Spécifie l’heure de début d’un planning sous la forme d’un objet DateTime. Une chaîne peut être fournie si elle peut être convertie en un DateTime valide. |
| isEnabled   | Indique si la planification est activée. |
| intervalle    | Le type d’intervalle pour la planification.<br><br>jour<br>heure |
| fréquence   | Fréquence à laquelle la planification doit se déclencher dans le nombre de jours ou d’heures. |

Un exemple d’une ressource planning est inférieur.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variables
[Les variables d’Azure Automation](../automation/automation-variables.md) ont un type de **Microsoft.Automation/automationAccounts/variables** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Description | Description facultative de la variable. |
| isEncrypted | Spécifie si la variable doit être chiffrée. |
| type de        | Type de données de la variable. |
| valeur       | Valeur de la variable. |

Un exemple d’une ressource de variable est inférieure.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Modules
Votre solution de gestion n’a pas besoin de définir de [modules globales](../automation/automation-integration-modules.md) utilisé par les procédures opérationnelles, car ils seront toujours disponibles.  Vous n’avez pas besoin d’inclure une ressource d’un autre module utilisé par les procédures opérationnelles, et la procédure opérationnelle doit dépendre de la ressource du module pour vous assurer qu’il est créé avant la procédure opérationnelle.

[Modules d’intégration](../automation/automation-integration-modules.md) de type **Microsoft.Automation/automationAccounts/modules** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| contentLink | Spécifie le contenu du module. <br><br>URI - Uri pour le contenu de la procédure opérationnelle.  Il s’agit d’un fichier .ps1 pour PowerShell et le Script de procédures opérationnelles et un fichier de procédure opérationnelle de graphique exporté pour une procédure opérationnelle du graphique.  <br> version - Version de la procédure opérationnelle pour votre propre suivi. |

Un exemple d’une ressource du module est inférieur.

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Mise à jour de modules
Si vous mettez à jour une solution de gestion qui inclut une procédure opérationnelle par une planification et que la nouvelle version de votre solution a un nouveau module utilisé par cette procédure opérationnelle, la procédure opérationnelle peut ensuite utiliser l’ancienne version du module.  Vous devez inclure les procédures opérationnelles suivantes dans votre solution et créer un travail pour les exécuter avant toutes les autres procédures opérationnelles.  Cela permet de garantir que tous les modules sont mis à jour avant les procédures opérationnelles sont chargés.

- [ModulesinAutomationToLatestVersion-mise à jour](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garantit que tous les modules utilisés par les procédures opérationnelles de votre solution sont la version la plus récente.  
- [ReRegisterAutomationSchedule MS-gestion](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) sera réenregistrer toutes les ressources de planification afin de garantir que les procédures opérationnelles liés à leur utilisation les derniers modules.


Voici un exemple des éléments requis d’une solution pour prendre en charge de la mise à jour du module.
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Étapes suivantes

- [Ajouter une vue à votre solution](operations-management-suite-solutions-resources-views.md) pour visualiser des données collectées.