<properties
   pageTitle="Gérer l’Analytique de LAC de données Azure à l’aide d’Azure SDK pour Node.js | Azure"
   description="Apprenez à gérer les comptes de données lac Analytique, les sources de données, les travaux et les utilisateurs à l’aide d’Azure SDK pour Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gérer l’Analytique de LAC de données Azure à l’aide d’Azure SDK pour Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Le Kit de développement Azure pour Node.js peut être utilisé pour la gestion des comptes, les travaux et les catalogues Azure données lac Analytique. Pour afficher la rubrique de gestion à l’aide d’autres outils, cliquez sur l’onglet, sélectionnez ci-dessus.

Pour le moment, il prend en charge :

  *  **Version de Node.js : 0.10.0 ou supérieur**
  *  **Version de l’API REST pour compte : 2015-10-01-aperçu**
  *  **Version de l’API REST pour catalogue : 2015-10-01-aperçu**
  *  **Version des API REST pour tâche : 2016-03-20-aperçu**

## <a name="features"></a>Fonctionnalités

- Gestion des comptes : créer, obtenir, liste, update et delete.
- Gestion du travail : envoyer, obtenir, liste, Annuler.
- Gestion du catalogue : obtenir, de liste, de créer (secrets), de (secrets) de mettre à jour, de supprimer (secrets).

## <a name="how-to-install"></a>Comment faire pour installer

```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Authentification à l’aide d’Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Création du client de données lac Analytique

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Créer un compte Analytique lac de données

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Obtenir la liste des tâches

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Obtenir la liste des bases de données dans le catalogue Analytique du lac données
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Voir aussi

- [Kit de développement logiciel Microsoft Azure pour Node.js](https://github.com/azure/azure-sdk-for-node)
- [Kit de développement logiciel Microsoft Azure pour Node.js - gestion du magasin de données lac](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)
