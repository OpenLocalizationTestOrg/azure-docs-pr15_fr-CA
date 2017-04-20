<properties
    pageTitle="Journal de référence de mosaïque Concepteur de vue Analytique | Microsoft Azure"
    description="Concepteur de vues dans le journal Analytique vous permet de créer des affichages personnalisés dans la console de l’OMS qui contiennent différentes visualisations de données dans le référentiel de l’OMS. Cet article fournit une référence des paramètres pour chacun des mosaïques disponibles à utiliser dans vos vues personnalisées."
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
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-tile-reference"></a>Référence du journal Analytique vue concepteur mosaïque
Le Concepteur de vue dans le journal Analytique vous permet de créer des affichages personnalisés dans la console de l’OMS qui contiennent différentes visualisations de données dans le référentiel de l’OMS. Cet article fournit une référence des paramètres pour chacun des mosaïques disponibles à utiliser dans vos vues personnalisées.

Autres articles disponibles pour le Concepteur de vue sont :

- [Concepteur de vue](log-analytics-view-designer.md) - vue d’ensemble du Concepteur de vues et des procédures de création et de modification des affichages personnalisés.
- [Référence de partie visualisation](log-analytics-view-designer-parts.md) - référence des paramètres pour chacun des mosaïques disponibles à utiliser dans vos vues personnalisées. 


Le tableau suivant répertorie les différents types de mosaïques disponibles dans le Concepteur de vue.  Les sections suivantes décrivent chaque type de mosaïque en détail et leurs propriétés.

| Mosaïque | Description |
|:--|:--|
| [Numéro de](#number-tile) | Numéro unique indiquant le nombre d’enregistrements à partir d’une requête. |
| [Deux nombres](#two-numbers-tile) | Deux nombres unique indiquant le nombre d’enregistrements à partir de deux requêtes différentes. |
| [Bouée](#donut-tile) | Graphique basé sur une requête avec une valeur de synthèse dans le centre. |
| [Graphique en courbes de & légende](#line-chart-amp-callout-tile) | Graphique en courbes basé sur une requête et une légende avec une valeur de synthèse. |
| [Graphique en courbes](#line-chart-tile) | Graphique en courbes basé sur une requête. |
| [Deux chronologies](#two-timelines-tile) | Histogramme avec deux séries que chacune basée sur une requête distincte. |



## <a name="number-tile"></a>Mosaïque de numéro

La mosaïque de **numéro** affiche un numéro unique indiquant le nombre d’enregistrements à partir d’une requête de journal et d’une étiquette.

![Mosaïque de numéro](media/log-analytics-view-designer/tile-number.png)

| Paramètre | Description |
|:--|:--|
| Nom        | Texte à afficher dans la partie supérieure de la mosaïque. |
| Description | Texte à afficher sous le nom de la mosaïque.    |
| **Mosaïque** |
| Légende | Texte à afficher dans la valeur. |
| Requête | Requête à exécuter.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| **Avancé** |  **> Vérification de-flux de données** |
| Activé | Sélectionnez si la vérification du flux de données doit être activée pour la mosaïque.  Ainsi, un autre message si les données ne seront pas disponibles pour la mosaïque.  Cette opération est généralement utilisée pour fournir un message au cours de la période temporaire lorsque la vue est installée et les données sont disponibles. |
| Requête | Une requête pour exécuter pour vérifier si les données sont disponibles pour l’affichage.  Si la requête ne renvoie aucun résultat, un message s’affiche au lieu de la valeur de la requête principale. |
| Message | Message à afficher si la requête de vérification du flux de données ne retourne pas de données.  Si vous ne fournissez aucun message, *Effectuez l’évaluation* s’affiche. |
| **Intervalle de temps** |
| Durée | Durée à partir de la date à utiliser pour l’intervalle de temps de la requête.  Par exemple, si **7 jours** est spécifiée, la requête est limitée aux enregistrements créés à partir de 7 jours à la date actuelle. |
| Décalage des données de fin | Offset facultatif à partir des données en cours à utiliser pour l’intervalle de temps de la requête principale.  Par exemple, si le **jour de -1** est utilisé pour les **7 jours** utilisé pour la **durée**et le **décalage de date de fin** , la requête est limitée aux enregistrements créés à partir de 8 jours à hier. |

## <a name="two-numbers-tile"></a>Mosaïque de deux nombres

La mosaïque de **Numéro deux** affiche deux chiffres indiquant le nombre d’enregistrements à partir de deux requêtes de journal différent et une étiquette pour chacune.

![Mosaïque de deux nombres](media/log-analytics-view-designer/tile-two-numbers.png)

| Paramètre | Description |
|:--|:--|
| Nom        | Texte à afficher dans la partie supérieure de la mosaïque. |
| Description | Texte à afficher sous le nom de la mosaïque.    |
| **Première mosaïque** |
| Légende | Texte à afficher dans la valeur. |
| Requête | Requête à exécuter.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| **Deuxième mosaïque** |
| Légende | Texte à afficher dans la valeur. |
| Requête | Requête à exécuter.  Le décompte du nombre d’enregistrements renvoyés par la requête s’affiche. |
| **Avancé** | **> Vérification de-flux de données** |
| Activé | Sélectionnez si la vérification du flux de données doit être activée pour la mosaïque.  Ainsi, un autre message si les données ne seront pas disponibles pour la mosaïque.  Cette opération est généralement utilisée pour fournir un message au cours de la période temporaire lorsque la vue est installée et les données sont disponibles. |
| Requête | Une requête pour exécuter pour vérifier si les données sont disponibles pour l’affichage.  Si la requête ne renvoie aucun résultat, un message s’affiche au lieu de la valeur de la requête principale. |
| Message | Message à afficher si la requête de vérification du flux de données ne retourne pas de données.  Si vous ne fournissez aucun message, *Effectuez l’évaluation* s’affiche. |
| **Intervalle de temps** |
| Durée | Durée à partir de la date à utiliser pour l’intervalle de temps de la requête.  Par exemple, si **7 jours** est spécifiée, la requête est limitée aux enregistrements créés à partir de 7 jours à la date actuelle. |
| Décalage des données de fin | Offset facultatif à partir des données en cours à utiliser pour l’intervalle de temps de la requête principale.  Par exemple, si le **jour de -1** est utilisé pour les **7 jours** utilisé pour la **durée**et le **décalage de date de fin** , la requête est limitée aux enregistrements créés à partir de 8 jours à hier. |

## <a name="donut-tile"></a>Mosaïque de bouée

La mosaïque de **bouée** affiche un nombre unique résumé à partir d’une colonne de valeur dans une requête de journal.  L’anneau affiche graphiquement les résultats des trois premiers enregistrements.

![Mosaïque de bouée](media/log-analytics-view-designer/tile-donut.png)

| Paramètre | Description |
|:--|:--|
| Nom        | Texte à afficher dans la partie supérieure de la mosaïque. |
| Description | Texte à afficher sous le nom de la mosaïque.    |
| **Bouée** |
| Requête | Requête à exécuter pour l’anneau.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé de **mesure** de synthétiser les résultats. |
| **Bouée** | **> Centre de** |
| Texte | Texte à afficher sous la valeur à l’intérieur de l’anneau. |
| Opération | L’opération à effectuer sur la propriété value pour résumer à une seule valeur.<br><br>-Sum : Ajouter les valeurs de tous les enregistrements avec la valeur de la propriété.<br>-Pourcentage : Le pourcentage des valeurs totalisées à partir des enregistrements avec la valeur de la propriété par rapport aux valeurs de somme de tous les enregistrements. |
| Valeurs de résultat utilisés dans l’opération de centre | Éventuellement, cliquez sur le signe plus pour ajouter une ou plusieurs valeurs.  Les résultats de la requête sera limitées à des enregistrements avec les valeurs de propriété que vous spécifiez.  Si aucune valeur n’est ajouté, que tous les enregistrements sont inclus dans la requête. |
| **Bouée** | **> Autres options** |
| Couleurs | La couleur à afficher pour chacune des trois propriétés principales.  Si vous souhaitez spécifier des couleurs de remplacement pour les valeurs de propriété spécifiques, puis utilisez mappage de couleur avancés. |
| Mappage de couleurs avancés | Affiche une couleur pour les valeurs de propriété spécifiques.  Si la valeur que vous spécifiez est dans les trois premiers, la couleur de remplacement s’affiche au lieu de la couleur standard.  Si la propriété n’est pas dans les trois premiers, la couleur n’est pas affichée. |
| **Avancé** | **> Vérification de-flux de données** |
| Activé | Sélectionnez si la vérification du flux de données doit être activée pour la mosaïque.  Ainsi, un autre message si les données ne seront pas disponibles pour la mosaïque.  Cette opération est généralement utilisée pour fournir un message au cours de la période temporaire lorsque la vue est installée et les données sont disponibles. |
| Requête | Une requête pour exécuter pour vérifier si les données sont disponibles pour l’affichage.  Si la requête ne renvoie aucun résultat, un message s’affiche au lieu de la valeur de la requête principale. |
| Message | Message à afficher si la requête de vérification du flux de données ne retourne pas de données.  Si vous ne fournissez aucun message, *Effectuez l’évaluation* s’affiche. |
| **Intervalle de temps** |
| Durée | Durée à partir de la date à utiliser pour l’intervalle de temps de la requête.  Par exemple, si **7 jours** est spécifiée, la requête est limitée aux enregistrements créés à partir de 7 jours à la date actuelle. |
| Décalage des données de fin | Offset facultatif à partir des données en cours à utiliser pour l’intervalle de temps de la requête principale.  Par exemple, si le **jour de -1** est utilisé pour les **7 jours** utilisé pour la **durée**et le **décalage de date de fin** , la requête est limitée aux enregistrements créés à partir de 8 jours à hier. |

## <a name="line-chart-tile"></a>Mosaïque de graphique de ligne

La mosaïque de **graphique en courbes** affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journaux au fil du temps.  

![Mosaïque de graphique en courbes & légende](media/log-analytics-view-designer/tile-line-chart.png)

| Paramètre | Description |
|:--|:--|
| Nom        | Texte à afficher dans la partie supérieure de la mosaïque. |
| Description | Texte à afficher sous le nom de la mosaïque.    |
| **Graphique en courbes** |  
| Requête | Requête à exécuter pour le graphique en courbes.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé de **mesure** de synthétiser les résultats.  Si la requête utilise le mot-clé de **l’intervalle de** l’axe des abscisses du graphique utilisera cet intervalle de temps.  Si la requête n’inclut pas le mot-clé **intervalle** intervalles horaires sont utilisées pour l’axe x. |
| **Graphique en courbes** | **> Axe Y** |
| Utiliser une échelle logarithmique | Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe y. |
| Unités | Spécifiez les unités pour les valeurs renvoyées par la requête.  Ces informations sont utilisées pour afficher les étiquettes du graphique qui indique les types de valeur et, éventuellement, pour la conversion des valeurs.  Le **Type d’unité** spécifie la catégorie de l’unité et définit les valeurs de **Type d’unité en cours** qui sont disponibles.  Si vous sélectionnez une valeur de **convertir** les valeurs numériques sont converties à partir du type **d’Unité en cours** pour la **convertir en** type. |
| Étiquette personnalisée | Texte à afficher pour l’axe des Y en regard de l’étiquette pour le type d’unité.  Si aucune étiquette n’est spécifié, le type d’unité est affiché. |
| **Avancé** | **> Vérification de-flux de données** |
| Activé | Sélectionnez si la vérification du flux de données doit être activée pour la mosaïque.  Ainsi, un autre message si les données ne seront pas disponibles pour la mosaïque.  Cette opération est généralement utilisée pour fournir un message au cours de la période temporaire lorsque la vue est installée et les données sont disponibles. |
| Requête | Une requête pour exécuter pour vérifier si les données sont disponibles pour l’affichage.  Si la requête ne renvoie aucun résultat, un message s’affiche au lieu de la valeur de la requête principale. |
| Message | Message à afficher si la requête de vérification du flux de données ne retourne pas de données.  Si vous ne fournissez aucun message, *Effectuez l’évaluation* s’affiche. |
| **Intervalle de temps** |
| Durée | Durée à partir de la date à utiliser pour l’intervalle de temps de la requête.  Par exemple, si **7 jours** est spécifiée, la requête est limitée aux enregistrements créés à partir de 7 jours à la date actuelle. |
| Décalage des données de fin | Offset facultatif à partir des données en cours à utiliser pour l’intervalle de temps de la requête principale.  Par exemple, si le **jour de -1** est utilisé pour les **7 jours** utilisé pour la **durée**et le **décalage de date de fin** , la requête est limitée aux enregistrements créés à partir de 8 jours à hier. |


## <a name="line-chart--callout-tile"></a>Mosaïque & légende du graphique de ligne

La mosaïque de la **légende et le graphique en courbes** affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps et une légende avec une valeur de synthèse.  

![Mosaïque de graphique en courbes & légende](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Paramètre | Description |
|:--|:--|
| Nom        | Texte à afficher dans la partie supérieure de la mosaïque. |
| Description | Texte à afficher sous le nom de la mosaïque.    |
| **Graphique en courbes** |  
| Requête | Requête à exécuter pour le graphique en courbes.  La première propriété doit être une valeur de texte et de la seconde propriété une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé de **mesure** de synthétiser les résultats.  Si la requête utilise le mot-clé de **l’intervalle de** l’axe des abscisses du graphique utilisera cet intervalle de temps.  Si la requête n’inclut pas le mot-clé **intervalle** intervalles horaires sont utilisées pour l’axe x. |
| **Graphique en courbes** | **> Légende** |
| Légende | Texte de titre à afficher au-dessus de la valeur de la légende. |
| Nom de la série | Valeur de la propriété de la série à utiliser pour la valeur de la légende.  Si aucune série n’est fourni, tous les enregistrements de la requête sont utilisés. |
| Opération | L’opération à effectuer sur la propriété value pour résumer à une seule valeur de la légende.<br>-Moyenne : Moyenne de la valeur à partir de tous les enregistrements.<br><br>-Count : Nombre de tous les enregistrements renvoyés par la requête.<br>-Dernier exemple : Valeur à partir de la dernière période incluse dans le graphique.<br>-Max : Valeur maximale entre les intervalles incluses dans le graphique.<br>-Min : Valeur minimale entre les intervalles incluses dans le graphique.<br>-Somme : Somme de la valeur à partir de tous les enregistrements. |
| **Graphique en courbes** | **> Axe Y** |
| Utiliser une échelle logarithmique | Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe y. |
| Unités | Spécifiez les unités pour les valeurs renvoyées par la requête.  Ces informations sont utilisées pour afficher les étiquettes du graphique qui indique les types de valeur et, éventuellement, pour la conversion des valeurs.  Le **Type d’unité** spécifie la catégorie de l’unité et définit les valeurs de **Type d’unité en cours** qui sont disponibles.  Si vous sélectionnez une valeur de **convertir** les valeurs numériques sont converties à partir du type **d’Unité en cours** pour la **convertir en** type. |
| Étiquette personnalisée | Texte à afficher pour l’axe des Y en regard de l’étiquette pour le type d’unité.  Si aucune étiquette n’est spécifié, le type d’unité est affiché. |
| **Avancé** | **> Vérification de-flux de données** |
| Activé | Sélectionnez si la vérification du flux de données doit être activée pour la mosaïque.  Ainsi, un autre message si les données ne seront pas disponibles pour la mosaïque.  Cette opération est généralement utilisée pour fournir un message au cours de la période temporaire lorsque la vue est installée et les données sont disponibles. |
| Requête | Une requête pour exécuter pour vérifier si les données sont disponibles pour l’affichage.  Si la requête ne renvoie aucun résultat, un message s’affiche au lieu de la valeur de la requête principale. |
| Message | Message à afficher si la requête de vérification du flux de données ne retourne pas de données.  Si vous ne fournissez aucun message, *Effectuez l’évaluation* s’affiche. |
| **Intervalle de temps** |
| Durée | Durée à partir de la date à utiliser pour l’intervalle de temps de la requête.  Par exemple, si **7 jours** est spécifiée, la requête est limitée aux enregistrements créés à partir de 7 jours à la date actuelle. |
| Décalage des données de fin | Offset facultatif à partir des données en cours à utiliser pour l’intervalle de temps de la requête principale.  Par exemple, si le **jour de -1** est utilisé pour les **7 jours** utilisé pour la **durée**et le **décalage de date de fin** , la requête est limitée aux enregistrements créés à partir de 8 jours à hier. |

## <a name="two-timelines-tile"></a>Mosaïque de deux chronologies

La mosaïque de **deux chronologies** affiche les résultats de deux requêtes de journal dans le temps sous la forme d’histogrammes.  Une légende est affichée pour chaque série.  

![Mosaïque de deux chronologies](media/log-analytics-view-designer/tile-two-timelines.png)

| Paramètre | Description |
|:--|:--|
| Nom        | Texte à afficher dans la partie supérieure de la mosaïque. |
| Description | Texte à afficher sous le nom de la mosaïque.    |
| Premier graphique   
| Légende | Texte à afficher dans la légende de la première série.
| Couleur | Couleur à utiliser pour les colonnes de la première série.
| Requête avec graphique | Requête à exécuter pour la première série.  Le décompte du nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes du graphique.
| Opération | L’opération à effectuer sur la propriété value pour résumer à une seule valeur de la légende.<br><br>-Moyenne : Moyenne de la valeur à partir de tous les enregistrements.<br>-Count : Nombre de tous les enregistrements renvoyés par la requête.<br>-Dernier exemple : Valeur à partir de la dernière période incluse dans le graphique.<br>-Max : Valeur maximale entre les intervalles incluses dans le graphique.
| **Le deuxième graphique** |
| Légende | Texte à afficher dans la légende pour la deuxième série.
| Couleur | Couleur à utiliser pour les colonnes de la deuxième série.
| Requête avec graphique | Requête à exécuter pour la deuxième série.  Le décompte du nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes du graphique.
| Opération | L’opération à effectuer sur la propriété value pour résumer à une seule valeur de la légende.<br><br>-Moyenne : Moyenne de la valeur à partir de tous les enregistrements.<br>-Count : Nombre de tous les enregistrements renvoyés par la requête.<br>-Dernier exemple : Valeur à partir de la dernière période incluse dans le graphique.<br>-Max : Valeur maximale entre les intervalles incluses dans le graphique. |
| **Avancé** | **> Vérification de-flux de données** |
| Activé | Sélectionnez si la vérification du flux de données doit être activée pour la mosaïque.  Ainsi, un autre message si les données ne seront pas disponibles pour la mosaïque.  Cette opération est généralement utilisée pour fournir un message au cours de la période temporaire lorsque la vue est installée et les données sont disponibles. |
| Requête | Une requête pour exécuter pour vérifier si les données sont disponibles pour l’affichage.  Si la requête ne renvoie aucun résultat, un message s’affiche au lieu de la valeur de la requête principale. |
| Message | Message à afficher si la requête de vérification du flux de données ne retourne pas de données.  Si vous ne fournissez aucun message, *Effectuez l’évaluation* s’affiche. |
| **Intervalle de temps** |
| Durée | Durée à partir de la date à utiliser pour l’intervalle de temps de la requête.  Par exemple, si **7 jours** est spécifiée, la requête est limitée aux enregistrements créés à partir de 7 jours à la date actuelle. |
| Décalage des données de fin | Offset facultatif à partir des données en cours à utiliser pour l’intervalle de temps de la requête principale.  Par exemple, si le **jour de -1** est utilisé pour les **7 jours** utilisé pour la **durée**et le **décalage de date de fin** , la requête est limitée aux enregistrements créés à partir de 8 jours à hier. |

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) prendre en charge les requêtes en mosaïques.
- Ajouter [Des éléments de visualisation](log-analytics-view-designer-parts.md) à votre affichage personnalisé.