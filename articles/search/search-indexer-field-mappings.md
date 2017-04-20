<properties
pageTitle="Mappages de champs dans les indexeurs Azure recherche"
description="Configurer les mappages de champ d’indexeur recherche d’Azure pour tenir compte des différences dans les noms de champs et de représentations de données"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Mappages de champs dans les indexeurs Azure recherche

Lorsque vous utilisez des indexeurs de recherche d’Azure, vous pouvez parfois vous retrouver dans des situations où les données d’entrée ne correspondant pas tout à fait le schéma d’index de votre cible. Dans ces cas, vous pouvez utiliser les **mappages de champs** pour transformer vos données dans la forme souhaitée. 

Certaines situations où les mappages de champs sont utiles :
 
- Votre source de données comporte un champ `_id`, mais recherche d’Azure n’autorise pas les noms de champ commençant par un trait de soulignement. Un mappage de champ vous permet de « renommer » un champ. 
- Vous souhaitez remplir plusieurs champs de l’index avec les mêmes données de source de données, par exemple parce que vous souhaitez appliquer différents analyseurs à ces champs. Mappages de champs vous permettent de « branche » dans un champ de source de données.
- Vous avez besoin, en Base64, coder ou décoder vos données. Les mappages de champ prend en charge plusieurs **fonctions de mappage**, y compris les fonctions de Base64, codage et le décodage.   


> [AZURE.IMPORTANT] Actuellement, la fonctionnalité de mappage de champ est en mode Aperçu avant. Il est uniquement disponible dans l’API REST à l’aide de la version **2015-02-28-aperçu**. Veuillez mémoriser, afficher un aperçu des API sont destinés à des tests et d’évaluation et ne doit pas être utilisé dans des environnements de production.

## <a name="setting-up-field-mappings"></a>Configuration des mappages de champs

Vous pouvez ajouter des mappages de champs lors de la création d’un nouvel indexeur à l’aide de l’API d' [Indexeur de créer](search-api-indexers-2015-02-28-preview.md#create-indexer) . Vous pouvez gérer les mappages de champs sur un indexeur d’indexation à l’aide de l’API d' [Indexeur de mise à jour](search-api-indexers-2015-02-28-preview.md#update-indexer) . 

Un mappage de champ comprend 3 parties : 

1. A `sourceFieldName`, qui représente un champ dans la source de données. Cette propriété est requise. 

2. Facultatif `targetFieldName`, qui représente un champ dans votre index de recherche. En cas d’omission, le même nom dans la source de données est utilisé. 

3. Facultatif `mappingFunction`, qui peut transformer vos données en utilisant l’une de plusieurs des fonctions prédéfinies. La liste complète des fonctions est [inférieur](#mappingFunctions).

Les mappages de champs sont ajoutés à la `fieldMappings` tableau sur la définition de l’indexeur. 

Par exemple, voici comment vous pouvez prendre en considération les différences dans les noms de champ : 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Un indexeur peut avoir plusieurs mappages de champs. Par exemple, voici comment vous pouvez « branche » un champ :

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Recherche Azure utilise une comparaison respectant la casse pour résoudre les noms de champ et de la fonction dans les mappages de champ. Cette méthode est pratique (vous n’avez obtenir tous les le boîtier de la droite), mais cela signifie que votre source de données ou un index ne peut pas avoir des champs qui diffèrent uniquement par la casse.  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>Fonctions de mappage de champ

Ces fonctions sont actuellement prises en charge : 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

Effectue un codage Base64 de *sécurisés pour les URL* de la chaîne d’entrée. Suppose que l’entrée est au format UTF-8. 

#### <a name="sample-use-case"></a>Cas d’usage exemple 

Uniquement les caractères sécurisés pour les URL peuvent apparaître dans une clé de document de recherche d’Azure (car les clients doivent être en mesure de traiter le document à l’aide de l’API de recherche, par exemple). Si vos données contiennent des caractères d’URL non sécurisé et que vous souhaitez utiliser pour remplir un champ clé dans votre index de recherche, utiliser cette fonction.   

#### <a name="example"></a>Exemple 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

Effectue le décodage en Base64 de la chaîne d’entrée. L’entrée est considérée comme un *sécurisés pour les URL* de chaîne de codage Base64. 

#### <a name="sample-use-case"></a>Cas d’usage exemple 

Les valeurs BLOB des métadonnées personnalisées doivent être codé en ASCII. Vous pouvez utiliser le codage Base64 pour représenter des chaînes Unicode arbitraires dans le blob des métadonnées personnalisées. Cependant, pour rendre la recherche explicite, vous pouvez utiliser cette fonction pour retrouver les données codées en chaînes « regular » lors du remplissage de l’index de recherche.  

#### <a name="example"></a>Exemple 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

Divise un champ de type chaîne en utilisant le séparateur spécifié et sélectionne le jeton à la position spécifiée dans la division qui en résulte.

Par exemple, si l’entrée est `Jane Doe`, le `delimiter` est `" "`(espace) et le `position` est 0, le résultat est `Jane`; Si le `position` est 1, le résultat est `Doe`. Si la position fait référence à un jeton qui n’existe pas, une erreur est renvoyée.

#### <a name="sample-use-case"></a>Cas d’usage exemple 

Votre source de données contient un `PersonName` champ et que vous souhaitez indexer en tant que deux `FirstName` et `LastName` champs. Vous pouvez utiliser cette fonction pour diviser l’entrée à l’aide de l’espace comme séparateur.

#### <a name="parameters"></a>Paramètres

- `delimiter`: une chaîne à utiliser comme séparateur lors du fractionnement de la chaîne d’entrée.
- `position`: une position de base zéro d’entier du jeton de choisir après fractionnée de la chaîne d’entrée.    

#### <a name="example"></a>Exemple

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

Transforme une chaîne mise en forme en tant que tableau de chaînes JSON en un tableau de chaînes qui peut être utilisé pour remplir un `Collection(Edm.String)` champ dans l’index. 

Par exemple, si la chaîne d’entrée est `["red", "white", "blue"]`, puis le champ cible de type `Collection(Edm.String)` sera rempli avec les valeurs de trois `red`, `white` et `blue`. Pour les valeurs d’entrée qui ne peut pas être analysées en tant que tableaux de chaînes JSON, une erreur est renvoyée. 

#### <a name="sample-use-case"></a>Cas d’usage exemple

Une base de données SQL Azure n’a pas un type de données intégré qui mappe naturellement à `Collection(Edm.String)` champs de recherche d’Azure. Pour remplir les champs de chaîne de collection, mettre en forme vos données source sous la forme d’un tableau de chaînes JSON et utiliser cette fonction. 

#### <a name="example"></a>Exemple 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche d’Azure

Si vous avez des demandes de fonctionnalités ou des idées d’amélioration, veuillez contacter nous sur notre [site de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).