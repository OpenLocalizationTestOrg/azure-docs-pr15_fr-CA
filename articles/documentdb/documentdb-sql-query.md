<properties 
    pageTitle="Syntaxe SQL et SQL de requête pour DocumentDB | Microsoft Azure" 
    description="Obtenir des informations sur la syntaxe SQL, les concepts de base de données et les requêtes SQL pour DocumentDB, une base de données NoSQL. SQL peut être utilisé comme un langage de requête JSON dans DocumentDB." 
    keywords="la syntaxe SQL, requête sql, requêtes sql, langage de requête json, les concepts de base de données et les requêtes sql, fonctions d’agrégation"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>Les requêtes SQL et la syntaxe SQL dans DocumentDB
Microsoft Azure DocumentDB prend en charge l’interrogation de documents à l’aide de SQL (langage SQL) comme langage de requête un JSON. DocumentDB est réellement exempt de schéma. Du fait de son engagement dans le modèle de données JSON directement dans le moteur de base de données, il fournit l’indexation automatique de documents JSON sans schéma explicite ou la création d’index secondaires. 

Lors de la conception du langage de requête pour DocumentDB, nous avions deux objectifs à l’esprit :

-   Au lieu d’inventer un nouveau langage de requête JSON, nous souhaitions prise en charge de SQL. SQL est un des langages de requête plus familiers et les plus courants. DocumentDB SQL fournit un modèle de programmation formel pour des requêtes riches sur des documents JSON.
-   Comme JSON document capable d’exécuter JavaScript directement dans le moteur de base de données, ce que nous voulons utiliser le modèle de programmation de JavaScript comme base pour notre langage de requête. Le SQL DocumentDB est associé à une racine dans le système de types de JavaScript, évaluation de l’expression et appel de la fonction. Ce tour fournit un modèle de programmation naturel pour les projections relationnelles, la navigation hiérarchique entre documents JSON, auto-jointures, recherches spatiales et l’appel de fonctions définies par l’utilisateur (UDF) d’entièrement écrit en JavaScript, parmi d’autres fonctionnalités. 

Nous pensons que ces fonctionnalités sont essentiels pour réduire le frottement entre l’application et la base de données et sont essentielles pour la productivité des développeurs.

Nous vous recommandons de mise en route en regardant la vidéo suivante, où Aravind Ramachandran montre les fonctionnalités d’interrogation de DocumentDB, et en visitant notre [Site avant-première de requête](http://www.documentdb.com/sql/demo), où vous pouvez essayer de DocumentDB et exécuter des requêtes SQL par rapport à notre groupe de données.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Ensuite, revenez à cet article, où nous allons commencer avec un didacticiel de requête SQL qui vous guide à travers certains documents simples de JSON et les commandes SQL.

## <a name="getting-started-with-sql-commands-in-documentdb"></a>Mise en route avec des commandes SQL dans DocumentDB
Pour voir les DocumentDB SQL au travail, nous allons commencer avec quelques documents JSON simples et guident tout au long de certaines requêtes simples. Pensez à ces deux documents JSON sur deux familles. Notez qu’avec DocumentDB, nous n’avez pas besoin de créer explicitement des schémas ni index secondaire. Il nous faut simplement insérer les documents JSON à une collection de DocumentDB et les interroger par la suite. Nous avons ici un JSON simple document pour la famille Andersen, les parents, enfants (et leurs animaux), des informations d’adresse et d’enregistrement. Le document possède des propriétés imbriquées, nombres, valeurs booléennes, tableaux et chaînes. 

**Document**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


Voici un second document avec une légère différence – `givenName` et `familyName` sont utilisés à la place de `firstName` et `lastName`.

**Document**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



Maintenant nous allons essayer quelques requêtes sur ces données de comprendre certains aspects essentiels de la DocumentDB SQL. Par exemple, la requête suivante renvoie les documents dont le champ id correspond à `AndersenFamily`. Dans la mesure où il s’agit d’un `SELECT *`, le résultat de la requête est le document JSON complet :

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Examinons maintenant le cas où il faut remettre en forme de la sortie JSON dans une forme différente. Cette requête projette un objet JSON avec deux champs sélectionnés, Name et City, lors de la ville de l’adresse portant le même nom que l’état. Dans ce cas, correspond à « NY, NY ».

**Requête**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Résultats**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


La requête suivante renvoie tous les noms donnés des enfants de la famille dont l’id correspond à `WakefieldFamily` commandée par la ville de résidence.

**Requête**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Résultats**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Nous souhaitons attirer l’attention sur quelques aspects importants du langage de requête DocumentDB les exemples que nous avons vu jusqu’ici :  
 
-   DocumentDB SQL fonctionnant sur des valeurs JSON, il traite avec une arborescence en forme d’entités au lieu de lignes et de colonnes. Par conséquent, le langage permet de faire référence à des nœuds de l’arborescence à une quelconque profondeur arbitraire, comme `Node1.Node2.Node3…..Nodem`, semblable à relationnelle SQL faisant référence à la référence des deux parties de `<table>.<column>`.   
-   Le langage de requête structuré fonctionne avec les données sans schéma. Par conséquent, le système de type doit être lié de manière dynamique. La même expression peut générer différents types de documents différents. Le résultat d’une requête est une valeur JSON valide, mais il n’est pas garanti d’un schéma fixe.  
-   DocumentDB prend uniquement en charge les documents JSON stricts. Cela signifie que le système de type et les expressions sont limitées à traiter uniquement avec les types JSON. Reportez-vous à la [spécification de JSON](http://www.json.org/) pour plus de détails.  
-   Une collection de DocumentDB est un conteneur de schéma libres de documents JSON. Les relations dans les entités au sein et entre des documents dans une collection de données capturées implicitement par la relation contenant-contenu et non par des relations de clé étrangères et de clé primaire. Il s’agit d’un aspect important de noter au vu des jointures internes au document décrits plus loin dans cet article.

## <a name="documentdb-indexing"></a>L’indexation de DocumentDB

Avant d’entrer dans la syntaxe de DocumentDB SQL, il convient d’Explorer la structure d’indexation dans DocumentDB. 

L’objectif de l’index de la base de données est de servir les requêtes dans leurs diverses formes et de formes avec une consommation minimale des ressources (telles qu’UC et d’entrée/sortie) tout en assurant le bon débit et à faible latence. Souvent, le choix de l’index droit pour l’interrogation d’une base de données nécessite beaucoup de planification et l’expérimentation. Cette approche pose un défi pour les bases de données sans schéma où les données n’est pas conforme à un schéma strict et évoluent rapidement. 

Par conséquent, lorsque nous avons conçu le DocumentDB de l’indexation de sous-système, nous définissons les objectifs suivants :

-   Index des documents sans nécessiter de schéma : le sous-système d’indexation ne nécessitent des informations de schéma pas ou ne souhaite pas faire de suppositions sur le schéma des documents. 

-   Prise en charge des requêtes relationnelles et hiérarchiques efficaces et riches : l’index prend en charge le langage de requête DocumentDB efficace, y compris la prise en charge des projections hiérarchiques et relationnelles.

-   Prise en charge de requêtes cohérentes constitué un volume constant d’écritures : des charges de travail élevée en écriture débit avec les requêtes cohérentes, l’index est mis à jour de façon incrémentielle, efficacement et en ligne face à un volume constant d’écritures. La mise à jour de l’index cohérente est cruciale pour servir les requêtes au niveau de la cohérence, dans lequel l’utilisateur a configuré le service de document.

-   Prise en charge de l’architecture mutualisée : étant donné le modèle en fonction de réservation pour la gouvernance de ressources entre les locataires, des mises à jour de l’index sont effectuées dans le budget des ressources système (processeur, mémoire et entrées/sorties par seconde) affectée par le réplica. 

-   L’efficacité du stockage : rapport coût/efficacité, la surcharge de stockage sur disque de l’index est limitée et prévisibles. Ceci est crucial car DocumentDB permet au développeur de faire des compromis en fonction des coûts entre les index en charge en ce qui concerne les performances des requêtes.  

Les [exemples de DocumentDB](https://github.com/Azure/azure-documentdb-net) sur le site MSDN pour voir exemples montrant comment configurer la stratégie d’indexation pour une collection. Passons maintenant les détails de la syntaxe DocumentDB SQL.


## <a name="basics-of-a-documentdb-sql-query"></a>Notions de base d’une requête de DocumentDB SQL
Chaque requête se compose d’une clause SELECT et une option de la clause WHERE par les normes ANSI-SQL. En règle générale, pour chaque requête, la source dans la clause FROM est énumérée. Puis le filtre dans la clause WHERE est appliqué sur la source pour récupérer un sous-ensemble de documents JSON. Enfin, la clause SELECT est utilisée pour les valeurs JSON demandés dans la liste de sélection de projet.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>La clause FROM
Le `FROM <from_specification>` clause est facultative, sauf si la source est filtrée ou projetée ultérieurement dans la requête. L’objectif de cette clause doit spécifier la source de données sur laquelle la requête doit fonctionner. La collection entière est généralement la source, mais on peut spécifier un sous-ensemble de la collection à la place. 

Une requête comme `SELECT * FROM Families` indique que la collection entière de la famille est la source sur lequel vous souhaitez énumérer. Un identificateur spécial racine peut être utilisé pour représenter la collection au lieu d’utiliser le nom de la collection. La liste suivante contient les règles qui sont appliquées par requête :

- La collection peut être un alias, tel que `SELECT f.id FROM Families AS f` ou simplement `SELECT f.id FROM Families f`. Ici `f` est l’équivalent de `Families`. `AS`est un mot-clé facultatif à l’alias de l’identificateur.

-   Notez qu’une fois avec un alias, la source d’origine ne peut pas être lié. Par exemple, `SELECT Families.id FROM Families f` est syntaxiquement incorrecte dans la mesure où l’identificateur « Famille » ne peut pas être résolu plus.

-   Toutes les propriétés devant être référencé doivent être qualifiées complet. En l’absence de conformité de schéma stricte, cela est appliquée pour éviter les liaisons ambiguës. Par conséquent, `SELECT id FROM Families f` est syntaxiquement incorrecte depuis la propriété `id` n’est pas lié.
    
### <a name="sub-documents"></a>Documents de sous-dossiers
La source peut également être réduite à un sous-ensemble plus petit. Par exemple, l’énumération des sous-arborescence dans chaque document, la racine secondaire pourrait alors devenir la source, comme indiqué dans l’exemple suivant.

**Requête**

    SELECT * 
    FROM Families.children

**Résultats**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Bien que l’exemple ci-dessus a utilisé un tableau comme source, un objet peut également servir comme source, qui est ce qui est indiqué dans l’exemple suivant. Toute valeur JSON valide (non ne pas définie) qui se trouve dans la source sera considéré pour l’inclusion dans le résultat de la requête. Si vous n’ont pas certaines familles un `address.state` valeur, ils sont exclus du résultat de requête.

**Requête**

    SELECT * 
    FROM Families.address.state

**Résultats**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>Clause WHERE
La clause WHERE (**`WHERE <filter_condition>`**) est facultatif. Il spécifie l’ou les conditions que les documents JSON fournis par la source doivent satisfaire pour être inclus dans le résultat. N’importe quel document JSON doit évaluer les conditions spécifiées à « true » à prendre en compte pour le résultat. La clause WHERE est utilisée par la couche de l’index afin de déterminer le plus petit sous-ensemble absolu des documents source qui peut faire partie du résultat. 

La requête suivante demande des documents contenant une propriété de nom dont la valeur est `AndersenFamily`. Tout autre document qui ne dispose pas d’une propriété name, ou lorsque la valeur ne correspond pas à `AndersenFamily` est exclu. 

**Requête**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


L’exemple précédent a montré une requête d’égalité simples. DocumentDB SQL prend également en charge une variété d’expressions scalaires. Les plus communément utilisés sont des expressions binaires et unaire. Les références de propriété de l’objet JSON de source sont également des expressions valides. 

Les opérateurs binaires suivants sont actuellement pris en charge et peuvent être utilisés dans les requêtes, comme indiqué dans les exemples suivants :  
<table>
<tr>
<td>Opérations arithmétiques</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Au niveau du bit</td>    
<td>|, &, ^, <<, >>, >>> (zéro-remplissage décalage vers la droite) </td>
</tr>
<tr>
<td>Logique</td>
<td>ET, OU, PAS</td>
</tr>
<tr>
<td>Comparaison</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Chaîne</td> 
<td>|| (concaténer)</td>
</tr>
</table>  

Examinons certaines requêtes utilisant des opérateurs binaires.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Les opérateurs unaires +,-, ~ pas sont également pris en charge et peut être utilisé à l’intérieur des requêtes, comme indiqué dans l’exemple suivant :

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Outre la binaire et les opérateurs unaires, les références de propriété sont également autorisées. Par exemple, `SELECT * FROM Families f WHERE f.isRegistered` renvoie le document JSON qui contient la propriété `isRegistered` où la valeur de la propriété est égale à la JSON `true` valeur. Toutes les autres valeurs (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>`, etc.) au document source exclu du résultat des prospects. 

### <a name="equality-and-comparison-operators"></a>Opérateurs d’égalité et de comparaison
Le tableau suivant montre le résultat de comparaisons d’égalité dans DocumentDB SQL entre deux types JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Non défini</strong>
         </td>
         <td valign="top">
            <strong>Valeur null</strong>
         </td>
         <td valign="top">
            <strong>Valeur booléenne</strong>
         </td>
         <td valign="top">
            <strong>Numéro de</strong>
         </td>
         <td valign="top">
            <strong>Chaîne</strong>
         </td>
         <td valign="top">
            <strong>Objet</strong>
         </td>
         <td valign="top">
            <strong>Tableau</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Non défini<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Valeur null<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Valeur booléenne<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Numéro de<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Chaîne<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objet<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Tableau<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
      </tr>
   </tbody>
</table>

Pour les autres opérateurs de comparaison tels que >, > =, ! =, < et < =, les règles suivantes s’appliquent :   

-   Comparaison entre types entraîne non défini.
-   Comparaison entre deux objets ou deux tableaux de résultats de non défini.   

Si le résultat de l’expression scalaire dans le filtre est non définie, le document correspondant ne serait pas inclus dans le résultat, dans la mesure où non défini n’atteint pas logiquement à « true ».

### <a name="between-keyword"></a>ENTRE le mot clé
Vous pouvez également utiliser le mot clé BETWEEN pour exprimer des requêtes sur des plages de valeurs, comme dans ANSI SQL. ENTRE peut être utilisée avec des chaînes ou des nombres.

Par exemple, cette requête renvoie tous les documents de famille dans lequel grade du premier enfant est entre 1-5 (tous deux inclus). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Contrairement à dans ANSI-SQL, vous pouvez également utiliser la clause BETWEEN dans la clause FROM, comme dans l’exemple suivant.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Pour le temps d’exécution requête plus rapides, pensez à créer une stratégie d’indexation qui utilise un type d’index de plage par rapport à des propriétés numériques/les chemins sont filtrés dans la clause BETWEEN. 

La principale différence entre DocumentDB et ANSI SQL est que vous pouvez exprimer des requêtes de plage par rapport aux propriétés de types différents, par exemple, vous pouvez avoir « qualité » est un nombre (5) dans des documents et des chaînes dans d’autres (« grade4 »). Dans ces cas, comme dans JavaScript, une comparaison entre deux résultats différents types dans « non défini » et le document sera ignorée.

### <a name="logical-and-or-and-not-operators"></a>Logique (et, ou et pas) les opérateurs
Opérateurs logiques avec des valeurs Boolean. Les tables de vérité logiques pour ces opérateurs sont présentées dans les tableaux ci-dessous.

OU|True|False|Non défini
---|---|---|---
True|True|True|True
False|True|False|Non défini
Non défini|True|Non défini|Non défini

ET|True|False|Non défini
---|---|---|---
True|True|False|Non défini
False|False|False|False
Non défini|Non défini|False|Non défini

PAS|  |
---|---
True|False
False|True
Non défini|Non défini

### <a name="in-keyword"></a>Mot clé
Le mot clé IN peut être utilisé pour vérifier si une valeur spécifiée correspond à la valeur dans une liste. Par exemple, cette requête renvoie tous les documents de famille dont l’id est « WakefieldFamily » ou « AndersenFamily ». 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Cet exemple renvoie tous les documents où l’état est une des valeurs spécifiées.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Ternaire ( ?) et des opérateurs de fusion ( ?)
Les opérateurs de ternaire et de fusion peuvent être utilisés pour créer des expressions conditionnelles, similaires à des langages de programmation les plus courants tels que C# et JavaScript. 

L’opérateur ternaire ( ?) peut être très utile lors de la construction de nouvelles propriétés JSON à la volée. Par exemple, maintenant vous pouvez écrire des requêtes pour classer les niveaux de la classe dans un format lisible humain comme débutant/intermédiaire/Avancé comme illustré ci-dessous.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Vous pouvez également imbriquer les appels à l’opérateur comme dans la requête ci-dessous.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Comme avec les autres opérateurs de requête, si les propriétés référencées dans l’expression conditionnelle sont manquantes dans un document, ou si les types comparés sont différents, puis ces documents seront exclus dans les résultats de la requête.

L’opérateur de fusion ( ?) peut être utilisé pour contrôler efficacement la présence d’une propriété (également appelé est défini) dans un document. Cela est utile lors de l’interrogation contre semi-structurés ou des données de types différents. Par exemple, cette requête renvoie le « nom » si elle existe, ou le nom de « famille » si elle n’est pas présent.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>Accesseur de propriété entre guillemets
Vous pouvez également accéder aux propriétés à l’aide de l’opérateur de propriété entre guillemets `[]`. Par exemple, `SELECT c.grade` et `SELECT c["grade"]` sont équivalentes. Cette syntaxe est utile lorsque vous avez besoin d’échappement d’une propriété qui contient des espaces, des caractères spéciaux, ou en cas de partager le même nom qu’un mot réservé ou un mot clé SQL.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>Clause SELECT
La clause SELECT (**`SELECT <select_list>`**) est obligatoire et spécifie les valeurs qui seront extraites à partir de la requête, tout comme dans ANSI-SQL. Le sous-ensemble est été filtré sur les documents d’origine sont transmises à la phase de projection, où les valeurs JSON spécifiés sont récupérés et construit un nouvel objet JSON, pour chaque entrée passée sur celui-ci. 

L’exemple suivant montre une requête sélection classique. 

**Requête**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propriétés imbriquées
Dans l’exemple suivant, nous allons projeter deux propriétés imbriquées `f.address.state` et `f.address.city`.

**Requête**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projection prend également en charge les expressions de JSON, comme illustré dans l’exemple suivant.

**Requête**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Examinons à présent le rôle de `$1` ici. Le `SELECT` clause doit créer un objet JSON et dans la mesure où aucune clé n’est fourni, nous utilisons les noms de variables implicites argument commençant par `$1`. Par exemple, cette requête renvoie les deux variables de l’argument implicite, intitulées `$1` et `$2`.

**Requête**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Création d’alias
Maintenant nous allons étendre l’exemple ci-dessus avec alias explicites de valeurs. Comme c’est le mot clé utilisé pour un alias. Notez qu’il est facultatif comme indiqué lors de la projection de la deuxième valeur en tant que `NameInfo`. 

Dans le cas où une requête a deux propriétés portant le même nom, alias doit servir à renommer un ou deux des propriétés afin qu’elles sont désambiguïsés dans le résultat prévu.

**Requête**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expressions scalaires
Outre les références de propriété, la clause SELECT prend également en charge les expressions scalaires, telles que des constantes, des expressions arithmétiques, expressions logiques, etc.. Par exemple, voici une requête « Hello World » simple.

**Requête**

    SELECT "Hello World"

**Résultats**

    [{
      "$1": "Hello World"
    }]


Voici un exemple plus complexe qui utilise une expression scalaire.

**Requête**

    SELECT ((2 + 11 % 7)-2)/3   

**Résultats**

    [{
      "$1": 1.33333
    }]


Dans l’exemple suivant, le résultat de l’expression scalaire est une valeur booléenne.

**Requête**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Résultats**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Création d’objet et de tableau
Une autre fonction clé de DocumentDB SQL est la création d’un tableau/objet. Dans l’exemple précédent, notez que nous avons créé un nouvel objet JSON. De même, un peut également construire des tableaux comme indiqué dans les exemples suivants.

**Requête**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Résultats**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>Mot clé VALUE
Le mot clé **VALUE** fournit un moyen de retourner la valeur JSON. Par exemple, la requête ci-dessous renvoie la valeur scalaire `"Hello World"` au lieu de `{$1: "Hello World"}`.

**Requête**

    SELECT VALUE "Hello World"

**Résultats**

    [
      "Hello World"
    ]


La requête suivante renvoie la valeur JSON sans le `"address"` étiquette dans les résultats.

**Requête**

    SELECT VALUE f.address
    FROM Families f 

**Résultats**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

L’exemple suivant étend ici pour montrer comment renvoyer les valeurs primitives JSON (le niveau feuille de l’arborescence JSON). 

**Requête**

    SELECT VALUE f.address.state
    FROM Families f 

**Résultats**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* Opérateur
L’opérateur spécial (*) est pris en charge pour le projet en tant que-est. Lorsqu’il est utilisé, il doit être le seul champ prévu. Lors d’une requête telle que `SELECT * FROM Families f` est valide, `SELECT VALUE * FROM Families f ` et `SELECT *, f.id FROM Families f ` ne sont pas valides.

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>Opérateur TOP
Le mot clé TOP peut être utilisé pour limiter le nombre de valeurs à partir d’une requête. HAUT est utilisé conjointement avec la clause ORDER BY, le jeu de résultats est limité au premier nombre N de valeurs ordonnés ; Sinon, il retourne les N premiers résultats dans un ordre non défini. Pour obtenir les meilleurs résultats, dans une instruction SELECT, toujours utiliser une clause ORDER BY avec la clause TOP. Il s’agit de la seule façon prévisible indiquer quelles lignes sont affectées par la haut. 


**Requête**

    SELECT TOP 1 * 
    FROM Families f 

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

HAUT peut être utilisé avec une valeur constante (comme indiqué ci-dessus) ou avec la valeur d’une variable à l’aide de requêtes paramétrées. Pour plus d’informations, veuillez consulter ci-dessous les requêtes paramétrées.

## <a name="order-by-clause"></a>Clause ORDER BY
Comme dans ANSI-SQL, vous pouvez inclure une clause Order By facultative lors de l’interrogation. La clause peut inclure un argument ASC/DESC facultatif pour spécifier l’ordre dans lequel les résultats doivent être récupérées. Pour un examen plus détaillé Order By, reportez-vous à la [Commande DocumentDB par la procédure](documentdb-orderby.md).

Par exemple, voici une requête qui extrait les familles dans l’ordre du nom de la ville résidence.

**Requête**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**Résultats**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

Et Voici une requête qui extrait les familles dans l’ordre de la date de création, qui est stockée sous la forme d’un nombre représentant l’époque de temps, c'est-à-dire, temps écoulé depuis le 1er janvier 1970 en secondes.

**Requête**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**Résultats**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>Les requêtes SQL et les concepts de base de données avancées
### <a name="iteration"></a>Itération
Une nouvelle construction a été ajoutée via le mot-clé **IN** dans SQL DocumentDB pour fournir la prise en charge pour itérer sur des tableaux JSON. La source de prend en charge pour l’itération. Commençons par l’exemple suivant :

**Requête**

    SELECT * 
    FROM Families.children

**Résultats**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Maintenant examinons une autre requête qui effectue l’itération sur les enfants de la collection. Notez la différence dans le tableau de sortie. Cet exemple montre comment fractionner `children` et aplatit les résultats dans un tableau unique.  

**Requête**

    SELECT * 
    FROM c IN Families.children

**Résultats**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Cela peut plus servir à filtrer sur chaque entrée du tableau, comme illustré dans l’exemple suivant.

**Requête**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Résultats**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>Jointures
Dans une base de données relationnelle, la nécessité de jointure entre les tables est très importante. Il est le corollaire logique à la conception des schémas normalisées. En revanche, DocumentDB traite le modèle de données dénormalisées de documents de schéma-free. Ceci est l’équivalent logique d’une « jointure réflexive ».

La syntaxe qui prend en charge de la langue est la jointure de la jointure < from_source2 > < from_source1 >... JOINTURE < from_sourceN >. Globalement, renvoie un ensemble de **N**- uplets (tuple avec les valeurs de **N** ). Chaque tuple a des valeurs produites par une itération de tous les alias de collection sur leurs jeux respectifs. En d’autres termes, il s’agit d’un produit cartésien des jeux participant à la jointure.

Les exemples suivants illustrent le fonctionne de la clause de jointure. Dans l’exemple suivant, le résultat est vide car le produit croisé de chaque document à partir de la source et un ensemble vide est vide.

**Requête**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Résultats**  

    [{
    }]


Dans l’exemple suivant, la jointure est entre la racine du document et le `children` racine secondaire. Il s’agit d’un produit croisé entre deux objets JSON. Le fait que les enfants est un tableau n’est pas efficace de la jointure dans la mesure où nous avons affaire à une seule racine qui est le tableau d’enfants. Par conséquent le résultat contient uniquement deux résultats, dans la mesure où le produit croisé de chaque document avec le tableau donne exactement un seul document.

**Requête**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**Résultats**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


L’exemple suivant illustre une jointure plus conventionnelle :

**Requête**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Résultats**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



La première chose à noter est que la `from_source` de la **jointure** clause est un itérateur. Par conséquent, le flux dans ce cas est la suivante :  

-   Développez chaque élément enfant **c** dans le tableau.
-   Appliquer un produit croisé avec la racine du document **f** avec chaque élément enfant **c** a été aplatie dans la première étape.
-   Enfin, la propriété de nom d’objet **f** racine uniquement le projet. 

Le premier document (`AndersenFamily`) contient un seul élément enfant, afin que le jeu de résultats contient un seul objet correspondant à ce document. Le deuxième document (`WakefieldFamily`) contient deux enfants. Par conséquent, le produit croisé génère un objet distinct pour chaque enfant, ce qui entraîne deux objets, un pour chaque enfant correspondant à ce document. Notez que les champs de la racine de ces deux documents seront identiques, tout comme vous pouvez vous y attendre dans un produit croisé.

L’utilitaire réel de la jointure est en tuples de formulaire depuis le produit vectoriel dans une forme qui autrement seraient difficile au projet. En outre, comme nous le verrons dans l’exemple ci-dessous, vous pouvez filtrer sur la combinaison d’un tuple que vous permet de l’utilisateur a choisi une condition satisfaite par les tuples globale.

**Requête**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**Résultats**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Cet exemple est une extension naturelle de l’exemple précédent et effectue une jointure double. Par conséquent, le produit croisé peuvent être affiché comme le pseudo-code suivant.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`a un enfant qui a un animal familier. Par conséquent, le produit croisé génère une ligne (1*1*1) à partir de cette famille. Toutefois, WakefieldFamily a deux enfants, mais qu’un seul enfant « Jesse » a animal de compagnie. Jesse a cependant les 2 animaux. Par conséquent le produit croisé donne 1*1*2 = 2 lignes à partir de cette famille.

Dans l’exemple suivant, il existe un filtre supplémentaire sur `pet`. Ceci exclut tous les tuples, où le nom d’animal de compagnie n’est pas « Clichés instantané ». Notez que nous sommes en mesure de générer de tuples à partir des baies, filtre sur un des éléments du tuple et de n’importe quelle combinaison des éléments de projet. 

**Requête**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Résultats**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>Intégration de JavaScript
DocumentDB fournit un modèle de programmation pour l’exécution de la logique d’application JavaScript basé directement sur les collections en termes de procédures stockées et les déclencheurs. Cela permet à la fois pour :

-   Possibilité d’effectuer des opérations CRUD transactionnelles de hautes performances et des requêtes sur des documents dans une collection en raison de l’intégration profonde d’exécution JavaScript directement dans le moteur de base de données. 
-   Une modélisation naturelle de flux de contrôle, variable de portée et affectation et intégration de primitives avec des transactions de la base de données de gestion des exceptions. Pour plus d’informations sur la prise en charge de DocumentDB pour l’intégration de JavaScript, reportez-vous à la documentation de programmabilité de côté serveur JavaScript.

###<a name="user-defined-functions-udfs"></a>Les fonctions définies par l’utilisateur
Ainsi que les types déjà définis dans cet article, DocumentDB SQL fournit la prise en charge pour le fonctions définis par l’utilisateur (UDF). En particulier, les UDF scalaires sont prises en charge où les développeurs peuvent passer zéro, un ou plusieurs arguments et retournent un résultat unique argument précédent. Chacun de ces arguments sont vérifiés pour les valeurs de JSON juridique.  

La syntaxe de DocumentDB SQL est étendue pour prendre en charge la logique d’application personnalisée à l’aide de ces fonctions définies par l’utilisateur. Les UDF peuvent être enregistrés avec DocumentDB et ensuite être référencés dans le cadre d’une requête SQL. En fait, les FDU sont ordinateurs conçus pour être appelés par les requêtes. En corollaire à cette option, les FDU n’ont pas de l’accès à l’objet de contexte qui ont d’autres types JavaScript (procédures stockées et les déclencheurs). Étant donné que les requêtes s’exécutent en lecture seule, ils peuvent exécuter sur primaire ou secondaires réplicas. Par conséquent, les UDF sont conçus pour fonctionner sur des réplicas secondaires à la différence des autres types JavaScript.

Voici un exemple de comment un fichier UDF peut être enregistré dans la base de DocumentDB, en particulier dans une collection de documents.

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
L’exemple précédent crée un fichier UDF dont le nom est `REGEX_MATCH`. Elle accepte deux valeurs de chaîne JSON `input` et `pattern` et vérifie si la première correspond au modèle spécifié dans la seconde en utilisant la fonction de String.Match () de JavaScript.


Nous pouvons à présent utiliser cette FDU dans une requête dans une projection. Fichiers UDF doivent être qualifiés par le préfixe sensible à la casse « udf ». Lorsqu’elle est appelée à partir de requêtes. 

>[AZURE.NOTE] Avant le 17/3/2015, DocumentDB prise en charge des appels de FDU sans le « udf ». préfixe comme sélectionnez le REGEX_MATCH(). Ce modèle d’appel a été désapprouvé.  

**Requête**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Résultats**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

Le fichier UDF peut également servir à l’intérieur d’un filtre comme indiqué dans l’exemple ci-dessous, également qualifiés par « udf ». préfixe :

**Requête**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Résultats**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Par essence, UDF sont les expressions scalaires valides et peuvent être utilisées dans les filtres et de projections. 

Pour développer la puissance de FDU, examinons un autre exemple avec la logique conditionnelle :

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
Voici un exemple qui teste la FDU.

**Requête**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Résultats**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Comme les exemples précédents de présentation, UDF intégrant la puissance du langage JavaScript avec le SQL DocumentDB pour fournir une interface programmable riche pour faire une logique complexe procédurale, conditionnelle à l’aide des fonctionnalités d’exécution JavaScript intégrées.

DocumentDB SQL fournit les arguments aux FDU pour chaque document de la source à l’étape actuelle du traitement de la FDU (clause WHERE ou la clause SELECT). Le résultat est intégré en toute transparence dans le pipeline d’exécution globale. Si les propriétés visé par la FDU, les paramètres ne sont pas disponibles dans la valeur JSON, le paramètre est considéré comme non définie et, par conséquent, l’appel de la FDU entièrement ignoré. De la même façon si le résultat de la FDU n’est pas défini, il n’est pas inclus dans le résultat. 

En résumé, UDF sont des outils à faire une logique métier complexe dans le cadre de la requête.

### <a name="operator-evaluation"></a>Évaluation d’opérateur
DocumentDB, en la raison d’être d’une base de données JSON, dessine le parallèle avec les opérateurs de JavaScript et de sa sémantique d’évaluation. Alors que DocumentDB essaie de préserver la sémantique en termes de prise en charge JSON JavaScript, l’évaluation de l’opération d’écart et dans certains cas.

Dans DocumentDB SQL, contrairement à dans SQL traditionnel, les types de valeurs sont souvent pas connus jusqu'à ce que les valeurs sont effectivement récupérées à partir de la base de données. Pour exécuter efficacement des requêtes, la plupart des opérateurs ont des exigences de type stricte. 

DocumentDB SQL n’effectue pas les conversions implicites, contrairement à JavaScript. Par exemple, une requête comme `SELECT * FROM Person p WHERE p.Age = 21` correspond à des documents qui contiennent une propriété Age dont la valeur est 21. Tout autre document dont la propriété Age correspond aux variations éventuellement infinie de chaîne « 21 » ou autres telles que « 021 », « 21.0 », « 0021 », « 00021 », etc. ne sera pas trouvé. Il s’agit en revanche le JavaScript où les valeurs de chaîne sont implicitement convertis en nombres (basée sur un opérateur, ex : ==). Ce choix est crucial pour des index efficaces dans le DocumentDB SQL. 

## <a name="parameterized-sql-queries"></a>Requêtes SQL paramétrées
DocumentDB prend en charge les requêtes avec paramètres exprimées avec le familier @ notation. SQL paramétrée fournit robuste de gestion et d’échappement des entrées d’utilisateur, empêche la divulgation accidentelle de données par l’intermédiaire d’injection de SQL. 

Par exemple, vous pouvez écrire une requête qui prend le nom et l’état de l’adresse en tant que paramètres et puis l’exécuter pour différentes valeurs de nom et d’adresse état basé sur l’entrée d’utilisateur.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Cette demande peut ensuite être envoyée à DocumentDB sous la forme d’une requête paramétrée de JSON comme indiqué ci-dessous.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

L’argument début peut être définie à l’aide de requêtes paramétrées comme indiqué ci-dessous.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Les valeurs de paramètre peuvent être n’importe quel JSON valide (chaînes, nombres, valeurs booléennes, null, même les tableaux ou imbriqué JSON). Également DocumentDB étant sans schéma, les paramètres ne sont pas validées par rapport à n’importe quel type.

##<a name="built-in-functions"></a>Fonctions intégrées
DocumentDB prend également en charge un certain nombre de fonctions intégrées pour des opérations courantes qui peuvent être utilisées dans des requêtes comme fonctions de défini par l’utilisateur (UDF).

<table>
<tr>
<td>Fonctions mathématiques</td> 
<td>ABS, plafond, EXP, plancher, journal, LOG10, POWER, arrondi, signe, SQRT, carré, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, TAN, degrés, PI, RADIANS, SIN et COT</td>
</tr>
<tr>
<td>Fonctions de vérification de type</td>    
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED et IS_PRIMITIVE</td>
</tr>
<tr>
<td>Fonctions de chaîne</td>   
<td>CONCAT, CONTAINS, ENDSWITH, INDEX_OF, gauche, longueur, inférieur, LTRIM, remplacer, répliquer, inverse, droite, RTRIM, STARTSWITH, SUBSTRING et supérieur</td>
</tr>
<tr>
<td>Fonctions de tableau</td>    
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH et ARRAY_SLICE</td>
</tr>
<tr>
<td>Fonctions spatiales</td>  
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID et ST_ISVALIDDETAILED</td>
</tr>
</table>  

Si vous utilisez actuellement une fonction définie par l’utilisateur (UDF) pour lequel une fonction intégrée est désormais disponible, vous devez utiliser la fonction intégrée correspondante tel qu’il va être plus rapides à exécuter et plus efficacement. 

### <a name="mathematical-functions"></a>Fonctions mathématiques
Les fonctions mathématiques chaque effectuent un calcul, généralement basé sur les valeurs d’entrée fournies comme arguments, et renvoient une valeur numérique. Voici un tableau des fonctions mathématiques intégrées prises en charge.

<table>
<tr>
<td><strong>Utilisation de</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>Retourne la valeur absolue (positive) de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">PLAFOND (num_expr)</a></td> 
<td>Retourne le plus petit nombre entier supérieur ou égal à l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">PLANCHER (num_expr)</a></td> 
<td>Retourne le plus grand entier inférieur ou égal à l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>Renvoie l’exposant de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">JOURNAL (num_expr [, base])</a></td> 
<td>Renvoie le logarithme népérien de l’expression numérique spécifiée, soit le logarithme à l’aide de la base spécifiée</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>Retourne la valeur logarithmique de base 10 de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td> 
<td>Renvoie une valeur numérique, arrondie à l’entier le plus proche.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td> 
<td>Renvoie une valeur numérique, tronquée au nombre entier le plus proche.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>   
<td>Renvoie la racine carrée de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">CARRÉ (num_expr)</a></td>   
<td>Retourne le carré de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">ALIMENTATION (num_expr, num_expr)</a></td>   
<td>Retourne la puissance de l’expression numérique spécifiée à la valeur spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGNE (num_expr)</a></td>   
<td>Retourne la valeur du signe (-1, 0, 1) de l’expression numérique spécifiée.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>   
<td>Renvoie l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelé arc cosinus.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>   
<td>Renvoie l’angle, en radians, dont le sinus est l’expression numérique spécifiée. Il est également appelé arc sinus.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>   
<td>Renvoie l’angle, en radians, dont la tangente est l’expression numérique spécifiée. Il est également appelé arc tangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>Renvoie l’angle, en radians, entre l’axe des abscisses positifs et le rayon depuis l’origine au point (y, x), où x et y sont les valeurs des deux expressions float spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>Retourne le cosinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>Renvoie la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">DEGRÉS (num_expr)</a></td> 
<td>Renvoie l’angle correspondant en degrés pour un angle spécifié en radians.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI)</a></td>   
<td>Retourne la valeur constante de PI.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANS (num_expr)</a></td> 
<td>Renvoie les radians lorsqu’une expression numérique, en degrés, est entrée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td> 
<td>Retourne le sinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>Renvoie la tangente de l’expression d’entrée, de l’expression spécifiée.</td>
</tr>

</table> 

Par exemple, vous pouvez maintenant exécuter les requêtes comme suit :

**Requête**

    SELECT VALUE ABS(-4)

**Résultats**

    [4]

La principale différence entre les fonctions de DocumentDB par rapport à ANSI SQL est qu’ils sont conçus pour fonctionner avec les données de schéma sans schéma et mixtes. Par exemple, si vous disposez d’un document dans lequel la propriété Size est manquant ou a une valeur non numérique comme « inconnu », puis le document est ignoré, au lieu de renvoyer une erreur.

### <a name="type-checking-functions"></a>Fonctions de vérification de type
Les fonctions de vérification de type permettent de vérifier le type d’une expression dans les requêtes SQL. Les fonctions de vérification de type peuvent être utilisées pour déterminer le type de propriétés dans les documents à la volée, lorsqu’elle est inconnue ou variable. Voici un tableau de type intégré pris en charge la vérification des fonctions.

<table>
<tr>
  <td><strong>Utilisation de</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si le type de la valeur est un tableau.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si le type de la valeur est une valeur booléenne.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si le type de la valeur est null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si le type de la valeur est un nombre.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si le type de la valeur est un objet JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si le type de la valeur est une chaîne.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si une valeur a été assignée à la propriété.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si le type de la valeur est null, nombre, booléen ou chaîne.</td>
</tr>

</table>

À l’aide de ces fonctions, vous pouvez maintenant exécuter les requêtes comme suit :

**Requête**

    SELECT VALUE IS_NUMBER(-4)

**Résultats**

    [true]

### <a name="string-functions"></a>Fonctions de chaîne
Les fonctions scalaires suivantes effectuent une opération sur une valeur de chaîne d’entrée et renvoient une chaîne, une valeur numérique ou booléenne. Voici un tableau des fonctions de chaîne intégrées :

Utilisation de|Description
---|---
[LONGUEUR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Renvoie le nombre de caractères de l’expression de chaîne spécifiée
[CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Retourne une chaîne qui est le résultat de la concaténation de deux ou plusieurs valeurs de chaîne.
[SOUS-chaîne (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Renvoie la partie d’une expression de chaîne.
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde.
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde.
[CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde.
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Renvoie la position de départ de la première occurrence de la seconde chaîne d’expression dans la première expression de la chaîne spécifiée, ou -1 si la chaîne n’est pas trouvée.
[GAUCHE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Retourne la partie gauche d’une chaîne avec le nombre spécifié de caractères.
[DROITE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Retourne la partie de droite d’une chaîne avec le nombre spécifié de caractères.
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Retourne une expression de chaîne après avoir supprimé les espaces à gauche.
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Retourne une expression de chaîne après troncature de tous les espaces à droite.
[INFÉRIEUR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Retourne une expression de chaîne après la conversion des données de type caractère en majuscules en minuscules.
[SUPÉRIEUR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Retourne une expression de chaîne après la conversion des données en caractères minuscules en majuscules.
[Remplacer (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Remplace toutes les occurrences d’une valeur de chaîne spécifiée par une autre valeur de chaîne.
[REPLICATE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Répète une valeur de chaîne un nombre spécifié de fois.
[INVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Renvoie l’ordre inverse d’une valeur de chaîne.

À l’aide de ces fonctions, vous pouvez maintenant exécuter les requêtes comme suit. Par exemple, vous pouvez retourner le nom de famille en majuscules comme suit :

**Requête**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Résultats**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Ou de concaténer des chaînes, comme dans cet exemple :

**Requête**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Résultats**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Fonctions de chaîne peuvent également être utilisées dans la clause WHERE pour filtrer les résultats, comme dans l’exemple suivant :

**Requête**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Résultats**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Fonctions de tableau
Les fonctions scalaires suivantes effectuent une opération sur une valeur du tableau d’entrée et le retour numérique, la valeur booléenne ou un tableau. Voici un tableau des fonctions de tableau intégré :

Utilisation de|Description
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Renvoie le nombre d’éléments de l’expression de tableau spécifié.
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Retourne un tableau qui est le résultat de la concaténation de deux ou plusieurs valeurs de tableau.
[ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Retourne une valeur booléenne qui indique si le tableau contient la valeur spécifiée.
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Renvoie la partie d’une expression de tableau.

Fonctions de tableau peuvent être utilisées pour manipuler des tableaux dans JSON. Par exemple, voici une requête qui retourne tous les documents où un des parents est « Robin Wakefield ». 

**Requête**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Voici un autre exemple qui utilise ARRAY_LENGTH pour obtenir le nombre d’enfants par famille.

**Requête**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Résultats**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Fonctions spatiales

DocumentDB prend en charge les fonctions intégrées de Open Geospatial Consortium (OGC) suivantes pour l’interrogation de géographique. Pour plus d’informations géographiques prise en charge dans DocumentDB, consultez [utilisation des données géographique dans Azure DocumentDB](documentdb-geospatial.md). 

<table>
<tr>
  <td><strong>Utilisation de</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Renvoie la distance entre les deux expressions du point GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Retourne une expression booléenne indiquant si le point de GeoJSON spécifié dans le premier argument est dans le polygone de GeoJSON dans le deuxième argument.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Retourne une valeur Boolean indiquant si l’expression de point ou de polygone GeoJSON spécifiée est valide.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Renvoie la valeur d’une valeur JSON qui contient une valeur booléenne si l’expression de point ou de polygone GeoJSON spécifiée est valide et si elle est non valide, en outre la raison sous la forme d’une valeur de chaîne.</td>
</tr>
</table>

Fonctions de spatiales peuvent être utilisées pour effectuer des querries de proximité par rapport à des données spatiales. Par exemple, voici une requête qui renvoie tous les documents de famille situés à 30 kilomètres de l’emplacement spécifié à l’aide de la fonction intégrée de ST_DISTANCE. 

**Requête**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis « requêtes à distance » seront servis efficacement par le biais de l’index. Pour plus d’informations sur l’indexation spatiale, reportez-vous à la section ci-dessous. Si vous n’avez pas un index spatial pour les chemins d’accès spécifiés, vous pouvez toujours effectuer des recherches spatiales en spécifiant `x-ms-documentdb-query-enable-scan` en-tête de requête avec la valeur définie sur « true ». Dans .NET, cela est possible en passant l’argument facultatif **FeedOptions** requêtes avec [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) avec la valeur true. 

ST_WITHIN peut être utilisé pour vérifier si un point se trouve dans un polygone. Polygones sont utilisés pour représenter des limites telles que des codes postaux, les frontières des États ou des formations. À nouveau si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis « dans » requêtes seront servis efficacement par le biais de l’index. 

Arguments de polygone dans la ST_WITHIN peuvent contenir uniquement une seule sonnerie, c'est-à-dire les polygones ne doivent pas contenir de trous. Vérifier les [limites de DocumentDB](documentdb-limits.md) pour le nombre maximal de points autorisés dans un polygone pour une requête ST_WITHIN.

**Requête**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Résultats**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Similaire à works de types comment incompatibles dans la requête de DocumentDB, si la valeur de l’emplacement spécifié dans l’argument est incorrecte ou non valide, puis il prend la valeur **non défini** et le document évalué soit ignorée à partir des résultats de la requête. Si votre requête ne renvoie aucun résultat, l’exécution du débogage à la ST_ISVALIDDETAILED pourquoi le type spatail n’est pas valide.     

ST_ISVALID et ST_ISVALIDDETAILED peuvent être utilisé pour vérifier si un objet spatial est valide. Par exemple, la requête suivante vérifie la validité d’un point avec une valeur hors limites latitude (-132.8). ST_ISVALID retourne simplement une valeur booléenne et ST_ISVALIDDETAILED renvoie la valeur booléenne et une chaîne qui contient la raison pour laquelle il est non valide.

**Requête**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Résultats**

    [{
      "$1": false
    }]

Ces fonctions peuvent également être utilisées pour valider des polygones. Par exemple, ici nous utilisons ST_ISVALIDDETAILED pour valider un polygone qui n’est pas fermé. 

**Requête**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Résultats**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
Qui encapsule les fonctions spatiales et la syntaxe SQL pour DocumentDB. Maintenant examinons à présent comment LINQ interrogation fonctionne et comment il interagit avec la syntaxe nous avons vu jusqu'à présent.

## <a name="linq-to-documentdb-sql"></a>LINQ to SQL de DocumentDB
LINQ est un modèle de programmation .NET qui exprime le calcul sous forme de requêtes sur les flux d’objets. DocumentDB fournit un client de bibliothèque côté d’interface avec LINQ en facilitant une conversion entre un mappage à partir d’un sous-ensemble de LINQ et les objets .NET et JSON interroge pour DocumentDB des requêtes. 

L’illustration ci-dessous présente l’architecture de prise en charge des requêtes LINQ à l’aide de DocumentDB.  Le client DocumentDB, aux développeurs de créer un objet **IQueryable** directement en interrogeant le fournisseur de requête DocumentDB, qui ensuite traduit la requête LINQ dans une requête DocumentDB. La requête est ensuite transmise au serveur DocumentDB pour récupérer un jeu de résultats au format JSON. Les résultats renvoyés sont désérialisés dans un flux d’objets .NET sur le client.

![Architecture de prise en charge des requêtes LINQ à l’aide de DocumentDB - syntaxe SQL, langage de requête JSON, les concepts de base de données et les requêtes SQL][1]
 


### <a name="net-and-json-mapping"></a>.NET et mappage de JSON
Le mappage entre les objets .NET et les documents JSON est naturel, chaque champ de membre de données est mappé à un objet JSON, où le nom du champ correspond à la partie « clé » de l’objet et la partie de la « valeur » est mis en correspondance avec la partie de la valeur de l’objet de manière récursive. Prenons l’exemple suivant. L’objet de famille créé est mappé sur le document JSON, comme illustré ci-dessous. Et inversement, le document JSON est mappé sur un objet .NET.

**Classe C#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ to SQL traduction de
Le fournisseur de requêtes DocumentDB effectue un mappage de l’effort optimal à partir d’une requête LINQ dans une requête DocumentDB SQL. Dans la description suivante, nous supposons que le lecteur possède une connaissance de base de LINQ.

Tout d’abord, pour le système de type, nous prenons en charge tous les types primitifs JSON – types numériques, boolean, string et null. Seuls ces types JSON sont pris en charge. Les expressions scalaires suivantes sont prises en charge.

-   Les valeurs de constante – ces inclut des valeurs constantes de types de données primitifs au moment de l’évaluation de la requête.

-   Expressions d’index de tableau de la propriété – ces expressions font référence à la propriété d’un objet ou un élément de tableau.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Les expressions arithmétiques - notamment des expressions arithmétiques courantes sur des valeurs numériques et booléens. Pour obtenir la liste complète, reportez-vous à la spécification SQL.

        2 * family.children[0].grade;
        x + y;

-   Expression de comparaison de chaîne - celles-ci incluent la comparaison d’une valeur de chaîne pour une valeur de chaîne constante.  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   / Tableau d’objets expression de création de-ces expressions en retour un objet de type de valeur composée ou anonyme ou un tableau de ces objets. Ces valeurs peuvent être imbriqués.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>Liste des opérateurs pris en charge de LINQ
Voici une liste des opérateurs LINQ pris en charge dans le fournisseur LINQ inclus avec le Kit de développement .NET DocumentDB.

-   **Sélectionnez**: traduisent des Projections sur le SQL, y compris la construction d’objets
-   **Où**: filtres traduire SQL WHERE et prennent en charge la conversion entre les & &, || et ! pour les opérateurs SQL
-   **SelectMany**: permet le déroulement des tableaux à la clause SQL JOIN. Peut être utilisée pour la chaîne/imbriquer des expressions de filtre sur les éléments de tableau
-   **OrderBy et OrderByDescending**: par ordre croissant ou décroissant se traduit par :
-   **CompareTo**: se traduit par des comparaisons de la plage. Couramment utilisé pour les chaînes, car ils ne sont pas comparables dans .NET
-   **Prendre**: se traduit par la haut SQL pour limiter les résultats d’une requête
-   **Fonctions mathématiques**: prend en charge la conversion à partir de. Asin d’Abs, Acos, du NET, Atan, plafond, Cos, Exp, plancher, journal, Log10, Pow, arrondi, signe, Sin, Sqrt, Tan, Truncate pour les fonctions intégrées de SQL équivalentes.
-   **Fonctions de chaîne**: prend en charge la conversion à partir de. Concat, Contains, EndsWith de NET, IndexOf, Count, ToLower, TrimStart, remplacer, inverse, TrimEnd, StartsWith, SubString, ToUpper pour les fonctions intégrées de SQL équivalentes.
-   **Fonctions de tableau**: prend en charge la conversion à partir de. De NET Concat contient et nombre de fonctions intégrées SQL équivalentes.
-   **Fonctions d’Extension géographique**: prend en charge la traduction à partir de la Distance, dans, IsValid et IsValidDetailed les méthodes stub pour les fonctions intégrées de SQL équivalentes.
-   **Fonction de Extension de fonction définie par l’utilisateur**: traduction de prend en charge à partir de la méthode stub UserDefinedFunctionProvider.Invoke à l’utilisateur correspondant défini par la fonction.
-   **Divers**: prend en charge la traduction de la fusion et les opérateurs conditionnels. Permet de convertir contient chaîne contient, ARRAY_CONTAINS ou IN SQL en fonction du contexte.

### <a name="sql-query-operators"></a>Opérateurs de requête SQL
Voici quelques exemples qui illustrent comment certains des opérateurs de requête standard LINQ sont convertis pour les requêtes DocumentDB.

#### <a name="select-operator"></a>Sélectionnez l’opérateur
La syntaxe est `input.Select(x => f(x))`, où `f` est une expression scalaire.

**Expression lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expression lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expression lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Opérateur SelectMany
La syntaxe est `input.SelectMany(x => f(x))`, où `f` est une expression scalaire qui renvoie un type de collection.

**Expression lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Où opérateur
La syntaxe est `input.Where(x => f(x))`, où `f` est une expression scalaire qui renvoie une valeur booléenne.

**Expression lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expression lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Requêtes SQL composite
Les opérateurs ci-dessus peuvent être composés pour former des requêtes plus puissantes. Dans la mesure où DocumentDB prend en charge les collections imbriquées, la composition peut être concaténée ou imbriquée.

#### <a name="concatenation"></a>Concaténation 

La syntaxe est `input(.|.SelectMany())(.Select()|.Where())*`. Une requête concaténée peut démarrer avec un texte facultatif `SelectMany` requête suivie de plusieurs `Select` ou `Where` opérateurs.


**Expression lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expression lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expression lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expression lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Imbrication

La syntaxe est `input.SelectMany(x=>x.Q())` est un `Select`, `SelectMany`, ou `Where` opérateur.

Dans une requête imbriquée, la requête interne est appliquée à chaque élément de la collection externe. Une fonctionnalité importante est que la requête interne peut faire référence aux champs des éléments de la collection externe comme les jointures réflexives.

**Expression lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expression lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expression lambda LINQ**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>Requêtes SQL en cours d’exécution
DocumentDB expose des ressources via une API REST qui peut être appelée par n’importe quel langage capable de faire des demandes HTTP/HTTPS. En outre, DocumentDB propose des bibliothèques de programmation pour plusieurs langues courantes telles que .NET, Node.js, JavaScript et les Python. L’API REST et toutes les différentes bibliothèques prennent en charge l’interrogation au moyen de SQL. Le Kit de développement .NET prend en charge LINQ interrogation en plus de SQL.

Les exemples suivants montrent comment créer une requête et l’envoyer par rapport à un compte de base de données DocumentDB.

### <a name="rest-api"></a>API REST
DocumentDB offre un modèle de programmation RESTful ouvert via HTTP. Comptes de base de données peuvent être mis en service à l’aide d’un abonnement Azure. Le modèle de ressource DocumentDB se compose d’un ensembles de ressources sous un compte de base de données, chacun d’eux est dédié à l’aide d’un URI logique et stable. Un ensemble de ressources est appelé un flux dans ce document. Un compte de base de données se compose d’un ensemble de bases de données, chacun contenant plusieurs collections, chacun de la tour contiennent d’autres types de ressources, des documents et des fichiers UDF.

Le modèle d’interaction de base avec ces ressources est via les verbes HTTP GET, PUT, POST et DELETE avec leur interprétation standard. Le verbe POST est utilisé pour la création d’une nouvelle ressource, pour l’exécution d’une procédure stockée ou de l’émission d’une requête DocumentDB. Les requêtes sont toujours lus uniquement les opérations avec aucun effet secondaire.

Les exemples suivants montrent une publication pour une requête DocumentDB effectuée par rapport à une collection contenant les deux exemples de documents que nous avons vu jusqu'à présent. La requête comporte un filtre simple sur la propriété de nom JSON. Notez l’utilisation de la `x-ms-documentdb-isquery` et Content-Type : `application/query+json` les en-têtes pour indiquer que l’opération est une requête.


**Demande**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**Résultats**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


Le deuxième exemple montre une requête plus complexe qui retourne plusieurs résultats de la jointure.

**Demande**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Résultats**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Si les résultats d’une requête ne peut pas tenir dans une seule page de résultats, puis l’API REST retourne un jeton de continuation par le biais de la `x-ms-continuation-token` en-tête de réponse. Les clients peuvent paginer les résultats en incluant l’en-tête dans les résultats. Le nombre de résultats par page peut également être contrôlé par le biais de la `x-ms-max-item-count` en-tête numéro.

Pour gérer la stratégie de la cohérence des données pour les requêtes, utilisez le `x-ms-consistency-level` en-tête à toutes les demandes de l’API REST. Pour garantir la cohérence de la session, il est nécessaire également répercutées plus tard `x-ms-session-token` en-tête de Cookie dans la requête. Notez que stratégie d’indexation de la collection interrogée peut également influer sur la cohérence des résultats de la requête. Avec la valeur par défaut des paramètres de stratégie d’indexation, pour les collections de l’index est toujours en cours avec le contenu du document et les résultats de requête correspondent à la cohérence choisie pour les données. Si la stratégie d’indexation est assouplie pour Lazy, requêtes peuvent renvoyer des résultats obsolètes. Pour plus d’informations, consultez [Niveaux de cohérence DocumentDB] [consistency-levels].

Si la stratégie d’indexation configurée sur la collection ne peut pas prendre en charge la requête spécifiée, le serveur de DocumentDB renvoie 400 « demande incorrecte ». Il est renvoyé pour les requêtes de plage pour les chemins d’accès configurés pour les recherches de hachage (égalité) et des chemins d’accès explicitement exclus de l’indexation. Le `x-ms-documentdb-query-enable-scan` en-tête peut être spécifié pour permettre à la requête effectuer une analyse lorsqu’un index n’est pas disponible.

### <a name="c-net-sdk"></a>LE KIT DE DÉVELOPPEMENT C# (.NET)
Le Kit de développement .NET prend en charge LINQ et SQL interrogation. L’exemple suivant montre comment effectuer la requête de filtre simple présentée plus haut dans ce document.


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Cet exemple compare deux propriétés sont égales dans chaque document et utilise des projections anonymes. 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


L’exemple suivant montre des jointures, exprimées par le biais de LINQ SelectMany.


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



Le client .NET parcourt automatiquement toutes les pages de résultats de requête dans les blocs de foreach, comme indiqué ci-dessus. Les options de requête introduites dans la section de l’API REST sont également disponibles dans le Kit de développement .NET à l’aide de la `FeedOptions` et `FeedResponse` classes dans la méthode CreateDocumentQuery. Le nombre de pages peut être contrôlé à l’aide de la `MaxItemCount` paramètre. 

Vous pouvez contrôler explicitement la pagination en créant `IDocumentQueryable` à l’aide de la `IQueryable` objet, puis en lisant le` ResponseContinuationToken` valeurs et en les passant en différé en tant que `RequestContinuationToken` dans `FeedOptions`. `EnableScanInQuery`peut être défini pour permettre des analyses lorsque la requête ne peut pas être pris en charge par la politique d’indexation configurée. Pour les collections partitionnées, vous pouvez utiliser `PartitionKey` pour exécuter une requête sur une seule partition (bien que les DocumentDB peuvent automatiquement extraire ce texte de la requête), et `EnableCrossPartitionQuery` pour exécuter des requêtes devant être exécuté sur plusieurs partitions. 

[Exemples de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net) pour voir plus d’échantillons contenant des requêtes. 

### <a name="javascript-server-side-api"></a>API de JavaScript côté serveur 
DocumentDB fournit un modèle de programmation pour l’exécution de la logique d’application JavaScript basé directement sur les collections à l’aide de procédures stockées et les déclencheurs. La logique JavaScript inscrite à un niveau de collection peut alors émettre des opérations de base de données sur les opérations sur les documents de la collection donnée. Ces opérations sont encapsulées dans des transactions ACID ambiantes.

L’exemple suivant montre comment utiliser le queryDocuments dans l’API JavaScript du serveur pour effectuer des requêtes à partir de l’intérieur de procédures stockées et les déclencheurs.


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>Fonctions d’agrégation

Est prise en charge native pour les fonctions d’agrégation, mais si vous avez besoin de fonctionnalités de nombre ou somme dans le même temps, vous pouvez obtenir le même résultat à l’aide de différentes méthodes.  

Sur le chemin d’accès de lecture :

- Vous pouvez effectuer des fonctions d’agrégation par la récupération des données et de procéder à un inventaire localement. Il est conseillé d’utiliser une projection de requête peu coûteuses telles que `SELECT VALUE 1` au lieu du document complet, tel que `SELECT * FROM c`. Cela permet de maximiser le nombre de documents traités dans chaque page de résultats, en évitant des allers-retours supplémentaires vers le service si nécessaire.
- Vous pouvez également utiliser une procédure stockée afin de minimiser la latence du réseau dans la répétition de boucles. Pour un exemple de procédure stockée qui calcule le nombre pour une requête de filtre donné, consultez [Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js). La procédure stockée peut permettre aux utilisateurs de combiner la logique d’entreprise ainsi que d’effectuer des agrégations de façon efficace.

Sur le chemin d’accès de l’écriture :

- Un autre modèle commun est au préalable l’agrégation des résultats dans le chemin d’accès « écriture ». Ceci est particulièrement intéressant lorsque le volume de demandes de « lire » est supérieur à celui des demandes de « écriture ». Une fois pré-regroupé, les résultats sont disponibles avec un point unique demande de lecture.  La meilleure façon d’agrégation préalable dans DocumentDB est à définir un déclencheur qui est appelé avec chaque « écriture » et un document de métadonnées contenant les derniers résultats de la requête qui est matérialisée en cours de mise à jour. Par exemple, regardez l’exemple [UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js) , qui met à jour les minSize, maxSize et totalSize du document de la collection de métadonnées. L’exemple peut être étendu pour mettre à jour un compteur, sum, etc..

##<a name="references"></a>Références
1.  [Introduction à DocumentDB Azure][introduction]
2.  [Spécification de DocumentDB SQL](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [Exemples de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
4.  [Niveaux de cohérence DocumentDB][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  Spécification de JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  Techniques d’évaluation des requêtes pour les bases de données volumineuses [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Traitement des requêtes dans une base de données relationnelle parallèle systèmes, IEEE Computer appuyez sur la société, 1994
11. Lu, Ooi, Tan, traitement des requêtes dans une base de données relationnelle parallèle systèmes, IEEE Computer appuyez sur la société, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins : Latin de porc : un langage non étrangère pour le traitement de données, SIGMOD 2008.
13.     G. Graefe. Le framework de Cascades pour l’optimisation des requêtes. Données de IEEE ENG Bull., 18, paragraphe 3 : 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 
