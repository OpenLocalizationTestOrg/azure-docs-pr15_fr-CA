<properties
    pageTitle="Didacticiel d’usine de données : premier pipeline de données | Microsoft Azure"
    description="Ce didacticiel Azure Data Factory vous montre comment créer et planifier une fabrique de données qui traite des données à l’aide du script de la ruche sur un cluster Hadoop."
    services="data-factory"
    keywords="données Azure usine (didacticiel), hadoop cluster, hadoop ruche"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>Didacticiel : Créer votre premier pipeline pour traiter des données à l’aide d’Hadoop cluster 
> [AZURE.SELECTOR]
- [Vue d’ensemble et des conditions préalables](data-factory-build-your-first-pipeline.md)
- [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modèle de gestionnaire de ressources](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Dans ce didacticiel, vous générez votre première fabrique de données Azure avec un pipeline de données qui traite les données en exécutant le script de la ruche sur un cluster Azure HDInsight (Hadoop). 

> [AZURE.NOTE] Cet article ne fournit pas une vue d’ensemble conceptuelle de la fabrique de données Azure. Pour une vue d’ensemble conceptuelle du service, consultez [Introduction à l’usine de données Azure](data-factory-introduction.md). Reportez-vous à la section [Data Factory cursus](https://azure.microsoft.com/documentation/learning-paths/data-factory/) pour une méthode recommandée pour naviguer dans notre contenu pour en savoir plus sur le Factory de données.

## <a name="whats-covered-in-this-tutorial"></a>Sujets traités dans ce didacticiel ? 
**Azure Data Factory** vous permet de composer des tâches de **traitement** de données et de **déplacement** des données comme les flux de travail pilotés par les données (également appelés pipelines de données). Vous apprenez à créer votre première activité de tuyau avec un traitement de données (ou de la transformation de données) des données. Cette activité utilise un cluster HDInsight Hadoop pour transformer et d’analyser les journaux web d’exemple.  

Dans ce didacticiel, vous effectuez les opérations suivantes :

1.  Créer une **fabrique de données**. Une fabrique de données peut contenir un ou ces données de processus et de déplacer les tuyaux davantage de données. 
2.  Créer des **services liés**. Vous créez un service lié à la liaison d’un magasin de données ou un service de calcul sur le factory de données. Un magasin de données, tels que le stockage Azure conserve les données d’entrée/sortie d’activités dans le pipeline. Un service de calcul comme HDInsight Hadoop cluster processus/transformations de données.    
3.  Créez d’entrée et de sortie **des groupes de données**. Un groupe de données d’entrée représente l’entrée d’une activité dans le pipeline et la sortie d’un groupe de données représente la sortie de l’activité.
3.  Créer le **pipeline**. Un tuyau peut avoir une ou plusieurs activités (exemples : activité de copie, d’activité de la ruche HDInsight). Cet exemple utilise l’activité HDInsight ruche qui exécute un script de la ruche sur un cluster HDInsight Hadoop. Le script crée d’abord une table qui fait référence aux données de journal web brutes stockées dans le stockage blob Azure et les données brutes des partitions puis par année et mois.

    Il existe deux types d’activités prises en charge par l’usine de données Azure. Ils sont : [activités de déplacement des données](data-factory-data-movement-activities.md) et des [activités de transformation des données](data-factory-data-transformation-activities.md). Il existe des données qu’un seul mouvement activité, l’activité de copie. Dans ce didacticiel, vous n’utilisez pas l’activité de copie. Pour un didacticiel sur l’utilisation de l’activité de la copie, voir [didacticiel : copier des données d’un Azure blob pour Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Activité HDInsight ruche que vous utilisez dans ce didacticiel est une des activités de transformation de données pris en charge par Data Factory.  
 
Voici la **vue schéma** de la fabrique de données exemple que vous générez dans ce didacticiel. 

![Affichage de diagramme dans le didacticiel de Data Factory](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

Dans ce didacticiel, le dossier **inputdata** du conteneur blob Azure **adfgetstarted** contient un fichier nommé input.log. Ce fichier journal comporte des entrées à partir de trois mois : janvier, février et mars de 2016. Voici les exemples de lignes pour chaque mois dans le fichier d’entrée. 

    2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Lorsque le fichier est traité par le pipeline d’activité de la ruche HDInsight, l’activité s’exécute un script de la ruche sur le cluster HDInsight que les partitions d’entrée de données par année et mois. Le script crée trois dossiers de sortie qui contient un fichier avec des entrées de chaque mois.  

    adfgetstarted/partitioneddata/year=2016/month=1/000000_0
    adfgetstarted/partitioneddata/year=2016/month=2/000000_0
    adfgetstarted/partitioneddata/year=2016/month=3/000000_0

À partir des lignes de l’exemple ci-dessus, la première condition (avec 2016-01-01) est écrite dans le fichier 000000_0 dans le mois = 1 dossier. De même, l’autre est écrite dans le fichier au cours du mois = 2 dossier et le troisième est écrite dans le fichier du mois = dossier 3.  


## <a name="pre-requisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des connaissances suivantes :

1.  **Abonnement azure** - si vous n’avez pas un abonnement Azure, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Reportez-vous à l’article [Gratuit d’évaluation](https://azure.microsoft.com/pricing/free-trial/) sur comment vous pouvez obtenir un compte d’essai gratuit.

2.  **Le stockage azure** , vous utilisez un compte de stockage Azure pour stocker les données dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) . Après avoir créé le compte de stockage, notez le **nom de compte** et une **touche d’accès rapide**. Reportez-vous à la section [Affichage, de copie et de touches d’accès de stockage régénérer](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys). 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>Télécharger des fichiers vers le stockage Azure pour le didacticiel
Avant de commencer le didacticiel, vous devez préparer votre compte de stockage Azure avec les fichiers d’exemple pour le didacticiel.

1. Télécharger le fichier de requête de ruche (HQL) pour le dossier de **scripts** du conteneur blob **adfgetstarted** .
2. Télécharger le fichier d’entrée vers le dossier **inputdata** du conteneur blob **adfgetstarted** . 

#### <a name="create-hql-script-file"></a>Créer le fichier de script HQL 

1. Lancez **le bloc-notes** et collez le script HQL suivant. Ce script de ruche crée deux tables : **WebLogsRaw** et **WebLogsPartitioned**. Cliquez sur **fichier** dans le menu et sélectionnez **Enregistrer sous**. Basculez vers le dossier **C:\adfgetstarted** sur votre disque dur. Sélectionnez * *tous les fichiers (*.*) **pour la** Enregistrer en tant que** de type champ. Entrez **partitionweblogs.hql** de le **le nom de fichier**. Vérifiez que le **le** champ au bas de la boîte de dialogue est défini sur **ANSI**de codage. Si non, la valeur **ANSI **.  

        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw

Lors de l’exécution, l’activité de la ruche dans le pipeline de l’usine de données passe les valeurs la **inputtable** et **partitionedtable** les paramètres comme indiqué dans l’extrait suivant :  

        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

Le **storageaccountname** est le nom de votre compte de stockage Azure.
 
#### <a name="create-a-sample-input-file"></a>Créer un exemple de fichier d’entrée
En utilisant le bloc-notes, créez un fichier nommé **input.log** dans la **c:\adfgetstarted** avec le contenu suivant : 

    #Software: Microsoft Internet Information Services 8.0
    #Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>Téléchargement des fichiers d’entrée et HQL à votre stockage de Blob Azure

Cette section fournit des instructions sur l’utilisation d’outil de **AzCopy** pour copier les fichiers input.log et partitionweblogs.hql pour le stockage des objets Blob Azure. Vous pouvez utiliser n’importe quel outil de votre choix (par exemple : [Explorateur de stockage Microsoft Azure](http://storageexplorer.com/), [CloudXPlorer par le logiciel ClumsyLeaf](http://clumsyleaf.com/products/cloudxplorer) pour effectuer cette tâche.   
     
1. Télécharger la [version la plus récente de **AzCopy**](http://aka.ms/downloadazcopy)ou la [dernière version d’aperçu](http://aka.ms/downloadazcopypr). Reportez-vous à l’article [comment utiliser AzCopy](../storage/storage-use-azcopy.md) pour obtenir des instructions sur l’utilisation de l’utilitaire.
2. Naviguez jusqu’au dossier c:\adfgetstarted et exécutez la commande suivante : 

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

    Cette commande télécharge le fichier **input.log** vers le compte de stockage (conteneur de**adfgetstarted** et **inputdata** dossier). Remplacez **storageaccountname** par le nom de votre compte de stockage et **storageaccesskey** avec la clé d’accès de stockage.

    > [AZURE.NOTE] Cette commande crée un conteneur nommé **adfgetstarted** dans le stockage Azure Blob et copie le fichier **input.log** à partir de votre disque local dans le dossier **inputdata** dans le conteneur. 
    
3. Une fois que le fichier a été téléchargé avec succès, vous voyez la sortie similaire à celle qui suit à partir de AzCopy.
    
        Finished 1 of total 1 file(s).
        [2015/12/16 23:07:33] Transfer summary:
        -----------------
        Total files transferred: 1
        Transfer successfully:   1
        Transfer skipped:        0
        Transfer failed:         0
        Elapsed time:            00.00:00:01
5. Exécutez la commande suivante pour télécharger le fichier **partitionweblogs.hql** dans le dossier **scripts** du conteneur **adfgetstarted** . Voici la commande : 
    
        AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

Vous avez terminé la configuration requise. Vous pouvez créer une fabrique de données à l’aide d’une des manières suivantes. Cliquez sur les onglets en haut ou sur les liens suivants pour exécuter le didacticiel. 

- [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modèle de gestionnaire de ressources](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)