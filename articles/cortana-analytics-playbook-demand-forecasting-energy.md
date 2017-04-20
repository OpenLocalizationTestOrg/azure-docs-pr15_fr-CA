<properties
    pageTitle="Cortana Intelligence Solution modèle manuel pour la prévision de la demande d’énergie | Microsoft Azure"
    description="Un modèle de Solution avec Microsoft Cortana Intelligence qui contribue à la prévision de la demande pour une société d’énergie."
    services="cortana-analytics"
    documentationCenter=""
    authors="ilanr9"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/24/2016"
    ms.author="ilanr9;yijichen;garye"/>

# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence Solution modèle manuel pour la prévision de la demande d’énergie  

## <a name="executive-summary"></a>Rapport de synthèse  

Au cours des quelques dernières années, Internet des objets (IoT), les sources d’énergie alternatives et les données volumineuses ont fusionné pour créer de grandes opportunités dans le domaine de l’utilitaire et de l’énergie. Dans le même temps, l’utilitaire et le secteur de l’énergie tout ont vu la consommation de l’aplatissement consommateurs exigent les meilleures façons de contrôler leur utilisation de l’énergie. Par conséquent, l’utilitaire et les sociétés de grille actives sont absolument besoin d’innover et de se renouveler. En outre, plusieurs grilles de puissance et utilitaire deviennent obsolètes et très coûteux à maintenir et à gérer. Au cours de l’année dernière, l’équipe a travaillé sur un certain nombre d’engagements dans le domaine de l’énergie. Au cours de ces engagements, nous avons rencontré de nombreux cas dans lesquels les utilitaires ou les éditeurs de logiciels indépendants (Independent Software Vendors) cherchiez en prévision de la demande énergétique futures. Ces prévisions jouent un rôle important dans leur entreprise actuels et futurs et sont devenus la base pour les différents cas d’usage. Citons notamment la prévision de charge de puissance à court et à long terme, commerciaux, équilibrage de la charge, l’optimisation de la grille etc.. Données volumineuses et les méthodes de l’Analytique avancée (AA) par exemple Machine Learning (ML) sont les déterminants pour la production des prévisions précises et fiables.  

Dans ce manuel, nous avons rassemblé les commerciaux et les instructions analytiques nécessaires pour un développement réussi et solution de prévision du déploiement de la demande énergétique. Ces instructions proposées peuvent aider utilitaires, données scientifiques et les ingénieurs de données lors de l’établissement des solutions entièrement operationalized, nuage, prévision de la demande. Pour les sociétés qui débute leurs données volumineuses et voyage d’analytique avancée, une telle solution peut représenter l’amorçage initial dans leur stratégie à long terme de la grille active.

>[AZURE.TIP] Pour télécharger un diagramme qui fournit une vue d’ensemble architecturale de ce modèle, consultez [architecture de modèle de Solution Cortana Intelligence pour la prévision de la demande d’énergie](cortana-analytics-architecture-demand-forecasting-energy.md).  

## <a name="overview"></a>Vue d’ensemble  

Ce document traite de l’entreprise, les données et les aspects techniques de l’utilisation de Cortana Intelligence et en particulier Azure Machine Learning (AML) pour la mise en œuvre et le déploiement de Solutions de prévisions de l’énergie. Le document se compose de trois parties principales :  

1. Présentation de l’entreprise  
2. Présentation de données  
3. Mise en œuvre technique

La partie de la **Présentation de l’entreprise** présente l’aspect de l’entreprise à comprendre et à prendre en compte avant de prendre une décision d’investissement. Il explique comment qualifier le problème de l’entreprise à portée de main afin de garantir qu’analytique prédictive et apprentissage automatique sont en effet efficaces et applicables. Plus le document explique les principes fondamentaux de l’apprentissage automatique et comment il est utilisé pour résoudre les problèmes de prévisions de l’énergie. Il présente les conditions requises et les critères de qualification d’un cas d’usage. Quelques cas d’utilisation et cas client scénarios sont également fournis.

Les données sont l’ingrédient principal de toute solution d’apprentissage de machine. La partie de la **Présentation de données** de ce document traite certains aspects importants de données. Il décrit le type de données qui sont nécessaire pour la prévision de l’énergie, les exigences de qualité de données et quelles sources de données existent en général. Nous expliquons également comment les données brutes sont utilisées pour préparer des fonctionnalités de données qui en fait la partie de la modélisation du lecteur.

La troisième partie du document traite de l’aspect **Technique de mise en œuvre** d’une solution. Ingénierie de fonctionnalité et de modélisation sont au cœur du processus de science de données et par conséquent sont examinées en détail. Il couvre le concept de services web, qui sont un véhicule important pour le déploiement de cloud de solutions d’analytique prédictive. Nous décrivons également une architecture classique d’une solution de bout en bout pour operationalized.

En outre, le document inclut des documents de référence que vous pouvez utiliser pour mieux comprendre le domaine et la technologie.

Il est important de noter que nous ne souhaitez pas couvrir dans ce document, le processus de science de données plus profond, ses aspects mathématiques et techniques. Vous trouverez ces informations dans la [documentation d’Azure ML](http://azure.microsoft.com/services/machine-learning/) et des [blogs](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Public cible   
Le public cible de ce document est de l’entreprise et du personnel technique qui souhaite acquérir des connaissances et compréhension d’apprentissage automatique solutions et comment ils sont utilisés spécifiquement dans le domaine de la prévision de l’énergie.

Les scientifiques de données peuvent également bénéficier de la lecture de ce document pour obtenir une meilleure compréhension du processus de haut niveau qui pilote le déploiement d’une solution de prévision d’énergie. Dans ce contexte qu’il peut également servir à établir une base correcte et point de départ pour en savoir plus détaillées et avancées de matériau.

### <a name="industry-trends"></a>Tendances du secteur  
Dans ces dernières années, IoT, les sources d’énergie alternatives et les données sont fusionnées pour créer de grandes opportunités dans l’espace d’utilitaire et de l’énergie. Dans le même temps, l’utilitaire et les secteurs de l’énergie tout ont vu la consommation de l’aplatissement consommateurs exigent les meilleures façons de contrôler leur utilisation de l’énergie.

De nombreux utilitaire et sociétés de services énergétiques actives ont été novatrices la [grille dynamique](https://en.wikipedia.org/wiki/Smart_grid) en déployant un nombre d’utilisation de cas qui utilisent des données générées par la grille. Plusieurs cas d’usage concernent les caractéristiques intrinsèques de la production d’électricité : il ne peut pas être accumulé ni stockée côté en tant que stock. Par conséquent, ce qui est produit est nécessaire. Utilitaires qui souhaitent devenir plus efficace est nécessaire prévoir la consommation d’énergie tout simplement parce que vous obtiendrez ainsi une plus grande possibilité de **Solde offre et la demande**, empêchant ainsi gaspillage d’énergie, **réduire les émissions de gaz à effet de serre**et des coûts de contrôle.

Lors de la communication avec des coûts, il existe un autre aspect important, ce qui est des prix. Nouvelles possibilités aux échanges d’alimentation entre les utilitaires ont été à devoir de **prévision de la demande future et les futur prix de l’électricité**. Cela peut aider les entreprises à déterminer leurs volumes de production.

Lorsque nous utilisons le terme « actif », nous font en fait référence à une grille qui peut apprendre et faire des prévisions. Il peut anticiper les variations saisonnières de la consommation, ainsi que **prévoir les situations de surcharge temporaire et ajuster automatiquement pour qu’il**. Par le contrôle à distance de consommation (à l’aide de ces compteurs smart), les situations de surcharge localisées peuvent être traitées. **Par tout d’abord prédire et d’agir**, la grille devient plus intelligemment au fil du temps.

Pour le reste de ce document, nous nous concentrerons sur une gamme spécifique de cas d’usage qui couvrent la prévision de l’avenir, à court terme et à long terme à la demande d’énergie. Nous travaillent dans ces domaines pendant quelques mois et avez acquis des connaissances et les compétences qui nous permet de produire des résultats de qualité industrie. Autres cas d’utilisation seront traités ainsi que dans le document dans un avenir proche.

## <a name="business-understanding"></a>Présentation de l’entreprise

### <a name="business-goals"></a>Objectifs de l’entreprise
L’objectif de **Démonstration de l’énergie** est de montrer un classique analytique prédictive et l’apprentissage des solutions qui peuvent être déployées dans un délai très court de l’ordinateur. Plus précisément, notre objectif en cours est sur l’activation des solutions de prévision de la demande énergétique afin que sa valeur commerciale peut être rapidement réalisé et exploitée sur. Les informations contenues dans ce manuel peuvent aider le client à atteindre les objectifs suivants :
-   Solution basée sur un peu de temps à valeur d’apprentissage automatique
-   Possibilité de développer un projet pilote utiliser cas pour d’autres exemples d’utilisation, ou à une portée plus large en fonction de leurs besoins de l’entreprise
-   Rapidement connaissance des produits Cortana Intelligence Suite

Avec ces objectifs à l’esprit, ce manuel vise à fournir de l’entreprise et les connaissances techniques qui vous aideront à atteindre ces objectifs.

### <a name="power-load-and-demand-forecasting"></a>Charge d’alimentation et de la prévision de la demande
Dans le secteur de l’énergie, il peut exister plusieurs façons dans la demande de prévision peut aider à résoudre les problèmes critiques de l’entreprise. En fait, la prévision de la demande peut être considéré comme le fondement de nombreux cas d’usage principaux dans l’industrie. En général, nous prenons en compte deux types de prévisions de la demande d’énergie : à court terme et à long terme. Chacun d’eux peut répondre à un objectif différent et utilisent une approche différente. La principale différence entre les deux est l’horizon de prévision, c'est-à-dire l’intervalle de temps dans le futur pour lequel nous serait de prévision.

#### <a name="short-term-load-forecasting"></a>Court terme charge prévision
Dans le contexte de la demande énergétique, court terme charger les prévisions (STLF) est défini comme la charge agrégée qui est prévue dans un avenir proche des différentes parties de la grille (ou de la grille dans son ensemble). Dans ce contexte, à court terme est défini pour être l’horizon temporel dans la plage de 1 heure à 24 heures. Dans certains cas, un horizon de 48 heures est également possible. Par conséquent, STLF est très courant dans un cas d’utilisation opérationnelle de la grille. Voici quelques exemples de STLF par les cas d’usage :
-   Approvisionnement et demande l’équilibrage
-   Prise en charge de la négociation d’alimentation
-   Fabrication de marché (prix de puissance de paramètre)
-   Optimisation opérationnelle de grille
-   [Réponse de la demande](https://en.wikipedia.org/wiki/Demand_response)
-   Nombre maximal de prévision de la demande
-   Gestion de la demande
-   L’équilibrage de charge et de prévention de surcharge
-   À long terme prévision de charge
-   Détection des pannes et anomalies
-   Réduction/audit de pointe 

Modèle STLF sont essentiellement basés sur le passé proche (dernier jour ou semaine) relatives à la consommation et l’utilisation prévue de température sous la forme d’un important predictor. Obtention de prévision de la prochaine heure precise de la temperature et jusqu'à 24 heures devient moins compliqué jours d’aujourd'hui. Ces modèles sont moins sensibles aux saisonniers ou des tendances de consommation à long terme.

SLTF solutions sont également susceptibles de générer un volume élevé d’appels de prévision (demandes de service) dans la mesure où elles sont appelées sur une base horaire et dans certains cas même avec une fréquence plus élevée. Il est également très courant de voir d’implantation où chaque sous-station individuelle ou le transformateur est représentée sous la forme d’un modèle autonome et par conséquent, le volume de requêtes de prévision sont encore plus.

#### <a name="long-term-load-forecasting"></a>À long terme prévision de charge
L’objectif de longue durée charge prévision (LTLF) est à la prévision de la demande de puissance avec un horizon de temps allant de 1 semaine à plusieurs mois (et dans certains cas pour un nombre d’années). Cette plage de horizon est principalement applicable pour la planification et le placement des cas d’usage.

Pour les scénarios à long terme, il est important de disposer de données de haute qualité qui couvre une période de plusieurs années (minimum 3 ans). Ces modèles seront généralement extraire les modèles du caractère saisonnier des données historiques et utiliser des predicators externes tels que les modèles météorologiques et climatiques.

Il est important de clarifier que plus l’horizon de prévision, plus la prévision peut être moins précise. Il est donc important de créer des intervalles de confiance avec les prévisions réelles qui permettrait à l’homme factoriser la variante possible dans leur processus de planification.

Étant donné que le scénario de la consommation pour LTLF est principalement de planification, nous pouvons nous attendre bien inférieurs volumes de prévision (par rapport à STLF). Nous verriez normalement ces prévisions incorporées dans des outils de visualisation tels que Excel ou PowerBI et être appelés manuellement par l’utilisateur.

### <a name="short-term-vs-long-term-prediction"></a>Prédiction de court terme et Long terme
Le tableau suivant compare les STLF et LTLF en ce qui concerne les attributs plus importants :

|Attribut|Prévision de charge de court terme|À long terme des prévisions de charge|
|---|---|---|
|Horizon de prévision|À partir de 1 heure à 48 heures|De 1 à 6 mois ou plus|
|Granularité des données|Toutes les heures|Toutes les heures ou tous les jours|
|Scénarios d’utilisation classiques|<ul><li>L’équilibrage de la demande/approvisionnement</li><li>Choisissez la prévision d’heures</li><li>Réponse de la demande</li></ul>|<ul><li>Planification à long terme</li><li>Planification des ressources de grille</li><li>Planification des ressources</li></ul>|
|PRÉDICTEURS classiques|<ul><li>Jour ou par semaine</li><li>Heure de la journée</li><li>Température horaire</li></ul>|<ul><li>Mois de l’année</li><li>Jour du mois</li><li>Température de long terme et climat</li></ul>|
|Plage de données historiques|Deux à trois années de données|5 à 10 années de données|
|Précision par défaut|MAPE * inférieure ou égale à 5 %|MAPE * inférieure ou égale à 25 %|
|Fréquence de prévision|Produit toutes les heures ou toutes les 24 heures|Produit une fois, mensuel, trimestriel ou annuel|
\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – erreur de pourcentage moyen moyenne

Comme le montre ce tableau, il est très important de faire la distinction entre court et long terme prévision des scénarios comme ceux-ci représentent différents besoins professionnels et peut-être de déploiement différentes et consommation.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Cas d’usage exemple 1 : eSmart systèmes – optimisation de surcharge
Est dynamiquement à un rôle important d’une [grille active](https://en.wikipedia.org/wiki/Smart_grid) et constamment optimiser et ajuster les habitudes de consommation changent. La consommation d’énergie peut être affectée par les modifications apportées à court terme qui sont principalement provoquées par les fluctuations de température (*par exemple*, plus de puissance est utilisée de climatisation ou de chauffage). Dans le même temps, la consommation d’énergie est également influencée par les tendances à long terme. Il peut s’agir des effets du caractère saisonnier, jours fériés, croissance à long terme de la consommation et facteurs même économiques tels que les index de consommateur, le prix de l’huile et le PIB.

Dans ce cas d’usage, [eSmart](http://www.esmartsystems.com/) souhaite déployer une solution basée sur le cloud qui permet de prédire la propension d’une situation de surcharge sur les sous-stations donnée de la grille. En particulier, eSmart souhaite identifier des sous-stations susceptibles de surcharge dans l’heure qui suit, pour une action immédiate qui pourrait être prise pour éviter ou résoudre ce problème.

Un texte précis et exécution rapide de prévision nécessite l’implémentation de trois modèles de prévision :
-   Modèle de long terme qui permet la prévision de consommation d’énergie sur chaque sous-station au cours suivant de quelques semaines ou mois
-   Modèle à court terme qui permet la prévision de situation de surcharge sur une sous-station donnée au cours de la prochaine heure
-   Modèle de température qui fournit des prévisions de température futur sur plusieurs scénarios

L’objectif du modèle à long terme est de classer les sous-stations par leur propension à surcharger (compte tenu de leur capacité de transmission de puissance) au cours de la semaine ou du mois suivant. Cela permet la création d’une liste courte des sous-stations qui répondront en tant qu’entrée pour la prévision à court terme. Comme la température est un important predictor pour le modèle à long terme, il est nécessaire pour produire des prévisions de température de scénario multiples et les flux en tant qu’entrée dans le modèle à long terme en permanence. La prévision à court terme est ensuite appelée pour prédire les sous-stations sont susceptible de surcharge dans l’heure qui suit.

Les modèles à court terme et à long terme sont déployés individuellement par chaque sous-station. Par conséquent, l’exécution de pratique de ces modèles nécessite d’orchestration complète. Pour obtenir une précision supérieure prévision à court terme, un modèle plus granulaire est dédié pour chaque heure de la journée. Tous ces modèles exécutent toutes les heures et terminer l’exécution de quelques minutes pour laisser suffisamment de temps répondre et prendre des actions préventives, si nécessaire. Cet ensemble de modèles est mis à jour par la réadaptation périodique à l’aide de données les plus récentes.

Vous trouverez plus d’informations sur ce cas d’usage [ici](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Utilisation de critères de Qualification de cas – conditions préalables
Le principal atout de l’Intelligence de Cortana est dans sa capacité puissante pour déployer et mettre à l’échelle machine centré sur les formations. Il est conçu pour la prise en charge des milliers de prévisions sont exécutés simultanément. Il peut automatiquement mettre à l’échelle afin de répondre à un modèle de consommation changent. Le focus de la solution est donc sur les performances de calcul et de précision. Par exemple, une société est intéressée dans la production de la demande d’énergie précis prévision de la prochaine heure et pour chaque heure de la journée. D’autre part, nous intéresse moins répondre à la question de la raison pour laquelle la demande est prévue soit telle qu’elle est (le modèle lui-même s’occupera de qui).

Par conséquent, il est important de réaliser que pas tous les cas d’usage et les problèmes de l’entreprise peuvent être résolus efficacement à l’aide d’apprentissage automatique.

Intelligence de Cortana et à l’apprentissage de l’ordinateur peuvent être très efficaces dans la résolution d’un problème d’une entreprise donnée lorsque les conditions suivantes sont réunies :
-   Le problème de l’entreprise en main est **prédictive** par nature. Un exemple de cas d’utilisation prédictive est une société qui souhaite de prédire la charge sur une sous-station donnée au cours de la prochaine heure. D’autre part, les analyse et classement des pilotes de la demande historique serait **descriptif** par nature et par conséquent moins applicables.
-   Il existe un clair **chemin d’accès de l’action** à entreprendre lorsque la prévision est disponible. Par exemple, le prévoir une surcharge sur une sous-station lors de la prochaine heure peut déclencher une action proactive de réduire la charge associée à cette sous-station et empêchant ainsi potentiellement une surcharge.
-   Le cas d’usage représente un **type classique de problème** tel qui, lorsqu’il a résolu peut ouvrir la voie à la résolution des autres similaires cas d’usage.
-   Le client peut définir **les objectifs quantitatifs et qualitatifs** pour illustrer une mise en œuvre de la solution réussie. Par exemple, un objectif quantitatif pour la prévision de la demande d’énergie est le seuil de précision requise (*par exemple*, jusqu'à 5 % erreur est autorisé) ou lorsque prédiction sous-station surcharge puis la précision (taux de positifs trues) et rappel (étendue de positifs trues) doit être supérieure à un seuil donné. Ces objectifs doivent provenir des objectifs de l’entreprise du client.
-   Il existe un **scénario d’intégration** de clair avec workflow d’entreprise de la société. Par exemple, la prévision de charge de sous-station peut être intégrée dans le centre de contrôle de grille pour autoriser des activités de prévention de surcharge.
-   Le client est prêt à utiliser des **données avec une qualité suffisante** pour prendre en charge les cas d’usage (voir plus dans la section suivante, la **Qualité des données**, de ce manuel).
-   Le client s’appuie sur l’architecture de cloud centré sur les données ou **en nuage de machine de formation**, y compris ML d’Azure et d’autres composants de Cortana Intelligence Suite.
-   Le client est prêt à établir **un flux de données de bout en bout** qu’installations la livraison des données dans le nuage sur une base continue et est disposé à **mettre** la solution.
-   Le client est prêt à **consacrer des ressources** qui va être activement au cours de la mise en œuvre pilote initial afin que les connaissances et la propriété de la solution peuvent être transférés au client en cas de réussite.
-   La ressource client doit être un **Professionnel de données qualifiés**, scientifique, de préférence, données.

Qualification d’un cas d’usage en fonction des critères ci-dessus peut considérablement améliorer les taux de réussite d’un cas d’usage et établir une bonne beachhead pour l’exécution du cas d’une utilisation ultérieure.

### <a name="cloud-based-solutions"></a>Solutions basées sur le nuage
Cortana Intelligence Suite sur Azure est un environnement intégré qui se trouve dans le nuage. Le déploiement d’une solution avancée d’analytique dans un environnement de cloud conserve des avantages substantiels pour les entreprises et en même temps peuvent signifier un changement important pour les entreprises qui utilisent encore des solutions d’informatique sur site. Dans le secteur de l’énergie, il existe une tendance nette de la migration progressive des opérations dans le nuage. Cette tendance va de pair avec le développement de la grille dynamique, comme indiqué plus haut, **Les tendances du secteur**. Ce manuel se concentre sur une solution de cloud dans le domaine de l’énergie, il est important d’expliquer les avantages et autres considérations de déploiement d’une solution basée sur le nuage.

Le principal avantage d’une solution basée sur le nuage est sans doute le coût. En tant que solution utilise les composants déployés par le nuage, il n’y a aucun initial les coûts ou coût des marchandises vendues (coût des marchandises vendues) composant associé. Cela signifie qu’il est inutile d’investir dans le matériel, de logiciels et de maintenance informatique, et est par conséquent une réduction substantielle des risques pour l’entreprise.

Un autre avantage important est la structure de coûts de paiement à l’utilisation de solutions basées sur le nuage. Déploiement et mise à l’échelle sur une base juste nécessaire en nuage de serveurs de stockage ou de l’informatique. Cette valeur représente l’avantage de l’efficacité de coût d’une solution basée sur le nuage.

Enfin, il n’est pas nécessaire d’investir dans le développement de la future infrastructure ou de maintenance informatique comme tout cela fait partie de l’offre de nuage. À cet égard, Cortana Intelligence Suite inclut le meilleur dans les services de la classe et sa carte routière conserve en constante évolution. Nouvelles fonctionnalités, les composants et fonctionnalités sont introduites en permanence et évoluent.

Pour une société qui commence sa transition vers le nuage, nous recommandons vivement de prendre une approche progressive en implémentant un plan de migration de nuage. Nous pensons que pour les utilitaires et les sociétés dans le domaine de l’énergie, les cas d’usage qui sont décrits dans ce manuel représentent une excellente opportunité pour le pilotage des solutions d’analytique prédictive dans le nuage.

#### <a name="business-case-justification-considerations"></a>Considérations relatives à la Justification de cas professionnelle
Dans de nombreux cas, le client peut être intéressé dans la fabrication d’une justification pour un cas d’utilisation dans lequel une solution basée sur le nuage et l’apprentissage automatique sont des composants importants. Contrairement à une solution sur site, dans le cas d’une solution basée sur le nuage, le composant de coût initial est minime, et la plupart des éléments de coût est associée à l’activité réelle. Lorsqu’il s’agit de déployer une solution sur Cortana Intelligence Suite de prévision d’énergie, plusieurs services peuvent être intégrés avec une structure de coût commun unique. Par exemple, les bases de données (*par exemple*, SQL Azure) peuvent être utilisés pour stocker les données brutes et pour les prévisions réelles Azure ML est ensuite utilisée pour héberger les services de prévisions. Dans cet exemple, la structure des coûts pourrait inclure des composants transactionnels et stockage.

D’autre part, une doit avoir une bonne compréhension de la valeur commerciale de fonctionnement d’une demande d’énergie prévision (court ou long terme). En fait, il est important de comprendre la valeur commerciale de chaque opération de prévision. Par exemple, avec précision des prévisions de charge pour les prochaines 24 heures peuvent empêcher la surproduction ou peuvent d’éviter les surcharges sur la grille et cela peut être évalué en termes d’économies sur une base quotidienne.

La solution de prévision d’une formule de base pour le calcul de l’avantage financier de la demande : ![solution de prévision de formule de base pour le calcul de l’avantage financier de la demande](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Cortana Intelligence Suite fournit un modèle de tarification de paiement, il est inutile d’engager un composant de coût fixe pour cette formule. Cette formule peut être calculée sur une base quotidienne, mensuelle ou annuelle.

En cours Cortana Intelligence Suite et ML Azure tarifications sont disponibles [ici](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Processus de développement de solution
Le cycle de développement d’une demande d’énergie prévision solution implique généralement 4 phases, dans lesquelles nous rendre l’utilisation des technologies en nuage et les services au sein de la Suite d’Intelligence Cortana.

Ceci est illustré dans le diagramme suivant :

![Cycle de grille Smart](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Le paragraphe suivant décrit ce processus en 4 étapes :

1.  **Collecte de données** – toute solution analytique avancée en fonction s’appuie sur des données (voir la **Présentation des données**). En particulier, lorsqu’il s’agit d’analytique de prévision et les prévisions, nous reposent sur des flux continu et dynamique de données. Dans le cas de la prévision de la demande énergétique, ces données peuvent provenir directement à partir de compteurs actives ou déjà être regroupé en agrégats sur une base de données sur prem. Nous utilisons également sur d’autres sources externes de données, telles que la météo et de la température. Ce flux continu de données doit être orchestré, planifié et stocké. [Usine de données Azure](http://azure.microsoft.com/services/data-factory/) (CAD) est notre principal pour accomplir cette tâche.
2.  **Modélisation** – pour précise et fiable de l’énergie prévoit, une doit développer (train) et gérer un modèle idéal que l’utilisation de données historiques et extrait les modèles significatives et prédictives dans les données. La zone de la Machine Learning (ML) a augmenté avec des algorithmes plus avancés en cours de développement régulièrement. Azure ML Studio propose une expérience utilisateur satisfaisante qui permet d’utiliser les algorithmes ML plus avancées au sein d’un flux de travail terminée. Ce flux de travail est illustrée dans un diagramme de flux intuitif ainsi que la préparation des données, fonction d’extraction, la modélisation et évaluation du modèle. L’utilisateur peut extraire des centaines de modèles différents qui sont inclus dans cet environnement. À la fin de cette phase, un chercheur de données aura un modèle de travail entièrement évaluée et prêt pour le déploiement.

    Le schéma suivant est une illustration d’un flux de travail typique :

    ![Modélisation de Workflow](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)

3.  **Déploiement** – avec un modèle de travail en cours, l’étape suivante est déploiement. Ici, le modèle est converti en un service web qui expose une API RESTful qui peut être appelé simultanément sur Internet à partir de différents clients de consommation. ML Azure offre un moyen simple de déployer un modèle directement à partir de Studio Azure ML d’un simple clic d’un bouton. Le processus de déploiement tout se passe en coulisses. Cette solution peut automatiquement mettre à l’échelle pour répondre à la consommation requise.

4.  **Consommation** – au cours de cette phase, nous rendre utilisent le modèle de prévision pour produire des prévisions. La consommation peut être piloté à partir d’une application utilisateur*(par exemple*, le tableau de bord) ou directement à partir d’un système opérationnel tel que demande / d’approvisionnement équilibrage de système ou une solution d’optimisation de grille. Plusieurs cas d’usage peuvent être pilotées à partir d’un modèle unique.

## <a name="data-understanding"></a>Présentation de données
Après avoir couvert les considérations d’entreprise (voir la section **Présentation de l’entreprise**) d’une solution de prévision de la demande d’énergie, nous sommes maintenant prêts à discuter de la partie des données. Toute solution analytique prédictive s’appuie sur des données fiables. Pour la prévision de la demande énergétique, nous s’appuient sur des données historiques de consommation avec différents niveaux de granularité. Que des données historiques sont utilisées comme matière première. Elle est soumises à une analyse approfondie dans lequel le chercheur de données identifiera PRÉDICTEURS (également appelés en tant que fonctions) qui peuvent être placés dans un modèle qui génère finalement les prévisions requises.

Dans le reste de cette section, nous décrirons les différentes étapes et comprendre les données et comment afficher un formulaire utilisable.

### <a name="the-model-development-cycle"></a>Le Cycle de développement de modèle
Production de modèles nécessite une préparation minutieuse de la prévision et planification. Décomposer en plusieurs étapes du processus de modélisation et de se concentrer sur une seule étape à la fois peuvent améliorer considérablement le résultat de l’ensemble du processus.

Le diagramme suivant illustre la façon dont le processus de modélisation peut être divisé en plusieurs étapes :

![Modèle du Cycle de développement](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Comme le montre que le cycle comprend six étapes :
-   Formulation de problème
-   Réception de données et de l’exploration de données
-   Préparation des données et l’ingénierie de la fonctionnalité
-   Modélisation
-   Évaluation du modèle
-   Développement

Dans le reste de cette section, nous décrirons les étapes individuelles et les éléments à prendre en compte à chaque étape.

### <a name="problem-formulation"></a>Formulation de problème
Nous pouvons envisager la formulation du problème comme l’étape la plus importante à prendre avant d’implémenter une solution analytique prédictive. Ici nous transforme le problème de l’entreprise et elle se décomposer à des éléments spécifiques qui peuvent être résolus à l’aide des données et de techniques de modélisation. Il est préférable de formuler le problème comme un ensemble de questions, nous pouvons répondre. Voici quelques questions possibles pouvant être appliquées dans le cadre de la prévision de la demande d’énergie :
-   Quelle est la charge prévue sur une sous-station individuelle dans l’heure ou le jour suivant ?
-   À quelle heure de la journée ma grille recevront les crêtes de demande ?
-   Quelle est la probabilité ma grille pour supporter la charge maximale attendue ?
-   La quantité d’énergie doit générer la centrale lors de chaque heure de la journée ?

Formulation de ces questions nous permet de se concentrer sur les bonnes données lors de l’obtention et la mise en œuvre d’une solution entièrement aligné avec le problème de l’entreprise à portée de main. En outre, nous pouvons définir des audits clés qui nous permettent d’évaluer les performances du modèle. Par exemple, précise comment les prévisions convient et quelle est la plage de l’erreur qui serait acceptable par l’entreprise ?

### <a name="data-sources"></a>Sources de données
La grille dynamique moderne collecte des données à partir de différentes parties et les composants de la grille. Ces données représentent les divers aspects de l’opération et de l’utilisation de la grille de puissance. Dans le cadre de la prévision de la demande d’énergie, nous limitons la discussion sur les sources de données qui reflètent la consommation de la demande réelle. Une importante source de consommation d’énergie sont actives mètres. Utilitaires dans le monde déploient rapidement mètres actives pour leurs clients. Compteurs Smart enregistrent la consommation d’énergie réelle et constamment relaient des données à la société. Données sont collectées et envoyées à l’intervalle fixe, comprise entre 5 minutes et 1 heure. Les compteurs actives plus avancées peuvent être programmés à distance permettant de contrôler et d’équilibrer la consommation réelle au sein d’un ménage. Les données de compteur Smart sont relativement fiables et inclut la date et l’heure. Qui la rendent un élément important pour la prévision de la demande. Les données de compteur peuvent être agrégées (totalisées) à différents niveaux dans la topologie de la grille : transformateur, sous-station, la région, *etc.*. Nous pouvons ensuite choisir le niveau d’agrégation requis pour construire un modèle de prévision pour elle. Par exemple, si la société souhaite prévision de charge future sur chacun de ses sous-stations de grille puis données des tous les compteurs peuvent être agrégées pour chaque sous-station individuelle et utilisées comme entrée pour le modèle de prévision. Nous faisons référence aux compteurs smart comme source de données interne.

Une prévision de la demande d’énergie fiable reposera également sur d’autres sources de données externes. Un facteur important qui affecte la consommation d’énergie est la météo, ou plus précisément la température. Les données historiques montrent la corrélation entre la température extérieure et de la consommation d’énergie. Au cours des jours de l’été à chaud, rendre les consommateurs utilisent de leur conditionnement d’air et au cours de la puissance d’hiver sur les systèmes de chauffage. Une source fiable des températures historiques à l’emplacement de la grille est donc clé. En outre, nous utilisons également des prévisions précises de température comme PRÉDICTEUR de la consommation d’énergie.

Autres sources de données externes peuvent également aider dans la création de modèles de prévision de la demande énergétique. Il peut s’agir à long terme les changements climatiques, les index économiques (*par exemple*, le PIB) et autres. Dans ce document nous n’inclut pas ces autres sources de données.

### <a name="data-structure"></a>Structure de données
Après avoir identifié les sources de données requises, nous souhaitons vous assurer que les données brutes collectées incluent les fonctionnalités de données correctes. Pour créer un modèle de prévision de la demande fiable, il nous faudrait pour s’assurer que les données collectées incluent des éléments de données qui peuvent aider à prédire la future de la demande. Voici certaines exigences de base relatives à la structure de données (schéma) de données brutes.

Les données brutes se comprennent de lignes et de colonnes. Chaque mesure est représenté sous la forme d’une seule ligne de données. Chaque ligne de données contient plusieurs colonnes (également appelées fonctionnalités ou des champs).

1.  **Horodatage** – le champ timestamp représente le temps réel, où la mesure a été enregistrée. Il doit respecter un des formats de date/heure courante. Les parties de la date et l’heure doivent être inclus. Dans la plupart des cas, il n’est pas nécessaire pour le temps d’être enregistrées jusqu'à ce que le deuxième niveau de granularité. Il est important de spécifier le fuseau horaire dans lequel les données sont enregistrées.
2.  **ID compteur** - ce champ identifie le compteur ou l’unité de mesure. Il est une variable catégorique et peut être une combinaison de chiffres et de caractères.
3.  La **valeur de consommation** – il s’agit de la consommation réelle à une heure donnée. La consommation peut être mesurée en kWh (kilowatt-hour) ou toute autre préférence unités. Il est important de noter que l’unité de mesure doit maintenir une cohérence entre toutes les mesures dans les données. Dans certains cas, plus 3 phases d’alimentation peut être fournie sur la consommation. Dans ce cas, nous faudrait collecter toutes les phases de consommation indépendant.
4.  **Température** : la température est généralement collectée à partir d’une source indépendante. Toutefois, il doit être compatible avec les données de consommation. Elle doit inclure un horodateur comme décrit ci-dessus qui lui permettra d’être synchronisées avec les données de consommation réelle. La valeur de la température peut être spécifiée en degrés Celsius ou Fahrenheit mais doit rester cohérente sur toutes les mesures.
5.  **Emplacement :** Le champ d’emplacement est généralement associé à l’endroit où les données de température a été collectées. Elle peut être représentée sous la forme d’un numéro de code postal ou latitude/longitude / (longitude).

Le tableau suivant présente des exemples d’un bon format de données de la consommation et de la température :

|**Date**|**Heure**|**ID compteur**|**Phase 1**|**Phase 2**|**Phase 3**|
|--------|--------|------------|-----------|-----------|-----------|
|7/1/2015|10:00:00|ABC1234     |7.0        |2.1        |5.3        |
|7/1/2015|10:00:01|ABC1234     |7.1        |2.2        |4.3        |
|7/1/2015|10:00:02|ABC1234     |6.0        |2.1        |4.0        |

|**Date**|**Heure**|**Emplacement**|**Température**|
|--------|--------|-------------|---------------|
|7/1/2015|10:00:00|11242        |24,4           |
|7/1/2015|10:00:01|11242        |24,4           |
|7/1/2015|10:00:02|11242        |24,5           |

Comme indiqué ci-dessus, l’exemple contient 3 valeurs différentes pour la consommation associée à 3 phases d’alimentation. Notez également que les champs de date et d’heure sont séparés, toutefois ils peuvent également être combinées en une seule colonne. Dans ce cas, la colonne emplacement est représentée dans un format de code postal à 5 chiffres et de la température dans un format degré Celsius.

### <a name="data-format"></a>Format de données
Cortana Intelligence Suite peut prendre en charge les formats de données les plus courants comme CSV, TSV, JSON, *etc.*. Il est important que le format de données reste cohérent pour le cycle de vie du projet.

### <a name="data-ingestion"></a>Réception de données
Étant donné que la prévision de la demande d’énergie est constamment et fréquence prévue, nous devons nous assurer que les données brutes sont transmis au moyen d’un processus d’acquisition de données solide et fiable. Le processus d’acquisition doit garantir que les données brutes sont disponibles pour les processus de prévision au moment requis. Cela implique que la fréquence de réception de données doit être supérieure à la fréquence de prévision.

Par exemple : Si notre demande de solution de prévision générerait une nouvelle prévision à 8 h 00 tous les jours, puis il faut s’assurer que toutes les données qui ont été collectées au cours des dernières 24 heures a été entièrement ingérées jusqu'à ce point et a même incluent des données de la dernière heure.

Pour ce faire, Cortana Intelligence Suite propose différentes façons de prendre en charge d’un processus d’acquisition de données fiables. Cela va être examinée plus en détail dans la section « **déploiement** » de ce document.

### <a name="data-quality"></a>Qualité des données
La source de données brutes est nécessaire pour effectuer la prévision de la demande de fiable et précise doit répondre aux certains critères de qualité des données de base. Bien des méthodes statistiques avancées peuvent être utilisés pour compenser un problème de qualité de données possibles, il faut toujours s’assurer que nous sommes qui traversent un seuil de qualité des données de base lors de la réception de nouvelles données. Voici quelques considérations relatives à la qualité des données brutes :
-   **Valeur manquante** : cela fait référence à la situation spécifique n’a pas été collecté. Le principe de base est que le taux de valeur manquante ne doit pas être supérieur à 10 % pendant une période de temps donnée. Dans les cas qu’une seule valeur il manque doit le spécifier à l’aide d’une valeur prédéfinie (par exemple : « 9999 ») et non pas « 0 » qui peut être une mesure valide.
-   **Précision de la mesure** – la valeur réelle de la consommation ou à la température doivent être enregistrés avec précision. Mesures inexactes produira des prévisions erronées. En général, l’erreur de mesure doit être inférieure à 1 % par rapport à la valeur true.
-   **Moment de la mesure** – il est nécessaire que l’horodatage réelle des données collectées ne seront pas s’écarter de plus de 10 secondes par rapport à l’heure réelle de la mesure proprement dite.
-   **Synchronisation** – lorsque plusieurs sources de données sont utilisés (*par exemple*, la consommation et la température) nous devons faire en sorte que sont aucune synchronisation de l’heure des problèmes entre eux. Cela signifie que la différence de temps entre l’horodatage collecté tous deux sources de données indépendantes ne doit pas dépasser de plus de 10 secondes.
-   **Latence** : comme indiqué plus haut, la **Réception des données**, nous sommes dépendants d’un processus de flux et de la réception de données fiable. Pour que nous devons nous assurer que nous contrôler la latence des données de contrôle. Cela est spécifié en tant que la différence de temps entre le moment où la mesure a été prise et l’heure à laquelle il a été chargé dans le stockage de Cortana Intelligence Suite et est prêt à l’emploi. Pour les charge à court terme la latence totale de prévision ne doit pas être supérieure à 30 minutes. Pour les charge à long terme la latence totale de prévision ne doit pas être supérieure à 1 jour.

### <a name="data-preparation-and-feature-engineering"></a>Préparation des données et l’ingénierie de la fonctionnalité
Une fois les données brutes ont été ingéré (voir **Intégration de données**) et ont été stockées en toute sécurité, elle est prête à être traitée. La phase de préparation des données est en fait les données brutes et conversion (transformation, la mise en forme) dans un formulaire pour la phase de modélisation. Qui peut inclure des opérations simples, notamment à l’aide de la colonne de données brutes en est à sa valeur mesurée réelle, valeurs de standardisé, plus complexe des opérations telles que le [décalage de l’heure](https://en.wikipedia.org/wiki/Lag_operator)et d’autres. Les colonnes de données nouvellement créée sont appelés fonctionnalités de données et le processus de génération de ces est appelé ingénierie de fonctionnalité. À la fin de ce processus, nous aurions un nouveau jeu de données qui a été dérivé à partir des données brutes et peut être utilisé pour la modélisation. En outre, la phase de préparation des données doit prendre en charge les valeurs manquantes (voir la **Qualité des données**) et de les compenser. Dans certains cas, nous devez également de normaliser les données pour vous assurer que toutes les valeurs sont représentées dans la même échelle.

Dans cette section, de que nous indiquons certains des fonctionnalités de données courantes qui sont incluses dans l’énergie modèles de prévision de la demande.

**Temps écoulé fonctionnalités :** Ces fonctionnalités sont dérivées à partir des données date/timestamp. Elles sont extraites et convertis en fonctionnalités par catégorie, telles que :
-   Heure de la journée – c’est l’heure de la journée qui prend des valeurs de 0 à 23
-   Jour de semaine – cela représente le jour de la semaine et accepte des valeurs allant de 1 (dimanche) à 7 (samedi)
-   Jour du mois – cela représente la date réelle et peut prendre les valeurs de 1 à 31
-   Mois de l’année – cela représente le mois et accepte des valeurs allant de 1 (janvier) à 12 (décembre)
-   Week-end – il s’agit d’une fonction de valeur binaire qui prend la valeur 0 pour les jours de la semaine ou 1 pour le week-end
-   Vacances - il s’agit d’une fonction de valeur binaire qui prend la valeur 0 pour un jour normal ou 1 pour un jour férié
-   Conditions de Fourier – les termes de Fourier sont des poids qui sont dérivées de l’horodatage et sont utilisés pour capturer le caractère saisonnier (cycles) dans les données. Dans la mesure où nous pouvons ont plusieurs saisons dans nos données, nous devons plusieurs termes de Fourier. Par exemple, les valeurs de la demande peuvent avoir quotidiens, hebdomadaires et annuels saisons/de cycles qui se traduit en termes de Fourier 3.

**Fonctionnalités de mesure indépendant :** Les fonctionnalités indépendantes incluent tous les éléments de données que nous souhaitons utiliser comme variables explicatives dans notre modèle. Ici nous exclure la fonctionnalité dépendante qui nous devra prévoir.
-   Fonctionnalité de retard – il s’agit d’horaire décalé vers les valeurs de la demande réelle. Par exemple, les fonctionnalités de retard 1 contiendra la valeur de la demande dans l’heure précédente (en supposant les données horaires) par rapport à l’horodatage actuel. De la même façon, nous pouvons ajouter un retard de 2, un retard de 3, *etc.*. La combinaison réelle des fonctionnalités de retard qui sont utilisées sont déterminés lors de la phase de modélisation par l’évaluation des résultats du modèle.
-   Tendances à long terme – cette fonction représente la croissance linéaire de la demande entre les années.

**Fonctionnalité dépendante :** La fonctionnalité dépendante est la colonne de données qui nous aimerions notre modèle à prévoir. Avec l' [apprentissage de machine de mise sous contrôle](https://en.wikipedia.org/wiki/Supervised_learning), nous devons tout d’abord former le modèle en utilisant les fonctionnalités dépendantes (qui est également désignée comme étiquettes). Ainsi, le modèle découvrir les modèles dans les données associées à la fonctionnalité dépendante. En prévision de la demande énergétique nous souhaitons généralement prévoir la demande réelle et nous serait par conséquent l’utiliser comme la fonctionnalité dépendante.

**Gestion des valeurs manquantes :** Pendant la phase de préparation de données, il faut déterminer la meilleure stratégie pour gérer les valeurs manquantes. Généralement cela en utilisant les différentes [méthodes d’imputation de données](https://en.wikipedia.org/wiki/Imputation_(statistics))de statistiques. Dans le cas de la prévision de la demande d’énergie, nous imputer généralement des valeurs manquantes à l’aide d’une moyenne mobile de points de données disponibles précédents.

**La normalisation des données :** Normalisation des données est un autre type de transformation qui est utilisé pour importer toutes les données numériques comme les prévisions de la demande d’une échelle similaire. Cela permet généralement d’améliorer la précision et la précision du modèle. Serait en général cela nous en divisant la valeur réelle par la plage de données.
Cela mettra à l’échelle la valeur d’origine vers le bas dans une plage plus petite, généralement entre -1 et 1.

## <a name="modeling"></a>Modélisation
La phase de modélisation est la conversion des données dans un modèle de lieu. Au cœur de ce processus, il sont des algorithmes qui analyse les données d’historique (données de formation), extraire les modèles et générer un modèle. Ce modèle peut servir ultérieurement de prévoir de nouvelles données qui n’a pas été utilisées pour construire le modèle.

Une fois que nous avons un modèle de travail fiable, nous pouvons utiliser pour évaluer les nouvelles données sont structurées de manière à inclure les fonctionnalités requises (X). Le processus de notation rendra utilisent le modèle persistant (objet à partir de la phase de formation) et de prévoir la variable cible qui est désignée par Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Techniques de modélisation de la prévision de la demande
Dans le cas de la demande de prévisions nous rendre l’utilisation de données historiques, qui sont classées par heure. Généralement, nous faisons référence à des données qui inclut la dimension de temps en tant que [séries temporelles](https://en.wikipedia.org/wiki/Time_series). L’objectif de la modélisation série est de trouver connexes des tendances, caractère saisonnier, auto-correlation (corrélation dans le temps), du temps et de formuler dans un modèle.

Ces dernières années, des algorithmes avancés ont été développés pour prendre en charge les prévisions de temps série et à améliorer l’exactitude de la prévision. Nous Présentez brièvement quelques-uns ici.

> [AZURE.NOTE] Cette section n’est pas destinée à être utilisé en tant que machine d’apprentissage et de la prévision de vue d’ensemble, mais plutôt comme un court sondage de modélisation techniques qui sont couramment utilisées pour la prévision de la demande. Pour plus d’informations et de fournitures scolaires sur les prévisions de temps série, nous vous recommandons de la documentation en ligne [prévisions : principes et pratiques](https://www.otexts.org/book/fpp).

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (moyenne mobile)**](https://www.otexts.org/fpp/6/2)
Moyenne de déplacement est une des techniques analytiques premier qui a été utilisé pour la prévision de temps série et il est toujours parmi les plus couramment utilisés techniques à compter d’aujourd'hui. C’est également la base pour plus avancées techniques de prévision. Avec la moyenne mobile nous sommes prévisions le prochain point de données en faisant la moyenne sur les points les plus récents K, où K représente l’ordre de la moyenne mobile.

La technique de moyenne de déplacement a pour effet de lissage de la prévision et par conséquent peut ne pas gérer la volatilité bien grande dans les données.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (lissage exponentiel)**](https://www.otexts.org/fpp/7/5)
Lissage exponentiel (ETS) est une famille de différentes méthodes qui utilisent la moyenne pondérée des points de données récents afin de prévoir le prochain point de données. L’idée est d’affecter le poids est supérieur aux valeurs plus récentes et de diminuer progressivement ce poids pour les anciennes valeurs mesurées. Il existe un certain nombre de méthodes différentes avec cette famille, certains d'entre eux la gestion du caractère saisonnier des dans les données de [Méthode saisonnières des hivers dépourvus de l’Holt](https://www.otexts.org/fpp/7/5).

Certaines de ces méthodes est aussi prendre en compte le caractère saisonnier des données.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (Auto régression intégré moyenne mobile)**](https://www.otexts.org/fpp/8)
Auto régression intégré déplacement moyen (ARIMA) est une autre famille de méthodes qui est couramment utilisée pour la série de temps de prévision. Il combine pratiquement méthodes d’auto-régression avec la moyenne mobile. Les méthodes d’auto-régression utilisent des modèles de régression en prenant les valeurs précédentes de la série de temps afin de calculer le point de date suivant. Méthodes ARIMA s’appliquent également la différenciation des méthodes qui incluent le calcul de la différence entre les points de données et celles au lieu de la valeur mesurée d’origine à l’aide de. Enfin, ARIMA utilise également des techniques moyennes mobiles décrits ci-dessus. La combinaison de toutes ces méthodes de diverses façons est ce qui construit la famille de méthodes ARIMA.

ETS et ARIMA sont largement utilisés aujourd'hui pour la prévision de la demande d’énergie et de nombreux autres problèmes de prévisions. Dans de nombreux cas, elles sont combinées pour fournir des résultats très précis.

**Régression de grand Multiple** Modèles de régression pourraient être l’approche de modélisation plus importante dans le domaine de l’apprentissage automatique et les statistiques. Dans le cadre de la série temporelle régression nous permet de prévoir les valeurs futures (*par exemple*, de la demande). De régression nous prendre une combinaison linéaire de la COMPETENCE des moyens d’et les poids (également appelés coefficients) de ces indicateurs pendant le processus de formation. L’objectif est de produire une droite de régression qui est notre valeur prévue de prévision. Méthodes de régression sont appropriés lorsque la variable cible est numérique et par conséquent également ajuste les prévisions temps série. Il existe un large éventail de méthodes de régression, y compris des modèles de régression très simple comme [Une régression linéaire](https://en.wikipedia.org/wiki/Linear_regression) et ceux plus avancés comme les arbres de décision, [Forêts de Random](https://en.wikipedia.org/wiki/Random_forest), [Réseaux neuronaux](https://en.wikipedia.org/wiki/Artificial_neural_network)et augmentée des arbres de décision.

Création de la demande énergétique en prévision comme un problème de régression nous offre une grande flexibilité dans le choix de nos fonctions de données qui peuvent être combinées dans les facteurs externes tels que de la température et les données de série de temps de la demande réelle. Plus d’informations sur les composants sélectionnés sont présentés dans la section (voir **Préparation des données et ingénierie de fonction**) de ce manuel d’ingénierie de fonctionnalité.

À partir de notre expérience avec la mise en œuvre et de déploiement du projet pilote prévisions de la demande d’énergie, nous avons constaté que les modèles de régression avancées disponibles dans Azure ML ont tendance à produire les meilleurs résultats et nous leur utilisation.

## <a name="model-evaluation"></a>Évaluation du modèle
Évaluation du modèle a un rôle essentiel dans le **Cycle de développement de modèle**. À cette étape, nous étudierons valide le modèle et ses performances avec les données réelles. Au cours de l’étape de modélisation, nous utilisons une partie des données disponibles pour la formation du modèle. Au cours de la phase d’évaluation, nous prenons le reste des données à tester le modèle. Pratiquement, cela signifie que nous sommes alimenter les données du nouveau modèle qui a été restructurées et contient les mêmes fonctionnalités que le dataset de la formation. Toutefois, pendant le processus de validation, nous utiliser le modèle pour prédire la variable cible plutôt que de fournir la variable cible disponible. Nous font souvent référence à ce processus comme modèle de score. Puis nous utiliser les valeurs cibles de true et comparez-les avec celles prévues. L’objectif ici est de mesurer et de minimiser l’erreur de prévision, c'est-à-dire la différence entre les prévisions et la valeur true. Quantification de la mesure d’erreur d’est la clé dans la mesure où nous souhaitons ajuster le modèle et vérifier si l’erreur est effectivement diminuer. Ajustage du modèle peut être effectuée en modifiant les paramètres du modèle qui contrôlent le processus d’apprentissage, ou en ajoutant ou supprimant des fonctionnalités de données (appelées [paramètres balayage](https://channel9.msdn.com/Blogs/Windows-Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Pratiquement, cela signifie que nous devons parcourir entre le génie de fonctionnalité, de modélisation et les phases d’évaluation de modèle plusieurs fois jusqu'à ce que nous sommes en mesure de réduire l’erreur au niveau requis.

Il est important d’accentuation que l’erreur de prévision ne sera jamais zéro n’est jamais un modèle qui peut prédire parfaitement chaque résultat. Il existe toutefois une certaine magnitude d’erreur acceptable par l’entreprise. Pendant le processus de validation, nous souhaitons garantir que notre erreur un modèle de prévision est au niveau d’ou mieux que le niveau de tolérance de l’entreprise. Il est donc important de définir le niveau de l’erreur maximale tolérée au début du cycle pendant la phase de la **Formulation du problème** .

### <a name="typical-evaluation-techniques"></a>Techniques d’évaluation standard
Il existe différentes manières dans la prévision erreur pouvant être mesurée et quantifié. Dans cette section, nous allons étudier la discussion sur les techniques d’évaluation pertinents à la série time et spécifiques pour la prévision de la demande énergétique.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE est l’acronyme signifie une erreur de pourcentage absolu. Avec MAPE, nous sommes calculant la différence entre chaque prévision point et la valeur réelle de ce point. Puis, nous quantifier l’erreur par point en calculant la proportion de la différence par rapport à la valeur réelle. À la dernière étape nous moyenne de ces valeurs. La formule mathématique utilisée pour MAPE est la suivante :

![Formule de MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*où<sub>t</sub> est la valeur réelle, F,<sub>t</sub> est la valeur prévue, n est l’horizon prévision.*

## <a name="deployment"></a>Déploiement
Une fois que nous avons cerné la phase de modélisation et valider les performances de modèle que nous sommes prêts à passer à la phase de déploiement. Dans ce contexte, le déploiement signifie l’activation du client à utiliser le modèle par les prévisions réelles qu’il exécute à grande échelle. Le concept de déploiement est la clé de ML d’Azure dans la mesure où notre objectif principal consiste à appeler en permanence les prévisions, plutôt que le simple fait d’obtenir les informations à partir des données. La phase de déploiement est la partie où nous activer le modèle être consommés à grande échelle.

Dans le cadre de la prévision de la demande d’énergie, notre objectif est d’appeler continues et périodiques des prévisions tout en garantissant que les données actualisées seront disponibles pour le modèle et que les données prévisionnelles sont envoyées au client beaucoup.

### <a name="web-services-deployment"></a>Déploiement de Services Web
Le bloc de construction déployable principal dans Azure ML est le service web. C’est le moyen le plus efficace pour permettre la consommation d’un modèle de prévision dans le nuage. Le service Web encapsule le modèle et qu’il l’empaquette avec un [RESTful](http://www.restapitutorial.com/) API (Application Programming Interface). L’API peut être utilisée dans le cadre d’un code de client, comme illustré dans le schéma ci-dessous.

![Service de déploiement et la consommation](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Comme vous pouvez le constater, le service web est déployé dans le nuage Cortana Intelligence Suite et peut être appelé sur son point de terminaison reste API exposée. Différents types de clients sur différents domaines peuvent appeler le service par le biais de l’API Web simultanément. Le service web peut également évoluer pour prendre en charge des milliers d’appels simultanés.

### <a name="a-typical-solution-architecture"></a>Une Architecture de Solution par défaut
Lors du déploiement d’une demande d’énergie solution de prévision, nous sommes intéressés par le déploiement d’une solution de bout en bout qui va au-delà du service web de prévision et facilite le flux de données. Au moment de que nous appeler des prévisions, il nous faudrait pour vous assurer que le modèle est chargé avec les fonctionnalités de données à jour. Cela implique que les données brutes collectées récemment sont constamment ingérées, traitées et transformées en la fonctionnalité requise sur laquelle le modèle a été créé. Dans le même temps, nous aimerions à disposition les données prévisionnelles pour la fin d’utilisation des clients. Un exemple données cycle de flux (ou un pipeline de données) est illustrée dans le diagramme ci-dessous :

![Flux de données de bout en bout de prévision de la demande énergétique](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Voici les étapes qui se produisent dans le cadre du cycle de prévision de la demande énergétique :
1.  Des millions de mètres de données déployés en permanence génèrent des données sur la consommation d’énergie en temps réel.
2.  Ces données sont collectées et téléchargées dans un référentiel de nuage (*par exemple*, les Blob Azure).
3.  Avant d’être traitées, les données brutes sont agrégées à un niveau régional ou un sous-station tel que défini par l’entreprise.
4.  Le traitement de la fonction (voir la **Préparation des données et la fonctionnalité de traitement**) puis intervient et produit les données requises pour modéliser de formation ou d’évaluation, les données de jeu de fonctionnalité sont stockées dans une base de données (*par exemple*, SQL Azure).
5.  Le service formation de nouveau est appelé pour ré-exercer le modèle de prévision – cette version mise à jour du modèle est rendu persistant afin qu’il peut être utilisé par le service web de score.
6.  Le service web de score est appelé sur un planning qui correspond à la fréquence requise de prévision.
7.  Les données prévisionnelles sont stockées dans une base de données qui est accessible par le client de la consommation de fin.
8.  Le client de la consommation récupère les prévisions, il s’applique dans la grille et consomme selon le cas d’usage requis.

Il est important de noter que la totalité de ce cycle est totalement automatisée et s’exécute sur une planification. L’orchestration complète de ce cycle de données est possible à l’aide d’outils tels que [Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Architecture de déploiement de bout en bout
Pour pratiquement déployer une solution de prévision de la demande énergétique sur Cortana Intelligence, nous devons garantir que les composants requis sont établies et correctement configurés.

Le diagramme suivant illustre une architecture classique de veille Cortana en fonction qui implémente et orchestre le cycle de flux de données qui est décrit ci-dessus :

![Architecture de déploiement de bout en bout](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Pour plus d’informations sur chacun des composants et de l’ensemble de l’architecture, veuillez consulter le modèle de Solution de l’énergie.
