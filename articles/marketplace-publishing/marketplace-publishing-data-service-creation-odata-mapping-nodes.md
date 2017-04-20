<properties
   pageTitle="Guide de création d’un Service de données pour le marché | Microsoft Azure"
   description="De la création, de certifier et de déployer un Service de données pour obtenir des instructions détaillées d’achat sur le marché d’Azure."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Présentation du schéma de nœuds pour le mappage d’un service web existant pour OData dans CSDL

>[AZURE.IMPORTANT] **Pour l’instant nous ne sont plus arrivant des nouvelles publications de Service de données. Nouvelle dataservices ne sera pas obtenir approuvée pour la liste.** Si vous disposez d’une application d’entreprise SaaS vous souhaitez publier sur AppSource, vous pouvez trouver plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous possédez une applications IaaS ou service de développeur vous souhaitez publier sur Azure Marketplace, vous trouverez plus d’informations [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Ce document vous aide à clarifier la structure de nœud pour le mappage d’un protocole OData à CSDL. Il est important de noter que la structure de nœud est du code XML. Schéma de racine, parent et enfant est donc applicable lors de la conception de votre mappage OData.

## <a name="ignored-elements"></a>Éléments ignorés
Voici les haut niveau CSDL éléments (nœuds XML) qui ne vont pas être utilisé par le serveur principal d’Azure Marketplace lors de l’importation de métadonnées du service web. Ils peuvent être présents, mais seront ignorées.

| Élément | Champ d’application |
|----|----|
| À l’aide d’élément | Le nœud, sous-noeuds et tous les attributs |
| Documentation, élément | Le nœud, sous-noeuds et tous les attributs |
| ComplexType | Le nœud, sous-noeuds et tous les attributs |
| Association, élément | Le nœud, sous-noeuds et tous les attributs |
| Propriété étendue | Le nœud, sous-noeuds et tous les attributs |
| EntityContainer | Seuls les attributs suivants sont ignorés : *étend* et *AssociationSet* |
| Schéma | Seuls les attributs suivants sont ignorés : *Namespace* |
| FunctionImport | Seuls les attributs suivants sont ignorés : *Mode* (valeur par défaut de ln est utilisée) |
| EntityType | Uniquement les nœuds sub suivantes sont ignorées : *clé* et *PropertyRef* |

La section suivante décrit les modifications (ajout et ignoré des éléments) à différents nœuds CSDL XML en détail.

## <a name="functionimport-node"></a>FunctionImport nœud
Un nœud FunctionImport représente une URL (point d’entrée) qui expose un service à l’utilisateur final. Le nœud permet décrivant la manière dont l’URL est adressée, quels paramètres sont disponibles pour l’utilisateur final et la façon dont ces paramètres sont fournis.

Plus d’informations sur ce nœud sont trouvés [ici] [MSDNFunctionImportLink]

[MSDNFunctionImportLink] : (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

Les éléments suivants sont des attributs supplémentaires (les ajouts aux attributs) qui sont exposées par le nœud FunctionImport :

**d:baseUri** -
modèle URI de la ressource reste exposé au marché. Marketplace utilise ce modèle pour construire des requêtes sur le service web REST. Le modèle de l’URI contient des espaces réservés pour les paramètres sous la forme {nom_paramètre}, où nom_paramètre est le nom du paramètre. Exemple : apiVersion = {apiVersion}.
Les paramètres sont autorisés s’affiche en tant que paramètres de l’URI ou comme faisant partie du chemin d’accès de l’URI. En cas d’apparition dans le chemin d’accès, ils sont toujours obligatoires (ne peut pas être marqué comme nullable). *Exemple :*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nom** : le nom de la fonction importée.  Ne peut pas être le même que les autres noms définis dans le langage CSDL.  Exemple : Nom = « GetModelUsageFile »

**EntitySet** *(facultatif)* - si la fonction retourne une collection de types d’entité, la valeur de l' **EntitySet** doit être le jeu auquel appartient la collection d’entités. Dans le cas contraire, l’attribut **EntitySet** ne doit pas être utilisé. *Exemple :*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(Facultatif)* : Spécifie le type des éléments retournés par l’URI.  N’utilisez pas cet attribut si la fonction ne retourne pas une valeur. Les types pris en charge sont les suivants :

 - **Collection (<Entity type name>)**: Spécifie une collection de types d’entité définis. Le nom est présent dans l’attribut de nom du nœud EntityType. Par exemple, Collection (WXC. HourlyResult).
 - **Premières (<mime type>)**: Spécifie un document/blob brut qui est renvoyé à l’utilisateur. Un exemple est Raw(image/jpeg) d’autres exemples :

  - ReturnType="Raw(text/plain) »
  - ReturnType = « Collection (sage. DeleteAllUsageFilesEntity) » *

**d:Paging** - spécifie la façon dont la pagination est gérée par la ressource reste. Les valeurs des paramètres sont utilisés dans des branches, par exemple, la page = {$page} & itemsperpage = {$size} les options disponibles sont :

- **Aucun :** aucune pagination n’est disponible
- **Ignorer :** pagination est exprimée par une logique « ignorer » et de « prélèvement » (en haut). Ignorer saute les éléments M et take puis retourne les éléments N. Valeur de paramètre : $skip
- **Prendre :** Take renvoie les N éléments suivant. Valeur de paramètre : $take
- **PageSize :** pagination est exprimée via une page logique et la taille (éléments par page). Page représente la page en cours est retournée. Valeur de paramètre : $page
- **Taille :** taille représente le nombre d’éléments renvoyés pour chaque page. Valeur de paramètre : $size

**d:AllowedHttpMethods** *(Facultatif)* : Spécifie le verbe est géré par la ressource reste. En outre, limite verbe accepté à la valeur spécifiée.  Par défaut = POST.  *Exemple :* `d:AllowedHttpMethods="GET"` Les options disponibles sont :

- **Obtenir :** habituellement utilisée pour renvoyer les données
- **POST :** généralement utilisée pour insérer de nouvelles données
- **Mettre :** généralement utilisé pour mettre à jour des données
- **Supprimer :** permet de supprimer des données

Les nœuds enfants supplémentaires (non couverts par la documentation CSDL) dans le nœud FunctionImport sont les suivantes :

**d:RequestBody** *(Facultatif)* - le corps de la demande est utilisée pour indiquer que la requête attend un corps à envoyer. Paramètres peuvent être données dans le corps de la demande. Elles sont exprimées au sein d’accolades, par exemple, {nom_paramètre}. Ces paramètres sont mappés à partir de l’entrée d’utilisateur dans l’organisme qui est transféré vers les services du fournisseur de contenu. L’élément requestBody possède un attribut avec le nom httpMethod. L’attribut autorise deux valeurs :

- **POST :** Utilisé si la demande est une publication HTTP
- **Obtenir :** Utilisé si la demande est un verbe HTTP GET

    Exemple :

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** et **d:Namespace** - ce nœud décrit les espaces de noms qui sont définis dans le fichier XML qui est retourné par le function import (point de terminaison URI). Le code XML qui est retourné par le service de serveur principal peut contenir un nombre quelconque d’espaces de noms afin de différencier le contenu qui est retourné. **Tous ces espaces de noms, si utilisé dans des requêtes XPath d:Map ou d:Match doivent être répertoriés.** Le nœud d:Namespaces contient une jeu/liste de nœuds de d:Namespace. Chacun d’eux indique un espace de noms utilisé dans la réponse du service back-end. L’attribut du nœud d:Namespace sont les suivantes :

-   **d:Prefix :** Le préfixe de l’espace de noms, comme dans les résultats XML retournés par le service, par exemple, f:FirstName, f:LastName, où f est le préfixe.
- **d:Uri :** URI complet de l’espace de noms utilisé dans le document de résultat. Il représente la valeur que le préfixe est résolu à lors de l’exécution.

**d:ErrorHandling** *(Facultatif)* : ce nœud contient les conditions de gestion des erreurs. Chacune des conditions est validé par rapport à ce qui est retourné par les services du fournisseur de contenu. Si une condition correspond à du code d’erreur HTTP proposé qu'un message d’erreur est renvoyé à l’utilisateur final.

**d:ErrorHandling** *(Facultatif)* et **d:Condition** *(facultatif)* - le nœud de condition contient une condition qui est vérifiée dans le résultat retourné par service du fournisseur. Les attributs **requis** sont les suivants :

- **d:Match :** Une expression XPath qui vérifie si une nœud donnée/valeur est présente dans le fichier de sortie du fournisseur de contenu XML. L’expression XPath est exécuté sur la sortie et doit retourner la valeur true si la condition est une correspondance ou la valeur false dans le cas contraire.
- **d:HttpStatusCode :** Le code d’état HTTP qui doit être retourné par le marché dans le cas de la condition correspond à. Marché signalizes des erreurs à l’utilisateur par le biais de codes d’état HTTP. Une liste des codes d’état HTTP est disponible à l’adresse http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage :** Le message d’erreur qui est renvoyé – avec le code d’état HTTP – permet à l’utilisateur final. Il s’agit d’un message d’erreur convivial qui ne contiennent aucun secret.

**d:Title** *(Facultatif)* - permet de décrire le titre de la fonction. Provenance de la valeur pour le titre

- L’attribut de mappage facultative (xpath) qui spécifie où trouver le titre dans la réponse renvoyée à partir de la demande de service.
- - Ou - le titre spécifié en tant que valeur du nœud.

**d:Rights** *(Facultatif)* - les droits (par exemple, copyright) associés à la fonction. La valeur des droits proviennent :

- L’attribut de mappage facultative (xpath) qui indique où trouver les droits dans la réponse renvoyée à partir de la demande de service.
-   - Ou - les droits spécifiés en tant que valeur du nœud.

**d:description** *(Facultatif)* - une brève description de la fonction. La valeur de la description est en provenance de

- L’attribut de mappage facultative (xpath) qui indique où trouver la description de la réponse retournée à partir de la demande de service.
- - Ou – la description spécifiée en tant que valeur du nœud.

**d:EmitSelfLink** - *Voir exemple ci-dessus « FunctionImport pour 'Paginer' a renvoyé des données »*

**d:EncodeParameterValue** - extension facultative à OData

**d:QueryResourceCost** - extension facultative à OData

**d:Map** - extension facultative à OData

**d:Headers** - extension facultative à OData

**d:Headers** - extension facultative à OData

**d:Value** - extension facultative à OData

**d:HttpStatusCode** - extension facultative à OData

**d:ErrorMessage** - Extension facultative à OData

## <a name="parameter-node"></a>Nœud de paramètre

Ce nœud représente un paramètre, qui est exposé en tant que partie du modèle URI ou de demander l’organisme qui a été spécifié dans le nœud FunctionImport.

Page d’un document d’informations très utiles sur le nœud « Élément de paramètre » se trouve à [ici](http://msdn.microsoft.com/library/ee473431.aspx) (utilisez la liste déroulante **Version autre** pour sélectionner une autre version si nécessaire pour afficher la documentation). *Exemple :*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Attribut de paramètre | Est requis | Valeur |
|----|----|----|
| Nom | Oui | Le nom du paramètre. Respect de la casse !  Respecter la BaseUri. **Exemple :**`<Property Name="IsDormant" Type="Byte" />` |
| Type de | Oui | Le type de paramètre. La valeur doit être un **EDMSimpleType** ou un type complexe qui est dans la portée du modèle. Pour plus d’informations, consultez « 6 types de propriété/paramètre pris en charge ».  (Sensible à la casse ! Premier caractère est en majuscule, reste sont en minuscules).  Voir aussi, [modèle Types CSDL (Conceptual)] [MSDNParameterLink]. **Exemple :**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode | N° | **In**, Out ou InOut selon que le paramètre est une entrée, une sortie ou un paramètre d’entrée/sortie. (Le seul « IN » est disponible dans Azure Marketplace.) **Exemple :**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | N° | La longueur maximale autorisée pour le paramètre. **Exemple :**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Précision | N° | La précision du paramètre. **Exemple :**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Échelle | N° | L’échelle du paramètre. **Exemple :**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink] : (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

Les attributs qui ont été ajoutées à la spécification CSDL sont les suivantes :

| Attribut de paramètre | Description |
|----|----|
| **d:Regex** *(Facultatif)* | Une instruction d’expression régulière utilisée pour valider la valeur d’entrée pour le paramètre. Si la valeur d’entrée ne correspond pas à l’instruction, la valeur est rejetée. Cela permet de spécifier également un ensemble de valeurs possibles, par exemple, ^ [0-9] + ? $ pour autoriser uniquement des nombres. **Exemple :** ' < nom de paramètre = « nom » Mode = « In » Type = « Chaîne » d : Nullable = « false » d:Regex = "^ [a-zA-Z] * $» d:Description = « un nom qui ne peut pas contenir d’espaces ou de la non-alpha des caractères non anglais » d:SampleValues = « Georges|John|Thomas|James "/ > » |
| **d:Enum** *(Facultatif)* | Liste des valeurs valides pour le paramètre, séparés par un canal. Le type des valeurs doit correspondre à du paramètre par le type défini. Exemple : « anglais|métrique|brut`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< nom de paramètre = « Durée » Type = « String » Mode = « In » Nullable = « true » d:Enum = « 1 an|5years|10years "/ > » |
| **d : Nullable** *(Facultatif)* | Permet de définir si un paramètre peut être null. La valeur par défaut : true. Toutefois, les paramètres qui sont exposés dans le chemin d’accès dans le modèle de l’URI ne peut pas être null. Lorsque l’attribut est défini sur false pour ces paramètres – l’entrée d’utilisateur est substituée. **Exemple :**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Facultatif)* | Un exemple de valeur pour l’afficher sous la forme d’une note pour le Client dans l’interface utilisateur.  Il est possible d’ajouter plusieurs valeurs à l’aide d’une liste séparée par des canaux, c'est-à-dire ' un|b|c` **Example:** `< nom de paramètre = « BikeOwner » Type = « String » Mode = « In » d:SampleValues = « George|John|Thomas|James "/ > » |

## <a name="entitytype-node"></a>Nœud de l’EntityType

Ce nœud représente un des types retournés par le marché à l’utilisateur final. Il contient également le mappage de la sortie qui est retournée par les services du fournisseur de contenu pour les valeurs qui sont retournées à l’utilisateur final.

Plus d’informations sur ce nœud sont trouvés à [ici](http://msdn.microsoft.com/library/bb399206.aspx) (utilisez la liste déroulante **Version autre** pour sélectionner une autre version si nécessaire pour afficher la documentation).

| Nom de l’attribut | Est requis | Valeur |
|----|----|----|
| Nom | Oui | Le nom du type d’entité. **Exemple :**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | N° | Le nom d’un autre type d’entité qui est le type de base du type d’entité qui est défini. **Exemple :**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Les attributs qui ont été ajoutées à la spécification CSDL sont les suivantes :

**d:Map** - expression XPath exécutée sur la sortie du service. L’hypothèse est que la sortie du service contienne un ensemble d’éléments qui se répètent, comme un atome d’alimentation où il existe un ensemble de nœuds d’entrée qui se répètent. Chacun de ces extensible nœuds contient un enregistrement. L’expression XPath est alors indiqué au point au niveau du nœud extensible individuel dans les résultats de service du fournisseur de contenu qui contient les valeurs d’un enregistrement distinct. Exemple de sortie du service :

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

L’expression XPath doit être /foo/barre de car chaque de la barre de nœud est le nœud répété dans la sortie et contient le contenu qui est retourné à l’utilisateur final.

**Clé** - cet attribut est ignoré par le marché. RESTE en fonction des services web, en général n’exposent pas une clé primaire.


## <a name="property-node"></a>Nœud de propriété

Ce nœud contient une propriété de l’enregistrement.

Plus d’informations sur ce nœud sont trouvés au [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (utilisez le menu déroulant de **Version autres** pour sélectionner une autre version si nécessaire pour afficher la documentation). *Exemple :*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Obligatoire | Valeur |
|----|----|----|
| Nom | Oui | Le nom de la propriété. |
| Type de | Oui | Le type de la valeur de la propriété. Le type de valeur de propriété doit être un **EDMSimpleType** ou un type complexe (indiqué par un nom qualifié complet) qui se trouve dans la portée du modèle. Pour plus d’informations, consultez Types de modèle conceptuel (CSDL). |
| Nullable | N° | **True** (la valeur par défaut) ou **False** selon si la propriété peut avoir une valeur null. Remarque : Dans la version du langage CSDL indiqué par l’espace de noms [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) , une propriété de type complexe doit être Nullable = « False ». |
| DefaultValue | N° | La valeur par défaut de la propriété. |
|MaxLength | N° | La longueur maximale de la valeur de la propriété. |
| FixedLength | N° | **Valeur True** ou **False** selon si la valeur de propriété va être stockée sous la forme d’une chaîne de longueur fiexed. |
| Précision | N° | Fait référence au nombre maximal de chiffres à conserver dans la valeur numérique. |
| Échelle | N° | Nombre maximal de décimales à conserver dans la valeur numérique. |
| Unicode | N° | **Valeur True** ou **False** selon si la valeur de propriété est stocké comme une chaîne Unicode. |
| Classement | N° | Chaîne qui spécifie la séquence de classement à utiliser dans la source de données. |
| ConcurrencyMode | N° | **Aucun** (la valeur par défaut) ou **fixe**. Si la valeur est définie à **fixe**, la valeur de la propriété servira dans les contrôles d’accès concurrentiel optimiste. |

Les attributs supplémentaires qui ont été ajoutées à la spécification CSDL sont les suivantes :

**d:Map** - expression XPath exécutée sur le service de sortie et extrait une propriété de sortie. L’objet XPath spécifié est par rapport au nœud extensible qui a été sélectionné dans XPath du nœud EntityType. Il est également possible de spécifier un XPath absolu pour permettre, y compris une ressource statique dans chacun des nœuds de sortie, comme par exemple une déclaration de copyright qui se trouve uniquement une fois dans le service d’origine de sortie doivent être présent dans chacune des lignes dans la sortie OData. Exemple du service :

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

L’expression XPath est ./bar/baz0 pour obtenir le nœud baz0 dans les services du fournisseur de contenu.

**d:CharMaxLength** - pour le type de chaîne, vous pouvez spécifier la longueur maximale. Consultez l’exemple DataService CSDL

**d:IsPrimaryKey** - indique si la colonne est la clé primaire de la table/vue. Consultez l’exemple DataService CSDL.

**d:isExposed** - détermine si le schéma de table est exposé (généralement true). Consultez l’exemple DataService CSDL

**d:IsView** *(Facultatif)* - true si elle est basée sur une vue plutôt qu’une table.  Consultez l’exemple DataService CSDL

**d:Tableschema** - voir l’exemple DataService CSDL

**d:ColumnName** - est le nom de la colonne dans la table/vue.  Consultez l’exemple DataService CSDL

**d:IsReturned** - est la valeur booléenne qui détermine si le Service expose cette valeur pour le client.  Consultez l’exemple DataService CSDL

**d:IsQueryable** - est la valeur booléenne qui détermine si la colonne peut être utilisée dans une requête de base de données.   Consultez l’exemple DataService CSDL

**d:OrdinalPosition** - est la position numérique de la colonne de l’apparence, x, dans la table ou la vue, où x est compris entre 1 et le nombre de colonnes de la table.  Consultez l’exemple DataService CSDL

**d:DatabaseDataType** - est le type de données de la colonne dans la base de données, par exemple de type de données SQL. Consultez l’exemple DataService CSDL

## <a name="supported-parametersproperty-types"></a>Types de paramètres/propriétés prises en charge
Voici les types pris en charge pour les paramètres et les propriétés. (Sensible à la casse)

| Types primitifs | Description |
|----|----|
| Valeur null | Représente l’absence d’une valeur |
| Valeur booléenne | Représente le concept mathématique de logique à valeurs binaires|
| Octets | Valeur de l’entier non signé 8 bits.|
|DateTime| Représente la date et l’heure avec des valeurs comprises entre 12:00:00 minuit, 1er janvier 1753 apr. J.C. à 11:59:59 (du soir), décembre 9999 après J.-C.|
|Décimal | Représente des valeurs numériques avec précision et échelle fixes. Ce type peut décrire une valeur numérique comprise entre-10 ^ 255 + 1 et + 10 ^-255 1|
| Double | Représente un nombre avec une précision de 15 chiffres et pouvant représenter des valeurs avec une plage approximative de ± 2,23E-308 à ± 1,79E à virgule flottante +308. **Utiliser le séparateur décimal en raison de problème d’exportation Exel**|
| Unique | Représente un nombre avec une précision de 7 chiffres et pouvant représenter des valeurs avec une plage approximative de ± 1,18E-38 à ± 3,40E à virgule flottante +38|
|GUID |Représente une valeur d’identificateur unique de 16 octets (128 bits) |
|Int16|Représente une valeur d’entier 16 bits signé |
|Int32|Représente une valeur d’entier signé 32 bits. |
|Int64|Représente une valeur d’entier 64 bits signé |
|Chaîne | Représente des données de type caractère à longueur fixe ou variable |


## <a name="see-also"></a>Voir aussi
- Si vous vous intéressez à comprendre le processus de mappage OData et l’objectif global, lisez ce [Mappage de données Service OData](marketplace-publishing-data-service-creation-odata-mapping.md) pour examiner les définitions, les structures et les instructions de l’article.
- Si vous êtes intéressés par l’étude des exemples, lisez l’article [Exemples de mappage de données Service OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour voir des exemples de code et de comprendre le contexte et la syntaxe du code.
- Pour renvoyer le chemin d’accès indiqué pour la publication d’un Service de données sur le marché d’Azure, lisez l’article [Guide de publication de Service de données](marketplace-publishing-data-service-creation.md).
