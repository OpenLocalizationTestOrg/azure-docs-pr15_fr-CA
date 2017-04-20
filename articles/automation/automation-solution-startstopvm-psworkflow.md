<properties 
    pageTitle="Démarrage et arrêt des machines virtuelles avec Azure Automation - PowerShell Workflow | Microsoft Azure"
    description="Version graphique de scénario Automatisation d’Azure, y compris les procédures opérationnelles pour le démarrage et l’arrêt des machines virtuelles classiques."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Scénario d’automatisation Azure - démarrage et arrêt des machines virtuelles

Ce scénario d’automatisation de Azure inclut des procédures opérationnelles pour le démarrage et l’arrêt des machines virtuelles classiques.  Vous pouvez utiliser ce scénario pour une des opérations suivantes :  

- Utilisez les procédures opérationnelles sans modification dans votre propre environnement. 
- Modifier les procédures opérationnelles pour exécuter des fonctionnalités personnalisées.  
- Appelez les procédures opérationnelles à partir d’une autre procédure d’opérationnelle dans le cadre d’une solution globale. 
- Utilisez les procédures opérationnelles sous forme de didacticiels pour apprendre les concepts de création de procédure opérationnelle. 

> [AZURE.SELECTOR]
- [Graphique](automation-solution-startstopvm-graphical.md)
- [Flux de travail de PowerShell](automation-solution-startstopvm-psworkflow.md)

Il s’agit de la version de procédure opérationnelle PowerShell Workflow de ce scénario. Il est également disponible à l’aide de [procédures opérationnelles de graphique](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Le scénario de mise en route

Ce scénario se compose de deux procédures opérationnelles PowerShell de flux de travail que vous pouvez télécharger à partir des liens suivants.  Consultez la [version graphique](automation-solution-startstopvm-graphical.md) de ce scénario, les liens vers les procédures opérationnelles de graphique.

| Procédure opérationnelle | Lien | Type de | Description |
|:---|:---|:---|:---|
| Démarrer-AzureVMs | [Démarrer Azure VM classique](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Flux de travail de PowerShell | Démarre toutes les machines virtuelles classiques dans une subscriptionor Azure tous les ordinateurs virtuels avec un nom de service particulier. |
| Stop-AzureVMs | [Arrêter Azure VM classique](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Flux de travail de PowerShell | Arrête tous les ordinateurs virtuels dans un compte automation ou tous les ordinateurs virtuels avec un nom de service particulier.  |


## <a name="installing-and-configuring-the-scenario"></a>Installation et configuration du scénario

### <a name="1-install-the-runbooks"></a>1. Installez les procédures opérationnelles

Après avoir téléchargé les procédures opérationnelles, vous pouvez les importer à l’aide de la procédure dans [l’importation d’une procédure opérationnelle](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2-review-the-description-and-requirements"></a>2. Vérifiez la description et les exigences
Les procédures opérationnelles incluent le texte d’aide commentées qui inclut une description et les ressources requises.  Vous pouvez également obtenir les mêmes informations à partir de cet article. 

### <a name="3-configure-assets"></a>3. Configuration d’immobilisations
Les procédures opérationnelles nécessitent les actifs suivants que vous devez créer et remplir avec les valeurs appropriées.

| Type d’immobilisation | Nom de la ressource | Description |
|:---|:---|:---|:---|
| Informations d’identification | AzureCredential | Contient les informations d’identification d’un compte ayant les autorisations pour démarrer et arrêter des ordinateurs virtuels dans l’abonnement Azure.  Vous pouvez également spécifier un autre actif d’informations d’identification dans le paramètre **d’informations d’identification** de l’activité de **Add-AzureAccount** . |
| Variable | AzureSubscriptionId | Contient l’ID d’abonnement de votre abonnement Azure. |

## <a name="using-the-scenario"></a>À l’aide du scénario

### <a name="parameters"></a>Paramètres

Les procédures opérationnelles chaque possèdent les paramètres suivants.  Vous devez fournir des valeurs pour les paramètres obligatoires et pouvez éventuellement fournir des valeurs pour d’autres paramètres en fonction de vos besoins.

| Paramètre | Type de | Obligatoire | Description |
|:---|:---|:---|:---|
| ServiceName | chaîne | N° | Si une valeur est fournie, toutes les machines virtuelles portant le même nom de service sont démarrés ou arrêtés.  Si aucune valeur n’est fournie, tous les ordinateurs virtuels classiques dans l’abonnement Azure sont démarrés ou arrêtés. |
| AzureSubscriptionIdAssetName | chaîne | N° | Contient le nom de [variable actif](#installing-and-configuring-the-scenario) qui contient l’ID d’abonnement de votre abonnement Azure.  Si vous ne spécifiez aucune valeur, c’est *AzureSubscriptionId* qui est utilisée.  |
| AzureCredentialAssetName | chaîne | N° | Contient le nom de [ressource d’informations d’identification](#installing-and-configuring-the-scenario) qui contient les informations d’identification pour la procédure opérationnelle à utiliser.  Si vous ne spécifiez aucune valeur, c’est *AzureCredential* qui est utilisée.  |

### <a name="starting-the-runbooks"></a>Démarrer les procédures opérationnelles

Vous pouvez utiliser une des méthodes de [démarrage d’une procédure opérationnelle dans Azure Automation](automation-starting-a-runbook.md) Démarrer, des procédures opérationnelles dans ce scénario.

Les exemples de commandes suivant utilise Windows PowerShell pour exécuter **StartAzureVMs** pour démarrer tous les ordinateurs virtuels avec le nom du service *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Sortie

Les procédures opérationnelles seront [un message de sortie](automation-runbook-output-and-messages.md) pour chaque ordinateur virtuel en indiquant ou non de l’instruction de départ ou d’arrêt a été envoyée avec succès.  Vous pouvez rechercher une chaîne spécifique dans la sortie pour déterminer le résultat de chaque procédure opérationnelle.  Les chaînes de sortie possibles sont répertoriés dans le tableau suivant.

| Procédure opérationnelle | Condition | Message |
|:---|:---|:---|
| Démarrer-AzureVMs | Machine virtuelle est déjà en cours d’exécution.  | MyVM est déjà en cours d’exécution. |
| Démarrer-AzureVMs | Demande de démarrage de machine virtuelle envoyé avec succès | MyVM a été démarré. |
| Démarrer-AzureVMs | Échec de la demande de démarrage de la machine virtuelle  | MyVM n’a pas pu démarrer |
| Stop-AzureVMs | Machine virtuelle est déjà arrêté.  | MyVM est déjà arrêté. |
| Stop-AzureVMs | Arrêt de la demande de la machine virtuelle envoyé avec succès | MyVM a été arrêté. |
| Stop-AzureVMs | Échec de la demande d’arrêt de la machine virtuelle  | MyVM n’a pas pu arrêter |

Par exemple, l’extrait de code suivant à partir d’une procédure opérationnelle tente de démarrer tous les ordinateurs virtuels avec le nom du service *MyServiceName*.  Si une de l’échec de demandes de début, puis erreur actions peuvent être prises. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Analyse détaillée

Voici une description détaillée de la procédures opérationnelles dans ce scénario.  Vous pouvez utiliser ces informations pour personnaliser les procédures opérationnelles ou simplement pour obtenir des informations à partir de ceux-ci pour la création de vos propres scénarios d’automatisation.

### <a name="parameters"></a>Paramètres

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Le flux de travail démarre en obtenant les valeurs pour les [paramètres d’entrée](#using-the-scenario).  Si les noms de ressources ne sont pas fournis les noms par défaut sont utilisés.

### <a name="output"></a>Sortie

    # Returns strings with status messages
    [OutputType([String])]

Cette ligne déclare que la sortie de la procédure opérationnelle sera une chaîne.  Ce n’est pas nécessaire, mais est conseillée pour lorsque la procédure opérationnelle est utilisée comme une [procédure opérationnelle des enfants](automation-child-runbooks.md) afin qu’une procédure opérationnelle de parents connaissent le type de sortie d’attendre.

### <a name="authentication"></a>Authentification

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Les lignes suivant définissent les [informations d’identification](automation-configuring.md#configuring-authentication-to-azure-resources) et un abonnement Azure qui sera utilisé pour le reste de la procédure opérationnelle.
Tout d’abord, nous utilisons **Get-AutomationPSCredential** pour obtenir de l’actif qui contient les informations d’identification disposant d’un accès pour démarrer et arrêter des ordinateurs virtuels dans l’abonnement Azure. **Ajouter-AzureAccount** utilise ensuite cet actif pour définir les informations d’identification.  Le résultat est assigné à une variable fictive afin qu’il n’est pas inclus dans la sortie de procédure opérationnelle.  

L’immobilisation variable avec l’ID d’abonnement est ensuite récupérée avec **Get-AutomationVariable** et de l’abonnement avec **Select-AzureSubscription**.

### <a name="get-vms"></a>Obtenir des machines virtuelles

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**Get-AzureVM** permet de récupérer les ordinateurs virtuels que fonctionne avec la procédure opérationnelle.  Si une valeur est fournie dans la variable d’entrée **ServiceName** , uniquement les ordinateurs virtuels portant le même nom de service sont récupérés.  Si **ServiceName** est vide, tous les ordinateurs virtuels sont récupérés.

### <a name="startstop-virtual-machines-and-send-output"></a>Démarrer/arrêter les machines virtuelles et envoyer la sortie

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

L’étape suivante de lignes par l’intermédiaire de chaque machine virtuelle.  Tout d’abord le **PowerState** de l’ordinateur virtuel est vérifiée pour voir si elle est déjà en cours d’exécution ou arrêté, selon la procédure opérationnelle.  S’il est déjà dans l’état de la cible, un message est envoyé à la sortie et la fin de la procédure opérationnelle.  Si ce n’est pas le cas, puis **Démarrer-AzureVM** ou **Stop-AzureVM** est utilisé pour tenter de démarrer ou d’arrêter l’ordinateur virtuel avec le résultat de la requête stockée dans une variable.  Puis un message en sortie indiquant si la demande pour démarrer ou arrêter a été envoyée avec succès.


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’utilisation des procédures opérationnelles des enfants, consultez les [procédures opérationnelles des enfants dans Azure Automation](automation-child-runbooks.md) 
- Pour plus d’informations sur les messages de sortie lors de l’exécution de la procédure opérationnelle et de la journalisation pour résoudre les problèmes, consultez la [sortie de procédure opérationnelle et messages dans Azure Automation](automation-runbook-output-and-messages.md)
