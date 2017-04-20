<properties
    pageTitle="Comment identifier les scénarios et de planifier les avancées de traitement analytique des données | Microsoft Azure"
    description="Plan d’analytique avancée en tenant compte d’une série de questions clées."
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


# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Comment faire pour identifier des scénarios et plan de traitement de données analytique avancée

Les ressources devez vous souhaitez inclure lors de la définition d’un environnement à analytique avancée de traitement sur un groupe de données ? Cet article propose une série de questions à poser qui permettra d’identifier les tâches et les ressources appropriés à votre scénario. L’ordre des étapes principales pour analytique prédictive est décrit dans [Quel est le processus de la Science des données (TDSP) équipe ?](data-science-process-overview.md). Chacune de ces étapes nécessite des ressources spécifiques pour les tâches pertinentes pour votre scénario particulier. Les principales questions pour identifier votre scénario concernent la logistique des données, caractéristiques, la qualité des jeux de données et que les outils et les langues que vous souhaitez effectuer l’analyse.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Questions logistiques : emplacements des données et le déplacement
Les questions logistiques concernent l’emplacement de la **source de données**, la **cible de destination** dans Azure et d’exigences pour le déplacement des données, y compris la planification et des ressources impliquées. Les données peuvent ont besoin de déplacer plusieurs fois au cours du processus analytique. Un scénario courant consiste à déplacer des données locales dans une forme de stockage sur Azure puis en Studio de formation de Machine.

1. **Quel est votre source de données ?** Est-il local ou dans le nuage ? Par exemple :
    - Les données sont accessibles à une adresse HTTP.
    - Les données résident dans un emplacement de fichier local/réseau.
    - Les données sont dans une base de données SQL Server.
    - Les données sont stockées dans un conteneur de stockage Azure

2. **Quelle est la destination Azure ?** Où le besoin pour les traiter ou modélisation ? Par exemple :
    - Stockage des objets Blob Azure
    - Bases de données SQL Azure
    - SQL Server sur une machine virtuelle Azure
    - HDInsight (Hadoop sur Azure) ou les tables de la ruche
    - Apprentissage automatique Azure
    - Montables Azure des disques durs virtuels.

3. **Comment vous allez déplacer les données ?** Les procédures et les ressources disponibles pour l’acquisition ou de charger des données dans divers environnements de traitement et de stockage différents sont décrits dans les rubriques suivantes.

    -  [Charger des données dans des environnements de stockage pour l’analytique](machine-learning-data-science-ingest-data.md)
    -  [Importation de vos données de formation dans un Studio de formation Azure Machine à partir de diverses sources de données](machine-learning-data-science-import-data.md).

4. **Les données doivent-elles être déplacé à intervalles réguliers ou modifié lors de la migration ?** Envisagez d’utiliser le Factory de données Azure (CAD) lorsque les données devant être migré en permanence, en particulier si un scénario hybride qui accède à la fois sur site et ressources de cloud est impliqué, ou lorsque les données sont traitées ou doivent être modifié ou disposer d’une logique métier ajoutée au cours de la migration. Pour plus d’informations, reportez-vous à [déplacer des données à partir d’un serveur SQL sur site de SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md)

5. **La quantité de données doit être déplacée vers Azure ?** Des groupes de données très volumineux peut dépasser la capacité de stockage de certains environnements. Pour obtenir un exemple, consultez la rubrique sur les limites de taille pour Studio de formation de Machine dans la section suivante. Dans de tels cas, un échantillon des données peut-être être utilisé pendant l’analyse. Pour plus d’informations à un groupe de données dans différents environnements d’Azure, bas-exemple, consultez [exemple de données dans le processus de Science de données équipe](machine-learning-data-science-sample-data.md).


## <a name="data-characteristics-questions-type-format-and-size"></a>Questions de caractéristiques de données : type, format et taille
Ces questions sont essentielles à la planification de votre système de stockage et d’environnements, chacun d'entre eux sont adaptés à différents types de données et chacune d'entre elles ont certaines restrictions de traitement.

1. **Quels sont les types de données ?** Par exemple :
    - Numérique
    - Par catégorie
    - Chaînes
    - Binaire

2. **Comment sont vos données mises en forme ?** Par exemple :
    - Séparées par des virgules (CSV) ou séparé par des tabulations (TSV) de fichiers plats
    - Compressé ou non compressé
    - BLOB Azure
    - Tables d’Hadoop ruche
    - Tables SQL Server

2. **La taille sont vos données ?**
    - Petite : moins de 2 Go
    - Moyenne : Supérieure à 2 et inférieure à 10 Go
    - Important : Supérieure à 10 Go

Prenons l’exemple de l’environnement de Studio de formation de Machine Azure :

- Pour obtenir la liste des types pris en charge par Studio de formation de Machine Azure, consultez la section [types de données et les formats de données pris en charge](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) et formats de données.
- Pour plus d’informations sur les limites des données de Studio de formation de Machine Azure, consultez la **la taille du jeu de données est possible pour les modules Mon ?** section [d’importation et exportation de données d’apprentissage automatique](machine-learning-faq.md#machine-learning-studio-questions)

Pour plus d’informations sur les limitations d’autres services Azure utilisés dans le processus analytique, reportez-vous à la section [abonnement Azure et les limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Questions de qualité de données : exploration et traitement préalable

1. **Ce que vous savez sur vos données ?** Explorer les données lorsque vous avez besoin d’obtenir un comprendre ses caractéristiques. Les modèles ou il tendances des expositions, nouveautés de valeurs aberrantes a ou combien de valeurs est manquants. Cette étape est importante pour déterminer le degré de traitement préalable, si nécessaire, de formuler des hypothèses qui pourraient suggérer les fonctionnalités les plus appropriées ou type d’analyse, de la formulation de plans pour la collecte de données supplémentaires. Calcul des statistiques descriptives et le traçage des visualisations sont des techniques utiles pour le contrôle de données. Pour plus d’informations étudier un groupe de données dans différents environnements d’Azure, consultez [Explorer des données dans le processus de Science de données équipe](machine-learning-data-science-explore-data.md).

2. **Les données nécessitent de prétraitement ou de nettoyage ?**
De pré-traitement et de nettoyage des données sont des tâches importantes qui en général doivent être effectuées avant que le groupe de données peut être utilisé efficacement pour l’apprentissage de l’ordinateur. Données brutes souvent bruyants et peu fiables et peuvent être des valeurs manquantes. Pour la modélisation à l’aide de ces données peut produire des résultats trompeurs. Pour une description, consultez [tâches pour préparer des données pour une machine de formation](machine-learning-data-science-prepare-data.md).

## <a name="tools-and-languages-questions"></a>Outils et langages de questions
Il existe beaucoup d’options selon les langages et environnements de développement ou d’outils vous avez besoin ou qui sont plus adaptable à l’aide de.

1. **Quelles sont les langues que vous souhaitez utiliser pour l’analyse ?**  
    - R
    - Python
    - SQL

2. **Quels outils utiliser pour l’analyse de données ?**
    - [Microsoft Azure Powershell](powershell-install-configure.md) - un langage de script utilisé pour administrer vos ressources Azure dans un langage de script.
    - [Studio d’apprentissage Machine Azure](machine-learning-what-is-ml-studio/)
    - [Analytique de révolution](http://www.revolutionanalytics.com/revolution-r-open)
    - [RStudio](http://www.rstudio.com)
    - [Outils de Python pour Visual Studio](http://microsoft.github.io/PTVS/)
    - [Anaconda](https://www.continuum.io/why-anaconda)
    - [Ordinateurs portables de Jupyter](http://jupyter.org/)
    - [Puissance de Microsoft BI](http://powerbi.microsoft.com)


## <a name="identify-your-advanced-analytics-scenario"></a>Identifier votre scénario analytique avancée
Une fois que vous avez répondu aux questions de la section précédente, vous êtes prêt à déterminer quel scénario mieux adaptée à votre cas. Les scénarios d’exemple sont décrits dans les [scénarios d’analytique avancée dans l’apprentissage automatique de Azure](machine-learning-data-science-plan-sample-scenarios.md).
