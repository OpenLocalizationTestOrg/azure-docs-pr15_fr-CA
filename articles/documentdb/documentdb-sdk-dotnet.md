<properties 
    pageTitle="L’API .NET DocumentDB et Kit de développement logiciel | Microsoft Azure" 
    description="Pour tout savoir sur les API .NET et le SDK, y compris les dates de commercialisation, les dates de retraite et les modifications apportées entre chaque version du SDK .NET DocumentDB." 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>Les API DocumentDB et kits de développement logiciel 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>SDK et API de .NET DocumentDB

<table>
<tr><td>**Téléchargement du Kit de développement logiciel**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentation sur l’API**</td><td>[Documentation de référence sur l’API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Exemples**</td><td>[Exemples de code .NET](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Mise en route**</td><td>[Mise en route avec le Kit de développement .NET DocumentDB](documentdb-get-started.md)</td></tr>
<tr><td>**Didacticiel d’application Web**</td><td>[Développement d’applications Web avec DocumentDB](documentdb-dotnet-application.md)</td></tr>
<tr><td>**En cours de framework pris en charge**</td><td>[Microsoft.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

> [AZURE.IMPORTANT] À partir de la version de version 1.9.2, vous pouvez recevoir System.NotSupportedException lors de l’interrogation des collections partitionnées. Pour éviter cette erreur, assurez-vous que votre processus hôte 64 bits. Pour les projets de l’exécutables, il est possible en désactivant l’option « Prefer 32 bits » dans la fenêtre Propriétés du projet, sur l’onglet Générer.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - Prise en charge d’ajouté une connectivité directe pour les collections partitionnées.
  - Amélioration des performances pour le niveau de cohérence limitées de son obsolescence.
  - Ajout de polygone et LineString DataTypes tout en spécifiant la collection indexation de stratégie pour gardiennage des recherches spatiales.
  - LINQ prise en charge ajoutée pour StringEnumConverter, IsoDateTimeConverter et UnixDateTimeConverter lors de la conversion des prédicats.
  - Divers correctifs de bogues de kit de développement logiciel.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - Résout un problème qui a provoqué la NotFoundException suivante : la session de lecture n’est pas disponible pour le jeton de session d’entrée. Cette exception s’est produite dans certains cas lors de l’interrogation pour la zone de lecture d’un compte géo-distribués.
  - La propriété ResponseStream dans la classe ResourceResponse, qui permet d’accéder directement dans le flux sous-jacent à partir d’une réponse exposée.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[question 1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Permet de modifier les classes ResourceResponse, FeedResponse, StoredProcedureResponse et MediaResponse pour implémenter l’interface publique correspondante afin qu’ils peuvent être fictive pour test piloté par déploiement (TDD).
  - Résout un problème qui a provoqué un en-tête de clé de partition incorrect lors de l’utilisation d’un objet JsonSerializerSettings personnalisé pour les données de sérialisation.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Résout un problème qui a provoqué l’échec des requêtes de longue durée avec l’erreur : jeton d’autorisation n’est pas valide à l’heure actuelle.
  - Résout un problème qui a supprimé la SqlParameterCollection d’origine à partir d’entre les requêtes de partition haut/order by.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Prise en charge supplémentaire pour des requêtes parallèles pour les collections partitionnées.
  - Prise en charge pour les requêtes ORDER BY et haut de partition pour les collections partitionnées.
  - Fixe les références manquantes au DocumentDB.Spatial.Sql.dll et Microsoft.Azure.Documents.ServiceInterop.dll sont nécessaires lors du référencement d’un projet DocumentDB avec une référence au package DocumentDB Nuget.
  - Fixe la possibilité d’utiliser des paramètres de types différents lors de l’utilisation de fonctions définies par l’utilisateur dans LINQ. 
  - Fixe un bogue pour les comptes global répliqués où Upsert appels ont été incitées à lire d’autres emplacements au lieu de l’emplacement de l’écriture.
  - Méthodes ajoutées à l’interface IDocumentClient qui étaient absents : 
      - Méthode UpsertAttachmentAsync qui accepte mediaStream et options en tant que paramètres
      - Méthode CreateAttachmentAsync qui prend un paramètre options
      - Méthode CreateOfferQuery qui accepte querySpec en tant que paramètre.
  - Dans le cas de classes unsealed publics qui sont exposés dans l’interface IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Ajouter la prise en charge pour les comptes de la base de données de plusieurs régions.
  - Prise en charge supplémentaire pour les tentatives de demandes accélérées.  Utilisateur peut personnaliser le nombre de tentatives et le temps d’attente maximum en configurant la propriété ConnectionPolicy.RetryOptions.
  - Ajouter une nouvelle interface IDocumentClient qui définit les signatures de toutes les propriétés de DocumenClient et de méthodes.  Dans le cadre de cette modification, également modifié les méthodes d’extension qui créer IQueryable et IOrderedQueryable à des méthodes sur la classe DocumentClient.
  - Ajout de l’option de configuration pour définir la ServicePoint.ConnectionLimit pour un point de terminaison DocumentDB donné Uri.  Utilisez ConnectionPolicy.MaxConnectionLimit pour modifier la valeur par défaut, qui est définie à 50.
  - IPartitionResolver déconseillée et sa mise en oeuvre.  Prise en charge de IPartitionResolver est désormais obsolète. Il est recommandé d’utiliser partitionné de Collections de débit et de stockage plus élevée.


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Ajouter qu'une surcharge d’Uri selon la méthode ExecuteStoredProcedureAsync qui accepte RequestOptions en tant que paramètre.
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Temps ajouté pour la prise en charge live (TTL) pour les documents.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Corrige un bogue dans l’emballage de Nuget du Kit de développement .NET d’emballage dans le cadre d’une solution de Service de Cloud Azure.
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Mise en œuvre [des collections partitionnées](documentdb-partition-data.md) et [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[version 1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Fixe]** Interrogation lève de point de terminaison DocumentDB : ' System.Net.Http.HttpRequestException : erreur lors de la copie du contenu d’un flux de données.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - LINQ de développé en charge, y compris les nouveaux opérateurs pour les expressions de paginations, conditionnelles et plage de comparaison.
    - Pour activer le comportement SELECT TOP dans LINQ opérateur Take
    - Opérateur de CompareTo pour permettre les comparaisons de chaîne plage
    - Conditionnel ( ?) et de la fusion d’opérateurs ( ?)
  - **[Fixe]** ArgumentOutOfRangeException lors de la combinaison de projection de modèle avec Where dans les requêtes linq.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Fixe]** Si vous sélectionnez n’est pas la dernière expression du fournisseur LINQ supposé sans projection et produit SELECT * incorrecte.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Mise en œuvre Upsert, méthodes d’ajout de UpsertXXXAsync
 - Améliorations des performances pour toutes les demandes
 - Prise en charge de fournisseur LINQ de conditionnel, de fusion et les méthodes CompareTo des chaînes
 - **[Fixe]** Fournisseur LINQ--> contient de mettre en œuvre une méthode sur une liste pour générer le même SQL en tant que IEnumerable et tableau
 - **[Fixe]** BackoffRetryUtility utilise à nouveau le même HttpRequestMessage au lieu de créer un nouveau de nouvelle tentative
 - **[Obsolète]** UriFactory.CreateCollection--> devez désormais utiliser des UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Fixe]** Problèmes de localisation lors de l’utilisation des informations de culture en non comme nl-NL etc.. 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID de base de routage
    - Nouveau programme d’assistance UriFactory pour vous aider à ID de construction en fonction des liens de ressources
    - Nouvelles surcharges sur DocumentClient de l’URI
  - Ajout de IsValid() et de IsValidDetailed() dans LINQ pour géographiques
  - Prise en charge améliorée du fournisseur LINQ
    - **Math** - Abs, Acos, Asin, Atan, plafond, Cos, Exp, plancher, journal, Log10, Pow, arrondi, signe, Sin, Sqrt, Tan, tronquer
    - **Chaîne** - Concat, contient, EndsWith, IndexOf, Count, ToLower, TrimStart, remplacer, inverser, TrimEnd, StartsWith, SubString, ToUpper
    - **Tableau** - Concat, contient le nombre,
    - Opérateur **dans**

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[version 1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Prise en charge pour la modification des règles d’indexation
    - Nouvelle méthode de ReplaceDocumentCollectionAsync de DocumentClient
    - Nouvelle propriété IndexTransformationProgress dans ResourceResponse<T> pour le suivi du pourcentage d’avancement index des modifications de stratégie
    - DocumentCollection.IndexingPolicy est désormais mutable
  - Prise en charge de l’indexation spatiale et la requête
    - Nouvel espace de noms Microsoft.Azure.Documents.Spatial pour la sérialisation/désérialisation de types spatiales comme Point et polygone
    - Nouvelle classe de SpatialIndex pour l’indexation des données de GeoJSON stockées dans DocumentDB
  - **[Fixe]** : requête SQL incorrecte généré à partir d’expression linq [n ° 38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dépendance de v5.0.7 de Newtonsoft.Json 
- Modifications apportées à la prise en charge par la commande
  - Prise en charge du fournisseur LINQ pour OrderBy() ou OrderByDescending()
  - Prise en charge par la commande IndexingPolicy 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Prise en charge pour le partitionnement des données en utilisant les nouvelles classes HashPartitionResolver et RangePartitionResolver et le IPartitionResolver
- Sérialisation de DataContract
- Prise en charge de GUID de fournisseur LINQ
- Prise en charge UDF dans LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- KIT DE DÉVELOPPEMENT LOGICIEL GA

> [AZURE.NOTE]
Il y a un changement de nom de package NuGet entre aperçu et GA. Nous sommes passés de **Microsoft.Azure.Documents.Client** à **Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-Preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Kits de développement de visualisation [obsolète]

## <a name="release--retirement-dates"></a>Version & Dates de retraite
Microsoft fournira notification au moins **12 mois** avant la suppression d’un kit de développement pour adoucir la transition vers une version plus récente/pris en charge.

Nouvelles fonctionnalités et fonctionnalités et optimisations ne sont ajoutées que dans le Kit de développement en cours, en tant que tel, il est recommandé que vous toujours mettre à niveau vers la dernière version SDK dès que possible. 

Toute demande de DocumentDB à l’aide d’un kit de développement logiciel annulée est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement DocumentDB Azure pour .NET avant la version **1.0.0** seront déclassées **le 29 février 2016**. 
 
<br/>
 
| Version | Date de publication | Date de déclassement 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 27 septembre 2016 |---
| [1.9.5](#1.9.5) | 01 septembre 2016 |---
| [question 1.9.4](#1.9.4) | 24 août 2016 |---
| [1.9.3](#1.9.3) | 15 août 2016 |---
| [1.9.2](#1.9.2) | 23 juillet 2016 |---
| 1.9.1 | Déconseillée |---
| 1.9.0 | Déconseillée |---
| [1.8.0](#1.8.0) | 14 juin 2016 |---
| [1.7.1](#1.7.1) | 06 mai 2016 |---
| [1.7.0](#1.7.0) | 26 avril 2016 |---
| [1.6.3](#1.6.3) | 08 avril 2016 |---
| [1.6.2](#1.6.2) | 29 mars 2016 |---
| [version 1.5.3](#1.5.3) | 19 février 2016 |---
| [1.5.2](#1.5.2) | 14 décembre 2015 |---
| [1.5.1](#1.5.1) | 23 novembre 2015 |---
| [1.5.0](#1.5.0) | 05 octobre 2015 |---
| [1.4.1](#1.4.1) | 25 août 2015 |---
| [1.4.0](#1.4.0) | 13 août 2015 |---
| [version 1.3.0](#1.3.0) | 05 août 2015 |---
| [1.2.0](#1.2.0) | 06 juillet 2015 |---
| [1.1.0](#1.1.0) | 30 avril 2015. |---
| [1.0.0](#1.0.0) | 08 avril 2015 |---
| [0.9.3-prelease](#0.9.x-preview) | 12 mars 2015 | 29 février 2016
| [0.9.2-prelease](#0.9.x-preview) | Janvier 2015 | 29 février 2016
| [.9.1-prelease](#0.9.x-preview) | 13 octobre 2014. | 29 février 2016
| [0.9.0-prelease](#0.9.x-preview) | 21 août 2014. | 29 février 2016

## <a name="faq"></a>FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
