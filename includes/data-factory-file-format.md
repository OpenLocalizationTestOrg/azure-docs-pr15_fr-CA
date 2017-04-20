## <a name="specifying-formats"></a>Spécification des formats

Usine de données Azure prend en charge les types de format suivants : 

- [Format de texte](#specifying-textformat)
- [Format JSON](#specifying-jsonformat)
- [Format de Avro](#specifying-avroformat)
- [Format des ORC](#specifying-orcformat)
- [Format de parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Spécifier le format du texte

Si le format est défini pour le **format du texte**, vous pouvez spécifier les propriétés **optionnelles** suivantes dans la section de **Format** .

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | Le caractère utilisé pour séparer des colonnes dans un fichier. | Caractère qu’un seul est autorisé. La valeur par défaut est la virgule (','). <br/><br/>Pour utiliser un caractère Unicode, reportez-vous à [Caractères Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) pour obtenir le code correspondant pour lui. Par exemple, vous pouvez envisager d’utiliser un caractère non imprimable rare qui n’existe pas probablement dans vos données : spécifiez « \u0001 » qui représente le début du titre (SOH). | N° |
| rowDelimiter | Le caractère utilisé pour séparer les lignes dans un fichier. | Caractère qu’un seul est autorisé. La valeur par défaut est une des valeurs suivantes à la lecture : [« \r\n », « \r », « \n »] et « \r\n » lors de l’écriture. | N° |
| Dont | Le caractère spécial utilisé pour échapper à un délimiteur de colonne dans le contenu du fichier d’entrée. <br/><br/>Vous ne pouvez pas spécifier dont et quoteChar pour une table. | Caractère qu’un seul est autorisé. Aucune valeur par défaut. <br/><br/>Exemple : Si vous disposez d’une virgule («, ») comme délimiteur de colonne, mais vous souhaitez que la virgule dans le texte (exemple : « Hello, world »), vous pouvez définir le caractère d’échappement '$' et utilisez la chaîne « $ de Hello, world » dans la source. | N° | 
| propriété quoteChar | Le caractère utilisé pour le devis d’une valeur de chaîne. Les séparateurs de colonne et de ligne à l’intérieur des guillemets seraient traités comme une partie de la valeur de chaîne. Cette propriété s’applique aux groupes de données d’entrée et de sortie.<br/><br/>Vous ne pouvez pas spécifier dont et quoteChar pour une table. | Caractère qu’un seul est autorisé. Aucune valeur par défaut. <br/><br/>Par exemple, si vous disposez d’une virgule (', ') comme délimiteur de colonne, mais vous souhaitez que la virgule dans le texte (exemple : < Hello, world >), vous pouvez définir "(guillemets doubles) comme le caractère guillemet et l’utilisation de la chaîne « Hello, world » dans la source. | N° |
| nullValue | Un ou plusieurs caractères utilisés pour représenter une valeur null. | Un ou plusieurs caractères. Les valeurs par défaut sont « \N » et « NULL » sur la lecture et « \N » lors de l’écriture. | N° |
| encodingName | Spécifiez le nom de codage. | Un nom de codage valide. consultez [Les propriétés Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemple : windows-1250 ou shift_jis. La valeur par défaut est UTF-8. | N° | 
| firstRowAsHeader | Spécifie s’il faut considérer la première ligne comme en-tête. Pour un groupe de données d’entrée, Data Factory lit la première ligne comme en-tête. Pour la sortie d’un groupe de données, Data Factory écrit la première ligne sous la forme d’un en-tête. <br/><br/>Pour des exemples de scénarios, consultez [scénarios d’utilisation de **firstRowAsHeader** et **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) . | True<br/>Faux (par défaut) | N° |
| skipLineCount | Indique le nombre de lignes à ignorer lors de la lecture des données à partir des fichiers d’entrée. Si à la fois skipLineCount et firstRowAsHeader sont spécifiés, les lignes sont ignorés tout d’abord, et ensuite les informations d’en-tête sont lue à partir du fichier d’entrée. <br/><br/>Pour des exemples de scénarios, consultez [scénarios d’utilisation de firstRowAsHeader et skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) . | Nombre entier | N° | 
| treatEmptyAsNull | Spécifie s’il faut traiter une chaîne null ou vide comme une valeur null lors de la lecture des données à partir d’un fichier d’entrée. | True (par défaut)<br/>False | N° |  

#### <a name="textformat-example"></a>Exemple de format du texte
L’exemple suivant affiche certaines des propriétés de format de TextFormat.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

Pour utiliser dont place quoteChar, remplacez la ligne quoteChar avec le dont suivante :

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scénarios d’utilisation de firstRowAsHeader et skipLineCount

- Vous copiez à partir d’une source non-fichier dans un fichier texte et que vous souhaitez ajouter une ligne d’en-tête qui contient les métadonnées de schéma (par exemple : schéma SQL). Spécifiez **firstRowAsHeader** comme true dans le dataset de sortie pour ce scénario. 
- Vous copiez à partir d’un fichier texte contenant une ligne d’en-tête pour un récepteur de fichier non et que vous souhaitez supprimer cette ligne. Spécifiez **firstRowAsHeader** comme true dans le groupe de données d’entrée.
- Vous copiez à partir d’un fichier texte et que vous souhaitez ignorer les quelques lignes qui ne contiennent aucune information d’en-tête ou de données au début. Spécifiez **skipLineCount** pour indiquer le nombre de lignes à ignorer. Si le reste du fichier contient une ligne d’en-tête, vous pouvez également spécifier **firstRowAsHeader**. Si à la fois **skipLineCount** et **firstRowAsHeader** sont spécifiés, les lignes sont ignorés tout d’abord, et ensuite les informations d’en-tête sont lue à partir du fichier d’entrée

### <a name="specifying-avroformat"></a>Spécification de AvroFormat
Si le format est défini sur AvroFormat, vous n’avez pas besoin spécifier les propriétés de la section de Format dans la section typeProperties. Exemple :

    "format":
    {
        "type": "AvroFormat",
    }

Pour utiliser le format de Avro dans une table de la ruche, vous pouvez consulter [de la ruche Apache le didacticiel](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Notez les points suivants :  

- [Types de données complexes](http://avro.apache.org/docs/current/spec.html#schema_complex) ne sont pas pris en charge (enregistrements, des enums, des tableaux, des cartes, des unions et fixe)

### <a name="specifying-jsonformat"></a>Spécification de JsonFormat

Si le format est défini sur **JsonFormat**, vous pouvez spécifier les propriétés **optionnelles** suivantes dans la section de **Format** .

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| filePattern | Indiquer le modèle de données enregistrés dans chaque fichier JSON. Valeurs autorisées sont : **setOfObjects** et **arrayOfObjects**. La valeur **par défaut** est : **setOfObjects**. Voir suivant les sections pour plus d’informations sur ces modèles.| N° |
| encodingName | Spécifiez le nom de codage. Pour une liste de noms de codage valides, consultez : [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) propriété. Par exemple : windows-1250 ou shift_jis. La valeur **par défaut** : **UTF-8**. | N° | 
| nestingSeparator | Caractère utilisé pour séparer les niveaux d’imbrication. La valeur par défaut est '.' (point). | N° | 


#### <a name="setofobjects-file-pattern"></a>modèle de setOfObjects de fichier

Chaque fichier contient le seul objet ou concaténées délimitée par des ligne de plusieurs objets. Lorsque cette option est choisie dans un dataset de sortie, activité de copie de produit un seul fichier JSON avec chaque objet par ligne (ligne séparées par des virgules).

**objet unique** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**ligne délimitée de JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concaténé**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>modèle de fichier arrayOfObjects. 

Chaque fichier contient un tableau d’objets. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Exemple de JsonFormat

Si vous avez un fichier JSON avec le contenu suivant :  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

et vous voulez la copier dans une table SQL d’Azure dans le format suivant : 

ID  | Name.First | Name.Middle | Name.Last | Balises
--- | ---------- | ----------- | --------- | ----
1 | John | valeur null | Doe | [« Données » d’usine", « Azure »]

Le groupe de données d’entrée avec type de JsonFormat est défini comme suit : (définition partielle avec uniquement les parties pertinentes)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Si la structure n’est pas définie, l’activité de copie aplatit la structure par défaut et copiez chaque chose. 

#### <a name="supported-json-structure"></a>Structure JSON prises en charge
Notez les points suivants : 

- Chaque objet d’une collection de paires nom/valeur est mappé à une seule ligne de données dans un format tabulaire. Les objets peuvent être imbriqués et vous pouvez définir comment aplatir la structure d’un groupe de données avec le séparateur d’imbrication (.) par défaut. Consultez l' [exemple de JsonFormat](#jsonformat-example) précédant la section pour obtenir un exemple.  
- Si la structure n’est pas définie dans le dataset de données usine, l’activité de copie détecte le schéma à partir de l’objet premier et aplatir l’objet entier. 
- Si l’entrée JSON a un tableau, l’activité de copie convertit la valeur de la baie tout entière dans une chaîne. Vous pouvez choisir Ignorer en utilisant le [mappage de colonnes ou de filtrage](#column-mapping-with-translator-rules).
- S’il existe des noms en double au même niveau, l’activité de copie sélectionne la dernière.
- Les noms des propriétés respectent la casse. Deux propriétés de même nom mais des boyaux différents sont considérées comme deux propriétés distinctes. 

### <a name="specifying-orcformat"></a>Spécification de OrcFormat
Si le format est défini sur OrcFormat, vous n’avez pas besoin spécifier les propriétés de la section de Format dans la section typeProperties. Exemple :

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Si vous ne copiez pas les fichiers des ORC **comme-est** entre les locaux et cloud magasins de données, vous devez installer le 8 JRE (Java Runtime Environment) sur votre ordinateur de la passerelle. Une passerelle 64 bits requiert JRE de 64 bits et 32 bits passerelle JRE de 32 bits. Vous pouvez trouver les deux versions à partir [d’ici](http://go.microsoft.com/fwlink/?LinkId=808605). Choisissez celui qui convient.

Notez les points suivants :

-   Les données complexes types ne sont pas pris en charge (STRUCT, carte, liste, UNION)
-   Fichier des ORC a trois [options relatives à la compression](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usine de données prend en charge la lecture des données à partir du fichier ORC dans un de ces formats compressés. Il utilise la compression codec est dans les métadonnées pour lire les données. Toutefois, lors de l’écriture dans un fichier ORC, Data Factory choisit ZLIB, qui est la valeur par défaut pour les ORC. Actuellement, il n’existe aucune option pour substituer ce comportement. 

### <a name="specifying-parquetformat"></a>Spécification de ParquetFormat
Si le format est défini sur ParquetFormat, vous n’avez pas besoin spécifier les propriétés de la section de Format dans la section typeProperties. Exemple :

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Si vous ne copiez pas les fichiers de Parquet **comme-est** entre les locaux et cloud magasins de données, vous devez installer le 8 JRE (Java Runtime Environment) sur votre ordinateur de la passerelle. Une passerelle 64 bits requiert JRE de 64 bits et 32 bits passerelle JRE de 32 bits. Vous pouvez trouver les deux versions à partir [d’ici](http://go.microsoft.com/fwlink/?LinkId=808605). Choisissez celui qui convient.

Notez les points suivants :

-   Les données complexes types ne sont pas pris en charge (liste, liste)
-   Fichier de parquet possède les options de compression suivantes : aucun, LZO, SNAPPY et GZIP. Usine de données prend en charge la lecture des données à partir du fichier ORC dans un de ces formats compressés. Il utilise le codec de compression dans les métadonnées pour lire les données. Toutefois, lors de l’écriture dans un fichier de Parquet, usine de données choisit SNAPPY, qui est la valeur par défaut pour le format de Parquet. Actuellement, il n’existe aucune option pour substituer ce comportement. 
