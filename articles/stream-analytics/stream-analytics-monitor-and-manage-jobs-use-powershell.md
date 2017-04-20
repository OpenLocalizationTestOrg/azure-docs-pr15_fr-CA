<properties 
    pageTitle="Surveiller et gérer les tâches de flux de données Analytique avec PowerShell | Microsoft Azure" 
    description="Apprenez à utiliser des applets de commande et de PowerShell d’Azure pour surveiller et gérer les tâches de flux de données Analytique." 
    keywords="Azure powershell, les applets de commande powershell azure, commande powershell, powershell scripting" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Surveiller et gérer les tâches de flux Analytique avec les applets de commande PowerShell de Azure

Apprenez à surveiller et gérer les ressources de flux Analytique avec les applets de commande PowerShell de Azure et powershell scripts qui exécutent des tâches de flux de données Analytique base.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Conditions requises pour l’exécution d’applets de commande PowerShell de Azure pour flux Analytique

 - Créer un groupe de ressources Azure dans votre abonnement. Voici un exemple de script PowerShell d’Azure. Pour plus d’informations de PowerShell d’Azure, consultez [installer et configurer Azure PowerShell](../powershell-install-configure.md);  

PowerShell Azure 0.9.8 :  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0 :  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] Travaux d’Analytique de flux créés par programmation n’ont pas de contrôle activé par défaut.  Vous pouvez activer manuellement la surveillance dans le portail Azure par page de moniteur du travail en cliquant sur le bouton Activer ou ce faire par programme en procédant à [l’Analytique de flux Azure - travaux des Analytique flux moniteur par programmation](stream-analytics-monitor-jobs.md).

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Applets de commande PowerShell Azure pour flux Analytique
Les applets de commande PowerShell de Azure suivant peut servir à surveiller et gérer les travaux d’Azure flux Analytique. Notez que différentes versions Azure PowerShell. 
**Dans les exemples cités, la première commande est pour Azure PowerShell 0.9.8, la seconde commande est pour Azure PowerShell 1.0.** Les commandes d’Azure PowerShell 1.0 aura toujours « AzureRM » dans la commande.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Répertorie toutes les tâches de flux Analytique définis dans l’abonnement Azure ou d’un groupe de ressources spécifié, ou obtient des informations de travail sur une tâche spécifique au sein d’un groupe de ressources.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsJob

Cette commande PowerShell renvoie des informations sur toutes les tâches de flux de données Analytique dans l’abonnement Azure.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Cette commande PowerShell renvoie des informations sur toutes les tâches de flux de données Analytique dans le groupe de ressources StreamAnalytics-par défaut-Central-US.

**Exemple 3**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Cette commande PowerShell renvoie des informations sur la tâche de flux de données Analytique StreamingJob dans le groupe de ressources StreamAnalytics-par défaut-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Répertorie toutes les entrées qui sont définies dans un travail Analytique de flux de données spécifié, ou obtient des informations sur une entrée spécifique.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Cette commande PowerShell renvoie des informations sur toutes les entrées définies dans le projet StreamingJob.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Cette commande PowerShell renvoie des informations sur l’entrée nommée EntryStream définie dans le projet StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Répertorie toutes les sorties sont définis dans un travail Analytique de flux de données spécifié, ou obtient des informations à propos d’une sortie spécifique.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Cette commande PowerShell renvoie des informations sur les sorties définis dans le projet StreamingJob.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Cette commande PowerShell renvoie des informations sur la sortie nommée sortie définie dans le projet StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Obtient des informations sur le quota de diffusion en continu des unités dans une région déterminée.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Cette commande PowerShell renvoie des informations sur l’utilisation d’unités de transmission en continu et de quota dans la région Amérique centrale.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtient des informations à propos d’une transformation spécifique définie dans une tâche de flux de données Analytique.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Cette commande PowerShell renvoie des informations sur la transformation appelée StreamingJob dans le projet StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Nouvelle-AzureStreamAnalyticsInput | Nouvelle-AzureRMStreamAnalyticsInput
Crée une nouvelle entrée dans une tâche de flux de données Analytique ou mises à jour d’une entrée spécifiée existante.
  
Le nom de l’entrée peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux sont spécifiés, le nom de la ligne de commande doit être le même que celui du fichier.

Si vous spécifiez une entrée qui existe déjà et que vous ne spécifiez pas de paramètre-Force, l’applet de commande vous demande s’il faut ou non remplacer l’entrée existante.

Si vous spécifiez l’option – Force le paramètre et spécifiez un fichier d’entrée de nom, l’entrée sera remplacée sans demande de confirmation.

Pour plus d’informations sur la structure de fichier JSON et du contenu, reportez-vous à l' [Entrée de créer (Analytique de flux Azure)] [ msdn-rest-api-create-stream-analytics-input] section de la [bibliothèque de référence d’API de flux Analytique gestion reste][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Cette commande PowerShell crée une nouvelle entrée à partir du fichier Input.json. Si une entrée existante avec le nom spécifié dans le fichier de définition d’entrée est déjà définie, l’applet de commande vous demande de remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Cette commande PowerShell crée une nouvelle entrée dans le projet nommé EntryStream. Si une entrée existante portant ce nom est déjà définie, l’applet de commande vous demande de remplacer ou non.

**Exemple 3**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Cette commande PowerShell remplace la définition de la source d’entrée existante appelée EntryStream avec la définition à partir du fichier.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Nouvelle-AzureStreamAnalyticsJob | Nouvelle-AzureRMStreamAnalyticsJob
Crée une nouvelle tâche de flux de données Analytique dans Microsoft Azure, ou met à jour la définition d’un travail spécifié existant.

Le nom de la tâche peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux sont spécifiés, le nom de la ligne de commande doit être le même que celui du fichier.

Si vous spécifiez un nom de travail qui existe déjà et que vous ne spécifiez pas de paramètre-Force, l’applet de commande vous demande de remplacer le projet existant ou non.

Si vous spécifiez – forcer le paramètre et spécifiez un nom de travail existant, la définition de travail est remplacée sans demande de confirmation.

Pour plus d’informations sur la structure de fichier JSON et du contenu, consultez [Créer un flux de travail Analytique] [ msdn-rest-api-create-stream-analytics-job] section de la [bibliothèque de référence d’API de flux Analytique gestion reste][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Cette commande PowerShell crée une nouvelle tâche à partir de la définition de JobDefinition.json. Si un projet existant avec le nom spécifié dans le fichier de définition de travail est déjà défini, l’applet de commande vous demande de remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Cette commande PowerShell remplace la définition de tâche StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Nouvelle-AzureStreamAnalyticsOutput | Nouvelle-AzureRMStreamAnalyticsOutput
Crée une nouvelle sortie d’un projet de flux de données Analytique ou mises à jour de sortie existant.  

Le nom de la sortie peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux sont spécifiés, le nom de la ligne de commande doit être le même que celui du fichier.

Si vous spécifiez une sortie qui existe déjà et que vous ne spécifiez pas de paramètre-Force, l’applet de commande vous demande de remplacer le résultat existant ou non.

Si vous spécifiez l’option – Force le paramètre et spécifiez le nom de sortie existant, la sortie est remplacée sans demande de confirmation.

Pour plus d’informations sur la structure de fichier JSON et du contenu, reportez-vous à la [Sortie de créer (Analytique de flux Azure)] [ msdn-rest-api-create-stream-analytics-output] section de la [bibliothèque de référence d’API de flux Analytique gestion reste][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Cette commande PowerShell crée une nouvelle sortie appelée « sortie » dans le projet StreamingJob. Si une sortie existante portant le même nom est déjà définie, l’applet de commande vous demande de remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Cette commande PowerShell remplace la définition de « sortie » de la tâche StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Nouvelle-AzureStreamAnalyticsTransformation | Nouvelle-AzureRMStreamAnalyticsTransformation
Crée une nouvelle transformation dans une tâche de flux de données Analytique ou mises à jour de la transformation existante.
  
Le nom de la transformation peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux sont spécifiés, le nom de la ligne de commande doit être le même que celui du fichier.

Si vous spécifiez une transformation qui existe déjà et que vous ne spécifiez pas de paramètre-Force, l’applet de commande vous demande de remplacer la transformation existante ou non.

Si vous spécifiez – forcer le paramètre et spécifier un nom de transformation existante, la transformation est remplacée sans demande de confirmation.

Pour plus d’informations sur la structure de fichier JSON et du contenu, reportez-vous à la [Créer une Transformation (Analytique de flux Azure)] [ msdn-rest-api-create-stream-analytics-transformation] section de la [bibliothèque de référence d’API de flux Analytique gestion reste][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Cette commande PowerShell crée une nouvelle transformation appelée StreamingJobTransform dans le projet StreamingJob. Si une transformation existante est déjà définie avec ce nom, l’applet de commande vous demande de remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Cette commande PowerShell remplace la définition de StreamingJobTransform à la StreamingJob de la tâche.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Supprimer-AzureStreamAnalyticsInput | Supprimer-AzureRMStreamAnalyticsInput
En mode asynchrone, supprime une entrée spécifique d’une tâche de flux de données Analytique de Microsoft Azure.  
Si vous spécifiez – paramètre Force, l’entrée sera supprimée sans confirmation.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0 :  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Cette commande PowerShell supprime l’entrée EventStream dans le projet StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Supprimer-AzureStreamAnalyticsJob | Supprimer-AzureRMStreamAnalyticsJob
En mode asynchrone supprime un travail Analytique de flux spécifique dans Microsoft Azure.  
Si vous spécifiez – paramètre Force, la tâche sera supprimée sans confirmation.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0 :  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Cette commande PowerShell supprime la tâche StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Supprimer-AzureStreamAnalyticsOutput | Supprimer-AzureRMStreamAnalyticsOutput
En mode asynchrone, supprime une sortie spécifique d’une tâche de flux de données Analytique de Microsoft Azure.  
Si vous spécifiez – paramètre Force, la sortie sera supprimée sans confirmation.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0 :  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Cette commande PowerShell, supprime la sortie de sortie de la tâche StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Démarrer-AzureStreamAnalyticsJob | Démarrer-AzureRMStreamAnalyticsJob
Déploie et démarre une tâche de flux de données Analytique dans Microsoft Azure de façon asynchrone.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0 :  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Cette commande PowerShell démarre le travail StreamingJob avec une heure de début de sortie personnalisée la valeur 12 décembre 2012, 12:12:12 UTC.


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Arrête un travail Analytique de flux de données de s’exécuter dans Microsoft Azure et annule l’allocation des ressources qui ont été utilisé de manière asynchrone. La définition de travail et les métadonnées restent disponibles au sein de votre abonnement via le portail Azure et gestion API, telles que le travail peut être modifié et redémarré. Vous ne sera pas débitée pour une tâche à l’état arrêté.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0 :  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Cette commande PowerShell arrête la tâche StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Permet de tester la capacité de flux Analytique pour se connecter à une entrée spécifiée.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0 :  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Cette commande PowerShell teste l’état de la connexion de l’EntryStream d’entrée dans StreamingJob.  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Permet de tester la capacité de flux Analytique pour se connecter à une sortie spécifié.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0 :  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Ce tests de commande PowerShell de la sortie de l’état de la connexion de sortie dans StreamingJob.  

## <a name="get-support"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 


## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
