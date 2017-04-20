<properties
    pageTitle="Journal du Concepteur de vue Analytique | Microsoft Azure"
    description="Concepteur de vues dans le journal Analytique vous permet de créer des affichages personnalisés dans la console de l’OMS qui contiennent différentes visualisations de données dans le référentiel de l’OMS. Cet article fournit une référence des paramètres de chacune des parties de visualisation disponibles à utiliser dans vos vues personnalisées."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-visualization-part-reference"></a>Référence de partie visualisation journal Concepteur de vue Analytique
Le Concepteur de vue dans le journal Analytique vous permet de créer des affichages personnalisés dans la console de l’OMS qui contiennent différentes visualisations de données à partir du référentiel de l’OMS. Cet article fournit une référence des paramètres de chacune des parties de visualisation disponibles à utiliser dans vos vues personnalisées.

Autres articles disponibles pour le Concepteur de vue sont :

- [Concepteur de vue](log-analytics-view-designer.md) - vue d’ensemble du Concepteur de vues et des procédures de création et de modification des affichages personnalisés.
- [Référence de mosaïque](log-analytics-view-designer-tiles.md) - référence des paramètres pour chacun des mosaïques disponibles à utiliser dans vos vues personnalisées. 

Le tableau suivant décrit les différents types de mosaïques disponibles dans le Concepteur de vue.  Les sections suivantes décrivent chaque type de mosaïque en détail et leurs propriétés.

| Type d’affichage | Description |
|:--|:--|
| [Liste des requêtes](#list-of-queries-part) | Affiche la liste des requêtes de recherche de journal.  L’utilisateur peut cliquer sur chaque requête pour afficher ses résultats.  |
| [Numéro de & liste](#number-amp-list-part) | L’en-tête a un seul numéro avec indication du nombre d’enregistrements à partir d’une requête de recherche de journal.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps. |
| [Deux numéros de & liste](#two-numbers-amp-list-part) | L’en-tête a deux chiffres indiquant le nombre d’enregistrements à partir de requêtes de recherche de journal distinct.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps. |
| [& Liste de bouée](#donut-amp-list-part) | En-tête affiche un numéro unique résumé à partir d’une colonne de valeur dans une requête de journal.  L’anneau affiche graphiquement les résultats des trois premiers enregistrements. |
| [& Liste des deux barres de planning](#two-timelines-amp-list-part) | En-tête affiche les résultats de deux requêtes de journal dans le temps sous forme de graphiques de la colonne avec une légende affichant un nombre unique résumé à partir d’une colonne de valeur dans une requête de journal.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps. |   
| [Informations](#information-part) | En-tête affiche le texte statique et un lien facultatif.  Liste affiche un ou plusieurs éléments avec le titre et le texte statique. |
| [Graphique en courbes, légende & liste](#line-chart-callout-amp-list-part) | En-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps et une légende avec une valeur de synthèse.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps. |
| [Liste & courbes](#line-chart-amp-list-part) | En-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journaux au fil du temps.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps. |
| [Pile de partie de graphiques de ligne](#stack-of-line-charts-part) | Affiche les trois graphiques de ligne distincte avec plusieurs séries à partir d’une requête de journaux au fil du temps. |


## <a name="list-of-queries-part"></a>Liste de la partie de requêtes

Affiche la liste des requêtes de recherche de journal.  L’utilisateur peut cliquer sur chaque requête pour afficher ses résultats.  L’affichage comprendra une seule requête par défaut, et vous pouvez cliquer sur **+ requête** pour ajouter des requêtes supplémentaires.

![Liste d’affichage des requêtes](media/log-analytics-view-designer/view-list-queries.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre | Texte à afficher en haut de la vue. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Les filtres | Liste de propriétés à inclure dans le volet gauche de filtre lorsque l’utilisateur sélectionne une requête délimitée par des virgules. |
| Mode de rendu | Vue initiale affichée lorsque la requête est activée.  L’utilisateur peut sélectionner toutes les vues disponibles après l’ouverture de la requête. |
| **Requêtes** |
| Requête de recherche | Requête à exécuter. |
| Nom convivial | Nom descriptif de la requête à afficher à l’utilisateur. |


## <a name="number--list-part"></a>Partie de nombre et de la liste

L’en-tête a un seul numéro avec indication du nombre d’enregistrements à partir d’une requête de recherche de journal.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps.


![Liste d’affichage des requêtes](media/log-analytics-view-designer/view-number-list.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher en haut de la vue. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Icône | Fichier de l’image à afficher à côté du résultat dans l’en-tête.
| Utilisez l’icône | Sélectionnez cette option pour que l’affichage de l’icône. |
| **Titre** |
| Légende | Texte à afficher en haut de l’en-tête. |
| Requête | Requête à exécuter pour l’en-tête.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| **Liste** |
| Requête | Requête à exécuter pour la liste.  Les deux premières propriétés pour les dix premiers enregistrements dans les résultats seront afficheront.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Les barres sont automatiquement créées en fonction de la valeur relative de la colonne numérique.<br><br>Utilisez la commande de tri dans la requête pour trier les enregistrements dans la liste.  L’utilisateur peut cliquer sur voir tous les pour exécuter la requête et renvoyer tous les enregistrements. |
| Masquer le graphique | Sélectionnez cette option pour désactiver le graphique vers la droite de la colonne numérique. |
| Activer les graphiques sparkline | Sélectionnez cette option pour afficher les graphiques sparkline au lieu de la barre horizontale.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Couleur | Couleur des barres ou des graphiques sparkline. |
| Nom de & séparateur de valeur | Délimiteur de caractère unique si vous souhaitez analyser la propriété text en plusieurs valeurs.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#name-value-separator) . |
| Requête de navigation | Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#navigation-query) . |
| **Liste** | **> Les titres de colonne** |
| Nom | Texte à afficher en haut de la première colonne de la liste. |
| Valeur | Texte à afficher en haut de la deuxième colonne de la liste. |
| **Liste** | **> Seuils de** |
| Activer les seuils | Sélectionnez cette option pour activer les seuils.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#thresholds) . |


## <a name="two-numbers--list-part"></a>Deux nombres et la partie de la liste

L’en-tête a deux chiffres indiquant le nombre d’enregistrements à partir de requêtes de recherche de journal distinct.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps.

![Afficher les deux numéros de & liste](media/log-analytics-view-designer/view-two-numbers-list.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher en haut de la vue. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Icône | Fichier de l’image à afficher à côté du résultat dans l’en-tête.
| Utilisez l’icône | Sélectionnez cette option pour que l’affichage de l’icône. |
| **Titre** |
| Légende | Texte à afficher en haut de l’en-tête. |
| Requête | Requête à exécuter pour l’en-tête.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| **Liste** |
| Requête | Requête à exécuter pour la liste.  Les deux premières propriétés pour les dix premiers enregistrements dans les résultats seront afficheront.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Les barres sont automatiquement créées en fonction de la valeur relative de la colonne numérique.<br><br>Utilisez la commande de tri dans la requête pour trier les enregistrements dans la liste.  L’utilisateur peut cliquer sur voir tous les pour exécuter la requête et renvoyer tous les enregistrements. |
| Masquer le graphique | Sélectionnez cette option pour désactiver le graphique vers la droite de la colonne numérique. |
| Activer les graphiques sparkline | Sélectionnez cette option pour afficher les graphiques sparkline au lieu de la barre horizontale.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Couleur | Couleur des barres ou des graphiques sparkline. |
| Opération | Opération à effectuer pour le graphique sparkline.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Nom de & séparateur de valeur | Délimiteur de caractère unique si vous souhaitez analyser la propriété text en plusieurs valeurs.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#name-value-separator) . |
| Requête de navigation | Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#navigation-query) . |
| **Liste** | **> Les titres de colonne** |
| Nom | Texte à afficher en haut de la première colonne de la liste. |
| Valeur | Texte à afficher en haut de la deuxième colonne de la liste. |
| **Liste** | **> Seuils de** |
| Activer les seuils | Sélectionnez cette option pour activer les seuils.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#thresholds) . |

## <a name="donut--list-part"></a>Partie de bouée & liste

En-tête affiche un numéro unique résumé à partir d’une colonne de valeur dans une requête de journal.  L’anneau affiche graphiquement les résultats des trois premiers enregistrements.

![Mode & liste de bouée](media/log-analytics-view-designer/view-donut-list.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher dans la partie supérieure de la mosaïque. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Icône | Fichier de l’image à afficher à côté du résultat dans l’en-tête. |
| Utilisez l’icône | Sélectionnez cette option pour que l’affichage de l’icône. |
| **En-tête** |
| Titre | Texte à afficher en haut de l’en-tête.
| Sous-titre | Texte à afficher sous le titre en haut de l’en-tête.
| **Bouée** |
| Requête | Requête à exécuter pour l’anneau.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique. |
| **Bouée** |  **> Centre de** |
| Texte | Texte à afficher sous la valeur à l’intérieur de l’anneau. |
| Opération | L’opération à effectuer sur la propriété value pour résumer à une seule valeur.<br><br>-Sum : Ajoutez les valeurs de tous les enregistrements.<br>-Pourcentage : Le pourcentage d’enregistrements renvoyés par les valeurs dans les **valeurs de résultat utilisés dans l’opération de centre** dans le nombre total d’enregistrements dans la requête. |
| Valeurs de résultat utilisés dans l’opération de centre | Éventuellement, cliquez sur le signe plus pour ajouter une ou plusieurs valeurs.  Les résultats de la requête sera limitées à des enregistrements avec les valeurs de propriété que vous spécifiez.  Si aucune valeur n’est ajouté, tous les enregistrements sont inclus dans la requête. |
| **Options supplémentaires** | **> Couleurs** |
| Couleur 1<br>Couleur 2<br>Couleur 3 | Sélectionnez la couleur de chacun des valeurs affichées dans l’anneau. |
| **Options supplémentaires** | **> Mappage de couleur avancé** |
| Valeur de champ | Tapez le nom d’un champ à afficher dans une couleur différente s’il est inclus dans l’anneau. |
| Couleur | Sélectionnez la couleur pour le champ unique. |
| **Liste** |
| Requête | Requête à exécuter pour la liste.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| Masquer le graphique | Sélectionnez cette option pour désactiver le graphique vers la droite de la colonne numérique. |
| Activer les graphiques sparkline | Sélectionnez cette option pour afficher les graphiques sparkline au lieu de la barre horizontale.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Couleur | Couleur des barres ou des graphiques sparkline. |
| Opération | Opération à effectuer pour le graphique sparkline.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Nom de & séparateur de valeur | Délimiteur de caractère unique si vous souhaitez analyser la propriété text en plusieurs valeurs.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#name-value-separator) . |
| Requête de navigation | Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#navigation-query) . |
| **Liste** | **> Les titres de colonne** |
| Nom | Texte à afficher en haut de la première colonne de la liste. |
| Valeur | Texte à afficher en haut de la deuxième colonne de la liste. |
| **Liste** | **> Seuils de** |
| Activer les seuils | Sélectionnez cette option pour activer les seuils.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#thresholds) . |

## <a name="two-timelines--list-part"></a>Deux parties de & liste des chronologies

En-tête affiche les résultats de deux requêtes de journal dans le temps sous forme de graphiques de la colonne avec une légende affichant un nombre unique résumé à partir d’une colonne de valeur dans une requête de journal.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps.

![Afficher les deux barres de planning & liste](media/log-analytics-view-designer/view-two-timelines-list.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher dans la partie supérieure de la mosaïque. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Icône | Fichier de l’image à afficher à côté du résultat dans l’en-tête. |
| Utilisez l’icône | Sélectionnez cette option pour que l’affichage de l’icône. |
| **Tout d’abord le graphique<br>deuxième graphique** |
| Légende | Texte à afficher dans la légende de la première série. |
| Couleur | Couleur à utiliser pour les colonnes de la série. |
| Requête | Requête à exécuter pour la première série.  Le décompte du nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes du graphique. |
| Opération | L’opération à effectuer sur la propriété value pour résumer à une seule valeur de la légende.<br><br>-Somme : Somme de la valeur à partir de tous les enregistrements.<br>-Moyenne : Moyenne de la valeur à partir de tous les enregistrements.<br>-Dernier exemple : Valeur à partir de la dernière période incluse dans le graphique.<br>-Premier exemple : Valeur dans le premier intervalle incluse dans le graphique.<br>-Count : Nombre de tous les enregistrements renvoyés par la requête.|
| **Liste** |
| Requête | Requête à exécuter pour la liste.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| Masquer le graphique | Sélectionnez cette option pour désactiver le graphique vers la droite de la colonne numérique. |
| Activer les graphiques sparkline | Sélectionnez cette option pour afficher les graphiques sparkline au lieu de la barre horizontale.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Couleur | Couleur des barres ou des graphiques sparkline. |
| Opération | Opération à effectuer pour le graphique sparkline.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Requête de navigation | Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#navigation-query) . |
| **Liste** | **> Les titres de colonne** |
| Nom | Texte à afficher en haut de la première colonne de la liste. |
| Valeur | Texte à afficher en haut de la deuxième colonne de la liste. |
| **Liste** | **> Seuils de** |
| Activer les seuils | Sélectionnez cette option pour activer les seuils.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#thresholds) . |

## <a name="information-part"></a>Partie d’informations

En-tête affiche le texte statique et un lien facultatif.  Liste affiche un ou plusieurs éléments avec le titre et le texte statique.

![Affichage des informations](media/log-analytics-view-designer/view-information.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher dans la partie supérieure de la mosaïque. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Couleur | Couleur d’arrière-plan de l’en-tête. |
| **En-tête** |
| Image | Fichier de l’image à afficher dans l’en-tête. |
| Étiquette | Texte à afficher dans l’en-tête. |
| **En-tête** | **> Lien** |
| Étiquette | Texte du lien. |
| URL | URL pour le lien. |
| **Éléments d’information** |
| Titre | Texte à afficher pour le titre de chaque élément. |
| Contenu | Texte à afficher pour chaque élément. |


## <a name="line-chart-callout--list-part"></a>Graphique en courbes, légende et composant de liste

En-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps et une légende avec une valeur de synthèse.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps.

![Graphique en courbes, légende et affichage de la liste](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher dans la partie supérieure de la mosaïque. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Icône | Fichier de l’image à afficher à côté du résultat dans l’en-tête. |
| Utilisez l’icône | Sélectionnez cette option pour que l’affichage de l’icône. |
| **En-tête** |
| Titre | Texte à afficher en haut de l’en-tête. |
| Sous-titre | Texte à afficher sous le titre en haut de l’en-tête. |
| **Graphique en courbes** |
| Requête | Requête à exécuter pour le graphique en courbes.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé de **mesure** de synthétiser les résultats.  Si la requête utilise le mot-clé de **l’intervalle de** l’axe des abscisses du graphique utilisera cet intervalle de temps.  Si la requête n’inclut pas le mot-clé **intervalle** intervalles horaires sont utilisées pour l’axe x. |
| **Graphique en courbes** | **> Légende** |
| Titre de la légende | Texte à afficher au-dessus de la valeur de la légende. |
| Nom de la série | Valeur de la propriété de la série à utiliser pour la valeur de la légende.  Si aucune série n’est fourni, tous les enregistrements de la requête sont utilisés. |
| Opération | L’opération à effectuer sur la propriété value pour résumer à une seule valeur de la légende.<br><br>-Moyenne : Moyenne de la valeur à partir de tous les enregistrements.<br>-Nombre de tous les enregistrements renvoyés par la requête.<br>-Dernier exemple : Valeur à partir de la dernière période incluse dans le graphique.<br>-Max : Valeur maximale entre les intervalles incluses dans le graphique.<br>-Min : Valeur minimale entre les intervalles incluses dans le graphique.<br>-Somme : Somme de la valeur à partir de tous les enregistrements. |
| **Graphique en courbes** | **> Axe Y** |
| Utiliser une échelle logarithmique | Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe y. |
| Unités | Spécifiez les unités pour les valeurs renvoyées par la requête.  Ces informations sont utilisées pour afficher les étiquettes du graphique qui indique les types de valeur et, éventuellement, pour la conversion des valeurs.  Le Type d’unité spécifie la catégorie de l’unité et définit les valeurs de Type d’unité en cours qui sont disponibles.  Si vous sélectionnez une valeur dans convertir puis les valeurs numériques sont converties à partir du type d’unité en cours pour la convertir en type. |
| Étiquette personnalisée | Texte à afficher pour l’axe des Y en regard de l’étiquette pour le type d’unité.  Si aucune étiquette n’est spécifié, le type d’unité est affiché. |
| **Liste** |
| Requête | Requête à exécuter pour la liste.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| Masquer le graphique | Sélectionnez cette option pour désactiver le graphique vers la droite de la colonne numérique. |
| Activer les graphiques sparkline | Sélectionnez cette option pour afficher les graphiques sparkline au lieu de la barre horizontale.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Couleur | Couleur des barres ou des graphiques sparkline. |
| Opération | Opération à effectuer pour le graphique sparkline.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Nom de & séparateur de valeur | Délimiteur de caractère unique si vous souhaitez analyser la propriété text en plusieurs valeurs.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#name-value-separator) . |
| Requête de navigation | Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#navigation-query) . |
| **Liste** | **> Les titres de colonne** |
| Nom | Texte à afficher en haut de la première colonne de la liste. |
| Valeur | Texte à afficher en haut de la deuxième colonne de la liste. |
| **Liste** | **> Seuils de** |
| Activer les seuils | Sélectionnez cette option pour activer les seuils.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#thresholds) . |

## <a name="line-chart--list-part"></a>Partie graphique et la liste de ligne

En-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journaux au fil du temps.  Liste affiche les dix premiers résultats d’une requête avec un graphique qui indique la valeur relative d’une colonne numérique ou de sa modification dans le temps.

![Affichage graphique et la liste des lignes](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher dans la partie supérieure de la mosaïque. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Icône | Fichier de l’image à afficher à côté du résultat dans l’en-tête. |
| Utilisez l’icône | Sélectionnez cette option pour que l’affichage de l’icône. |
| **En-tête** |
| Titre | Texte à afficher en haut de l’en-tête. |
| Sous-titre | Texte à afficher sous le titre en haut de l’en-tête. |
| **Graphique en courbes** |
| Requête | Requête à exécuter pour le graphique en courbes.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé de **mesure** de synthétiser les résultats.  Si la requête utilise le mot-clé de **l’intervalle de** l’axe des abscisses du graphique utilisera cet intervalle de temps.  Si la requête n’inclut pas le mot-clé **intervalle** intervalles horaires sont utilisées pour l’axe x. |
| **Graphique en courbes** | **> Axe Y** |
| Utiliser une échelle logarithmique | Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe y. |
| Unités | Spécifiez les unités pour les valeurs renvoyées par la requête.  Ces informations sont utilisées pour afficher les étiquettes du graphique qui indique les types de valeur et, éventuellement, pour la conversion des valeurs.  Le Type d’unité spécifie la catégorie de l’unité et définit les valeurs de Type d’unité en cours qui sont disponibles.  Si vous sélectionnez une valeur dans convertir puis les valeurs numériques sont converties à partir du type d’unité en cours pour la convertir en type. |
| Étiquette personnalisée | Texte à afficher pour l’axe des Y en regard de l’étiquette pour le type d’unité.  Si aucune étiquette n’est spécifié, le type d’unité est affiché. |
| **Liste** |
| Requête | Requête à exécuter pour la liste.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| Masquer le graphique | Sélectionnez cette option pour désactiver le graphique vers la droite de la colonne numérique. |
| Activer les graphiques sparkline | Sélectionnez cette option pour afficher les graphiques sparkline au lieu de la barre horizontale.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Couleur | Couleur des barres ou des graphiques sparkline. |
| Opération | Opération à effectuer pour le graphique sparkline.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#sparklines) . |
| Nom de & séparateur de valeur | Délimiteur de caractère unique si vous souhaitez analyser la propriété text en plusieurs valeurs.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#name-value-separator) . |
| Requête de navigation | Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#navigation-query) . |
| **Liste** | **> Les titres de colonne** |
| Nom | Texte à afficher en haut de la première colonne de la liste. |
| Valeur | Texte à afficher en haut de la deuxième colonne de la liste. |
| **Liste** | **> Seuils de** |
| Activer les seuils | Sélectionnez cette option pour activer les seuils.  Pour plus d’informations, reportez-vous à la section [Paramètres communs](#thresholds) . |

## <a name="stack-of-line-charts-part"></a>Pile de partie de graphiques de ligne

Affiche les trois graphiques de ligne distincte avec plusieurs séries à partir d’une requête de journaux au fil du temps.

![Pile de graphiques en courbes](media/log-analytics-view-designer/view-stack-line-charts.png)

| Paramètre | Description |
|:--|:--|
| **Général** |
| Titre de groupe | Texte à afficher dans la partie supérieure de la mosaïque. |
| Nouveau groupe | Permet de créer un nouveau groupe dans la vue commençant à la vue actuelle. |
| Icône | Fichier de l’image à afficher à côté du résultat dans l’en-tête. |
| **Graphique 1<br>graphique 2<br>graphique 3** | **> En-tête** |
| Titre | Texte à afficher en haut du graphique. |
| Sous-titre | Texte à afficher sous le titre en haut du graphique. |
| **Graphique 1<br>graphique 2<br>graphique 3** | **Graphique en courbes** |
| Requête | Requête à exécuter pour le graphique en courbes.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé de **mesure** de synthétiser les résultats.  Si la requête utilise le mot-clé de **l’intervalle de** l’axe des abscisses du graphique utilisera cet intervalle de temps.  Si la requête n’inclut pas le mot-clé **intervalle** intervalles horaires sont utilisées pour l’axe x. |
| **Graphique** | **> Axe Y** |
| Utiliser une échelle logarithmique | Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe y. |
| Unités | Spécifiez les unités pour les valeurs renvoyées par la requête.  Ces informations sont utilisées pour afficher les étiquettes du graphique qui indique les types de valeur et, éventuellement, pour la conversion des valeurs.  Le Type d’unité spécifie la catégorie de l’unité et définit les valeurs de Type d’unité en cours qui sont disponibles.  Si vous sélectionnez une valeur dans convertir puis les valeurs numériques sont converties à partir du type d’unité en cours pour la convertir en type. |
| Étiquette personnalisée | Texte à afficher pour l’axe des Y en regard de l’étiquette pour le type d’unité.  Si aucune étiquette n’est spécifié, le type d’unité est affiché. |

## <a name="common-settings"></a>Paramètres courants
Les sections suivantes décrivent les paramètres communs à plusieurs parties de visualisation.

### <a name="name-value-separator">Nom de & séparateur de valeur</a>
Délimiteur de caractère unique si vous souhaitez analyser la propriété text à partir d’une requête de liste à plusieurs valeurs.  Si vous spécifiez un délimiteur, vous pouvez fournir des noms pour chaque champ, séparées par le même séparateur dans la zone Nom.

Par exemple, considérez une propriété appelée *emplacement* incluant des valeurs telles que la *Création de Redmond 41* et *Bellevue-Building12*.  Vous pouvez spécifier – nom & séparateur de valeur et *Création de ville* pour le nom.  Cette analyse chaque valeur dans les deux propriétés appelées la *Ville* et le *bâtiment*. 

### <a name="navigation-query">Requête de navigation</a>
Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  *{L’élément sélectionné}* permet d’inclure la syntaxe de l’élément sélectionné par l’utilisateur.

Par exemple, si la requête comporte une colonne appelée *ordinateur* et la requête de navigation est *{élément sélectionné}*, une requête telle que *ordinateur = « Monordinateur »* doit être exécuté lorsque l’utilisateur a sélectionné un ordinateur.  Si la requête de navigation est *Type = événement {élément sélectionné}* ensuite la requête *Type = ordinateur d’événement = « Monordinateur »* doit être exécuté.

### <a name="sparklines">Graphiques sparkline</a>
Un graphique sparkline est un petit trait qui illustre la valeur d’une entrée de liste dans le temps.  Pour les pièces de la visualisation d’une liste, vous pouvez sélectionner s’il faut afficher une barre qui indique la valeur relative d’une colonne numérique ou d’un graphique sparkline indiquant sa valeur au fil du temps horizontale.

Le tableau suivant décrit les paramètres pour les graphiques sparkline.

| Paramètre | Description |
|:--|:--|
| Activer les graphiques sparkline | Sélectionnez cette option pour afficher les graphiques sparkline au lieu de la barre horizontale. |
| Opération | Si les graphiques sparkline sont activés, il s’agit de l’opération à effectuer sur chaque propriété de la liste pour calculer les valeurs pour le graphique sparkline.<br><br>-Un dernier exemple : Dernière valeur de la série sur l’intervalle de temps.<br>-Max : Valeur maximale de la série sur l’intervalle de temps.<br>-Min : Les valeur minimale de la série sur l’intervalle de temps.<br>-Somme : Somme des valeurs de la série sur l’intervalle de temps.<br>-Résumé : Utilise la même commande de mesure que la requête dans l’en-tête. |

### <a name="thresholds">Seuils</a>
Seuils permettent d’afficher une icône de couleur en regard de chaque élément dans une liste en vous donnant une indication visuelle rapide d’éléments dépassent une valeur particulière ou compris dans une plage spécifique.  Par exemple, vous pouvez afficher une icône verte pour les éléments avec une valeur acceptable, jaune si la valeur est comprise dans une plage qui indique un avertissement et le rouge si elle est supérieure à une valeur d’erreur.

Lorsque vous activez des seuils pour une partie, vous devez spécifier une ou plusieurs des seuils.  Si la valeur d’un élément est supérieure à une valeur de seuil et inférieure à la valeur de seuil suivante, cette couleur est utilisée.  Si l’élément est supérieure à la valeur de seuil puis le plus élevé, cette couleur est définie.   

Chaque ensemble de seuil a un seuil de valeur **par défaut**.  Il s’agit de la couleur que si aucune autre valeur n’est dépassé.  Vous pouvez ajouter ou supprimer des seuils en cliquant sur les **+** ou bouton **x** .

Le tableau suivant décrit les paramètres de seuils.

| Paramètre | Description |
|:--|:--|
| Activer les seuils | Permet d’afficher une icône de couleur vers la gauche de chaque valeur indiquant son état de santé par rapport aux seuils spécifiés. |
| Nom | Nom permettant d’identifier la valeur de seuil. |
| Seuil | Valeur du seuil.  La couleur de la santé pour chaque élément de liste est définie sur la couleur de la plus grande valeur de seuil dépassée par la valeur de l’élément.  Il existe un seuil par défaut qui est la couleur si aucune valeur de seuil n’est dépassés. |
| Couleur | Couleur de la valeur de seuil. |


## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) prendre en charge les requêtes dans des parties de visualisation.