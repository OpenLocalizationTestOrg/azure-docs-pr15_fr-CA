<properties
   pageTitle="Créer un index des documents dans plusieurs langues dans Azure recherche | Microsoft Azure | Service de recherche de nuage hébergé"
   description=" Recherche Azure prend en charge les 56 langues, exploitant des analyseurs de langue à partir de la technologie Lucene et traitement de langage naturel à partir de Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Créer un index des documents dans plusieurs langues dans Azure recherche
> [AZURE.SELECTOR]
- [Portail](search-language-support.md)
- [RESTE](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Décupler les performances des analyseurs de langue est aussi simple que la propriété d’un paramètre sur un champ de recherche dans la définition d’index. Maintenant, vous pouvez effectuer cette étape dans le portail.

Voici les captures d’écran de lames Azure Portal pour recherche Azure qui permettent aux utilisateurs de définir un schéma d’index. À partir de cette carte, les utilisateurs peuvent créer tous les champs et définir la propriété de l’analyseur pour chacun d’eux.

> [AZURE.IMPORTANT] Vous ne pouvez définir un analyseur de langage lors de la définition de champ, comme dans lors de la création d’un index à partir du sol des, ou lors de l’ajout d’un nouveau champ à un index existant. Assurez-vous que vous spécifiez entièrement tous les attributs, y compris l’analyseur, lors de la création du champ. Il se peut que vous ne pourrez pas modifier les attributs ou le type d’analyseur une fois que vous enregistrez vos modifications.

## <a name="define-a-new-field-definition"></a>Définir une autre définition de champ

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et ouvrir la lame de service de votre service de recherche.
2. Cliquez sur **index d’ajouter** dans la barre de commande en haut du tableau de bord de service pour démarrer un nouvel index, ou ouvrez un index existant pour définir un analyseur sur des champs que vous ajoutez à un index existant.
3. La lame de champs s’affiche, vous propose des options permettant de définir le schéma de l’index, y compris l’onglet Analyseur utilisé pour le choix d’un analyseur de langage.
4. Dans les champs, démarrez une définition de champ en fournissant un nom, en choisissant le type de données et définition des attributs pour marquer le champ en tant que texte intégral consultables, récupérables dans les résultats de la recherche, utilisables dans des structures de navigation facette, pouvant être triée et ainsi de suite. 
5. Avant de passer au champ suivant, ouvrez l’onglet **Analyseur** . 

   
![][1]
*Pour sélectionner un analyseur, cliquez sur l’onglet Analyseur de sur la lame de champs*

## <a name="choose-an-analyzer"></a>Choisissez un analyseur

6. Faites défiler la liste pour rechercher le champ que vous définissez. 
7. Si vous n’avez pas coché le champ comme la recherche, cliquez sur la case à cocher maintenant pour marquer comme **possibilité de recherche**.
8. Cliquez sur la zone Analyzer pour afficher la liste des analyseurs disponibles.
9. Choisissez l’analyseur que vous souhaitez utiliser.

![][2]
*Sélectionnez un des analyseurs pris en charge pour chaque champ.*

Par défaut, tous les champs de recherche utilisent l' [Analyseur de Lucene Standard](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) qui est indépendant de la langue. Pour afficher la liste complète des analyseurs pris en charge, consultez [Prise en charge linguistique dans Azure recherche](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Une fois que l’Analyseur de langage est sélectionné pour un champ, il sera utilisé avec chaque demande de recherche et d’indexation pour ce champ. Lorsqu’une requête est adressée à plusieurs champs, à l’aide de différents analyseurs, la requête sera traitée indépendamment par les analyseurs de droite pour chaque champ.

De nombreuses applications web et mobiles servent les utilisateurs dans le monde entier à l’aide de différentes langues. Il est possible de définir un index pour un scénario à ceci en créant un champ pour chaque langue prise en charge.

![][3]
*Définition de l’index avec un champ de description pour chaque langue prise en charge*

Si la langue de l’agent d’émission d’une requête est appelée, une demande de recherche peut être configurée pour un champ spécifique en utilisant le paramètre de requête **searchFields** . La requête suivante sera émise par rapport à la description en polonais :

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Vous pouvez interroger l’index à partir du portail, à l’aide de **l’Explorateur recherche** pour coller dans une requête similaire à celle présentée ci-dessus. Explorateur de recherche est disponible dans la barre de commande de la lame de service. Pour plus d’informations, reportez-vous à la section [requête votre index de recherche d’Azure dans le portail](search-explorer.md) .

Parfois la langue de l’agent d’émission d’une requête ne connaît pas, auquel cas la requête peut être exécutée simultanément sur tous les champs. Si nécessaire, préférence pour les résultats dans une langue donnée peut être défini à l’aide de la [notation de profils](https://msdn.microsoft.com/library/azure/dn798928.aspx). Dans l’exemple ci-dessous, correspondances trouvées dans la description en anglais auront un score supérieur par rapport aux correspondances en polonais et en Français :

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Si vous êtes un développeur .NET, notez que vous pouvez configurer à l’aide du [Kit de développement .NET Azure recherche](http://www.nuget.org/packages/Microsoft.Azure.Search)des analyseurs de langage. La dernière version prend en charge aussi bien les analyseurs de langage Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
