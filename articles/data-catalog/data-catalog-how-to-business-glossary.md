<properties
    pageTitle="Comment paramétrer le glossaire d’entreprise régi balisage | Microsoft Azure"
    description="Article d’apprentissage mettant en surbrillance le glossaire d’entreprise dans le catalogue de données Azure pour définir et utiliser un vocabulaire commun pour les professionnels à balise enregistré ressources de données."
    services="data-catalog"
    documentationCenter=""
    authors="steelanddata"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/21/2016"
    ms.author="maroche"/>

# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Comment configurer le glossaire d’entreprise pour le marquage de régie

## <a name="introduction"></a>Introduction

Catalogue de données Azure fournit des fonctionnalités pour la découverte de source de données, permettant aux utilisateurs de facilement découvrir et comprendre les sources de données que nécessaires pour effectuer une analyse et prendre des décisions. Ces fonctions de découverte avoir un impact plus grand lorsque les utilisateurs peuvent rechercher et comprendre la large gamme de sources de données disponibles.

Le balisage est une fonctionnalité de catalogue de données afin de promouvoir une meilleure compréhension des données de ressources. Balisage permet aux utilisateurs d’associer des mots-clés à un actif ou d’une colonne, qui à son tour permet de découvrir l’actif via la recherche, et permet aux utilisateurs de mieux comprendre le contexte et l’intention de l’actif.

Toutefois, le balisage peut parfois causer des problèmes qui lui sont propres. Voici quelques exemples de problèmes qui peuvent être introduites par le balisage sont :

1.  Utilisateurs à l’aide des abréviations sur des actifs et du texte de développé sur d’autres lors de l’étiquetage. Cette incohérence entrave la découverte des actifs même si l’objectif était de baliser les éléments avec la même balise.
2.  Balises qui ont des significations différentes dans des contextes différents. Par exemple, une balise appelée « Revenus » sur un ensemble de données client peut signifier que les recettes par client, mais la même balise sur un groupe de données de vente trimestriel peut signifier le chiffre d’affaires trimestriel de la société.  

Pour vous aider à résoudre des problèmes similaires, catalogue de données comprend un glossaire de l’entreprise.

Le glossaire de Business de catalogue de données permet aux entreprises de termes commerciaux clés de document et de leurs définitions pour créer un vocabulaire commun pour les professionnels. Cette gouvernance permet une cohérence dans l’utilisation des données dans toute l’organisation. Une fois les termes sont définis dans le glossaire de l’entreprise, pouvoir être affectées aux ressources de données dans le catalogue, à l’aide de la même approche balisage, permettant ainsi _régie de balisage_.

> [AZURE.NOTE] Les fonctionnalités décrites dans cet article sont uniquement disponibles dans l’Édition Standard de Azure données catalogue. L’édition gratuite ne fournit pas de fonctionnalités de balisage régi ou un glossaire d’entreprise.

## <a name="glossary-availability-and-privileges"></a>Privilèges et disponibilité du glossaire

*Le glossaire d’entreprise est disponible dans l’Édition Standard de Azure données catalogue. L’édition du catalogue de données libre n’inclut pas un glossaire.*

Le glossaire de l’entreprise sont accessibles via l’option « Glossaire » dans le menu de navigation du portail de catalogue de données.  

![Accéder au glossaire de l’entreprise](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)


Les administrateurs de catalogue de données et les membres du rôle Administrateurs de glossaire peuvent créer, modifier et supprimer des termes du glossaire dans le glossaire de l’entreprise. Tous les utilisateurs du catalogue de données peuvent afficher les définitions des termes et peuvent baliser des ressources avec des termes du glossaire.

![Ajout d’un nouveau terme de glossaire](./media/data-catalog-how-to-business-glossary/02-new-term.png)


## <a name="creating-glossary-terms"></a>Création des termes du glossaire

Les administrateurs de données catalogue et glossaire peut créer nouveaux termes du glossaire en cliquant sur le nouveau terme » bouton pour créer des termes du glossaire avec les champs suivants :

* Une définition d’entreprise pour le terme
* Une description qui capture la destination ou règles d’entreprise pour l’immobilisation ou de la colonne
* Une liste des parties prenantes qui connaissent le plus sur le terme
* Le terme parent, qui définit la hiérarchie d’organisation dans lequel le terme


## <a name="glossary-term-hierarchies"></a>Hiérarchies de terme de glossaire

Le glossaire de professionnels du catalogue de données permet de décrire votre vocabulaire d’entreprise en tant que hiérarchie de termes. Cela permet aux organisations de créer une classification des termes qui représente mieux la taxonomie de leur entreprise.

Le nom d’un terme doit être unique à un niveau donné de la hiérarchie, les noms en double ne sont pas autorisés. Il n’y a aucune limite au nombre de niveaux dans une hiérarchie, mais une hiérarchie est souvent plus faciles à comprendre lorsqu’il y a trois niveaux ou moins.

L’utilisation de hiérarchies dans le glossaire d’entreprise est facultative. En laissant le parent terme champ vide pour les termes du glossaire pour créer une liste de plat (non hiérarchique) des termes dans le glossaire.  

## <a name="tagging-assets-with-glossary-terms"></a>Étiquetage des actifs avec les termes du glossaire

Une fois les termes du glossaire ont été définies dans le catalogue, l’expérience de l’étiquetage des actifs est optimisé pour rechercher le glossaire que l’utilisateur tape leur balise. Le portail du catalogue de données affiche une liste de correspondance pour l’utilisateur de choisir à partir de termes du glossaire. Si l’utilisateur sélectionne un terme dans la liste, qu'il est ajouté à l’actif en tant que balise (également appelé balise de glossaire). L’utilisateur peut également choisir de créer une nouvelle balise en tapant un terme (ou n’est pas dans le glossaire balise de l’utilisateur).

![Actifs de données balisées avec la balise d’un utilisateur et les deux balises glossaire](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [AZURE.NOTE] Balises d’utilisateur sont le seul type de balise pris en charge dans l’édition du catalogue de données libre.

### <a name="hover-behavior-on-tags"></a>Comportement de pointage sur les balises
Dans le portail du catalogue de données, les deux types de balises sont visuellement distinctes, avec les comportements de pointeur différent. Lorsque l’utilisateur pointe sur une balise utilisateur, ils peuvent voir le texte de balise et l’utilisateur ou les utilisateurs ont ajoutés à la balise. Lorsque l’utilisateur pointe sur une balise du glossaire, ils voient également la définition de la terme de glossaire et un lien pour ouvrir le glossaire de l’entreprise pour afficher la définition complète du terme.

### <a name="search-filters-for-tags"></a>Filtres de recherche pour les balises
Les balises de glossaire et les balises de l’utilisateur sont consultables et peuvent être utilisés comme filtres dans une recherche.

## <a name="summary"></a>Résumé
Le glossaire d’entreprise dans le catalogue de données Azure et le balisage régi elle permet, autoriser des actifs de données identifié, la gestion et la découverte de manière cohérente. Le glossaire de l’entreprise peut favoriser l’apprentissage du vocabulaire d’entreprise entre les utilisateurs d’une organisation et prend en charge les meta-données significatives à capturer, effectue le suivi des actifs et la compréhension d’un jeu d’enfant.

## <a name="see-also"></a>Voir aussi

- [Documentation de l’API REST de glossaire d’activité](https://msdn.microsoft.com/library/mt708855.aspx)
