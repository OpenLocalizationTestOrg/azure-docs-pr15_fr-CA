<properties 
    pageTitle="Référence d’Analytique dans les perspectives d’Application | Microsoft Azure" 
    description="Référence des instructions d’Analytique, l’outil de recherche puissant de perspectives de l’Application. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>Référence pour l’Analytique

[Analytique](app-insights-analytics.md) est une fonctionnalité puissante de recherche [d’Idées d’Application](app-insights-overview.md). Ces pages décrivent le langage de requête Analytique.

> [AZURE.NOTE] [Lecteur de test Analytique sur nos données simulées](https://analytics.applicationinsights.io/demo) si votre application n’est pas envoyer des données aux analyses d’Application encore.

## <a name="index"></a>Index


**Vous permettent de** [vous permettent de](#let-clause)


**Requêtes et opérateurs** [nombre](#count-operator)  |  [évaluer](#evaluate-operator) | [étendre](#extend-operator) | [jointure](#join-operator) | [limite de](#limit-operator) | [mvexpand](#mvexpand-operator) | [analyser](#parse-operator) | [projet](#project-operator) | [rangement projet](#project-away-operator) | [plage](#range-operator) | [réduire](#reduce-operator) | [restituer la directive](#render-directive) | [restreindre la clause](#restrict-clause) | [tri](#sort-operator) | [résumer](#summarize-operator) | [prendre](#take-operator) | [haut](#top-operator) | [imbriqué à haut](#top-nested-operator) | [union](#union-operator) | [où](#where-operator) | [where dans](#where-in-operator)

**Agrégations** [tout](#any)  |  [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [nombre](#count) | [Nb.Si](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [centile](#percentile) | [centiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [somme](#sum) | [variation](#variance)

**Valeurs scalaires** [Littéraux booléens](#boolean-literals)  |  [Opérateurs booléens](#boolean-operators) | [Casts](#casts) | [scalaires comparaisons](#scalar-comparisons) | [gettype](#gettype) | [hachage](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [NOT NULL](#notnull) | [toscalar](#toscalar)

**Numéros** [Opérateurs arithmétiques](#arithmetic-operators)  |  [De littéraux numériques](#numeric-literals) | [abs](#abs) | [emplacement](#bin) | [exp](#exp) | [sol](#floor) | [gamma](#gamma) | [journal](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Date et heure** [Date et heure expressions](#date-and-time-expressions)  |  [Date et d’heure](#date-and-time-literals) | [il y a](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [maintenant](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Chaîne** [GUID](#guids)  |  [Obscurcie des littéraux de chaîne](#obfuscated-string-literals) | [Les littéraux de chaîne](#string-literals) | [des comparaisons de chaînes](#string-comparisons) | [countof](#countof) | [Extraire](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [Remplacer](#replace) | [fractionnement](#split) | [strcat](#strcat) | [strlen](#strlen) | [sous-chaîne](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Tableaux, objets et dynamique** [Littéraux de tableau et d’objet](#array-and-object-literals)  |  [Les fonctions objet dynamique](#dynamic-object-functions) | [des objets dynamiques dans les clauses let](#dynamic-objects-in-let-clauses) | [expressions de chemin d’accès de JSON](#json-path-expressions) | [noms](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [plage](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>Vous permettent de

### <a name="let-clause"></a>Let, clause

**Tabulaire let - une table d’affectation de noms**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Scalaire let - désignant une valeur de**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda let - nom d’une fonction**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Une clause let lie un [nom](#names) à la table de résultats, une valeur scalaire ou une fonction. La clause est un préfixe à une requête et la portée de la liaison est cette requête. (Let ne permet pas à des éléments de nom que vous utilisez ultérieurement dans votre session.)

**Syntaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type :* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query :* Une requête non précédée d’une clause let.

**Exemples**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Jointure réflexive :

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>Requêtes et opérateurs

Une requête sur votre télémétrie se compose d’une référence à un flux source, suivi d’un pipeline de filtres. Par exemple :


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Chaque filtre préfixé par le caractère `|` est une instance d’un *opérateur*, avec certains paramètres. L’entrée de l’opérateur est le tableau qui est le résultat du pipeline précédent. Dans la plupart des cas, tous les paramètres sont des [expressions scalaires](#scalars) par rapport aux colonnes de l’entrée. Dans de rares cas, les paramètres sont les noms des colonnes d’entrée et dans quelques rares cas, le paramètre est une seconde table. Le résultat d’une requête est toujours un tableau, même s’il ne comporte qu’une seule colonne et une ligne.

Les requêtes peuvent contenir des sauts de ligne unique, mais sont terminent par une ligne vide. Ils peuvent contenir des commentaires entre `//` et de fin de ligne.

Une requête peut être préfixée par une ou plusieurs [clauses de laisser](#let-clause), qui définissent des valeurs scalaires, des tableaux ou des fonctions qui peuvent être utilisées dans la requête.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`est utilisé dans les exemples de requête ci-dessous pour indiquer la table pipeline ou source précédente.
> 

### <a name="count-operator"></a>opérateur Count

Le `count` opérateur retourne le nombre d’enregistrements (lignes) dans le jeu d’enregistrements d’entrée.

**Syntaxe**

    T | count

**Arguments**

* *T*: les données sous forme de tableau, dont les enregistrements doivent être comptabilisés.

**Propriété renvoie**

Cette fonction renvoie un tableau avec un enregistrement unique et une colonne de type `long`. La valeur de la cellule seule est le nombre d’enregistrements dans *T*. 

**Exemple**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>évaluer l’opérateur

`evaluate`est un mécanisme d’extension qui permet aux algorithmes spécialisés à ajouter aux requêtes.

`evaluate`doit être le dernier opérateur dans le pipeline de requête (à l’exception d’un possible `render`). Il ne doit pas apparaître dans le corps d’une fonction.

[évaluer les autocluster](#evaluate-autocluster) | [évaluer le panier](#evaluate-basket) | [évaluer les diffpatterns](#evaluate-diffpatterns) | [évaluer extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>évaluer les autocluster

     T | evaluate autocluster()

AutoCluster recherche des modèles courants d’attributs discrets (dimensions) dans les données et permet de réduire les résultats de la requête d’origine (s’il s’agit des lignes k 100 ou 100) à un petit nombre de modèles. AutoCluster a été développé pour vous aider à analyser les échecs (par exemple, les exceptions, les incidents) mais peut fonctionner sur n’importe quel jeu de données filtré. 

**Syntaxe**

    T | evaluate autocluster( arguments )

**Propriété renvoie**

AutoCluster renvoie un jeu (généralement de petite taille) des motifs qui capture des portions de données avec les valeurs courantes partagées entre plusieurs attributs discrets. Chaque modèle est représenté par une ligne dans les résultats. 

Les deux premières colonnes sont le nombre et le pourcentage de lignes de la requête d’origine qui sont capturées par le modèle. Les colonnes restantes sont à partir de la requête d’origine et leur valeur est soit une valeur spécifique de la colonne ou ' *' ce qui signifie que des valeurs de variables. 

Notez que les modèles ne sont pas disjoints : ils peuvent se recouvrir et généralement, ne couvrent pas toutes les lignes d’origine. Certaines lignes ne doivent pas tomber sous n’importe quel modèle.

**Conseils**

* Utilisez `where` et `project` dans le canal d’entrée afin de réduire les données à tout ce qui vous intéresse.
* Lorsque vous trouvez une ligne intéressante, vous pouvez souhaiter y plonger davantage en ajoutant des valeurs spécifiques à votre `where` filtre.

**Arguments (facultatifs)**

* `output=all | values | minimal` 

    Le format des résultats. Les nombre et le pourcentage colonnes apparaissent toujours dans les résultats. 

 * `all`-toutes les colonnes de l’entrée sont sortie
 * `values`-filtres de colonnes avec uniquement « * » dans les résultats
 * `minimal`-filtre aussi les colonnes qui sont identiques pour toutes les lignes de la requête d’origine. 


* `min_percent=`*double* (par défaut : 1)

    La couverture de pourcentage minimal de lignes générées.

    Exemple :`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*int* (par défaut : 25) 

    Le nombre de graines détermine le nombre de points de recherche locale initiale de l’algorithme. Dans certains cas, en fonction de la structure des données, l’augmentation du nombre de graines augmente le nombre (ou qualité) des résultats dans l’espace de recherche accrue au compromis de requête plus lent. L’argument num_seeds a réduire les résultats dans les deux directions afin de réduire ce inférieur à 5 obtiendrez des améliorations de performances négligeable et augmenter au-dessus de 50 génère rarement des modèles supplémentaires.

    Exemple :`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < double < 1*+ (par défaut : 0,5)

    Vous permet de contrôler l’équilibre entre générique (couverture élevée) et informatifs (nombreuses valeurs partagées). Size_weight de plus en plus réduit généralement le nombre de modèles, et chaque modèle est généralement couvrir une plus grande. Réduction de size_weight généralement génère des modèles plus spécifiques et plus les valeurs partagées plus petit pourcentage couverture. Les coulisses formule est une moyenne géométrique pondérée entre le score générique normalisé et le score informatif avec size_weight et size_weight-1 comme les poids. 

    Exemple :`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*nom_colonne*

    Considère que chaque ligne de l’entrée en fonction du poids spécifié (par défaut chaque ligne a une épaisseur de '1'), une utilisation courante d’une colonne de poids est de prendre en compte échantillonnage ou sur la création de compartiments / l’agrégation des données qui sont déjà incorporées dans chaque ligne.

    Exemple :`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>évaluer le panier

     T | evaluate basket()

Panier de recherche tous les modèles de fréquentes des attributs discrets (dimensions) dans les données et retournera tous les motifs de fréquentes que passé le seuil de fréquence dans la requête d’origine. Panier est certain de trouver tous les modèles de fréquents dans les données, mais n’est pas garantie pour que l’exécution polynomiale. L’exécution de la requête est linéaire dans le nombre de lignes, mais dans certains cas peut être exponentielle du nombre de colonnes (dimensions). Panier est basé sur l’algorithme à priori de développé à l’origine pour le data mining analyse de panier. 

**Propriété renvoie**

Tous les modèles figurant dans plus d’une fraction spécifiée (valeur par défaut 0,05) des événements.

**Arguments (facultatifs)**


* `threshold=`*0.015 < double < 1* (par défaut : 0,05) 

    Définit le ratio minimal de lignes à prendre en considération fréquentes (modèles avec le plus petit rapport ne seront pas retournés).

    Exemple :`T | evaluate basket("threshold=0.02")`


* `weight_column=`*nom_colonne*

    Considère que chaque ligne de l’entrée en fonction du poids spécifié (par défaut chaque ligne a une épaisseur de '1'), une utilisation courante d’une colonne de poids est de prendre en compte échantillonnage ou sur la création de compartiments / l’agrégation des données qui sont déjà incorporées dans chaque ligne.

    Exemple : T | évaluer les basket("weight_column=sample_Count")


* `max_dims=`*1 < int* (par défaut : 5)

    Définit le nombre maximal de dimensions non corrélées par panier, limitée par défaut à diminuer l’exécution de la requête.


* `output=minimize` | `all` 

    Le format des résultats. Les nombre et le pourcentage colonnes apparaissent toujours dans les résultats.

 * `minimize`-filtres de colonnes avec uniquement « * » dans les résultats.
 * `all`-toutes les colonnes de l’entrée sont affichés.




#### <a name="evaluate-diffpatterns"></a>évaluer les diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns compare deux ensembles de données de la même structure et recherche des modèles d’attributs discrets (dimensions) qui caractérisent les différences entre les deux ensembles de données. Diffpatterns a été développé pour vous aider à analyser les échecs (par exemple, en comparaison des défaillances non-échecs dans un laps de temps donné) mais peut potentiellement trouver les différences entre les deux ensembles de données de la même structure. 

**Syntaxe**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**Propriété renvoie**

Diffpatterns renvoie un jeu (généralement petit) des modèles afin de saisir les différentes parties des données dans les deux jeux (c'est-à-dire un modèle un pourcentage élevé des lignes de la première série de données et le faible pourcentage de lignes dans le second jeu de capture). Chaque modèle est représenté par une ligne dans les résultats.

Les quatre premières colonnes sont le nombre et le pourcentage de lignes de la requête d’origine qui sont capturées par le modèle dans chaque jeu, la cinquième colonne est la différence (exprimée en points de pourcentage absolu) entre les deux ensembles. Les colonnes restantes sont à partir de la requête d’origine et leur valeur est soit une valeur spécifique de la colonne ou * ce qui signifie que des valeurs de variables. 

Notez que les modèles ne sont pas distinctes : ils peuvent se recouvrir et généralement, ne couvrent pas toutes les lignes d’origine. Certaines lignes ne doivent pas tomber sous n’importe quel modèle.

**Conseils**

* Utiliser où et de projet dans le tuyau d’entrée afin de réduire les données à tout ce qui vous intéresse.

* Lorsque vous trouvez une ligne intéressante, vous pouvez souhaiter y plonger davantage en ajoutant des valeurs spécifiques à votre emplacement filtrer.

**Arguments**

* `split=`*nom de la colonne* (obligatoire)

    La colonne doit avoir exactement deux valeurs. Si nécessaire, créez une colonne de ce type :

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*chaîne*

    Indique l’algorithme de rechercher uniquement les modèles qui ont un pourcentage plus élevé dans le jeu de données cible, la cible doit être une des deux valeurs de la colonne de fractionnement.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0.015 < double < 1* (par défaut : 0,05) 

    Définit la différence de modèle minimal (rapport) entre les deux ensembles.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    Le format des résultats. Les nombre et le pourcentage colonnes apparaissent toujours dans les résultats. 

 * `minimize`-filtres de colonnes avec uniquement « * » dans les résultats
 * `all`-toutes les colonnes de l’entrée sont sortie

* `weight_column=`*nom_colonne*

    Considère que chaque ligne de l’entrée en fonction du poids spécifié (par défaut, chaque ligne a une épaisseur de '1'). Une utilisation courante d’une colonne de poids est de prendre en compte échantillonnage ou sur la création de compartiments / l’agrégation des données qui sont déjà incorporées dans chaque ligne.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>évaluer les extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns est utilisé pour enrichir une table avec plusieurs colonnes simples qui sont dynamiquement extraites de (semi) colonne (s) structurées selon leur type. Actuellement il prend en charge les colonnes json uniquement, à la fois dynamique et la sérialisation de jsons de chaîne.


* `max_columns=`*int* (par défaut : 10) 

    Le nombre de nouvelles colonnes ajoutées est dynamique et il peut être très grand (il est en fait le nombre de clés distinctes dans tous les enregistrements de json) afin que nous devons le limiter. Les nouvelles colonnes sont triées dans l’ordre décroissant selon leur fréquence et à max_columns sont ajoutés à la table.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*double* (par défaut : 10.0) 

    Une autre façon de limiter les nouvelles colonnes en ignorant les colonnes dont la fréquence est inférieure à min_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*bool* (par défaut : true) 

    Une valeur True indique le nom de la colonne complexe sera ajouté comme préfixe pour les noms de colonnes extraites.


* `prefix_delimiter=`*chaîne* (par défaut : « _ ») 

    Si add_prefix = true ce paramètre définit le délimiteur qui sera utilisé pour concaténer les noms des nouvelles colonnes.

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*bool* (par défaut : false) 

    Si true, les colonnes (json) d’origine restent dans la table de sortie.


* `output=query | table` 

    Le format des résultats. 

 * `table`-La sortie est la même table comme reçu moins la nouvelles colonnes qui ont été extraits à partir des colonnes d’entrée ainsi que des colonnes d’entrée spécifiés.
 * `query`-La sortie est une chaîne représentant la requête que vous préconisez pour obtenir le résultat sous forme de tableau. 




### <a name="extend-operator"></a>étendre l’opérateur

     T | extend duration = stopTime - startTime

Ajoutez une ou plusieurs colonnes calculées dans une table. 


**Syntaxe**

    T | extend ColumnName = Expression [, ...]

**Arguments**

* *T:* La table d’entrée.
* *ColumnName :* Au nom d’une colonne à ajouter. [Les noms](#names) sont sensibles à la casse et peut contenir un ordre alphabétique, numérique ou '_' caractères. Utilisez `['...']` ou `["..."]` pour citer les mots clés ou noms avec d’autres caractères.
* *Expression :* Un calcul sur les colonnes existantes.

**Propriété renvoie**

Une copie de la table d’entrée, avec les colonnes supplémentaires spécifiées.

**Conseils**

* Utilisez [`project`](#project-operator) au lieu de cela, si vous souhaitez également supprimer ou renommer certaines colonnes.
* N’utilisez pas `extend` pour obtenir un nom plus court à utiliser dans une expression de type long. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Les colonnes natifs de la table ont été indexés ; votre nouveau nom définit une colonne supplémentaire qui n’est pas indexée, la requête est susceptible de s’exécuter plus lentement.

**Exemple**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>opérateur de jointure

    Table1 | join (Table2) on CommonColumn

Fusionne les lignes des deux tables par les valeurs correspondantes de la colonne spécifiée.


**Syntaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Arguments**

* *Table1* - en le « côté gauche » de la jointure.
* *Table2* - « droite » de la jointure. Il peut être une expression de requête imbriquée qui renvoie une table.
* *CommonColumn* - une colonne qui porte le même nom dans les deux tables.
* *Type* - indique comment les lignes des deux tables doivent être mis en correspondance.

**Propriété renvoie**

Un tableau avec :

* Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes. Les colonnes du côté droit seront renommés automatiquement s’il existe des conflits de nom.
* Une ligne pour chaque correspondance entre les tables d’entrée. Une correspondance est une ligne sélectionnée dans une table qui a la même valeur pour toutes les les `on` les champs sous la forme d’une ligne dans l’autre table. 

* `Kind`non spécifié

    Une seule ligne à partir de la gauche est mis en correspondance pour chaque valeur de la `on` clé. La sortie contient une ligne pour chaque occurrence de cette ligne avec les lignes à partir de la droite.

* `Kind=inner`
 
     Il existe une ligne dans la sortie pour chaque combinaison de lignes correspondantes à partir de la gauche et de droite.

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     En plus des correspondances internes, il existe une ligne pour chaque ligne sur la gauche (et/ou la droite), même si elle n’a aucune correspondance. Dans ce cas, les cellules de sortie qui ne correspondent pas contient de valeurs NULL.

* `kind=leftanti`

     Renvoie tous les enregistrements du côté gauche qui n’ont pas de correspondance à partir de la droite. La table de résultats a simplement les colonnes du côté gauche. 
 
S’il existe plusieurs lignes avec les mêmes valeurs pour ces champs, vous obtiendrez des lignes pour toutes les combinaisons.

**Conseils**

Pour des performances optimales :

* Utilisez `where` et `project` pour réduire le nombre de lignes et de colonnes dans les tables d’entrée, avant le `join`. 
* Si une table est toujours inférieure à l’autre, l’utiliser comme (canal) gauche de la jointure.
* Les colonnes de la correspondance de jointure doivent avoir le même nom. Utilisez l’opérateur de projet si nécessaire de renommer une colonne dans une des tables.

**Exemple**

Sont accordées des activités à partir d’un journal dans lequel certaines entrées marque le début et la fin d’une activité. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>opérateur de limite

     T | limit 5

Retourne le nombre spécifié de lignes de la table d’entrée. Il n’y a aucune garantie que les enregistrements sont retournés. (Pour renvoyer des enregistrements spécifiques, utilisez [`top`](#top-operator).)

**Alias**`take`

**Syntaxe**

    T | limit NumberOfRows


**Conseils**

`Take`est un moyen simple et efficace pour voir un exemple des résultats pendant que vous travaillez de manière interactive. N’oubliez pas qu’il ne garantit pas de produire des lignes particulières, ou pour les produire dans un ordre particulier.

Il existe une limite implicite du nombre de lignes retournées au client, même si vous n’utilisez pas `take`. Pour lever cette limite, vous devez utiliser le `notruncation` option de demande du client.



### <a name="mvexpand-operator"></a>opérateur de mvexpand

    T | mvexpand listColumn 

Développe une liste à partir d’une cellule (JSON) typée dynamique afin que chaque entrée comporte une ligne distincte. Toutes les autres cellules d’une ligne de développé sont dupliqués. 

(Voir aussi [`summarize makelist`](#summarize-operator) qui effectue la fonction inverse.)

**Exemple**

Supposons que la table d’entrée est :

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|« hello »|{« clé » : « valeur »}|
|2|« world »|[0, 1, « k », « v »]|

    mvexpand D

Il en résulte :

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|« hello »|{« clé » : « valeur »}|
|2|« world »|0|
|2|« world »|1|
|2|« world »|« k »|
|2|« world »|« v »|


**Syntaxe**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Arguments**

* *ColumnName :* Dans le résultat, les tableaux dans la colonne nommée sont étendues à plusieurs lignes. 
* *ArrayExpression :* Une expression produisant un tableau. Si ce formulaire est utilisé, une nouvelle colonne est ajoutée et l’existante est conservée.
* *Nom :* Nom de la nouvelle colonne.
* *Typename :* Effectue un cast de l’expression à un type particulier de développé
* *RowLimit :* Le nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 128.

**Propriété renvoie**

Plusieurs lignes de chacune des valeurs dans n’importe quelle baie dans la colonne nommée, ou dans l’expression de tableau.

La colonne de développé a toujours le type dynamique. Utilisez un cast tel que `todatetime()` ou `toint()` si vous souhaitez calculer ou agrégation de valeurs.

Deux modes d’expansions de sac de propriétés sont prises en charge :

* `bagexpansion=bag`: Conteneurs de propriétés sont développés dans les conteneurs de propriétés d’entrée unique. Il s’agit de l’extension par défaut.
* `bagexpansion=array`: Conteneurs de propriétés sont développés en deux éléments `[` *clé*`,`*valeur* `]` tableau des structures, permettant un accès uniforme à des clés et des valeurs (comme, par exemple, en exécutant une agrégation de comptage distincte sur les noms de propriété). 

**Exemples**


    exceptions | take 1 
  	| mvexpand details[0]

Fractionne un enregistrement d’exception en lignes pour chaque élément dans le champ Détails.



### <a name="parse-operator"></a>opérateur d’analyse

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Extrait des valeurs d’une chaîne. Peut utiliser la correspondance d’expression régulière ou de simple.

**Syntaxe**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Arguments**

* `T`: La table d’entrée.
* `kind`: 
 * `simple`(par défaut) : le `Match` chaînes sont des chaînes de texte brut.
 * `relaxed`: si le texte n’analyse que le type d’une colonne, la colonne est définie à null et l’analyse continue 
 * `regex`: le `Match` chaînes sont des expressions régulières.
* `Text`: Une colonne ou une autre expression qui prend la valeur ou peut être convertie en une chaîne.
* *Correspondance :* Correspond à la partie suivante de la chaîne et l’ignorer.
* *Colonne :* Affecter à l’article suivant de la chaîne à cette colonne. La colonne est créée si elle n’existe pas.
* *Type :* Analyser la partie suivante de la chaîne en tant que type spécifié, tel qu’int, date, double. 


**Propriété renvoie**

La table d’entrée, étendu en fonction de la liste des colonnes.

Les éléments de la `with` clause sont mis en correspondance avec le texte source à son tour. Chaque élément chews sur un segment du texte source : 

* Une expression régulière ou une chaîne littérale déplace le curseur correspondant à la longueur de la correspondance.
* Dans une analyse de l’expression régulière, une expression régulière peut utiliser l’opérateur de réduction ' ?' pour vous déplacer dans les meilleurs délais à la correspondance suivante.
* Un nom de colonne avec un type d’analyse le texte en tant que type spécifié. Si type = moyenne, une analyse infructueuses invalide correspondant au modèle entier.
* Un nom de colonne, un type, avec ou sans le type 'string', copie le nombre minimal de caractères pour atteindre la correspondance suivante.
* ' *' Ignore le nombre minimal de caractères pour atteindre la correspondance suivante. Vous pouvez utiliser «*» au début et à la fin du modèle, ou après un type autre que string ou entre les correspondances de la chaîne.

Tous les éléments dans un modèle d’analyse doivent correspondre correctement ; dans le cas contraire, aucun résultat ne sera produit. L’exception à cette règle est que, lorsque la nature = continue de souple, en cas d’échec de l’analyse d’une variable typée, le reste de l’analyse.

**Exemples**

*Simple :*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | compteur | présent | Année
---|---|---|---
1 | 2 | SOCKS | 63

*Souple :*

Lorsque l’entrée contient une correspondance appropriée pour chaque colonne typé, une analyse souple produit les mêmes résultats sous la forme d’une simple analyse. Mais si une des colonnes typées n’analyse correctement, une analyse souple continue à traiter le reste du modèle, qu’une simple analyse s’arrête et ne peut pas générer un résultat.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | présent | Année
---|---|---
1 |  SOCKS | 63


*Expression régulière :*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

ressources | tranche | verrou | mise à jour | précédent
---|---|---|---|---
Planificateur | 16 | 17/02/2016 08:41:00 | 17/02/2016 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>opérateur de projet

    T | project cost=price*quantity, price

Sélectionnez les colonnes à inclure, renommer ou supprimer et insérer de nouvelles colonnes calculées. L’ordre des colonnes dans le résultat est spécifié par l’ordre des arguments. Uniquement les colonnes spécifiées dans les arguments sont inclus dans le résultat : des autres dans l’entrée sont supprimées.  (Voir aussi `extend`.)


**Syntaxe**

    T | project ColumnName [= Expression] [, ...]

**Arguments**

* *T:* La table d’entrée.
* *ColumnName :* Le nom d’une colonne d’apparaître dans la sortie. S’il n’existe pas d' *Expression*, une colonne de ce nom doit apparaître dans l’entrée. [Les noms](#names) sont sensibles à la casse et peut contenir un ordre alphabétique, numérique ou '_' caractères. Utilisez `['...']` ou `["..."]` pour citer les mots clés ou noms avec d’autres caractères.
* *Expression :* Expression scalaire facultative faisant référence à des colonnes d’entrée. 

    Il n’est autorisé que pour renvoyer une nouvelle colonne calculée avec le même nom qu’une colonne existante dans l’entrée.

**Propriété renvoie**

Une table qui comporte les colonnes nommées en tant qu’arguments, ainsi que le nombre de lignes que la table d’entrée.

**Exemple**

L’exemple suivant montre plusieurs types de manipulations qui peuvent être effectuées à l’aide de la `project` opérateur. La table d’entrée `T` comporte trois colonnes de type `int`: `A`, `B`, et `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>opérateur de projet de rangement

    T | project-away column1, column2, ...

Exclure les colonnes spécifiées. Le résultat contient toutes les colonnes d’entrée, à l’exception de ceux que vous nommez.

### <a name="range-operator"></a>opérateur de plage

    range LastWeek from ago(7d) to now() step 1d

Génère une table d’une seule colonne de valeurs. Notez qu’il n’a pas un pipeline d’entrée. 

|SemaineDernière|
|---|
|09:10:04.627 de 2015-12-05|
|12 / 2015 / 06 09:10:04.627|
|...|
|12 / 12 / 2015 09:10:04.627|



**Syntaxe**

    range ColumnName from Start to Stop step Step

**Arguments**

* *ColumnName :* Le nom de la colonne dans la table de sortie.
* *Démarrer :* La plus petite valeur dans la sortie.
* *Arrêter :* La valeur la plus élevée en cours de génération dans le résultat (ou une limite sur la valeur la plus élevée, si *l’étape* les étapes sur cette valeur).
* *Étape :* La différence entre deux valeurs consécutives. 

Les arguments doivent être des valeurs de type numérique, date ou timespan. Ils ne peuvent pas référencer les colonnes d’une table. (Si vous souhaitez calculer la plage basée sur une table d’entrée, utilisez la [ *fonction*de la plage](#range), peut-être avec l' [opérateur de mvexpand](#mvexpand-operator).) 

**Propriété renvoie**

Une table avec une colonne unique nommée *ColumnName*, dont les valeurs sont de *Démarrer*, *Démarrer* + *étape*,... jusqu'à et y compris *Arrêter*.

**Exemple**  

```AIQL
range Steps from 1 to 8 step 3
```

Une table avec une colonne appelée `Steps` dont le type est `long` et dont les valeurs sont `1`, `4`, et `7`.

**Exemple**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Une table de minuit au cours des sept derniers jours. La fonction de l’emplacement (au sol) réduit chaque fois au début de la journée.

**Exemple**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Montre comment les `range` opérateur peut être utilisé pour créer une petite, ad hoc, une table de dimension qui est ensuite utilisé pour introduire les zéros non significatifs dans lequel la source de données n’a aucune valeur.

### <a name="reduce-operator"></a>réduire l’opérateur

    exceptions | reduce by outerMessage

Essaie de regrouper des enregistrements semblables. Pour chaque groupe, l’opérateur renvoie la `Pattern` il pense le mieux à ce groupe et le `Count` d’enregistrements dans ce groupe.


![](./media/app-insights-analytics-reference/reduce.png)

**Syntaxe**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Arguments**

* *ColumnName :* La colonne à examiner. Il doit être de type chaîne.
* *Seuil :* Une valeur dans la plage de {valeur 0.. 1}. Valeur par défaut est 0,001. Pour les entrées de grande taille, le seuil doit être faible. 

**Propriété renvoie**

Deux colonnes, `Pattern` et `Count`. Dans de nombreux cas, le modèle sera une valeur complète de la colonne. Dans certains cas, il peut identifier les termes courants et remplacez les pièces de variable avec ' *'.

Par exemple, le résultat de le `reduce by city` peut inclure : 

|Modèle | Nombre |
|---|---|
| SAN * | 5182 |
| Saint * | 2846 |
| Moscou | 3726 |
| \*-sur-\* | 2730 |
| Paris | 27163 |


### <a name="render-directive"></a>rendre la directive

    T | render [ table | timechart  | barchart | piechart ]

Rendu dirige la couche de présentation de l’affichage de la table. Il doit être le dernier élément du tuyau. Il s’agit d’une alternative pratique à l’utilisation des contrôles de l’affichage, ce qui vous permet d’enregistrer une requête avec une méthode de présentation particulière.

### <a name="restrict-clause"></a>clause de restriction 

Spécifie le jeu de noms de tables disponibles aux opérateurs qui suivent. Par exemple :

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>opérateur de tri 

    T | sort by country asc, price desc

Trier les lignes de la table d’entrée dans l’ordre par une ou plusieurs colonnes.

**Alias**`order`

**Syntaxe**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Arguments**

* *T:* Le tableau d’entrée à trier.
* *Colonne :* Colonne de *T* par lequel trier. Le type des valeurs doit être numérique, de date, d’heure ou de chaîne.
* `asc`Effectuez un tri en ordre croissant, faible à élevé. La valeur par défaut est `desc`, décroissant élevé à faible.

**Exemple**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Toutes les lignes dans les Traces de table ayant un certain `ActivityId`, trié par leur horodatage.

### <a name="summarize-operator"></a>résumer l’opérateur

Génère une table qui regroupe le contenu de la table d’entrée.
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Un tableau qui indique le nombre, la durée moyenne de demande et l’ensemble des villes dans chaque pays. Il existe une ligne dans la sortie pour chaque pays distincts. Les colonnes de sortie affichent le nombre, durée moyenne, villes et pays. Toutes les autres colonnes d’entrée sont ignorées.


    T | summarize count() by price_range=bin(price, 10.0)

Un tableau qui indique le nombre d’éléments que les prix dans chaque intervalle [0,10.0], [10.0,20.0], et ainsi de suite. Cet exemple comporte une colonne pour le nombre et l’autre pour la fourchette de prix. Toutes les autres colonnes d’entrée sont ignorées.


**Syntaxe**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Arguments**

* *Colonne :* Nom facultatif d’une colonne de résultat. Par défaut, un nom dérivé de l’expression. [Les noms](#names) sont sensibles à la casse et peut contenir un ordre alphabétique, numérique ou '_' caractères. Utilisez `['...']` ou `["..."]` pour citer les mots clés ou noms avec d’autres caractères.
* *Agrégation :* Un appel à une fonction d’agrégation telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Voir [agrégations](#aggregations).
* *GroupExpression :* Une expression par rapport aux colonnes, qui fournit un ensemble de valeurs distinctes. En général, il est soit un nom de colonne qui fournit déjà un ensemble restreint de valeurs, ou `bin()` avec une colonne numérique ou temps en tant qu’argument. 

Si vous fournissez une expression numérique ou de temps sans l’aide de `bin()`, Analytique applique automatiquement avec un intervalle de `1h` pour les heures, ou `1.0` pour les nombres.

Si vous ne fournissez pas une *GroupExpression,* l’ensemble du tableau est résumé dans une ligne de sortie unique.



**Propriété renvoie**

Les lignes d’entrée sont disposées en groupes ayant les mêmes valeurs de la `by` expressions. Puis, les fonctions d’agrégation spécifiée sont calculées sur chaque groupe, en produisant une ligne pour chaque groupe. Le résultat contient la `by` colonnes et également au moins une colonne pour chaque agrégat de calculée. (Certaines fonctions d’agrégation retournent plusieurs colonnes.)

Le résultat a autant de lignes qu’il sont a des combinaisons distinctes de `by` valeurs. Si vous souhaitez synthétiser sur des plages de valeurs numériques, utilisez `bin()` pour réduire des plages de valeurs discrètes.

**Remarque**

Bien que vous pouvez fournir des expressions arbitraires pour l’agrégation et les expressions de regroupement, il est plus efficace d’utiliser des noms de colonne simples ou appliquer `bin()` d’une colonne numérique.



### <a name="take-operator"></a>opérateur Take

Alias de [limite](#limit-operator)


### <a name="top-operator"></a>opérateur Top

    T | top 5 by Name desc nulls first

Retourne les *N* premiers enregistrements triés par les colonnes spécifiées.


**Syntaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Arguments**

* *NumberOfRows :* Le nombre de lignes de *T* à retourner.
* *Sort_expression :* Expression utilisée trier les lignes. Il s’agit généralement d’un nom de colonne. Vous pouvez spécifier plus d’une sort_expression.
* `asc`ou `desc` (la valeur par défaut) peut apparaître au contrôle si sélection provient réellement de « bas » ou « haut » de la plage.
* `nulls first`ou `nulls last` contrôles où apparaissent les valeurs null. `First`est la valeur par défaut pour `asc`, `last` est la valeur par défaut de `desc`.


**Conseils**

`top 5 by name`en apparence équivaut à `sort by name | take 5`. Toutefois, il s’exécute plus rapidement et renvoie le tri des résultats, qu’il `take` aucune garantie de ce type.

### <a name="top-nested-operator"></a>opérateur de haut-imbriquée

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

Produit des résultats hiérarchiques, où chaque niveau est un zoom à partir du niveau précédent. Il est utile pour répondre aux questions qui vous sembler « quelles sont les requêtes top 5 et pour chacun d’eux, quels sont les compartiments 3 top de ses performances, et pour chacun d’eux, qui sont les pays haut 3 les demandes proviennent de »

**Syntaxe**

   T | imbriquées en haut des colonnes N par agrégation [,...]

**Arguments**

* N:int - nombre de lignes à renvoyer ou à passer au niveau suivant. Dans une requête avec trois niveaux, où N est 3, 3 et 5, le nombre total de lignes sera 45.
* COLONNE - une colonne pour grouper par pour l’agrégation. 
* AGRÉGATION - il s’agit d’une [fonction d’agrégation](#aggregations) à appliquer à chaque groupe de lignes. Les résultats de ces groupements déterminera les principaux groupes à afficher.


### <a name="union-operator"></a>opérateur d’union

     Table1 | union Table2, Table3

Prend deux ou plusieurs tables et renvoie les lignes de chacun d'entre eux. 

**Syntaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Arguments**

* *Table1*, *Table2* ...
 *  Le nom d’une table, tel que `requests`, ou une table définie dans une [clause de let](#let-clause); ou
 *  Une expression de requête tels que`(requests | where success=="True")`
 *  Un ensemble de tables spécifiées avec un caractère générique. Par exemple, `e*` serait forment l’union de toutes les tables définies dans des clauses permettent aux précédentes, dont le nom commence par 'e', ainsi que de la table « exceptions ».
* `kind`: 
 * `inner`-Il a été le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
 * `outer`-Le résultat comporte toutes les colonnes qui se produisent dans une des entrées. Les cellules qui n’ont pas été définies par une ligne d’entrée sont définies sur `null`.
* `withsource=`*ColumnName :* Si spécifié, la sortie inclut une colonne appelée *ColumnName* , dont la valeur indique à quelle table source a contribué à chaque ligne.

**Propriété renvoie**

Une table avec autant de lignes qu’il sont de toutes les tables d’entrée et autant de colonnes qu’il est des noms de colonne unique dans les entrées.

**Exemple**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
L’Union de toutes les tables dont les noms commencent « tt ».


**Exemple**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
Le nombre d’utilisateurs distincts qui ont produit soit une `exceptions` événement ou un `traces` événement sur le jour précédent. Dans le résultat, la colonne « SourceTable » indique « Query » ou « Commande ».

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Cette version plus efficace donne le même résultat. Il filtre chaque table avant la création de l’union.

### <a name="where-operator"></a>où opérateur

     requests | where resultCode==200

Filtre d’une table pour le sous-ensemble de lignes satisfaisant à un prédicat.

**Alias**`filter`

**Syntaxe**

    T | where Predicate

**Arguments**

* *T:* L’entrée sous forme de tableau, dont les enregistrements doivent être filtrées.
* *Prédicat :* A `boolean` [expression](#boolean) par rapport aux colonnes de *T*. Elle est évaluée pour chaque ligne dans *T*.

**Propriété renvoie**

Lignes de *T* pour laquelle *prédicat* est `true`.

**Conseils**

Pour obtenir les meilleures performances :

* **Comparaisons simples d’utilisation** entre les constantes et les noms de colonne. (« Constante » signifie la constante sur la table - pour `now()` et `ago()` sont OK, et ce sont des valeurs scalaires affectés dans un [ `let` clause](#let-clause).)

    Par exemple, `where Timestamp >= ago(1d)` à `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest conditions de première**: Si vous avez plusieurs clauses unies avec `and`, placez tout d’abord les clauses qui impliquent qu’une colonne. Par conséquent, `Timestamp > ago(1d) and OpId == EventId` est meilleure que l’inverse.


**Exemple**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Enregistrements qui n’antérieurs à 1 heure, proviennent de la Source appelée « Kuskus » et deux colonnes de la même valeur. 

Notez que nous mettons la comparaison entre les deux colonnes en dernier, car il ne peut pas utiliser l’index et force une analyse.


### <a name="where-in-operator"></a>opérateur de WHERE

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**Syntaxe**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**Arguments**

* `col`: Une colonne de la table.
* `expr1`... : Une liste d’expressions scalaires.

Utilisez `in` est utilisée pour inclure uniquement les lignes où `col` est égale à une des expressions de `expr1...`.

Utilisez `!in` pour inclure uniquement les lignes où `col` n’est pas égale à une des expressions `expr1...`.  


## <a name="aggregations"></a>Agrégations

Les agrégations sont des fonctions utilisées pour combiner des valeurs dans les groupes créés dans le [résumer l’opération](#summarize-operator). Par exemple, dans cette requête, dcount() est une fonction d’agrégation :

    requests | summarize dcount(name) by success

### <a name="any"></a>tout 

    any(Expression)

Sélectionne une ligne du groupe et renvoie la valeur de l’expression spécifiée de manière aléatoire.

Cela est utile, par exemple, si une colonne a un grand nombre de valeurs similaires (par exemple, une colonne « texte de l’erreur ») et vous souhaitez échantillonner cette colonne une seule fois par une valeur unique de la clé de groupe composé. 

**Exemple**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Recherche une ligne dans le groupe qui réduit/optimisation de la *ExprToMaximize*et retourne la valeur de *ExprToReturn* (ou `*` pour renvoyer la ligne entière).

**Conseil**: les colonnes pass-through sont automatiquement renommés. Pour vous assurer que vous utilisez des noms de droite, examinez les résultats à l’aide de `take 5` avant de vous canaliser les résultats dans un autre opérateur.

**Exemples**

Pour chaque nom de la demande, afficher lorsque la requête la plus longue s’est produite :

    requests | summarize argmax(duration, timestamp) by name

Afficher tous les détails de la demande la plus longue, et pas seulement l’horodatage :

    requests | summarize argmax(duration, *) by name


Rechercher la plus petite valeur de chaque mesure, ainsi que sa date et autres données :

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>Moy.

    avg(Expression)

Calcule la moyenne d’une *Expression* au sein du groupe.

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

Renvoie le schéma minimal qui admet toutes les valeurs de *DynamicExpression*. 

Le type de colonne du paramètre doit être `dynamic` -un sac de tableau ou de la propriété. 

**Exemple**

    exceptions | summarize buildschema(details)

Résultat :

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Notez que `indexer` est utilisé pour marquer où vous devez utiliser un index numérique. Pour ce schéma, certains chemins d’accès valides seraient être (en supposant que ces index d’exemple est dans une plage) :

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Exemple**

Supposons que la colonne d’entrée possède trois valeurs dynamiques :

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Le schéma qui en résulte est le suivant :

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Le schéma indique que :

* L’objet racine est un conteneur avec quatre propriétés nommées x, y, z et t.
* La propriété appelée « x », ce qui peut être de type « int » ou de type « chaîne ».
* La propriété appelée « y » Impossible de type « double », ou un autre conteneur avec une propriété appelée « w » de type « chaîne ».
* Le ``indexer`` mot clé indique que « z » et « t » sont des tableaux.
* Chaque élément dans le tableau « z » est soit un entier ou une chaîne.
* « t » est un tableau de chaînes.
* Chaque propriété est implicitement optionnel et un tableau peut être vide.

##### <a name="schema-model"></a>Modèle de schéma

La syntaxe du schéma retourné est :

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Ils sont équivalents à un sous-ensemble des annotations de type machine à écrire, codé sous la forme d’une valeur dynamique. Dans la machine à écrire, le schéma de l’exemple serait :

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>nombre

    count([ Predicate ])

Renvoie le nombre de lignes pour lequel *prédicat* correspond à `true`. Si aucun *prédicat* n’est spécifié, retourne le nombre total d’enregistrements dans le groupe. 

**Perf astuce**: utilisez `summarize count(filter)` au lieu de`where filter | summarize count()`

> [AZURE.NOTE] Évitez d’utiliser count() pour rechercher le nombre de demandes, d’exceptions ou d’autres événements qui sont sont produites. Lorsque [l’échantillonnage](app-insights-sampling.md) est en fonctionnement, le nombre de points de données conservées dans les perspectives d’Application sera inférieur au nombre d’événements d’origine. Utilisez plutôt `summarize sum(itemCount)...`. La propriété itemCount reflète le nombre d’événements d’origine qui sont représentées par chaque point de données conservées.

### <a name="countif"></a>Nb.Si

    countif(Predicate)

Renvoie le nombre de lignes pour lequel *prédicat* correspond à `true`.

**Perf astuce**: utilisez `summarize countif(filter)` au lieu de`where filter | summarize count()`

> [AZURE.NOTE] Évitez d’utiliser countif() pour rechercher le nombre de demandes, d’exceptions ou d’autres événements qui sont sont produites. Lorsque [l’échantillonnage](app-insights-sampling.md) est en fonctionnement, le nombre de points de données sera inférieur au nombre d’événements réels. Utilisez plutôt `summarize sum(itemCount)...`. La propriété itemCount reflète le nombre d’événements d’origine qui sont représentées par chaque point de données conservées.

### <a name="dcount"></a>DCount

    dcount( Expression [ ,  Accuracy ])

Renvoie une estimation du nombre de valeurs distinctes de *Expr* dans le groupe. (Pour répertorier les valeurs distinctes, utilisez [`makeset`](#makeset).)

*Précision*, si spécifié, contrôle de l’équilibre entre vitesse et précision.

 * `0`= le calcul moins précis et la plus rapide.
 * `1`la valeur par défaut, qui équilibre le temps de précision et de calcul ; sur erreur de 0,8 %.
 * `2`= plus précis et plus lent le calcul ; Erreur de 0,4 % environ.

**Exemple**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Renvoie une estimation du nombre de valeurs distinctes *Expr* de lignes dans le groupe pour lequel *prédicat* a la valeur true. (Pour répertorier les valeurs distinctes, utilisez [`makeset`](#makeset).)

*Précision*, si spécifié, contrôle de l’équilibre entre vitesse et précision.

 * `0`= le calcul moins précis et la plus rapide.
 * `1`la valeur par défaut, qui équilibre le temps de précision et de calcul ; sur erreur de 0,8 %.
 * `2`= plus précis et plus lent le calcul ; Erreur de 0,4 % environ.

**Exemple**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makeList

    makelist(Expr [ ,  MaxListSize ] )

Retourne un `dynamic` tableau (JSON) de toutes les valeurs de l' *argument Expr* dans le groupe. 

* *MaxListSize* est une limite de type integer facultatif sur le nombre maximal d’éléments à retourner (valeur par défaut est *128*).

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

Retourne un `dynamic` tableau (JSON) de l’ensemble de valeurs distinctes prenant *Expr* dans le groupe. (Conseil : pour simplement compter les valeurs distinctes, utilisez [`dcount`](#dcount).)
  
*  *MaxSetSize* est une limite de type integer facultatif sur le nombre maximal d’éléments à retourner (valeur par défaut est *128*).

**Exemple**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Voir aussi la [ `mvexpand` opérateur](#mvexpand-operator) pour la fonction inverse.


### <a name="max-min"></a>Max, min

    max(Expr)

Calcule la valeur maximale de *Expr*.
    
    min(Expr)

Calcule la valeur minimale de *Expr*.

**Conseil**: cela vous donne le min ou max sur son propre - par exemple, les plus élevés ou plus bas prix. Mais si vous souhaitez que les autres colonnes de la ligne - par exemple, le nom du fournisseur et le prix le plus bas - [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile, centiles, percentilew, percentilesw

    percentile(Expression, Percentile)

Renvoie une estimation pour l' *Expression* de la position spécifiée dans le groupe. La précision dépend de la densité de population dans la région de la position.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Comme `percentile()`, mais calcule un nombre de valeurs de centile (ce qui est plus rapide que le calcul de chaque centile individuellement).

    percentilew(Expression, WeightExpression, Percentile)

Pourcentage pondéré. Utilisez ces données pré-regroupé.  `WeightExpression`est un entier qui indique le nombre de lignes d’origine est représenté par chaque ligne agrégé.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Comme `percentilew()`, mais calcule un nombre de valeurs de centile.

**Exemples**


La valeur de `duration` qui est supérieure à 95 % de l’échantillon de jeu et inférieure à 5 % de l’ensemble de l’exemple, calculé pour chaque nom de la demande :

    request 
  	| summarize percentile(duration, 95)
      by name

Omettez » par... » pour calculer pour l’ensemble du tableau.

Calculer les centiles plusieurs noms de demande différents simultanément :

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Les résultats montrent que pour la /Events/Index de la demande, 5 % des demandes sont répondu dans 2.44s moins de la moitié d'entre eux dans 3.52s, et 5 % sont plus lentes que 6.85s.

Calculer plusieurs statistiques :

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Centiles pondérées

Utilisez les fonctions de la position pondérée dans les cas où les données ont été préalablement agrégées. 

Par exemple, supposons que votre application exécute des milliers d’opérations par seconde, et vous souhaitez connaître leur latence. La solution simple est de générer une demande d’idées d’Application ou d’un événement personnalisé pour chaque opération. Cela créerait un grand nombre de trafic, bien que l’échantillonnage adaptive prenne effet pour la réduire. Mais vous pouvez décider d’implémenter une solution encore mieux : vous allez écrire du code dans votre application pour regrouper les données avant de l’envoyer aux analyses de l’Application. La synthèse agrégée est envoyée à des intervalles réguliers, en réduisant le débit de peut-être quelques points par minute.

Votre code prend un flux de mesures de latence en millisecondes. Par exemple :
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Il compte les mesures dans les emplacements suivants :`{ 10, 20, 30, 40, 50, 100 }`

Périodiquement, il effectue une série d’appels de TrackEvent, une pour chaque compartiment, avec des valeurs personnalisées dans chaque appel : 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

Dans Analytique, vous consultez un tel groupe d’événements comme suit :

`opCount` | `latency`| signification
---|---|---
8 | 10 | = 8 opérations dans l’emplacement de 10 MS
6 | 20 | = opérations 6 dans l’emplacement de 20 MS
3 | 30 | = 3 opérations dans l’emplacement de 30ms
1 | 40 | = opérations 1 dans l’emplacement 40ms

Pour obtenir une image précise de la distribution d’origine des latences d’événement, nous utilisons `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Les résultats sont les mêmes que si nous avions utilisé brut `percentiles` sur l’ensemble des mesures.

> [AZURE.NOTE] Centiles pondérées ne sont pas applicables aux [exemples de données](app-insights-sampling.md), où chaque ligne échantillonnée représente un échantillon aléatoire de lignes d’origine, plutôt que sur un emplacement. Les fonctions de centile brut sont appropriées des données échantillonnées.

#### <a name="estimation-error-in-percentiles"></a>Erreur d’estimation de centiles

L’agrégat centiles fournit une valeur approximative à l’aide de [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Quelques points importants : 

* Les limites de l’erreur d’estimation varient selon la valeur de la position requise. Le niveau de précision est à la fin de [0..100], mise à l’échelle, centiles 0 et 100 sont l’exacte valeurs minimale et maximale de la distribution. La précision diminue progressivement vers le milieu de l’échelle. Il est plus défavorable au niveau médian et est plafonné à 1 %. 
* Limites d’erreur sont observés sur le rang, pas de la valeur. Supposons que la position (X, 50) a retourné la valeur de Xm. L’estimation garantit qu’au moins de 49 % et au maximum de 51 % des valeurs de X sont inférieures à Xm. Il n’existe aucune limite théorique sur la différence entre Xm et réelle valeur médiane de X.

### <a name="stdev"></a>StDev

     stdev(Expr)

Retourne l’écart-type de *Expr* sur le groupe.

### <a name="variance"></a>écart

    variance(Expr)

Retourne la variance de *Expr* sur le groupe.

### <a name="sum"></a>somme

    sum(Expr)

Renvoie la somme de *Expr* sur le groupe.                      


## <a name="scalars"></a>Valeurs scalaires

[les casts](#casts) | [des comparaisons](#scalar-comparisons)
<br/>
[GetType](#gettype) | [hachage](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [NOT NULL](#notnull) | [toscalar](#toscalar)

Les types pris en charge sont :

| Type de      | Nom (s) supplémentaires   | Type .NET équivalent |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>Casts

Vous pouvez effectuer un cast d’un type à un autre. En général, si le sens de la conversion, il fonctionnera :

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Vérifier si une chaîne peut être convertie en un type spécifique :

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Comparaisons scalaires

||
---|---
`<` |Moins
`<=`|Inférieur ou égal à
`>` |Supérieur
`>=`|Supérieur ou égal à
`<>`|Pas égal à
`!=`|Pas égal à 
`in`| Opérande de droite est un tableau (dynamique) et l’opérande de gauche est égale à un de ses éléments.
`!in`| Opérande de droite est un tableau (dynamique) et l’opérande de gauche n’est pas égale à une de ses éléments.




### <a name="gettype"></a>GetType

**Propriété renvoie**

Chaîne représentant le type de stockage sous-jacent de l’argument unique. Ceci est particulièrement utile lorsque vous avez des valeurs de type `dynamic`: dans ce cas `gettype()` indiquent comment une valeur est encodée.

**Exemples**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>hachage

**Syntaxe**

    hash(source [, mod])

**Arguments**

* *source*: la source scalaire la valeur de hachage est calculée.
* *Mod*: le modulo valeur à appliquer sur le résultat du hachage.

**Propriété renvoie**

La valeur xxhash (long) de la valeur scalaire donné modulo la valeur mod donnée (si spécifié).

**Exemples**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>IFF

Le `iff()` fonction évalue le premier argument (le prédicat) et retourne soit la valeur de la deuxième ou la troisième arguments selon que le prédicat est `true` ou `false`. Les deuxième et le troisième arguments doivent être du même type.

**Syntaxe**

    iff(predicate, ifTrue, ifFalse)


**Arguments**

* *prédicat :* Une expression qui s’évalue à un `boolean` valeur.
* *ifTrue :* Une expression qui est évaluée et la valeur retournée par la fonction si le *prédicat* est évalué à `true`.
* *ifFalse :* Une expression qui est évaluée et la valeur retournée par la fonction si le *prédicat* est évalué à `false`.

**Propriété renvoie**

Cette fonction renvoie la valeur de *ifTrue* si le *prédicat* est évalué à `true`, ou la valeur *ifFalse* dans le cas contraire.

**Exemple**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>IsNull, isnotnull, NOT NULL

    isnull(parsejson("")) == true

Prend un argument unique et indique si elle est null.

**Syntaxe**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Propriété renvoie**

Valeur True ou false selon si la valeur est null ou non null.


|x|IsNull(x)
|---|---
| "" | False
|« x » | False
|parseJSON("")|valeur True
|parseJSON("[]")|False
|parseJSON("{}")|False

**Exemple**

    T | where isnotnull(PossiblyNull) | count

Notez qu’il existe d’autres moyens de réaliser cet effet :

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

Évalue une expression ou une requête et retourne le résultat sous la forme d’une valeur unique. Cette fonction est utile pour les calculs intermédiaires ; par exemple, calculer un nombre total d’événements et l’utiliser ensuite comme base.

**Syntaxe**

    toscalar(query)
    toscalar(scalar)

**Propriété renvoie**

L’argument évaluée. Si l’argument est un tableau, cette propriété renvoie la première colonne de la première ligne. (Conseillée est d’organiser que l’argument ne dispose qu’une seule colonne et ligne.)

**Exemple**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Littéraux booléens

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Opérateurs booléens

    and 
    or 

    

## <a name="numbers"></a>Numéros

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Littéraux numériques

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>Opérateurs arithmétiques

|| |
|---|-------------|
| + | Ajouter         |
| - | Soustraire    |
| * | Multiplier    |
| / | Diviser      |
| % | Modulo      |
||
|`<` |Moins
|`<=`|Inférieur ou égal à
|`>` |Supérieur
|`>=`|Supérieur ou égal à
|`<>`|Pas égal à
|`!=`|Pas égal à 


### <a name="abs"></a>ABS

**Syntaxe**

    abs(x)

**Arguments**

* x - entier, réel ou timespan

**Propriété renvoie**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>emplacement, plancher

Arrondit les valeurs à un nombre entier multiple de la taille d’un emplacement donné. Utilisé beaucoup dans les [`summarize by`](#summarize-operator) requête. Si vous avez un ensemble de dispersion de valeurs, ils seront regroupés dans un plus petit ensemble de valeurs spécifiques.

Alias `floor`.

**Syntaxe**

     bin(value, roundTo)
     floor(value, roundTo)

**Arguments**

* *valeur :* Un nombre, une date ou un timespan. 
* *roundTo :* La « taille ». Nombre, date ou timespan qui divise la *valeur*. 

**Propriété renvoie**

Multiple le plus proche de *roundTo* sous *valeur*.  
 
    (toint((value/roundTo)-0.5)) * roundTo

**Exemples**

Expression | Résultat
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


L’expression suivante calcule un histogramme des durées, avec une taille de paquet de 1 seconde :

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>EXP

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>plancher

Un alias de [`bin()`](#bin).

### <a name="gamma"></a>gamma

La [fonction gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Syntaxe**

    gamma(x)

**Arguments**

* *x:* Un nombre réel

Pour les nombres entiers positifs, `gamma(x) == (x-1)!` , par exemple, `gamma(5) == 4 * 3 * 2 * 1`.

Voir aussi [loggamma](#loggamma).


### <a name="log"></a>journal

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`doit être un nombre réel > 0. Dans le cas contraire, null est renvoyé.

### <a name="loggamma"></a>loggamma


Le logarithme népérien de la valeur absolue de la [fonction gamma](#gamma).

**Syntaxe**

    loggamma(x)

**Arguments**

* *x:* Un nombre réel


### <a name="rand"></a>RAND

Un générateur de nombres aléatoires.

* `rand()`-un nombre réel compris entre 0,0 et 1,0
* `rand(n)`-un entier compris entre 0 et n-1




### <a name="sqrt"></a>SQRT

La fonction racine carrée.  

**Syntaxe**

    sqrt(x)

**Arguments**

* *x:* Un nombre réel > = 0.

**Propriété renvoie**

* Un nombre positif tel que`sqrt(x) * sqrt(x) == x`
* `null`Si l’argument est négatif, ou ne peut pas être converti en un `real` valeur. 




### <a name="toint"></a>ToInt

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>ToDouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>Date et heure


[Il y a](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [maintenant](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Littéraux de date et d’heure

|||
---|---
**DateTime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Heures sont toujours en UTC. L’omission de la date qui donne un temps aujourd'hui.
`now()`|L’heure actuelle.
`now(`-*TimeSpan*`)`|`now()-`*TimeSpan*
`ago(`*TimeSpan*`)`|`now()-`*TimeSpan*
**TimeSpan**|
`2d`|2 jours
`1.5h`|1 heure et demie 
`30m`|30 minutes
`10s`|10 secondes
`0.1s`|0,1 seconde
`100ms`| 100 millisecondes
`10microsecond`|
`1tick`|100 NS
`time("15 seconds")`|
`time("2")`| 2 jours
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>Expressions de date et d’heure

Expression |Résultat
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Moins
`<=`|Inférieur ou égal à
`>` |Supérieur
`>=`|Supérieur ou égal à
`<>`|Pas égal à
`!=`|Pas égal à 




### <a name="ago"></a>Il y a

Soustrait un timespan donné à partir de l’heure de l’horloge UTC actuelle. Comme `now()`, cette fonction peut être utilisée plusieurs fois dans une instruction et l’heure de l’horloge UTC référencé est le même pour toutes les instanciations.

**Syntaxe**

    ago(a_timespan)

**Arguments**

* *a_timespan*: l’intervalle à soustraire de l’heure de l’horloge UTC actuelle (`now()`).

**Propriété renvoie**

    now() - a_timespan

**Exemple**

Toutes les lignes de l’horodatage de la dernière heure :

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>DatePart

    datepart("Day", datetime(2015-12-14)) == 14

Extrait une partie spécifiée d’une date sous la forme d’un entier.

**Syntaxe**

    datepart(part, datetime)

**Arguments**

* `part:String`-{« Année », « Mois », « Jour », « Heure », « Minute », « Seconde », « Milliseconde », « Microsecondes », « Nanosecondes »}
* `datetime`

**Propriété renvoie**

Long qui représente le composant spécifié.


### <a name="dayofmonth"></a>DayOfMonth

    dayofmonth(datetime("2016-05-15")) == 15 

Le nombre ordinal du jour du mois.

**Syntaxe**

    dayofmonth(a_date)

**Arguments**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>jour de la semaine

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Le nombre de jours écoulés depuis le précédent entier dimanche, sous la forme d’un `timespan`.

**Syntaxe**

    dayofweek(a_date)

**Arguments**

* `a_date`: A `datetime`.

**Propriété renvoie**

Le `timespan` depuis minuit au début de ce qui précède le dimanche, arrondi à un nombre entier de jours.

**Exemples**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>DAYOFYEAR

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Le nombre ordinal du jour de l’année.

**Syntaxe**

    dayofyear(a_date)

**Arguments**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getMonth

Obtenir le nombre de mois (1 à 12) à partir d’une valeur datetime.

**Exemple**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getYear

Obtenir l’année à partir de datetime.

**Exemple**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>maintenant

    now()
    now(-2d)

L’heure de l’horloge UTC actuelle, décalage éventuellement par un timespan donné. Cette fonction peut être utilisée plusieurs fois dans une instruction et l’heure de l’horloge référencé sera le même pour toutes les instances.

**Syntaxe**

    now([offset])

**Arguments**

* *décalage :* A `timespan`, ajouté à l’heure de l’horloge UTC actuelle. Par défaut : 0.

**Propriété renvoie**

L’heure de l’horloge UTC actuelle sous la forme d’un `datetime`.

    now() + offset

**Exemple**

Détermine l’intervalle depuis l’événement identifié par le prédicat :

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>ToDateTime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Vérifier si une chaîne est une date valide :

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>ToTimeSpan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>WeekOfYear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Le résultat de l’entier représente le numéro de semaine par la norme ISO 8601. Le premier jour de la semaine est le dimanche, et la première semaine de l’année est la semaine qui contient le premier jeudi de cette année. (Au cours des derniers jours d’une année peuvent contenir par conséquent certains jours de la semaine 1 de l’année suivante, ou les premiers jours peuvent contenir certains de semaine 52 ou 53 de l’année précédente).


## <a name="string"></a>Chaîne

[countof](#countof) | [Extraire](#extract) | [extractjson](#extractjson)  | [fonction isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [Remplacer](#replace) | [fractionnement](#split) | [strcat](#strcat) | [strlen](#strlen) | [sous-chaîne](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>Littéraux de chaîne

Les règles sont les mêmes que dans JavaScript.

Chaînes peuvent figurer soit entre apostrophes ou de guillemets doubles. 

Barre oblique inverse (`\`) est utilisé pour les caractères d’échappement, telle que `\t` (onglet), `\n` (nouvelle ligne) et les instances d’encadrement caractère apostrophe.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Littéraux de chaîne masquée

Les littéraux de chaîne obscurcies sont des chaînes qui masque Analytique lors de la sortie de la chaîne (par exemple, lors du suivi). Le processus d’obscurcissement remplace les caractères tout obscurcies par un début (`*`) caractères.

Pour créer un littéral de chaîne masqué, faites précéder le nom `h` ou 'H'. Par exemple :

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Comparaisons de chaînes

Opérateur|Description|Respect de la casse|Exemple de True
---|---|---|---
`==`|Est égal à |Oui| `"aBc" == "aBc"`
`<>` `!=`|Pas égal à|Oui| `"abc" <> "ABC"`
`=~`|Est égal à |N°| `"abc" =~ "ABC"`
`!~`|Pas égal à |N°| `"aBc" !~ "xyz"`
`has`|Droit côté (RHS) est un terme ensemble de gauche côté (LHS)|N°| `"North America" has "america"`
`!has`|RHS n’est pas une période complète de LHS|N°|`"North America" !has "amer"` 
`hasprefix`|RHS est un préfixe d’un terme dans LHS|N°|`"North America" hasprefix "ame"`
`!hasprefix`|RHS n’est pas un préfixe de n’importe quel terme dans LHS|N°|`"North America" !hasprefix "mer"`
`hassuffix`|RHS est un suffixe d’un terme dans LHS|N°|`"North America" hassuffix "rth"`
`!hassuffix`|RHS n’est pas un suffixe de n’importe quel terme dans LHS|N°|`"North America" !hassuffix "mer"`
`contains` | RHS se produit en tant que sous-chaîne de LHS|N°| `"FabriKam" contains "BRik"`
`!contains`| RHS ne se produit pas dans LHS|N°| `"Fabrikam" !contains "xyz"`
`containscs` | RHS se produit en tant que sous-chaîne de LHS|Oui| `"FabriKam" contains "Kam"`
`!containscs`| RHS ne se produit pas dans LHS|Oui| `"Fabrikam" !contains "Kam"`
`startswith`|RHS est une sous-chaîne initiale de LHS.|N°|`"Fabrikam" startswith "fab"`
`!startswith`|RHS n’est pas une sous-chaîne initiale de LHS.|N°|`"Fabrikam" !startswith "abr"`
`endswith`|RHS est une sous-chaîne de terminal de LHS.|N°|`"Fabrikam" endswith "kam"`
`!endswith`|RHS n’est pas une sous-chaîne de terminal de LHS.|N°|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS contient une correspondance pour RHS|Oui| `"Fabrikam" matches regex "b.*k"`
`in`|Est égal à un des éléments|Oui|`"abc" in ("123", "345", "abc")`
`!in`|Pas égal à un des éléments|Oui|`"bc" !in ("123", "345", "abc")`

Utilisez `has` ou `in` si vous testez la présence d’un terme ensemble lexical - c'est-à-dire, un symbole ou un mot alphanumérique délimitée par les caractères non alphanumériques ou de début ou de fin de champ. `has`effectue plus rapides que les `contains`, `startswith` ou `endswith`. La première de ces requêtes s’exécute plus rapidement :

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Compte les occurrences d’une sous-chaîne dans une chaîne. Correspondances de chaîne simple peuvent se chevaucher ; correspondance d’expression régulière ne le font pas.

**Syntaxe**

    countof(text, search [, kind])

**Arguments**

* *texte :* Une chaîne.
* *recherche :* La chaîne ordinaire ou une expression régulière à l’intérieur du *texte*.
* *type :* `"normal"|"regex"` Par défaut `normal`. 

**Propriété renvoie**

Le nombre de fois où la chaîne de recherche peut être mis en correspondance dans le conteneur. Correspondances de chaîne simple peuvent se chevaucher ; correspondance d’expression régulière ne le font pas.

**Exemples**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (pas 2 !)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>extraire

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtenir une correspondance pour une [expression régulière](#regular-expressions) à partir d’une chaîne de texte. Le cas échéant, il convertit ensuite la sous-chaîne extraite dans le type indiqué.

**Syntaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Arguments**

* *regex :* Une [expression régulière](#regular-expressions).
* *captureGroup :* Un positif `int` constante qui indique le groupe de capture pour extraire. 0 représente la correspondance entière, 1 pour la valeur correspondance à la première '(' parenthèse')' dans l’expression régulière, de 2 ou plus pour les parenthèses.
* *texte :* A `string` à rechercher.
* *typeLiteral :* Un littéral de type facultatif (par exemple, `typeof(long)`). S’il est fourni, la sous-chaîne extraite est convertie en ce type. 

**Propriété renvoie**

Si *regex* trouve une correspondance dans le *texte*: la sous-chaîne mis en correspondance avec le groupe de capture indiqué *captureGroup*, éventuellement converti à *typeLiteral*.

Si il n’y a aucune correspondance, ou Échec de la conversion de type : `null`. 

**Exemples**

L’exemple de chaîne de `Trace` est recherché dans une définition de `Duration`. La correspondance est convertie en `real`, puis il multipliée par une constante (`1s`) afin que `Duration` est de type `timespan`. Dans cet exemple, il est égal à 123.45 secondes :

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Cet exemple est équivalent à `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>la fonction IsEmpty, isnotempty, notempty

    isempty("") == true

True si l’argument est une chaîne vide ou null.
Voir aussi [isnull](#isnull).


**Syntaxe**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Propriété renvoie**

Indique si l’argument est une chaîne vide ou isnull.

|x|IsEmpty(x)
|---|---
| "" | valeur True
|« x » | False
|parseJSON("")|valeur True
|parseJSON("[]")|False
|parseJSON("{}")|False


**Exemple**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>ParseURL

Fractionner une URL en ses différentes parties.

**Syntaxe**

    parseurl(urlstring)

**Arguments**

* *urlstring :* UNE URL.

**Propriété renvoie**

Objet contenant les parties en tant que chaînes.

**Exemple**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>remplacer

Remplacer toutes les correspondances de l’expression régulière par une autre chaîne.

**Syntaxe**

    replace(regex, rewrite, text)

**Arguments**

* *regex :* L' [expression régulière](https://github.com/google/re2/wiki/Syntax) pour rechercher du *texte*. Il peut contenir des groupes de capture entre parenthèses' (' ')'. 
* *de réécriture :* Le remplacement regex pour toute correspondance par *matchingRegex*. Utilisez `\0` pour faire référence à la totalité de la correspondance, `\1` pour le premier groupe de capture, `\2` et ainsi de suite pour les groupes de capture suivants.
* *texte :* Une chaîne.

**Propriété renvoie**

*texte* après avoir remplacé toutes les correspondances de *l’expression régulière* avec les évaluations de la *réécrire*. Correspondances ne se chevauchent pas.

**Exemple**

Cette instruction :

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

A les conséquences suivantes :

| x    | Str | remplacé|
|---|---|---|
| 1    | Nombre est 1.000000  | Nombre était : 1.000000|
| 2    | Nombre est 2.000000  | Nombre était : 2.000000|
| 3    | Nombre est 3.000000  | Nombre était : 3.000000|
| 4    | Le numéro est 4.000000  | Nombre était : 4.000000|
| 5    | Le numéro est 5,000000  | Nombre était : 5,000000|
 



### <a name="split"></a>Fractionner

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Fractionne une chaîne donnée en fonction d’un délimiteur de donnée et renvoie un tableau avec les sous-chaînes conatined. Le cas échéant, une sous-chaîne spécifique peut être retourné si existe.

**Syntaxe**

    split(source, delimiter [, requestedIndex])

**Arguments**

* *source*: la chaîne source qui sera scindée selon le délimiteur donné.
* *délimiteur*: délimiteur qui sera utilisé pour la fractionner la chaîne source.
* *requestedIndex*: un index de base zéro facultatif `int`. S’il est fourni, le tableau de chaînes retourné contient la sous-chaîne demandée si existe. 

**Propriété renvoie**

Un tableau de chaînes qui contient les sous-chaînes de la chaîne source donnée sont délimités par le séparateur spécifié.

**Exemples**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

Concatène entre les arguments de 1 à 16, qui doivent être des chaînes.

### <a name="strlen"></a>strlen

    strlen("hello") == 5

Longueur d’une chaîne.

### <a name="substring"></a>sous-chaîne

    substring("abcdefg", 1, 2) == "bc"

Extraire une sous-chaîne d’une chaîne source donné à partir d’un index donné. Le cas échéant, la longueur de la sous-chaîne demandée peut être spécifiée.

**Syntaxe**

    substring(source, startingIndex [, length])

**Arguments**

* *source :* La chaîne source est extraite la sous-chaîne.
* *startingIndex :* Début caractère position de base zéro de la sous-chaîne demandée.
* *longueur :* Paramètre facultatif qui peut être utilisé pour spécifier le nombre de caractères dans la sous-chaîne. 

**Propriété renvoie**

Une sous-chaîne de la chaîne donnée. La sous-chaîne démarre à la position de caractère startingIndex (base zéro) et continue jusqu'à la fin des chaîne ou la longueur des caractères si spécifié.

**Exemples**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>ToLower

    tolower("HELLO") == "hello"

Convertit une chaîne en minuscules.

### <a name="toupper"></a>ToUpper

    toupper("hello") == "HELLO"

Convertit une chaîne en majuscules.



### <a name="guids"></a>GUID

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Tableaux, objets et dynamique

[littéraux](#dynamic-literals) | [casting](#casting-dynamic-objects) | [opérateurs](#operators) | [permettent de clauses](#dynamic-objects-in-let-clauses)
<br/>
[ArrayLength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [plage](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)


Voici le résultat d’une requête sur une exception d’idées d’Application. La valeur de `details` est un tableau.

![](./media/app-insights-analytics-reference/310.png)

**L’indexation :** Index des tableaux et les objets comme dans JavaScript :

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Mais utiliser `arraylength` et d’autres fonctions Analytique (pas « .length » !)

**Un cast** Dans certains cas, il est nécessaire de caster un élément que vous extrayez à partir d’un objet, car son type peut varier. Par exemple, `summarize...to` a besoin d’un type spécifique :

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Littéraux** Pour créer un tableau explicite ou l’objet du sac de propriétés, vous devez l’écrire en tant que chaîne au format JSON et cast :

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand :** Pour décomposer les propriétés d’un objet dans des lignes distinctes, utilisez mvexpand :

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath :** Pour rechercher tous les chemins d’accès dans un objet complexe :

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema :** Pour trouver le schéma minimal qui admet toutes les valeurs de l’expression de la table :

    exceptions | summarize buildschema(details)

Résultat :

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Notez que `indexer` est utilisé pour marquer où vous devez utiliser un index numérique. Pour ce schéma, certains chemins d’accès valides seraient être (en supposant que ces index d’exemple est dans une plage) :

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Littéraux de tableau et d’objet

Pour créer un littéral dynamique, utilisez `parsejson` (alias `todynamic`) avec un JSON argument de chaîne :

* `parsejson('[43, 21, 65]')`-un tableau de nombres
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-une valeur unique de type dynamique contenant un nombre
* `parsejson('"21"')`-une valeur unique de type dynamique contenant une chaîne

Notez que, contrairement à JavaScript, JSON rend obligatoire l’utilisation de guillemets doubles (`"`) autour de chaînes. Par conséquent, il est généralement plus facile de devis une chaîne codée en JSON littéraux en utilisant des guillemets simples (`'`).

Cet exemple crée une valeur dynamique, puis utilise ses champs :

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Fonctions de l’objet dynamique

|||
|---|---|
| *valeur* `in` *tableau*| True s’il existe un élément de *tableau* qui == *valeur*<br/>`where City in ('London', 'Paris', 'Rome')`
| *valeur* `!in` *tableau*| True si aucun élément du *tableau* qui == *valeur*
|[`arraylength(`tableau`)`](#arraylength)| Null si elle n’est pas un tableau
|[`extractjson(`chemin d’accès, objet`)`](#extractjson)|Utilise le chemin d’accès pour naviguer dans l’objet.
|[`parsejson(`source`)`](#parsejson)| Convertit une chaîne JSON en un objet dynamique.
|[`range(`à partir, à, l’étape`)`](#range)| Un tableau de valeurs
|[`mvexpand`listColumn](#mvexpand-operator) | Réplique une ligne pour chaque valeur dans une liste dans une cellule spécifiée.
|[`summarize buildschema(`colonne`)`](#buildschema) |Déduit le schéma du type de contenu de la colonne
|[`summarize makelist(`colonne`)`](#makelist)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau.
|[`summarize makeset(`colonne`)`](#makeset) | Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, sans duplication.

### <a name="dynamic-objects-in-let-clauses"></a>Objets dynamiques dans les clauses let


[Laisser les clauses](#let-clause) magasin dynamiques valeurs sous forme de chaînes, donc ces deux clauses sont équivalentes et doivent tous deux le `parsejson` (ou `todynamic`) avant d’être utilisés :

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>ArrayLength

Le nombre d’éléments dans un tableau dynamique.

**Syntaxe**

    arraylength(array)

**Arguments**

* *tableau :* A `dynamic` valeur.

**Propriété renvoie**

Le nombre d’éléments dans un *tableau*, ou `null` si le *tableau* n’est pas un tableau.

**Exemples**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtenez un élément spécifié un texte JSON à l’aide d’une expression de chemin d’accès. Vous pouvez également convertir la chaîne extraite à un type spécifique.


**Syntaxe**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Propriété renvoie**

Cette fonction exécute une requête de JsonPath dans la source de données qui contient une chaîne JSON valide, éventuellement convertir cette valeur en un autre type selon le troisième argument.



**Exemple**

La notation des [crochets] et la notation par points sont équivalentes :

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Optimiser les performances**

* Appliquer des clauses where avant d’utiliser`extractjson()`
* Envisager d’utiliser une correspondance d’expression régulière à [Extraire](#extract) . Il peut s’exécuter beaucoup plus rapidement et est efficace si le JSON est généré à partir d’un modèle.
* Utilisez `parsejson()` si vous avez besoin extraire le plus d’une valeur à partir du JSON.
* Envisager le JSON analysé à la réception en déclarant le type de la colonne d’être dynamique.

### <a name="json-path-expressions"></a>Expressions de chemin d’accès de JSON

|||
|---|---|
|`$`|Objet racine|
|`@`|Objet en cours|
|`[0]`|Indice de tableau|
|`.`ou`[0]` | Enfant|

*(Nous n’actuellement mettre en œuvre des caractères génériques, la récursivité, union ou tranches.)*




### <a name="parsejson"></a>parseJSON

Interprète un `string` sous la forme d’une [valeur JSON](http://json.org/)) et renvoie la valeur en tant que `dynamic`. Il est supérieur à l’utilisation de `extractjson()` lorsque vous devez extraire le plus d’un élément d’un objet composé de JSON.

**Syntaxe**

    parsejson(json)

**Arguments**

* *json :* Un document JSON.

**Propriété renvoie**

Un objet de type `dynamic` spécifié par *json*.

**Exemple**

Dans l’exemple suivant, lors de la `context_custom_metrics` est un `string` qui ressemble à ceci : 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

le fragment suivant récupère la valeur de la `duration` emplacement dans l’objet et à partir de qu’il récupère deux emplacements, `duration.value` et  `duration.min` (`118.0` et `110.0`, respectivement).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>plage

Le `range()` fonction (à ne pas confondre avec le `range` opérateur) génère un tableau dynamique contenant une série de valeurs d’espacés de façon égale.

**Syntaxe**

    range(start, stop, step)

**Arguments**

* *Démarrer :* La valeur du premier élément dans le tableau résultant. 
* *Arrêter :* La valeur du dernier élément dans le tableau résultant, ou la valeur minimale supérieure à celle du dernier élément dans le tableau résultant et dans un nombre entier multiple de l' *étape* de *démarrage*.
* *étape :* La différence entre deux éléments consécutifs du tableau.

**Exemples**

L’exemple suivant renvoie `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

L’exemple suivant renvoie un tableau contenant tous les jours de l’année 2015 :

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Convertit une chaîne en une valeur dynamique.

### <a name="treepath"></a>TreePath

    treepath(dynamic_object)

Énumère toutes les expressions de chemin d’accès qui identifient des feuilles dans un objet dynamique. 

**Propriété renvoie**

Un tableau d’expressions de chemin d’accès.

**Exemples**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Notez que « [0] » indique la présence d’un tableau, mais ne spécifie pas l’index utilisé par un chemin d’accès spécifique.

### <a name="zip"></a>ZIP

    zip(list1, list2, ...)

Regroupe un ensemble de listes dans une liste de tuples.

* `list1...`: Une liste de valeurs

**Exemples**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>Noms

Les noms peuvent contenir jusqu'à 1 024 caractères. Ils sont sensibles à la casse et peut contenir des lettres, des chiffres et des traits de soulignement (`_`). 

Un nom à l’aide de devis ['... '] or [" ... «] pour inclure d’autres caractères ou utilisez un mot clé en tant que nom. Par exemple :

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|[' chemin d’accès\\fichier. utl\n\'x\''] | Utilisez \ d’échappement de caractères|
|[« d-e.=/f#\n »] | |
|[@'path\file'] | Aucun échappement - \ est littéral|
|[@"\now& puis\"] | |
|[emplacement] | À l’aide d’un mot clé de langage en tant que nom|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


