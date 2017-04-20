<properties
    pageTitle="Mise en route avec les tâches de base de données élastique"
    description="comment utiliser les tâches de base de données élastique"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>Mise en route avec les tâches de base de données élastique

Tâches de base de données élastiques (aperçu) pour la base de données de SQL Azure permet de vous la fiabilité d’exécuter des scripts T-SQL qui s’étendent sur plusieurs bases de données lors de la nouvelle tentative automatiquement et en offrant des garanties d’achèvement éventuel. Pour plus d’informations sur la fonctionnalité de travail élastique de la base de données, reportez-vous à la [page de vue d’ensemble de fonctionnalité](sql-database-elastic-jobs-overview.md).

Cette rubrique étend l’exemple de [mise en route avec les outils de base de données élastique](sql-database-elastic-scale-get-started.md). Une fois terminé, vous serez à même : Apprenez à créer et gérer des tâches de gérer un groupe de bases de données liées. Il n’est pas nécessaire d’utiliser les outils d’évolutivité élastique pour tirer parti des avantages de travaux élastiques.

## <a name="prerequisites"></a>Conditions préalables

Téléchargez et exécutez la [mise en route avec les exemples d’outils élastique de la base de données](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Créer un partagé Gestionnaire de mappage à l’aide de l’exemple d’application

Ici vous allez créer un mappage partagé manager avec plusieurs milieu des fragments, suivie de l’insertion de données dans les milieu des fragments. Si vous avez déjà configuré avec des données sharded dans leur milieu des fragments, vous pouvez ignorer les étapes suivantes et passer à la section suivante.

1. Générer et exécuter l’exemple d’application **mise en route avec les outils de base de données élastique** . Suivez la procédure jusqu'à l’étape 7 dans la section [télécharger et exécute l’exemple d’application](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). À la fin de l’étape 7, vous verrez l’invite de commande suivante :

    ![invite de commande][1]

2.  Dans la fenêtre de commande, tapez « 1 » et appuyez sur **entrée**. Ceci crée l’éclater Gestionnaire de mappage et ajoute deux milieu des fragments sur le serveur. Tapez « 3 », puis appuyez sur **entrée**. Répétez cette action quatre fois. Cela permet d’insérer des lignes de données exemple dans les milieu des fragments.

3.  Le [Portail Azure](https://portal.azure.com) doit afficher les trois nouvelles bases de données dans votre serveur v12 :

    ![Confirmation de Visual Studio][2]

    À ce stade, nous allons créer une collection de base de données personnalisée qui reflète toutes les bases de données de la table partagé. Cela nous permettra de créer et d’exécuter un travail qui ajoutent une nouvelle table dans le milieu des fragments.

Ici nous serait généralement créer une cible de table partagé, à l’aide de l’applet de commande **New-AzureSqlJobTarget** . La base de données du Gestionnaire de table partagé doit être défini en tant que base de données cible et puis la table partagé spécifique est spécifiée en tant que cible. Au lieu de cela, nous allons énumérer toutes les bases de données dans le serveur et ajoutez les bases de données dans la nouvelle collection personnalisée, à l’exception de base de données master.

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Crée une collection personnalisée et ajoute toutes les bases de données sur le serveur à la cible de la collection personnalisée, à l’exception de master.


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Créer un Script T-SQL pour exécution sur plusieurs bases de données

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Créer la tâche pour exécuter un script sur le groupe personnalisé des bases de données

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>Exécuter la tâche 

Le script PowerShell suivant peut être utilisé pour exécuter une tâche existante :

Mettre à jour la variable suivante pour refléter le nom de la tâche souhaitée à exécuter :

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>Récupérer l’état de l’exécution d’une tâche unique

Utilisez l’applet de commande **Get-AzureSqlJobExecution** même avec le paramètre **IncludeChildren** pour afficher l’état d’exécution de travaux d’enfants, à savoir l’état spécifique à chaque exécution du travail sur chaque base de données ciblée par le projet.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>L’état d’affichage entre plusieurs exécutions de travaux

L’applet de commande **Get-AzureSqlJobExecution** a plusieurs paramètres facultatifs qui peuvent être utilisés pour afficher plusieurs exécutions de travaux, filtrées par le biais de paramètres fournis. Le code ci-dessous illustre quelques-unes des façons possibles d’utiliser Get-AzureSqlJobExecution :

Récupérer toutes les exécutions de la tâche de niveau supérieur active :

    Get-AzureSqlJobExecution

Récupérer toutes les exécutions du travail de niveau supérieur, y compris les exécutions de la tâche inactive :

    Get-AzureSqlJobExecution -IncludeInactive

Récupérer toutes les exécutions de travaux d’enfants d’un ID d’exécution de tâche fourni, y compris les exécutions de la tâche inactive :

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Récupérer toutes les exécutions de travaux créées à l’aide d’une planification / combinaison, y compris les travaux inactifs du travail :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Récupérer tous les projets qui ciblent un mappage partagé spécifié, y compris les travaux inactifs :

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Récupérer tous les projets qui ciblent une collection personnalisée spécifiée, y compris les travaux inactifs :

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Récupérer la liste des exécutions de tâches de travail au sein de l’exécution d’une tâche spécifique :

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Récupérer les détails de l’exécution de tâches de travail :

Le script PowerShell suivant peut être utilisé pour afficher les détails d’une exécution de tâche de projet, qui est particulièrement utile lors du débogage des erreurs d’exécution.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="retrieve-failures-within-job-task-executions"></a>Récupérer des échecs dans les exécutions de tâche de projet

L’objet JobTaskExecution inclut une propriété pour le cycle de vie de la tâche ainsi que d’une propriété de Message. Si une tâche l’exécution du travail a échoué, la propriété du cycle de vie sera définie à *Échec* et la propriété Message est fixée pour le message d’exception qui en résulte et sa pile. Si une tâche n’a pas réussi, il est important afficher les détails des tâches de projet qui n’a pas abouti pour une tâche donnée.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>En attente de l’exécution d’une tâche à terminer

Le script PowerShell suivant peut être utilisé pour attendre une tâche Terminer :

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Créer une stratégie d’exécution personnalisés

Élastiques travaux de base de données prend en charge la création de stratégies d’exécution personnalisés qui peuvent être appliqués lors du démarrage des travaux.
  
Stratégies d’exécution autorisent actuellement pour la définition de :

* Nom : L’identificateur de la stratégie d’exécution.
* Du délai d’attente de travail : Durée totale avant une tâche sera annulée par élastique travaux de base de données.
* Intervalle de nouvelle tentative initiale : L’intervalle d’attente avant la première nouvelle tentative.
* Intervalle maximal de tentatives : Cap des intervalles de reprise à utiliser.
* Réessayer intervalle intervalle Coefficient : Coefficient permet de calculer le prochain intervalle entre les tentatives.  La formule suivante est utilisée : (intervalle avant nouvelle tentative initiale) * Math.pow (intervalle intervalle Coefficient (), (nombre de tentatives de) - 2). 
* Nombre maximal de tentatives : Le nombre maximal de nouvelles tentatives tente d’exécuter un travail.

La stratégie d’exécution par défaut utilise les valeurs suivantes :

* Nom : Stratégie d’exécution par défaut
* Délai d’attente de travail : 1 semaine
* Intervalle de nouvelle tentative initiale : 100 millisecondes
* Intervalle maximal de tentatives : 30 minutes
* Coefficient de l’intervalle de tentatives : 2
* Nombre maximal de tentatives : 2 147 483 647

Créer la stratégie d’exécution de votre choix :

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Mise à jour d’une stratégie d’exécution personnalisés

Mise à jour de la stratégie d’exécution de votre choix pour mettre à jour :

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>Annulation d’une tâche

Élastique de tâches de base de données prend en charge les demandes d’annulation de tâches.  Si élastique travaux de base de données détecte une demande d’annulation d’une tâche en cours d’exécution, il va tenter d’arrêter le travail.

Il existe qu’élastique travaux de base de données peut effectuer une annulation de deux façons :

1. Annulation en cours d’exécution des tâches : si une annulation est détectée pendant une tâche est en cours d’exécution, une annulation va être tentée dans l’aspect en cours d’exécution de la tâche.  Par exemple : s’il existe une requête longue en cours d’exécution lorsqu’une annulation est tentée, il y aura une tentative d’annulation de la requête.
2. Tentatives de tâche annulation : Si une annulation est détectée par le thread de contrôle avant de lancer une tâche pour exécution, le thread de contrôle sera éviter le lancement de la tâche et déclarez la demande comme étant annulée.

Si une annulation de tâche est demandée pour un projet parent, la demande d’annulation sera respectée pour la tâche parente et toutes ses tâches enfants.
 
Pour soumettre une demande d’annulation, utilisez l’applet de commande **Stop-AzureSqlJobExecution** et définissez le paramètre **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Suppression d’un travail par nom et de l’historique du travail

Élastiques travaux de base de données prend en charge la suppression des travaux asynchrone. Un travail peut être marqué pour suppression et que le système supprime le travail et son historique de travail une fois que toutes les exécutions de la tâche est terminée pour le travail. Le système n’annule pas automatiquement les exécutions de la tâche active.  

Au lieu de cela, Stop-AzureSqlJobExecution doit être appelée pour annuler les exécutions de la tâche active.

Pour déclencher la suppression de la tâche, utilisez l’applet de commande **Remove-AzureSqlJob** et définissez le paramètre de **NomTâche** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>Créer une cible de la base de données personnalisée
Cibles de la base de données personnalisés peuvent être définis dans les travaux élastique de la base de données qui peuvent être utilisés pour l’exécution directement ou pour l’inclusion dans un groupe de la base de données personnalisée. Dans la mesure où **les pools élastique de la base de données** ne sont pas encore directement pris en charge via la APIs PowerShell, vous créez simplement une cible de la base de données personnalisée et de la cible de collection de base de données personnalisée qui englobe toutes les bases de données dans le pool.

Définir les variables suivantes pour prendre en compte les informations de la base de données souhaitée :

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>Créer une cible de la collection personnalisée de la base de données
Une cible de la collection de la base de données personnalisés peut être définie pour permettre l’exécution sur plusieurs cibles de la base de données. Après avoir créé un groupe de la base de données, bases de données peuvent être associés à la cible de la collection personnalisée.

Définir les variables suivantes pour refléter la configuration de la cible souhaitée collection personnalisée :

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>Ajouter des bases de données vers une cible de la collection personnalisée de la base de données

Cibles de la base de données peuvent être associées à des cibles de collection de base de données personnalisée pour créer un groupe de bases de données. Lors de la création d’un projet qui cible une cible de la collection personnalisée de la base de données, il sera développé pour cibler les bases de données associées au groupe au moment de l’exécution.

Ajouter la base de données à une collection personnalisée spécifique :

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Passez en revue les bases de données au sein d’une cible de la collection personnalisée de la base de données

Utilisez l’applet de commande **Get-AzureSqlJobTarget** pour récupérer les bases de données enfant dans une cible de la collection personnalisée de la base de données. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Créer une tâche pour exécuter un script sur une cible de la collection personnalisée de la base de données

Pour créer une tâche sur un groupe de bases de données définies par une cible de la collection personnalisée de la base de données, utilisez l’applet de commande **New-AzureSqlJob** . Élastiques travaux de base de données vous développez la tâche en plusieurs tâches enfants correspondant chacun à une base de données associé à la cible de la collection personnalisée de la base de données et vous assurer que le script est exécuté sur chaque base de données. Encore une fois, il est important que les scripts sont idempotents à résister aux tentatives.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Collecte de données sur les bases de données

**Travaux d’élastique de la base de données** prend en charge l’exécution d’une requête sur un groupe de bases de données et envoie les résultats vers une table d’une base de données spécifié. La table peut être interrogée après le fait de voir les résultats de la requête à partir de chaque base de données. Fournit un mécanisme asynchrone pour exécuter une requête sur plusieurs bases de données. Cas d’échec des bases de données est temporairement indisponible sont gérées automatiquement par l’intermédiaire de tentatives.

La table de destination spécifiée sera créée automatiquement si elle n’existe pas encore, qui correspondent au schéma du jeu de résultats. Si l’exécution d’une script renvoie plusieurs jeux de résultats, les tâches de base de données élastique enverra uniquement la première personne à la table de destination fourni.

Le script PowerShell suivant peut être utilisé pour exécuter un script collecte ses résultats dans un tableau spécifié. Ce script suppose qu’un script T-SQL a été créé, qui retourne un seul jeu de résultats et une cible de la collection personnalisée de la base de données a été créée.

Définir les éléments suivants afin de refléter le script désiré, informations d’identification et cible d’exécution :

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Créer et démarrer un travail pour les scénarios de collecte de données
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Créer une planification pour l’exécution du travail à l’aide d’un déclencheur de tâche

Le script PowerShell suivant peut être utilisé pour créer une planification de récurrence. Ce script utilise un intervalle d’une minute, mais le nouveau-AzureSqlJobSchedule prend également en charge les paramètres - DayInterval et - HourInterval, - MonthInterval, - WeekInterval. Les tableaux qui s’exécuter qu’une seule fois peuvent être créés en transmettant - unique.

Créer un nouveau tableau :

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Créer un déclencheur de tâche pour une tâche s’exécute selon une planification

Un déclencheur de tâche peut être défini pour un travail à exécuter en fonction d’un calendrier. Le script PowerShell suivant peut être utilisé pour créer un déclencheur de la tâche.

Définir les variables suivantes pour correspondre à la tâche de votre choix et de la planification :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Supprimer une association planifiée pour arrêter la tâche à partir de l’exécution de la planification

Pour interrompre l’exécution du travail récurrents via un déclencheur de la tâche, le déclencheur de tâche peut être supprimé. Supprimer un déclencheur de tâche pour arrêter une tâche à partir d’en cours d’exécution en fonction d’une planification à l’aide de l’applet de commande **Remove-AzureSqlJobTrigger** .

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>Importer des résultats de la requête de base de données élastique dans Excel

 Vous pouvez importer les résultats à partir d’une requête vers un fichier Excel.

1. Lancez Excel 2013.
2.  Naviguer sur le ruban de **données** .
3.  Cliquez sur **à partir d’autres Sources** , puis cliquez sur **à partir de SQL Server**.

    ![Importation d’Excel à partir d’autres sources][5]
4.  Dans l' **Assistant connexion de données** , tapez les références de nom et de la connexion du serveur. Puis cliquez sur **suivant**.
5.  Dans la boîte de dialogue **Sélectionner la base de données qui contient les données souhaitées**, sélectionnez la base de données **ElasticDBQuery** .
6.  Sélectionnez la table **Customers** dans la liste et cliquez sur **suivant**. Puis cliquez sur **Terminer**.
7.  Dans le formulaire de **Données** , sous **Sélectionnez la manière dont vous souhaitez afficher ces données dans votre classeur**, sélectionnez la **Table** et cliquez sur **OK**.

Toutes les lignes de la table **clients** , stockées dans un autre milieu des fragments de remplir la feuille Excel.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant utiliser les fonctions de données de Microsoft Excel. Utiliser la chaîne de connexion avec votre nom de serveur, nom de base de données et les informations d’identification pour se connecter vos outils d’intégration de BI et les données de la base de données de requête élastique. Assurez-vous que SQL Server est pris en charge comme source de données de votre outil. Reportez-vous à la base de données de requête élastique et les tables externes comme toute autre base de données SQL Server et de SQL Server que vous devez vous connecter à avec votre outil de.

### <a name="cost"></a>Coût
Il n’y a aucun frais supplémentaires pour l’utilisation de la fonctionnalité de requête de base de données élastique. Toutefois, à ce moment cette fonctionnalité est disponible uniquement sur les bases de données premium, sous la forme d’un point de terminaison, mais au milieu des fragments peuvent être de n’importe quel niveau de service.

Informations de tarification, consultez [Détails de prix de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
