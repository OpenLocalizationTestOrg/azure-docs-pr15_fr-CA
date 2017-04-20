<properties 
    pageTitle="Créer et gérer des tâches élastique de la base de données à l’aide de PowerShell" 
    description="PowerShell est utilisé pour gérer les pools de la base de données de SQL Azure" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>Créer et gérer un travaux d’élastique de la base de données de la base de données SQL à l’aide de PowerShell (aperçu)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



La APIs PowerShell pour des **travaux d’élastique de la base de données** (en aperçu), vous permettent de définir un groupe de bases de données par rapport à laquelle les scripts seront exécutent. Cet article explique comment créer et gérer des **travaux d’élastique de la base de données** à l’aide des applets de commande PowerShell. Consultez [vue d’ensemble des tâches élastique](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Conditions préalables
* Un abonnement Azure. Pour une version d’évaluation gratuite, consultez [un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Un ensemble de bases de données créées avec les outils de base de données élastique. Reportez-vous à la section [mise en route avec les outils de base de données élastique](sql-database-elastic-scale-get-started.md).
* PowerShell Azure. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).
* **Travaux d’élastique de la base de données** Package de PowerShell : voir [travaux d’installation de la base de données élastique](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Sélectionnez votre abonnement Azure

Pour sélectionner l’abonnement que vous avez besoin de votre Id (**-SubscriptionId**) de l’abonnement ou l’abonnement nom (**-SubscriptionName**). Si vous avez plusieurs abonnements, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copier les informations d’abonnement de votre choix dans le jeu de résultats. Une fois que vous avez vos informations d’abonnement, exécutez l’applet de commande suivante pour définir cet abonnement par défaut, à savoir la cible pour la création et la gestion des travaux :

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) est recommandé pour l’utilisation de développer et exécuter des scripts PowerShell contre les travaux élastique de la base de données.

## <a name="elastic-database-jobs-objects"></a>Objets de tâches de base de données élastiques

Le tableau suivant répertorie tous les types objet de **travaux de l’élasticité de la base de données** ainsi que sa description et ses pertinentes PowerShell APIs.

<table style="width:100%">
  <tr>
    <th>Type d’objet</th>
    <th>Description</th>
    <th>API de PowerShell connexes</th>
  </tr>
  <tr>
    <td>Informations d’identification</td>
    <td>Nom d’utilisateur et mot de passe à utiliser lors de la connexion aux bases de données pour l’exécution de scripts ou l’application de DACPACs. <p>Le mot de passe est chiffré avant d’envoyer à et de stockage dans la base de données des tâches de base de données élastique.  Le mot de passe est décrypté par le service élastique travaux de base de données via les informations d’identification créé et téléchargé à partir du script d’installation.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Nouvelle-AzureSqlJobCredential</p><p>Ensemble-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script Transact-SQL à utiliser pour l’exécution sur plusieurs bases de données.  Le script doit être créé pour être idempotent, dans la mesure où le service réessaiera l’exécution du script lors de défaillances.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Nouvelle-AzureSqlJobContent</p>
    <p>Ensemble-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Application de couche données </a> package à appliquer entre les bases de données.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Nouvelle-AzureSqlJobContent</p>
    <p>Ensemble-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Base de données cible</td>
    <td>Nom de serveur et de base de données qui pointe vers une base de données de SQL Azure.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nouvelle-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Cible du mappage partagé</td>
    <td>Combinaison d’une cible de la base de données et les informations d’identification à utiliser pour déterminer les informations stockées dans une table d’éclater élastique de la base de données.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nouvelle-AzureSqlJobTarget</p>
    <p>Ensemble-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Cible de la Collection personnalisée</td>
    <td>Groupe défini de bases de données utilisent collectivement pour l’exécution.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nouvelle-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Collection personnalisée enfant cible</td>
    <td>Cible de base de données qui est référencée à partir d’une collection personnalisée.</td>
    <td>
    <p>Ajouter-AzureSqlJobChildTarget</p>
    <p>Supprimer-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Travail</td>
    <td>
    <p>Définition des paramètres d’une tâche qui peut être utilisé pour déclencher l’exécution ou pour répondre à une planification.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Nouvelle-AzureSqlJob</p>
    <p>Ensemble-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Exécution du travail</td>
    <td>
    <p>Conteneur de tâches nécessaires pour répondre à l’exécution d’un script ou l’application d’un DACPAC à une cible à l’aide des informations d’identification pour les connexions de base de données avec des erreurs soit géré conformément à la stratégie d’exécution.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Démarrer-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Attente-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Exécution de la tâche de projet</td>
    <td>
    <p>Unité de travail unique pour répondre à une tâche.</p>
    <p>Si une tâche ne peut pas correctement exécuter, le message d’exception qui en résulte est enregistré et une nouvelle tâche de projet correspondant est créée et exécutée conformément à la stratégie d’exécution spécifiée.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Démarrer-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Attente-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Stratégie d’exécution de tâche</td>
    <td>
    <p>Contrôle travaux des délais d’exécution, nombre maximal de tentatives et intervalles entre les tentatives.</p>
    <p>Élastiques travaux de base de données inclut une stratégie d’exécution de travail par défaut avec intervalle exponentielle des intervalles de temps entre chaque tentative de provoquer essentiellement les tentatives infinies des échecs de tâche de projet.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Nouvelle-AzureSqlJobExecutionPolicy</p>
    <p>Ensemble-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Planification</td>
    <td>
    <p>Spécification pour avoir lieu dans un intervalle de récurrence ou à la fois de l’exécution basé sur le temps.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Nouvelle-AzureSqlJobSchedule</p>
    <p>Ensemble-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Déclencheurs de tâche</td>
    <td>
    <p>Un mappage entre un projet et un calendrier pour l’exécution du travail de déclenchement en fonction de la planification.</p>
    </td>
    <td>
    <p>Nouvelle-AzureSqlJobTrigger</p>
    <p>Supprimer-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Types de groupe de travaux d’élastique de la base de données pris en charge
Le travail s’exécute les scripts Transact-SQL (T-SQL) ou l’application de DACPACs sur un groupe de bases de données. Lorsqu’un travail est soumis pour être exécutées sur un groupe de bases de données, la tâche « développe » l’en tâches enfants où chacun effectue l’exécution demandée par rapport à une base de données unique dans le groupe. 
 
Il existe deux types de groupes que vous pouvez créer : 

* Groupe de [Mappage de partagé](sql-database-elastic-scale-shard-map-management.md) : lorsqu’un travail est soumis pour cibler un mappage partagé, la tâche interroge la table partagé pour déterminer son ensemble de milieu des fragments et crée ensuite des tâches pour chaque partagé enfant dans le plan de l’éclater.
* Groupe de Collection personnalisé : personnalisé défini par l’ensemble des bases de données. Lorsqu’un projet cible une collection personnalisée, il crée des enfants tâches pour chaque base de données actuellement dans la collection personnalisée.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Pour définir la base de données élastique tâches de connexion

Une connexion doit être définie dans la *base de données de contrôle* de travaux avant d’utiliser les API de travaux. Cette applet de commande en cours d’exécution déclenche une fenêtre d’informations d’identification contextuel qui demande le nom d’utilisateur et le mot de passe créé lors de l’installation des travaux de l’élasticité de la base de données. Tous les exemples fournis dans cette rubrique supposent que cette première étape a déjà été effectuée.

Ouvrir une connexion à des tâches de base de données élastique :

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Informations d’identification chiffrées dans les tâches de base de données élastique

Informations d’identification de la base de données peuvent être insérées dans la *base de données de contrôle* de travaux avec son mot de passe crypté. Il est nécessaire de stocker les informations d’identification pour activer les travaux à exécuter par la suite, (à l’aide de planifications de travaux).
 
Le cryptage fonctionne via un certificat créé dans le cadre du script d’installation. Le script d’installation crée et télécharge le certificat dans le Service en nuage Azure pour le décryptage des mots de passe cryptés stockés. Le Service en nuage Azure stocke ensuite la clé publique de la tâches, *base de données de contrôle* qui permet à l’interface API de PowerShell ou portail classique d’Azure crypter un mot de passe fourni sans toutefois exiger le certificat doit être installé localement.
 
Les mots de passe des informations d’identification sont cryptées et sécurisée des utilisateurs avec un accès en lecture aux objets de travaux élastique de la base de données. Mais il est possible pour un utilisateur malveillant disposant d’un accès en lecture-écriture pour les objets élastique travaux de base de données extraire un mot de passe. Informations d’identification sont conçues pour être réutilisés entre les exécutions de travaux. Informations d’identification sont transmises aux bases de données cible lors de l’établissement des connexions. Il n’y a pas de restrictions sur les bases de données cible utilisés pour chaque information d’identification, l’utilisateur malveillant ajoute une cible de la base de données pour une base de données sous contrôle de l’utilisateur malveillant. L’utilisateur peut démarrer par la suite d’un travail de ciblage de cette base de données pour obtenir le mot de passe de l’information d’identification.

Meilleures pratiques de sécurité pour les tâches de base de données élastique sont les suivantes :

* Limite l’utilisation de l’API à des personnes de confiance.
* Informations d’identification doivent avoir le minimum de privilèges nécessaires pour effectuer la tâche.  Plus d’informations peuvent être affichées au sein de cet article de MSDN de SQL Server des [autorisations](https://msdn.microsoft.com/library/bb669084.aspx) .

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Pour créer des informations d’identification chiffrées pour l’exécution du travail sur les bases de données

Pour créer une nouvelle information d’identification cryptée, l' [**applet de commande Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) invite à entrer un nom d’utilisateur et le mot de passe qui peut être passé à l' [**applet de commande New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Pour mettre à jour les informations d’identification

Lors de la modification de mots de passe, utilisez l' [**applet de commande Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) et définissez le paramètre **CredentialName** .

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Pour définir une cible de table partagé élastique de la base de données

Pour exécuter une tâche par rapport à toutes les bases de données dans un jeu partagé (créé à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)), utilisez un mappage de partagé en tant que cible de la base de données. Cet exemple requiert une application sharded, créée à l’aide de la bibliothèque de client élastique de la base de données. Reportez-vous à la section [mise en route avec les exemples d’outils élastique de la base de données](sql-database-elastic-scale-get-started.md).

La base de données du Gestionnaire de table partagé doit être défini en tant que base de données cible et puis la table partagé spécifique doit être spécifiée en tant que cible.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Créer un Script T-SQL pour exécution sur plusieurs bases de données

Lors de la création des scripts T-SQL pour exécution, il est hautement recommandé de les générer pour être [idempotents](https://en.wikipedia.org/wiki/Idempotence) et résistance aux défaillances. Élastiques travaux de base de données va réessayer l’exécution d’un script à chaque fois que l’exécution rencontre une défaillance, quelle que soit la classification de l’échec.

Pour créer et enregistrer un script pour l’exécution et définir les paramètres **- ContentName présent** et **- CommandText** , utilisez l' [**applet de commande New-AzureSqlJobContent**](https://msdn.microsoft.com/library/mt346085.aspx) .

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Créer un script à partir d’un fichier
Si le script T-SQL est défini dans un fichier, utilisez ceci pour importer le script :

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Pour mettre à jour un script T-SQL pour exécution sur plusieurs bases de données  

Ce script PowerShell met à jour le texte de la commande T-SQL pour un script existant.

Définir les variables suivantes pour prendre en compte la définition du script désiré à définir :

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Pour mettre à jour la définition d’un script existant

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Pour créer un travail pour exécuter un script sur une carte partagé

Ce script PowerShell démarre une tâche pour l’exécution d’un script sur chaque partagé dans un mappage d’éclater évolutivité élastique.

Définir les variables suivantes pour refléter le script désiré et cible :

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Pour exécuter un travail 

Ce script PowerShell exécute une tâche existante :

Mettre à jour la variable suivante pour refléter le nom de la tâche souhaitée à exécuter :

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Pour récupérer l’état de l’exécution d’une tâche unique

Utilisez l' [**applet de commande Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) et définissez le paramètre **JobExecutionId** pour afficher l’état d’exécution des travaux.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Utilisez l’applet de commande **Get-AzureSqlJobExecution** même avec le paramètre **IncludeChildren** pour afficher l’état d’exécution de travaux d’enfants, à savoir l’état spécifique à chaque exécution du travail sur chaque base de données ciblée par le projet.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Pour afficher l’état sur plusieurs exécutions de travaux

L' [**applet de commande Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) a plusieurs paramètres facultatifs qui peuvent être utilisés pour afficher plusieurs exécutions de travaux, filtrées par le biais de paramètres fournis. Le code ci-dessous illustre quelques-unes des façons possibles d’utiliser Get-AzureSqlJobExecution :

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

## <a name="to-retrieve-failures-within-job-task-executions"></a>Pour récupérer des échecs dans les exécutions de tâche de projet

L' **objet de JobTaskExecution** inclut une propriété pour le cycle de vie de la tâche ainsi que d’une propriété de message. Si une tâche l’exécution du travail a échoué, la propriété du cycle de vie sera définie à *Échec* et la propriété message est fixée pour le message d’exception qui en résulte et sa pile. Si une tâche n’a pas réussi, il est important afficher les détails des tâches de projet qui n’a pas abouti pour une tâche donnée.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Pour attendre l’exécution d’une tâche à terminer

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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Annuler les tâches en cours d’exécution : si une annulation est détectée pendant une tâche est en cours d’exécution, une annulation va être tentée dans l’aspect en cours d’exécution de la tâche.  Par exemple : s’il existe une requête longue en cours d’exécution lorsqu’une annulation est tentée, il y aura une tentative d’annulation de la requête.
2. Annulation des tentatives de tâche : si une annulation est détectée par le thread de contrôle avant de lancer une tâche pour exécution, le thread de contrôle va éviter le lancement de la tâche et déclarer la demande comme étant annulée.

Si une annulation de tâche est demandée pour un projet parent, la demande d’annulation sera respectée pour la tâche parente et toutes ses tâches enfants.
 
Pour soumettre une demande d’annulation, utilisez l' [**applet de commande Stop-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) et définissez le paramètre **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Pour supprimer une tâche et l’historique du travail en mode asynchrone

Élastiques travaux de base de données prend en charge la suppression des travaux asynchrone. Un travail peut être marqué pour suppression et que le système supprime le travail et son historique de travail une fois que toutes les exécutions de la tâche est terminée pour le travail. Le système n’annule pas automatiquement les exécutions de la tâche active.  

L’appel [**Stop-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) pour annuler les exécutions de la tâche active.

Pour déclencher la suppression de la tâche, utilisez l' [**applet de commande Remove-AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx) et définissez le paramètre de **NomTâche** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>Pour créer une cible de la base de données personnalisée

Vous pouvez définir des cibles de base de données personnalisée pour l’exécution directe ou pour l’inclusion dans un groupe de la base de données personnalisée. Par exemple, **pools d’élasticité de la base de données** ne étant pas encore directement pris en charge à l’aide de PowerShell APIs, vous pouvez créer une cible de la base de données personnalisée et de la cible de collection de base de données personnalisée qui englobe toutes les bases de données dans le pool.

Définir les variables suivantes pour prendre en compte les informations de la base de données souhaitée :

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Pour créer une cible de la collection personnalisée de la base de données

Pour définir une cible de collection de base de données personnalisée pour permettre l’exécution sur plusieurs cibles de la base de données, utilisez l’applet de commande [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Après avoir créé un groupe de la base de données, les bases de données peuvent être associés à la cible de la collection personnalisée.

Définir les variables suivantes pour refléter la configuration de la cible souhaitée collection personnalisée :

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Pour ajouter des bases de données vers une cible de la collection personnalisée de la base de données

Pour ajouter une base de données à une collection personnalisée spécifique utilisez l’applet de commande [**Add-AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx) .

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Passez en revue les bases de données au sein d’une cible de la collection personnalisée de la base de données

Utilisez l’applet de commande [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) pour récupérer les bases de données enfant dans une cible de la collection personnalisée de la base de données. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Créer une tâche pour exécuter un script sur une cible de la collection personnalisée de la base de données

Pour créer une tâche sur un groupe de bases de données définies par une cible de la collection personnalisée de la base de données, utilisez l’applet de commande [**New-AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) . Élastiques travaux de base de données vous développez la tâche en plusieurs tâches enfants correspondant chacun à une base de données associé à la cible de la collection personnalisée de la base de données et vous assurer que le script est exécuté sur chaque base de données. Encore une fois, il est important que les scripts sont idempotents à résister aux tentatives.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Collecte de données sur les bases de données

Vous pouvez utiliser une tâche à exécuter une requête sur un groupe de bases de données et envoyer les résultats à une table spécifique. La table peut être interrogée après le fait de voir les résultats de la requête à partir de chaque base de données. Cela fournit une méthode asynchrone pour exécuter une requête sur plusieurs bases de données. Tentatives ayant échoué sont traitées automatiquement via les nouvelles tentatives.

La table de destination spécifiée sera automatiquement créée s’il n’en existe pas encore. La nouvelle table correspondre au schéma du jeu de résultats. Si un script renvoie plusieurs jeux de résultats, les tâches de base de données élastique enverra uniquement la première à la table de destination.

Le script PowerShell suivant exécute un script et collecte ses résultats dans un tableau spécifié. Ce script suppose qu’un script T-SQL qui renvoie un seul jeu de résultats a été créé et qu’une cible de la collection personnalisée de la base de données a été créée.

Ce script utilise l’applet de commande [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Définir les paramètres de script, les informations d’identification et cible d’exécution :

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Pour créer et démarrer un travail pour les scénarios de collecte de données

Ce script utilise l’applet de commande [**Start-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx) .
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Pour planifier un déclencheur de l’exécution du travail

Le script PowerShell suivant peut être utilisé pour créer une planification récurrente. Ce script utilise un intervalle en minutes, mais le [**Nouveau-AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) prend également en charge les paramètres - DayInterval et - HourInterval, - MonthInterval, - WeekInterval. Les tableaux qui s’exécuter qu’une seule fois peuvent être créés en transmettant - unique.

Créer un nouveau tableau :

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Pour déclencher une tâche s’exécutée selon une planification

Un déclencheur de tâche peut être défini pour un travail à exécuter en fonction d’un calendrier. Le script PowerShell suivant peut être utilisé pour créer un déclencheur de la tâche.

Utilisez [New-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) et de définir les variables suivantes pour correspondre à la tâche de votre choix et de la planification :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Pour supprimer une association planifiée pour arrêter la tâche à partir de l’exécution de la planification

Pour interrompre l’exécution du travail récurrents via un déclencheur de la tâche, le déclencheur de tâche peut être supprimé. Supprimer un déclencheur de tâche pour arrêter une tâche à partir d’en cours d’exécution en fonction d’une planification à l’aide de l' [**applet de commande Remove-AzureSqlJobTrigger**](https://msdn.microsoft.com/library/mt346070.aspx).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Récupérer des déclencheurs de tâche liées à un calendrier

Le script PowerShell suivant peut être utilisé pour obtenir et afficher les déclencheurs de tâche enregistrés pour un calendrier particulier.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Pour récupérer des déclencheurs de tâche liée à un projet 

Pour obtenir et afficher des tableaux contenant un projet enregistré, utilisez [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) .

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Pour créer une application de couche données (DACPAC) pour l’exécution sur plusieurs bases de données

Pour créer un DACPAC, consultez [applications de couche données](https://msdn.microsoft.com/library/ee210546.aspx). Pour déployer un DACPAC, utilisez l' [applet de commande New-AzureSqlJobContent](https://msdn.microsoft.com/library/mt346085.aspx). Le DACPAC doit être accessible au service. Il est recommandé de télécharger un DACPAC créé au stockage Azure et créer une [Signature de l’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) pour le DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Mise à jour d’une application de couche données (DACPAC) pour l’exécution sur plusieurs bases de données

DACPACs existants enregistrés dans élastique travaux de base de données peuvent être mis à jour pour pointer vers le nouvel URI. Mettre à jour l’URI DACPAC sur un DACPAC enregistré existant, utilisez l' [**applet de commande Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) :

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Pour créer un travail pour appliquer une application de couche données (DACPAC) sur les bases de données

Après qu’un DACPAC a été créé dans les travaux de base de données élastique, une tâche peut être créée pour appliquer la DACPAC dans un groupe de bases de données. Le script PowerShell suivant peut être utilisé pour créer un travail DACPAC au sein d’une collection personnalisée de bases de données :

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
