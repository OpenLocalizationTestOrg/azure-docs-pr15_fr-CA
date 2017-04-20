<properties 
    pageTitle="Demander des unités de DocumentDB | Microsoft Azure" 
    description="Obtenir des informations sur la façon de comprendre, de spécifier et d’estimer les besoins en unité de demande dans DocumentDB." 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>Demander des unités de DocumentDB
Désormais disponible : DocumentDB [Calculatrice des unités de demande](https://www.documentdb.com/capacityplanner). Pour en savoir plus de [estimer le débit nécessaire](documentdb-request-units.md#estimating-throughput-needs).

![Calculatrice de débit][5]

##<a name="introduction"></a>Introduction
Cet article fournit une vue d’ensemble des unités de demande dans [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :  

-   Que sont les demander des unités et demander des frais ?
-   Comment spécifier les capacités d’unité de demande pour une collection ?
-   Comment estimer la qu'unité de demande de mon application a besoin.
-   Que se passe-t-il si je dépasse la capacité d’unité de demande pour une collection ?


##<a name="request-units-and-request-charges"></a>Unités de la demande et des frais de la demande
DocumentDB offre des performances rapides et prévisibles en *réservation* des ressources pour satisfaire les que besoins de débit de votre application.  Étant donné que l’application de charger et de schémas d’accès changent au fil du temps, DocumentDB vous permet de facilement augmenter ou diminuer la quantité de débit réservé disponible à votre application.

Avec DocumentDB, débit réservé est spécifié en unités de demande de traitement par seconde.  Vous pouvez considérer d’unités de la demande en tant que devise de débit, par laquelle vous *réserver* un montant de garantie des unités de demande disponibles à votre application par seconde.  Chaque opération de DocumentDB - écriture d’un document, d’une requête, mise à jour d’un document - consomme le processeur, mémoire et e/s.  En d’autres termes, chaque opération entraîne des *frais de la demande*, ce qui est exprimée en *unités de demande*.  Comprendre les facteurs qui affectent les frais d’unité de demande, ainsi que des exigences de débit de votre application, vous permet d’exécuter votre application en tant que coût efficacement que possible. 

##<a name="specifying-request-unit-capacity"></a>Spécification de requête unité capacité
Lorsque vous créez une collection de DocumentDB, vous spécifiez le nombre d’unités de demande par seconde (RUs) réservée à la collection.  Une fois la collection créée, l’allocation totale du RUs spécifié est réservée pour une utilisation de la collection.  Chaque collection est garantie dédiée et isolée des caractéristiques de débit.  

Il est important de noter que le DocumentDB fonctionne sur un modèle de réservation ; Autrement dit, vous êtes facturé pour la quantité de débit *réservé* à la collection, indépendamment de la quantité que le débit est activement *utilisée*.  Gardez à l’esprit, toutefois, le qui en tant que le chargement de votre application, données et modification des modèles d’utilisation, vous pouvez évoluer facilement vers la quantité de réservé RUs par le biais de kits de développement logiciel DocumentDB ou à l’aide du [Portail Azure](https://portal.azure.com).  Pour plus d’informations sur le débit d’échelle haut et bas, voir [DocumentDB des niveaux de performance](documentdb-performance-levels.md).

##<a name="request-unit-considerations"></a>Considérations relatives à la demande unité
Lors de l’estimation du nombre d’unités de demande à réserver pour votre collection de DocumentDB, il est important de tenir compte de variables suivantes :

- **Taille du document**. Comme les tailles de document augmentent les unités consommées pour lire ou écrire que les données augmentera.
- **Nombre de propriétés de document**. En supposant que par défaut l’indexation de toutes les propriétés, les unités utilisées pour écrire un document augmente à mesure que de l’augmentation du nombre de propriété.
- **La cohérence des données**. Lorsque vous utilisez des niveaux de cohérence des données de fortes ou d’obsolescence de limitées, des unités supplémentaires seront consommées pour lire des documents.
- **Les propriétés indexées**. Une stratégie d’index sur chaque collection détermine les propriétés qui sont indexées par défaut. Vous pouvez réduire votre consommation d’unités demande en limitant le nombre de propriétés indexées ou par l’indexation différée.
- **L’indexation de documents**. Par défaut de que chaque document est automatiquement indexé, vous allez consommer moins d’unités demande si vous choisissez de ne pas indexer certains de vos documents.
- **Modèles de requête**. La complexité d’une requête a une incidence sur le nombre d’unités demande sont utilisé pour une opération. Le nombre de prédicats, nature des prédicats, projections, nombre de fichiers UDF et la taille de l’ensemble de données source toutes les influent sur le coût des opérations de requête.
- **L’utilisation du script**.  Comme avec les requêtes, les procédures stockées et les déclencheurs consomment des unités de demande en fonction de la complexité des opérations en cours d’exécution. Lorsque vous développez votre application, vérifiez que l’en-tête de facture de demande pour mieux comprendre comment chaque opération consomme la capacité d’unité de demande.

##<a name="estimating-throughput-needs"></a>Estimation des besoins de débit
Une unité de la demande est une mesure normalisée du coût de traitement de la demande. Une unité de demande unique représente la capacité de traitement nécessaire à la lecture (via une liaison automatique ou id), un seul document JSON de 1 Ko consistant à 10 valeurs de propriété unique (à l’exclusion des propriétés système). Une demande de (insert) de créer, de remplacer ou de supprimer le document même consomme plus de traitement à partir du service et, par conséquent plus d’unités demande.   

> [AZURE.NOTE] La ligne de base de l’unité 1 demande pour un document de 1 Ko correspond à une simple opération d’obtention à liaison automatique ou l’id du document.

###<a name="use-the-request-unit-calculator"></a>Utiliser la calculatrice des unités demande
À l’aide de clients bien régler leurs estimations de débit, est une [Calculatrice des unités demande](https://www.documentdb.com/capacityplanner) de web basé pour aider à évaluer les exigences d’unité de demande pour les opérations courantes, notamment :

- Document crée (écrit)
- Lit de document
- Supprime du document
- Mises à jour de document

L’outil inclut également la prise en charge d’estimer les besoins de stockage de données basés sur les exemples de documents que vous fournissez.

À l’aide de l’outil est simple :

1. Télécharger un ou plusieurs documents JSON représentatifs.

    ![Télécharger des documents dans la calculatrice des unités demande][2]

2. Pour estimer les besoins en stockage de données, entrez le nombre total de documents que vous souhaitez stocker.

3. Entrez le numéro du document créer, lire, mettre à jour et supprimer des opérations que vous avez besoin (sur une base par seconde). Pour estimer les frais d’unité demande des opérations de mise à jour de document, télécharger une copie de l’exemple de l’étape 1 ci-dessus qui inclut des mises à jour des champs type de document.  Par exemple, si les mises à jour de document modifient généralement deux propriétés nommées lastLogin et userVisits, puis simplement copier l’exemple de document, mettre à jour les valeurs de ces deux propriétés et télécharger le document copié.

    ![Permet d’entrer les exigences de débit dans la calculatrice des unités demande][3]

4. Cliquez sur Calculer et examiner les résultats.

    ![Demander les résultats de la calculatrice unité][4]

>[AZURE.NOTE]Si vous avez les types de document qui diffèrent considérablement en termes de taille et le nombre de propriétés indexées, puis télécharger un exemple de chaque *type* de document par défaut dans l’outil et calculer les résultats.

###<a name="use-the-documentdb-request-charge-response-header"></a>Utilisez l’en-tête de réponse de frais de requête DocumentDB
Chaque réponse du service DocumentDB comprend un en-tête personnalisé (x-ms-demande-frais) qui contient les unités de la requête utilisées pour la demande. Cet en-tête est également accessible via les kits de développement de DocumentDB. Dans le Kit de développement .NET, RequestCharge est une propriété de l’objet ResourceResponse.  Pour les requêtes, l’Explorateur de la requête DocumentDB dans le portail Azure fournit des informations de frais de demande pour les requêtes exécutées.

![Examen des frais du demandeur dans l’Explorateur de requête][1]

En gardant cela à l’esprit, une méthode d’estimer le montant de débit réservé requis par votre application est d’enregistrer les frais unitaires de demande d’exécuter des opérations courantes contre un document commercial utilisé par votre application et puis d’estimer le nombre d’opérations vous comptez effectuer chaque seconde.  Veillez à mesurer et inclure des requêtes classiques et DocumentDB l’utilisation de script ainsi.

>[AZURE.NOTE]Si vous avez les types de document qui diffèrent considérablement en termes de taille et le nombre de propriétés indexées, puis enregistrez les frais unitaires de demande opération applicable associé à chaque *type* de document par défaut.

Par exemple :

1. Enregistrer les frais unitaires de demande de création (insertion) un document typique. 
2. Enregistrement les frais unitaires de demande de lecture d’un document par défaut.
3. Enregistrement les frais unitaires de demande de mise à jour d’un document typique.
3. Enregistrement les frais unitaires de demande de requêtes de document standard, courant.
4. Enregistrer les frais unitaires demande des scripts personnalisés (procédures stockées, déclencheurs, fonctions définies par l’utilisateur) exploitées par l’application
5. Calculer les unités de demande requis étant données le nombre estimé d’opérations que vous prévoyez pour exécuter chaque seconde.

##<a name="a-request-unit-estimation-example"></a>Un exemple d’estimation unité demande
Examinez le document suivant ~ 1 Ko :

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]Les documents sont réduites dans DocumentDB, afin que le système soit calculée la taille du document ci-dessus est légèrement inférieure à 1 Ko.


Le tableau suivant montre des frais d’unité pour les opérations courantes sur ce document (les frais unitaires approximative demande suppose que le niveau de cohérence de compte est défini à « Session » et que tous les documents sont automatiquement indexés) demande approximative :

Opération|Demander des frais unitaires 
---|---
Créer le document|~ 15 RU 
Lire le document|~ 1 RU
Document de requête par id|~2.5 RU

En outre, ce tableau indique approximativement demande frais d’unité pour les requêtes par défaut utilisés dans l’application :

Requête|Demander des frais unitaires|# de Documents renvoyés
---|---|--- 
Sélectionnez des aliments par id|~2.5 RU|1 
Sélectionnez des aliments par fabricant|~ 7 RU|7
Sélectionnez par groupe d’aliments et de commande en poids|~ 70 RU|100
SELECT top 10 denrées alimentaires dans un groupe d’aliments|~ 10 RU|10

>[AZURE.NOTE]Frais de RU varient en fonction du nombre de documents renvoyés.

Avec ces informations, nous pouvons estimer les exigences RU pour cette application étant donné le nombre d’opérations et nous prévoyons par seconde de requêtes :

Requête d’opération|Nombre estimé par seconde|RUs requis 
---|---|--- 
Créer le document|10|150 
Lire le document|100|100 
Sélectionnez des aliments par fabricant|25|175 
Sélectionnez par groupe d’aliments|10|700 
Sélectionner les 10 premiers|15|Total 150|155|1275

Dans ce cas, nous prévoyons une exigence de débit moyen de 1,275 RU/s.  Arrondi de concurrence la plus proche de 100, nous serait provisionner 1 300 RU/s pour la collection de cette application.

##<a id="RequestRateTooLarge"></a>Limites de débit réservé supérieure à
Rappelez-vous que la consommation d’unités demande est évaluée sous la forme d’un taux par seconde. Pour les applications qui dépassent le taux unitaire demande mis en service pour une collection, les demandes pour cette collection seront accélérés jusqu'à ce que la vitesse tombe sous le niveau réservé. Lorsqu’un accélérateur se produit, le serveur manière préemptive mettre fin à la demande avec RequestRateTooLargeException (code d’état HTTP 429) et renvoyer l’en-tête x-ms-réessayer-après-ms qui indique la durée, en millisecondes, pendant laquelle l’utilisateur doit attendre avant de réessayer la demande.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si vous utilisez les requêtes LINQ et de kit de développement Client .NET, puis la plupart du temps, que vous devrez jamais traiter cette exception, comme la version actuelle du Kit de développement Client .NET intercepte implicitement cette réponse, respecte l’en-tête retry-after spécifiée par le serveur et de tentatives de la demande. Sauf si votre compte est utilisé simultanément par plusieurs clients, la prochaine tentative réussit.

Si vous avez plusieurs clients cumulativement au-dessus du taux de la demande, le comportement des nouvelles tentatives par défaut ne suffisent pas, et le client lèvera une DocumentClientException avec le code d’état 429 à l’application. Dans ce cas, vous pouvez envisager gère le comportement des nouvelles tentatives et logique dans les erreurs de votre application des routines de gestion ou d’augmenter le débit réservé pour la collection.

##<a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le débit réservé avec les bases de données DocumentDB d’Azure, Explorez ces ressources :
 
- [Tarification de la DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gestion de la capacité de DocumentDB](documentdb-manage.md) 
- [Données de modélisation dans DocumentDB](documentdb-modeling-data.md)
- [DocumentDB des niveaux de performances](documentdb-partition-data.md)

Pour en savoir plus sur DocumentDB, consultez la [documentation](https://azure.microsoft.com/documentation/services/documentdb/)de DocumentDB d’Azure. 

Pour vous familiariser avec l’échelle et de tests de performances avec DocumentDB, reportez-vous à la section [performances et tester l’évolutivité avec Azure DocumentDB](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
