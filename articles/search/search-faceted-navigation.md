<properties 
    pageTitle="Comment faire pour implémenter la navigation à facettes dans Azure recherche | Microsoft Azure | Rechercher des catégories de navigation" 
    description="Ajouter la navigation à facettes à des applications qui s’intègrent à la recherche d’Azure, un service de recherche de nuage hébergé sur Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>Comment faire pour implémenter la navigation à facettes dans Azure recherche

Navigation à facettes est un mécanisme de filtrage qui fournit une navigation autonome d’exploration dans des applications de recherche. Alors que le terme « navigation à facettes » est inconnu, il est presque un étant donné que vous avez utilisé un autre avant. Comme dans l’exemple suivant, la navigation à facettes est rien de plus que les catégories utilisées pour filtrer les résultats.

## <a name="what-it-looks-like"></a>Ce qu’il ressemble à

 ![][1]
  
Les facettes peuvent vous aider à trouver ce que vous recherchez, tout en garantissant que vous n’aurez aucun résultats. En tant que développeur, facettes vous permettent d’exposer les critères de recherche plus utiles pour la navigation de votre corpus de recherche. Dans les applications de vente au détail en ligne, navigation à facettes repose souvent sur les marques, départements (chaussures pour enfants), taille, prix, popularité et des notations. 

Implémentation de la navigation à facettes diffère à travers des technologies de recherche et peut être très complexe. Dans Azure, recherche, navigation à facettes est générée au moment de la requête, à l’aide des champs attribués précédemment spécifiés dans votre schéma. Dans les requêtes de l’application est générée, une requête doit envoyer les *paramètres de requête de facette* pour recevoir les valeurs de filtre de facettes disponible pour ce jeu de résultats du document. Pour réellement supprimer le résultat du document, l’application doit appliquer un `$filter` expression.

En termes de développement d’application, écrivez du code qui construit des requêtes constitue l’essentiel du travail. La plupart des problèmes d’application que vous souhaiteriez de navigation à facettes est fournie par le service, y compris la prise en charge intégrée pour la définition de plages et de l’obtention du nombre pour les résultats de la facette. Ce service comprend également les valeurs par défaut sensibles qui vous aident à éviter les structures de navigation difficile à manipuler. 

Cet article contient les sections suivantes :

- [Comment le créer](#howtobuildit)
- [Génération de la couche de présentation](#presentationlayer)
- [Construction de l’index](#buildindex)
- [Vérification de la qualité des données](#checkdata)
- [Créer la requête](#buildquery)
- [Conseils sur la façon de contrôler la navigation à facettes](#tips)
- [Navigation à facettes en fonction des valeurs de la plage](#rangefacets)
- [Navigation à facettes basée sur GeoPoints](#geofacets)
- [Essayez-le](#tryitout)

##<a name="why-use-it"></a>Pourquoi l’utiliser
Les applications de recherche plus efficaces ont plusieurs modèles interaction en dehors d’une zone de recherche. Navigation à facettes est un autre point d’entrée de la recherche, offrant une alternative pratique à la saisie des expressions de recherche complexe à la main.

##<a name="know-the-fundamentals"></a>Connaître les notions de base

Si vous ne connaissez pas rechercher le développement, la meilleure façon de penser à une navigation à facettes est qu’il affiche les possibilités de recherche auto-dirigée. Il s’agit d’un type d’expérience de la recherche de zoom, basé sur des filtres prédéfinis, utilisés pour cerner rapidement les résultats de la recherche à l’aide du pointer-cliquer. 

**Modèle d’interaction**

L’expérience de recherche pour la navigation à facettes est itératif, nous pouvons donc commencer par comprendre il sous la forme d’une séquence de requêtes qui se déroulent en réponse aux actions de l’utilisateur.

Le point de départ est une page d’application qui offre une navigation à facettes, généralement placée sur la périphérie. Navigation à facettes est souvent une structure arborescente, avec des cases à cocher pour chaque valeur ou texte interactif. 

1.  Une requête envoyée à la recherche d’Azure spécifie la structure de navigation à facettes via un ou plusieurs paramètres de requête de facette. Par exemple, la requête peut inclure des `facet=Rating`, éventuellement avec un `:values` ou `:sort` option pour affiner la présentation.
2.  La couche de présentation affiche une page de recherche qui fournit une navigation à facettes, à l’aide des facettes spécifiées dans la demande.
3.  Doté d’une structure de navigation à facettes qui inclut le contrôle d’accès, l’utilisateur clique sur « 4 » pour indiquer que seuls les produits dont le niveau est supérieur ou égal à 4 doivent s’afficher. 
4.  En réponse, l’application envoie une requête qui inclut`$filter=Rating ge 4` 
5.  La couche présentation met à jour l’affichage dans la page, un jeu de résultats réduits contenant uniquement les éléments qui répondent aux nouveaux critères (dans ce cas, les produits classés 4 et ultérieures).

Une facette est un paramètre de requête, mais ne la confondez pas avec la requête. Il n’est jamais utilisée comme critère de sélection d’une requête. Au lieu de cela, pense que facette paramètres de requête comme entrées de la structure de navigation qui est renvoyée dans la réponse. Pour chaque paramètre de requête de facette que vous fournissez, recherche d’Azure va évaluer le nombre de documents se trouvent dans les résultats partiels pour chaque valeur de la facette.

Notez le `$filter` à l’étape 4. Il s’agit d’un aspect important de la navigation à facettes. Bien que les filtres et les facettes sont indépendantes dans l’API, vous devez à la fois pour fournir l’expérience que vous avez l’intention de. 

**Modèles de conception**

Dans le code de l’application, le modèle est à utiliser des paramètres de requête de facette pour retourner la structure de navigation à facettes en même temps que les résultats de facette, plus une expression $filter qui gère l’événement click. Pensez à la `$filter` expression comme le code derrière la suppression effective des résultats de recherche renvoyés pour la couche de présentation. Étant donné une facette de couleurs, cliquez sur la couleur rouge est implémentée via une `$filter` expression qui sélectionne uniquement les éléments qui ont une couleur rouge. 

**Notions de base de requêtes recherche Azure**

Recherche d’Azure, une demande est spécifiée via un ou plusieurs paramètres de requête (pour une description de chacun d’eux, consultez [Rechercher des Documents](http://msdn.microsoft.com/library/azure/dn798927.aspx) ). Aucun des paramètres de requête sont obligatoires, mais vous devez disposer d’au moins un ordre pour une requête valide.

Précision, entendue généralement comme la possibilité de filtrer les résultats non pertinents, est obtenue par le biais d’une ou les deux de ces expressions :

- **recherche =**<br/>
La valeur de ce paramètre constitue l’expression de recherche. Il peut être un seul élément de texte ou une expression de recherche complexe qui inclut plusieurs conditions et des opérateurs. Sur le serveur, une expression de recherche est utilisée pour la recherche de texte intégral, recherche les champs de recherche dans l’index pour la correspondance des termes, en retournant les résultats par ordre. Si vous définissez `search` null, la requête à l’exécution est sur la totalité de l’index (c'est-à-dire, `search=*`). Dans ce cas, les autres éléments de la requête, comme une `$filter` ou profil de notation, seront les principaux facteurs affectant les documents sont retournés `($filter`) et dans quel ordre (`scoringProfile` ou `$orderb`y).

- **$filter =**<br/>
Un filtre est un mécanisme puissant pour limiter la taille des résultats de recherche basé sur les valeurs des attributs de document spécifique. A `$filter` est évalué en premier, suivi par la logique faceting qui génère les valeurs disponibles et le nombre correspondant pour chaque valeur

Expressions de recherche complexes diminue les performances de la requête. Si possible, utiliser des expressions de filtre bien construite pour augmenter la précision et d’améliorer les performances de la requête.

Pour mieux comprendre comment un filtre ajoute une plus grande précision, comparez une expression de recherche complexe qui contient une expression de filtre :

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Bien que les deux requêtes sont valides, le second est excellente si vous recherchez des non-motels avec le stationnement de Seattle. La première requête s’appuie sur ces mots spécifiques qui est mentionné ou non mentionnés dans les champs de type chaîne comme le nom, la Description et tout autre champ contenant des données de recherche. La deuxième requête recherche des correspondances précises sur les données structurées et est susceptible d’être beaucoup plus précis.

Dans les applications qui incluent la navigation à facettes, vous devez être sûr que chaque action de l’utilisateur sur une structure de navigation à facettes est accompagnée d’une réduction du champ des résultats de recherche, grâce à une expression de filtre.

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>Comment le créer

Navigation à facettes dans Azure recherche est implémentée dans votre code d’application qui génère de la demande, mais s’appuie sur des éléments prédéfinis dans votre schéma.

Prédéfinis dans votre recherche, l’index est la `Facetable [true|false]` attribut index, sur les champs sélectionnés pour activer ou désactiver leur utilisation dans une structure de navigation à facettes. Sans `"Facetable" = true`, un champ ne peut pas être utilisé dans la navigation de facette.

Au moment de la requête, votre code d’application crée une requête qui inclut `facet=[string]`, un paramètre de demande qui fournit le champ à facettes par. Une requête peut comprendre plusieurs facettes, tel que `&facet=color&facet=category&facet=rating`, chacun séparé par un caractère esperluette (&).

Code de l’application doit également construire une `$filter` expression pour gérer les événements click dans la navigation à facettes. A `$filter` réduit les résultats de la recherche, à l’aide de la valeur de la facette comme critères de filtre.

Recherche Azure renvoie les résultats de la recherche, par les mots entrés par l’utilisateur, ainsi que des mises à jour de la structure de navigation à facettes. Recherche d’Azure, navigation à facettes est une construction de niveau unique, avec les valeurs de facettes et compte du nombre de résultats sont trouvés pour chacun d’eux.

La couche de présentation dans votre code fournit l’expérience utilisateur. Il doit répertorier les éléments constitutifs de la navigation à facettes, comme l’étiquette, les valeurs, les cases à cocher et le nombre. L’API REST de recherche Azure est indépendant de la plate-forme, utilisez quelque langue et la plate-forme que vous souhaitez. L’important est d’inclure des éléments d’interface utilisateur qui prennent en charge incrémentielle actualisation, avec l’état de l’interface utilisateur mis à jour chaque facette supplémentaire est activée. 

Dans les sections suivantes, nous allons étudier comment générer chaque partie, en commençant par la couche de présentation.

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>Génération de la couche de présentation

Utilisation de la couche de présentation peut vous aider à découvrir des exigences qui peuvent être omis dans le cas contraire et comprennent les capacités sont essentielles à l’expérience de recherche.

En termes de navigation à facettes, votre page web ou une application affiche la structure de navigation à facettes, détecte l’entrée d’utilisateur sur la page et insère les éléments modifiés. 

Pour les applications web AJAX est couramment utilisée dans la couche de présentation, car elle vous permet d’actualiser les modifications incrémentielles. Vous pouvez également utiliser ASP.NET MVC ou toute autre plate-forme de visualisation qui peut se connecter à un service de recherche d’Azure via HTTP. L’exemple d’application référencé dans cet article : **Catalogue AdventureWorks** – qui se trouve être une application ASP.NET MVC.

L’exemple suivant, extrait du fichier **index.cshtml** de l’exemple d’application crée une structure HTML dynamique pour afficher la navigation à facettes dans la page de résultats de recherche. Dans l’exemple, navigation à facettes est intégrée à la page de résultats de recherche et s’affiche une fois que l’utilisateur a soumis un terme de recherche.

Notez que chaque facette a une étiquette (couleurs, catégories, prix), une liaison à un champ à facettes (couleur, categoryName, listPrice) et un `.count` paramètre, utilisé pour retourner le nombre d’éléments trouvés pour ce résultat de facette.

  ![][2]
 

> [AZURE.TIP] Lorsque vous créez la page de résultats de recherche, n’oubliez pas d’ajouter un mécanisme de compensation des facettes. Si vous utilisez les cases à cocher, les utilisateurs peuvent facilement caractère intuitif les comment effacer les filtres. Pour les autres mises en page, vous devrez peut-être un modèle de plan de site ou une autre approche de creative. Par exemple, dans l’exemple d’application AdventureWorks catalogue, vous pouvez cliquer sur le titre, le catalogue AdventureWorks, pour rétablir la page de recherche.

<a name="buildindex"></a>
##<a name="build-the-index"></a>Construction de l’index

Faceting est activé sur une base de champ par champ dans l’index, via cet attribut index : `"Facetable": true`.  
Tous les types de champ pouvant être utilisé dans la navigation à facettes sont `Facetable` par défaut. Ces types de champs comprennent `Edm.String`, `Edm.DateTimeOffset`et tous les types de champ numérique (tous les types de champ sont essentiellement des facetable à l’exception de `Edm.GeographyPoint`, qui ne peut pas être utilisé dans la navigation à facettes). 

Lors de la création d’un index, une pratique recommandée pour la navigation à facettes est explicitement désactiver faceting pour les champs qui ne doivent jamais être utilisés comme une facette.  En particulier, les champs de chaîne de valeurs singleton, comme un nom d’ID ou un produit, doivent être définies sur `"Facetable": false` pour empêcher leur utilisation accidentelle (et inefficace) dans une navigation à facettes.

Voici le schéma pour l’exemple d’application AdventureWorks catalogue (ajustée de certains attributs pour réduire la taille globale) :

 ![][3]
 
Notez que `Facetable` est désactivée pour les champs de type chaîne qui ne devrait pas être utilisés en tant que facettes, telles que l’ID ou le nom. Tourner les faceting off où vous n’avez pas besoin qu’il vous permet de conserver une petite taille de l’index et améliore généralement les performances.

> [AZURE.TIP] Pour obtenir les meilleurs résultats, incluent l’ensemble des attributs d’index pour chaque champ. Bien que les `Facetable` est activée par défaut pour presque tous les champs, volontairement définissant chaque attribut peut vous aider à imaginer les implications de chaque décision de schéma. 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>Vérification de la qualité des données 

Lors du développement d’une application orientée données, préparation des données est souvent une des parties plus gros du travail. Applications de recherche ne font pas exception. La qualité de vos données a une incidence directe sur si la structure de navigation à facettes matérialise que vous attendez, ainsi que son efficacité pour vous aider à construire des filtres réduisant le résultat défini.

Recherche d’Azure, le corpus de recherche est constitué à partir de documents remplir un index. Documents fournissent les valeurs qui sont utilisées pour calculer les facettes. Si vous souhaitez la facette par prix ou par marque, chaque document doit contenir des valeurs pour le *nom du produit* et *ProductPrice* qui sont valides, cohérentes et productif sous la forme d’une option de filtre.

Vous trouverez ci-dessous quelques rappels de ce qu’il faut éliminer pour :

- Pour chaque champ que vous souhaitez facette par, demandez-vous si elle contient des valeurs qui conviennent en tant que filtres dans Recherche auto-dirigée. Les valeurs doivent être suffisamment distinctive pour proposer un choix clair entre concurrents options, court et descriptif.
- Fautes d’orthographe ou valeurs presque correspondantes. Si vous facette sur couleur et les valeurs du champ Inclure Orange et Ornage (une faute d’orthographe), les deux choisit une facette basée sur le champ de couleur.
- Texte de casse mixte peut également causer des dommages dans la navigation à facettes, orange et Orange apparaissant sous forme de deux valeurs différentes. 
- Unique et plusieurs versions de la même valeur peuvent entraîner une facette distincte pour chacun.

Comme vous pouvez l’imaginer, diligence dans la préparation des données est un aspect essentiel de la navigation à facettes efficace.

<a name="buildquery"></a>
##<a name="build-the-query"></a>Créer la requête

Le code que vous écrivez pour la création de requêtes doit spécifier toutes les parties d’une requête valide, y compris des expressions de recherche, les facettes, les filtres, attribution de scores profils – tout utilisée pour formuler une demande. Dans cette section, nous allons Explorer où interviennent les facettes dans une requête, et comment les filtres sont utilisés avec les facettes pour fournir un jeu de résultats réduits.

Un exemple est souvent un bon endroit pour commencer. L’exemple suivant, extrait du fichier **CatalogSearch.cs** , génère une demande qui crée la navigation de facette basée sur les prix, la catégorie et la couleur. 

Notez que les facettes font partie intégrante de cet exemple d’application. L’expérience de recherche dans le catalogue AdventureWorks est conçu autour de filtres et de navigation à facettes. Ceci est évident dans l’emplacement bien visible de la navigation à facettes dans la page. L’exemple d’application inclut des paramètres de l’URI pour facettes (couleur, catégorie, prix) en tant que propriétés de la méthode de recherche (comme construit dans l’exemple d’application).

  ![][4]
 
Un paramètre de requête facette est défini sur un champ et selon le type de données, peut être plus paramétrée par liste délimitée par des virgules qui contient `count:<integer>`, `sort:<>`, `intervals:<integer>`, et `values:<list>`. Une liste de valeurs est prise en charge pour les données numériques lors de la définition des plages. Pour plus de détails, reportez-vous à la section [Rechercher des Documents (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Ainsi que des facettes, la demande formulée par votre application doit également créer des filtres pour affiner l’ensemble de documents de candidat basé sur une sélection de valeur de facette. Pour un magasin de vélos, la navigation à facettes fournit des indices à des questions telles que « les couleurs, les fabricants et les types de vélos sont disponibles », tout en filtrant les réponses aux questions telles que « les vélos exactes sont rouges, vélos tout terrain, dans ce prix plage ».

Lorsqu’un utilisateur clique sur « Rouge » pour indiquer que seuls les produits de couleur rouge doivent s’afficher, la requête suivante, l’application envoie inclurait `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Meilleures pratiques pour la navigation à facettes

La liste suivante récapitule les meilleures pratiques.

- **Précision**<br/>
Utiliser des filtres. Si vous utilisez uniquement les expressions de recherche uniquement, la recherche de radical pourraient provoquer un document doit être retourné qui n’a pas la valeur de la facette précis dans un de ses champs. 

- **Champs de cible**<br/>
Dans à facettes de zoom, vous voulez généralement inclure uniquement les documents dont la valeur de la facette dans un champ spécifique (facettes), pas n’importe où sur tous les champs de recherche. Ajout d’un filtre de renforce le champ cible en dirigeant le service pour effectuer une recherche uniquement dans le champ à facettes pour une valeur correspondante.

- **Efficacité des index**<br/>
Si votre application utilise exclusivement les navigation à facettes (autrement dit, aucune zone de recherche), vous pouvez marquer le champ en tant que `searchable=false`, `facetable=true` pour produire un index plus compact. En outre, l’indexation se produit uniquement sur les valeurs de facettes ensemble, avec aucun saut de mot ou de l’indexation des composants d’une valeur de plusieurs mot.

- **Performances**<br/>
Les filtres de limiter l’ensemble de documents de candidats pour la recherche et les exclure de classement. Si vous avez un grand ensemble de documents, en utilisant une perceuse facette très sélective de bas souvent vous donnera améliorer considérablement les performances.


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>Conseils sur la façon de contrôler la navigation à facettes

Vous trouverez ci-dessous une feuille d’info-bulle avec des conseils sur des problèmes spécifiques.

**Ajouter des étiquettes pour chaque champ dans la navigation de facette**

Étiquettes sont généralement définies dans le code HTML ou d’un formulaire (**index.cshtml** dans l’exemple d’application). Il existe aucune API dans Azure, recherchez les étiquettes de navigation de facette ou tout autre type de métadonnées.

**Définir les champs peuvent être utilisés en tant que facette**

Rappelez-vous que le schéma de l’index détermine les champs à utiliser comme une facette. Si qu'un champ est facetable, la requête spécifie quels champs facette par. Le champ par lequel vous êtes faceting fournit les valeurs qui apparaissent en dessous de l’étiquette. 

Les valeurs qui s’affichent sous chaque étiquette sont récupérées à partir de l’index. Par exemple, si le champ de la facette est de *couleur*, les valeurs disponibles pour le filtrage supplémentaires seront les valeurs pour ce champ (rouge, noir et ainsi de suite).

Pour les valeurs numériques et DateTime uniquement, vous pouvez définir explicitement des valeurs dans le champ de la facette (par exemple, `facet=Rating,values:1|2|3|4|5`). Une liste de valeurs est autorisée pour ces types de champs afin de simplifier la séparation de résultats de facette dans des plages contiguës (soit des plages basées sur des valeurs numériques ou des périodes de temps). 

**Ajuster les résultats de facette**

Résultats de facette sont des documents trouvés dans les résultats de recherche qui correspondent à un terme de facette. Dans l’exemple suivant, dans les résultats de la recherche pour le *cloud computing*, 254 éléments également ont *spécification interne* comme un type de contenu. Les éléments ne sont pas nécessairement mutuellement exclusives. Si un élément répond aux critères des deux filtres, elle est comptée dans chacun d’eux. Cela est possible lorsque faceting sur `Collection(Edm.String)` champs, qui sont souvent utilisés pour mettre en œuvre le balisage de document.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

En général, si vous trouvez que durablement les résultats de la facette sont trop volumineux, il est recommandé que vous ajoutez plus de filtres, comme expliqué dans les sections précédentes, pour donner à vos utilisateurs d’application plus d’options pour affiner la recherche.

**Limiter les éléments dans la navigation de facette**

Pour chaque champ à facettes dans l’arborescence de navigation, il existe une limite par défaut de 10 valeurs. Cette valeur par défaut de sens pour les structures de navigation car il conserve la liste des valeurs à une taille gérable. Vous pouvez substituer la valeur par défaut en assignant une valeur à compter.

- `&facet=city,count:5`Spécifie que les 5 premières villes de haut rang des résultats sont retournés sous la forme d’un résultat de facette. Étant donné un terme de recherche de correspondances de 32 et de « aéroport », si la requête spécifie `&facet=city,count:5`, uniquement les cinq premières villes uniques avec le plus de documents dans les résultats de recherche sont inclus dans les résultats de la facette.

Notez la différence entre les résultats de la recherche et les résultats de la facette. Résultats de la recherche sont tous les documents qui correspondent à la requête. Résultats de la facette sont les correspondances pour chaque valeur de la facette. Dans l’exemple, les résultats de la recherche inclura des noms de ville qui ne sont pas dans la liste de classification des facettes (5 dans notre exemple). Les résultats qui sont filtrés par le biais de navigation à facettes sont visibles à l’utilisateur lorsqu’il ou elle efface des facettes, ou s’il choisit d’autres facettes en dehors de la ville. 

> [AZURE.NOTE] Traitant des `count` lorsqu’il y a plus d’un type peut être source de confusion. Le tableau suivant offre un bref résumé de la façon dont le terme est utilisé dans l’API de recherche d’Azure, exemples de code et la documentation. 

- `@colorFacet.count`<br/>
Dans le code de présentation, vous devez voir un paramètre count sur la facette, utilisée pour afficher le nombre de résultats de la facette. Dans les résultats de facette, le nombre indique le nombre de documents qui correspondent dans le terme de facette ou la plage.

- `&facet=City,count:12`<br/>
Dans une requête de facette, vous pouvez définir le nombre une valeur.  La valeur par défaut est 10, mais vous pouvez la définir supérieur ou inférieur. Définition de `count:12` Obtient le haut 12 correspond à dans les résultats de facette par nombre de document.

- "`@odata.count`"<br/>
Dans la réponse à la requête, cette valeur indique le nombre d’éléments correspondants dans les résultats de recherche. En moyenne, il est plus importante que la somme de tous les résultats de facette associée, en raison de la présence d’éléments qui correspondent au terme de la recherche, mais aucune correspondance de valeur de facette.


**Niveaux dans la navigation à facettes** 

Comme indiqué, il n’existe aucune prise en charge directe pour imbriquer des facettes dans une hiérarchie. Prêts à l’emploi, une navigation à facettes prend uniquement en charge un niveau de filtres. Cependant, les solutions de contournement existent. Vous pouvez coder une structure hiérarchique de facette dans un `Collection(Edm.String)` avec une entrée point par hiérarchie. Mise en œuvre de cette solution de contournement est au-delà de la portée de cet article, mais vous pouvez lire sur les collections dans [OData par exemple](http://msdn.microsoft.com/library/ff478141.aspx). 

**Validation des champs**

Si vous générez la liste de facettes de façon dynamique en fonction de l’entrée d’utilisateur non fiable, vous devez soit valider que les noms des champs à facettes sont valides ou échappement les noms lors de la génération d’URL à l’aide d’une `Uri.EscapeDataString()` dans .NET, ou son équivalent dans votre plate-forme de choix.

**Nombre de résultats de facette**

Lorsque vous ajoutez un filtre pour une requête à facettes, vous pouvez souhaiter conserver l’instruction facette (par exemple, `facet=Rating&$filter=Rating ge 4`). Techniquement, les facettes = contrôle d’accès n’est pas nécessaire, mais maintenant il renvoie le nombre de valeurs de facettes pour le classement 4 et versions ultérieures. Par exemple, si un utilisateur clique sur « 4 » et la requête inclut un filtre pour supérieure ou égale à « 4 », nombres sont retournés pour chaque classification de 4 et ultérieures.  

**Implications ont sur le nombre de facettes**

Dans certaines circonstances, vous pouvez trouver que le nombre de facettes ne correspondre pas les jeux de résultats (voir [navigation à facettes dans Azure recherche (billet)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Nombre de facettes peut être inexactes en raison de l’architecture ont. Chaque index de recherche a plusieurs milieu des fragments, et chacun d’eux signale les facettes top N par nombre de document, qui est ensuite combiné en un résultat unique. Si certains milieu des fragments ont beaucoup de mise en correspondance des valeurs, tandis que d’autres ont moins, vous pouvez trouver que certaines valeurs de facettes sont manquants ou sous-pris en compte dans les résultats.

Bien que ce comportement peut modifier à tout moment, si vous rencontrez ce problème aujourd'hui, vous pouvez le contourner en artificiellement gonfler le nombre :<number> à un très grand nombre d’appliquer le rapports complets et détaillés de chaque partagé. Si la valeur de count : est supérieur ou égal au nombre de valeurs uniques dans le champ, vous avez la garantie des résultats précis. Toutefois, lorsque le nombre de document est réellement élevé, qu'il existe une altération des performances, donc utilisé cette option judicieusement.

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>Navigation de facette basée sur une plage de valeurs

Faceting sur des plages est une exigence courante pour application recherche. Plages sont prises en charge pour les données numériques et les valeurs DateTime. Vous pouvez en savoir plus sur chaque approche de [Rechercher des Documents (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Recherche Azure simplifie la construction de la plage en fournissant deux approches pour calculer une plage. Ces deux approches, Azure recherche crée les plages appropriés étant donnés les entrées que vous avez fournies. Par exemple, si vous spécifiez les valeurs de la plage de 10 | 20 | 30, il crée automatiquement les plages de 0 -10, 10-20, 20-30. L’exemple d’application supprime les intervalles qui sont vides. 

**Méthode 1 : Utiliser le paramètre d’intervalle**<br/>
Pour définir les facettes du prix par incréments de 10 $, vous devez spécifier :`&facet=price,interval:10`


**L’approche 2 : Utiliser une liste de valeurs**<br/>
Pour les données numériques, vous pouvez utiliser une liste de valeurs.  Prenez en compte la plage facette pour listPrice, rendu comme suit :

  ![][5]

La plage est spécifiée dans le fichier **CatalogSearch.cs** à l’aide d’une liste de valeurs :

    facet=listPrice,values:10|25|100|500|1000|2500

Chaque plage est construit à l’aide de 0 comme point de départ, une valeur dans la liste sous la forme d’un point de terminaison, puis de la tranche précédente pour créer des intervalles discrets. Recherche Azure fera dans le cadre de la navigation à facettes. Vous n’avez pas à écrire du code pour la structuration de chaque intervalle.

### <a name="build-a-filter-for-facet-ranges"></a>Créer un filtre pour les plages de facette ###

Pour filtrer les documents basés sur une plage sélectionnée par l’utilisateur, vous pouvez utiliser la `"ge"` et `"lt"` filtrer des opérateurs dans une expression deux parties qui définit les points de terminaison de la plage. Par exemple, si l’utilisateur choisit la plage 10 à 25, le filtre est `$filter=listPrice ge 10 and listPrice lt 25`.

Dans l’exemple d’application, l’expression de filtre utilise les paramètres de **prix** et de **priceTo** pour définir les points de terminaison. La méthode **BuildFilter** dans **CatalogSearch.cs** contient l’expression de filtre qui vous donne les documents à l’intérieur d’une plage.

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>Navigation filtrée basée sur GeoPoints

Il est courant de voir des filtres qui vous aident à choisir un magasin, un restaurant ou destination en fonction de sa proximité à votre emplacement actuel. Bien que ce type de filtre peut ressembler à une navigation à facettes, il est en fait juste un filtre. Nous mentionner ici pour ceux d'entre vous qui recherchent spécifiquement des conseils d’implémentation pour ce problème de conception particulière.

Il existe deux fonctions géographiques dans recherche d’Azure, **geo.distance** et **geo.intersects**.

- La fonction **geo.distance** retourne la distance en kilomètres entre deux points, un un champ et l’autre une constante passée comme partie du filtre. 

- La fonction **geo.intersects** retourne la valeur true si un point donné se trouve dans un polygone donné, où le point est un champ et le polygone est spécifié en tant que constante liste de coordonnées passées en tant que partie du filtre.

Vous trouverez des exemples de filtres dans la [syntaxe d’expression de OData (recherche Azure)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##<a name="try-it-out"></a>Essayez-le

Azure démo de Works recherche Adventure sur Codeplex contient les exemples référencés dans cet article. Lorsque vous travaillez avec les résultats de la recherche, regardez l’URL pour les modifications dans la construction de la requête. Cette application se produit pour ajouter des facettes à l’URI que vous sélectionnez chacune d’elles.

1.  Configurer l’exemple d’application à utiliser votre URL de service et de la clé de l’api. 

    Notez le schéma défini dans le fichier Program.cs du projet CatalogIndexer. Il spécifie les champs de facetable pour les listPrice, couleur, taille, poids, categoryName et modelName.  Seuls certains de ces (couleur, listPrice, categoryName) sont réellement implémentées dans la navigation à facettes.

3.  Exécutez l’application. 

    Dans un premier temps, que la zone de recherche est visible. Vous pouvez cliquez sur le bouton Rechercher tout de suite pour obtenir tous les résultats, ou bien tapez un terme de recherche.

    ![][7]
 
4.  Permet d’entrer un terme de recherche, comme le vélo et cliquez sur **Rechercher**. La requête s’exécute rapidement.
 
    Une structure de navigation à facettes est également retournée avec les résultats de recherche.  Dans l’URL, les facettes de couleurs, les catégories et les prix sont null. Dans la page résultats de la recherche, la structure de navigation à facettes inclut des compteurs pour chaque résultat de facette.

     ![][8]
 
5.  Cliquez sur une couleur, une catégorie et une gamme de prix. Les facettes sont null sur une recherche initiale, mais lorsqu’ils prennent des valeurs, les résultats de recherche sont retirés des éléments qui ne correspondent plus. Notez que l’URI choisit les modifications à votre requête.

    ![][9]
 
6.  Pour effacer la requête à facettes de sorte que vous pouvez essayer de comportements de requête différente, cliquez sur **Catalogue AdventureWorks** en haut de la page.

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>Étape suivante

Pour tester vos connaissances, vous pouvez ajouter un champ de facette *modelName*. L’index est déjà configuré pour cette facette, donc aucune modification de l’index n’est nécessaires. Mais vous devrez modifier le code HTML pour inclure une nouvelle facette pour les modèles et ajouter le champ de facette pour le constructeur de la requête.

Pour plus d’informations sur les principes de conception pour la navigation à facettes, nous vous recommandons les liens suivants :

- [Conception de la recherche à facettes](http://www.uie.com/articles/faceted_search/)
- [Motifs de conception : Navigation à facettes](http://alistapart.com/article/design-patterns-faceted-navigation)

Vous pouvez également visionner [Approfondie de recherche Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). En 45:25, vous trouverez une démonstration sur la façon d’implémenter les facettes.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 