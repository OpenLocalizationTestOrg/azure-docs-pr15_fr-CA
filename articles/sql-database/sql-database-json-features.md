<properties
    pageTitle="Fonctionnalités de JSON de base de données SQL Azure | Microsoft Azure"
    description="Une base de données SQL Azure permet d’analyse, des requêtes et des données au format de notation de Notation JSON (JavaScript Object)."
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Mise en route avec les fonctionnalités JSON dans la base de données de SQL Azure

Une base de données SQL Azure vous permet d’analyser et interroger les données représentées dans le format JavaScript Object Notation [(JSON)](http://www.json.org/) et exporter vos données relationnelles en tant que texte JSON.

JSON est un format de données les plus courants utilisé pour échanger des données dans des applications web moderne et mobiles. JSON est également utilisé pour stocker des données semi-structurées dans les fichiers journaux ou de bases de données NoSQL comme [DocumentDB d’Azure](https://azure.microsoft.com/services/documentdb/). De nombreux services de web reste renvoient des résultats au format texte JSON ou acceptent des données au format JSON. Des services Azure plus comme [Recherche d’Azure](https://azure.microsoft.com/services/search/), [Le stockage Azure](https://azure.microsoft.com/services/storage/)et [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) ont les points de terminaison REST qui retournent ou utilisent JSON.

Une base de données SQL Azure permet de travailler facilement avec des données JSON et intégrer votre base de données avec les services modernes.

## <a name="overview"></a>Vue d’ensemble

Une base de données SQL Azure fournit les fonctions suivantes pour travailler avec des données JSON :

![Fonctions JSON](./media/sql-database-json-features/image_1.png)

Si vous avez le texte JSON, vous pouvez extraire des données à partir de JSON ou vérifiez que JSON est correctement formaté à l’aide de fonctions intégrées, [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)et [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). La fonction [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) vous permet de mettre à jour la valeur à l’intérieur du texte JSON. Plus avancés interrogation et analysis, fonction [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) pouvez transformer un tableau d’objets JSON dans un ensemble de lignes. Toutes les requêtes SQL peuvent être exécutées sur le jeu de résultats renvoyé. Enfin, voici une clause [De JSON](https://msdn.microsoft.com/library/dn921882.aspx) qui vous permet de mettre en forme les données stockées dans vos tables relationnelles en tant que texte JSON.

## <a name="formatting-relational-data-in-json-format"></a>Mise en forme de données relationnelles au format JSON
Si vous disposez d’un service web que prend les données à partir de la base de données de la couche et fournissant une réponse en JSON formater ou infrastructures JavaScript côté client ou les bibliothèques qui acceptent des données au format JSON, vous pouvez mettre en forme le contenu de votre base de données au format JSON directement dans une requête SQL. Vous n’avez plus avez écrire le code d’application qui met en forme les résultats de base de données de SQL Azure en tant que JSON ou incluez une bibliothèque de sérialisation JSON pour convertir les résultats de la requête sous forme de tableau et puis sérialiser des objets au format JSON. Au lieu de cela, vous pouvez utiliser la clause FOR JSON pour formater les résultats de la requête SQL en tant que JSON dans la base de données de SQL Azure et l’utiliser directement dans votre application.

Dans l’exemple suivant, les lignes de la table Sales.Customer sont mis en forme en tant que JSON en utilisant la clause FOR JSON :

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

La clause pour le chemin de JSON met en forme les résultats de la requête en tant que texte JSON. Les noms de colonne sont utilisées comme clés, tandis que les valeurs de cellule sont générés en tant que valeurs JSON :

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Le jeu de résultats est mis en forme en tant que JSON tableau où chaque ligne est mis en forme comme un objet distinct de JSON.

Chemin d’accès indique que vous pouvez personnaliser le format de sortie de votre résultat JSON dans les alias de colonne à l’aide de la notation par points. La requête suivante modifie le nom de la clé « CustomerName » dans le format JSON et place les numéros de téléphone et de télécopie dans le sous-objet « Contact » :

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Voici à quoi ressemble le résultat de cette requête :

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Dans cet exemple nous a retourné un objet JSON plutôt qu’un tableau en spécifiant l’option [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . Vous pouvez utiliser cette option si vous savez que vous renvoyez un objet unique par requête.

La valeur principale de la clause FOR JSON est qu’il vous permet de renvoyer des données hiérarchiques complexes à partir de votre base de données au format JSON objets imbriqués ou tableaux. L’exemple suivant montre comment inclure des commandes qui appartiennent au client sous la forme d’un tableau imbriqué de commandes :

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Au lieu d’envoyer les séparer des requêtes pour obtenir des données client et pour extraire une liste de commandes associées, vous pouvez obtenir toutes les données nécessaires à l’aide d’une requête unique, comme indiqué dans l’exemple de sortie suivant :

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Travail avec des données JSON

Si vous n’avez pas strictement structuré de données, si vous avez des sous-objets complexes, des tableaux ou des données hiérarchiques, ou si vos structures de données évoluent dans le temps, le format JSON peut vous aider pour représenter une structure de données complexes.

JSON est un format textuel qui peut être utilisé comme tout autre type de chaîne dans la base de données de SQL Azure. Vous pouvez envoyer ou stocker des données JSON comme un NVARCHAR standard :

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Les données JSON utilisées dans cet exemple sont représentées à l’aide du type nvarchar (max). JSON pouvant être inséré dans cette table ou en tant qu’argument de la procédure stockée à l’aide de la syntaxe de Transact-SQL standard comme indiqué dans l’exemple suivant :

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

N’importe quel langage côté client ou une bibliothèque qui fonctionne avec les données de chaîne dans la base de données de SQL Azure fonctionne également avec des données JSON. JSON peut être stockée dans une table qui prend en charge le type NVARCHAR, par exemple une table de mémoire optimisée ou une version de système. JSON n’introduit pas une contrainte dans le code côté client ou dans la couche de base de données.

## <a name="querying-json-data"></a>Interrogation des données JSON

Si vous avez des données au format JSON, stockée dans des tables SQL d’Azure, fonctions JSON vous permettent d’utiliser ces données dans n’importe quelle requête SQL.

Fonctions JSON qui sont disponibles dans Azure SQL de base de données permettent vous traitez les données mises en forme en tant que JSON comme tout autre type de données SQL. Vous pouvez facilement extraire des valeurs à partir du texte JSON et utiliser des données JSON dans une requête :

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

La fonction JSON_VALUE extrait une valeur de texte JSON stockée dans la colonne de données. Cette fonction utilise un chemin d’accès de type JavaScript pour faire référence à une valeur en texte JSON à extraire. La valeur extraite peut être utilisée dans n’importe quelle partie de requête SQL.

La fonction JSON_QUERY est similaire à JSON_VALUE. Contrairement à JSON_VALUE, cette fonction extrait le sous-objet complexe tels que des tableaux ou des objets qui sont placés dans le texte JSON.

La fonction JSON_MODIFY vous permet de spécifier le chemin d’accès de la valeur dans le texte JSON qui doit être mis à jour, ainsi que d’une nouvelle valeur qui remplacera l’ancienne. De cette façon vous pouvez facilement mettre à jour texte JSON sans nouvelle analyse en vue de la structure entière.

Étant donné que JSON est stocké dans un texte standard, il n’y a aucune garantie que les valeurs stockées dans les colonnes de texte sont correctement mis en forme. Vous pouvez vérifier que le texte stocké dans la colonne JSON est correctement formaté à l’aide des contraintes de vérification de base de données de SQL Azure standard et la fonction ISJSON :

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Si le texte d’entrée est correctement formaté JSON, la fonction ISJSON renvoie la valeur 1. Sur chaque insertion ou de mise à jour de colonne JSON, cette contrainte vérifiera que nouvelle valeur texte n’est pas JSON mal formé.

## <a name="transforming-json-into-tabular-format"></a>Transformation de JSON au format tabulaire

Une base de données SQL Azure vous permet également de transformer des collections de JSON dans les données tabulaires JSON de format et de charge ou de la requête.

OPENJSON est une fonction table qui analyse le texte JSON, localise un tableau d’objets JSON, parcourt les éléments du tableau et renvoie une ligne dans le résultat de sortie pour chaque élément du tableau.

![JSON sous forme de tableau](./media/sql-database-json-features/image_2.png)

Dans l’exemple ci-dessus, nous pouvons spécifier où placer le tableau JSON qui doit être ouverts (dans le $. Chemin de commandes), les colonnes doivent être retournées en tant que résultat et où trouver les valeurs JSON qui seront renvoyées sous forme de cellules.

Nous pouvons transformer un tableau JSON dans le @orders variable dans un ensemble de lignes, analyser le jeu de résultats, ou insérer des lignes dans un tableau standard :

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
La collection de commandes mis en forme comme un tableau JSON et fourni comme un paramètre à la procédure stockée peut être analysé et inséré dans la table commandes.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment intégrer JSON dans votre application, consultez ces ressources :

- [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [Documentation MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
- [Channel 9 vidéo](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Pour en savoir plus sur les différents scénarios d’intégration de JSON dans votre application, consultez les démonstrations de cette [vidéo de Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou rechercher un scénario correspondant à votre cas d’utilisation de [billets de Blog de JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).
