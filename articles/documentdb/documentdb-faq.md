<properties 
    pageTitle="Questions de base de données de DocumentDB - Questions fréquentes | Microsoft Azure" 
    description="Obtenez les réponses aux questions fréquemment posées sur Azure DocumentDB un service de base de données de documents NoSQL de JSON. Répondez aux questions de base de données sur la capacité, des niveaux de performance et de mise à l’échelle." 
    keywords="Questions de base de données, Forum aux questions, documentdb, azure, Microsoft azure"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>Forum aux questions sur DocumentDB

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Questions de base de données sur les principes de base de Microsoft Azure DocumentDB

### <a name="what-is-microsoft-azure-documentdb"></a>Nouveautés Microsoft Azure DocumentDB 
Microsoft Azure DocumentDB est surprenante rapide, à l’échelle de la planète NoSQL document de base de données-as-a-service qui propose des requêtes enrichies sur les données sans schéma, contribue à offrir des performances fiables et configurables et permet le développement rapide, l’aide d’une plateforme managée porté par la puissance et atteindre de Microsoft Azure. DocumentDB est la solution idéale pour le jeu de mobile, web, et les applications IoT lorsque le débit prévisible, haute disponibilité, une latence faible et un modèle de données sans schéma sont clé des exigences. DocumentDB offre une flexibilité de schéma et enrichi d’indexation via un modèle de données natif JSON et inclut la prise en charge transactionnelle de plusieurs document avec JavaScript intégré.  
  
Pour plus d’informations de base de données, les réponses et les instructions sur le déploiement et l’utilisation de ce service, voir la [page de documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>Quel type de base de données est DocumentDB ?
DocumentDB est un document orienté base de données NoSQL qui stocke les données au format JSON.  DocumentDB prend en charge des structures imbriquées, données de contained automatique qui peuvent être interrogées via une DocumentDB enrichi [Grammaire de requête SQL](documentdb-sql-query.md). DocumentDB offre des performances élevées transactionnelle de traitement du côté serveur JavaScript par le biais de [procédures stockées, déclencheurs et les fonctions définies par l’utilisateur](documentdb-programming.md). La base de données prend également en charge les niveaux de cohérence ACCORDABLE développeur associé à [des niveaux de performance](documentdb-performance-levels.md).
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>Bases de données DocumentDB disposent des tables à une base de données relationnelle (SGBDR) ?
Non, DocumentDB stocke des données dans des collections de documents JSON.  Pour plus d’informations sur les ressources de DocumentDB, consultez [concepts et le modèle de ressources DocumentDB](documentdb-resources.md). Pour plus d’informations sur comment les solutions NoSQL DocumentDB diffèrent des solutions relationnelles, consultez [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>DocumentDB de bases de données prennent en charge les données sans schéma ?
Oui, DocumentDB permet aux applications de stocker les documents JSON arbitraires sans définition de schéma ou les indications. Les données sont immédiatement disponibles pour la requête via l’interface de requête DocumentDB SQL.   

### <a name="does-documentdb-support-acid-transactions"></a>DocumentDB prend en charge les transactions ACID ?
Oui, DocumentDB prend en charge les transactions entre documents exprimées en tant que déclencheurs et les procédures stockée de JavaScript. Une portée limitées à une partition unique au sein de chaque collection et exécutées avec la sémantique ACID que toutes les transactions ou nothing isolé des autres demandes des utilisateurs et le code s’exécutant simultanément.  Si des exceptions sont levées par le biais de l’exécution du côté serveur de code d’application JavaScript, la transaction entière est annulée. Pour plus d’informations sur les transactions, reportez-vous à la section [programme de transactions de base de données](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>Quels sont les scénarios d’utilisation classiques pour DocumentDB ?  
DocumentDB est un bon choix pour le nouveau jeu de mobile, web, et les applications IoT où automatique de l’échelle, des performances prévisibles, rapide d’ordre milliseconde des temps de réponse et la possibilité d’interroger sur les données sans schéma est important. DocumentDB se prête à développement rapide et la prise en charge de l’itération continue de modèles de données d’application. Les applications qui gèrent les données et les contenus générés par l’utilisateur sont [les scénarios d’utilisation courants pour DocumentDB](documentdb-use-cases.md).  

### <a name="how-does-documentdb-offer-predictable-performance"></a>Comment DocumentDB offre des performances prévisibles ?
Une [unité de la demande](documentdb-request-units.md) est la mesure de débit dans DocumentDB. 1 RU correspond au débit de l’obtention d’un document de 1 Ko. Chaque opération dans les DocumentDB, y compris les lectures, écritures, les requêtes SQL et exécutions de procédures stockées a la valeur RU déterministe en fonction du débit requis pour terminer l’opération. Au lieu de penser aux UC, d’e/s et de mémoire et chaque leur impact sur le débit de votre application, vous pouvez considérer en une seule mesure RU.

Chaque collection de DocumentDB peut être réservée avec un débit mis en service en termes de service RUs de débit par seconde. Pour les applications de n’importe quelle échelle, peut évaluer les demandes individuelles pour mesurer leurs valeurs de RU et fourniture des collections pour gérer l’ensemble des unités de demande pour toutes les requêtes. Vous pouvez également évoluer ou mettre à l’échelle vers le bas débit de votre collection selon les besoins du evolve de votre application. Pour plus d’informations sur les unités de demande et d’assistance pour déterminer votre collection doit, [Gérer les performances et la capacité](documentdb-manage.md) de lire et essayez le [calculateur de débit](https://www.documentdb.com/capacityplanner). 

### <a name="is-documentdb-hipaa-compliant"></a>Est DocumentDB HIPAA conforme ?
Oui, DocumentDB est la conformité HIPAA. HIPAA établit les conditions requises pour l’utilisation, la divulgation et protection des informations sanitaires identifiables de façon individuelle. Pour plus d’informations, consultez le [Centre de confidentialité de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-documentdb"></a>Quelles sont les limites de stockage de DocumentDB ? 
Il n’existe aucune limite théorique de la quantité totale de données une collection pouvant stocker dans DocumentDB. Si vous souhaitez stocker plus de 250 Go de données au sein d’une collection unique, veuillez [contacter le support technique](documentdb-increase-limits.md) pour avoir votre quota de compte a augmenté. 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>Quelles sont les limites de débit de DocumentDB ? 
Il n’existe aucune limite théorique au montant total de débit que DocumentDB, peut prendre en charge une collection si votre charge de travail peut être distribué à peu près uniforme parmi un nombre suffisamment grand de clés de la partition. Si vous souhaitez dépasser demande 250 000 unités par seconde par collection ou par compte, veuillez [contacter le support technique](documentdb-increase-limits.md) pour avoir votre quota de compte a augmenté. 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Combien coûte Microsoft Azure DocumentDB ?
Reportez-vous à la page de [Détails de tarification DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) pour plus de détails. Frais d’utilisation de DocumentDB sont déterminées par le nombre de collections en cours d’utilisation, le nombre d’heures que les collections sont en ligne, et le stockage consommée et le débit mis en service pour chaque collection. 

### <a name="is-there-a-free-account-available"></a>Existe-t-il un compte gratuit ?
Si vous êtes nouveau sur Azure, vous pouvez vous inscrire pour un [compte gratuit Azure](https://azure.microsoft.com/free/), qui vous donne des 30 derniers jours et 200 $ pour essayer de tous les services Azure. Ou bien, si vous disposez d’un abonnement de Visual Studio, vous êtes éligible à [150 euros en crédits d’Azure gratuits par mois](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) pour les utiliser sur n’importe quel service Azure.  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>Comment puis-je obtenir une aide supplémentaire sur le DocumentDB ?
Si vous avez besoin d’aide, veuillez en contact avec nous de [Débordement de pile](http://stackoverflow.com/questions/tagged/azure-documentdb), les [Forums de développeurs MSDN Azure DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), ou planifier une [conversation de 1:1 avec l’équipe d’ingénierie DocumentDB](http://www.askdocdb.com/). Pour rester à jour sur les fonctionnalités et les dernières nouvelles de DocumentDB, suivez-nous sur [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Configurer Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>Comment faire d’abonnement pour Microsoft Azure DocumentDB ?
Microsoft Azure DocumentDB est disponible dans [Azure Portal][azure-portal].  Tout d’abord vous devez vous inscrire pour un abonnement Microsoft Azure.  Une fois que vous vous inscrivez pour un abonnement Microsoft Azure, vous pouvez ajouter un compte de DocumentDB à votre abonnement Azure. Pour obtenir des instructions sur l’ajout d’un compte DocumentDB, voir [Création d’un compte de base de données DocumentDB](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>Ce qu’est une clé principale ?
Une clé principale est un jeton de sécurité pour accéder à toutes les ressources d’un compte. Personnes avec la clé ont lu et l’accès en écriture à toutes les ressources dans le compte de base de données. Soyez prudent lors de la distribution des clés principales. La clé principale de principale et secondaire la clé principale sont disponibles dans la lame **clés **du [Portail Azure][azure-portal]. Pour plus d’informations sur les clés, reportez-vous à la section [Afficher, copier et les touches d’accès régénérer](documentdb-manage-account.md#keys).

### <a name="how-do-i-create-a-database"></a>Comment pour créer une base de données ?
Vous pouvez créer des bases de données à l’aide du [Portail Azure]() comme décrit dans [Création d’une base de données DocumentDB](documentdb-create-database.md)de la [DocumentDB SDK](documentdb-sdk-dotnet.md), ou via les [API du reste](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>Ce qui est une collection ?
Une collection est un conteneur de documents JSON et la logique d’application JavaScript associée. Une collection est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le débit et utilisé storaged. Les collections peuvent couvrir une ou plusieurs partitions et des serveurs et peuvent évoluer pour gérer pratiquement illimité de volumes de stockage ou de débit.

Les collections sont également les entités de facturation pour DocumentDB. Chaque collection est facturée en fonction de toutes les heures sur le débit mis en service et de l’espace de stockage utilisé. Pour plus d’informations, reportez-vous à la section [DocumentDB de tarification](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Comment définir des utilisateurs et des autorisations ?
Vous pouvez créer des utilisateurs et des autorisations à l’aide de la [DocumentDB SDK](documentdb-sdk-dotnet.md) ou via les [API du reste](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Questions de base de données sur le développement avec Microsoft Azure DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb"></a>Comment faire commencer développement contre les DocumentDB ?
[Kits de développement SDK](documentdb-sdk-dotnet.md) sont disponibles pour .NET, Python, Node.js, JavaScript et Java.  Les développeurs peuvent également exploiter des [API RESTful de HTTP](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour interagir avec les ressources de DocumentDB à partir d’une variété de plates-formes et des langages. 

Exemples de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md)et kits de développement logiciel [Python](documentdb-python-samples.md) sont disponibles sur GitHub.

### <a name="does-documentdb-support-sql"></a>DocumentDB prend en charge SQL ?
Le langage de requête DocumentDB SQL est un sous-ensemble d’amélioration de la fonctionnalité de requête pris en charge par SQL. Le langage de requête DocumentDB SQL fournit enrichi hiérarchique et opérateurs relationnels et extensibilité via JavaScript en fonction utilisateur défini par les fonctions. Grammaire JSON permet une modélisation de documents à JSON sous forme d’arborescences avec les étiquettes comme les nœuds d’arbre qui est utilisée par les techniques d’indexation automatiques de DocumentDB ainsi que le langage de requête SQL de DocumentDB.  Pour plus d’informations sur l’utilisation de la grammaire SQL, reportez-vous à la [Requête DocumentDB] [ query] l’article.

### <a name="what-are-the-data-types-supported-by-documentdb"></a>Quels sont les types de données pris en charge par DocumentDB ?
Types de données primitifs pris en charge dans DocumentDB sont identiques au format JSON. JSON est un système de type simple qui se compose de la valeur de chaînes, nombres (IEEE754 double précision) et les valeurs booléennes : trues, false et null.  Les types de données plus complexes tels que dateheure, Guid, Int64 et la géométrie peuvent être représentés dans JSON et DocumentDB par le biais de la création d’objets imbriqués à l’aide de l’opérateur de {} et les tableaux à l’aide de l’opérateur []. 

### <a name="how-does-documentdb-provide-concurrency"></a>Comment DocumentDB ne fournit pas d’accès concurrentiel ?
DocumentDB prend en charge le contrôle de concurrence optimiste (OCC) par le biais de balises d’entité HTTP ou etags. Toutes les ressources DocumentDB a un etag et l’etag est définie sur le serveur chaque fois qu’un document est mis à jour. L’en-tête etag et la valeur actuelle sont incluses dans tous les messages de réponse. ETags peut être utilisé avec l’en-tête If-Match pour permettre au serveur déterminer si une ressource doit être mis à jour. La valeur de If-Match est la valeur etag pour être vérifiés. Si elle correspond à la valeur etag server, la ressource sera mis à jour. Si l’etag n’est plus actif, le serveur rejette l’opération avec un « HTTP 412 condition préalable échec » code de réponse. Le client devra ensuite extraire à nouveau la ressource pour obtenir la valeur actuelle d’etag pour la ressource. En outre, etags utilisable avec en-tête If-None-Match pour déterminer si un nouveau extraire d’une ressource est nécessaire. 

Pour utiliser l’accès concurrentiel optimiste dans .NET, utilisez la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Pour obtenir un exemple de .NET, consultez [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) dans l’échantillon DocumentManagement sur github.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>Comment effectuer des transactions dans DocumentDB ?
DocumentDB prend en charge LINQ de transactions via les procédures stockée de JavaScript et des déclencheurs. Toutes les opérations de base de données à l’intérieur de scripts sont exécutées en isolement de capture instantanée étendue à la collection, si elle est une collection à partition unique, ou des documents avec la même valeur de clé de partition dans une collection, si la collection est partitionnée. Une capture instantanée des versions de document (ETags) est effectuée au début de la transaction et validée que si le script réussit. Si le code JavaScript lève une erreur, la transaction est restaurée. Pour plus d’informations, consultez [programmation de DocumentDB côté serveur](documentdb-programming.md) .

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>Comment puis-je bloc des documents de l’insertion dans DocumentDB ? 
Il existe trois façons d’en bloc de documents de l’insertion dans DocumentDB :

- L’outil de migration de données, comme indiqué dans [l’importation de données à DocumentDB](documentdb-import-data.md).
- Explorateur de document dans le portail d’Azure, comme décrit dans le [bloc ajouter des documents avec l’Explorateur de Document](documentdb-view-json-document-explorer.md#BulkAdd).
- Les procédures stockées, comme décrit dans la [programmation de DocumentDB côté serveur](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>DocumentDB en charge la mise en cache de lien de ressources ?
Oui, car DocumentDB est un service RESTful, liens vers les ressources sont immuables et peuvent être mises en cache. Les clients de DocumentDB peuvent spécifier un en-tête « If-None-Match » pour les lectures sur n’importe quelle ressource comme document ou de collecte et de mise à jour uniquement lorsque la version du serveur a modifier des copies de leurs locales. 

### <a name="is-a-local-instance-of-documentdb-available"></a>Une instance locale de DocumentDB n’est disponible ?
Une instance locale de DocumentDB n’est pas disponible pour l’instant. Vous pouvez suivre l’état d’un émulateur local et le vote pour lui sur le [forum de commentaires](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
