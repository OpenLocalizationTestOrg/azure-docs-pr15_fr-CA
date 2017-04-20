<properties
    pageTitle="Guide technique pour le modèle de Solution d’analyse décisionnelle Cortana pour une maintenance PREVENTIVE dans l’aéronautique et autres entreprises | Microsoft Azure"
    description="Guide technique pour le modèle de Solution avec Microsoft Cortana Intelligence pour une maintenance PREVENTIVE dans l’aéronautique, les utilitaires et transport."
    services="cortana-analytics"
    documentationCenter=""
    authors="fboylu"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="fboylu" />

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Guide technique pour le modèle de Solution d’analyse décisionnelle Cortana pour une maintenance PREVENTIVE dans l’aéronautique et autres entreprises

## <a name="acknowledgements"></a>**Accusés de réception**
Cet article est créé par les chercheurs de données Yan Zhang, Gauher Shaheen, Fidan Boylu Uz et les ingénieur Dan Grecoe chez Microsoft.

## <a name="overview"></a>**Vue d’ensemble**

Modèles de solutions sont conçues pour accélérer le processus de création d’une démonstration E2E sur Cortana Intelligence Suite. Un modèle déployé provisionner votre abonnement avec les composants Cortana Intelligence nécessaires et créer les relations entre eux. Il alimente également le pipeline de données avec les données d’exemple générées à partir d’une application de générateur de données qui sera de télécharger et d’installer sur votre ordinateur local après avoir déployé le modèle de solution. Les données générées à partir du générateur seront hydrate le pipeline de données et commencer à générer des prévisions d’apprentissage machine qui peuvent ensuite être visualisées sur le tableau de bord d’alimentation. Le processus de déploiement va vous guider tout au long des différentes étapes pour configurer vos informations d’identification de la solution. Assurez-vous que vous enregistrez ces informations d’identification telles que le nom de la solution, nom d’utilisateur et mot de passe que vous fournissez au cours du déploiement.  

L’objectif de ce document est d’expliquer l’architecture de référence et mis en service dans votre abonnement dans le cadre de ce modèle de solution de différents composants. Le document traite également de la remplacer les exemples de données avec les données réelles de votre choix pour être en mesure de voir les perspectives et les prévisions à partir de vos propres données. En outre, le document décrit les parties du modèle de Solution qui devra être modifié si vous souhaitez personnaliser la solution à vos propres données. Vous trouverez des instructions sur la façon de créer le tableau de bord d’alimentation pour ce modèle de Solution à la fin.

>[AZURE.TIP] Vous pouvez télécharger et imprimer une [version PDF de ce document](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## <a name="big-picture"></a>**Vue d’ensemble**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Lorsque la solution est déployée, divers services Azure dans Cortana Analytique Suite sont activés (*c'est-à-dire* événement pivot, flux Analytique, HDInsight, Data Factory, apprentissage automatique, *etc.*). Le schéma d’architecture ci-dessus montre, à un niveau élevé, comment prédictive Maintenance aéronautique modèle de Solution est construite à partir de bout en bout. Vous serez en mesure de rechercher ces services dans le portail azure en cliquant dessus dans le diagramme de modèle de solution créé avec le déploiement de la solution, à l’exception de HDInsight que ce service est mis en service à la demande lorsque les activités connexes de pipeline sont requises pour l’exécution et supprimés par la suite.
Vous pouvez télécharger une [version en taille normale du diagramme](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Les sections suivantes décrivent chaque élément.

## <a name="data-source-and-ingestion"></a>**Réception et la source de données**

### <a name="synthetic-data-source"></a>Source de données synthétiques

Pour ce modèle de la source de données utilisée est générée à partir d’une application de bureau que vous allez télécharger et exécuter localement une fois le déploiement réussi. Vous trouverez les instructions pour télécharger et installer cette application dans la barre des propriétés lorsque vous sélectionnez le premier nœud appelé générateur de données de gestion prédictive sur le diagramme de modèle de solution. Cette application flux le service [Concentrateur d’événements Azure](#azure-event-hub) avec points de données ou des événements, qui seront utilisés dans le reste du flux de la solution. Cette source de données est constituée d’ou dérivée de données accessibles à partir du [référentiel de données NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) en utilisant le [jeu de données de Simulation de turboréacteur à double flux moteur de dégradation](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

L’application de génération des événements remplissent le concentrateur d’événements Azure uniquement pendant qu’il est en cours d’exécution sur votre ordinateur.

### <a name="azure-event-hub"></a>Concentrateur d’événements Azure

Le service de [Concentrateur d’événements Azure](https://azure.microsoft.com/services/event-hubs/) est le destinataire de l’entrée fournie par la Source de données synthétiques décrite ci-dessus.

## <a name="data-preparation-and-analysis"></a>**Analyse et la préparation des données**


### <a name="azure-stream-analytics"></a>Analytique de flux Azure

Le service [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) permet de pratiquement en temps réel analytique sur le flux d’entrée à partir du service de [Concentrateur d’événements Azure](#azure-event-hub) et publier les résultats dans un tableau de bord [D’alimentation](https://powerbi.microsoft.com) ainsi que l’archivage de tous les événements entrants brutes pour le service de [Stockage Azure](https://azure.microsoft.com/services/storage/) pour un traitement ultérieur par le service [Factory de données Azure](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hd-insights-custom-aggregation"></a>Agrégation personnalisée de perspectives sur la HD

Le service d’Azure HD Insight est utilisé pour exécuter des scripts de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orchestrés par Azure Data Factory) pour fournir des agrégations sur les événements bruts qui ont été archivés en utilisant le service Analytique de flux Azure.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

Le service de [Formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) est utilisé (orchestrés par Azure Data Factory) pour effectuer des prévisions sur la durée d’utilité restant (RUL) d’un moteur d’aéronef particulier étant donné les entrées reçues.

## <a name="data-publishing"></a>**Publication de données**


### <a name="azure-sql-database-service"></a>Service de base de données SQL Azure

Le service de [Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/) est utilisé pour stocker (gérés par Azure Data Factory) les prévisions reçues par le service de formation de Machine Azure qui va être consommé dans le tableau de bord [D’alimentation](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consommation des données**

### <a name="power-bi"></a>Alimentation BI

Le service de [Puissance BI](https://powerbi.microsoft.com) est utilisé pour afficher un tableau de bord qui contient des agrégations et alertes fournies par le service [Analytique de flux Azure](https://azure.microsoft.com/services/stream-analytics/) ainsi que les prévisions RUL stockées dans la [Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/) produites à l’aide du service de [Formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) . Pour obtenir des Instructions sur la façon de créer le tableau de bord d’alimentation pour ce modèle de la Solution, reportez-vous à la section ci-dessous.

## <a name="how-to-bring-in-your-own-data"></a>**Comment faire pour importer vos propres données.**

Cette section décrit comment mettre vos propres données à Azure et les zones nécessiterait des modifications pour les données que vous mettez dans cette architecture.

Il est peu probable que n’importe quel groupe de données que vous amener renverrait le groupe de données utilisé par le [jeu de données de Simulation de réacteur à double flux moteur dégradation](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) utilisé pour ce modèle de la solution. Comprendre les exigences et vos données seront des éléments cruciaux dans comment vous modifiez ce modèle pour travailler avec vos propres données. S’il s’agit de votre première exposition au service formation de Machine d’Azure, vous pouvez obtenir une introduction à celui-ci à l’aide de l’exemple de [la création de votre première expérience](machine-learning-create-experiment.md).

Les paragraphes suivants examinent les sections du modèle qui requièrent des modifications lors de l’introduction d’un nouveau groupe de données.

### <a name="azure-event-hub"></a>Concentrateur d’événements Azure

Le service de concentrateur d’événements Azure est très générique, telles que les données peuvent être validées sur le concentrateur au format CSV ou JSON. Aucun traitement spécial n’intervient dans le concentrateur d’événements Azure, mais il est important que vous compreniez les données introduites dans il.

Ce document ne décrit pas comment intégrer vos données, mais vous pouvez facilement envoyer les événements ou les données à un concentrateur d’événements Azure à l’aide de l’API de concentrateur d’événement.

### <a name="azure-stream-analytics"></a>Analytique de flux Azure

Le service Azure flux Analytique sert à fournir près analytique en temps réel en lisant à partir du flux de données et la sortie de données vers n’importe quel nombre de sources.

Pour une Maintenance PREVENTIVE pour le modèle de Solution aéronautique, la requête Analytique de flux Azure se compose de quatre sous-requêtes, chaque événements beaucoup du service concentrateur d’événements Azure et avoir les sorties à quatre emplacements distincts. Ces sorties comprennent trois jeux de données BI de puissance et un emplacement de stockage Azure.

Vous trouverez la requête Analytique de flux Azure en :

-   Se connecter au portail Azure

-   Localiser les travaux analytique de flux ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) qui ont été générés lors du déployée de la solution (*par exemple*, **maintenancesa02asapbi** et **maintenancesa02asablob** pour la solution de gestion prédictive)

-   Sélection

    -   ***Entrées*** pour afficher les entrées de la requête

    -   ***Requête*** pour afficher la requête elle-même

    -   ***Sorties*** pour afficher les différentes sorties

Vous trouverez des informations sur la construction de la requête Analytique de flux d’Azure dans la [Référence de requête Analytique de flux](https://msdn.microsoft.com/library/azure/dn834998.aspx) sur MSDN.

Dans cette solution, les requêtes de trois groupes de données avec près d’informations analytique en temps réel sur le flux de données entrant à un tableau de bord BI d’alimentation qui est fournie dans le cadre de ce modèle de solution de sortie. Car il y a une connaissance implicite sur le format de données entrantes, ces requêtes doit être modifié en fonction de votre format de données.

La requête dans le second flux analytique travail **maintenancesa02asablob** produit simplement tous les événements de [Concentrateur d’événements](https://azure.microsoft.com/services/event-hubs/) au [Stockage Azure](https://azure.microsoft.com/services/storage/) et ne requiert, par conséquent, aucune modification, quel que soit votre format de données que les informations d’événement complet sont diffusé en continu au stockage.

### <a name="azure-data-factory"></a>Usine de données Azure

Le service de [Fabrique de données Azure](https://azure.microsoft.com/documentation/services/data-factory/) orchestre le mouvement et le traitement des données. Dans la gestion prédictive pour le modèle de Solution aéronautique le factory de données est constitué de trois [pipelines de](../data-factory/data-factory-create-pipelines.md) déplacer et de traiter les données à l’aide de différentes technologies.  Vous pouvez accéder à votre fabrique de données en ouvrant la le nœud Data Factory au bas du diagramme de modèle de solution créée avec le déploiement de la solution. Ceci vous dirigera sur le factory de données sur votre portail Azure. Si vous constatez des erreurs dans vos groupes de données, vous pouvez ignorer ceux qu’ils sont en raison de la fabrique de données en cours de déploiement avant le démarrage du Générateur de données. Ces erreurs n’empêchent pas votre fabrique de données fonctionne.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Cette section traite de la nécessaire [pipelines](../data-factory/data-factory-create-pipelines.md) et [activités](../data-factory/data-factory-create-pipelines.md) contenus dans [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Voici l’affichage du schéma de la solution.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Deux des tuyaux de cette fabrique de contenir des scripts de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) qui servent à partitionner et agrégation des données. Lorsque vous avez noté, les scripts se situeront dans le compte de [Stockage Azure](https://azure.microsoft.com/services/storage/) créé lors de l’installation. Leur emplacement sera : maintenancesascript\\\\script\\\\ruche\\ \\ (ou https://[Your solution name].blob.core.windows.net/maintenancesascript).

Comme pour les requêtes [Azure flux Analytique](#azure-stream-analytics-1) , les scripts de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) ont une connaissance implicite sur le format de données entrantes, ces requêtes devra être modifié en fonction de vos besoins [d’ingénierie de fonctionnalité](machine-learning-feature-selection-and-engineering.md) et de format de données.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient une activité unique - une activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour partitionner les données à placer dans le [Stockage Azure](https://azure.microsoft.com/services/storage/) pendant la tâche [Analytique de flux Azure](https://azure.microsoft.com/services/stream-analytics/) .

Le script de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche de partitionnement est ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient plusieurs activités et dont le résultat final est de qu'essayer des prévisions évaluées de la [Formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) associé à ce modèle de la solution.

Les activités contenues dans ce sont :

-   Activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour effectuer des agrégations et l’ingénierie de la fonctionnalité nécessaire à l’expérience [d’Apprentissage automatique de Azure](https://azure.microsoft.com/services/machine-learning/) .
    Le script de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche de partitionnement est ***PrepareMLInput.hql***.

-   Activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) dans un blob [Azure Storage](https://azure.microsoft.com/services/storage/) unique qui peut être pas accès à l’activité de la [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) activité qui appelle l' [Apprentissage automatique de Azure](https://azure.microsoft.com/services/machine-learning/) tester qui entraîne les résultats dans un seul blob de [Stockage Azure](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient une activité unique - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace que les résultats de la [Formation de Machine Azure](#azure-machine-learning) essayer à partir de la ***MLScoringPipeline*** à la [Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/) qui a été mis en service dans le cadre de l’installation de modèle de solution.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

L’expérience [d’Apprentissage automatique de Azure](https://azure.microsoft.com/services/machine-learning/) utilisé pour ce modèle de solution fournit le reste utile vie (RUL) un moteur d’avion. L’expérience est spécifique à l’ensemble de données consommée et donc nécessitera modification ou remplacement spécifique pour les données qui s’affiche dans.

Pour plus d’informations sur la création de l’expérience d’apprentissage automatique de Azure, consultez [Maintenance PREVENTIVE : étape 1 sur 3, la préparation des données et l’ingénierie de la fonction](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Surveiller la progression**
 Une fois que le Générateur de données est lancé, le pipeline commence à obtenir hydraté et les différents composants de votre solution de démarrent son en action suivant les commandes émises par l’usine de données. Il existe deux méthodes que vous pouvez surveiller le pipeline.

1. Une de la tâche de flux de données Analytique écrit des données brutes entrantes pour le stockage des objets blob. Si vous cliquez sur le composant de stockage des objets Blob de votre solution à partir de l’écran vous correctement déployé la solution et puis cliquez sur Ouvrir dans le panneau de droite, il vous amènera sur le [portail de gestion](https://portal.azure.com/). Une seule fois, cliquez sur les objets BLOB. Dans l’écran suivant, vous verrez une liste de conteneurs. Cliquez sur **maintenancesadata**. Dans l’écran suivant, vous verrez le dossier **rawdata** . Dans le dossier rawdata, vous verrez des dossiers avec des noms tels que les heures = 17, heure = 18 etc.. Si vous voyez ces dossiers, il indique que les données brutes sont correctement générée sur votre ordinateur et stockées dans le stockage blob. Vous devez voir fichiers csv doivent avoir des tailles finies en Mo dans ces dossiers.

2. La dernière étape du pipeline consiste à écrire les données (par exemple, des prévisions à partir de l’apprentissage automatique) dans la base de données de SQL. Vous devrez peut-être attendre un maximum de trois heures pour les données à afficher dans la base de données de SQL. Une façon de contrôler la quantité de données est disponible dans votre base de données SQL est via [azure portal](https://manage.windowsazure.com/). Dans le volet gauche, localisez les bases de données SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) et cliquez dessus. Recherchez votre base de données **pmaintenancedb** , puis cliquez dessus. Sur la page suivante, dans la partie inférieure, cliquez sur Gérer

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

    Ici, vous pouvez cliquer sur Nouvelle requête et la requête pour le nombre de lignes (par exemple, select count à partir de PMResult). Au fur et à mesure du votre base de données, augmentez le nombre de lignes dans la table.


## <a name="power-bi-dashboard"></a>**Tableau de bord d’alimentation**

### <a name="overview"></a>Vue d’ensemble

Cette section décrit la configuration de tableau de bord d’alimentation à visualiser vos données en temps réel à partir d’analytique de flux Azure (chemin réactif), ainsi que les résultats des prévisions par lots à partir de la machine Azure cursus (à froid).

### <a name="setup-cold-path-dashboard"></a>Le programme d’installation de tableau de bord de chemin à froid

Dans le pipeline de données chemin à froid, l’objectif essentiel est d’obtenir la RUL prédictive (restant le cycle de vie) de chaque moteur d’avion une fois qu’il a terminé un vol (cycle). Le résultat de la prédiction est mis à jour toutes les 3 heures pour prédire les moteurs que vous avez un vol au cours des 3 dernières heures.

BI d’alimentation se connecte à une base de données Azure SQL comme sa source de données, où se trouvent les résultats de la prévision. Remarque : 1) au moment de déployer votre solution, une prédiction réelle s’afficheront dans la base de données dans les 3 heures.
Le fichier pbix fourni avec le téléchargement de générateur contenait des données de la valeur de départ afin que vous pouvez créer le tableau de bord d’alimentation immédiatement. 2) dans cette étape, la condition préalable est pour télécharger et installer le logiciel gratuit [d’alimentation BI bureau](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Les étapes suivantes vous guideront comment connecter le fichier pbix à la base de données SQL qui a été lancé au moment du déploiement de la solution qui contient des données (*par exemple*. résultats de la prévision) pour la visualisation.

1.  Obtenir les informations d’identification de base de données.

    Vous aurez besoin du **nom du serveur de base de données, nom de la base de données, nom d’utilisateur et mot de passe** avant de passer aux étapes suivantes. Voici la procédure pour vous guider comment les rechercher.

    -   Une fois **« de base de données SQL Azure »** sur le diagramme de modèle de solution s’affiche en vert, cliquez dessus, puis cliquez sur **« Ouvrir »**.

    -   Vous verrez une nouvel onglet/fenêtre du navigateur affiche la page de portail Azure. Dans le volet gauche, cliquez sur **« Groupes de ressources »** .

    -   Sélectionnez l’abonnement que vous utilisez pour le déploiement de la solution, puis sélectionnez **' YourSolutionName\_ResourceGroup'**.

    -   Dans le nouveau pop panneau, cliquez sur le ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) icône pour accéder à votre base de données. Le nom de votre base de données est située en regard de cette icône (*par exemple*, **'pmaintenancedb'**) et le **nom du serveur de base de données** est répertorié sous la propriété de nom de serveur et doit être similaire à **YourSoutionName.database.windows.net**.

    -   Votre base de données, **nom d’utilisateur** et le **mot de passe** sont le même que le nom d’utilisateur et le mot de passe précédemment enregistrés pendant le déploiement de la solution.

2.  Mettre à jour la source de données, le chemin à froid du fichier de rapport avec le bureau de BI d’alimentation.

    -   Dans le dossier sur votre ordinateur où vous avez téléchargé et décompressé le fichier du générateur, double-cliquez sur le **PowerBI\\PredictiveMaintenanceAerospace.pbix** fichier. Si vous voyez des messages d’avertissement lorsque vous ouvrez le fichier, les ignorer. En haut du fichier, cliquez sur **« Modifier les requêtes »**.

        ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

    -   Vous verrez deux tables, **RemainingUsefulLife** et **PMResult**. Sélectionnez la première table, puis cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) en regard de **« Source »** sous **« étapes appliquées »** sur le panneau droit **Les paramètres de requête** . Ignorer les messages d’avertissement s’affichent.

    -   Dans la fenêtre contextuelle, remplacer **« Serveur »** et **« Database »** avec votre propre nom du serveur et de base de données, puis cliquez sur **'OK'**. Nom de serveur, assurez-vous de que spécifier le port 1433 (**YourSoutionName.database.windows.net, 1433**). Laissez le champ de base de données en tant que **pmaintenancedb**. Ignorer les messages d’avertissement qui s’affichent à l’écran.

    -   Dans le protocole pop suivant à la fenêtre, vous verrez deux options dans le volet gauche (**Windows** et **base de données**). Cliquez sur **« Database »**, indiquez **« Username »** et **« Password »** (c’est le nom d’utilisateur et le mot de passe lorsque vous d’abord déployé la solution et créé une base de données Azure SQL). Dans ***Sélectionnez le niveau à appliquer ces paramètres à***, vérifiez l’option de niveau de base de données. Cliquez ensuite sur **« Se connecter »**.

    -   Cliquez sur la deuxième table, **PMResult** , puis cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) en regard de **« Source »** sous **« étapes appliquées »** sur le panneau droit de **' paramètres de requête '** et à jour les noms de serveur et de base de données comme dans la procédure ci-dessus et cliquez sur OK.

    -   Une fois que vous êtes guidé à la page précédente, fermez la fenêtre. Un message s’affichera out - cliquez sur **Appliquer**. Enfin, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications. Votre fichier BI de puissance a maintenant établie la connexion au serveur. Si votre visualisations sont vides, veillez à que désactiver les sélections sur les visualisations pour visualiser toutes les données en cliquant sur l’icône représentant une gomme dans le coin supérieur droit de légendes. Utilisez le bouton Actualiser pour refléter les nouvelles données sur les visualisations. Initialement, vous verrez uniquement les données d’amorçage sur votre visualisations comme le factory de données est planifié pour actualiser toutes les 3 heures. Après 3 heures, vous verrez les nouvelles prévisions reflétées dans votre visualisations lorsque vous actualisez les données.

3.  (Facultatif) Publier le tableau de bord de chemin à froid à [BI de puissance en ligne](http://www.powerbi.com/). Notez que cette étape a besoin d’un compte de puissance BI (ou le compte de l’Office 365).

    -   Cliquez sur **« Publier »** et quelques secondes plus tard une fenêtre affiche « Publication d’alimentation BI succès ! » avec une coche verte. Cliquez sur le lien ci-dessous « PredictiveMaintenanceAerospace.pbix ouvrir dans BI d’alimentation ». Pour obtenir des instructions détaillées, voir la [publication d’alimentation BI bureau](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Pour créer un nouveau tableau de bord : cliquez sur le **+** en regard de la section **tableaux de bord** dans le volet gauche. Entrez le nom « Demo de Maintenance prédictive » pour ce tableau de bord.

    -   Une fois que vous ouvrez le rapport, cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) pour épingler toutes les visualisations à votre tableau de bord. Pour obtenir des instructions détaillées, reportez-vous à la section [broche un carreau à un tableau de bord BI de puissance à partir d’un rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
    Accédez à la page de tableau de bord et ajuster la taille et l’emplacement de votre visualisations et modifier leurs titres. Pour trouver des instructions détaillées sur la façon de modifier votre mosaïque, consultez [Modifier une mosaïque : redimensionner, déplacer, renommer, code pin, supprimer, ajouter un lien hypertexte](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Voici un tableau de bord exemple avec certaines visualisations de chemin à froid épinglés à elle.  Selon la durée d’exécution de votre générateur de données, vos numéros sur les visualisations peuvent être différentes.
    <br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png)
<br/>
    -   Pour planifier l’actualisation des données, passez votre souris sur le groupe de données **PredictiveMaintenanceAerospace** , cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) , puis cliquez sur **Actualiser de la planification**.
<br/>
        **Remarque :** Si vous voyez un message d’avertissement, cliquez sur **Modifier les informations d’identification** et vous assurer que vos informations d’identification de base de données sont les mêmes que celles décrites à l’étape 1.
<br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png)
<br/>
    -   Développez la section de **l’Actualisation de la planification** . Activer « garder vos données à jour ».
    <br/>
    -   Planifier l’actualisation en fonction de vos besoins. Pour plus d’informations, voir [Actualiser des données BI d’alimentation](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Le programme d’installation de tableau de bord de chemin réactif

La procédure suivante vous guide comment visualiser la sortie des données en temps réel à partir de projets Analytique de flux qui ont été générés au moment du déploiement de la solution. Un compte [d’Analyse Décisionnelle de puissance en ligne](http://www.powerbi.com/) est nécessaire pour effectuer les étapes suivantes. Si vous n’avez pas un compte, vous pouvez [en créer un](https://powerbi.microsoft.com/pricing).

1.  Ajouter une sortie de puissance BI dans Azure flux Analytique (ASA).

    -  Vous devez suivre les instructions de [Analytique de flux Azure & BI de l’alimentation : un tableau de bord analytique en temps réel pour une visibilité en temps réel des données de flux de données](stream-analytics-power-bi-dashboard.md) pour définir le résultat de votre travail d’Analytique de flux Azure comme votre tableau de bord d’alimentation.
    - La requête ASA a trois sorties qui sont **aircraftmonitor**, **aircraftalert**et **flightsbyhour**. Vous pouvez afficher la requête en cliquant sur l’onglet requête. Correspondant à chacune de ces tables, vous devez ajouter une sortie vers ASA. Lorsque vous ajoutez le premier résultat (*par exemple,* **aircraftmonitor**) Vérifiez que **l’Alias de sortie**, le **Nom du groupe de données** et le **Nom de la Table** sont les mêmes (**aircraftmonitor**). Répétez les étapes pour ajouter des sorties de **aircraftalert**et **flightsbyhour**. Une fois que vous avez ajouté tous les trois tables de sortie et a démarré la tâche ASA, vous devez obtenir un message de confirmation (*par exemple*, « Départ flux analytique tâche maintenancesa02asapbi est réussi »).

2. Connectez-vous à [BI de puissance en ligne](http://www.powerbi.com)

    -   Sur le panneau gauche de la section des jeux de données dans mon espace de travail, les noms **aircraftmonitor**, **aircraftalert**et **flightsbyhour** ***groupe de données*** doivent apparaître. Voici les données en flux continu que vous poussé dans Azure flux Analytique à l’étape précédente. Le groupe de données **flightsbyhour** n’apparaîtra pas en même temps que les autres deux groupes de données en raison de la nature de la requête SQL sous-jacente. Toutefois, il doit s’afficher après une heure.
    -   Assurez-vous que le volet de ***visualisation*** est ouvert et qu’il est affiché sur le côté droit de l’écran.

3. Une fois que les données qui transitent dans BI d’alimentation, vous pouvez commencer à visualiser les données de transmission en continu. Ci-dessous un tableau de bord exemple avec certaines visualisations chemin réactif est épinglé à celui-ci. Vous pouvez créer d’autres tuiles du tableau de bord basés sur des groupes de données approprié. Selon la durée d’exécution de votre générateur de données, vos numéros sur les visualisations peuvent être différentes.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Voici quelques étapes pour créer une des mosaïques ci-dessus, la mosaïque de la « flotte vue de capteur 11 et seuil 48,26 » :

    -   Cliquez sur groupe de données **aircraftmonitor** sur le panneau gauche de section des jeux de données.

    -   Cliquez sur l’icône de **Graphique en courbes** .

    -   Dans le volet **champs** , cliquez sur **traitement** afin qu’il s’affiche sous « Axe » dans le volet de **visualisation** .

    -   Cliquez sur « s.11 » et « s.11\_alerte » afin qu’elles s’affichent sous « Valeurs ». Cliquez sur la flèche située en regard de **s.11** et **s.11\_alerte**, modifier la « Somme » à « Moyenne ».

    -   Cliquez sur **Enregistrer** dans la partie supérieure et le nom de l’état « aircraftmonitor ». Le rapport nommé « aircraftmonitor » s’affiche dans la section **rapports** dans le volet de **navigation** sur la gauche.

    -   Cliquez sur l’icône **Visual broche** sur le coin supérieur droit de ce graphique en courbes. Une fenêtre « Pin au tableau de bord » peut apparaître pour vous permettre de choisir un tableau de bord. Sélectionnez « Démonstration de Maintenance prédictive », puis cliquez sur « Ajouter ».

    -   Placez la souris sur cette mosaïque sur le tableau de bord, cliquez sur l’icône « Modifier » dans le coin supérieur droit pour modifier son titre à « Flotte vue de capteur 11 et seuil 48,26 » et un sous-titre à « Moyenne de flotte au fil du temps ».

## <a name="how-to-delete-your-solution"></a>**Comment faire pour supprimer votre solution**
Veuillez vous assurer que vous arrêtez le Générateur de données lorsque vous N'utilisez pas activement la solution en exécutant le Générateur de données générera des coûts plus élevés. Veuillez supprimer la solution si vous ne l’utilisez pas. Votre solution vous supprimez tous les composants mis en service dans votre abonnement lorsque vous avez déployé la solution. Pour supprimer la solution, cliquez sur votre nom dans le panneau gauche du modèle de solution de la solution et cliquez sur Supprimer.

## <a name="cost-estimation-tools"></a>**Outils d’estimation des coûts**

Les deux outils suivants sont disponibles pour vous aider à mieux comprendre les coûts impliqués dans l’exécution de la Maintenance PREVENTIVE aéronautique modèle de Solution de votre abonnement :

-   [Microsoft Azure coût outil estimation (en ligne)](https://azure.microsoft.com/pricing/calculator/)

-   [Outil Microsoft Azure coût estimateur (bureau)](http://www.microsoft.com/download/details.aspx?id=43376)
