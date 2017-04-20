<properties
   pageTitle="Interagir avec les rapports à l’aide de l’API JavaScript | Microsoft Azure"
   description="Puissance incorporées de BI, interagir avec les rapports à l’aide de l’API JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagir avec les rapports BI d’alimentation à l’aide de l’API JavaScript

L’API JavaScript de BI d’alimentation vous permet d’incorporer facilement des rapports BI de puissance dans vos applications. Avec l’API, vos applications peuvent interagir par programmation avec les différents éléments de rapport comme des pages et des filtres. Cette interactivité rend des rapports BI d’alimentation une partie plus intégrée de votre application.

Vous incorporez un état d’alimentation BI dans votre application à l’aide d’un iframe qui est hébergé dans le cadre de l’application. L’iframe agit comme un bouclier entre votre application et de l’état, comme vous pouvez le voir dans l’image suivante. 

![Iframe incorporée de BI de puissance sans API Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

L’iframe rend le processus d’incorporation beaucoup plus facile, mais sans l’API JavaScript du rapport et votre application ne peut pas interagir avec eux. Ce manque d’interaction peut rendre le sentiment le rapport n’est pas réellement partie de l’application. Le rapport et l’application vraiment besoin de communiquer en arrière, comme dans l’image suivante.

![Iframe incorporée de BI de puissance avec l’API Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

L’API JavaScript de BI d’alimentation vous permet d’écrire du code qui peut transmettre de façon sécurisée à travers la limite d’iframe. Cela permet à votre application pour exécuter par programmation une action dans un rapport et d’écouter des événements à partir des actions par les utilisateurs dans le rapport.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Que pouvez-vous faire avec l’API JavaScript de BI d’alimentation ?
Avec l’API JavaScript, vous pouvez gérer des rapports, naviguer vers les pages d’un rapport, filtrer un rapport et gérer l’incorporation des événements. Le diagramme suivant illustre la structure de l’API.

![Diagramme de BI JavaScript API d’alimentation](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>Gérer les rapports
L’API Javascript permet de gérer le comportement au niveau de la page et le rapport :

- Incorporer un rapport d’analyse Décisionnelle d’alimentation spécifique d’en toute sécurité dans votre application, essayez les [incorporer l’application de démonstration](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Jeton d’accès de jeu
- Configurer le rapport
  - Activer et désactiver le volet de filtre et le volet de navigation de page, essayez du [mettre à jour les paramètres application de démonstration](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Définir les paramètres par défaut pour les pages et les filtres - essayez la [démonstration des valeurs par défaut de la valeur](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Permet d’entrer et de quitter le mode plein écran

[En savoir plus sur l’incorporation d’un rapport](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>Naviguer vers les pages d’un rapport
Enbales de l’API JavaScript vous permet de découvrir toutes les pages dans un rapport et pour définir la page en cours. Essayez de l' [application de démonstration de navigation](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[En savoir plus sur la navigation entre les pages](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrer un rapport
L’API JavaScript fournit la base et avancées de filtrage des fonctionnalités pour les rapports intégrés et les pages du rapport. Essayez le [filtrage d’application de démonstration](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)et examinez le code introduction ici.  


#### <a name="basic-filters"></a>Filtres de base
Un filtre de base est placé sur un niveau de hiérarchie ou de la colonne et contient une liste de valeurs à inclure ou à exclure.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Filtres avancés
Les filtres avancés utilisent l’opérateur logique et ou ou et accepter une ou deux conditions, chacune avec leur propre opérateur et une valeur. Conditions prises en charge sont les suivants :

- Aucun
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- Contient
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Est
- IsNot
- ESTVIDE
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Pour en savoir plus sur le filtrage](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>Gestion des événements
En plus d’envoyer des informations dans l’iframe, votre application peut également recevoir des informations sur les événements suivants en provenance de l’iframe :

- Incorporer
  - chargé
  - erreur
- Rapports
  - pageChanged
  - dataSelected (disponible prochainement)

[En savoir plus sur la gestion des événements](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’API JavaScript de BI d’alimentation, consultez les liens suivants :

- [Wiki d’API JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Référence du modèle objet](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Exemples
  - [Angulaire](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Démo en direct](https://microsoft.github.io/PowerBI-JavaScript/demo/)
