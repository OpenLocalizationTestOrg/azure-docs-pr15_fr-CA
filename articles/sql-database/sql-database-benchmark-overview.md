<properties
    pageTitle="Vue d’ensemble du point de référence de base de données SQL Azure"
    description="Cette rubrique décrit la référence de base de données SQL Azure utilisé pour mesurer les performances de la base de données de SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="06/21/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-benchmark-overview"></a>Vue d’ensemble du point de référence de base de données SQL Azure

## <a name="overview"></a>Vue d’ensemble
Base de données SQL de Microsoft Azure propose trois [niveaux de service](sql-database-service-tiers.md) avec plusieurs niveaux de performances. Chaque niveau de performances fournit un ensemble croissant de ressources, ou « puissance », conçu pour offrir un débit supérieur de plus en plus.

Il est important d’être en mesure de quantifier la la puissance croissante de chaque niveau de performances se traduit par une augmentation de la base de données. Pour faire de Microsoft a développé la référence de base de données SQL Azure (BASD). Le banc d’essai exerce une combinaison d’opérations de base de toutes les charges de travail OLTP. Nous mesurer le débit obtenu pour les bases de données en cours d’exécution dans chaque niveau de performances.

Les ressources et l’alimentation de chaque niveau de performances et les niveaux de service sont exprimées en [Unités de base de données de Transaction (DTUs)](sql-database-technical-overview.md#understand-dtus). DTUs fournissent un moyen de décrire la capacité relative d’un niveau de performance basée sur une mesure combinée de processeur, mémoire, lire et écrire les taux offerts par chaque niveau de performances. L’évaluation de DTU d’une base de données de doubler équivaut à doubler la puissance de la base de données. Le banc d’essai permet d’évaluer l’impact sur les performances de la base de données de la puissance croissante offerte par chaque niveau de performances en exerçant des opérations de base de données, lors de la mise à l’échelle de taille de base de données, le nombre d’utilisateurs et de taux de transaction en fonction des ressources fournies dans la base de données.

En exprimant le débit de la couche de service de base à l’aide de transactions à l’heure, le niveau de service Standard à l’aide de transactions par minute et le niveau de service Premium à l’aide de transactions par seconde, elle facilite à associer rapidement les performances potentielles de chaque niveau de service pour les besoins d’une application.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Mise en corrélation des résultats de performances de base de données réels
Il est important de comprendre que BASD, comme tous les bancs d’essai, est représentatif et indicatif uniquement. Les taux de transaction avec l’application de banc d’essai ne sera pas les mêmes que celles qui peuvent être obtenus avec d’autres applications. Le banc d’essai comprend un ensemble de types est exécutée par rapport à un schéma contenant une plage de types de données et les tables de transaction différent. Alors que le banc d’essai exerce les mêmes opérations de base qui sont communes à toutes les charges de travail OLTP, il ne représente pas une classe spécifique de la base de données ou de l’application. L’objectif du test est de servir de guide pour la performance relative d’une base de données susceptibles de lors de la mise à l’échelle vers le haut ou vers le bas entre des niveaux de performance. En réalité, les bases de données sont de complexité et de tailles différentes, rencontrent différentes combinaisons de charges de travail et doit répondre de différentes manières. Par exemple, une application gourmande en e/s peut-être atteint les seuils d’e/s plus tôt, ou une application mobilisant le processeur peut-être atteint les limites de l’UC plus tôt. Il n’y a aucune garantie que les bases de données particulier mettra à l’échelle de la même façon que le banc d’essai sous l’augmentation de la charge.

Le banc d’essai et sa méthodologie sont décrites plus en détail ci-dessous.

## <a name="benchmark-summary"></a>Résumé du banc d’essai
BASD mesure les performances d’une combinaison d’opérations de base de la base de données qui se produisent plus fréquemment dans les charges de travail (OLTP) de traitement des transactions en ligne. Bien que le banc d’essai est conçu pour le cloud computing l’esprit, le schéma de base de données, le remplissage des données, et les transactions ont été conçues pour être largement représentative des éléments de base plus couramment utilisés dans les charges de travail OLTP.

## <a name="schema"></a>Schéma
Le schéma est conçu pour avoir suffisamment de variété et de la complexité pour prendre en charge d’un large éventail d’opérations. Le banc d’essai est exécuté sur une base de données composé six tables. Les tables se répartissent en trois catégories : taille fixe, l’évolution et le développement. Il existe deux tables de taille fixe ; trois tables de mise à l’échelle ; et une table en pleine croissance. Les tableaux de taille fixe ont un nombre constant de lignes. Mise à l’échelle des tables ont une cardinalité est proportionnelle aux performances de la base de données, mais ne change pas pendant le test d’évaluation. Le tableau de croissance est dimensionné comme une table de mise à l’échelle de chargement initial, mais ensuite les modifications de cardinalité au cours de l’exécution du banc d’essai de lignes sont insérées et supprimées.

Le schéma inclut une combinaison de types de données entier, date/heure, numérique et caractère. Le schéma inclut des clés primaires et secondaires, mais pas toutes les clés étrangères, autrement dit, il n’existe aucune des contraintes d’intégrité référentielle entre les tables.

Un programme de génération de données génère les données pour la base de données initiale. Données numériques et d’entier sont générées avec les différentes stratégies. Dans certains cas, les valeurs sont distribuées de façon aléatoire sur une plage. Dans les autres cas, un ensemble de valeurs est aléatoire permuté pour assurer une distribution spécifique. Les champs de texte sont générées à partir d’une liste pondérée des mots pour produire l’aspect des données réalistes.

La base de données est dimensionné selon un « facteur d’échelle ». Le facteur d’échelle (abrégé en SF) détermine la cardinalité de la mise à l’échelle et de croissance des tables. Comme décrit dans la section utilisateurs et rythmée, la taille de la base de données, le nombre d’utilisateurs et des performances maximales tout mettre à l’échelle proportionnellement à l’autre.

## <a name="transactions"></a>Transactions
La charge de travail est constitué de neuf types de transactions, comme indiqué dans le tableau ci-dessous. Chaque transaction est conçue pour mettre en évidence d’un ensemble particulier de caractéristiques de système dans le matériel moteur et système de base de données, avec un contraste élevé des autres transactions. Cette approche rend plus facile d’évaluer l’impact des différents composants de performances globales. Par exemple, la transaction « Lecture Heavy » génère un nombre important d’opérations de lecture à partir du disque.

| Type de transaction | Description |
|---|---|
| Lecture Lite | SÉLECTIONNEZ ; en mémoire ; en lecture seule |
| Support de lecture | SÉLECTIONNEZ ; principalement en mémoire ; en lecture seule |
| Contrôle de lecture | SÉLECTIONNEZ ; surtout pas en mémoire ; en lecture seule |
| Mise à jour Lite | MISE À JOUR ; en mémoire ; en lecture-écriture. |
| Mise à jour lourds | MISE À JOUR ; surtout pas en mémoire ; en lecture-écriture. |
| Insertion Lite | INSÉRER ; en mémoire ; en lecture-écriture. |
| Insérer les lourds | INSÉRER ; surtout pas en mémoire ; en lecture-écriture. |
| Supprimer | SUPPRIMER ; mélange d’en mémoire et pas en mémoire ; en lecture-écriture. |
| Intensive de l’UC | SÉLECTIONNEZ ; en mémoire ; charge de l’UC relativement lourde ; en lecture seule |

## <a name="workload-mix"></a>Combinaison de la charge de travail
Transactions sont sélectionnées de manière aléatoire à partir d’une distribution pondérée avec le mixage global suivant. Le mixage global a un taux de lecture d’environ 2:1.

| Type de transaction | % de combinaison |
|---|---|
| Lecture Lite | 35 |
| Support de lecture | 20 |
| Contrôle de lecture | 5 |
| Mise à jour Lite | 20 |
| Mise à jour lourds | 3 |
| Insertion Lite | 3 |
| Insérer les lourds | 2 |
| Supprimer | 2 |
| Intensive de l’UC | 10 |

## <a name="users-and-pacing"></a>Les utilisateurs et la stimulation
La charge de travail du banc d’essai pilotée à partir d’un outil qui envoie des transactions sur un ensemble de connexions pour simuler le comportement d’un nombre d’utilisateurs simultanés. Bien que toutes les connexions et les transactions sont générée par machine, par souci de simplicité nous faisons référence à ces connexions comme des « utilisateurs ». Bien que chaque utilisateur fonctionne indépendamment de tous les autres utilisateurs, tous les utilisateurs exécuter le même cycle des étapes ci-dessous :

1. Établir une connexion de base de données.
2. Répétez jusqu'à ce que signalé pour quitter :
    - Sélectionnez une transaction de façon aléatoire (à partir d’une distribution pondérée).
    - Effectuer la transaction sélectionnée et mesurer le temps de réponse.
    - Attendre un rythme.
3. Fermer la connexion de base de données.
4. Sortie.

Le rythme (à l’étape 2c) est sélectionnée de manière aléatoire, mais avec une distribution qui possède une moyenne de 1,0 seconde. Chaque utilisateur peut, génèrent donc en moyenne, une transaction au maximum par seconde.

## <a name="scaling-rules"></a>Règles de mise à l’échelle
Le nombre d’utilisateurs est déterminé par la taille de la base de données (en unités de facteur d’échelle). Il existe un utilisateur pour chaque cinq unités de facteur d’échelle. En raison du rythme, un utilisateur peut générer une transaction au maximum par seconde, en moyenne.

Par exemple, un facteur d’Echelle de 500 (SF = 500) base de données des 100 utilisateurs et peut atteindre un taux maximal de 100 TPS. Pour piloter un TPS supérieur taux nécessite plus d’utilisateurs et de plus d’une base de données.

Le tableau ci-dessous indique le nombre d’utilisateurs soutenus en fait pour chaque niveau de performances et les niveaux de service.

| Niveau de service (niveau de Performance) | Utilisateurs | Taille de la base de données |
|---|---|---|
| Base | 5 | 720 MO |
| Standard (S0) | 10 | 1 GO |
| Standard (S1) | 20 | 2.1 GO |
| Standard (S2) | 50 | 7.1 GO |
| Prime (P1) | 100 | 14 GO |
| Prime (P2) | 200 | 28 GO |
| Prime (P6/P3) | 800 | 114 GO |

## <a name="measurement-duration"></a>Durée de la mesure
Une exécution de benchmark valide requiert une durée de mesure de l’état stable d’au moins une heure.

## <a name="metrics"></a>Mesures
La mesure essentielle dans le banc d’essai est débit et temps de réponse.

- Le débit est la mesure de performances essentielles dans le banc d’essai. Le débit est signalé dans les transactions par unité de temps, tous les types de transactions d’inventaire.
- Temps de réponse est une mesure de prévisibilité des performances. La contrainte de temps de réponse varie en fonction de la classe de service, avec des classes plus élevés de service ayant une spécification de temps de réponse plus stricte, comme illustré ci-dessous.

| Classe de Service  | Mesure de débit | Délai de réponse |
|---|---|---|
| Premium | Transactions par seconde | 95e centile à 0,5 secondes |
| Standard | Transactions par minute | 90e centile à 1,0 seconde |
| Base | Transactions par heure | 80th centile 2.0 secondes |

## <a name="conclusion"></a>Conclusion
La référence de base de données SQL Azure mesure les performances relatives de la base de données de SQL Azure s’exécutant au sein de la plage de niveaux de service disponibles et de performances. Le banc d’essai exerce une combinaison d’opérations de base de la base de données qui se produisent plus fréquemment dans les charges de travail (OLTP) de traitement des transactions en ligne. Par mesure de performances réelles, le banc d’essai fournit une évaluation plus significative de l’impact sur le débit de modifier le niveau de performance en juste répertoriant les ressources fournies par chaque niveau de vitesse du processeur, la taille de la mémoire et Ops ES/s. À l’avenir, nous continuerons à évoluer au banc d’essai pour élargir son champ d’application et de développer les données fournies.

## <a name="resources"></a>Ressources
[Présentation de la base de données SQL](sql-database-technical-overview.md)

[Les niveaux de service et de performances](sql-database-service-tiers.md)

[Conseils pour les performances des bases de données unique](sql-database-performance-guidance.md)
