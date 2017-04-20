<properties
    pageTitle="Qu’est-il arrivé à mon projet de service cloud ? | Microsoft Azure | Visual Studio de services connectés"
    description="Décrit ce qui se passe dans un projet de services cloud après que la connexion à un compte de stockage Azure à l’aide de Visual Studio de services connectés"
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

# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il advenu de mon projet de services cloud (Visual Studio Azure Storage connecté le service) ?

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

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour le stockage Azure ajouté
Éléments ont été créés avec la chaîne de connexion et la clé du compte de stockage sélectionné. Les modifications ont été apportées aux fichiers suivants :

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**
