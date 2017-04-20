## <a name="fileshare-dataset-type-properties"></a>Propriétés de type de groupe de données de partage de fichiers

Pour obtenir une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, consultez l’article de la [Création de groupes de données](../articles/data-factory/data-factory-create-datasets.md) . Sections de structure, de disponibilité et de stratégie d’un groupe de données JSON sont similaires pour tous les types de groupe de données. 

La section **typeProperties** est différente pour chaque type de groupe de données. Il fournit des informations spécifiques au type de groupe de données. La section typeProperties pour un groupe de données de type jeu de données de **partage de fichiers** possède les propriétés suivantes :

Propriété | Description | Obligatoire
-------- | ----------- | --------
folderPath | Sub chemin d’accès du dossier. Utilisez le caractère d’échappement ' \ ' pour les caractères spéciaux dans la chaîne. Pour obtenir des exemples, consultez [l’exemple lié les définitions de service et le groupe de données](#sample-linked-service-and-dataset-definitions) .<br/><br/>Vous pouvez combiner cette propriété avec **partitionBy** pour que les chemins d’accès de dossier en fonction de la tranche de dates-heures de début et de fin. | Oui
nom de fichier | Spécifiez le nom du fichier dans le **folderPath** si vous souhaitez que la table pour faire référence à un fichier spécifique dans le dossier. Si vous ne spécifiez pas la valeur de cette propriété, le tableau pointe vers tous les fichiers dans le dossier.<br/><br/>Lorsque le nom de fichier n’est pas spécifié pour la sortie d’un groupe de données, le nom du fichier généré serait le suivant ce format : <br/><br/>Données. <Guid>.txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | N°
partitionedBy | partitionedBy peut servir à spécifier une dynamique folderPath, nom de fichier de données de série de temps. Par exemple, folderPath paramétrée pour chaque heure de données. | N°
Format | Les types de format suivants sont pris en charge : **TextFormat**, **AvroFormat**, **JsonFormat**et **OrcFormat**. Définissez **la propriété sous un format** à l’une de ces valeurs. Consultez les sections [Spécifiant TextFormat](#specifying-textformat), [Spécifiant les AvroFormat](#specifying-avroformat), [JsonFormat de spécifier](#specifying-jsonformat)et [Spécification de OrcFormat](#specifying-orcformat) pour plus de détails. Si vous souhaitez copier les fichiers en tant que-est entre bases orientées fichier (copie binaire), vous pouvez ignorer la section de format dans les deux définitions de jeu de données d’entrée et de sortie. | N°
fileFilter | Spécifier un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers.<br/><br/>Valeurs autorisées sont : `*` (plusieurs caractères) et `?` (un seul caractère).<br/><br/>Exemple 1 :`"fileFilter": "*.log"`<br/>Exemple 2 :`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter s’applique à un groupe de données de partage de fichiers d’entrée. Cette propriété n’est pas pris en charge avec très.  | N°
| compression | Permet de spécifier le type et le niveau de compression pour les données. Types pris en charge sont : **GZip**et **Deflate**et **BZip2** niveaux pris en charge sont : **optimale** et la **plus rapide**. Actuellement, les paramètres de compression ne sont pas pris en charge pour les données de **AvroFormat** ou de **OrcFormat**. Pour plus d’informations, reportez-vous à la section [prise en charge de la Compression](#compression-support) .  | N° |
| useBinaryTransfer | Spécifier si utiliser le mode de transfert binaire. Valeur True pour le mode binaire et ASCII false. Valeur par défaut : True. Cette propriété peut uniquement être utilisée lorsque le type de service lié associé est du type : Serveur_ftp. | N° | 
 

> [AZURE.NOTE] nom de fichier et fileFilter ne peuvent pas être utilisés simultanément.

### <a name="using-partionedby-property"></a>À l’aide de la propriété de partionedBy

Comme mentionné dans la section précédente, vous pouvez spécifier une dynamique folderPath, nom de fichier de données de la série temps avec partitionedBy. Vous pouvez le faire avec les macros de données usine et la variable système SliceStart, SliceEnd d’indiquer la période logique pour une tranche de données donné. 

Pour obtenir des informations sur la série de temps des groupes de données, de planification et de tranches, consultez [Création des groupes de données](../articles/data-factory/data-factory-create-datasets.md), [de planification et l’exécution](../articles/data-factory/data-factory-scheduling-and-execution.md)et des articles de la [Création des tuyaux](../articles/data-factory/data-factory-create-pipelines.md) . 

#### <a name="sample-1"></a>Exemple 1 :

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Dans cet exemple {section} est remplacée par la valeur de la variable de système SliceStart Factory de données dans le format (YYYYMMDDHH) spécifiée. La SliceStart fait référence pour l’heure de début de la tranche. Le folderPath est différent pour chaque section. Exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemple 2 :

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Dans cet exemple, l’année, mois, jour et heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés CheminDossier et nom de fichier.
