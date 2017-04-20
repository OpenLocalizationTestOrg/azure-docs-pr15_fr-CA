<properties
    pageTitle="Collectez des données pour exercer votre modèle : recommandations API d’apprentissage de l’ordinateur | Microsoft Azure"
    description="Machine Azure recommandations de formation - collectez des données pour exercer votre modèle"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>La collecte de données pour exercer votre modèle #

L’API de recommandations apprend à partir de vos dernières transactions pour trouver les éléments à recommandé pour un utilisateur particulier.

Après avoir créé un modèle, vous devrez fournir deux éléments d’information avant de pouvoir effectuer n’importe quelle formation : un fichier de catalogue et les données d’utilisation.

>   Si vous n’avez pas déjà fait, nous vous invitons à compléter le [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).


## <a name="catalog-data"></a>Les données de catalogue ##

### <a name="catalog-file-format"></a>Format de fichier catalogue ###

Le fichier catalogue contient des informations sur les articles que vous offrez à vos clients.
Les données du catalogue doivent suivre le format suivant :

- Sans fonctionnalités-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Fonctionnalités-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>Exemples de lignes dans un fichier de catalogue

Sans fonctionnalités :

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Fonctionnalités :

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>Détails de format

| Nom | Obligatoire | Type de |  Description |
|:---|:---|:---|:---|
| Id de l’élément |Oui | [A-z], [a-z] [0-9], [_] & #40 ; Trait de soulignement & #41 ; [-] & #40 ; tiret & #41 ;<br> Longueur maximale : 50 | Identificateur unique d’un élément. |
| Nom de l’élément | Oui | Tous les caractères alphanumériques<br> Longueur maximale : 255 | Nom de l’élément. |
| Catégorie d’article | Oui | Tous les caractères alphanumériques <br> Longueur maximale : 255 | Catégorie à laquelle cet article appartient (par exemple : cuisson en livres, feuilletons...) ; ne peut être vide. |
| Description | Non, sauf si les fonctionnalités sont présents (mais ne peut être vide) | Tous les caractères alphanumériques <br> Longueur maximale : 4000 | Description de cet élément. |
| Liste des fonctionnalités | N° | Tous les caractères alphanumériques <br> Longueur maximale : 4000 ; Nombre max de fonctionnalités : 20 | Liste séparée par des virgules du nom de la fonction = valeur de fonctionnalité qui peut être utilisé pour améliorer la recommandation de modèle.|

#### <a name="uploading-a-catalog-file"></a>Téléchargement d’un fichier de catalogue

Recherchez la [référence des API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) pour le téléchargement d’un fichier de catalogue.  

Notez que le contenu du fichier catalogue doit être passé en tant que corps de la demande.

Si vous téléchargez plusieurs fichiers de catalogue vers le même modèle avec plusieurs appels, il insère les nouveaux éléments de catalogue. Les éléments existants restent avec les valeurs d’origine. Vous ne peut pas mettre à jour les données de catalogue à l’aide de cette méthode.

>   Remarque : La taille de fichier maximale est de 200 Mo.
>   Le nombre maximal d’éléments dans le catalogue prises en charge est de 100 000 éléments.


## <a name="why-add-features-to-the-catalog"></a>Pourquoi ajouter des fonctionnalités au catalogue ?

Le moteur de recommandations crée un modèle statistique qui vous indique que les éléments sont susceptibles d’être aimé ou acheté par un client. Lorsque vous avez un nouveau produit qui n’a jamais été interagi avec elle n’est pas possible de créer un modèle sur des occurrences uniquement. Supposons que vous commencez à offrir une nouvelle « violon des enfants » dans votre magasin, dans la mesure où vous n’avez jamais vendu ce violon avant que vous ne pouvez pas savoir quels autres articles à recommander avec ce violon.

Ceci dit, si le moteur sait que violon d’informations (par exemple, il s’agit d’un instrument de musique, il est pour enfants âges 7-10, il n’est pas un violon coûteux, les etc.), puis le moteur peut apprendre à partir d’autres produits avec des fonctionnalités semblables. Par exemple, vous avez vendu de violon dans le passé et généralement les personnes qui achètent violons ont tendance à acheter des CD de musique classique et de peuplements de la musique de la feuille.  Le système recherche ces connexions entre les fonctionnalités et des recommandations basées sur les fonctionnalités, alors que votre nouveau violon a peu d’utilisation.

Fonctionnalités sont importées en tant que partie des données de catalogue, et puis les leur rang (ou l’importance de la fonction dans le modèle) est associé à un rang. Rang de la fonction peut modifier selon le modèle de données d’utilisation et le type d’éléments. Mais pour les articles/cohérentes l’utilisation, le rang doit avoir uniquement de petites fluctuations. Le rang de fonctionnalités est un nombre non négatif. Le numéro 0 signifie que la fonctionnalité n’a pas été classée (qui se produit si vous appelez cette API avant la fin de la première génération de rang). La date à laquelle le rang a été attribué est appelée la fraîcheur de score.


###<a name="features-are-categorical"></a>Les fonctionnalités sont Categorical

Cela signifie que vous devez créer des fonctionnalités qui ressemblent à une catégorie. Par exemple, prix = 9.34 n’est pas une fonctionnalité par catégorie. D’autre part, une fonctionnalité comme priceRange = Under5Dollars est une fonctionnalité par catégorie. Une autre erreur répandue est d’utiliser le nom de l’élément sous la forme d’une fonctionnalité. Cela entraînera le nom d’un élément d’être unique afin qu’il ne serait pas décrivent une catégorie. Assurez-vous que les fonctionnalités représentent des catégories d’articles.


###<a name="how-manywhich-features-should-i-use"></a>Fonctionnalités comment plusieurs/qui dois-je utiliser ?


En fin de compte les recommandations de build prend en charge la création d’un modèle doté de fonctionnalités de jusqu'à 20. Vous pouvez affecter plus de 20 fonctions pour les éléments de votre catalogue, mais vous devez effectuer une génération de classement et de sélectionner uniquement les fonctionnalités de ce rang élevé. (Une fonction avec un classement de 2.0 ou plus est une très bonne fonction à utiliser !). 


###<a name="when-are-features-actually-used"></a>Lorsque fonctionnalités réellement utilisées ?

Fonctionnalités sont utilisées par le modèle lorsqu’il n’a pas suffisamment de données transaction pour fournir des recommandations sur les seules informations de transaction. Donc les fonctionnalités auront le plus grand impact sur les « éléments à froid » – articles avec un nombre restreint de transactions. Si tous les éléments ont des informations de transaction suffisante vous peut-être pas enrichir votre modèle avec des fonctionnalités.


###<a name="using-product-features"></a>À l’aide des fonctionnalités du produit

Pour utiliser les fonctionnalités en tant que partie de votre build, que vous devez :

1. Assurez-vous que votre catalogue possède des fonctionnalités lorsque vous le téléchargez.

2. Déclencher une build de classement. Cela fera l’analyse sur l’importance/rang des fonctionnalités.

3. Déclencher une build de recommandations, suit la définition des paramètres de génération : true, allowColdItemPlacement sur true, la valeur useFeaturesInModel et modelingFeatureList doit être défini à la liste séparée par des virgules des fonctionnalités que vous souhaitez utiliser dans votre modèle. Pour plus d’informations, consultez [recommandations de paramètres de type de build](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) .





## <a name="usage-data"></a>Données d’utilisation ##
Un fichier d’utilisation contient des informations sur la façon dont ces éléments sont utilisés, ou les transactions à partir de votre entreprise.

#### <a name="usage-format-details"></a>Détails de Format de l’utilisation
Un fichier d’utilisation est un fichier de (valeur séparée par des virgules) de CSV dans lequel chaque ligne dans un fichier d’utilisation représente une interaction entre un utilisateur et d’un élément. Chaque ligne est mise en forme comme suit :<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| Nom  | Obligatoire | Type de | Description
|-------|------------|------|---------------
|Id de l’utilisateur|         Oui|[A-z], [a-z] [0-9], [_] & #40 ; Trait de soulignement & #41 ; [-] & #40 ; tiret & #41 ;<br> Longueur maximale : 255 |Identificateur unique de l’utilisateur.
|Id de l’élément|Oui|[A-z], [a-z] [0-9], [& #, 95 ;] & #40 ; Trait de soulignement & #41 ; [-] & #40 ; tiret & #41 ;<br> Longueur maximale : 50|Identificateur unique d’un élément.
|Heure|Oui|Date au format : AAAA/MM/ddTHH (par exemple, 2013/06/20T10:00:00)|Heure de données.
|Événement|N° | Une des valeurs suivantes :<br>• Cliquez sur<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Achat| Le type de transaction. |

#### <a name="sample-rows-in-a-usage-file"></a>Exemples de lignes dans un fichier d’utilisation

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>Téléchargement d’un fichier d’utilisation

Recherchez la [référence des API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) pour le téléchargement de fichiers d’utilisation.
Notez que vous devez transmettre le contenu du fichier utilisation comme le corps de l’appel HTTP.

>  Remarque :

>  Taille de fichier maximale : 200 Mo. Vous risquez de télécharger plusieurs fichiers d’utilisation.

>  Vous devez télécharger un fichier de catalogue avant de commencer à ajouter des données d’utilisation de votre modèle. Seuls les éléments dans le fichier de catalogue seront utilisés lors de la phase de formation. Tous les autres éléments seront ignorés.

## <a name="how-much-data-do-you-need"></a>La quantité de données avez-vous besoin ?

La qualité de votre modèle est très dépendante de la qualité et la quantité de vos données.
Le système apprend que lorsque les utilisateurs achètent des éléments différents (nous appelons cette collaboration occurrences). Pour les versions FBT, il est également important de savoir quels éléments sont achetés dans les mêmes transactions. 

Une bonne méthode empirique est d’utiliser la plupart des éléments dans des transactions de 20 ou plus, donc si vous avez 10 000 éléments dans votre catalogue, nous vous recommande que vous disposez d’au moins 20 fois ce nombre de transactions ou d’environ 200 000. Une fois encore, il s’agit d’une règle générale. Vous devrez faire plusieurs essais avec vos données.

Une fois que vous avez créé un modèle, vous pouvez effectuer une [évaluation en mode hors connexion](cognitive-services-recommendations-buildtypes.md) pour vérifier le fonctionnement de votre modèle est susceptible d’effectuer.
