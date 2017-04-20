<properties
    pageTitle="Prévisions dans le Guide technique de l’énergie de la demande | Microsoft Azure"
    description="Guide technique pour le modèle de Solution avec Microsoft Cortana Intelligence pour la prévision de l’énergie de la demande."
    services="cortana-analytics"
    documentationCenter=""
    authors="yijichen"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="inqiu;yijichen;ilanr9"/>

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guide technique pour le modèle de Solution Cortana Intelligence pour la prévision de l’énergie de la demande

## <a name="overview"></a>**Vue d’ensemble**

Modèles de solutions sont conçues pour accélérer le processus de création d’une démonstration E2E sur Cortana Intelligence Suite. Un modèle déployé provisionner votre abonnement avec composant de Cortana Intelligence nécessaire et créer les relations entre. Il alimente également le pipeline de données avec les données d’exemple générées à partir d’une application de simulation de données. Télécharger le simulateur de données à partir du lien fourni et l’installer sur votre ordinateur local, consultez le fichier readme.txt pour les instructions sur l’utilisation du simulateur. Données générées à partir du simulateur seront hydrate le pipeline de données et commencer à générer la prévision d’apprentissage machine qui peut ensuite être visualisée sur le tableau de bord d’alimentation.

Le modèle de solution se trouve [ici](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1) 

Le processus de déploiement va vous guider tout au long des différentes étapes pour configurer vos informations d’identification de la solution. Assurez-vous que vous enregistrez ces informations d’identification telles que le nom de la solution, nom d’utilisateur et mot de passe que vous fournissez au cours du déploiement.

L’objectif de ce document est d’expliquer l’architecture de référence et mis en service dans votre abonnement dans le cadre de ce modèle de Solution de différents composants. Le document parle également comment remplacer l’exemple de données avec les données réelles de votre choix pour pouvoir voir les analyses/prévisions à partir de vous a remporté des données. En outre, le document aborde les parties du modèle de Solution qui devra être modifié si vous souhaitez personnaliser la solution à vos propres données. Vous trouverez des instructions sur la façon de créer le tableau de bord d’alimentation pour ce modèle de Solution à la fin.

## <a name="big-picture"></a>**Vue d’ensemble**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Architecture expliqué
Lorsque la solution est déployée, divers services Azure dans Cortana Analytique Suite sont activés (*c'est-à-dire* événement pivot, flux Analytique, HDInsight, Data Factory, apprentissage automatique, *etc.*). Le schéma d’architecture ci-dessus montre, à un niveau élevé, comment la prévision de la demande pour le modèle de Solution de l’énergie est construite de bout en bout. Vous serez en mesure de rechercher ces services en cliquant dessus dans le diagramme de modèle de solution créé avec le déploiement de la solution. Les sections suivantes décrivent chaque élément.

## <a name="data-source-and-ingestion"></a>**Réception et la Source de données**

### <a name="synthetic-data-source"></a>Source de données synthétiques

Pour ce modèle de la source de données utilisée est générée à partir d’une application de bureau que vous allez télécharger et exécuter localement une fois le déploiement réussi. Vous trouverez les instructions pour télécharger et installer cette application dans la barre des propriétés lorsque vous sélectionnez le premier nœud appelé Simulator données de prévisions de l’énergie sur le diagramme de modèle de solution. Cette application flux le service [Concentrateur d’événements Azure](#azure-event-hub) avec points de données ou des événements, qui seront utilisés dans le reste du flux de la solution.

L’application de génération des événements remplissent le concentrateur d’événements Azure uniquement pendant qu’il est en cours d’exécution sur votre ordinateur.

### <a name="azure-event-hub"></a>Concentrateur d’événements Azure

Le service de [Concentrateur d’événements Azure](https://azure.microsoft.com/services/event-hubs/) est le destinataire de l’entrée fournie par la Source de données synthétiques décrite ci-dessus.

## <a name="data-preparation-and-analysis"></a>**Analyse et la préparation des données**


### <a name="azure-stream-analytics"></a>Analytique de flux Azure

Le service [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) permet de pratiquement en temps réel analytique sur le flux d’entrée à partir du service de [Concentrateur d’événements Azure](#azure-event-hub) et publier les résultats dans un tableau de bord [D’alimentation](https://powerbi.microsoft.com) ainsi que l’archivage de tous les événements entrants brutes pour le service de [Stockage Azure](https://azure.microsoft.com/services/storage/) pour un traitement ultérieur par le service [Factory de données Azure](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hd-insights-custom-aggregation"></a>Agrégation personnalisée de perspectives sur la HD

Le service d’Azure HD Insight est utilisé pour exécuter des scripts de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orchestrés par Azure Data Factory) pour fournir des agrégations sur les événements bruts qui ont été archivés en utilisant le service Analytique de flux Azure.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

Le service de [Formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) est utilisé (orchestrés par Azure Data Factory) pour effectuer la prévision de consommation future d’une région donnée, étant donnée les entrées reçues.

## <a name="data-publishing"></a>**Publication de données**


### <a name="azure-sql-database-service"></a>Service de base de données SQL Azure

Le service de [Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/) est utilisé pour stocker (gérés par Azure Data Factory) les prévisions reçues par le service de formation de Machine Azure qui va être consommé dans le tableau de bord [D’alimentation](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consommation des données**

### <a name="power-bi"></a>Alimentation BI

Le service de [Puissance BI](https://powerbi.microsoft.com) est utilisé pour afficher un tableau de bord qui contient des agrégations fourni par l' [Analytique de flux Azure](https://azure.microsoft.com/services/stream-analytics/) service ainsi qu’à la demande des prévisions, stockées dans la [Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/) produites à l’aide du service de [Formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) . Pour obtenir des Instructions sur la façon de créer le tableau de bord d’alimentation pour ce modèle de la Solution, reportez-vous à la section ci-dessous.

## <a name="how-to-bring-in-your-own-data"></a>**Comment faire pour importer vos propres données.**

Cette section décrit comment mettre vos propres données à Azure et les zones nécessiterait des modifications pour les données que vous mettez dans cette architecture.

Il est peu probable que n’importe quel groupe de données que vous amener renverrait le groupe de données utilisé pour ce modèle de la solution. Comprendre les exigences et vos données seront des éléments cruciaux dans comment vous modifiez ce modèle pour travailler avec vos propres données. S’il s’agit de votre première exposition au service formation de Machine d’Azure, vous pouvez obtenir une introduction à celui-ci à l’aide de l’exemple de [la création de votre première expérience](machine-learning\machine-learning-create-experiment.md).

Les paragraphes suivants examinent les sections du modèle qui requièrent des modifications lors de l’introduction d’un nouveau groupe de données.

### <a name="azure-event-hub"></a>Concentrateur d’événements Azure

Le service de [Concentrateur d’événements Azure](https://azure.microsoft.com/services/event-hubs/) est très générique, telles que les données peuvent être validées sur le concentrateur au format CSV ou JSON. Aucun traitement spécial n’intervient dans le concentrateur d’événements Azure, mais il est important de que comprendre les données introduites dans il.

Ce document ne décrit pas comment intégrer vos données, mais un peut facilement envoyer les événements ou les données à un concentrateur d’événements Azure, à l’aide de l' [API de concentrateur d’événements](event-hubs\event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Analytique de flux Azure

Le service [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) sert à fournir près analytique en temps réel en lisant à partir du flux de données et la sortie de données vers n’importe quel nombre de sources.

Pour la prévision de la demande pour le modèle de Solution de l’énergie, la requête Analytique de flux Azure se compose de deux sous-requêtes, chaque consommation d’événements du service de concentrateur d’événements Azure comme entrées et avoir les sorties à deux emplacements distincts. Ces sorties comprennent BI de puissance d’un groupe de données et un emplacement de stockage Azure.

Vous trouverez la requête [Analytique de flux Azure](https://azure.microsoft.com/services/stream-analytics/) en :

-   Se connecter au [portail de gestion Azure](https://manage.windowsazure.com/)

-   Localiser les travaux analytique de flux ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) qui ont été générés lors du déployée de la solution. L’une est pour pousser des données blob de stockage (par exemple, mytest1streaming432822asablob) et l’autre est pour transmettre des données à alimentation BI (par exemple, mytest1streaming432822asapbi).


-   Sélection

    -   ***Entrées*** pour afficher les entrées de la requête

    -   ***Requête*** pour afficher la requête elle-même

    -   ***Sorties*** pour afficher les différentes sorties

Vous trouverez des informations sur la construction de la requête Analytique de flux d’Azure dans la [Référence de requête Analytique de flux](https://msdn.microsoft.com/library/azure/dn834998.aspx) sur MSDN.

Dans cette solution, le projet Azure flux Analytique qui génère le dataset avec près d’informations analytique en temps réel sur le flux de données entrant à un tableau de bord d’alimentation est fourni dans le cadre de ce modèle de la solution. Car il y a une connaissance implicite sur le format de données entrantes, ces requêtes doit être modifié en fonction de votre format de données.

L’autre travail Azure flux Analytique affiche tous les événements de [Concentrateur d’événements](https://azure.microsoft.com/services/event-hubs/) au [Stockage Azure](https://azure.microsoft.com/services/storage/) et ne requiert, par conséquent, aucune modification, quel que soit votre format de données que les informations d’événement complet sont diffusé en continu au stockage.

### <a name="azure-data-factory"></a>Usine de données Azure

Le service de [Fabrique de données Azure](https://azure.microsoft.com/documentation/services/data-factory/) orchestre le mouvement et le traitement des données. Dans la prévision de la demande pour le modèle de Solution de l’énergie le factory de données se compose de douze [pipelines](data-factory\data-factory-create-pipelines.md) déplacer et traiter les données à l’aide de différentes technologies.

  Vous pouvez accéder à votre fabrique de données en ouvrant le nœud Data Factory au bas du diagramme de modèle de solution créé avec le déploiement de la solution. Ceci vous dirigera sur le factory de données sur le portail de gestion Azure. Si vous constatez des erreurs dans vos groupes de données, vous pouvez ignorer ceux qu’ils sont en raison de la fabrique de données en cours de déploiement avant le démarrage du Générateur de données. Ces erreurs n’empêchent pas votre fabrique de données fonctionne.

Cette section traite de la nécessaire [pipelines](data-factory\data-factory-create-pipelines.md) et [activités](data-factory\data-factory-create-pipelines.md) contenus dans [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Voici l’affichage du schéma de la solution.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinq des tuyaux de cette fabrique de contenir des scripts de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) qui servent à partitionner et regrouper les données. Lorsque vous avez noté, les scripts se situeront dans le compte de [Stockage Azure](https://azure.microsoft.com/services/storage/) créé lors de l’installation. Leur emplacement sera : demandforecasting\\\\script\\\\ruche\\ \\ (ou https://[Your solution name].blob.core.windows.net/demandforecasting).

Comme pour les requêtes [Azure flux Analytique](#azure-stream-analytics-1) , les scripts de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) ont une connaissance implicite sur le format de données entrantes, ces requêtes devra être modifié en fonction de vos besoins [d’ingénierie de fonctionnalité](machine-learning\machine-learning-feature-selection-and-engineering.md) et de format de données.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*

Ce pipeline [pipeline](data-factory\data-factory-create-pipelines.md) contient une activité unique - une activité [HDInsightHive](data-factory\data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) à agréger la toutes les 10 secondes diffusé en continu dans les données de la demande dans le niveau sous-station toutes les heures au niveau de la région et le placer dans le [Stockage Azure](https://azure.microsoft.com/services/storage/) la tâche d’Azure flux Analytique.

Le script de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche de partitionnement est ***AggregateDemandRegion1Hr.hql***


#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*

Ce [pipeline](data-factory\data-factory-create-pipelines.md) contient deux activités :
- Activité [HDInsightHive](data-factory\data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script de la ruche pour agréger les données de la demande historique horaire niveau sous-station au niveau de la zone horaire et la placer dans le stockage Azure pendant la tâche Analytique de flux de données Azure

- Activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les données agrégées à partir de l’objet blob de stockage Azure à la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

Le script de [la ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche est ***AggregateDemandHistoryRegion.hql***.


#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*

Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent plusieurs activités et dont le résultat final est les prévisions évaluées à partir de l’expérience d’apprentissage automatique de Azure associé à ce modèle de solution. Ils sont presque identiques, mais chacun d’eux gère uniquement l’autre région qui est effectuée par les différente RegionID passé dans le pipeline du chargeur automatique de documents et le script de la ruche pour chaque région.  
Les activités contenues dans ce sont :
-   Activité [HDInsightHive](data-factory\data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script de la ruche pour effectuer des agrégations et l’ingénierie de la fonctionnalité nécessaire à l’expérience d’apprentissage automatique de Azure. Les scripts de la ruche pour cette tâche sont respectives ***PrepareMLInputRegionX.hql***.

-   Activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’activité [HDInsightHive](data-factory\data-factory-hive-activity.md) dans un blob Azure Storage unique qui peut être pas accès à l’activité de la [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) activité qui appelle l’apprentissage Machine Azure expérimenter les résultats dans les résultats dans un stockage Azure blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une activité unique - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’expérience d’apprentissage automatique de Azure le respectifs ***MLScoringRegionXPipeline*** vers la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Cette [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une activité unique - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les données agrégées de la demande en cours à partir de ***LoadHistoryDemandDataPipeline*** à la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une activité unique - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les données de référence de la sous-station/région/Topologygeo qui sont téléchargées sur les blob Azure Storage dans le cadre de l’installation du modèle de solution à la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure
L’expérience [d’Apprentissage automatique de Azure](https://azure.microsoft.com/services/machine-learning/) utilisé pour ce modèle de solution fournit la prévision de la demande de la région. L’expérience est spécifique à l’ensemble de données consommée et donc nécessitera modification ou remplacement spécifique pour les données qui s’affiche dans.

## <a name="monitor-progress"></a>**Surveiller la progression**
Une fois que le Générateur de données est lancé, le pipeline commence à obtenir hydraté et les différents composants de votre solution de démarrent son en action suivant les commandes émises par l’usine de données. Il existe deux méthodes que vous pouvez surveiller le pipeline.

1. Vérifiez les données de stockage des objets Blob Azure.

    Une de la tâche de flux de données Analytique écrit des données brutes entrantes pour le stockage des objets blob. Si vous cliquez sur le composant de **Stockage des objets Blob Azure** de votre solution à partir de l’écran vous correctement déployé la solution et puis cliquez sur **Ouvrir** dans le panneau de droite, il vous amènera sur le [portail de gestion Azure](https://portal.azure.com). Une seule fois, cliquez sur les **objets BLOB**. Dans l’écran suivant, vous verrez une liste de conteneurs. Cliquez sur **« energysadata »**. Dans l’écran suivant, vous verrez le dossier **« demandongoing »** . Dans le dossier rawdata, vous verrez des dossiers avec des noms comme date = 2016-01-28 etc.. Si vous voyez ces dossiers, il indique que les données brutes sont correctement générée sur votre ordinateur et stockées dans le stockage blob. Vous devez voir les fichiers qui doivent avoir des tailles finies en Mo dans ces dossiers.

2. Vérifiez les données de base de données de SQL Azure.

    La dernière étape du pipeline consiste à écrire les données (par exemple, des prévisions à partir de l’apprentissage automatique) dans la base de données de SQL. Vous devrez peut-être attendre un maximum de 2 heures pour les données à afficher dans la base de données de SQL. Il est une façon de contrôler la quantité de données est disponible dans votre base de données SQL via le [portail de gestion Azure](https://manage.windowsazure.com/). Dans le volet gauche, localisez les bases de données SQL![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) et cliquez dessus. Recherchez votre base de données (par exemple, demo123456db), puis cliquez dessus. Sur la page suivante, sous la section **« Se connecter à votre base de données »** , cliquez sur **« Exécuter Transact-SQL des requêtes par rapport à votre base de données SQL »**.

    Ici, vous pouvez cliquer sur Nouvelle requête et la requête pour le nombre de lignes (par exemple, « select count à partir de DemandRealHourly) » à mesure que votre base de données augmente, le nombre de lignes dans la table doit augmenter.)

3. Vérifier les données à partir du tableau de bord d’alimentation.

    Vous pouvez paramétrer la puissance à chaud de chemin d’accès au tableau de bord pour surveiller des données brutes entrantes. Veuillez suivre les instructions de la section « Alimentation tableau de bord ».



## <a name="power-bi-dashboard"></a>**Tableau de bord d’alimentation**

### <a name="overview"></a>Vue d’ensemble

Cette section décrit la configuration d’alimentation tableau de bord pour visualiser vos données en temps réel à partir d’analytique de flux Azure (chemin réactif), ainsi que les prévisions à partir de la machine Azure cursus (à froid).


### <a name="setup-hot-path-dashboard"></a>Le programme d’installation de tableau de bord de chemin réactif

La procédure suivante vous guide comment visualiser la sortie des données en temps réel à partir de projets Analytique de flux qui ont été générés au moment du déploiement de la solution. Un compte [d’Analyse Décisionnelle de puissance en ligne](http://www.powerbi.com/) est nécessaire pour effectuer les étapes suivantes. Si vous n’avez pas un compte, vous pouvez [en créer un](https://powerbi.microsoft.com/pricing).

1.  Ajouter une sortie de puissance BI dans Azure flux Analytique (ASA).

    -  Vous devez suivre les instructions de [Analytique de flux Azure & BI de l’alimentation : un tableau de bord analytique en temps réel pour une visibilité en temps réel des données de flux de données](stream-analytics-power-bi-dashboard.md) pour définir le résultat de votre travail d’Analytique de flux Azure comme votre tableau de bord d’alimentation.

    - Recherchez la tâche analytique de flux dans votre [portail de gestion Azure](https://manage.windowsazure.com). Le nom de la tâche doit être : YourSolutionName + « streamingjob » + nombre aléatoire + « asapbi » (c'est-à-dire demostreamingjob123456asapbi).

    - Ajouter une sortie du PowerBI pour le travail d’ASA. Définir l' **Alias de sortie** comme **'PBIoutput'**. Définissez votre **Nom de groupe de données** et le **nom de la Table** en tant que **« EnergyStreamData »**. Après avoir ajouté la sortie, cliquez sur **« Démarrer »** en bas de la page pour démarrer la tâche de flux de données Analytique. Vous devez obtenir un message de confirmation (*par exemple*, « Départ flux analytique tâche myteststreamingjob12345asablob est réussie »).

2. Connectez-vous à [BI de puissance en ligne](http://www.powerbi.com)

    -   Sur le panneau gauche de la section des jeux de données dans mon espace de travail, vous devez être en mesure de voir un nouveau dataset affichant sur le panneau gauche de puissance BI. Voici les données en flux continu que vous poussé dans Azure flux Analytique à l’étape précédente.

    -   Assurez-vous que le volet de ***visualisation*** est ouvert et qu’il est affiché sur le côté droit de l’écran.

3. Créer la mosaïque « À la demande par horodatage » :
    -   Cliquez sur le groupe de données **'EnergyStreamData'** dans le panneau gauche de section des jeux de données.

    -   Cliquez sur **« graphique en courbes »** icône ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

    -   Cliquez sur 'EnergyStreamData' dans le panneau **champs** .

    -   Cliquez sur **« Timestamp »** et assurez-vous qu’il s’affiche sous « Axe ». Cliquez sur **« Load »** et assurez-vous qu’il s’affiche sous « Valeurs ».

    -   Cliquez sur **Enregistrer** dans la partie supérieure et de nommer le rapport en tant que « EnergyStreamDataReport ». Le rapport nommé « EnergyStreamDataReport » apparaît dans la section rapports dans le volet de navigation gauche.

    -   Cliquez sur **« code Pin Visual »** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) icône dans le coin supérieur droit de ce graphique en courbes, une fenêtre « Pin au tableau de bord » peut-être s’afficheront choisir un tableau de bord. Sélectionnez « EnergyStreamDataReport », puis cliquez sur « Ajouter ».

    -   Placez la souris sur cette mosaïque sur le tableau de bord, cliquez sur « Modifier » icône sur le haut à droite pour modifier son titre comme « À la demande par horodatage »

4.  Créer d’autres tuiles du tableau de bord basés sur des groupes de données approprié. L’affichage du tableau de bord final est présenté ci-dessous.
        ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### <a name="setup-cold-path-dashboard"></a>Le programme d’installation de tableau de bord de chemin à froid
Dans le pipeline de données de chemin à froid, l’objectif essentiel est d’obtenir la prévision de la demande de chaque région. BI d’alimentation se connecte à une base de données Azure SQL comme sa source de données, où se trouvent les résultats de la prévision.

> [AZURE.NOTE] 1) il faut plusieurs heures pour collecter suffisamment de prévision des résultats du tableau de bord. Nous vous recommandons de que commencer ce processus de 2 à 3 heures après que vous déjeuner le Générateur de données. 2) dans cette étape, la condition préalable est pour télécharger et installer le logiciel gratuit [d’alimentation BI bureau](https://powerbi.microsoft.com/desktop).



1.  Obtenir les informations d’identification de base de données.

    Vous devrez le **nom du serveur de base de données, nom de la base de données, nom d’utilisateur et mot de passe** avant de passer aux étapes suivantes. Voici la procédure pour vous guider comment les rechercher.

    -   Une fois **« de base de données SQL Azure »** sur le diagramme de modèle de solution s’affiche en vert, cliquez dessus, puis cliquez sur **« Ouvrir »**. Vous serez guidé à un portail de gestion Azure et votre page d’informations de base de données sera également ouvert.

    -   Sur la page, vous trouverez une section « Database ». Il répertorie la base de données que vous avez créé. Le nom de votre base de données doit être **« Votre nom de la Solution + nombre aléatoire + 'BD' »** (par exemple, « mytest12345db »).

    -   Cliquez sur votre base de données, dans le nouveau pop out pannel, vous pouvez trouver le nom de votre serveur de base de données dans la partie supérieure. Votre devraient de nom de nom de serveur de base de données est **« Votre nom de la Solution + nombre aléatoire + 'database.windows.net,1433' »** (par exemple, « mytest12345.database.windows.net,1433 »).

    -   Votre base de données, **nom d’utilisateur** et le **mot de passe** sont le même que le nom d’utilisateur et le mot de passe précédemment enregistrés pendant le déploiement de la solution.

2.  Mise à jour de la source de données du fichier courant BI chemin à froid
    -  Assurez-vous que vous avez installé la dernière version de [bureau de puissance BI](https://powerbi.microsoft.com/desktop).

    -   Dans le dossier **« DemandForecastingDataGeneratorv1.0 »** que vous avez téléchargé, double-cliquez sur le fichier **« Template\DemandForecastPowerBI.pbix de BI d’alimentation »** . Les visualisations initiales sont basées sur des données fictives. **Remarque :** Si vous voyez une erreur de massage, veuillez vous assurer que vous avez installé la dernière version de bureau de BI d’alimentation.

        Une fois que vous l’ouvrez, en haut du fichier, cliquez sur **« Modifier les requêtes »**. Dans la fenêtre contextuelle, double cliquez sur **'Source'** dans le panneau de droite.
    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

    -   Dans la fenêtre contextuelle, remplacer **« Serveur »** et **« Database »** avec votre propre nom du serveur et de base de données, puis cliquez sur **« OK »**. Nom de serveur, assurez-vous de que spécifier le port 1433 (**YourSolutionName.database.windows.net, 1433**). Ignorer les messages d’avertissement qui s’affichent à l’écran.

    -   Dans le protocole pop suivant à la fenêtre, vous verrez deux options dans le volet gauche (**Windows** et **base de données**). Cliquez sur **« Database »**, indiquez **« Username »** et **« Password »** (c’est le nom d’utilisateur et le mot de passe lorsque vous d’abord déployé la solution et créé une base de données Azure SQL). Dans ***Sélectionnez le niveau à appliquer ces paramètres à***, vérifiez l’option de niveau de base de données. Cliquez ensuite sur **« Se connecter »**.

    -   Une fois que vous êtes guidé à la page précédente, fermez la fenêtre. Un message s’affichera out - cliquez sur **Appliquer**. Enfin, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications. Votre fichier BI de puissance a maintenant établie la connexion au serveur. Si votre visualisations sont vides, veillez à que désactiver les sélections sur les visualisations pour visualiser toutes les données en cliquant sur l’icône représentant une gomme dans le coin supérieur droit de légendes. Utilisez le bouton Actualiser pour refléter les nouvelles données sur les visualisations. Initialement, vous verrez uniquement les données d’amorçage sur votre visualisations comme le factory de données est planifié pour actualiser toutes les 3 heures. Après 3 heures, vous verrez les nouvelles prévisions reflétées dans votre visualisations lorsque vous actualisez les données.

3. (Facultatif) Publier le tableau de bord de chemin à froid à [BI de puissance en ligne](http://www.powerbi.com/). Notez que cette étape a besoin d’un compte de puissance BI (ou le compte de l’Office 365).

    -   Cliquez sur **« Publier »** et quelques secondes plus tard une fenêtre affiche « Publication d’alimentation BI succès ! » avec une coche verte. Cliquez sur le lien ci-dessous « Ouvrir demoprediction.pbix BI de puissance ». Pour obtenir des instructions détaillées, voir la [publication d’alimentation BI bureau](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Pour créer un nouveau tableau de bord : cliquez sur le **+** en regard de la section **tableaux de bord** dans le volet gauche. Entrez le nom « Demo de prévision à la demande » pour ce tableau de bord.

    -   Une fois que vous ouvrez le rapport, cliquez sur ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) pour épingler toutes les visualisations à votre tableau de bord. Pour obtenir des instructions détaillées, reportez-vous à la section [broche un carreau à un tableau de bord BI de puissance à partir d’un rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
        Accédez à la page de tableau de bord et ajuster la taille et l’emplacement de votre visualisations et modifier leurs titres. Pour trouver des instructions détaillées sur la façon de modifier votre mosaïque, consultez [Modifier une mosaïque : redimensionner, déplacer, renommer, code pin, supprimer, ajouter un lien hypertexte](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Voici un tableau de bord exemple avec certaines visualisations de chemin à froid épinglés à elle.

        ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Facultatif) Actualisation de la planification de la source de données.
    -     Pour planifier l’actualisation des données, passez votre souris sur le groupe de données **EnergyBPI-Final** , cliquez sur ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) , puis cliquez sur **Actualiser de la planification**.
    **Remarque :** Si vous voyez un message d’avertissement, cliquez sur **Modifier les informations d’identification** et vous assurer que vos informations d’identification de base de données sont les mêmes que celles décrites à l’étape 1.

    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

    -   Développez la section de **l’Actualisation de la planification** . Activer « garder vos données à jour ».

    -   Planifier l’actualisation en fonction de vos besoins. Pour plus d’informations, voir [Actualiser des données BI d’alimentation](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## <a name="how-to-delete-your-solution"></a>**Comment faire pour supprimer votre solution**
Veuillez vous assurer que vous arrêtez le Générateur de données lorsque vous N'utilisez pas activement la solution en exécutant le Générateur de données générera des coûts plus élevés. Veuillez supprimer la solution si vous ne l’utilisez pas. Votre solution vous supprimez tous les composants mis en service dans votre abonnement lorsque vous avez déployé la solution. Pour supprimer la solution, cliquez sur votre nom dans le panneau gauche du modèle de solution de la solution et cliquez sur Supprimer.

## <a name="cost-estimation-tools"></a>**Outils d’Estimation des coûts**

Les deux outils suivants sont disponibles pour vous aider à mieux comprendre le total des coûts impliqués dans l’exécution la prévision de la demande pour le modèle de Solution de l’énergie dans votre abonnement :

-   [Microsoft Azure coût outil estimation (en ligne)](https://azure.microsoft.com/pricing/calculator/)

-   [Outil Microsoft Azure coût estimateur (bureau)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Accusés de réception**
Cet article est créé par le chercheur de données Yijing Chen et les ingénieur Qiu Min à Microsoft.
