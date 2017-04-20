<properties
    pageTitle="Utilisez le stockage Azure dans les applications Windows Store | Microsoft Azure"
    description="Apprenez à créer une application du Windows Store qui utilise le stockage Blob d’Azure, file d’attente, un tableau ou fichier."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Comment faire pour utiliser le stockage Azure dans les applications Windows Store

## <a name="overview"></a>Vue d’ensemble

Ce guide indique comment se familiariser avec le développement d’une application du Windows Store qui utilise le stockage Azure.

## <a name="download-required-tools"></a>Téléchargez les outils requis

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) vous permet de facilement générer, déboguer, localiser, un package, et déployer des applications du Windows Store. Visual Studio 2012 ou une version ultérieure est requis.
- La [Bibliothèque cliente de stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage) fournit une bibliothèque de classes Windows Runtime pour travailler avec le stockage Azure.
- [WCF données Services outils pour Windows applications Store](http://www.microsoft.com/download/details.aspx?id=30714) étend l’expérience d’ajouter une référence de Service avec prise en charge de OData côté client pour les applications du Windows Store dans Visual Studio.

## <a name="develop-apps"></a>Développer des applications

### <a name="getting-ready"></a>Préparation

Créez un nouveau projet d’application Windows Store dans Visual Studio 2012, ou une version ultérieure :

![banque-apps-stockage-vs-projet][store-apps-storage-vs-project]

Ensuite, ajoutez une référence à la bibliothèque cliente du stockage Azure en cliquant sur les **références**, en cliquant sur **Ajouter une référence**et en accédant au stockage Client Library pour Windows Runtime que vous avez téléchargé :

![banque-apps-stockage-choisissez-bibliothèque][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>À l’aide de la bibliothèque avec les services de file d’attente et de Blob

À ce stade, votre application est prête à appeler les services de file d’attente et les Blob Azure. Ajoutez les instructions **using** suivantes afin que les types de stockage Azure peuvent être référencés directement :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Ensuite, ajoutez un bouton à votre page. Ajoutez le code suivant à son événement **Click** et modifier votre méthode de gestionnaire d’événements en utilisant le [mot clé async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Ce code suppose que vous disposez des variables de chaîne de deux, *nom de compte* et *accountKey*. Ils représentent le nom de votre compte de stockage et de la clé de compte qui est associée à ce compte.

Générez et exécutez l’application. En cliquant sur le bouton pour vérifier l’existence d’un conteneur nommé *Conteneur1* dans votre compte et ensuite de le créer dans le cas contraire.

### <a name="using-the-library-with-the-table-service"></a>À l’aide de la bibliothèque avec le service de la Table

Les types qui sont utilisés pour communiquer avec le service de la Table d’Azure dépendent des Services de données WCF pour la bibliothèque d’applications Windows Store. Ensuite, ajoutez une référence aux bibliothèques WCF requis à l’aide de la Console du Gestionnaire de package :

![banque-apps-stockage--Gestionnaire de package][store-apps-storage-package-manager]

Utilisez la commande suivante pour le Gestionnaire de package de point à l’emplacement sur votre ordinateur :

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Cette commande ajoute automatiquement toutes les références nécessaires à votre projet. Si vous ne souhaitez pas utiliser la Console du Gestionnaire de package, vous pouvez ajouter le dossier NuGet de Services de données WCF sur votre ordinateur local à la liste des sources de package et puis d’ajouter la référence par le biais de l’interface utilisateur, comme décrit dans la [Gestion de NuGet Packages à l’aide de la boîte de dialogue](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Lorsque vous avez référencé le package NuGet de Services de données WCF, modifiez le code dans l’événement **Click** de votre bouton :

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Ce code vérifie si une table nommée *table1* existe dans votre compte et crée alors dans le cas contraire.

Vous pouvez également ajouter une référence à Microsoft.WindowsAzure.Storage.Table.dll, qui est disponible dans le même package que vous avez téléchargé. Cette bibliothèque contient des fonctionnalités supplémentaires, telles que la sérialisation basée sur la réflexion et de requêtes génériques. Notez que cette bibliothèque ne prend pas en charge JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
