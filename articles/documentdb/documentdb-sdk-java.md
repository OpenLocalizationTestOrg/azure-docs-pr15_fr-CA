
<properties
    pageTitle="DocumentDB Java API et SDK | Microsoft Azure"
    description="Pour tout savoir sur les API de Java SDK, y compris les dates de commercialisation, les dates de retraite et les modifications apportées entre chaque version du SDK Java DocumentDB."
    services="documentdb"
    documentationCenter="java"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>Les API DocumentDB et kits de développement logiciel

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-java-api-and-sdk"></a>SDK et API de DocumentDB Java

<table>
<tr><td>**Téléchargement du Kit de développement logiciel**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>
<tr><td>**Documentation sur l’API**</td><td>[Documentation de référence API de Java](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Contribuer au Kit de développement logiciel**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Mise en route**</td><td>[Mise en route avec le Kit de développement Java](documentdb-java-application.md)</td></tr>
<tr><td>**En cours de runtime pris en charge**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)

  - Prise en charge supplémentaire pour le niveau de cohérence BoundedStaleness.
  - Prise en charge supplémentaire pour une connectivité directe pour les opérations pour les collections partitionnées.
  - Corrige un bogue dans l’interrogation d’une base de données SQL.
  - Corrige un bogue dans le cache de session où jeton de session peut être défini de manière incorrecte.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)

  - Prise en charge pour les requêtes parallèles de partition.
  - Prise en charge pour les requêtes de haut/ORDER BY pour les collections partitionnées.
  - Prise en charge supplémentaire pour la cohérence renforcée.
  - Prise en charge pour les requêtes de nom en fonction lors de l’utilisation d’une connexion directe.
  - Fixe ActivityId maintenir une cohérence entre toutes les tentatives de demande.
  - Corrige un bogue lié au cache de session lors de la recréation d’une collection portant le même nom.
  - Ajout de polygone et LineString DataTypes tout en spécifiant la collection indexation de stratégie pour gardiennage des recherches spatiales.
  - Problèmes résolus avec Doc Java pour Java 1.8.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Corrige un bogue dans PartitionKeyDefinitionMap à mettre en cache les collections de partition unique et pas extraction supplémentaire demandes clés de partition.
  - Corrige un bogue tentative ne pas lorsqu’une valeur de clé de partition incorrect est fournie.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Ajouter la prise en charge pour les comptes de la base de données de plusieurs régions.
  - Prise en charge supplémentaire pour la relance automatique des demandes accélérée des options permettant de personnaliser le maximum de nouvelles tentatives et le délai entre deux tentatives maximum.  Consultez RetryOptions et ConnectionPolicy.getRetryOptions().
  - Code de partitionnement personnalisé basé sur le IPartitionResolver désapprouvée. Utilisez les collections partitionnées pour stockage et un débit plus élevé.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Prise en charge ajoutée de nouvelle tentative stratégie de limitation du.  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Temps ajouté pour la prise en charge live (TTL) pour les documents.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Mise en œuvre [des collections partitionnées](documentdb-partition-data.md) et [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md).

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Corrige un bogue dans HashPartitionResolver pour générer des valeurs de hachage dans little endian pour être cohérent avec les autres kits de développement logiciel.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Ajouter le hachage et la plage de partition résolveurs pour faciliter les applications ont entre plusieurs partitions.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Mettre en œuvre Upsert. Nouvelles méthodes d’upsertXXX ajoutés pour prendre en charge la fonctionnalité de Upsert.
- ID de la mise en œuvre en fonction de routage. Aucune modification d’API publiques, toutes les modifications en internes.

### <a name="a-name130130"></a><a name="1.3.0"/>version 1.3.0
- Version ignorée pour afficher le numéro de version dans l’alignement avec les autres kits de développement logiciel

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Prend en charge géographique Index
- Valide la propriété id pour toutes les ressources. ID de ressources ne peut pas contenir ?, /, #, \, caractères ou se terminent par un espace.
- Ajoute le nouvel en-tête « index transformation cours » à ResourceResponse.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implémente la stratégie d’indexation V2

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- KIT DE DÉVELOPPEMENT LOGICIEL GA

## <a name="release--retirement-dates"></a>Version & Dates de retraite
Microsoft fournira notification au moins **12 mois** avant la suppression d’un kit de développement pour adoucir la transition vers une version plus récente/pris en charge.

Nouvelles fonctionnalités et fonctionnalités et optimisations ne sont ajoutées que dans le Kit de développement en cours, en tant que tel, il est recommandé que vous toujours mettre à niveau vers la dernière version SDK dès que possible.

Toute demande de DocumentDB à l’aide d’un kit de développement logiciel annulée est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions de la DocumentDB d’Azure SDK pour Java avant la version **1.0.0** seront déclassées **le 29 février 2016**.

<br/>

| Version | Date de publication | Date de déclassement
| ---     | ---          | ---
| [1.9.1](#1.9.1) | 28 octobre 2016 |---
| [1.9.0](#1.9.0) | 03 octobre 2016 |---
| [1.8.1](#1.8.1) | 30 juin 2016 |---
| [1.8.0](#1.8.0) | 14 juin 2016 |---
| [1.7.1](#1.7.1) | 30 avril 2016 |---
| [1.7.0](#1.7.0) | 27 avril 2016 |---
| [1.6.0](#1.6.0) | 29 mars 2016 |---
| [1.5.1](#1.5.1) | 31 décembre 2015. |---
| [1.5.0](#1.5.0) | 04 décembre 2015 |---
| [1.4.0](#1.4.0) | 05 octobre 2015 |---
| [version 1.3.0](#1.3.0) | 05 octobre 2015 |---
| [1.2.0](#1.2.0) | 05 août 2015 |---
| [1.1.0](#1.1.0) | 09 juillet 2015 |---
| [version 1.0.1](#1.0.1) | 12 mai 2015 |---
| [1.0.0](#1.0.0) | 07 avril 2015 |---
| 0.9.5-prelease | 09, mar 2015 | 29 février 2016
| 0.9.4-prelease | 17 février 2015 | 29 février 2016
| 0.9.3-prelease | 13 janvier 2015. | 29 février 2016
| 0.9.2-prelease | 19 décembre 2014. | 29 février 2016
| 0.9.1-prelease | 19 décembre 2014. | 29 février 2016
| 0.9.0-prelease | 10 décembre 2014. | 29 février 2016

## <a name="faq"></a>FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .
