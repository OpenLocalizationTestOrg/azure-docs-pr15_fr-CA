<properties
    pageTitle="Vue d’ensemble de la Science de données à l’aide d’allumage sur Azure HDInsight | Microsoft Azure"
    description="La boîte à outils MLlib d’allumage apporte apprentissage machine considérable modélisation des fonctionnalités à l’environnement distribué de HDInsight."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Vue d’ensemble de la Science de données à l’aide d’allumage sur Azure HDInsight

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cet ensemble de rubriques Comment HDInsight Spark permet d’effectuer des tâches courantes de science des données telles que la réception des données, l’ingénierie de fonctionnalité, modélisation et évaluation du modèle. Les données utilisées sont un échantillon de 2013 NYC taxi voyage et le tarif de groupe de données. Les modèles intégrés incluent la régression logistique et linéaire, aléatoires, arborescences et des forêts dégradés amplifiées. Les rubriques indiquent également comment stocker ces modèles dans le stockage blob Azure (WASB) et score et d’évaluer leurs performances prédictive. Plus avancées rubriques comment les modèles peuvent être formés à l’aide de balayage de validation croisée et paramètre hyper. Cette rubrique décrit également comment configurer le cluster étincelle dont vous avez besoin d’effectuer les étapes dans les trois procédures pas à pas fournies. 

[Allumage](http://spark.apache.org/) est un framework qui prend en charge le traitement en mémoire pour augmenter les performances des applications analytiques de données volumineuses de traitement en parallèle open source. Moteur de traitement et est conçu pour la vitesse, facilité d’utilisation et analytique sophistiquées. Calcul distribué de mémoire fonctionnalités d’allumage rendre un bon choix pour les algorithmes itératifs dans les calculs de formation et graphique de machine. [MLlib](http://spark.apache.org/mllib/) est bibliothèque de formation de machine évolutive d’allumage qui apporte des fonctions de modélisation à cet environnement distribué. 

[HDInsight allumage](../hdinsight/hdinsight-apache-spark-overview.md) est l’Azure offre hébergée de l’allumage de l’open source. Il inclut également la prise en charge pour les **ordinateurs portables de Jupyter PySpark** sur le cluster d’allumage qui peut exécuter des requêtes interactives d’allumage SQL pour la transformation, le filtrage et la visualisation des données stockées dans les objets BLOB Azure (WASB). PySpark est l’API de Python pour l’allumage. Les extraits de code qui fournissent les solutions et afficher les placettes pertinentes pour visualiser les données ici s’exécutées dans des blocs-notes Jupyter installés sur des clusters de l’allumage. Les étapes de modélisation de ces rubriques contiennent du code qui montre comment former, évaluer, enregistrer et utiliser chaque type de modèle. 

Les étapes de l’installation et le code fourni dans cette procédure pas à pas est de HDInsight 3.4 étincelle 1.6. Cependant, le code ici et dans les ordinateurs portables est générique et devrait fonctionner sur n’importe quel cluster d’allumage. Si vous n’utilisez pas HDInsight Spark, les étapes d’installation et de gestion de cluster peuvent être légèrement différents de ce qui est présenté ici.

## <a name="prerequisites"></a>Conditions préalables

1 vous devez disposer d’un abonnement Azure. Si vous n’en avez pas encore, reportez-vous à la section [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. vous devez un cluster HDInsight 3.4 étincelle 1.6 pour effectuer cette procédure pas à pas. Pour en créer une, consultez les instructions fournies dans [mise en route : créer l’allumage d’Apache sur Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). La version et le type de cluster est spécifiée dans le menu **Sélectionner le Type de Cluster** . 


![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [AZURE.NOTE] Une rubrique qui montre comment utiliser Scala plutôt que les Python pour effectuer les tâches d’un processus de science des données de bout en bout, consultez [Science des données à l’aide de Scala avec allumage sur Azure](machine-learning-data-science-process-scala-walkthrough.md).

<!-- -->

>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="the-nyc-2013-taxi-data"></a>Les données NYC 2013 Taxi

Les données de voyage de Taxi NYC sont environ 20 Go sur les fichiers compressés valeurs séparées par des virgules (CSV) (Go ~ 48 non compressé), comprenant les tarifs et les voyages individuels de plus de 173 millions payé pour chaque séjour. Chaque enregistrement de voyage inclut les prélèvement et téléphoné et temps, hack rendues anonymes (pilote) numéro de licence et nombre de medallion (numéro d’identification du taxi unique). Les données couvre tous les déplacements de l’année 2013 et sont fournies dans les deux jeux de données suivantes pour chaque mois :

1. Les fichiers CSV de 'trip_data' contient les détails du voyage, tels que le nombre de passagers, prélèvement et cette chute pointe, déclenchement de durée et la longueur du voyage. Voici quelques exemples d’enregistrements :

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Les fichiers CSV de 'trip_fare' contient des détails sur le prix payé pour chaque voyage, telles que le type de paiement, tarif, surcharge et taxes, conseils et péages et le montant total payé. Voici quelques exemples d’enregistrements :

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


Nous avons pris un échantillon de 0,1 % de ces fichiers et rejoint le voyage\_données et voyage\_fichiers CVS du prix dans un dataset unique à utiliser comme le groupe de données d’entrée pour cette procédure pas à pas. Clé unique pour joindre le voyage\_données et voyage\_tarif est composée des champs : medallion, hack\_certificat et capteur\_datetime. Chaque enregistrement du groupe de données contient les attributs suivants représentant un voyage NYC Taxi :


|Champ| Brève Description
|------|---------------------------------
| medallion |Medallion de taxi rendues anonymes (id unique taxi)
| hack_license |    Numéro de licence de transport de Hackney de NEGOCIATIONS
| vendor_id |   Id de fournisseur de taxi
| rate_code | Taux de taxi NYC de tarif
| store_and_fwd_flag | Banque d’informations et l’indicateur vers l’avant
| pickup_datetime | Prélever à la date et l’heure
| dropoff_datetime | Cette chute date et heure
| pickup_hour | Choisir les horaires
| pickup_week | Choisir une semaine de l’année
| jour de la semaine | Jour de la semaine (plage 1-7)
| passenger_count | Nombre de passagers sur un trajet de taxi
| trip_time_in_secs | Durée en secondes
| trip_distance | Distance de voyage parcourue en miles
| pickup_longitude | Prélever la longitude
| pickup_latitude | Prélever latitude
| dropoff_longitude | Longitude de cette chute
| dropoff_latitude | Cette chute latitude
| direct_distance | Diriger la distance entre le prélèvement des emplacements de cette chute et
| payment_type | Type de paiement (autorités de certification, etc. de la carte de crédit).
| fare_amount | Montant frais
| supplément | Supplément
| mta_tax | Taxe du MTA
| tip_amount | Montant du pourboire
| tolls_amount | Montant des péages
| total_amount | Montant total
| Bonhomme de neige | Bonhomme de neige (0/1 pour non ou Oui)
| tip_class | Conseil de classe (0 : $0, 1 : 0-5 $ 2 : $6-10, 3 : $11 à 20, 4 : 20 >)


## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Exécuter du code à partir d’un ordinateur portable de Jupyter sur le cluster d’allumage 

Vous pouvez lancer le bloc-notes Jupyter à partir du portail Azure. Rechercher votre cluster étincelle sur votre tableau de bord et cliquez dessus pour entrer la page de gestion de votre cluster. Pour ouvrir le bloc-notes associé au cluster d’allumage, cliquez sur **Tableaux de bord de Cluster** -> **Jupyter bloc-notes** .

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

Vous pouvez également accéder à ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** pour accéder à la Jupyter de portables. Remplacer la partie NOM_CLUSTER de cette URL avec le nom de votre cluster. Vous devez le mot de passe pour votre compte d’administrateur accéder aux ordinateurs portables.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Sélectionnez PySpark pour afficher un répertoire contenant des exemples des ordinateurs portables qui utilisent l’API PySpark. Les ordinateurs portables qui contiennent les exemples de code pour cette suite de rubrique d’allumage sont disponibles sur [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)


Vous pouvez télécharger les portables directement à partir de Github au serveur Jupyter ordinateur portable sur votre cluster de l’explosion. Dans la page d’accueil de votre Jupyter, cliquez sur le bouton **Télécharger** dans la partie droite de l’écran. Un Explorateur de fichiers s’ouvre. Ici, vous pouvez coller l’URL Github (contenu brut) de l’ordinateur portable et cliquez sur **Ouvrir**. Les ordinateurs portables de PySpark sont disponibles à l’URL suivantes :

1.  [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.  [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3.  [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Vous voyez le nom du fichier dans votre liste de fichiers Jupyter avec un bouton **Télécharger** à nouveau. Cliquez sur ce bouton **Télécharger** . Maintenant, vous avez importé le bloc-notes. Répétez ces étapes pour télécharger les autres portables de cette procédure pas à pas.

> [AZURE.TIP] Vous pouvez cliquez sur les liens de votre navigateur et cliquez sur **Copier le lien** pour obtenir l’URL de contenu brut de github. Vous pouvez coller cette URL dans la boîte de dialogue Explorateur Jupyter télécharger des fichiers.

Vous pouvez maintenant :

- Consultez le code en cliquant sur l’ordinateur portable.
- Exécutez chaque cellule en appuyant sur **MAJ + ENTRÉE**.
- Exécutez le bloc-notes en cliquant sur **cellule** -> **exécuter**.
- Utiliser la visualisation automatique des requêtes.

> [AZURE.TIP] Le noyau PySpark affiche automatiquement les résultats de requêtes SQL (HiveQL). Vous disposez de l’option à sélectionner parmi les différents types de visualisations (Table, secteur, ligne, zone ou barre) en utilisant les boutons de menu de **Type** dans le bloc-notes :

![Courbe ROC de régression logistique pour l’approche générique](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Quel est l’avenir ?

Sont configurés avec un cluster HDInsight Spark et a chargé les portables de la Jupyter, vous êtes prêt à utiliser les rubriques qui correspondent à la PySpark de trois portables. Ils montrent comment Explorer les données, puis comment créer et utiliser des modèles. Le bloc-notes de l’exploration et la modélisation avancée des données indique comment inclure la validation croisée, hyper-paramètre de balayage et le modèle d’évaluation. 

**L’Exploration de données et modélisation avec allumage :** Explorer le groupe de données et créer, score et évaluer l’apprentissage modèles en passant par la rubrique [créer une classification binaire et modèles de régression pour les données à l’aide de la boîte à outils MLlib d’allumage](machine-learning-data-science-spark-data-exploration-modeling.md) de l’ordinateur.

**Consommation de modèle :** Pour savoir comment évaluer les modèles de classification et de régression créés dans cette rubrique, consultez [Score et l’évaluation des modèles de formation intégré d’allumage de machine](machine-learning-data-science-spark-model-consumption.md).

**Balayage de hyperparameter et validation croisée**: voir [avancées d’exploration de données et modélisation avec allumage](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sur comment les modèles peuvent être formés à l’aide de balayage de validation croisée et paramètre hyper

