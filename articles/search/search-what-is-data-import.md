<properties
    pageTitle="Téléchargement des données de recherche d’Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Apprenez à télécharger des données à un index de recherche d’Azure."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>Télécharger des données à la recherche d’Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTE](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>Modèles de recherche Azure du téléchargement des données
Il existe deux manières de remplir votre index de recherche d’Azure avec vos données. La première option repousse manuellement vos données dans l’index à l’aide de la recherche d’Azure [API REST](search-import-data-rest-api.md) ou le [Kit de développement .NET](search-import-data-dotnet.md). La seconde option consiste à [pointer une source de données pris en charge](search-indexer-overview.md) à votre index de recherche d’Azure et laisser Azure recherche automatiquement extraire vos données dans le service de recherche.

Ce guide couvre uniquement les instructions sur l’utilisation de la technique push de téléchargement de données (ce qui est pris en charge uniquement dans les [API REST](search-import-data-rest-api.md) et le [Kit de développement .NET](search-import-data-dotnet.md)), mais vous pouvez toujours plus d’informations sur le modèle d’extraction.

### <a name="push-data-to-an-index"></a>Envoyer des données pour un index

Cette approche fait référence à l’envoi par programmation des données à la recherche d’Azure pour le rendre disponible pour la recherche. Pour les applications présentant des exigences de latence très faible (par exemple, si vous devez rechercher des opérations pour être synchronisées avec les bases de données de stock dynamique), le modèle push est la seule option.

Vous pouvez utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou le [Kit de développement .NET](search-import-data-dotnet.md) pour envoyer des données à un index. Il n’existe actuellement aucune prise en charge de l’outil de diffusion de données via le portail.

Cette approche est plus flexible que le modèle d’extraction dans la mesure où vous pouvez télécharger des documents individuellement ou par lots (jusqu'à 1000 par lot ou 16 Mo, la limite se produit en premier). Le modèle push vous permet également de télécharger des documents à la recherche d’Azure indépendamment de ces données.

### <a name="pull-data-into-an-index"></a>Extraire des données dans un index

Le modèle d’extraction, analyse une source de données pris en charge et télécharge automatiquement les données dans l’index de recherche d’Azure vous, pour vous. Suivi des modifications et des suppressions à des documents existants en plus de la reconnaissance d’un nouveau document, indexeurs supprimer la nécessité de gérer activement les données dans l’index.

Recherche d’Azure, cette capacité est implémentée via *les indexeurs*, actuellement disponible pour [le stockage des objets Blob (aperçu)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [base de données Azure SQL et SQL Server sur Azure VM](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

La fonctionnalité de l’indexeur est exposée dans le [Portail Azure](search-import-data-portal.md) ainsi que dans l' [API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).
