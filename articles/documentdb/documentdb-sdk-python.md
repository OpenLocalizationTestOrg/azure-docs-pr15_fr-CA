<properties 
    pageTitle="Les Python DocumentDB API et SDK | Microsoft Azure" 
    description="Pour tout savoir sur les Python API et SDK, y compris les dates de commercialisation, les dates de retraite et les modifications apportées entre chaque version du SDK Python DocumentDB." 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>Les API DocumentDB et kits de développement logiciel

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>SDK et API de Python de DocumentDB

<table>
<tr><td>**Télécharger le Kit de développement logiciel**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentation sur l’API**</td><td>[Documentation de référence API de Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Instructions d’installation du Kit de développement logiciel**</td><td>[Instructions d’installation du Kit de développement logiciel Python](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuer au Kit de développement logiciel**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Mise en route**</td><td>[Mise en route avec le Kit de développement de Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plate-forme actuelle**</td><td>[Python 2.7](https://www.python.org/downloads/) et les [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- Prise en charge supplémentaire pour les Python 3.5.
- Prise en charge supplémentaire pour le regroupement de connexions à l’aide d’un module de demandes.
- Prise en charge supplémentaire pour la cohérence de la session.
- Prise en charge pour les requêtes de haut/ORDERBY pour les collections partitionnées.


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Prise en charge des stratégie ajoutée de nouvelle tentative pour les demandes accélérées. (Les demandes accélérées recevoir une exception trop grande vitesse demande, code d’erreur 429.) Par défaut, DocumentDB tentatives de neuf pour chaque requête lorsque le code d’erreur 429 est rencontré, en respectant le temps de retryAfter dans l’en-tête de réponse. Un intervalle fixe peut maintenant être définie dans le cadre de la propriété RetryOptions sur l’objet ConnectionPolicy si vous souhaitez ignorer l’heure de retryAfter retournée par le serveur entre les tentatives. DocumentDB maintenant attend un maximum de 30 secondes pour chaque demande à laquelle est limitée (quel que soit le nombre de tentatives) et renvoie la réponse avec le code d’erreur 429. Cette heure peut également être remplacé dans la propriété RetryOptions sur l’objet de ConnectionPolicy.

- DocumentDB renvoie à présent x-ms-gaz--nombre de tentatives x-ms-throttle-retry-wait-time-ms que les en-têtes de réponse dans chaque demande pour désigner l’accélérateur retry count et le temps de cummulative la demande ne soit entre les tentatives.

- Suppression de la classe RetryPolicy et la propriété correspondante (retry_policy) exposées sur la classe document_client et à la place a introduit une classe RetryOptions exposant la propriété RetryOptions sur la classe ConnectionPolicy qui peut être utilisée pour substituer certaines des options réessayer par défaut.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Ajouter la prise en charge pour les comptes de la base de données de plusieurs régions.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Ajouter la prise en charge de la fonctionnalité temps à Live(TTL) pour les documents.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correctifs liés à la division de côté serveur pour autoriser les caractères spéciaux dans le chemin d’accès de partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Mise en œuvre [des collections partitionnées](documentdb-partition-data.md) et [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Ajouter le hachage et la plage de partition résolveurs pour faciliter les applications ont entre plusieurs partitions.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Mettre en œuvre Upsert. Nouvelles méthodes de UpsertXXX ajoutés pour prendre en charge la fonctionnalité de Upsert.
- ID de la mise en œuvre en fonction de routage. Aucune modification d’API publiques, toutes les modifications en internes.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Index de géographique prend en charge.
- Valide la propriété id pour toutes les ressources. ID de ressources ne peut pas contenir ?, /, #, \, caractères ou se terminent par un espace.
- Ajoute le nouvel en-tête « index transformation cours » à ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implémente la stratégie d’indexation V2.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[version 1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Prend en charge la connexion proxy.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- KIT DE DÉVELOPPEMENT LOGICIEL GA.

## <a name="release--retirement-dates"></a>Dates de version & retraite
Microsoft fournira notification au moins **12 mois** avant la suppression d’un kit de développement pour adoucir la transition vers une version plus récente/pris en charge.

Nouvelles fonctionnalités et fonctionnalités et optimisations ne sont ajoutées que dans le Kit de développement en cours, en tant que tel, il est recommandé que vous toujours mettre à niveau vers la dernière version SDK dès que possible. 

Toute demande de DocumentDB à l’aide d’un kit de développement logiciel annulée est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement DocumentDB Azure pour les Python avant la version **1.0.0** seront déclassées **le 29 février 2016**. 

<br/>

| Version | Date de publication | Date de déclassement 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | 29 septembre 2016 |---
| [1.9.0](#1.9.0) | 07 juillet 2016 |---
| [1.8.0](#1.8.0) | 14 juin 2016 |---
| [1.7.0](#1.7.0) | 26 avril 2016 |---
| [1.6.1](#1.6.1) | 08 avril 2016 |---
| [1.6.0](#1.6.0) | 29 mars 2016 |---
| [1.5.0](#1.5.0) | 03 janvier 2016 |---
| [1.4.2](#1.4.2) | 06 octobre 2015 |---
| [1.4.1](#1.4.1) | 06 octobre 2015 |---
| [1.2.0](#1.2.0) | 06 août 2015 |---
| [1.1.0](#1.1.0) | 09 juillet 2015 |---
| [version 1.0.1](#1.0.1) | 25 mai 2015 |---
| [1.0.0](#1.0.0) | 07 avril 2015 |---
| 0.9.4-prelease | 14 janvier 2015. | 29 février 2016
| 0.9.3-prelease | 09 décembre 2014. | 29 février 2016
| 0.9.2-prelease | 25 novembre 2014. | 29 février 2016
| 0.9.1-prelease | 23 septembre 2014. | 29 février 2016
| 0.9.0-prelease | 21 août 2014. | 29 février 2016

## <a name="faq"></a>FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
