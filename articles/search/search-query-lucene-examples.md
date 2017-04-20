<properties
    pageTitle="Exemples de requête Lucene pour Azure recherche | Recherche de Microsoft Azure"
    description="Syntaxe de requête Lucene de recherche floue, recherche de proximité, terme dynamiser, recherche d’expression régulière et caractères génériques."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exemples de syntaxe de requête Lucene pour la création de requêtes de recherche d’Azure

Lors de la création de requêtes de recherche d’Azure, vous pouvez utiliser par défaut la [syntaxe de requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou l' autre [Analyseur de requête Lucene dans Azure recherche](https://msdn.microsoft.com/library/azure/mt589323.aspx). L’Analyseur de requête Lucene prend en charge les constructions de requête plus complexes, telles que les requêtes de portée d’un champ recherche floue, recherche de proximité, terme dynamiser et recherche d’expression reqular.

Dans cet article, vous pouvez parcourir les exemples qui affichent Lucene une syntaxe de requête et les résultats côte à côte. Exemples de s’exécuter sur un index de recherche prédéfini dans [JSFiddle](https://jsfiddle.net/), un éditeur de code en ligne pour tester les scripts et HTML. 

Avec le bouton droit sur les requête exemples d’URL pour ouvrir JSFiddle dans une fenêtre distincte du navigateur.

> [AZURE.NOTE] Les exemples suivants utilisent un index de recherche composé de travaux disponibles, basés sur un dataset fourni par l’initiative de la [Ville de New York OpenData](https://nycopendata.socrata.com/) . Ces données ne doivent pas être considérées en cours ou terminé. L’index est sur un service de bac à sable fourni par Microsoft. Vous n’avez pas besoin d’un abonnement Azure ou la recherche d’Azure pour essayer ces requêtes.

## <a name="viewing-the-examples-in-this-article"></a>Consultez les exemples dans cet article

Tous les exemples dans cet article permet de spécifier l’Analyseur de requête Lucene via le paramètre de recherche**queryType** . Lorsque vous utilisez l’Analyseur de requête Lucene à partir de votre code, vous indiquerez le **queryType** à chaque demande.  Les valeurs valides incluent **simple**|**complet**, de **simples** comme par défaut et **complète** de l’Analyseur de requête Lucene. Pour plus d’informations sur la spécification des paramètres de requête, consultez [Rechercher des Documents (API REST Service Azure recherche)](https://msdn.microsoft.com/library/azure/dn798927.aspx) .

**Exemple 1** – clic droit la requête suivante extrait pour l’ouvrir dans une nouvelle page de navigateur qui se charge de JSFiddle et exécute la requête :
- [& queryType = complet & recherche = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Cette requête renvoie les documents de notre index de travaux (chargé sur un service de bac à sable)

Dans la nouvelle fenêtre de navigateur, vous verrez le JavaScript source et la sortie HTML côte à côte. Le script fait référence à une requête, qui est fournie par l’URL de l’exemple dans cet article. Par exemple, dans l' **exemple 1**, la requête sous-jacente est la suivante :

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Notez la requête utilise un index de recherche d’Azure préconfiguré appelé nycjobs. Le paramètre **searchFields** limite la recherche au champ de titre d’entreprise. La **valeur de constante xlTextImport** est défini sur **complet**, qui indique à la recherche d’Azure à utiliser l’Analyseur de requête Lucene pour cette requête.

### <a name="fielded-query-operation"></a>Opération de requête STOCKEES

Vous pouvez modifier les exemples de cet article en spécifiant une construction **fieldname:searchterm** pour définir une opération de requête STOCKEES, où le champ est un mot unique, et le terme de recherche est également un mot ou une expression, éventuellement avec des opérateurs booléens. Certains exemples sont les suivants :

- business_title :(senior NOT junior)
- état : (« New York » et « Nouveau Jersey »)

Assurez-vous de mettre plusieurs chaînes entre guillemets si vous souhaitez que les deux chaînes à être évalué comme une entité unique, comme dans ce cas, recherchez les deux villes distinctes dans le champ emplacement. Assurez-vous également que l’opérateur est mis en majuscule comme vous voir avec NOT et and.

Le champ spécifié dans **fieldname:searchterm** doit être un champ de recherche. Pour plus d’informations sur l’utilisation des attributs d’index dans les définitions de champ, voir [Create Index (API REST Service Azure recherche)](https://msdn.microsoft.com/library/azure/dn798941.aspx) .

## <a name="fuzzy-search"></a>Recherche partielle

Une recherche floue recherche des correspondances dans des conditions qui ont une structure similaire. Par [Lucene documentation](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), recherches floues sont basés sur la [Distance de Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Pour faire une recherche partielle, utilisez le tilde « ~ » symbole à la fin d’un mot unique avec un paramètre optionnel, une valeur comprise entre 0 et 2, qui spécifie la distance de la modifier. Par exemple, « bleu ~ » ou « bleu ~ 1" retournerait bleu, bleu et le collage.

**Exemple 2** : droit l’extrait de requête suivant pour lui donner un bloc try. Cette requête recherche les livres de gestion avec le senior à terme dans les, mais pas adjoint :

- [& queryType = complet & recherche = business_title:senior non junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Recherche de proximité

Recherches de proximité sont utilisées pour rechercher des termes qui sont proches les uns des autres dans un document. Insérer un tilde « ~ » symbole à la fin d’une phrase suivie par le nombre de mots qui créent la limite de proximité. Par exemple, « aéroport hôtel » ~ 5 trouveront l’hôtel de conditions et l’aéroport dans 5 mots de l’autre dans un document.

**Exemple 3** : droit l’extrait de requête suivant. Cette requête recherche les projets de l’associer à terme (où il est mal orthographié) :

- [& queryType = complet & recherche = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Exemple 4** : avec le bouton droit la requête. Rechercher les travaux par le terme « analyste senior » où il est séparé par ne plus d’un mot :

- [& queryType = complet & recherche = business_title : « analyste senior » ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemple 5** : essayez à nouveau de supprimer les mots entre le terme « analyste senior ».

- [& queryType = complet & recherche = business_title : « analyste senior » ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Conditions d’amplification

AMPLIFICATION du terme fait référence à un document plus élevé si elle contient le terme amplifié par rapport à des documents qui ne contiennent pas le terme de classement. Cela diffère de notation des profils dans cette notation profils améliorer certains champs, au lieu des termes spécifiques. L’exemple suivant permet d’illustrer les différences.

Pensez à un profil d’évaluation qui augmente correspond à dans un champ particulier, par exemple le **genre** dans l’exemple musicstoreindex. AMPLIFICATION du terme peut servir à améliorer encore certain recherche conditions supérieures à d’autres personnes. Par exemple, « rock ^ 2 électronique » augmentera les documents qui contiennent les termes de recherche dans le champ de **genre** plus élevé que les autres champs de recherche dans l’index. En outre, les documents contenant le terme « rock » sont classés plus élevé que le terme de recherche « électronique », à la suite de la valeur de boost de terme (2).

Pour augmenter un terme, utilisez le signe, « ^ », symbole avec un facteur de boost (nombre) à la fin de l’expression que vous recherchez. Plus le facteur de boost, le mieux le terme sera par rapport aux autres termes de recherche. Par défaut, le facteur d’augmentation est 1. Bien que le facteur d’augmentation doit être positif, il peut être inférieur à 1 (par exemple, 0,2).

**Exemple 6** : avec le bouton droit la requête. Pour les projets avec le terme « analyste d’ordinateur » où nous voir aucun résultat avec l’ordinateur de mots et des analystes, mais les travaux d’analyse sont en haut des résultats de recherche.

- [& queryType = complet & recherche = business_title:computer analyste](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemple 7** : essayez de nouveau, cette augmentation de délai entraîne avec le terme ordinateur sur l’analyste terme si les deux mots n’existent pas.

- [& queryType = complet & recherche = business_title:computer ^ 2 analyste](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Expression régulière

Une recherche d’expression régulière trouve une correspondance basée sur le contenu entre barres obliques « / », comme documenté dans la [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemple 8** --cliquez du bouton droit la requête. Rechercher des tâches avec soit le terme Senior ou Junior.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

L’URL de cet exemple ne s’affiche pas correctement dans la page. Pour résoudre ce problème, copiez l’URL ci-dessous et collez-le dans l’adresse URL du navigateur :    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>Recherche par caractères génériques

Vous pouvez utiliser la syntaxe généralement reconnu pour plusieurs (\*) ou unique des recherches par caractères génériques caractères (?). Notez que l’Analyseur de requête Lucene prend en charge l’utilisation de ces symboles avec un seul terme et pas une expression.

**Exemple 9** --cliquez du bouton droit la requête. Rechercher des tâches qui contiennent le préfixe « prog », qui contiendra des livres de gestion avec les termes de programmation et le programmeur qu’il contient.

- [& queryType = intégral & $select = business_title & recherche = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Vous ne pouvez pas utiliser un * ou ? le premier caractère d’une recherche de symbole.


## <a name="next-steps"></a>Étapes suivantes

Essayez avec l’Analyseur de requête Lucene dans votre code. Les liens suivants expliquent comment configurer les requêtes de recherche pour .NET et l’API REST. Les liens utilisent la syntaxe simple par défaut, vous devez appliquer ce que vous avez appris dans cet article permet de spécifier la **valeur de constante xlTextImport**.

- [Interroger votre Index de recherche Azure à l’aide du Kit de développement .NET](search-query-dotnet.md)
- [Interroger votre Index de recherche Azure à l’aide de l’API REST](search-query-rest-api.md)


 