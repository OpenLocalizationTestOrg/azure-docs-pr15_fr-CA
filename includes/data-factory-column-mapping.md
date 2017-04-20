## <a name="column-mapping-with-translator-rules"></a>Mappage de colonnes avec les règles de conversion
Mappage de colonne peut être utilisé pour spécifier la manière dont les colonnes sont spécifiées dans la « structure » de mappage de table source aux colonnes spécifiée dans la « structure » de la table de récepteur. La propriété **columnMapping** est disponible dans la section **typeProperties** de l’activité de copie.

Mappage de la colonne prend en charge les scénarios suivants :

- Toutes les colonnes de la table source « structure » correspondent à toutes les colonnes de la table de récepteur « structure ».
- Un sous-ensemble des colonnes de la table source « structure » correspondent à toutes les colonnes de la table de récepteur « structure ».

Les éléments suivants sont les conditions d’erreur et provoque une exception :

- Moins de colonnes ou plus dans la « structure » de la table de récepteur que spécifié dans le mappage de colonnes.
- Mappage en double.
- Résultat de la requête SQL n’a pas un nom de colonne spécifié dans le mappage.

## <a name="column-mapping-samples"></a>Exemples de mappage de colonne
> [AZURE.NOTE] Les exemples ci-dessous concernent les SQL Azure et d’Azure Blob mais sont applicables à n’importe quel magasin de données qui prend en charge les groupes de données rectangulaire. Vous devrez ajuster le groupe de données et les définitions de service liés dans les exemples ci-dessous pour pointer vers des données dans la source de données correspondante. 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemple 1 – colonne mappage d’Azure SQL au blob Azure
Dans cet exemple, la table d’entrée dispose d’une structure et qu’il pointe vers une table SQL dans une base de données SQL d’Azure.

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Dans cet exemple, la table de sortie a une structure et qu’il pointe vers un objet blob dans un stockage blob Azure.

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

Vous trouverez ci-dessous le JSON pour l’activité. Colonnes de la source mappés aux colonnes de récepteur (**columnMappings**) à l’aide du **traducteur de** propriété.

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**Flux de mappage de colonne :**

![Flux de mappage de colonne](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemple 2 – avec une requête SQL à partir d’Azure SQL Azure blob de mappage de colonne
Dans cet exemple, une requête SQL est utilisée pour extraire des données d’Azure SQL au lieu de simplement spécifier le nom de la table et les noms de colonne dans la section « structure ». 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

Dans ce cas, les résultats de la requête sont tout d’abord mappées aux colonnes spécifiées dans la « structure » de la source. Ensuite, les colonnes de la source « structure » sont mappés aux colonnes de récepteur « structure » avec les règles spécifiées dans columnMappings.  Supposons que la requête renvoie 5 colonnes, deux colonnes supplémentaires, puis celles spécifiées dans la « structure » de la source.

**Flux de mappage de colonne**

![Flux de mappage de colonne-2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)







