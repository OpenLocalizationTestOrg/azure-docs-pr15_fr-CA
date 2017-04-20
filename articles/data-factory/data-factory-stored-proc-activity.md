<properties 
    pageTitle="SQL Server stockées activité de procédure" 
    description="Découvrez comment vous pouvez utiliser l’activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données de SQL Azure ou un magasin de données SQL Azure à partir d’un pipeline de données usine." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="spelluru"/>

# <a name="sql-server-stored-procedure-activity"></a>SQL Server stockées activité de procédure
> [AZURE.SELECTOR]
[La ruche](data-factory-hive-activity.md)  
[Porcs](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop en flux continu](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[La procédure stockée](data-factory-stored-proc-activity.md)
[Données lac Analytique U-SQL](data-factory-usql-activity.md)
[.NET personnalisés](data-factory-use-custom-activities.md)

Vous pouvez utiliser l’activité de la procédure stockée SQL Server dans un [pipeline](data-factory-create-pipelines.md) de données usine pour appeler une procédure stockée dans un des magasins de données suivants : 


- Base de données SQL Azure 
- Entrepôt de données SQL Azure  
- Base de données de SQL Server dans votre entreprise ou une machine virtuelle d’Azure. Vous devez installer la passerelle de gestion des données sur le même ordinateur qui héberge la base de données ou sur un ordinateur distinct afin d’éviter la concurrence pour les ressources de la base de données. Passerelle de gestion des données est un logiciel qui se connecte sur site hébergés dans Azure VM pour les services en nuage de façon sécurisée et gérée des sources de données et sources de données. Article de [déplacer des données entre des locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) voir pour plus d’informations sur la passerelle de gestion des données. 

Cet article s’appuie sur l’article [des activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="walkthrough"></a>Procédure pas à pas

### <a name="sample-table-and-stored-procedure"></a>Exemple de table et de procédure stockée
1. Créer le suivant **table** dans votre base de données de SQL Azure en utilisant SQL Server Management Studio ou tout autre outil que vous maîtrisez. La colonne datetimestamp est la date et l’heure à laquelle l’ID correspondant est générée. 

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    ID est l’unique identifié et que la colonne datetimestamp est la date et l’heure à laquelle l’ID correspondant est générée.
    ![Exemples de données](./media/data-factory-stored-proc-activity/sample-data.png)

    > [AZURE.NOTE] Cet exemple utilise la base de données de SQL Azure, mais fonctionne de la même manière pour l’entrepôt de données SQL Azure et la base de données de SQL Server. 
2. Créer le suivant **une procédure stockée** qui insère des données dans à l' **sampletable**.

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS
        
        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

    > [AZURE.IMPORTANT] **Nom** et la **casse** du paramètre (DateTime dans cet exemple) doivent correspondre à celle du paramètre spécifié dans la pipeline/activité JSON. Dans la définition de la procédure stockée, vérifiez que **@** est utilisé comme préfixe pour le paramètre.
    
### <a name="create-a-data-factory"></a>Créer une fabrique de données  
4. Connectez-vous au [portail Azure](https://portal.azure.com/). 
5. Cliquez sur **Nouveau** dans le menu de gauche et cliquez sur **Intelligence + Analytique**, cliquez sur le **Factory de données**.
    
    ![Nouvelle fabrique de données](media/data-factory-stored-proc-activity/new-data-factory.png)   
4.  Dans la **nouvelle fabrique de données** lame, entrez **SProcDF** pour le nom. Nom d’usine de données Azure est **globalement uniques**. Vous devez faire précéder le nom de la fabrique de données avec votre nom, pour permettre la création de la fabrique.

    ![Nouvelle fabrique de données](media/data-factory-stored-proc-activity/new-data-factory-blade.png)      
3.  Sélectionnez votre **abonnement Azure**. 
4.  Pour le **Groupe de ressources**, effectuez l’une des étapes suivantes : 
    1.  Cliquez sur **Créer nouveau** et entrez un nom pour le groupe de ressources.
    2.  Cliquez sur **utiliser existant** , sélectionnez un groupe de ressources existant.  
5.  Sélectionnez l' **emplacement** de la fabrique de données.
6.  Sélectionnez **Ajouter au tableau de bord** afin que vous puissiez voir le factory de données sur le tableau de bord prochaine dans qu'ouverture de session. 
6.  Sur la lame de **nouvelle fabrique de données** , cliquez sur **créer** .
6.  Vous consultez le factory de données en cours de création dans le **tableau de bord** du portail Azure. Après que le factory de données a été créé avec succès, vous consultez la page données en usine, qui indique le contenu de la fabrique de données.
    ![Page d’accueil de fabrique de données](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Création d’un service SQL Azure lié  
Après la création de la fabrique de données, vous créez un service SQL Azure lié qui lie votre base de données de SQL Azure sur le factory de données. Cette base de données contient la sampletable table et sp_sample de procédure stockée.

7.  Cliquez sur **auteur et déployer** sur la lame **Data Factory** pour **SProcDF** lancer l’éditeur de la fabrique de données.
2.  Cliquez sur **nouveau magasin de données** de la barre de commandes et choisissez **Base de données de SQL Azure**. Vous devez voir le script JSON pour la création d’un service SQL Azure lié dans l’éditeur. 

    ![Nouveau magasin de données](media/data-factory-stored-proc-activity/new-data-store.png)
4. Dans le script JSON, apportez les modifications suivantes : 
    1. Remplacer ** &lt;nom_serveur&gt; ** avec le nom de votre serveur de base de données de SQL Azure.
    2. Remplacer ** &lt;databasename&gt; ** avec la base de données dans lequel vous avez créé la table et la procédure stockée.
    3. Remplacer ** &lt; username@servername ** avec le compte d’utilisateur qui a accès à la base de données.
    4. Remplacer ** &lt;mot de passe&gt; ** avec le mot de passe pour le compte d’utilisateur. 

    ![Nouveau magasin de données](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
5. Dans la barre de commande pour déployer le service lié, cliquez sur **déployer** . Vérifiez que vous consultez le AzureSqlLinkedService dans l’arborescence sur la gauche. 

    ![arborescence avec des services liés](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Créer un groupe de données de sortie
6. Cliquez sur **... Plus** sur la barre d’outils, cliquez sur **Nouveau groupe de données**, cliquez sur **Les SQL Azure**. **Nouveau groupe de données** dans la barre de commandes et sélectionnez **Les SQL Azure**.

    ![arborescence avec des services liés](media/data-factory-stored-proc-activity/new-dataset.png)
7. Copiez-collez le script suivant JSON dans l’éditeur de JSON.

        {               
            "name": "sprocsampleout",
            "properties": {
                "type": "AzureSqlTable",
                "linkedServiceName": "AzureSqlLinkedService",
                "typeProperties": {
                    "tableName": "sampletable"
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }
7. Dans la barre de commande pour déployer le groupe de données, cliquez sur **déployer** . Vérifiez que vous consultez le groupe de données dans l’arborescence. 

    ![arborescence avec des services liés](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Créer un pipeline ayant une activité SqlServerStoredProcedure
Maintenant, nous allons créer un pipeline avec une activité SqlServerStoredProcedure.
 
9. Cliquez sur **... Plus** sur la commande de la barre et cliquez sur le **nouveau pipeline**. 
9. Copiez-collez l’extrait suivant de JSON. **StoredProcedureName** la valeur **sp_sample**. Nom et la casse du paramètre **DateTime** doivent correspondre au nom et la casse du paramètre dans la définition de la procédure stockée.  

        {
            "name": "SprocActivitySamplePipeline",
            "properties": {
                "activities": [
                    {
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
                            "storedProcedureName": "sp_sample",
                            "storedProcedureParameters": {
                                "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                            }
                        },
                        "outputs": [
                            {
                                "name": "sprocsampleout"
                            }
                        ],
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "SprocActivitySample"
                    }
                ],
                "start": "2016-08-02T00:00:00Z",
                "end": "2016-08-02T05:00:00Z",
                "isPaused": false
            }
        }

    Si vous avez besoin de passer null pour un paramètre, utilisez la syntaxe : « param1 » : null (tout en minuscules). 
9. Dans la barre d’outils pour déployer le pipeline, cliquez sur **déployer** .  

### <a name="monitor-the-pipeline"></a>Surveiller le pipeline

6. Cliquez sur **X** pour fermer l’éditeur de données usine lames et revenir à la lame de la fabrique de données, puis cliquez sur **diagramme**.

    ![mosaïque de diagramme](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
7. Dans la **Vue Affichage des diagrammes**, vous voyez une vue d’ensemble des tuyaux et des datasets utilisés dans ce didacticiel. 

    ![mosaïque de diagramme](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
8. Dans l’affichage des tâches, double-cliquez sur le groupe de données **sprocsampleout**. Vous consultez les tranches dans l’état prêt. Il convient de cinq tranches car une tranche est produite pour chaque heure entre l’heure de début et de fin dans le JSON.

    ![mosaïque de diagramme](media/data-factory-stored-proc-activity/data-factory-slices.png) 
10. Lorsqu’une tranche est dans l’état **prêt** , exécutez un * *Sélectionnez* à partir de sampletable** requête sur la base de données Azure SQL pour vérifier que les données a été insérées dans la table par la procédure stockée.

    ![Données de sortie](./media/data-factory-stored-proc-activity/output.png)

    Pour plus d’informations sur la surveillance des pipelines d’Azure Data Factory, voir [Moniteur du pipeline](data-factory-monitor-manage-pipelines.md) .  

> [AZURE.NOTE] Dans cet exemple, la SprocActivitySample a pas d’entrées. Si vous souhaitez chaîner cette activité à une activité en amont (c'est-à-dire de traitement préalable), les sorties de l’activité en amont peuvent être utilisées comme entrées dans cette activité. Dans ce cas, cette activité ne s’exécute pas tant que l’activité en amont est terminée et les sorties des activités en amont sont disponibles (dans un état « prêt »). Les entrées ne peuvent pas être utilisées directement en tant que paramètre à l’activité de procédure stockée

## <a name="json-format"></a>Format JSON
    {
        "name": "SQLSPROCActivity",
        "description": "description", 
        "type": "SqlServerStoredProcedure",
        "inputs":  [ { "name": "inputtable"  } ],
        "outputs":  [ { "name": "outputtable" } ],
        "typeProperties":
        {
            "storedProcedureName": "<name of the stored procedure>",
            "storedProcedureParameters":  
            {
                "param1": "param1Value"
                …
            }
        }
    }

## <a name="json-properties"></a>Propriétés JSON

Propriété | Description | Obligatoire
-------- | ----------- | --------
nom | Nom de l’activité | Oui
Description | Texte décrivant l’activité qui est utilisée pour | N°
type de | SqlServerStoredProcedure | Oui
entrées | Facultatif. Si vous ne spécifiez pas un groupe de données d’entrée, il doit être disponible (dans l’état « Prêt ») pour l’activité de la procédure stockée à exécuter. Le groupe de données d’entrée ne peut pas être consommé dans la procédure stockée en tant que paramètre. Il sert uniquement à vérifier la dépendance avant de démarrer l’activité de la procédure stockée. | N°
sorties | Vous devez spécifier un groupe de données de sortie d’une activité de procédure stockée. Dataset de sortie spécifie la **planification** de l’activité de la procédure stockée (horaire, hebdomadaire, mensuel, etc.). <br/><br/>Le dataset de sortie doit utiliser un **service lié** qui fait référence à une base de données de SQL Azure ou d’un entrepôt de données de SQL Azure ou d’une base de données de SQL Server dans lequel vous souhaitez que la procédure stockée à exécuter. <br/><br/>Le dataset de sortie peut être un moyen de passer le résultat de la procédure stockée pour ultérieure par une autre activité ([activités de chaînage](data-factory-scheduling-and-execution.md#chaining-activities)) dans le pipeline de traitement. Toutefois, Data Factory n’écrit pas automatiquement de la sortie d’une procédure stockée à ce dataset. Il s’agit de la procédure stockée qui écrit dans une table SQL qui désigne le groupe de données de sortie. <br/><br/>Dans certains cas, le dataset de sortie peut être un **groupe de données fictive**, qui est utilisée uniquement pour spécifier le plan d’exécution de l’activité de la procédure stockée. | Oui
storedProcedureName | Spécifiez le nom de la procédure stockée dans la base de données de Azure SQL ou d’un entrepôt de données SQL Azure qui est représenté par le service lié par la table de sortie. | Oui
storedProcedureParameters | Spécifiez des valeurs pour les paramètres de procédure stockée. Si vous avez besoin de passer null pour un paramètre, utilisez la syntaxe : « param1 » : null (minuscules). Consultez l’exemple suivant pour en savoir plus sur l’utilisation de cette propriété.| N°

## <a name="passing-a-static-value"></a>Passage d’une valeur statique 
Maintenant, prenons l’exemple d’ajout d’une autre colonne nommée 'Un scénario' dans la table qui contient une valeur statique appelée « Exemple de Document ».

![Exemple de données 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
    
    AS
    
    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

Passez maintenant, le paramètre de scénario et de la valeur de l’activité de la procédure stockée. La section typeProperties dans l’exemple précédent se présente comme l’extrait de code suivant :

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": 
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }

