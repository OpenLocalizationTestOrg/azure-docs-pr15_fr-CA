## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Spécification de définition de la structure des groupes de données rectangulaire
La section de la structure dans les jeux de données JSON est une section **facultative** pour les tableaux rectangulaires (avec des lignes et des colonnes) et constituée une collection de colonnes de la table. Vous allez utiliser la section structure soit des informations de type pour les conversions ou effectuant des mappages de colonnes. Les sections suivantes décrivent ces fonctionnalités en détail. 

Chaque colonne contient les propriétés suivantes :

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| nom | Nom de la colonne. | Oui |
| type de | Type de données de la colonne. Consultez la section des conversions de type ci-dessous pour plus d’informations sur quand devez-vous spécifier des informations de type | N° |
| culture | .NET en fonction de la culture à utiliser lorsque le type est spécifié et qu’il est du type .NET Datetime ou Datetimeoffset. Valeur par défaut est « en-us ».  | N° |
| format | Format de chaîne à utiliser lorsque le type est spécifié et qu’il est du type .NET Datetime ou Datetimeoffset. | N° |

L’exemple suivant montre la section structure JSON pour une table qui comporte trois colonnes ID d’utilisateur, nom et lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Utilisez les instructions suivantes pour le moment inclure des informations de « structure » et les éléments à inclure dans la section **structure** .

- **Pour les sources de données structurées** qui stockent des informations de schéma et de type de données ainsi que les données elles-mêmes (sources comme table Azure de SQL Server, Oracle, etc.), vous devez spécifier la section « structure » uniquement si vous voulez effectuer le mappage de colonne de colonnes source spécifique à des colonnes spécifiques dans un récepteur, et leurs noms ne sont pas identiques (voir les détails dans la section de mappage de colonne ci-dessous). 

    Comme mentionné ci-dessus, les informations de type sont facultatives dans la section « structure ». Structurées sources, les informations de type sont déjà disponibles dans le cadre de la définition de groupe de données dans le magasin de données, par conséquent, ne pas inclure les informations de type lorsque vous n’incluez pas la section « structure ».
- **Schéma sur les sources de données de lecture (en particulier les blob Azure)** vous pouvez choisir de stocker des données sans stocker les informations de type ou de schéma avec les données. Pour ces types de sources de données, vous devez inclure « structure » dans les 2 cas suivants :
    - Vous souhaitez effectuer le mappage de colonnes.
    - Lorsque le groupe de données est une source d’une activité de copie, vous pouvez fournir des informations de type dans la « structure » et usine de données utilisera ces informations de type pour la conversion vers des types natifs pour le récepteur. Article de [déplacer des données vers et depuis les Blob Azure](../articles/data-factory/data-factory-azure-blob-connector.md) voir pour plus d’informations.

### <a name="supported-net-based-types"></a>Prise en charge. Types basés sur le NET 
Usine de données prend en charge la CLS compatible avec .NET type valeurs suivantes pour fournir des informations de type dans la « structure » de schéma sur les sources de données lues comme blob Azure.

- Int16
- Int32 
- Int64
- Unique
- Double
- Décimal
- Byte]
- Bool
- Chaîne 
- GUID
- DateTime
- DateTimeOffset
- TimeSpan 

Datetime et Datetimeoffset, vous pouvez également spécifier « culture » et « format » chaîne afin de faciliter l’analyse de votre chaîne Datetime personnalisé. Consultez l’exemple pour la conversion de type ci-dessous.

