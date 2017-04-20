<properties 
    pageTitle="Quel est le processus d’équipe données Science ?  | Microsoft Azure" 
    description="Le processus d’équipe données Science est une méthode systématique pour créer des applications intelligentes qui exploitent analytique avancée." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="bradsev" /> 


# <a name="what-is-the-team-data-science-process-tdsp"></a>Quel est le processus de Science données Team (TDSP) ?

L' [Équipe de données Science processus (TDSP)](data-science-process-overview.md) fournit une approche systématique pour créer des applications intelligentes qui permet aux équipes de chercheurs de données pour collaborer efficacement sur le cycle de vie complet des activités nécessaires pour activer ces applications dans les produits. Le TDSP présente une séquence d’étapes qui fournissent des **conseils** sur la façon de définir le problème, configurez les outils et l’environnement, si nécessaire, analyser des données pertinentes, créer et évaluer des modèles de prévision, puis déployer ces modèles dans des applications d’entreprise. 

Voici les étapes dans le **Processus de Science de données Team**:  

![CAP-flux de travail](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

Le processus est **itératif**: la compréhension des nouvelles et existantes ou affinées dans le modèle évolue et requiert la retravailler les étapes effectuées précédemment dans la séquence. Développement d’organisation existante et les processus de planification de projet sont **facilement adapté** pour fonctionner avec la séquence définie par le TDSP d’étapes. 

Les étapes dans le processus sont génération de diagramme liés dans le [chemin d’accès de la formation TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et décrites ci-dessous.  

## <a name="preparation-steps"></a>Étapes de préparation 

## <a name="p1-plan-the-analytics-project"></a>P1. Planifier le projet analytique 

Démarrer un projet analytique par la définition de vos objectifs commerciaux et les problèmes. Ils sont spécifiés en termes de **besoins de l’entreprise**. Un objectif central de cette étape est d’identifier les variables clés de l’entreprise (prévision de ventes ou la probabilité qu’une commande est frauduleuse, par exemple) qui l’analyse doit prévoir pour satisfaire ces exigences. Planification supplémentaire est généralement essentielle pour développer une compréhension des **sources de données** requis pour contrer les objectifs du projet à partir d’un point de vue analytique. Il n’est pas rare, par exemple, pour trouver que les systèmes existants doivent collecter et consigner d’autres types de données pour résoudre le problème et de réaliser les objectifs du projet. Pour obtenir des instructions, reportez-vous à [planifier votre environnement pour le processus d’équipe données Science](machine-learning-data-science-plan-your-environment.md) et des [scénarios pour analytique avancée dans l’apprentissage automatique de Azure](machine-learning-data-science-plan-sample-scenarios.md).  

## <a name="p2-setup-analytics-environment"></a>P2. D’installer l’environnement analytique 

Un environnement analytique pour le processus de Science de données équipe comprend plusieurs composants : 

- **espaces de travail de données** où les données sont mis en place pour l’analyse et de modélisation, 
- une **infrastructure de traitement** pour le prétraitement, d’exploration et de modélisation de données
- une **infrastructure runtime** pour mettre les modèles d’analyse et exécuter des applications qui utilisent les modèles de client intelligent.  

L’infrastructure analytique qui doit être le programme d’installation fait souvent partie d’un environnement distinct à partir de systèmes opérationnels de base. Mais en général il s’appuie sur les données à partir de plusieurs systèmes au sein de l’entreprise et à partir de sources externes à l’entreprise. L’infrastructure analytique peut être purement en nuage, ou une installation sur site, ou un mélange des deux. Pour les options, voir [configurer les environnements de science de données pour une utilisation dans le processus de Science de données Team](machine-learning-data-science-environment-setup.md).

## <a name="analytics-steps"></a>Étapes d’Analytique :  

## <a name="1-ingest-data-into-the-analytical-environment"></a>1. acquisition de données dans l’environnement d’analyse 

La première étape consiste à mettre les données pertinentes à partir de diverses sources, soit à partir d’ou d’en dehors de l’entreprise, dans une analyse des environnements où les données peuvent être traitées. Le **format** des données à la source peut différer au format requis par la destination. Une transformation de données peut également donc être effectuées par les outils de l’ingestion. Pour les options, voir [charger les données dans des environnements de stockage pour l’analytique](machine-learning-data-science-ingest-data.md)

En plus de l’acquisition initiale des données, de nombreuses applications intelligentes sont requises pour actualiser les données régulièrement dans le cadre d’un processus de formation continue. Pour ce faire, vous pouvez configurer un **pipeline de données** ou un flux de travail. Elle constitue la partie de la partie itérative du processus qui inclut la reconstruction et réévaluer les modèles d’analyse utilisés par la déploiement de la solution intelligente des applications. Voir, par exemple, [déplacer des données à partir d’un serveur SQL sur site de SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md).


## <a name="2-explore-and-pre-process-data"></a>2. l’exploration et le prétraitement des données 

L’étape suivante consiste pour obtenir une bonne compréhension des données en étudiant ses **statistiques récapitulatives** , relations et à l’aide de techniques de cette **visualisation**. C’est également où les problèmes de **qualité des données** et d’intégrité, telles que des valeurs manquantes, les incompatibilités de types de données et des relations de données incohérentes, sont traités. Prétraitement des transformations sont utilisées pour nettoyer les données brutes avant plus analytique et modélisation peut avoir lieu. Pour une description, consultez [tâches pour préparer des données pour une machine de formation](machine-learning-data-science-prepare-data.md).


## <a name="3-develop-features"></a>3. Elaborer des fonctionnalités 

Les chercheurs de données, en collaboration avec des experts du domaine, doivent identifier les fonctionnalités qui capture les propriétés utiles de l’ensemble de données et pouvant être mieux utilisé pour prévoir les variables clés de l’entreprise identifiés lors de la planification. Ces nouvelles fonctionnalités peuvent être dérivées à partir de données existantes, ou peuvent nécessiter des données supplémentaires à collecter. Ce processus est appelé à **l’ingénierie de la fonction** et est une des principales étapes de la création d’un système efficace analytique prédictive. Cette étape nécessite une combinaison créative de domaine d’expertise et de répercuter les informations obtenues à partir de l’étape d’exploration de données. Pour obtenir des instructions, consultez [ingénierie dans le processus de Science de données équipe de fonctionnalité](machine-learning-data-science-create-features.md).


## <a name="4-create-predictive-models"></a>4. création de modèles de prévision 

Scientifiques de données construire des modèles d’analyse afin de prévoir les variables clés identifiés par les exigences métiers définies dans la planification étape à l’aide de données qui a été nettoyées et featurized. Systèmes d’apprentissage machine, prend en charge plusieurs **algorithmes de modélisation** qui s’appliquent à un grand nombre de cas. Pour obtenir des instructions, reportez-vous à la section [comment choisir les algorithmes pour la formation de Machine équipe Azure](machine-learning-algorithm-choice.md).

Les scientifiques de données doivent choisir le modèle le plus approprié pour leur tâche de prévision et il n’est pas rare que les résultats de plusieurs modèles doivent être combinés pour obtenir les meilleurs résultats. Les données d’entrée pour la modélisation sont généralement divisées au hasard en trois parties :

- un jeu de données, 
- un jeu de données de validation 
- un jeu de données de test 

Les modèles sont créés en utilisant le **jeu de données**. La combinaison optimale de modèles (avec des paramètres de réglage) est sélectionnée par les modèles en cours d’exécution et de mesurer les erreurs de prévision pour l' **ensemble de données de validation**. Enfin le **test d’un jeu de données** est utilisée pour évaluer les performances du modèle de données indépendantes qui n’a pas été utilisés pour former ou valider le modèle choisi.  Pour les procédures, consultez [l’évaluation des performances du modèle dans l’apprentissage automatique de Azure](machine-learning-evaluate-model-performance.md).


## <a name="5-deploy-and-consume-models"></a>5. permet de déployer et d’utiliser des modèles 

Une fois que nous disposons d’un ensemble de modèles qui fonctionnent bien, ils peuvent être **operationalized** pour les autres applications à consommer. Selon les besoins, les prévisions sont effectuées en **temps réel** ou sur une base **par lots** . Pour être operationalized, les modèles doivent être exposées à **Ouvrir l’interface de l’API** qui est consommée facilement à partir de diverses applications ce site Web en ligne, des feuilles de calcul, des tableaux de bord ou ligne d’applications d’entreprise et back-end. Voir [déploiement d’un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="summary-and-next-steps"></a>Synthèse et étapes suivantes

Le [Processus d’équipe données Science](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) est modélisé sous la forme d’une séquence d’étapes itératives qui **fournissent des conseils** sur les tâches nécessaires à l’utilisation d’analytique avancée pour créer des applications intelligentes. Chaque étape fournit également des détails sur l’utilisation des différentes technologies Microsoft pour effectuer les tâches décrites. 

Alors que TDSP n’impose pas de certains types d’artefacts de **documentation** , il est recommandé de documenter les résultats de l’exploration de données, la modélisation et l’évaluation, et pour enregistrer le code pertinent afin que l’analyse peut itéré lorsque cela est nécessaire. Cela permet également la réutilisation du travail analytique lorsque vous travaillez sur d’autres applications impliquant des tâches de prévision et des données similaires.

Procédures pas à pas complète de bout en bout illustrant toutes les étapes du processus pour **des scénarios spécifiques** sont également fournis. Voir, par exemple :

- [Le processus de Science de données équipe en action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [Le processus Science données d’équipe en action : à l’aide de HDInsight Hadoop clusters](machine-learning-data-science-process-hive-walkthrough.md).
- [Science de données à l’aide d’allumage sur Azure HD.mdnsight](machine-learning-data-science-spark-overview.md)
- [Science de données évolutive dans Azure données lac : une procédure pas à pas de bout en bout](machine-learning-data-science-process-data-lake-walkthrough.md)

 
