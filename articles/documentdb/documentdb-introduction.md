<properties 
    pageTitle="Introduction à DocumentDB, une base de données JSON | Microsoft Azure" 
    description="Obtenir des informations sur les DocumentDB d’Azure, une base de données NoSQL JSON. Cette base de données de document est conçu pour les données volumineuses, élastique, disponibilité et l’évolutivité." 
    keywords="base de données JSON, base de données de document"
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
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introduction à DocumentDB : une base de données NoSQL JSON

##<a name="what-is-documentdb"></a>Quel est DocumentDB ?

DocumentDB est un service de base de données NoSQL entièrement géré, conçu pour des performances rapides et prévisibles, haute disponibilité, une évolutivité élastique, distribution globale et facilité de développement. Comme indemne de schéma NoSQL, DocumentDB offre riches et familiers des fonctions de requête SQL avec des latences de faibles cohérence sur les données JSON - garantissant que 99 % de vos lectures sont pris en charge sous 10 millisecondes et 99 % de vos écritures sont pris en charge sous 15 millisecondes. Ces avantages uniques d’effectuer DocumentDB un très bon apte à web, mobile, jeux et IoT et nombreuses autres applications nécessitant une échelle transparente et réplication globale.

## <a name="how-can-i-learn-about-documentdb"></a>Comment puis-je obtenir des informations sur les DocumentDB ? 

Pour en savoir plus sur les DocumentDB et les voir en action, un rapide consiste à ces trois étapes : 

1. Regarder les deux minutes [What DocumentDB ?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) vidéo, qui présente les avantages liés à l’aide de DocumentDB.
2. Regardez la minute trois [DocumentDB de créer sur Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vidéo, qui met en surbrillance de la mise en route avec DocumentDB à l’aide du portail Azure.
3. Visitez le [Laboratoire de la requête](http://www.documentdb.com/sql/demo), où vous pouvez parcourir différentes activités pour en savoir plus sur les riches fonctionnalités de requête disponibles dans DocumentDB. Ensuite, head via l’onglet Sandbox et exécuter vos propres requêtes SQL personnalisées et expérimenter DocumentDB.

Ensuite, revenez à cet article, où nous allons approfondir.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Les fonctions et les fonctionnalités clés offertes par DocumentDB ?  

DocumentDB Azure offre les fonctions suivantes et les avantages :

-   **Débit ELASTIQUEMENT évolutif et stockage :** Facilement évoluer ou mettre à l’échelle vers le bas de votre base de données DocumentDB JSON pour répondre aux besoins de votre application. Vos données sont stockées sur les disques SSD (SSD) de faibles latences prévisibles. DocumentDB prend en charge les conteneurs de stockage des données JSON appelées collections qui peuvent s’adapter à la taille de stockage pratiquement illimitée et débit mis en service. Vous pouvez ELASTIQUEMENT mise à l’échelle DocumentDB avec des performances prévisibles en toute transparence à mesure que votre application augmente. 

-   **La réplication plusieurs région :** DocumentDB réplique en toute transparence vos données à toutes les zones que vous avez associé à votre compte DocumentDB, qui vous permet de développer des applications qui nécessitent un accès global aux données tout en fournissant des compromis entre la cohérence, de disponibilité et de performances, toutes les garanties correspondantes. DocumentDB fournit un basculement transparent régional avec les API d’hébergement multiple et la possibilité de mise à l’échelle ELASTIQUEMENT du stockage et débit dans le monde entier. Découvrez plus de [distribuer des données globalement avec DocumentDB](documentdb-distribute-data-globally.md).

-   **Des requêtes Ad hoc avec la syntaxe courante de SQL :** Stocker les documents JSON hétérogènes au sein de DocumentDB et interroger ces documents grâce à une syntaxe SQL courante. DocumentDB utilise un verrou simultané, libre, journal structuré des technologies d’indexation pour indexer automatiquement le contenu du document. Cela permet des requêtes riches en temps réel sans avoir besoin de spécifier des options de schéma, les index secondaires ou des vues. Pour en savoir plus dans la [Requête DocumentDB](documentdb-sql-query.md). 

-   **L’exécution de JavaScript dans la base de données :** Exprimer la logique de l’application en tant que procédures stockées, des déclencheurs et des fonctions de défini par l’utilisateur (UDF) à l’aide de JavaScript standard. Cela permet à votre logique d’application fonctionner sur des données sans vous soucier de l’incompatibilité entre l’application et le schéma de base de données. DocumentDB fournit l’exécution JavaScript de logique d’application directement dans le moteur de base de données. L’intégration de JavaScript permet l’exécution de l’insertion, remplacement, de suppression et des opérations de sélection à partir d’un programme JavaScript comme une transaction isolée. Pour en savoir plus dans la [programmation côté serveur de DocumentDB](documentdb-programming.md).

-   **Des niveaux de cohérence ACCORDABLE :** Sélectionner à partir des quatre niveaux de cohérence bien définies pour atteindre un compromis optimal entre les performances et la cohérence. Pour les requêtes et les opérations de lecture, DocumentDB propose quatre niveaux de cohérence distinct : session forte de péremption délimitée, et une éventuelle. Ces niveaux de cohérence précis et bien définis vous autorise à effectuer sons compromis entre le temps de latence, la disponibilité et la cohérence. Pour en savoir plus dans [l’utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB](documentdb-consistency-levels.md).

-   **Entièrement gérés :** Éliminer la nécessité de gérer des ressources de base de données et de la machine. Comme un service entièrement géré de Microsoft Azure, ne pas devoir gérer des ordinateurs virtuels, déployer et configurer le logiciel, gérer la mise à l’échelle, ou traiter les mises à niveau de la couche de données complexes. Chaque base de données est automatiquement sauvegardée et protégée contre les défaillances régionaux. Vous pouvez facilement ajouter un compte de DocumentDB et provisionner une capacité comme vous en avez besoin, ce qui vous permet de vous concentrer sur votre application au lieu d’exploitation et la gestion de votre base de données. 

-   **Ouvert par conception :** Mise en route rapide en utilisant les outils et les compétences existantes. Programmation de DocumentDB est simple, conviviale et ne nécessite pas d’adopter les nouveaux outils ou d’adhérer à des extensions personnalisées JSON ou JavaScript. Vous pouvez accéder à toutes les fonctionnalités de base de données y compris CRUD, requête et JavaScript de traitement via une interface RESTful HTTP simple. DocumentDB s’appuie sur des normes, des langues et des formats existants tout en offrant des fonctionnalités de base de données sur les valeur élevée.

-   **L’indexation automatique :** Par défaut, DocumentDB [indexe automatiquement](documentdb-indexing.md) tous les documents dans la base de données et ne pas attendre ou exiger n’importe quel schéma ou la création d’index secondaire. Ne voulez pas indexer tous les éléments ? Ne vous inquiétez pas, vous pouvez [choisir de ne pas les chemins d’accès dans vos fichiers JSON](documentdb-indexing-policies.md) trop.

##<a name="data-management"></a>Comment DocumentDB gérer des données ?

DocumentDB Azure gère les données JSON au moyen de ressources d’une base de données bien définie. Ces ressources sont répliquées pour une haute disponibilité et sont adressables de manière unique par leur URI logique. DocumentDB offre qu'un HTTP simple en fonction d’un modèle de programmation RESTful pour toutes les ressources. 

Le compte de base de données de DocumentDB est un espace de noms unique qui vous permet d’accéder à DocumentDB d’Azure. Avant de pouvoir créer un compte de base de données, vous devez disposer d’un abonnement Azure, ce qui vous donne accès à un éventail de services Azure. 

Toutes les ressources au sein de DocumentDB sont modélisées et stockés en tant que documents JSON. Les ressources sont gérées en tant qu’éléments, qui sont JSON qui contient les métadonnées des documents, et comme les flux qui sont des collections d’éléments. Jeux d’éléments sont contenus dans leurs flux respectifs.

L’image ci-dessous montre les relations entre les ressources de DocumentDB :

![La relation hiérarchique entre les ressources dans DocumentDB, une base de données NoSQL JSON][1] 

Un compte de base de données se compose d’un ensemble de bases de données, chacun contenant plusieurs collections, chacun d’eux peut contenir des procédures stockées, déclencheurs, UDF, documents et pièces jointes. Une base de données est également associé aux utilisateurs, chacun avec un jeu d’autorisations pour accéder aux divers autres collections, procédures stockées, des déclencheurs, UDF, documents ou pièces jointes. Alors que les bases de données, les utilisateurs, les autorisations et les collections sont définies par le système de ressources avec des schémas connus - contiennent des documents, des procédures stockées, des déclencheurs, des UDF et des pièces jointes arbitraire, défini par l’utilisateur contenu JSON.  

##<a name="develop"></a>Comment puis-je développer des applications avec DocumentDB ?

DocumentDB Azure expose des ressources via une API REST qui peut être appelée par n’importe quel langage capable de faire des demandes HTTP/HTTPS. En outre, DocumentDB propose des bibliothèques de programmation pour plusieurs langues courantes. Ces bibliothèques de simplifient de nombreux aspects de l’utilisation de DocumentDB d’Azure en gérant les détails de la mise en cache des adresses, gestion des exceptions, les tentatives automatiques, etc.. Les bibliothèques sont actuellement disponibles pour les plates-formes et les langues suivantes :  

Télécharger | Documentation
--- | ---
[KIT DE DÉVELOPPEMENT .NET](http://go.microsoft.com/fwlink/?LinkID=402989) | [Bibliothèque de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Kit de développement logiciel Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) | [Bibliothèque de Node.js](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Bibliothèque de Java](http://azure.github.io/azure-documentdb-java/)
[Kit de développement logiciel JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Bibliothèque JavaScript](http://azure.github.io/azure-documentdb-js/)
n/a | [Kit de développement de JavaScript côté serveur](http://azure.github.io/azure-documentdb-js-server/)
[Kit de développement logiciel de Python](https://pypi.python.org/pypi/pydocumentdb) | [Bibliothèque de Python](http://azure.github.io/azure-documentdb-python/)

Créer, lire, mettre à jour et supprimer des opérations au-delà de base, DocumentDB fournit une interface de requête SQL riche pour récupérer les documents JSON et côté serveur prise en charge pour l’exécution de transactions de logique d’application JavaScript. Les interfaces de l’exécution de requêtes et de script sont disponibles par le biais de toutes les bibliothèques de plate-forme ainsi que les API de reste. 

### <a name="sql-query"></a>Requêtes SQL
DocumentDB Azure prend en charge l’interrogation de documents à l’aide d’un langage SQL, qui est associé à une racine dans le système de type JavaScript et les expressions avec prise en charge pour les requêtes relationnelles et hiérarchiques spatiales. Le langage de requête DocumentDB est une interface simple mais puissante d’interroger les documents JSON. Le langage prend en charge un sous-ensemble de la grammaire ANSI SQL et ajoute l’intégration complète de l’objet JavaScript, tableaux, construction d’objets et l’appel de fonction. DocumentDB fournit son modèle de requête sans indications d’indexation ou de n’importe quel schéma explicite du développeur.

Fonctions définies par l’utilisateur (UDF) peut être enregistrées avec DocumentDB et référencées dans le cadre d’une requête SQL, étendant par conséquent la grammaire pour prendre en charge la logique de l’application personnalisée. Ces fichiers UDF sont écrites en tant que programmes JavaScript et exécutées dans la base de données. 

Pour les développeurs .NET, DocumentDB propose également un fournisseur de requête LINQ en tant que partie du [Kit de développement .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Transactions et l’exécution de JavaScript
DocumentDB vous permet d’écrire la logique de l’application en tant que programmes nommés entièrement écrits en JavaScript. Ces programmes sont enregistrées pour une collection et peuvent émettre des opérations de base de données sur les documents au sein d’une collection donnée. JavaScript peut être enregistré pour l’exécution en tant que déclencheur, procédure stockée ou fonction définie par l’utilisateur. Les procédures stockées et les déclencheurs peuvent créer, lire, mettre à jour et supprimer des documents que d’exécuter les fonctions définies par l’utilisateur dans le cadre de la logique d’exécution de requête sans accès en écriture à la collection.

L’exécution de JavaScript dans DocumentDB est modelée d’après les concepts pris en charge par les systèmes de base de données relationnelle, avec JavaScript comme un substitut moderne de Transact-SQL. Toute la logique JavaScript est exécutée dans une transaction ambiante acide avec isolement de capture instantanée. Au cours de son exécution, si le code JavaScript lève une exception, la transaction entière est annulée.

## <a name="next-steps"></a>Étapes suivantes
Vous possédez déjà un compte Azure ? Puis vous pouvez commencer avec DocumentDB dans le [Portail Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) en [créant un compte de base de données DocumentDB](documentdb-create-account.md).

Vous n’avez un compte Azure ? Vous pouvez :

- Inscrivez-vous pour une [version d’essai Azure](https://azure.microsoft.com/free/), qui vous donne des 30 derniers jours et 200 $ pour essayer de tous les services Azure. 
- Si vous avez un abonnement MSDN, vous pouvez 150 [$ dans les crédits Azure gratuits par mois](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) pour les utiliser sur n’importe quel service Azure. 

Ensuite, lorsque vous êtes prêt pour en savoir plus, consultez nos [cursus](https://azure.microsoft.com/documentation/learning-paths/documentdb/) pour parcourir toutes les ressources de formation disponibles. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
