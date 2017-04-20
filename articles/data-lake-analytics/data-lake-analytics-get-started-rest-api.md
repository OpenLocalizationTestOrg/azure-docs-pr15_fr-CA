<properties 
   pageTitle="Mise en route de données Analytique de LAC à l’aide des API REST | Microsoft Azure" 
   description="WebHDFS autres API permet d’effectuer des opérations sur les données lac Analytique" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Mise en route d’Analytique de LAC de données Azure à l’aide des API de repos

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Apprenez à utiliser des WebHDFS reste les API et données lac Analytique reste à gérer les comptes données lac Analytique, de travaux et de catalogue. 

## <a name="prerequisites"></a>Conditions préalables

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Créer une Application Azure Active Directory**. L’application Azure annonce vous permet d’authentifier l’application Analytique du lac données avec Azure AD. Il existe différentes approches pour authentifier avec AD Azure, qui sont **l’authentification de l’utilisateur final** ou **service-service**. Pour plus d’informations sur l’authentification et les instructions, voir [authentification avec Analytique de LAC de données à l’aide d’Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

- [coin](http://curl.haxx.se/). Cet article utilise le roulage pour montrer comment effectuer des appels d’API REST contre un compte Analytique lac de données.

## <a name="authenticate-with-azure-active-directory"></a>Authentifier avec Azure Active Directory

Il existe deux méthodes d’authentification avec Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Authentification de l’utilisateur final (interactive)

À l’aide de cette méthode, application invite l’utilisateur à ouvrir une session et toutes les opérations sont effectuées dans le contexte de l’utilisateur. 

Suivez ces étapes pour l’authentification interactive :

1. Par l’intermédiaire de votre application, rediriger l’utilisateur vers l’URL suivante :

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<URI de redirection > doit être codée pour une utilisation dans une URL. Ainsi, pour https://localhost, utilisez `https%3A%2F%2Flocalhost`)

    Pour ce didacticiel, vous pouvez remplacer les valeurs d’espace réservé dans l’URL ci-dessus et collez-le dans la barre d’adresses d’un navigateur web. Vous allez être redirigé pour s’authentifier à l’aide de votre nom de connexion Azure. Une fois correctement vous connecter, la réponse est affichée dans la barre d’adresse du navigateur. La réponse sera au format suivant :
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capturer le code d’autorisation de la réponse. Pour ce didacticiel, vous pouvez copier le code d’autorisation dans la barre d’adresse du navigateur web et passez dans la demande POST sur le point de terminaison de jeton, comme indiqué ci-dessous :

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] Dans ce cas, le \<redirection-URI > ne doivent pas être codé.

3. La réponse est un objet JSON qui contient un jeton d’accès (par exemple, `"access_token": "<ACCESS_TOKEN>"`) et un jeton d’actualisation (par exemple, `"refresh_token": "<REFRESH_TOKEN>"`). Votre application utilise le jeton d’accès lors de l’accès le lac Azure Data Store et le jeton d’actualisation pour obtenir un autre jeton d’accès lors de l’expiration d’un jeton d’accès.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Lorsque le jeton d’accès arrive à expiration, vous pouvez demander un nouveau jeton d’accès en utilisant le jeton d’actualisation, comme indiqué ci-dessous :

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Pour plus d’informations sur l’authentification de l’utilisateur interactif, consultez [code d’autorisation accorder des flux](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Authentification de service-service (non interactif)

À l’aide de cette méthode, application fournit ses propres informations d’identification pour effectuer les opérations. Pour ce faire, vous devez émettre une demande POST, similaire à celui illustré ci-dessous : 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Le résultat de cette requête inclut un jeton d’autorisation (indiqué par `access-token` dans la sortie ci-dessous) qui vous passe par la suite à vos appels d’API REST. Enregistrer ce jeton d’authentification dans un fichier texte ; vous aurez besoin plus loin dans cet article.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Cet article utilise la **non interactif** approche. Pour plus d’informations sur la non interactif (appels de service-service), reportez-vous à la section [Service aux appels de service à l’aide des informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).
## <a name="create-a-data-lake-analytics-account"></a>Créer un compte Analytique lac de données

Avant de pouvoir créer un compte Analytique lac de données, vous devez créer un groupe de ressources d’Azure et d’un compte de banque de données lac.  Voir [Création d’un compte de banque de données lac](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

La commande Curl suivante montre comment créer un compte :

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID d’abonnement, \< `AzureResourceGroupName` \> avec un nom de groupe de ressources Azure existant, et \< `NewAzureDataLakeAnalyticsAccountName` \> avec un nouveau nom de compte de données lac Analytique. La charge utile de demande de cette commande est contenue dans le fichier **CreateDatalakeAnalyticsAccountRequest.json** qui est fourni pour le `-d` paramètre ci-dessus. Le contenu du fichier input.json de ressembler à ceci :

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Comptes d’Analytique de LAC des données de liste dans un abonnement

La commande Curl suivante montre comment répertorier les comptes dans un abonnement :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID d’abonnement. La sortie est similaire à :

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obtenir des informations sur un compte Analytique lac de données

La commande Curl suivante montre comment obtenir les informations de compte :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID d’abonnement, \< `AzureResourceGroupName` \> avec un nom de groupe de ressources Azure existant, et \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte Analytique de LAC données existant. La sortie est similaire à :

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Liste des magasins de LAC de données d’un compte Analytique lac de données

La commande Curl suivante montre comment afficher la liste des magasins de données lac d’un compte :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID d’abonnement, \< `AzureResourceGroupName` \> avec un nom de groupe de ressources Azure existant, et \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte Analytique de LAC données existant. La sortie est similaire à :

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Soumettre des travaux d’U-SQL

La commande Curl suivante montre comment soumettre un travail U-SQL :

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte Analytique de LAC données existant. La charge utile de demande de cette commande est contenue dans le fichier **SubmitADLAJob.json** qui est fourni pour le `-d` paramètre ci-dessus. Le contenu du fichier input.json de ressembler à ceci :

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

La sortie est similaire à :

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Répertorier les tâches SQL-U

La commande Curl suivante montre comment répertorier les travaux U-SQL :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, et \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte Analytique de LAC données existant. 


La sortie est similaire à :

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Obtenir des éléments de catalogue

La commande Curl suivante montre comment obtenir les bases de données à partir du catalogue :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

La sortie est similaire à :

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Voir aussi

- Pour voir une requête plus complexe, consultez [les journaux de site Web d’analyse à l’aide d’Azure données lac Analytique](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications de U-SQL, consultez [scripts de développer U-SQL à l’aide des outils de LAC de données pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour en savoir plus U-SQL, consultez [mise en route du lac de données Azure Analytique U-SQL language](data-lake-analytics-u-sql-get-started.md).
- Pour les tâches de gestion, voir [gestion de LAC Azure données Analytique à l’aide d’Azure portal](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de données lac Analytique, consultez [vue d’ensemble de l’Analytique de LAC de données Azure](data-lake-analytics-overview.md).
- Pour consulter le didacticiel même à l’aide d’autres outils, cliquez sur les sélecteurs de l’onglet en haut de la page.
- Pour enregistrer des informations de diagnostic, consultez [accès à des journaux de diagnostics pour Azure données lac Analytique](data-lake-analytics-diagnostic-logs.md)
