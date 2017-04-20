<properties
    pageTitle="Le processus de Science de données équipe en action : à l’aide de SQL Server | Microsoft Azure"
    description="Les processus Analytique avancée et technologie en Action"  
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
    ms.author="fashah;bradsev"/>


# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Le processus de Science de données équipe en action : à l’aide de SQL Server

Dans ce didacticiel, vous procédure pas à pas Création et déploiement d’un modèle d’apprentissage automatique à l’aide de SQL Server et un groupe de données accessible au public--le dataset [NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/) . La procédure suit un flux de travail de science de données standard : acquisition et Explorer les données, les fonctionnalités de rétroconception pour faciliter l’apprentissage, puis générer et déployer un modèle.


## <a name="dataset"></a>Description du groupe de données de séjours NYC Taxi

Les données de voyage de Taxi NYC sont environ 20 Go de fichiers CSV compressés (Go ~ 48 non compressé), comprenant les tarifs et les voyages individuels de plus de 173 millions payé pour chaque séjour. Chaque enregistrement voyage comprend un emplacement de prélèvement et de remise et de temps, hack rendues anonymes (pilote) numéro de licence et nombre de medallion (numéro d’identification du taxi unique). Les données couvre tous les déplacements de l’année 2013 et sont fournies dans les deux jeux de données suivantes pour chaque mois :

1. CSV 'trip_data' contient les détails du voyage, comme nombre de passagers, capteur et les points de cette chute, la durée du voyage et longueur du voyage. Voici quelques exemples d’enregistrements :

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Le 'trip_fare' CSV contient des détails sur le prix payé pour chaque voyage, telles que le type de paiement, tarif, surcharge et taxes, conseils et péages et le montant total payé. Voici quelques exemples d’enregistrements :

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Clé unique pour joindre le voyage\_données et voyage\_tarif est composée des champs : medallion, hack\_certificat et capteur\_datetime.

## <a name="mltasks"></a>Exemples de tâches de prévision

Nous arrête des trois problèmes de prévision basés sur le *Conseil\_montant*, à savoir :

1. Classement binaire : prévoir ou non une info-bulle a été payée pour un voyage, c'est-à-dire un *Conseil\_montant* qui est supérieur à 0 f est un exemple positif, tout en un *Conseil\_montant* $ 0 est un exemple négatif.

2. Multiclass classification : pour prédire la plage de pourboire pour le voyage. On divise la *Conseil\_montant* dans cinq emplacements ou les classes :

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. Tâche de régression : prévoir le montant du pourboire pour un voyage.  


## <a name="setup"></a>Environnement de science données paramètre d’Azure l’analytique avancée

Comme vous pouvez le voir dans le guide de [Planification de votre environnement](machine-learning-data-science-plan-your-environment.md) , il existe plusieurs options pour travailler avec le groupe de données NYC Taxi voyages dans Azure :

- Travailler avec les données d’objets BLOB Azure puis modèle dans l’apprentissage automatique de Azure
- Charger les données dans un de la base de données SQL Server puis un modèle dans l’apprentissage automatique de Azure

Dans ce didacticiel, nous vous montrerons importation en bloc parallèle des données à un SQL Server, exploration de données, la fonctionnalité d’ingénierie et vers le bas d’échantillonnage à l’aide de SQL Server Management Studio ainsi qu’à l’aide du bloc-notes de IPython. [Exemples de scripts](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) et les [ordinateurs portables de IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) sont partagés dans GitHub. Un bloc-notes de IPython exemple pour travailler avec les données d’objets BLOB Azure est également disponible dans le même emplacement.

Pour configurer votre environnement de Science de données Azure :

1. [Créer un compte de stockage](../storage/storage-create-storage-account.md)

2. [Créer un espace de travail formation de Machine Azure](machine-learning-create-workspace.md)

3. [Fourniture d’une Machine virtuelle de Science données](machine-learning-data-science-setup-sql-server-virtual-machine.md), qui sert un SQL Server et un serveur IPython bloc-notes.

    > [AZURE.NOTE] Les exemples de scripts et les ordinateurs portables de IPython seront téléchargés à votre machine virtuelle de Science de données pendant le processus d’installation. Lorsque le script après l’installation de machine virtuelle est terminée, les échantillons seront dans la bibliothèque de Documents de votre ordinateur virtuel :  
    > - Exemple de Scripts :`C:\Users\<user_name>\Documents\Data Science Scripts`  
    > - Exemples d’agendas IPython :`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
    > où `<user_name>` est le nom de connexion de votre machine virtuelle Windows. Nous ferons référence dans les dossiers exemple comme **Exemples de Scripts** et des **Exemples d’agendas IPython**.


En fonction de la taille du groupe de données, emplacement de source de données et l’environnement cible Azure sélectionné, ce scénario est semblable au [scénario \#5 : ensemble de données volumineux dans des fichiers locaux, cible de SQL Server dans Azure VM](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obtenir les données de la Source publique

Pour obtenir le groupe de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) à partir de son emplacement public, vous pouvez utiliser une des méthodes décrites dans le [Déplacement des données vers et depuis le stockage Blob Azure](machine-learning-data-science-move-azure-blob.md) pour copier les données dans votre nouvelle machine virtuelle.

Pour copier les données à l’aide de AzCopy :

1. Se connecter à votre machine virtuelle (VM)

2. Créer un nouveau répertoire sur disque des données de la machine virtuelle (Remarque : n’utilisez pas le disque temporaire, qui est fourni avec la machine virtuelle sous la forme d’un disque de données).

3. Dans une fenêtre d’invite de commandes, exécutez la ligne de commande Azcopy suivante, en remplaçant < path_to_data_folder > avec votre dossier de données créée dans (2) :

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

    Une fois la AzCopy terminée, un total de 24 zippés fichiers CSV (12 pour un voyage\_données et 12 pour le voyage\_tarif) doit se trouver dans le dossier de données.

4. Décompressez les fichiers téléchargés. Notez le dossier où se trouvent les fichiers non compressés. Ce dossier sera appelé le < chemin d’accès\_à\_données\_fichiers\>.

## <a name="dbload"></a>Importer en bloc des données dans la base de données de SQL Server

Les performances de chargement et de transfert de grandes quantités de données à une base de données SQL et les requêtes suivantes peuvent être améliorées à l’aide de _vues et Tables partitionnées_. Dans cette section, nous suivent les instructions de la rubrique [Parallèle en bloc importation à l’aide de SQL Partition de Tables de données](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) pour créer une nouvelle base de données et charger les données dans les tables partitionnées en parallèle.

1. Lorsque connecté à votre machine virtuelle, démarrez **SQL Server Management Studio**.

2. Se connecter à l’aide de l’authentification Windows.

    ![Connexion de SSMS][12]

3. Si vous n’avez pas encore modifié le mode d’authentification SQL Server et créé un nouvel utilisateur d’ouverture de session SQL, ouvrez le fichier de script nommé **Modifier\_auth.sql** dans le dossier **d’Exemples de Scripts** . Modifier le nom d’utilisateur par défaut et le mot de passe. Cliquez sur **! Exécutez** dans la barre d’outils pour exécuter le script.

    ![Exécuter le Script][13]

4. Vérifier et/ou modifier les SQL Server par défaut de base de données et le journal des dossiers pour assurer nouvellement créé les bases de données sera stockée dans un disque de données. L’image de machine virtuelle de SQL Server qui est optimisé pour les charges de datawarehousing est préconfiguré avec des disques de données et de journaux. Si votre ordinateur virtuel n’a pas inclus un disque de données et que vous avez ajouté de nouveaux disques durs virtuels pendant le processus de configuration de machine virtuelle, modifier les dossiers par défaut comme suit :

    - Double-cliquez sur le nom de SQL Server dans le panneau de gauche, puis cliquez sur **Propriétés**.

        ![Propriétés de SQL Server][14]

    - Sélectionnez les **Paramètres de base de données** dans la liste **Sélectionner une page** vers la gauche.

    - Vérifier et/ou modifier les **emplacements de base de données par défaut** pour les emplacements de **Disque de données** de votre choix. Il s’agit de nouvelles bases de données hébergeant si créé avec les paramètres d’emplacement par défaut.

        ![Valeurs par défaut de la base de données SQL][15]  

5. Pour créer une nouvelle base de données et un ensemble de groupes de fichiers pour stocker les tables partitionnées, ouvrez l’exemple de script **créer\_db\_default.sql**. Le script crée une nouvelle base de données nommée **TaxiNYC** et 12 groupes de fichiers à l’emplacement de données par défaut. Chaque groupe contiendra un mois de voyage\_données et voyage\_données du prix. Modifiez le nom de la base de données, si vous le souhaitez. Cliquez sur **! Exécutez** pour exécuter le script.

6. Ensuite, créez deux tables de partition, une pour le voyage\_et une autre pour le voyage\_tarif. Ouvrez l’exemple de script **créer\_partitionnée\_table.sql**, qui est :

    - Créer une fonction de partition pour fractionner les données par mois.
    - Créer un schéma de partition pour mapper les données de chaque mois à un autre groupe de fichiers.
    - Créez deux tables partitionnées mis en correspondance avec le schéma de partition : **nyctaxi\_voyage** contiendra le voyage\_données et **nyctaxi\_tarif** contiendra le voyage\_données du prix.

    Cliquez sur **! Exécutez** pour exécuter le script et créez les tables partitionnées.

7. Dans le dossier **Exemples de Scripts** , il existe deux exemples de scripts PowerShell fournis pour illustrer les importations en bloc parallèle de données à des tables SQL Server.

    - **bcp\_parallèles\_generic.ps1** est un script générique pour importer des données en bloc parallèle dans une table. Modifier ce script pour définir les variables d’entrée et de la cible, comme indiqué dans les lignes de commentaire dans le script.
    - **bcp\_parallèles\_nyctaxi.ps1** est une version pré-configurée de script générique et peut servir à charger les deux tables pour les données NYC Taxi voyages.  

8. Cliquez sur le **bcp\_parallèles\_nyctaxi.ps1** nom de script et cliquez sur **Modifier** pour l’ouvrir dans PowerShell. Passez en revue les variables prédéfinies et les modifier en fonction de votre nom de la base de données sélectionnée, dossier de données d’entrée, le dossier du journal cible et les chemins d’accès pour les fichiers du format exemple **nyctaxi_trip.xml** et **nyctaxi\_fare.xml** (fourni dans le dossier **d’Exemples de Scripts** ).

    ![Importer en bloc des données][16]

    Vous pouvez également sélectionner le mode d’authentification, la valeur par défaut est l’authentification Windows. Cliquez sur la flèche verte dans la barre d’outils à exécuter. Le script lance des opérations d’importation en bloc 24 12 parallèle, pour chaque table partitionnée. Vous pouvez surveiller la progression d’importation de données en ouvrant le dossier de données par défaut de SQL Server tel que défini ci-dessus.

9. Le script PowerShell signale de début et de fin. Lorsque tous les en gros importation complète, l’heure de fin est signalée. Vérifiez le dossier de journal cible pour vérifier que les importations en bloc ont réussi, par exemple, aucune erreur n’est signalée dans le dossier du journal cible.

10. Votre base de données est prêt pour l’exploration, ingénierie de fonctionnalité et d’autres opérations comme vous le souhaitez. Dans la mesure où les tables sont partitionnées en fonction de la **collecte\_datetime** champ, les requêtes qui incluent **collecte\_datetime** le schéma de partition bénéficieront de conditions dans la clause **WHERE** .

11. Dans **SQL Server Management Studio**, explorez l’exemple fourni de script **exemple\_queries.sql**. Pour exécuter un des exemples de requêtes, sélectionnez les lignes de la requête, puis cliquez sur **! Exécutez** dans la barre d’outils.

12. Les données NYC Taxi Trips sont chargées dans deux tables distinctes. Pour améliorer les opérations de jointure, il est vivement recommandé d’indexer les tables. L’exemple de script **créer\_partitionnée\_index.sql** crée des index partitionnés sur la clé de jointure composite **medallion, hack\_de licence et de remise en mains propres\_datetime**.

## <a name="dbexplore"></a>Exploration de données et de l’ingénierie de fonctionnalité dans SQL Server

Dans cette section, nous effectuera d’exploration de données et la génération de la fonctionnalité en exécutant des requêtes SQL directement dans **SQL Server Management Studio** à l’aide de la base de données SQL Server créé précédemment. Un exemple de script nommé **exemple\_queries.sql** est fourni dans le dossier **d’Exemples de Scripts** . Modifier le script pour modifier le nom de la base de données, si elle est différente de la valeur par défaut : **TaxiNYC**.

Dans cet exercice, nous allons :

- Se connecter à **SQL Server Management Studio** , à l’aide de l’authentification Windows ou l’authentification SQL et le nom de connexion SQL et un mot de passe.
- Explorez les distributions de données de certains champs dans différentes fenêtres.
- Examinez la qualité des données des champs de latitude et de longitude.
- Générer des étiquettes de classification binaire et multiclass en fonction de la **Conseil\_volume**.
- Générer des fonctionnalités et de calcul/comparer les distances de voyage.
- Joignez les deux tables et extraire un échantillon aléatoire qui sera utilisé pour construire des modèles.

Lorsque vous êtes prêt à passer à la formation de Machine d’Azure, vous pouvez :  

1. Enregistrez la requête SQL finale pour extraire et échantillonner les données et copier-coller la requête directement dans une [Importation de données] [ import-data] module d’apprentissage de Machine d’Azure, ou
2. Persistance des données échantillonnées et ouvragées vous prévoyez d’utiliser pour le modèle de création dans une nouvelle table de la base de données et utiliser la nouvelle table dans les [Données d’importation] [ import-data] module dans la formation de Machine Azure.

Dans cette section, nous allons enregistrer la requête finale pour les extraire et les exemples de données. La deuxième méthode est indiquée dans la section [fonction Engineering IPython bloc-notes et l’Exploration de données](#ipnb) .

Pour une vérification rapide du nombre de lignes et de colonnes dans les tables remplis précédemment à l’aide d’une importation en bloc parallèle,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploration : Distribution de voyage par medallion

Cet exemple identifie la medallion (numéros de taxi) avec plus de 100 voyages dans un laps de temps donné. La requête pourra bénéficier de l’accès à la table partitionnée dans la mesure où il est conditionné par le schéma de partition de **capteur\_datetime**. Interroger le groupe de données complet vérifiera également l’utilisation de la table partitionnée et/ou d’analyse d’index.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploration : Distribution de voyage par medallion et hack_license

    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Évaluation de qualité de données : Vérifier les enregistrements de longitude incorrecte et/ou latitude

Cet exemple examine si tous les champs de longitude ou latitude de contient une valeur non valide (les degrés en radians doivent être comprise entre -90 et 90), ou vous avez (0, 0) coordonnées.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploration : Distribution de Tipped et pas Bonhomme de neige voyages

Cet exemple recherche le nombre de déplacements qui ont été Bonhomme de neige et ne pas Bonhomme de neige en un temps donné période (ou dans le groupe de données complet si portant sur l’année complète). Cette distribution reflète la distribution binaire d’étiquette à utiliser ultérieurement pour la modélisation de la classification binaire.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploration : Conseil de Distribution de la plage de la classe

Cet exemple calcule la distribution des plages d’info-bulle dans un laps de temps donné (ou, dans le groupe de données complet si portant sur l’année complète). Il s’agit de la distribution des classes étiquette qui sera utilisé ultérieurement pour la modélisation de classification multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploration : Calculer et comparer la Distance de déplacement

Cet exemple convertit la remise en mains propres et la remise de longitude et latitude géographie de SQL pointe, calcule la distance de déplacement à l’aide de la différence de points de géographie SQL et renvoie un échantillon aléatoire des résultats de la comparaison. L’exemple de limite les résultats aux coordonnées valides uniquement à l’aide de la requête d’évaluation de qualité données évoquée précédemment.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Ingénierie de fonctionnalité dans les requêtes SQL

Les requêtes de prospection de conversion génération et géographie étiquette peuvent également servir pour générer des étiquettes de fonctionnalités en enlevant la partie comptage. Exemples SQL ingénieries de fonctionnalité supplémentaire sont fournies dans la section [fonction Engineering IPython bloc-notes et l’Exploration de données](#ipnb) . Il est plus efficace pour exécuter des requêtes de génération de la fonctionnalité sur le groupe de données complet ou un large sous-ensemble d’à l’aide de requêtes SQL qui s’exécutent directement sur l’instance de base de données SQL Server. Les requêtes peuvent être exécutées dans **SQL Server Management Studio**, IPython bloc-notes ou n’importe quel outil/environnement de développement qui peut accéder à la base de données localement ou à distance.

#### <a name="preparing-data-for-model-building"></a>Préparation des données pour la construction d’un modèle

La requête suivante jointures la **nyctaxi\_voyage** et **nyctaxi\_tarif** tables, génère un classement binaire étiquette **Bonhomme de neige**, une étiquette de classement de multiples **Conseil\_classe**et extrait un échantillon aléatoire de 1 % du groupe de données complet joint. Cette requête peut être copiée puis collée directement dans le [Studio de formation Azure Machine](https://studio.azureml.net) [Importer des données] [ import-data] module de réception de données directe à partir de l’instance de base de données SQL Server dans Azure. La requête exclut les enregistrements avec incorrect (0, 0) coordonnées.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Exploration de données et de l’ingénierie de fonctionnalité dans le carnet d’IPython

Dans cette section, nous exécuterons l’exploration de données et la génération de fonctionnalité à l’aide de requêtes des Python et SQL sur la base de données SQL Server créé précédemment. Un bloc-notes de IPython exemple nommé **machine-Learning-data-science-process-sql-story.ipynb** est fourni dans le dossier **d’Exemples d’agendas IPython** . Ce bloc-notes est également disponible sur [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

La séquence recommandée lorsque vous travaillez avec des données volumineuses est la suivante :

- Lire dans un petit échantillon des données dans un bloc de données en mémoire.
- Effectuer certaines visualisations et les explorations à l’aide des données échantillonnées.
- Expérimenter ingénierie de fonctionnalité à l’aide des données échantillonnées.
- Pour l’exploration de données plus grande, la manipulation des données et ingénierie de fonctionnalité, utiliser les Python pour émettre des requêtes SQL directement sur la base de données SQL Server dans la machine virtuelle d’Azure.
- Décidez de la taille de l’échantillon à utiliser pour la création de modèles de formation de Machine Azure.

Lorsque vous êtes prêt à passer à la formation de Machine d’Azure, vous pouvez :  

1. Enregistrez la requête SQL finale pour extraire et échantillonner les données et copier-coller la requête directement dans une [Importation de données] [ import-data] module dans la formation de Machine Azure. Cette méthode est présentée dans la section [Création de modèles dans l’apprentissage automatique de Azure](#mlmodel) .    
2. Persistance des données échantillonnées et ouvragées vous prévoyez d’utiliser pour le modèle de création dans une nouvelle table de la base de données, puis utilisez la nouvelle table dans les [Données d’importation] [ import-data] module.

Voici quelques fonctionnalité exemples d’ingénierie, exploration de données et visualisation de données. Pour plus d’exemples, consultez le bloc-notes SQL IPython exemple dans le dossier **d’Exemples d’agendas IPython** .

#### <a name="initialize-database-credentials"></a>Initialiser les informations d’identification de la base de données

Initialiser les paramètres de connexion de base de données dans les variables suivantes :

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Créer la connexion de base de données

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Numéro d’état de lignes et de colonnes dans la table nyctaxi_trip

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Nombre total de lignes = 173179759  
- Nombre total de colonnes = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Lecture dans un échantillon de données de petite taille à partir de la base de données de SQL Server

    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Temps de lecture de que la table d’exemple est 6.492000 secondes  
Nombre de lignes et de colonnes récupérés = (84952, 21)

#### <a name="descriptive-statistics"></a>Statistiques descriptives

Sont maintenant prêts à Explorer les données échantillonnées. Nous allons commencer par envisager des statistiques descriptives à la **voyage\_distance** (ou tout autre) (s) :

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualisation : Exemple de traçage zone

Nous examinons ensuite le traçage de la distance de déplacement visualiser l’étendue de la zone

    df1.boxplot(column='trip_distance',return_type='dict')

![Tracer #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisation : Exemple de tracé de Distribution

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tracer #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisation : Barre et ligne placettes

Dans cet exemple, nous l’emplacement de la distance de déplacement dans cinq emplacements et visualiser les résultats de mise au rebus.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Nous pouvons tracer la distribution emplacement ci-dessus dans une barre ou ligne de traçage comme indiqué ci-dessous

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Tracer #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Tracer #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualisation : Exemple de Scatterplot

Nous affichons le nuage de points entre **voyage\_temps\_dans\_s** et **voyage\_distance** pour voir s’il existe une corrélation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Tracer #6][6]

De la même façon, nous pouvons vérifier la relation entre **taux\_code** et **voyage\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Tracer #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>L’échantillonnage des données dans SQL

Lors de la préparation des données pour modéliser la génération dans [Azure Machine Learning Studio](https://studio.azureml.net), vous pouvez décider de la **requête SQL à utiliser directement dans le module d’importation de données** ou conserver les données d’ingénierie et échantillonnées dans une nouvelle table, vous pouvez utiliser dans les [Données d’importation] [ import-data] module avec une simple *FROM de *Sélectionner* < votre\_nouveau\_table\_nom >**.

Dans cette section, nous allons créer une nouvelle table pour contenir les données échantillonnées et ouvragées. Un exemple d’une requête SQL directe pour la construction du modèle est fourni dans la section [d’Exploration de données et d’ingénierie de fonctionnalité dans SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Créer un exemple de Table et la remplir avec 1 % des Tables jointes. Supprimer la première Table si elle existe.

Dans cette section, nous joindre les tables **nyctaxi\_voyage** et **nyctaxi\_tarif**, extraire un échantillon aléatoire de 1 %, et rendre persistantes des données échantillonnées dans une nouvelle table intitulée **nyctaxi\_un\_%**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploration de données à l’aide de requêtes de SQL dans IPython portable

Dans cette section, nous explorons les distributions de données en utilisant les données d’échantillonnage de 1 % qui sont rendu persistant dans la nouvelle table que nous avons créés ci-dessus. Notez que les explorations similaires peuvent être effectuées en utilisant les tables d’origine, en utilisant éventuellement **TABLESAMPLE** pour limiter l’exploration exemple ou en limitant les résultats pour une période de temps donnée à l’aide du **collecte\_datetime** partitions, comme illustré dans la section [d’Exploration de données et d’ingénierie de fonctionnalité dans SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Exploration : Distribution quotidienne de déplacements

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploration : Distribution de voyage par medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Génération de fonctionnalité à l’aide de requêtes SQL dans IPython portable

Dans cette section, nous allons générer nouvelles étiquettes et fonctionnalités directement à l’aide de requêtes SQL, pour la table d’exemple de 1 % nous créé dans la section précédente.

#### <a name="label-generation-generate-class-labels"></a>Génération d’étiquette : Générer des étiquettes de classe

Dans l’exemple suivant, nous allons générer deux jeux d’étiquettes à utiliser pour la modélisation :

1. Binaire étiquettes de classe **Bonhomme de neige** (prédire si une info-bulle est accordée)
2. Étiquettes de multiclass **Conseil\_classe** (prédiction de l’emplacement de l’info-bulle ou de la plage)

        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''

        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()

        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Ingénierie de fonctionnalité : Les fonctions de nombre de colonnes catégorielles

Cet exemple transforme un champ par catégorie dans un champ numérique en remplaçant chaque catégorie avec le nombre de ses occurrences dans les données.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Ingénierie de fonctionnalité : Emplacement des fonctionnalités pour les colonnes numériques

Cet exemple transforme un champ numérique continu en plages catégorie prédéfinie, par exemple, transformation de champ numérique dans un champ de catégorie.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Ingénierie de fonctionnalité : Extraire des fonctionnalités d’emplacement de Latitude/Longitude décimale

Cet exemple montre comment décompose la représentation décimale d’un champ de latitude ou de longitude en plusieurs champs de la région de granularité différent, comme, pays, ville, ville, bloc, etc.. Notez que les nouveaux champs de géo ne sont pas mappées à des emplacements. Pour plus d’informations sur les emplacements de Géocode de mappage, consultez [Services de reste de Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Vérifiez que la forme finale de la table featurized

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Nous sommes maintenant prêts à procéder à la construction du modèle et le modèle déploiement dans [Azure Machine Learning](https://studio.azureml.net). Les données sont prêtes pour un des problèmes de prévision identifiées précédemment, à savoir :

1. Classement binaire : pour prévoir ou non une info-bulle a été payée pour un voyage.

2. Multiclass classification : pour prédire la plage de pourboire, en fonction des classes définies précédemment.

3. Tâche de régression : prévoir le montant du pourboire pour un voyage.  


## <a name="mlmodel"></a>Création de modèles dans l’apprentissage automatique Azure

Pour commencer l’exercice de modélisation, connectez-vous à votre espace de travail de formation de Machine d’Azure. Si vous n’avez pas encore créé une machine de formation d’espace de travail, voir [créer un espace de travail de Machine d’apprentissage Azure](machine-learning-create-workspace.md).

1. Pour commencer avec formation de Machine d’Azure, consultez [Nouveautés Azure Machine Learning Studio ?](machine-learning-what-is-ml-studio.md)

2. Se connecter à la [Machine Azure Studio de formation](https://studio.azureml.net).

3. La page d’accueil de Studio fournit une mine d’informations, des vidéos, des didacticiels, des liens vers la référence de Modules et d’autres ressources. Pour plus d’informations sur la formation de Machine d’Azure, consultez le [Centre de Documentation de formation de Machine Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Une expérience de formation classique se compose des éléments suivants :

1. Créer une expérience **+ de nouveau** .
2. Obtenir les données de formation de Machine Azure.
3. Prétraitement, de transformer et de manipuler les données en fonction des besoins.
4. Générer les fonctionnalités en fonction des besoins.
5. Fractionner les données en groupes de données formation/validation/test (ou avoir des groupes de données distinct pour chaque).
6. Sélectionnez un ou plusieurs algorithmes d’apprentissage machine en fonction du problème de la formation à résoudre. Par exemple, une classification binaire, classification multiclass, régression.
7. Former un ou plusieurs modèles à l’aide de la formation dataset.
8. Le groupe de données de validation à l’aide des formation ou les modèles de score.
9. Évaluer l’ou les modèles pour calculer les mesures appropriées pour le problème de la formation.
10. Bien régler l’ou les modèles, puis sélectionnez le meilleur modèle à déployer.

Dans cet exercice, nous avons déjà exploré conçu les données dans SQL Server et a décidé, sur la taille de l’échantillon d’acquisition dans l’apprentissage automatique de Azure. Pour créer un ou plusieurs des modèles de prévision, nous avons décidé :

1. Obtenir les données de formation de Machine Azure en utilisant les [Données d’importation] [ import-data] module, disponible dans la section **données d’entrée et de sortie** . Pour plus d’informations, consultez [Importation de données] [ import-data] page de référence du module.

    ![Importer les données de formation de Machine Azure][17]

2. Sélectionnez la **Base de données de SQL Azure** comme **source de données** dans le panneau **Propriétés** .

3. Entrez le nom DNS de base de données dans le champ **nom du serveur de base de données** . Format :`tcp:<your_virtual_machine_DNS_name>,1433`

4. Entrez le **nom de la base de données** dans le champ correspondant.

5. Entrez le **nom d’utilisateur SQL** dans le **aqccount nom d’utilisateur et le mot de passe dans le **mot de passe du compte serveur utilisateur **.

6. Vérifiez l’option **d’accepter un certificat de serveur** .

7. Dans la zone de texte modifiable de **requête de base de données** , collez la requête qui extrait les nécessaires (y compris les champs calculés, tels que les étiquettes) des champs de base de données d’et vers le bas échantillonne les données à la taille de l’échantillon de votre choix.

La figure ci-dessous est un exemple d’une expérience de classification binaire que lire les données directement à partir de la base de données SQL Server. Expériences similaires peuvent être construits pour la classification de multiclass et des problèmes de régression.

![Train d’apprentissage Machine Azure][10]

> [AZURE.IMPORTANT] Dans les données de modélisation d’extraction et d’échantillonnage des exemples de la requête fournies dans les sections précédentes, **toutes les étiquettes pour les modélisation de trois exercices sont inclus dans la requête**. Une étape importante (obligatoire) dans chacun des exercices de modélisation est à **Exclure** les étiquettes inutiles pour les deux autres problèmes et toutes autres **fuites de cible**. Pour, par exemple, lorsque vous utilisez la classification binaire, utilisez l’étiquette **Bonhomme de neige** et exclure les champs **Conseil\_classe**, **pointe\_montant**, et **total\_volume**. Ces derniers sont des fuites de cible dans la mesure où ils impliquent la pointe payé.
>
> Pour exclure les colonnes inutiles et cibler des fuites, vous pouvez utiliser la [Sélection des colonnes dans un groupe de données] [ select-columns] module ou [Modifier les métadonnées][edit-metadata]. Pour plus d’informations, reportez-vous à la section [Sélectionner des colonnes dans un groupe de données] [ select-columns] et [Modifier les métadonnées] [ edit-metadata] pages de référence.

## <a name="mldeploy"></a>Déploiement de modèles dans l’apprentissage automatique Azure

Lorsque votre modèle est prêt, vous pouvez le déployer facilement comme un service web directement à partir de l’expérience. Pour plus d’informations sur le déploiement de services web de formation de Machine d’Azure, voir [déploiement d’un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md).

Pour déployer un service web, vous devez :

1. Créer une expérience d’évaluation.
2. Déployer le service web.

Pour créer une expérience d’évaluation à partir d’une expérience de formation **terminé** , cliquez sur **Créer évaluation tester** dans la barre d’actions inférieure.

![Score Azure][18]

Formation de Machine Azure va tenter de créer une expérience d’évaluation basée sur les composants de l’expérience de formation. En particulier, il sera :

1. Enregistrer le modèle formé et retirez les modules de formation de modèle.
2. Identifier un **port d’entrée** de logique pour représenter le schéma des données d’entrée attendu.
3. Identifier un **port de sortie** de logique pour représenter le schéma de sortie du service web attendu.

Lors de la création de l’expérience d’évaluation, examiner et ajustez si nécessaire. Un réglage par défaut est de remplacer le dataset d’entrée ou la requête par un qui exclut les champs de l’étiquette, celles-ci ne seront pas disponibles lorsque le service est appelé. Il est également conseillé de réduire la taille des dataset d’entrée ou de la requête à quelques enregistrements, juste assez pour indiquer le schéma d’entrée. Il est commun à exclure tous les d’entrée des champs et d’inclure uniquement les **Étiquettes d’un score** et **Un score de probabilités** dans la sortie à l’aide de la [Sélection des colonnes dans un groupe de données] pour le port de sortie,[ select-columns] module.

Un exemple de notation expérience est dans la figure ci-dessous. Lorsque vous êtes prêt à déployer, cliquez sur le bouton **Publier le SERVICE WEB** dans la barre d’actions inférieur.

![Apprentissage automatique Azure publier][11]

Pour résumer, dans ce didacticiel de la procédure pas à pas, vous avez créé un environnement de science de données Azure, a travaillé avec un dataset grand public d’acquisition de données pour modéliser la formation et le déploiement d’un service web de formation de Machine Azure.

### <a name="license-information"></a>Informations de licence

Cette procédure pas à pas l’échantillon et l’accompagne les scripts et les IPython notebook(s) sont partagées par Microsoft sous licence MIT. Veuillez vérifier le fichier LICENSE.txt le répertoire de l’exemple de code sur GitHub pour plus de détails.

### <a name="references"></a>Références

• La [Page de téléchargement de Monroy de Andrés NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/)  
• [Mise en échec des données de voyage du NYC Taxi par Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi briefings Commission recherche et statistiques](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
