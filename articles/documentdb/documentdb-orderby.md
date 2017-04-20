<properties 
    pageTitle="Tri des données DocumentDB à l’aide de Order By | Microsoft Azure" 
    description="Découvrez comment utiliser ORDER BY dans la DocumentDB des requêtes dans LINQ et SQL et comment spécifier une stratégie d’indexation pour les requêtes ORDER BY." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>Tri des données DocumentDB à l’aide de Order By
Microsoft Azure DocumentDB prend en charge l’interrogation de documents à l’aide de SQL sur les documents JSON. Résultats de la requête peuvent être commandés à l’aide de la clause ORDER BY dans les instructions de requête SQL.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes : 

- Comment interroger avec Order By ?
- Comment configurer une stratégie d’indexation pour Order By ?
- Nouveautés suivant ?

[Exemples](#samples) et un [Forum aux questions](#faq) sont également fournis.

Pour une référence complète sur l’interrogation de SQL, consultez le [didacticiel DocumentDB](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Comment faire pour interroger avec Order By
Comme dans ANSI-SQL, vous pouvez maintenant inclure une clause Order By facultative dans les instructions SQL lors de l’interrogation de DocumentDB. La clause peut inclure un argument ASC/DESC facultatif pour spécifier l’ordre dans lequel les résultats doivent être récupérées. 

### <a name="ordering-using-sql"></a>Commande à l’aide de SQL
Par exemple, voici une requête pour récupérer les livres de 10 premiers dans l’ordre décroissant de leurs titres. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Commande à l’aide de SQL avec filtrage
Vous pouvez commander à l’aide de n’importe quelle propriété imbriquée dans les documents, tels que Books.ShippingDetails.Weight, et vous pouvez spécifier des filtres supplémentaires dans la clause WHERE en combinaison avec Order By comme dans cet exemple :

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Commande à l’aide du fournisseur LINQ pour .NET
À l’aide du Kit de développement .NET version 1.2.0 et version ultérieure, vous pouvez également utiliser la clause OrderBy() ou OrderByDescending() dans les requêtes LINQ comme dans cet exemple :

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB prend en charge le tri avec un seul numérique, de chaîne ou de propriété booléenne par requête, avec les types de requêtes supplémentaires qui seront bientôt disponibles. Voir [Nouveautés suivant](#Whats_coming_next) pour plus de détails.

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurer une stratégie d’indexation pour Order By

Rappelez-vous que les DocumentDB prend en charge deux types d’index (hachage et plage), qui peuvent être définies pour les chemins d’accès/propriétés spécifiques, des types de données (numéros de chaînes /) et les valeurs de précision différente (précision maximale ou une valeur de précision fixe). Dans la mesure où DocumentDB utilise l’indexation par défaut de hachage, vous devez créer une nouvelle collection avec une stratégie d’indexation personnalisée avec plage sur les nombres, les chaînes ou les deux, pour utiliser Order By. 

>[AZURE.NOTE] Index de plage de chaîne ont été introduites sur le 7 juillet 2015 avec l’API REST version 2015-06-03. Pour créer des stratégies pour Order By par rapport à des chaînes, vous devez utiliser le SDK version 1.2.0 de la version 1.1.0 du Kit de développement logiciel Java ou Node.js, Python ou le Kit de développement .NET.
>
>Avant l’API REST version 2015-06-03, stratégie d’indexation de la collection par défaut était hachage de chaînes et les nombres. Cela a été modifié au hachage de chaînes et de plage pour les nombres. 

Pour plus d’informations, voir [DocumentDB des stratégies d’indexation](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>L’indexation de Order By sur toutes les propriétés
Voici comment vous pouvez créer une collection avec « Toutes les limites » indexation de Order By contre toutes les pièces numériques ou chaîne de propriétés qui apparaissent dans les documents JSON qu’il contient. Ici nous substituer le type d’index par défaut pour les valeurs de chaîne pour la plage et à la précision maximale (-1).
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Notez que Order By uniquement renvoie des résultats des types de données (chaîne et numéro) qui sont indexées avec un RangeIndex. Par exemple, si vous disposez de la valeur par défaut de l’indexation de stratégie qui n’en a RangeIndex sur des nombres, une clause Order By par rapport à un chemin d’accès avec des valeurs de chaîne ne retournera aucun document.

### <a name="indexing-for-order-by-for-a-single-property"></a>L’indexation de Order By d’une seule propriété
Voici comment vous pouvez créer une collection avec une indexation de commande par contre que la propriété de titre, qui est une chaîne. Il existe deux chemins d’accès, un pour la propriété Title (« titre / ? ») avec la plage d’indexation et l’autre pour toutes les autres propriétés avec le modèle d’indexation par défaut, qui est le hachage pour les chaînes et la plage de nombres.                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Exemples
Examinons ce [projet exemples de Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) qui montre comment utiliser Order By, y compris la création de l’indexation de stratégies et de la pagination à l’aide de Order By. Les échantillons sont open source et nous vous encourageons à soumettre des demandes d’extraction avec des contributions que pourraient bénéficier les autres développeurs de DocumentDB. Reportez-vous aux [instructions de la Contribution](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) pour obtenir des instructions sur la façon de contribuer.  

## <a name="faq"></a>FAQ

**Quelle est la consommation prévue de la demande d’unité (RU) de commande par requêtes ?**

Dans la mesure où Order By utilise l’index de DocumentDB pour les recherches, le nombre d’unités de demande utilisés par les requêtes Order By sera semblable aux requêtes équivalentes sans Order By. Comme toute autre opération sur DocumentDB, le nombre d’unités de la demande dépend des taille/formes de documents, ainsi que la complexité de la requête. 


**Quel est l’attendu l’indexation surcharge pour Order By ?**

La surcharge de stockage d’indexation sera proportionnelle au nombre de propriétés. Dans le pire des cas, les coûts des index sera de 100 % des données. Il n’y a aucune différence dans la charge de débit (unités de demande) plage/Order By, l’indexation et l’indexation de hachage par défaut.

**Comment pour interroger mes données existantes en DocumentDB à l’aide de Order By ?**

Pour trier les résultats de la requête à l’aide de Order By, vous devez modifier la stratégie d’indexation de la collection pour utiliser un type d’index de plage par rapport à la propriété utilisée pour trier. Consultez [modification d’indexation de stratégie](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Quelles sont les limites actuelles de Order By ?**

Order By peut être spécifié uniquement sur une propriété, soit numérique ou chaîne lorsqu’elle est plage indexée avec la précision maximale (-1).

Vous ne pouvez pas effectuer les opérations suivantes :
 
- Order By avec les propriétés de chaîne interne comme id, _rid et _self (prochainement).
- Order By avec des propriétés dérivées à partir du résultat d’une jointure intra-document (disponible prochainement).
- Commande par plusieurs propriétés (prochainement).
- Order By avec les requêtes sur les bases de données, collections, les utilisateurs, les autorisations ou les pièces jointes (prochainement).
- Order By avec les propriétés calculées, par exemple le résultat d’une expression ou une fonction UDF/intégré-in.

Order By n'est pas actuellement pris en charge pour les requêtes entre partitions lors de l’utilisation de l’Explorateur requête dans Azure portal.

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous recevez une erreur que Order By n'est pas pris en charge, vérifiez que vous utilisez une version du [Kit de développement logiciel](documentdb-sdk-dotnet.md) qui prend en charge par la commande. 

## <a name="next-steps"></a>Étapes suivantes

Une branche dans le [projet samples de Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) et démarrer le classement de vos données ! 

## <a name="references"></a>Références
* [Référence de la requête de DocumentDB](documentdb-sql-query.md)
* [Référence de l’indexation de la stratégie DocumentDB](documentdb-indexing-policies.md)
* [Référence de DocumentDB SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Ordre de DocumentDB par des exemples](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

