<properties 
   pageTitle="Mise en route de magasins de LAC de données Azure à l’aide d’Azure SDK pour Node.js | Microsoft Azure"
   description="Apprenez à utiliser les Node.js pour travailler avec les comptes de la banque de données LAC et le système de fichiers." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Mise en route de magasin de LAC de données Azure à l’aide d’Azure SDK pour Node.js

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)


Découvrez comment le SDK Azure pour Node.js permet de créer un compte Azure données lac banque et effectuer des opérations de base telles que la création de dossiers, charger et téléchargement des fichiers de données, supprimez votre compte, l’etc.. Pour plus d’informations sur la banque de données lac, consultez [Vue d’ensemble de la banque données lac](data-lake-store-overview.md). Actuellement, le Kit de développement logiciel prend en charge

  *  **Version de Node.js : 0.10.0 ou supérieur**
  *  **Version de l’API REST pour compte : 2015-10-01-aperçu**
  *  **Version de l’API du reste de système de fichiers : 2015-10-01-aperçu**

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Créer une Application Azure Active Directory**. L’application Azure annonce vous permet d’authentifier l’application magasin de LAC des données avec Azure AD. Il existe différentes approches pour authentifier avec AD Azure, qui sont **l’authentification de l’utilisateur final** ou **service-service**. Pour plus d’informations sur l’authentification et les instructions, voir [authentifier avec le magasin de données LAC avec Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Comment faire pour installer

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Authentification à l’aide d’Azure Active Directory

Les extraits de code ci-dessous montrent deux façons distinctes de l’authentification avec le magasin de données lac utilise Active Directory Azure. Pour plus d’informations sur les différentes méthodes à utiliser pour l’authentification avec le magasin de données lac, consultez [authentifier avec le magasin de données LAC avec Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

L’extrait de code ci-dessous requiert également des entrées comme nom de domaine AD Azure, ID de client pour une publicité Azure app, des etc.. Tous ces détails peuvent être récupérés d’une annonce Azure application vous devez créée, lesquels sont également inclus dans le lien ci-dessus.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Créer des Clients de la banque de données lac

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Créer un compte de banque de données lac

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
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

## <a name="create-a-file-with-content"></a>Créer un fichier avec un contenu
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Obtenir la liste des fichiers et des dossiers

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Voir aussi

- [Kit de développement logiciel Microsoft Azure pour Node.js](https://github.com/azure/azure-sdk-for-node)
- [Kit de développement logiciel Microsoft Azure pour Node.js - gestion lac Analytique des données](https://www.npmjs.com/package/azure-arm-datalake-analytics)
