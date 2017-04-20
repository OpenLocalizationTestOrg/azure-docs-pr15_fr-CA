<properties
    pageTitle="Créer un index de recherche d’Azure via le portail Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Créer un index en utilisant le portail Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Créer un index de recherche d’Azure via le portail Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)

Cet article vous guidera tout au long du processus de création d’une recherche d’Azure [index](search-what-is-an-index.md) via le portail d’Azure.

Avant de suivre ce guide et création d’un index, vous devez avoir déjà [créé un service de recherche d’Azure](search-create-service-portal.md).


## <a name="i-go-to-your-azure-search-blade"></a>I. Accédez à la blade de recherche d’Azure
1. Cliquez sur « Toutes les ressources » dans le menu sur le côté gauche du [Portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Sélectionnez votre service de recherche d’Azure

## <a name="ii-add-and-name-your-index"></a>II. Ajoutez et nommez votre index.
1. Cliquez sur le bouton « Ajouter index »
2. Nommez votre index de recherche d’Azure. Étant donné que nous allons créer un index pour rechercher des hôtels dans ce guide, nous avons nommé notre index « hôtels ».
  * Le nom d’index doit commencer par une lettre et contenir uniquement des lettres minuscules, des chiffres ou des tirets («- »).
  * Comme pour le nom de votre service, le nom d’index que vous choisissez sera également partie de l’URL du point de terminaison où vous envoyez vos demandes HTTP pour l’API de recherche Azure
3. Cliquez sur l’entrée « Champs » pour ouvrir une nouvelle lame

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Créer et définir les champs de l’index
1. En sélectionnant l’entrée « Champs », une nouvelle lame ouvre un formulaire pour saisir votre définition d’index.
2. Ajouter des champs à votre index à l’aide de l’écran.

  * Un champ de *clé* de type Edm.String est obligatoire pour chaque index de recherche d’Azure. Ce champ de clé est créé par défaut avec le champ nom « id ». Nous avons modifié il à « hotelId » dans notre index.
  * Certaines propriétés de votre schéma d’index ne peuvent être définies qu’une seule fois et ne peut pas être mis à jour à l’avenir. De ce fait, les mises à jour de schéma qui nécessiteraient la réindexation telles que la modification des types de champs ne sont pas actuellement possibles après la configuration initiale.
  * Nous avons choisi soigneusement les valeurs de propriété de chaque champ basé sur la manière dont nous ils seront utilisés dans une application. Gardez votre recherche utilisateur expérience et des besoins à l’esprit lorsque vous concevez votre index comme les [propriétés appropriées](https://msdn.microsoft.com/library/azure/dn798941.aspx)doit être affecté à chaque champ. Ces contrôles de propriétés, qui recherche les fonctionnalités (filtrage, faceting, tri, recherche de texte intégral, etc.) s’appliquent à des champs. Par exemple, il est probable que les personnes recherchant des hôtels sera intéressés par les correspondances de mots-clés dans le champ « description », afin de nous permettre la recherche de texte intégral de ce champ en définissant la propriété « Possibilité de recherche ».
    * Vous pouvez également définir l' [Analyseur de langage](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) pour chaque champ en cliquant sur l’onglet « Analyseur » en haut de la lame. Vous pouvez le voir ci-dessous que nous avons sélectionné un analyseur Français pour un champ dans notre index destiné au texte Français.

3. Cliquez sur **OK** sur la lame de « Champs » pour confirmer vos définitions de champ
4. Cliquez sur **OK** sur la lame « Index Ajouter » pour enregistrer et créer l’index que vous venez de définir.

Dans les captures d’écran ci-dessous, vous pouvez voir comment nous avons nommé et défini les champs pour notre index « hôtels ».

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Suivant
Création d’un index de recherche d’Azure, vous serez prêt à [charger votre contenu dans l’index](search-what-is-data-import.md) afin que vous pouvez lancer la recherche de vos données.
