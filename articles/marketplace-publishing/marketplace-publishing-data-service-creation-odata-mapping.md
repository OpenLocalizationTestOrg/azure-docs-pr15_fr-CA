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

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Mappage d’un service web existant pour OData dans CSDL

>[AZURE.IMPORTANT] **Pour l’instant nous ne sont plus arrivant des nouvelles publications de Service de données. Nouvelle dataservices ne sera pas obtenir approuvée pour la liste.** Si vous disposez d’une application d’entreprise SaaS vous souhaitez publier sur AppSource, vous pouvez trouver plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous possédez une applications IaaS ou service de développeur vous souhaitez publier sur Azure Marketplace, vous trouverez plus d’informations [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Cet article donne une vue d’ensemble sur l’utilisation d’un langage CSDL pour mapper un service existant à un service compatible OData. Il explique comment créer le document de mappage (CSDL) qui transforme la requête d’entrée du client via un appel de service et la sortie (données) au client via un OData compatible d’alimentation. Microsoft Azure Marketplace propose des services pour les utilisateurs finaux en utilisant le protocole OData. Les services exposés par les fournisseurs de contenu (les propriétaires de données) sont exposés dans une variété de formes, reste, SOAP, etc..

## <a name="what-is-a-csdl-and-its-structure"></a>Qu’est un langage CSDL et sa structure ?
Un langage CSDL (Conceptual Schema Definition Language) est une spécification définissant comment décrire le service web ou base de données dans la formulation XML commune vers Azure Marketplace.

Vue d’ensemble simple de la **flux de la demande :**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

Le **flux de données** est dans le sens inverse :

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figure 1** les diagrammes comment un client serait obtenir des données à partir d’un fournisseur de contenu (votre service) en passant par le marché Azure.  Le langage CSDL est utilisé par le composant de mise en correspondance/Transformation pour traiter la demande, et passent des données entre les services du fournisseur de contenu et le client demandeur.

*Figure 1 : Détaillée du flux de client demande au fournisseur de contenu via Azure Marketplace*

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Pour informations sur Atom, Atom Pub et que le protocole OData sur lequel les extensions d’Azure Marketplace build, vérifiez : [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Extrait de dessus lien :     *« du protocole Open Data (ci-après dénommé OData) vise à fournir un protocole basée sur REST pour les opérations CRUD-style (création, lecture, mise à jour et suppression) par rapport aux ressources exposées en tant que services de données. Un service de données « » est un point de terminaison dans le cas où il est exposées à partir d’un ou plusieurs « collections » chaque avec zéro, une ou plusieurs « entrées », qui consistent en des données typées de paires de valeur nommé. OData est publiée par Microsoft sous les normes OASIS (Organization for le Advancement of Structured Information Standards) afin que toute personne qui souhaite peut générer des serveurs, des clients ou des outils sans droits d’auteur ou les restrictions ».*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Trois éléments critiques qui doivent être définies par le langage CSDL sont :

- **point de terminaison** de la Service fournisseur Web adresse (URI) du Service
- Les **paramètres de données** passé comme entrée pour le fournisseur de services, les définitions des paramètres envoyés au service du fournisseur de contenu pour le type de données.
- **Schéma** des données renvoyées au Service demandant le schéma des données fournies par le service du fournisseur de contenu, y compris de conteneur, les collections et les tables, les variables ou des colonnes et les types de données.

Le diagramme suivant montre une vue d’ensemble du flux d’où le client entre l’instruction OData (appel au service du web du fournisseur de contenu) pour le retour des résultats/données.

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Étapes suivantes :

1. Le client envoie la demande via un appel de Service avec les paramètres d’entrée définis dans XML vers Azure Marketplace
2. CSDL est utilisé pour valider l’appel de Service.
    - L’appel d’assistance mis en forme est ensuite envoyé au service de fournisseurs de contenu en l’Azure Marketplace
3. Le service Web s’exécute et l’action du verbe Http des préformes (c'est-à-dire obtenir) les données sont retournées vers Azure Marketplace où les données demandées (le cas échéant) sont expose au Format XML sur le Client en utilisant le mappage défini dans le langage CSDL.
4. Le Client reçoit les données (le cas échéant) au format XML ou JSON

## <a name="definitions"></a>Définitions de

### <a name="odata-atom-pub"></a>Pub d’OData ATOM

Une extension à la pub ATOM où chaque entrée représente une ligne d’un résultat de la valeur. La partie de contenu de l’entrée est améliorée afin de contenir les valeurs de la ligne, sous forme de paires clé / valeur. Plus d’informations est disponible à l’adresse : [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - langage de définition de schéma conceptuel

Permet la définition de fonctions (procédures stockées) et les entités qui sont exposées via une base de données. Plus disponibles à l’adresse : [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] Cliquez sur la liste déroulante des **autres versions** , puis sélectionnez une version, si vous ne voyez pas l’article.

### <a name="edm---entry-data-model"></a>EDM - modèle de données d’entrée
- Vue d’ensemble : [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink] : http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- Aperçu : [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink] : http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- Types de données : [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink] : http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

Le suivant de flux détaillée gauche à droite à partir de l’endroit où le client entre l’instruction OData (appel au service du web du fournisseur de contenu) pour l’obtention de résultats/données sauvegarde :

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>Notions de base CSDL

Un langage CSDL (Conceptual Schema Definition Language) est une spécification définissant comment décrire le service web ou base de données dans la formulation XML commune vers Azure Marketplace. CSDL décrit la critique pièces qui **permet la transmission de données à partir de la Source de données vers Azure Marketplace.** Les éléments principaux sont décrits ici :

- Informations sur l’interface qui décrit toutes les fonctions disponibles publiquement (nœud FunctionImport)
- Type de données pour tous les messages requests(input) et responses(outputs) du message (EntityContainer/EntitySet/EntityType nœuds)
- Liaison d’informations sur le protocole de transport pour être utilisé (en-tête de nœud)
- Informations d’adresse pour localiser le service spécifié (attribut BaseURI)

En bref, le langage CSDL représente un contrat de plate-forme - et indépendant du langage utilisé entre le demandeur de service et le fournisseur de services. En utilisant le langage CSDL, un client de localiser un service de base de données/service web et appeler ses fonctions accessibles.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Une base de données ou une Collection concernant un CSDL
**La spécification CSDL**

CSDL est la grammaire XML permettant de décrire un service web. La spécification est divisée en 4 éléments majeurs : EntitySet, FunctionImport ; Espace de noms et EntityType.

Pour rendre cette abstraction plus facile à comprendre vous permet de relier un CSDL à une table.

Gardez à l’esprit ;

  CSDL représente un contrat de plate-forme - et indépendant du langage utilisé entre le **demandeur de service** et le **fournisseur de services**. À l’aide du langage CSDL, un **client** de localiser un **service de base de données/service web** et invoquer aucun de ses publiquement disponible **fonctions.**

Pour un Service de données vous peuvent considérer les quatre parties d’une CSDL en termes de base de données, de Table, d’une colonne et d’une procédure stockée.

Concernant ces comme suit pour un Service de données :

- EntityContainer ~ = base de données
- EntitySet ~ = Table
- EntityType ~ = colonnes
- FunctionImport ~ = procédure stockée

**Verbes HTTP autorisés**
- GET – renvoie les valeurs à partir de la base de données (renvoie une Collection)
- VALIDER – utilisé pour passer des données et valeurs de retour facultatives à partir de la base de données (créer une nouvelle entrée dans la collection, l’id ou l’URI retour)
- Supprimer – supprime des données de la base de données (supprime une collection)
- Insérer, mettre à jour les données dans une base de données (remplacer une collection ou en créer un)

## <a name="metadatamapping-document"></a>Document de correspondance des métadonnées

Le document de métadonnées/mappage permet de mapper des services web existants d’un fournisseur de contenu afin qu’elles puissent être exposées sous la forme d’un service web de OData par le système d’Azure Marketplace. Il est basé sur le langage CSDL et implémente quelques extensions de langage CSDL pour répondre aux besoins de repos en fonction des services web exposés par le biais de Azure Marketplace. Les extensions sont disponibles dans l’espace de noms [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Voici un exemple de fichier CSDL : (copier et coller la ci-dessous l’exemple CSDL dans un éditeur XML et les modifier pour correspondre à votre Service.  Puis collez dans CSDL de mappage sous l’onglet du DataService lors de la création de votre service dans le [Portail de publication Azure Marketplace](https://publish.windowsazure.com)).

**Termes :** Les termes de l’interface utilisateur (PPUI) du [Portail de publication](https://publish.windowsazure.com) , concernant les termes CSDL.
- Offre « Title » dans la PPUI se rapporte à la MyWebOffer
- MyCompany à le PPUI est liée au **Nom de l’éditeur** dans le [Centre de développement Microsoft](http://dev.windows.com/registration?accountprogram=azure) de l’interface utilisateur
- Votre API se rapporte à un site ou un Service de données (il s’agit d’un Plan dans le PPUI)

**Hiérarchie :** 
 une société (fournisseur de contenu) possède des offres qui ont des plans, à savoir service(s), la ligne vers le haut avec une API.

### <a name="webservice-csdl-example"></a>Exemple de WebService CSDL

Se connecte à un service qui expose un point de terminaison web application (par exemple, une application C#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] Voir d’autres exemples de services Web de CSDL dans l’article [exemples de mappage d’un service web existant pour OData via CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###<a name="dataservice-csdl-example"></a>Exemple DataService CSDL

Se connecte à un service qui expose une table de base de données ou une vue comme un point de terminaison sous exemple montre que deux API de base de données en fonction de CSDL API (peut utiliser des vues plutôt que des tables).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Voir aussi
- Si vous êtes intéressé par apprentissage et la compréhension des nœuds spécifiques et leurs paramètres, lisez cet article [Les nœuds de mappage de données Service OData](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) pour des définitions et des explications, des exemples et utiliser le contexte de la demande de devis.
- Si vous êtes intéressés par l’étude des exemples, lisez l’article [Exemples de mappage de données Service OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour voir des exemples de code et de comprendre le contexte et la syntaxe du code.
- Pour renvoyer le chemin d’accès indiqué pour la publication d’un Service de données sur le marché d’Azure, lisez l’article [Guide de publication de Service de données](marketplace-publishing-data-service-creation.md).
