<properties
    pageTitle="Qu’est-il arrivé à mon projet ASP.NET ? | Microsoft Azure | Visual Studio de services connectés"
    description="Décrit ce qui se produit après que l’ajout de stockage Azure à un projet ASP.NET à l’aide de Visual Studio de services connectés"
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

# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il advenu de mon projet ASP.NET (Visual Studio Azure Storage connecté le service) ?

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

##<a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour le stockage Azure ajouté
Dans le fichier web.config de votre projet, un élément a été créé avec la chaîne de connexion et la clé du compte de stockage sélectionné.

Pour plus d’informations, consultez [ASP.NET](http://www.asp.net).
