<properties
    pageTitle="Créer votre première fabrique de données (reste) | Microsoft Azure"
    description="Dans ce didacticiel, vous créez un tuyau d’Azure Data Factory exemple à l’aide des API REST de fabrique de données."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Didacticiel : Créer votre première fabrique de données Azure à l’aide des API REST de fabrique de données
> [AZURE.SELECTOR]
- [Vue d’ensemble et des conditions préalables](data-factory-build-your-first-pipeline.md)
- [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modèle de gestionnaire de ressources](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Dans cet article, l’API REST de fabrique de données vous permet de créer votre première fabrique de données Azure.

## <a name="prerequisites"></a>Conditions préalables
- Lisez l’article de [Vue d’ensemble](data-factory-build-your-first-pipeline.md) et suivez les étapes de la **condition préalable** .
- Installer le [coin](https://curl.haxx.se/dlwiz/) sur votre ordinateur. L’outil OURLÉE avec commandes REST vous permet de créer une fabrique de données. 
- Suivez les instructions [cet article](../resource-group-create-service-principal-portal.md) pour : 
    1. Créer une application Web nommée **ADFGetStartedApp** dans Azure Active Directory.
    2. Obtenir **l’ID de client** et de la **clé secrète**. 
    3. Obtenir **l’ID de client**. 
    4. Affecter le rôle de **Collaborateur de fabrique de données** de l’application **ADFGetStartedApp** .  
- Installer [PowerShell Azure](../powershell-install-configure.md).  
- Lancer **PowerShell** et exécutez la commande suivante. Conserver Azure PowerShell ouverte jusqu'à la fin de ce didacticiel. Si vous fermez et ouvrez de nouveau, vous devez réexécuter les commandes.
    1. **Connexion-AzureRmAccount** et entrez le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter au portail Azure.  
    2. Exécutez **Get-AzureRmSubscription** pour afficher tous les abonnements pour ce compte.
    3. Exécutez **Get-AzureRmSubscription - SubscriptionName NameOfAzureSubscription | Ensemble-AzureRmContext** pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **NameOfAzureSubscription** par le nom de votre abonnement Azure. 
3. Créer un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante dans la PowerShell :  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Certaines des étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé ADFTutorialResourceGroup. Si vous utilisez un groupe de ressources différent, vous devez utiliser le nom de votre groupe de ressources à la place de ADFTutorialResourceGroup dans ce didacticiel.

## <a name="create-json-definitions"></a>Créer des définitions de JSON
Créer des fichiers JSON dans le dossier où se trouve le curl.exe suivants. 

### <a name="datafactoryjson"></a>DataFactory.JSON 
> [AZURE.IMPORTANT] Nom doit être globalement unique, il est conseillé de préfixe/suffixe ADFCopyTutorialDF pour en faire un nom unique. 

    {  
        "name": "FirstDataFactoryREST",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.JSON
> [AZURE.IMPORTANT] Remplacez le **nom de compte** et de **accountkey** avec le nom et la clé de votre compte de stockage Azure. Pour savoir comment obtenir votre clé d’accès de stockage, reportez-vous à la section [Affichage, de copie et de touches d’accès de stockage régénérer](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }


### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.JSON

    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.2",
                "clusterSize": 1,
                "timeToLive": "00:30:00",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }

Le tableau suivant fournit des descriptions pour les propriétés JSON utilisées dans l’extrait de code :

| Propriété | Description |
| :------- | :---------- |
| Version | Spécifie que la version de la HDInsight créée pour 3.2. | 
| ClusterSize | Taille du cluster HDInsight. | 
| Propriété TimeToLive | Spécifie que le temps d’inactivité pour le cluster HDInsight, avant d’être supprimé. |
| linkedServiceName | Spécifie le compte de stockage qui est utilisé pour stocker les journaux qui sont générés par HDInsight |

Notez les points suivants : 

- Le Factory de données crée un cluster **Windows** HDInsight avec le JSON ci-dessus. Vous pourriez également y créer un cluster **Linux** HDInsight. Pour plus d’informations, reportez-vous à la section [Service de lié à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
- Vous pouvez utiliser **votre propre cluster HDInsight** au lieu d’utiliser un cluster d’HDInsight à la demande. Pour plus d’informations, reportez-vous à la section [HDInsight des services liés](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
- Le cluster HDInsight crée un **conteneur par défaut** dans le stockage blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est voulu par la conception. Avec le service de HDInsight liée à la demande, un HDInsight de cluster est créé chaque fois qu’une tranche est traitée, sauf s’il existe un live cluster (**la propriété timeToLive**) et est supprimé lorsque le traitement est terminé.

    Comme plusieurs sections sont traitées, vous voyez de nombreux conteneurs dans le stockage blob Azure. Si vous ne devez pas les pour des tâches de dépannage, vous souhaiterez les supprimer afin de réduire le coût de stockage. Les noms de ces conteneurs obéissent à un modèle : « chargeur automatique de documents**yourdatafactoryname**-**linkedservicename**- datetimestamp ». Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer les conteneurs dans le stockage blob Azure.

Pour plus d’informations, reportez-vous à la section [Service de lié à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 

### <a name="inputdatasetjson"></a>inputdataset.JSON

    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


Le JSON définit un groupe de données nommé **AzureBlobInput**, qui représente les données d’entrée pour une activité dans le pipeline. En outre, elle spécifie que les données d’entrée se trouve dans le conteneur de l’objet blob appelé **adfgetstarted** et le dossier appelé **inputdata**.

Le tableau suivant fournit des descriptions pour les propriétés JSON utilisées dans l’extrait de code :

| Propriété | Description |
| :------- | :---------- |
| type de | La propriété type a la valeur AzureBlob dans la mesure où les données résident dans le stockage blob Azure. |  
| linkedServiceName | fait référence à la StorageLinkedService que vous avez créé précédemment. |
| nom de fichier | Cette propriété est facultative. Si vous ne spécifiez pas cette propriété, tous les fichiers à partir de la folderPath sont prélevés. Dans ce cas, seulement la input.log est traitée. |
| type de | Les fichiers journaux sont au format texte, afin que l’utilisation de TextFormat. | 
| columnDelimiter | colonnes dans les fichiers journaux sont délimitées par une virgule () |
| fréquence/intervalle | la valeur de mois et de l’intervalle de fréquence est de 1, ce qui signifie que les tranches d’entrée sont disponibles, tous les mois. | 
| externe | Cette propriété a la valeur True si les données d’entrée ne sont pas générées par le service Data Factory. | 

### <a name="outputdatasetjson"></a>outputdataset.JSON

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }

Le JSON définit un groupe de données nommé **AzureBlobOutput**, qui représente des données de sortie d’une activité dans le pipeline. En outre, il indique que les résultats sont stockés dans le conteneur de l’objet blob appelé **adfgetstarted** et le dossier appelé **partitioneddata**. La section **disponibilité** indique que le dataset de sortie est produit sur une base mensuelle.

### <a name="pipelinejson"></a>pipeline.JSON
> [AZURE.IMPORTANT] Remplacez **storageaccountname** par le nom de votre compte de stockage Azure. 


    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [{
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [{
                    "name": "AzureBlobInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }],
            "start": "2016-07-10T00:00:00Z",
            "end": "2016-07-11T00:00:00Z",
            "isPaused": false
        }
    }

Dans l’extrait JSON, vous créez un pipeline qui se compose d’une seule activité qui utilise la ruche pour traiter des données sur un cluster HDInsight.

Le fichier de script de ruche, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par la scriptLinkedService, appelée **StorageLinkedService**) et dans le dossier de **scripts** dans conteneur **adfgetstarted**.

La section **définit** spécifie les paramètres d’exécution qui sont transmis au script ruche en tant que valeurs de configuration de ruche (par exemple : ${hiveconf : inputtable}, ${hiveconf:partitionedtable}).

Les propriétés **start** et **end** du pipeline spécifie la période active du pipeline.

Dans l’activité JSON, vous spécifiez que le script de la ruche s’exécute sur l’ordinateur spécifié par le **linkedServiceName** - **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Pour plus d’informations sur les propriétés JSON utilisées dans l’exemple précédent, consultez [Anatomie d’un Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) . 

## <a name="set-global-variables"></a>Définir des variables globales

Dans Azure PowerShell, exécutez les commandes suivantes après avoir remplacé les valeurs avec votre propre :

> [AZURE.IMPORTANT] Reportez-vous à la section [conditions requises](#prerequisites) pour obtenir des instructions sur l’obtention d’ID client, secret client, client ID et ID d’abonnement.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "FirstDataFactoryREST"



## <a name="authenticate-with-aad"></a>Authentifier avec DAS

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 



## <a name="create-data-factory"></a>Création de la fabrique de données

Dans cette étape, vous créez une fabrique de données Azure nommé **FirstDataFactoryREST**. Une fabrique de données peut avoir un ou plusieurs tuyaux. Un tuyau peut contenir une ou plusieurs activités. Par exemple, une activité de copie pour copier des données provenant d’une source vers un magasin de données de destination et une activité de ruche de HDInsight pour exécuter le script de ruche pour transformer les données. Exécutez les commandes suivantes pour créer le factory de données : 

1. Affecter la commande variable nommée **cmd**. 

    Vérifiez que le nom de la fabrique de données vous spécifiez ici (ADFCopyTutorialDF) correspond au nom spécifié dans le **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Permet d’afficher les résultats. Si l’usine de données a été créée avec succès, vous voyez le JSON pour le factory de données dans les **résultats**; dans le cas contraire, vous voyez le message d’erreur.  

        Write-Host $results

Notez les points suivants :
 
- Le nom de la fabrique de données Azure doit être globalement unique. Si le message d’erreur dans les résultats : **nom d’usine de données « FirstDataFactoryREST » n’est pas disponible**, procédez comme suit :  
    1. Modifier le nom (par exemple, yournameFirstDataFactoryREST) dans le fichier **datafactory.json** . Voir la rubrique [Data Factory - règles d’appellation](data-factory-naming-rules.md) pour les règles d’affectation de noms pour les artefacts de données usine.
    2. Dans la première commande où la variable **$cmd** est affectée à une valeur, remplacez FirstDataFactoryREST par le nouveau nom, exécutez la commande. 
    3. Exécutez les deux commandes pour appeler l’API REST pour la création de la fabrique de données et imprimer les résultats de l’opération. 
- Pour créer des instances de la fabrique de données, vous devez être un administrateur de collaborateur/de l’abonnement Azure
- Le nom de la fabrique de données peut être enregistré sous la forme d’un nom DNS à l’avenir et, par conséquent, devenir visible publiquement.
- Si vous recevez l’erreur : «**cet abonnement n’est pas enregistré pour utiliser l’espace de noms Microsoft.DataFactory**», effectuez l’une des opérations suivantes et recommencez la publication : 

    - Dans Azure PowerShell, exécutez la commande suivante pour enregistrer le fournisseur de données usine : 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Vous pouvez exécuter la commande suivante pour confirmer que la fabrique de données fournisseur est enregistré : 
    
            Get-AzureRmResourceProvider
    - Connexion à l’aide de l’abonnement Azure dans le [portail Azure](https://portal.azure.com) et accédez à une lame Data Factory (ou) créer une fabrique de données dans le portail Azure. Cette action enregistre automatiquement le fournisseur pour vous.

Avant de créer un tuyau, vous devez d’abord créer quelques entités de Data Factory. Tout d’abord, vous créez des services liés pour lier des banques de données/calcule à votre magasin de données, de définir l’entrée et de sortie des groupes de données pour représenter les données dans des magasins de données liées. 

## <a name="create-linked-services"></a>Créer des services liés 
Dans cette étape, vous liez votre compte de stockage Azure et un cluster d’Azure HDInsight à la demande à votre usine de données. Le compte de stockage Azure conserve les données d’entrée et de sortie pour le pipeline dans cet exemple. Le service HDInsight lié est utilisé pour exécuter le script de ruche spécifié dans l’activité du pipeline dans cet exemple. 

### <a name="create-azure-storage-linked-service"></a>Créer le service de stockage Azure lié
Dans cette étape, vous liez votre compte de stockage Azure à votre usine de données. Avec ce didacticiel, vous utilisez le même compte de stockage Azure pour stocker les données d’entrée/sortie et le fichier de script HQL.

1. Affecter la commande variable nommée **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Permet d’afficher les résultats. Si le service lié a été créé avec succès, vous voyez le JSON pour le service lié dans les **résultats**; dans le cas contraire, vous voyez le message d’erreur.
  
        Write-Host $results

### <a name="create-azure-hdinsight-linked-service"></a>Créer le service de HDInsight d’Azure lié
Dans cette étape, vous liez un cluster d’HDInsight à la demande à votre usine de données. Le cluster HDInsight est automatiquement créé lors de l’exécution et supprimé une fois ceci traitement et inactif le laps de temps spécifié. Vous pouvez utiliser votre propre cluster HDInsight au lieu d’utiliser un cluster d’HDInsight à la demande. Pour plus d’informations, consultez [Calculer les Services liés](data-factory-compute-linked-services.md) .  

1. Affecter la commande variable nommée **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Permet d’afficher les résultats. Si le service lié a été créé avec succès, vous voyez le JSON pour le service lié dans les **résultats**; dans le cas contraire, vous voyez le message d’erreur.  

        Write-Host $results

## <a name="create-datasets"></a>Créer des groupes de données
Dans cette étape, vous créez des groupes de données pour représenter l’entrée et la sortie des données pour le traitement de la ruche. Ces groupes de données, voir la **StorageLinkedService** que vous avez créé précédemment dans ce didacticiel. Les points de service lié à un compte de stockage Azure et les groupes de données spécifient les conteneur, dossier, nom de fichier dans le stockage qui contient l’entrée et de sortie des données.   

### <a name="create-input-dataset"></a>Créer le groupe de données d’entrée
Dans cette étape, vous créez le groupe de données d’entrée pour représenter les données d’entrée, stockées dans le stockage Blob d’Azure.

1. Affecter la commande variable nommée **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Permet d’afficher les résultats. Si le groupe de données a été créée avec succès, vous voyez le JSON pour le groupe de données dans les **résultats**; dans le cas contraire, vous voyez le message d’erreur.
  
        Write-Host $results
### <a name="create-output-dataset"></a>Créer le dataset de sortie
Dans cette étape, vous créez le dataset de sortie pour représenter les données de sortie stockées dans le stockage Blob d’Azure.

1. Affecter la commande variable nommée **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Permet d’afficher les résultats. Si le groupe de données a été créée avec succès, vous voyez le JSON pour le groupe de données dans les **résultats**; dans le cas contraire, vous voyez le message d’erreur.
  
        Write-Host $results 

## <a name="create-pipeline"></a>Créer des opportunités
Dans cette étape, vous créez votre premier tuyau avec une activité **HDInsightHive** . Tranche d’entrée est disponible chaque mois (fréquence : mois, intervalle : 1), la tranche de sortie est généré tous les mois, et la propriété du Planificateur de l’activité est également définie sur mensuel. Les paramètres pour le dataset de sortie et le Planificateur de l’activité doivent correspondre. Actuellement, dataset de sortie est un élément moteur la planification, vous devez créer un groupe de données de sortie même si l’activité ne produit pas de sortie. Si l’activité n’accepte aucune entrée, vous pouvez ignorer la création du groupe de données d’entrée.  

Vérifiez que vous consultez le fichier **input.log** dans le dossier **adfgetstarted/inputdata** dans le stockage blob Azure et exécutez la commande suivante pour déployer le pipeline. Étant donné que les heures de **début** et de **fin** sont définies dans le passé et **isPaused** est définie sur false, le pipeline (activité dans le pipeline) s’exécute immédiatement après le déploiement. 

1. Affecter la commande variable nommée **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Permet d’afficher les résultats. Si le groupe de données a été créée avec succès, vous voyez le JSON pour le groupe de données dans les **résultats**; dans le cas contraire, vous voyez le message d’erreur.  

        Write-Host $results
5. Félicitations, vous avez créé votre premier tuyau à l’aide de PowerShell d’Azure.

## <a name="monitor-pipeline"></a>Pipeline de moniteur
Dans cette étape, API REST de fabrique de données vous permet d’analyser les tranches qui est produits par le pipeline.

    $ds ="AzureBlobOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;

    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }


> [AZURE.IMPORTANT] 
> Création d’un cluster d’HDInsight de la demande dure un certain temps (environ 20 minutes). Par conséquent, prévoyez le tuyau à prendre **environ 30 minutes** pour traiter la tranche.  

Exécutez la commande Invoke- et la suivante jusqu'à ce que vous voyiez la tranche dans l’état **prêt** ou de l’état **d’Échec** . Lorsque la tranche est dans l’état prêt, vérifiez le dossier **partitioneddata** dans le conteneur **adfgetstarted** votre stockage blob pour les données de sortie.  La création d’un cluster d’HDInsight à la demande prend généralement un certain temps.

![données de sortie](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] Le fichier d’entrée est supprimé lorsque la tranche est traitée avec succès. Par conséquent, si vous souhaitez réexécuter la tranche ou ce didacticiel à nouveau, vous pouvez télécharger le fichier d’entrée (input.log) dans le dossier inputdata du conteneur adfgetstarted.

Vous pouvez également utiliser Azure portal tranches de surveiller et de résoudre les problèmes. Voir détails de [surveiller des pipelines à l’aide d’Azure portal](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline) .  

## <a name="summary"></a>Résumé 
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour traiter les données en exécutant le script de la ruche sur un cluster d’hadoop HDInsight. Vous avez utilisé l’éditeur de la fabrique de données dans le portail Azure pour effectuer les opérations suivantes :  

1.  Permet de créer une **fabrique de données**d' Azure.
2.  Permet de créer deux **services liés**:
    1.  Service de **Stockage azure** lié pour lier votre stockage blob Azure qui contient les fichiers d’entrée/sortie sur le factory de données.
    2.  Service de lié à la demande **HDInsight d’Azure** pour lier un cluster d’HDInsight Hadoop à la demande sur le factory de données. Azure Data Factory crée un HDInsight Hadoop cluster juste-à-temps pour traiter les données d’entrée et de produire des données de sortie. 
3.  Créer deux **groupes de données**, qui décrivent les données d’entrée et de sortie pour l’activité de la ruche de HDInsight dans le pipeline. 
4.  Créer un **pipeline** avec une activité de **Ruche de HDInsight** . 

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une transformation activité (HDInsight) qui exécute un script de la ruche sur un cluster d’Azure HDInsight à la demande. Pour savoir comment utiliser une activité de copie pour copier des données à partir d’un Blob Azure dans Azure SQL, reportez-vous à la section [didacticiel : copier des données à partir d’un Blob Azure dans Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Référence des API reste données en usine](https://msdn.microsoft.com/library/azure/dn906738.aspx) |  Reportez-vous à la documentation complète sur les applets de commande Data Factory |
| [Activités de Transformation des données](data-factory-data-transformation-activities.md) | Cet article fournit une liste d’activités de transformation de données (tels que la transformation de la ruche de HDInsight vous avez utilisé dans ce didacticiel) pris en charge par l’usine de données Azure. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory et comment les utiliser pour construire de bout en bout orientées données des flux de travail pour votre scénario ou une entreprise. |
| [Groupes de données](data-factory-create-datasets.md) | Cet article vous aide à comprendre les groupes de données dans Azure Data Factory.
| [Surveiller et gérer des Pipelines à l’aide de lames de portail Azure](data-factory-monitor-manage-pipelines.md) | Cet article décrit comment faire pour contrôler, gérer et déboguer vos pipelines à l’aide de lames de portail Azure. |
| [Surveiller et gérer les pipelines à l’aide de la surveillance de l’application](data-factory-monitor-manage-app.md) | Cet article décrit comment faire pour contrôler, gérer et déboguer des pipelines à l’aide du contrôle et application de gestion. 

