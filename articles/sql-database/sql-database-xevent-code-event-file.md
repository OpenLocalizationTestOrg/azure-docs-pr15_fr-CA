<properties 
    pageTitle="Code de base de données SQL XEvent événement fichier | Microsoft Azure" 
    description="Pour obtenir un exemple de code en deux phases qui montre la cible du fichier de l’événement dans un événement étendue sur la base de données de SQL Azure, fournit PowerShell et Transact-SQL. Le stockage Azure est une partie obligatoire de ce scénario." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Code de cible de fichier événement pour les événements étendus dans la base de données de SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Vous souhaitez un exemple de code complet pour un moyen fiable aux informations de capture et de rapport pour un événement étendue.


Dans de Microsoft SQL Server, le [fichier d’événements cible](http://msdn.microsoft.com/library/ff878115.aspx) permet de stocker des sorties d’événement dans un fichier de disque dur local. Mais ces fichiers ne sont pas disponibles pour la base de données de SQL Azure. Au lieu de cela, nous utilisent le service de stockage Azure pour prendre en charge de la cible du fichier d’événements.


Cette rubrique présente un exemple de code en deux phases :


- PowerShell, pour créer un conteneur de stockage Azure dans le nuage.

- Transact-SQL :
 - Pour affecter le conteneur de stockage Azure à une cible du fichier d’événements.
 - Pour créer et démarrer la session d’événements et ainsi de suite.


## <a name="prerequisites"></a>Conditions préalables


- Un compte Azure et abonnement. Vous pouvez vous inscrire pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).


- Toute base de données que vous pouvez créer une table dans.
 - Éventuellement vous pouvez [créer une base de données de démonstration **AdventureWorksLT** ](sql-database-get-started.md) en quelques minutes.


- SQL Server Management Studio (ssms.exe), idéalement sa dernière version mise à jour mensuelle. Vous pouvez télécharger la dernière ssms.exe à partir de :
 - Rubrique de [Téléchargement de SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Un lien direct vers le téléchargement.](http://go.microsoft.com/fwlink/?linkid=616025)


- Vous devez avoir les [modules Azure PowerShell](http://go.microsoft.com/?linkid=9811175) installé.
 - Les modules fournissent des commandes telles que - **New-AzureStorageAccount**.


## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Phase 1 : Code PowerShell conteneur de stockage Azure


Cette PowerShell est la phase 1 de l’exemple de code en deux phases.

Le script commence avec les commandes pour nettoyer après une éventuelle précédente est rerunnable.



1. Coller le script PowerShell dans un simple éditeur de texte tel que Notepad.exe, puis enregistrez le script sous la forme d’un fichier avec l' extension **.ps1**.

2. Démarrer PowerShell ISE en tant qu’administrateur.

3. À l’invite, tapez<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>et appuyez sur ENTRÉE.

4. Dans PowerShell ISE, ouvrez votre fichier **.ps1** . Exécutez le script.

5. Tout d’abord, le script démarre une nouvelle fenêtre dans laquelle vous vous connectez à Azure.
 - Si vous exécutez à nouveau le script sans interrompre votre session, vous avez la possibilité de pratique de commenter la commande **Add-AzureAccount** .


![PowerShell ISE, avec module Azure installé et prêt à exécuter le script.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Notez les valeurs nommées suivantes qui le script PowerShell imprime lorsqu’elle se termine. Vous devez modifier ces valeurs dans le script Transact-SQL qui suit la phase 2.


## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>La phase 2 : Code de Transact-SQL qui utilise le conteneur de stockage Azure


- Dans la phase 1 de cet exemple de code, vous avez exécuté un script PowerShell pour créer un conteneur de stockage Azure.
- Ensuite dans la phase 2, le script Transact-SQL suivant doit utiliser le conteneur.


Le script commence avec les commandes pour nettoyer après une éventuelle précédente est rerunnable.


Le script PowerShell imprimé quelques valeurs nommées lorsqu’elle est terminée. Vous devez modifier le script Transact-SQL pour utiliser ces valeurs. Rechercher **TODO** dans le script Transact-SQL pour rechercher les points d’édition.


1. Ouvrez SQL Server Management Studio (ssms.exe).

2. Se connecter à votre base de données de la base de données de SQL Azure.

3. Cliquez pour ouvrir une nouvelle fenêtre de requête.

4. Collez le script Transact-SQL suivant dans le volet de requête.

5. Rechercher chaque **TODO** dans le script et effectuez les modifications appropriées.

6. Enregistrez et exécutez ensuite le script.


&nbsp;


> [AZURE.WARNING] La valeur de clé des associations de sécurité générée par le script PowerShell précédent peut commencer par un ' ?' (point d’interrogation). Lorsque vous utilisez la clé SAS dans le script T-SQL suivant, vous devez *Supprimer l’interligne '?'*. Dans le cas contraire, vos efforts peuvent être bloqués par la sécurité.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Si la cible ne parvient pas à joindre lors de l’exécution, vous devez arrêter et redémarrer la session de l’événement :


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## <a name="output"></a>Sortie


Lorsque le script Transact-SQL est terminée, cliquez sur une cellule sous l’en-tête de la colonne **event_data_XML** . Un **<event>** élément s’affiche et montre une instruction UPDATE.

Voici une **<event>** élément qui a été généré au cours du test :


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;


Le script Transact-SQL précédent permet de lire les event_file les fonctions système suivantes :

- [Sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)


Une explication des options avancées pour l’affichage des données d’événements étendus est disponible à l’adresse :

- [Fonctions avancées de consultation des données de cible à partir d’événements étendus](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Conversion de l’exemple de code pour s’exécuter sur SQL Server


Supposons que vous souhaitiez exécuter l’exemple précédent de Transact-SQL de Microsoft SQL Server.


- Pour plus de simplicité, vous pouvez remplacer complètement l’utilisation du conteneur de stockage Azure avec un simple fichier tel que **C:\myeventdata.xel**. Le fichier doit être écrite sur le disque dur local de l’ordinateur qui héberge SQL Server.


- Vous n’avez pas de n’importe quel type d’instructions de Transact-SQL pour **créer la clé principale** et de **Créer les informations d’identification**.


- Dans l’instruction **CREATE EVENT SESSION** , dans sa clause **Ajouter une cible** , vous remplacez la valeur Http assignée à **filename =** avec une chaîne de chemin d’accès complet comme **C:\myfile.xel**.
 - Aucun compte de stockage Azure ne doit être impliqué.


## <a name="more-information"></a>Plus d’informations


Pour plus d’informations sur les comptes et les conteneurs dans le service de stockage Azure, voir :

- [Comment faire pour utiliser le stockage Blob à partir de .NET](../storage/storage-dotnet-how-to-use-blobs.md)
- [Dénomination et faisant référence à des conteneurs, des objets BLOB et métadonnées](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Travailler avec le conteneur racine](http://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Leçon 1 : Créer une stratégie d’accès stockée et une signature d’accès partagé dans un conteneur d’Azure](http://msdn.microsoft.com/library/dn466430.aspx)
    - [Leçon 2 : Créer les informations d’identification SQL Server à l’aide d’une signature d’accès partagé](http://msdn.microsoft.com/library/dn466435.aspx)




<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

