<properties
    pageTitle="Qu’est-il advenu de mon projet 5 de ASP.NET (Visual Studio connecté services) | Stockage de Microsoft Azure"
    description="Décrit ce qui se passe après que la connexion à un compte de stockage Azure dans un projet Visual Studio 5 d’ASP.NET à l’aide de Visual Studio de services connectés"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Qu’est-il advenu de mon projet 5 de ASP.NET (Visual Studio Azure Storage connecté services) ?

## <a name="references-added"></a>Références ajoutées

Le package NuGet de stockage Azure a été ajouté à votre projet Visual Studio.  
Ce package ajoute les références .NET suivants :

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

En outre, le package NuGet **Microsoft.Framework.Configuration.Json** a été ajouté.

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour le stockage Azure ajouté
Dans le fichier config.json de votre projet, un élément a été créé avec la chaîne de connexion et la clé du compte de stockage sélectionné.

Pour plus d’informations, reportez-vous à la section [5 de ASP.NET](http://www.asp.net/vnext).
