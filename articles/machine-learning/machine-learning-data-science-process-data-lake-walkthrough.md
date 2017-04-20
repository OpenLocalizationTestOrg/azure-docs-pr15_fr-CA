<properties
    pageTitle="Science de données évolutive dans Azure données lac : une procédure pas à pas de bout en bout de | Microsoft Azure"
    description="Comment utiliser du lac de données Azure pour effectuer des tâches données binaires et l’exploration de la classification sur un groupe de données."  
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
    ms.author="bradsev;weig"/>


# <a name="scalable-data-science-in-azure-data-lake-an-end-to-end-walkthrough"></a>Science de données évolutive dans Azure données lac : une procédure pas à pas de bout en bout

Cette procédure pas à pas montre comment lac de données Azure permet de faire d’exploration de données et les tâches de classification binaire sur un échantillon de voyage taxi NYC et dataset afin de prédire si une info-bulle sera payée par un tarif du prix. Il vous guide à travers les étapes du [Processus d’équipe données scientifiques](http://aka.ms/datascienceprocess), de bout en bout, de l’acquisition de données pour modéliser la formation et au déploiement d’un service web qui publie le modèle.


### <a name="azure-data-lake-analytics"></a>Données Azure lac Analytique

Le [Lac de données de Microsoft Azure](https://azure.microsoft.com/solutions/data-lake/) a toutes les fonctionnalités requises pour faciliter le travail de scientifiques de données pour stocker des données de toute taille, de forme et de vitesse et d’effectuer le traitement des données, analytique avancée et machine de modélisation d’apprentissage avec une évolutivité élevée à moindre coût.   Vous payez sur une base par projet, uniquement lorsque les données sont effectivement en cours de traitement. Analytique de LAC données Azure inclut U-SQL, un langage qui fusionne la nature déclarative de SQL avec la puissance expressive de C# pour fournir évolutives distribuées fonctionnalité de requête. Il vous permet de traiter des données non structurées en appliquant le schéma lors de la lecture, insérer une logique personnalisée et des fonctions de défini par l’utilisateur (UDF) et inclut l’extensibilité pour permettre la granularité fine de contrôle sur le mode d’exécution à grande échelle. Pour en savoir plus sur la philosophie de conception derrière U-SQL, consultez le [blog Visual Studio valider](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Données lac Analytique est également un élément clé de la Suite d’Analytique Cortana et fonctionne avec l’entrepôt de données de SQL Azure, alimentation BI et Data Factory. Cela vous offre une données de gros nuage complète et plate-forme d’analytique avancée.

Cette procédure pas à pas commence par décrire les conditions requises et les ressources qui sont nécessaires pour effectuer les tâches avec les données lac Analytique qui forment le processus scientifique des données et comment les installer. Il décrit les étapes de traitement de données à l’aide de SQL-U et conclut en expliquant comment utiliser les Python et ruche avec Studio de formation de Machine Azure pour générer et déployer les modèles de prévision. 


### <a name="u-sql-and-visual-studio"></a>U-SQL et Visual Studio

Cette procédure pas à pas vous recommande l’utilisation de Visual Studio pour modifier des scripts SQL-U pour traiter le groupe de données. Les scripts SQL-U sont décrites ici et dans un fichier distinct. Le processus inclut l’ingestion et Explorer les données d’échantillonnage. Il montre également comment exécuter une tâche de script en U-SQL à partir du portail Azure. Tables de la ruche sont créés pour les données dans un cluster HDInsight associé pour faciliter la création et le déploiement d’un modèle de classification binaire dans un Studio de formation de Machine Azure.  


### <a name="python"></a>Python

Cette procédure pas à pas contient également une section qui montre comment générer et déployer un modèle de prévision à l’aide de Python avec Studio de formation de Machine Azure.  Nous fournissons un portable Jupyter avec les scripts Python pour ces étapes de ce processus. L’ordinateur portable inclut le code pour certains fonctionnalité supplémentaire engineering étapes et modèles de construction multiclass classification des régression de modélisation en plus du modèle de classification binaire présenté ici. La tâche de régression doit prévoir la quantité de l’info-bulle basé sur d’autres fonctionnalités de pointe. 


### <a name="azure-machine-learning"></a>Apprentissage automatique Azure
Studio d’apprentissage Machine Azure est utilisée pour générer et déployer les modèles de prévision. Pour cela, à l’aide de deux approches : tout d’abord avec les scripts Python, puis avec des tables de ruche sur un cluster HDInsight (Hadoop).


### <a name="scripts"></a>Scripts

Uniquement les étapes principales sont décrites dans cette procédure pas à pas. Vous pouvez télécharger les **Jupyter bloc-notes** complet **U-SQL script** à partir de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).


## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ces rubriques, vous devez disposer des éléments suivants :

- Un abonnement Azure. Si vous n’en avez pas encore, reportez-vous à la section [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- [Recommandé] Visual Studio 2013 ou 2015. Si vous ne disposez pas une de ces versions installées, vous pouvez télécharger un version Community edition gratuite à partir [d’ici](https://www.visualstudio.com/visual-studio-homepage-vs.aspx). Cliquez sur le bouton **télécharger un 2015 communautaire** sous la section Visual Studio. 

>[AZURE.NOTE] Au lieu de Visual Studio, vous pouvez également utiliser le portail Azure à soumettre des requêtes de LAC de données Azure. Nous fournit des instructions sur la procédure à suivre à la fois avec Visual Studio et sur le portail dans la section des **données de processus avec SQL-U**. 

- Inscription pour l’aperçu du lac données Azure

>[AZURE.NOTE] Vous devez obtenir l’approbation d’utiliser Azure données lac banque (ADLS) et Azure données lac Analytique (ADLA) car ces services sont dans l’aperçu. Vous devrez vous inscrire lorsque vous créez votre premier ADLS ou le ADLA. Pour sigh des, cliquez sur **s’inscrire pour obtenir un aperçu**, lisez le contrat et cliquez sur **OK**. Voici, par exemple, la page d’inscription ADLS :

 ![2](./media/machine-learning-data-science-process-data-lake-walkthrough/2-ADLA-preview-signup.PNG)


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Préparer l’environnement de la science des données du lac de données Azure
Pour préparer l’environnement informatique de données pour cette procédure pas à pas, créez les ressources suivantes :

- Magasin de LAC données Azure (ADLS) 
- Données Azure lac Analytique (ADLA)
- Compte de stockage Blob Azure
- Compte de Machine Learning Studio Azure
- Outils de LAC données Azure pour Visual Studio (recommandé)

Cette section fournit des instructions sur la manière de créer chacune de ces ressources. Si vous choisissez d’utiliser les tables de la ruche avec apprentissage automatique Azure, et non les Python, créer un modèle, vous devez également configurer un cluster HDInsight (Hadoop). Dans cette procédure est décrite dans la section appropriée ci-dessous.
<br/>
>AZURE. Remarque : le **Magasin de LAC de données Azure** peuvent être créés séparément, ou lorsque vous créez l' **Analytique de LAC de données Azure** comme l’espace de stockage par défaut. Des instructions sont référencées pour la création de chacune de ces ressources séparément ci-après, mais le compte de stockage de données lac n’a pas besoin être créé séparément.
<br/>
### <a name="create-an-azure-data-lake-store"></a>Créer un magasin de LAC de données Azure

Créer un ADLS à partir du [portail Azure](http://portal.azure.com). Pour plus d’informations, reportez-vous [à l’aide de créer un cluster de HDInsight avec le magasin de données lac Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à définir l’identité du DAS de Cluster dans la lame de **source de données** de la lame de **Configuration facultatives** y. 

 ![3](./media/machine-learning-data-science-process-data-lake-walkthrough/3-create-ADLS.PNG)


### <a name="create-an-azure-data-lake-analytics-account"></a>Créer un compte Analytique de LAC de données Azure
Créez un compte ADLA à partir du [Portail Azure](http://portal.azure.com). Pour plus d’informations, consultez [didacticiel : mise en route d’Analytique de LAC Azure données avec Azure Portal](../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/machine-learning-data-science-process-data-lake-walkthrough/4-create-ADLA-new.PNG)


### <a name="create-an-azure-blob-storage-account"></a>Créer un compte de stockage Azure Blob
Créer un compte de stockage Azure Blob à partir du [Portail Azure](http://portal.azure.com). Pour plus d’informations, voir la création d’une section du compte de stockage dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).
    
 ![5](./media/machine-learning-data-science-process-data-lake-walkthrough/5-Create-Azure-Blob.PNG)


### <a name="set-up-an-azure-machine-learning-studio-account"></a>Configurer un compte Azure Machine Learning Studio
Signer dans Azure Machine formation Studio/à partir de la page de [Formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) . Cliquez sur le bouton **Démarrer maintenant** , puis choisissez un « Espace de travail libre » ou un « Espace de travail Standard ». Après cela, vous serez en mesure de créer des expériences dans Azure ML Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Installer les outils de LAC de données Azure [recommandé]
Installer les outils de LAC de données Azure pour votre version de Visual Studio à partir des [Outils de LAC de données Azure pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Une fois l’installation terminée avec succès, ouvrez Visual Studio. Vous devez voir le lac données onglet le menu en haut. Lorsque vous vous connectez à votre compte Azure, vos ressources Azure doivent apparaître dans le panneau de gauche.

 ![7](./media/machine-learning-data-science-process-data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)


## <a name="the-nyc-taxi-trips-dataset"></a>Le groupe de données NYC Taxi voyages
Le jeu de données que nous avons utilisé ici est un groupe de données accessible au public-- [NYC Taxi voyages dataset](http://www.andresmh.com/nyctaxitrips/). Les données de voyage de Taxi NYC se composent d’environ 20 Go de fichiers CSV compressés (Go ~ 48 non compressé), enregistrement des voyages individuels de plus de 173 millions et les tarifs payé pour chaque voyage. Chaque enregistrement de voyage inclut les emplacements de prélèvement et de remise et fois, numéro de licence hack rendues anonymes (pilote) et le nombre de medallion (numéro d’identification du taxi unique). Les données couvre tous les déplacements de l’année 2013 et sont fournies dans les deux jeux de données suivantes pour chaque mois :

 - CSV 'trip_data' contient les détails du voyage, comme nombre de passagers, capteur et les points de cette chute, la durée du voyage et longueur du voyage. Voici quelques exemples d’enregistrements :

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - Le 'trip_fare' CSV contient des détails sur le prix payé pour chaque voyage, telles que le type de paiement, tarif, surcharge et taxes, conseils et péages et le montant total payé. Voici quelques exemples d’enregistrements :

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Clé unique pour joindre le voyage\_données et voyage\_tarif est composé des trois champs suivants : medallion, hack\_licence et capteur\_datetime. Les fichiers CSV raw est accessible à partir d’un objet blob de stockage Azure public. Le script de U-SQL pour cette jointure est dans la section [tables de voyage et des frais de jointure](#join) .

## <a name="process-data-with-u-sql"></a>Traiter les données avec SQL-U

Les tâches de traitement de données illustrés dans cette section incluent l’ingestion, vérification de la qualité, exploration et les données d’échantillonnage. Nous montrent également comment joindre des tables de voyage et des frais. La dernière section montre une tâche de script SQL-U à partir du portail Azure exécution. Voici des liens vers chaque sous-section :

- [Réception de données : lecture de données à partir de l’objet blob public](#ingest)
- [Contrôles de qualité des données](#quality)
- [Exploration de données](#explore)
- [Joindre des tables de voyage et des frais](#join)
- [Échantillonnage de données](#sample)
- [Exécuter des travaux de U-SQL](#run)

Les scripts SQL-U sont décrites ici et dans un fichier distinct. Vous pouvez télécharger **U-SQL scripts** complet à partir de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Pour exécuter SQL-U, ouvrez Visual Studio, cliquez sur **fichier--> Nouveau--> projet**, choisissez **SQL-U projet**, nommez et enregistrez dans un dossier.

![8](./media/machine-learning-data-science-process-data-lake-walkthrough/8-create-USQL-project.PNG)

>[AZURE.NOTE] Il est possible d’utiliser le portail Azure pour exécuter U-SQL au lieu de Visual Studio. Vous pouvez accéder à la ressource Azure données lac Analytique sur le portail et soumettre des requêtes directement, comme illustré dans la figure suivante.

![9](./media/machine-learning-data-science-process-data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Réception de données : Lecture de données à partir de l’objet blob public

L’emplacement des données dans le blob Azure est référencé en tant que **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** et peuvent être extraites à l’aide de **Extractors.Csv()**. Remplacez par votre propre nom de conteneur et du nom du compte de stockage dans les scripts suivants pour container_name@blob_storage_account_name dans l’adresse wasb. Dans la mesure où les noms de fichier sont dans le même format, nous pouvons utiliser **voyage\_data_ {\*\}.csv** pour lire tous les fichiers de voyage 12. 

    ///Read in Trip data
    @trip0 =
        EXTRACT 
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Dans la mesure où il existe des en-têtes dans la première ligne, nous devons supprimer les en-têtes et de modifier les types de colonne dans celles qui sont appropriées. Vous pouvez soit enregistrer les données traitées à l’aide de **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ le stockage Azure données lac ou pour le stockage des objets Blob Azure compte en utilisant **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

    // change data types
    @trip =
        SELECT 
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

De la même façon, nous pouvons lire dans les jeux de données de prix. Cliquez avec le bouton droit sur banque de LAC de données Azure, vous pouvez choisir de consulter vos données dans **Azure Portal--> Explorateur de données** ou de l' **Explorateur de fichiers** dans Visual Studio. 

 ![10](./media/machine-learning-data-science-process-data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/machine-learning-data-science-process-data-lake-walkthrough/11-data-in-ADL.PNG)


### <a name="quality"></a>Contrôles de qualité des données

Une fois les tables de voyage et les prix ont été lus dans, contrôles de qualité des données est possible de la manière suivante. Les fichiers CSV peuvent être de sortie pour le stockage des objets Blob Azure ou magasin de LAC de données Azure. 

Rechercher le nombre de medallions et d’un numéro unique de medallions :

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;
    
    @ex_1 =
        SELECT
        pickup_month, 
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv(); 

Rechercher les medallions ayant plus de 100 trips :

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv(); 

Rechercher les enregistrements non valides en termes de pickup_longitude :

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

Rechercher des valeurs manquantes pour certaines variables :

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;
    
    @trip_summary6 =
        SELECT 
            vendor_id,
        SUM(missing_medallion) AS medallion_empty, 
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique  
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>Exploration de données

Nous pouvons faire une exploration de données pour obtenir une meilleure compréhension des données.

Trouvez la distribution de voyages Bonhomme de neige et non Bonhomme de neige :

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;
    
    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv(); 

Trouver la distribution du montant d’info-bulle avec les valeurs limites : 0,5,10 et 20 dollars.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv(); 

Rechercher des statistiques de base de la distance de déplacement :

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT 
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance 
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Rechercher les centiles de distance de voyage :

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv(); 


### <a name="join"></a>Joindre des tables de voyage et des frais

Tables de voyage et de prix peuvent être joints par medallion, hack_license et pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 

    ////output data to ADL
    OUTPUT @model_data_full   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 


Pour chaque niveau du nombre de passagers, calculer le nombre d’enregistrements, montant de pointe moyenne, la variation du montant de l’info-bulle, pourcentage de voyages Bonhomme de neige.

    // contigency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>Échantillonnage de données

Tout d’abord, nous allons sélectionner au hasard 0,1 % des données à partir de la table jointe :

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;
    
    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;
    
    OUTPUT @model_data_random_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv(); 

Puis nous faire d’échantillonnage stratifié binaire tip_class de variable :

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;
    
    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="run"></a>Exécuter des travaux de U-SQL

Lorsque vous avez terminé la modification de scripts de U-SQL, vous pouvez les envoyer au serveur à l’aide de votre compte Azure données lac Analytique. Cliquez sur **Données lac**, **Soumettre des travaux**, sélectionnez votre **Compte d’Analytique**, choisissez le **parallélisme**et cliquez sur le bouton **Envoyer** .  

 ![12](./media/machine-learning-data-science-process-data-lake-walkthrough/12-submit-USQL.PNG)

Lorsque la tâche est appliquée avec succès, le statut de votre travail s’affichera dans Visual Studio pour la surveillance. Une fois la tâche terminée, vous pouvez même relire le processus d’exécution des tâches et découvrez les étapes de goulot d’étranglement pour améliorer l’efficacité de votre travail. Vous pouvez également accéder au portail Azure pour vérifier l’état des travaux SQL-U.

 ![13](./media/machine-learning-data-science-process-data-lake-walkthrough/13-USQL-running-v2.PNG)


 ![14](./media/machine-learning-data-science-process-data-lake-walkthrough/14-USQL-jobs-portal.PNG)


Maintenant, vous pouvez vérifier les fichiers de sortie dans Azure Portal ou de stockage des objets Blob Azure. Nous allons utiliser les données d’exemple stratifié pour notre modélisation à l’étape suivante.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/machine-learning-data-science-process-data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)


## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Générer et déployer des modèles dans l’apprentissage automatique de Azure

Nous montrent les deux options disponibles vous permettant d’extraire des données dans la formation de Machine Azure pour créer et 

- Dans la première option, vous utilisez les exemples de données qui a été écrite pour un Blob Azure (dans l’étape de **prélèvement d’échantillons de données** ci-dessus) et utilisez les Python pour générer et déployer des modèles de formation de Machine Azure. 
- Dans la deuxième option, vous interroger les données de LAC de données Azure directement à l’aide d’une requête de la ruche. Cette option nécessite que vous créez un nouveau cluster HDInsight ou utilisez un cluster d’HDInsight existant dans lequel les tables de la ruche pointent vers les données de NY Taxi dans le stockage Azure données lac.  Nous aborderons ces deux options ci-dessous. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Option 1 : Utilisez les Python pour générer et déployer l’ordinateur modèles d’apprentissage

Pour générer et déployer des modèles de formation de machine à l’aide de Python, créez un bloc-notes de Jupyter sur votre ordinateur local ou dans le Studio d’apprentissage Machine Azure. Le bloc-notes de Jupyter sur [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) contient le code complet permettant de découvrir, de visualiser des données, d’ingénierie de fonctionnalité, de modélisation et de déploiement. Dans cet article, nous montrons que la modélisation et le déploiement. 

### <a name="import-python-libraries"></a>Bibliothèques d’importation Python

Pour exécuter l’exemple Jupyter bloc-notes ou les Python fichier de script, les Python suivant les packages sont nécessaires. Si vous utilisez le service AzureML portable, ces packages ont été pré-installés.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Lire les données à partir de l’objet blob

- Chaîne de connexion   

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    
- Lire dans en tant que texte

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![17](./media/machine-learning-data-science-process-data-lake-walkthrough/17-python_readin_csv.PNG)    
 
- Ajouter des noms de colonnes et de séparer les colonnes

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
    


- Modifier certaines colonnes en numeric

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Construire des modèles d’apprentissage machine

Ici, nous créons un modèle de classification binaire afin de prévoir si un voyage est Bonhomme de neige ou non. Dans le bloc-notes de Jupyter vous trouverez deux autres modèles : classification multiclass et les modèles de régression.

- Tout d’abord nous devons créer des variables factices qui peuvent être utilisés dans le scikit-en savoir plus de modèles

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- Créer la trame de données pour la modélisation

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
        
        X = data.iloc[:,1:]
        Y = data.tipped

- Formation et de test de fractionnement de 60-40

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- Régression logistique dans l’ensemble de la formation

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/machine-learning-data-science-process-data-lake-walkthrough/c1-py-logit-coefficient.PNG)

- Score de jeu de données de test

        Y_test_pred = logit_fit.predict(X_test)

- Calculer les mesures d’évaluation

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train
        
        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
        print fpr_test, tpr_test, thresholds_test
        
        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)
        
        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/machine-learning-data-science-process-data-lake-walkthrough/c2-py-logit-evaluation.PNG)


 
### <a name="build-web-service-api-and-consume-it-in-python"></a>Générer des API de Service Web et de le consommer dans les Python

Nous voulons mettre la machine d’apprentissage du modèle après que qu’il a été généré. Ici, nous utilisons le modèle binaire de logistique par exemple. Assurez-vous que le scikit-en savoir plus de la version dans votre ordinateur local est 0.15.1. Vous n’êtes pas obligé de vous inquiéter à ce sujet, si vous utilisez le service de studio Azure ML.

- Trouvez vos informations d’identification de l’espace de travail à partir des paramètres de studio Azure ML. Dans un Studio de formation Azure Machine, cliquez sur **paramètres** --> **nom de** --> **D’autorisation jetons**. 

    ![C3](./media/machine-learning-data-science-process-data-lake-walkthrough/c3-workspace-id.PNG)


        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- Créer le Service Web

        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)

- Obtenir des informations d’identification du service web

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
        
        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass

- Appeler les API de service Web. Vous devez attendre 5 à 10 secondes après l’étape précédente.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/machine-learning-data-science-process-data-lake-walkthrough/c4-call-API.PNG)


## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Option 2 : Créer et déployer des modèles directement dans la formation de Machine Azure

Studio d’apprentissage Machine Azure peut lire les données directement à partir de la banque de LAC de données Azure et ensuite être utilisé pour créer et déployer des modèles. Cette approche utilise une table de la ruche qui pointe vers le magasin de LAC de données Azure. Ceci nécessite qu’un cluster séparé de Azure HDInsight être mis en service, sur lequel est créé le tableau de la ruche. Les sections suivantes montrent comment effectuer cette opération. 

### <a name="create-an-hdinsight-linux-cluster"></a>Créer un Cluster de Linux HDInsight

Créer un Cluster de HDInsight (Linux) à partir du [portail Azure](http://portal.azure.com). Pour plus d’informations, voir la section **créer un cluster de HDInsight disposant d’un accès au magasin de LAC Azure données** dans [à l’aide de créer un cluster de HDInsight avec le magasin de données lac Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/machine-learning-data-science-process-data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Créer la table de la ruche dans HDInsight

Créons à présent ruche les tables à utiliser dans un Studio de formation Azure ordinateur dans le cluster de HDInsight à l’aide des données stockées dans le magasin de LAC de données Azure à l’étape précédente. Atteindre le cluster HDInsight juste créé. Cliquez sur **paramètres** --> **Propriétés** --> **Cluster DAS identité** --> **ADLS accès**, assurez-vous que votre compte Azure données lac est ajouté dans la liste en lecture, écriture et les droits d’exécution. 

 ![19](./media/machine-learning-data-science-process-data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)


Puis cliquez sur **tableau de bord** en regard du bouton **paramètres** et une fenêtre s’affiche. Cliquez sur **Affichage de la ruche** dans la partie supérieure droite de la page et vous verrez l' **Éditeur de requête**.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/machine-learning-data-science-process-data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)


Coller dans les scripts de ruche suivants pour créer une table. L’emplacement de la source de données est référence le lac Azure Data Store de cette façon : **adl://data_lake_store_name.azuredatalakestore.net:443/nom_dossier nom_fichier**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Lorsque la requête est terminée, vous verrez les résultats comme suit :

 ![22](./media/machine-learning-data-science-process-data-lake-walkthrough/22-Hive-Query-results.PNG)



### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Générer et déployer des modèles dans un Studio de formation de Machine Azure

Nous sommes maintenant prêts à générer et déployer un modèle qui le prévoit ou non une info-bulle est payée avec formation de Machine Azure. Les données d’exemple stratifié sont prêtes à être utilisé dans ce classement binaire (Conseil ou non) problème. Les modèles de prévision à l’aide de classification multiclass (tip_class) et la droite de régression (tip_amount) peuvent également être générés et déployées Studio de formation de Machine Azure, mais ici nous seulement montrer comment gérer le cas de l’utilisation du modèle de classification binaire.

1. Obtenir les données dans Azure ML à l’aide du module **d’Importation de données** , disponible dans la section **données d’entrée et de sortie** . Pour plus d’informations, consultez la page de référence de [module d’importation de données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) .
2. Sélectionnez **La ruche une requête** comme **source de données** dans le panneau **Propriétés** .
3. Collez le script suivant de la ruche dans l’éditeur de **requête de base de données de la ruche**

        select * from nyc_stratified_sample;

4. Permet d’entrer le cluster URI de HDInsight (vous pouvez trouver dans Azure Portal), le Hadoop d’informations d’identification, l’emplacement des données de sortie et le nom de conteneur/clé/nom de compte de stockage Azure.

 ![23](./media/machine-learning-data-science-process-data-lake-walkthrough/23-reader-module-v3.PNG)  

Un exemple de lecture de données à partir de la table de la ruche d’une expérience de classification binaire est illustré dans la figure ci-dessous.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthrough/24-AML-exp.PNG)

Une fois l’expérience est créée, cliquez sur **La valeur d’un Service Web** --> **Prédictive Web Service**

 ![25](./media/machine-learning-data-science-process-data-lake-walkthrough/25-AML-exp-deploy.PNG)

Exécuter automatiquement créé score d’expérimentation, lorsqu’il a terminé, cliquez sur **Déployer le Service Web**

 ![26](./media/machine-learning-data-science-process-data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Le tableau de bord de service web s’affiche peu de temps :

 ![27](./media/machine-learning-data-science-process-data-lake-walkthrough/27-AML-web-api.PNG)


## <a name="summary"></a>Résumé

À la fin de cette procédure pas à pas, vous avez créé un environnement informatique de données pour la création de solutions évolutives de bout en bout dans Azure données lac. Cet environnement a été utilisé pour analyser un dataset grand public, en guidant dans les étapes canoniques du processus données Science, de l’acquisition de données par le biais de formation du modèle, puis pour le déploiement du modèle sous la forme d’un service web. U-SQL a été utilisé pour traiter, explorez et les exemples de données. Python et ruche ont été utilisés avec Studio de formation de Machine Azure pour créer et déployer des modèles de prévision.

## <a name="whats-next"></a>Quel est l’avenir ?

Le chemin d’accès de la formation de l' [Équipe de données Science processus (TDSP)](http://aka.ms/datascienceprocess) fournit des liens vers les rubriques qui décrivent chaque étape du processus analytique avancée. Il existe une série de procédures pas à pas détaillé dans la page de [l’équipe de données Science processus procédures pas à pas](data-science-process-walkthroughs.md) qui mettent en évidence l’utilisation des ressources et des services dans divers scénarios analytique prédictive :

- [Le processus de Science de données équipe en action : à l’aide de SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md)
- [Le processus de Science de données équipe en action : l’utilisation de clusters de HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md)
- [Le processus d’équipe données Science : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [Vue d’ensemble du processus de Science de données à l’aide d’allumage sur Azure HDInsight](machine-learning-data-science-spark-overview.md)

