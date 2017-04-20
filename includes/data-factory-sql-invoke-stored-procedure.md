## <a name="invoking-stored-procedure-for-sql-sink"></a>L’appel de procédure stockée pour récepteur de SQL

Lors de la copie de données dans SQL Server ou la base de données de Azure SQL/SQL Server, un utilisateur spécifié à une procédure stockée peut être configurée et appelée avec des paramètres supplémentaires. 

Une procédure stockée peut être utilisée lorsque les mécanismes de copie intégrée ne servent pas à l’objectif. Cela est généralement exploitée lors d’un traitement supplémentaire (fusion de colonnes, recherchez les valeurs supplémentaires, insertion dans plusieurs tables...) doivent être effectués avant l’insertion finale de la source de données dans la table de destination. 

Vous devez appeler une procédure stockée de choix. L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une simple insertion dans une table de la base de données. 

**Dataset de sortie**

Dans cet exemple, le type est défini sur : SqlServerTable. Affectez-lui la valeur AzureSqlTable à utiliser avec une base de données SQL d’Azure. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Définissez la section SqlSink dans l’activité de copie JSON comme suit. Pour appeler une procédure stockée lors d’insérer des données, les propriétés à la fois SqlWriterStoredProcedureName et SqlWriterTableType sont nécessaires.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

Dans votre base de données, définissez la procédure stockée portant le même nom en tant que SqlWriterStoredProcedureName. Il gère les données d’entrée à partir de votre source spécifié et les insérer dans la table de sortie. Notez que le nom du paramètre de la procédure stockée doit être le même que le paramètre tableName défini dans le fichier de tableau JSON.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

Dans votre base de données, définissez le type de table portant le même nom en tant que SqlWriterTableType. Notez que le schéma du type de tableau doit être identique à celle du schéma retourné par vos données d’entrée.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

La fonction de procédure stockée tire parti des [Paramètres de la table](https://msdn.microsoft.com/library/bb675163.aspx).