<properties
    pageTitle="DocumentDB Node.js API et SDK | Microsoft Azure"
    description="Pour tout savoir sur les API de Node.js et SDK, y compris les dates de commercialisation, les dates de retraite et les modifications apportées entre chaque version du SDK Node.js DocumentDB."
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>Les API DocumentDB et kits de développement logiciel

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>SDK et API de DocumentDB Node.js

<table>
<tr><td>**Télécharger le Kit de développement logiciel**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentation sur l’API**</td><td>[Documentation de référence API de Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Instructions d’installation du Kit de développement logiciel**</td><td>[Instructions d’installation](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribuer au Kit de développement logiciel**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Exemples**</td><td>[Exemples de code Node.js](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Didacticiel de démarrage Get**</td><td>[Mise en route avec le Kit de développement logiciel Node.js](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Didacticiel d’application Web**</td><td>[Créer une application web de Node.js à l’aide de DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Plate-forme actuelle**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>Notes de publication

###<a name="1.10.0"/>1.10.0</a>

- Prise en charge pour les requêtes parallèles de partition.
- Prise en charge pour les requêtes de haut/ORDER BY pour les collections partitionnées.

###<a name="1.9.0"/>1.9.0</a>

- Prise en charge des stratégie ajoutée de nouvelle tentative pour les demandes accélérées. (Les demandes accélérées recevoir une exception trop grande vitesse demande, code d’erreur 429.) Par défaut, DocumentDB tentatives de neuf pour chaque requête lorsque le code d’erreur 429 est rencontré, en respectant le temps de retryAfter dans l’en-tête de réponse. Un intervalle fixe peut maintenant être définie dans le cadre de la propriété RetryOptions sur l’objet ConnectionPolicy si vous souhaitez ignorer l’heure de retryAfter retournée par le serveur entre les tentatives. DocumentDB maintenant attend un maximum de 30 secondes pour chaque demande à laquelle est limitée (quel que soit le nombre de tentatives) et renvoie la réponse avec le code d’erreur 429. Cette heure peut également être remplacé dans la propriété RetryOptions sur l’objet de ConnectionPolicy.

- DocumentDB renvoie à présent x-ms-gaz--nombre de tentatives x-ms-throttle-retry-wait-time-ms que les en-têtes de réponse dans chaque demande pour désigner l’accélérateur retry count et le temps de cummulative la demande ne soit entre les tentatives.

- La classe RetryOptions a été ajoutée, en exposant la propriété RetryOptions sur la classe ConnectionPolicy qui peut être utilisée pour substituer certaines des options réessayer par défaut.

###<a name="1.8.0"/>1.8.0</a>

 - Ajouter la prise en charge pour les comptes de la base de données de plusieurs régions.

###<a name="1.7.0"/>1.7.0</a>

- Ajouter la prise en charge de la fonctionnalité temps à Live(TTL) pour les documents.

###<a name="1.6.0"/>1.6.0</a>

- Mise en œuvre [des collections partitionnées](documentdb-partition-data.md) et [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- RangePartitionResolver.resolveForRead bogue où il a été ne renvoyant pas les liens en raison d’un mauvais concat des résultats.

###<a name="1.5.5"/>1.5.5</a>

- Fixe hashParitionResolver resolveForRead() : lorsque aucune clé de partition fourni a été levée d’exception, au lieu de renvoyer une liste de tous les liens enregistrés.

###<a name="1.5.4"/>1.5.4</a>

- Correctifs émettre [100](https://github.com/Azure/azure-documentdb-node/issues/100) - Agent de HTTPS dédié : éviter l’agent global DocumentDB à des fins de modification. Utiliser un agent dédié pour toutes les demandes de la version de lib.

###<a name="1.5.3"/>version 1.5.3</a>

- Correctifs émettent [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - handle correctement les tirets dans les identificateurs de media.

###<a name="1.5.2"/>1.5.2</a>

- Correctifs émettent [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter écouteur fuite d’avertissement.

###<a name="1.5.1"/>1.5.1</a>

- Correctifs émettent [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - renommer dossier hachage au hachage pour systèmes de respect de la casse.

### <a name="1.5.0"/>1.5.0</a>

- Prise en charge de mise en œuvre ont en ajoutant des résolveurs de partition de hachage et de la plage.

### <a name="1.4.0"/>1.4.0</a>

- Mettre en œuvre Upsert. Nouvelles méthodes d’upsertXXX sur documentClient.

### <a name="1.3.0"/>version 1.3.0</a>

- Ignoré pour afficher les numéros de version en parallèle avec les autres kits de développement logiciel.

### <a name="1.2.2"/>1.2.2</a>

- Fractionnement Q promet de wrapper pour le nouveau référentiel.
- Mise à jour de fichier de package pour npm Registre.

### <a name="1.2.1"/>1.2.1</a>

- Implémente le routage basé sur l’ID.
- Résout le problème [n ° 49](https://github.com/Azure/azure-documentdb-node/issues/49) - conflits de propriété en cours avec la méthode current().

### <a name="1.2.0"/>1.2.0</a>

- Prise en charge supplémentaire pour les index géographique.
- Valide la propriété id pour toutes les ressources. ID de ressources ne peut pas contenir ?, /, #, & #47 ; & #47 ; se terminent par un espace ou caractères.
- Ajoute le nouvel en-tête « index transformation cours » à ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implémente la stratégie d’indexation V2.

### <a name="1.0.3"/>1.0.3</a>

- Problème [#40] (https://github.com/Azure/azure-documentdb-node/issues/40) - mise en œuvre des configurations eslint et grunt dans le noyau et la promesse de kit de développement logiciel.

### <a name="1.0.2"/>1.0.2</a>

- De problème [n ° 45](https://github.com/Azure/azure-documentdb-node/issues/45) - groupes de wrapper de promesses n’inclut pas l’en-tête avec une erreur.

### <a name="1.0.1"/>version 1.0.1</a>

- Possibilité de mise en œuvre pour interroger les conflits en ajoutant readConflicts, readConflictAsync et queryConflicts.
- Documentation sur l’API mis à jour.
- Problème [n ° 41](https://github.com/Azure/azure-documentdb-node/issues/41) - erreur de client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>

- KIT DE DÉVELOPPEMENT LOGICIEL GA.

## <a name="release--retirement-dates"></a>Version & Dates de retraite
Microsoft fournira notification au moins **12 mois** avant la suppression d’un kit de développement pour adoucir la transition vers une version plus récente/pris en charge.

Nouvelles fonctionnalités et fonctionnalités et optimisations ne sont ajoutées que dans le Kit de développement en cours, en tant que tel, il est recommandé que vous toujours mettre à niveau vers la dernière version SDK dès que possible.

Toute demande de DocumentDB à l’aide d’un kit de développement logiciel annulée est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement DocumentDB Azure pour Node.js avant la version **1.0.0** seront déclassées **le 29 février 2016**.

<br/>

| Version | Date de publication | Date de déclassement
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 03 octobre 2016 |---
| [1.9.0](#1.9.0) | 07 juillet 2016 |---
| [1.8.0](#1.8.0) | 14 juin 2016 |---
| [1.7.0](#1.7.0) | 26 avril 2016 |---
| [1.6.0](#1.6.0) | 29 mars 2016 |---
| [1.5.6](#1.5.6) | 08 mars 2016 |---
| [1.5.5](#1.5.5) | 02 février 2016 |---
| [1.5.4](#1.5.4) | 01 février 2016 |---
| [1.5.2](#1.5.2) | 26 janvier 2016 |---
| [1.5.2](#1.5.2) | 22 janvier 2016 |---
| [1.5.1](#1.5.1) | 4 janvier 2016 |---
| [1.5.0](#1.5.0) | 31 décembre 2015. |---
| [1.4.0](#1.4.0) | 06 octobre 2015 |---
| [version 1.3.0](#1.3.0) | 06 octobre 2015 |---
| [1.2.2](#1.2.2) | 10 septembre 2015 |---
| [1.2.1](#1.2.1) | 15 août 2015 |---
| [1.2.0](#1.2.0) | 05 août 2015 |---
| [1.1.0](#1.1.0) | 09 juillet 2015 |---
| [1.0.3](#1.0.3) | 04 juin 2015 |---
| [1.0.2](#1.0.2) | 23 mai 2015 |---
| [version 1.0.1](#1.0.1) | 15 mai 2015 |---
| [1.0.0](#1.0.0) | 08 avril 2015 |---
| 0.9.4-Prerelease | 06 avril 2015 | 29 février 2016
| 0.9.3-Prerelease | 14 janvier 2015. | 29 février 2016
| 0.9.2-Prerelease | 18 décembre 2014. | 29 février 2016
| 0.9.1-Prerelease | 22 août 2014. | 29 février 2016
| 0.9.0-Prerelease | 21 août 2014. | 29 février 2016


## <a name="faq"></a>FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .
