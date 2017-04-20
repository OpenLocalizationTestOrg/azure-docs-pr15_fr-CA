<properties
    pageTitle="Le processus de Science de données équipe en action : à l’aide de SQL Data Warehouse | Microsoft Azure"
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
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Le processus de Science de données équipe en action : à l’aide de SQL Data Warehouse

Dans ce didacticiel, nous vous guidons à travers la création et le déploiement d’une machine d’apprentissage du modèle à l’aide de SQL Data Warehouse (SQL DW) pour un dataset publiquement disponible--le dataset [NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/) . Le modèle de classification binaire construit prévoit une info-bulle est payée pour un voyage et modèles de classification multiclass et de régression sont également discutés qui prévoient la distribution de l’info-bulle des montants versés ou non.

La procédure suit le flux de travail [d’Équipe données Science processus (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) . Nous montrent comment configurer un environnement informatique de données, comment faire pour charger les données du Data Warehouse de SQL et comment utiliser Data Warehouse de SQL ou un portable IPython pour Explorer les données et les ingénieur de fonctionnalités au modèle. Puis, nous montrons comment générer et déployer un modèle avec formation de Machine Azure.


## <a name="dataset"></a>Le groupe de données NYC Taxi voyages

Les données de voyage de Taxi NYC se composent d’environ 20 Go de fichiers CSV compressés (Go ~ 48 non compressé), enregistrement des voyages individuels de plus de 173 millions et les tarifs payé pour chaque voyage. Chaque enregistrement de voyage inclut les emplacements de prélèvement et de remise et fois, numéro de licence hack rendues anonymes (pilote) et le nombre de medallion (numéro d’identification du taxi unique). Les données couvre tous les déplacements de l’année 2013 et sont fournies dans les deux jeux de données suivantes pour chaque mois :

1. Le fichier **trip_data.csv** contient les détails du voyage, comme nombre de passagers, capteur et les points de cette chute, la durée du voyage et longueur du voyage. Voici quelques exemples d’enregistrements :

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Le fichier **trip_fare.csv** contient des détails sur le prix payé pour chaque voyage, telles que le type de paiement, tarif, surcharge et taxes, conseils et péages et le montant total payé. Voici quelques exemples d’enregistrements :

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La **clé unique** utilisée pour joindre le voyage\_données et voyage\_tarif est composé des trois champs suivants :

- medallion,
- HACK\_licence et
- remise en mains propres\_datetime.

## <a name="mltasks"></a>Adresse de trois types de tâches de prévision

Nous formuler trois problèmes de prévision basés sur le *Conseil\_montant* pour illustrer trois types de tâches de modélisation :

1. **Classement binaire**: pour prévoir ou non une info-bulle a été payée pour un voyage, c'est-à-dire un *Conseil\_montant* qui est supérieur à 0 f est un exemple positif, tout en un *Conseil\_montant* $ 0 est un exemple négatif.

2. **Multiclass classification**: pour prédire la plage de pourboire pour le voyage. On divise la *Conseil\_montant* dans cinq emplacements ou les classes :

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tâche de régression**: prévoir le montant du pourboire pour un voyage.  


## <a name="setup"></a>Configurer l’environnement de science de données Azure pour analytique avancée

Pour configurer votre environnement de Science de données Azure, procédez comme suit.

**Créer votre propre compte de stockage blob Azure**

- Lorsque vous configurez votre propre stockage blob Azure, choisissez un emplacement géographique-pour le stockage blob Azure dans ou aussi proche que possible **Du Sud des États-Unis centre**, qui est où sont stockées les données NYC Taxi. Les données sont copiées à l’aide de AzCopy à partir du conteneur de stockage blob publique à un conteneur dans votre compte de stockage. Plus votre stockage blob Azure Sud, plus fin de cette tâche (étape 4).
- Pour créer votre propre compte de stockage Azure, suivez les étapes décrites dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Veillez à prendre des notes sur les valeurs d’identification de compte de stockage suivant qu’elles seront nécessaires plus loin dans cette procédure pas à pas.

  - **Nom du compte de stockage**
  - **Clé de compte de stockage**
  - **Nom du conteneur** (à laquelle vous souhaitez que les données sont stockées dans le stockage blob Azure)

**Configurez votre instance du Data Warehouse de SQL Azure.**
Suivez la documentation à [créer un entrepôt de données SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) pour configurer une instance de SQL Data Warehouse. Assurez-vous que vous faites notations sur les informations d’identification SQL Data Warehouse suivantes qui seront utilisées dans les étapes ultérieures.

  - **Nom du serveur**: <server Name>. database.windows.net
  - **Nom SQLDW (base de données)**
  - **Nom d’utilisateur**
  - **Mot de passe**

**Installez Visual Studio 2015 et outils de données de SQL Server.** Pour obtenir des instructions, voir [installer Visual Studio 2015 de et/ou SSDT (outils de données de SQL Server) pour l’entrepôt de données SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Connectez-vous à votre Data Warehouse de SQL Azure avec Visual Studio.** Pour obtenir des instructions, consultez les étapes 1 et 2 de [se connecter au magasin de données SQL Azure avec Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

>[AZURE.NOTE] Exécutez la requête SQL suivante sur la base de données que vous avez créé dans votre magasin de données SQL (au lieu de la requête fournie à l’étape 3 de la rubrique connexion,) pour **créer une clé principale**.

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Créer un espace de travail formation de Machine Azure sous votre abonnement Azure.** Pour obtenir des instructions, voir [Création d’un espace de travail de formation de Machine d’Azure](machine-learning-create-workspace.md).

## <a name="getdata"></a>Charger les données dans l’entrepôt de données SQL

Ouvrez une console de commande de Windows PowerShell. Exécution de PowerShell suivant les fichiers que nous partageons avec vous sur Github dans un répertoire local que vous spécifiez avec le paramètre de script commandes pour télécharger l’exemple SQL *- DestDir*. Vous pouvez modifier la valeur du paramètre *DestDir -* à n’importe quel répertoire local. Si *- DestDir* n’existe pas, il sera créé par le script PowerShell.

>[AZURE.NOTE] Vous devrez peut-être **Exécuter en tant qu’administrateur** lors de l’exécution du script PowerShell suivant si votre répertoire *DestDir* a besoin de privilèges d’administrateur pour créer ou pour y écrire.

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Après l’exécution réussie, votre répertoire de travail actuel devient *- DestDir*. Vous devez être en mesure de voir l’écran comme ci-dessous :

![][19]

Dans votre *-DestDir*, exécutez le script PowerShell suivant en mode administrateur :

    ./SQLDW_Data_Import.ps1

Lorsque le script PowerShell s’exécute pour la première fois, le système vous demandera d’entrer les informations de votre du Data Warehouse de SQL Azure et votre compte de stockage blob Azure. À l’issue de ce script PowerShell exécute pour la première fois, les informations d’identification entrées vous auront été écrites dans un fichier de configuration SQLDW.conf dans le répertoire de travail actuel. L’exécution ultérieure de ce fichier de script PowerShell a la possibilité de lire que toutes nécessaires des paramètres à partir de ce fichier de configuration. Si vous devez modifier certains paramètres, vous pouvez choisir d’entrer les paramètres de l’écran, à l’invite de commandes par la suppression de ce fichier de configuration et entrer les valeurs des paramètres à l’invite ou pour modifier les valeurs de paramètre en modifiant le fichier SQLDW.conf dans votre répertoire *- DestDir* .

>[AZURE.NOTE] Afin d’éviter les conflits de noms de schéma avec celles qui existent déjà dans votre Data Warehouse de SQL Azure, lors de la lecture des paramètres directement à partir du fichier SQLDW.conf, un nombre aléatoire à 3 chiffres est ajouté au nom du schéma à partir du fichier SQLDW.conf le nom de schéma par défaut pour chaque exécution. Le script PowerShell peut vous demander un nom de schéma : le nom peut être spécifié à la discrétion de l’utilisateur.

Ce fichier de **script PowerShell** réalise les tâches suivantes :

- **Télécharge et installe des AzCopy**, si AzCopy n’est pas déjà installé.

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **Copie des données vers votre compte de stockage blob privée** à partir du blob public avec AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Charge les données à l’aide de Polybase (en exécutant LoadDataToSQLDW.sql) à votre du Data Warehouse de SQL Azure** à partir de votre compte de stockage blob privée avec les commandes suivantes.

    - Créer un schéma

            EXEC (''CREATE SCHEMA {schemaname};'');

    - Créer une information d’identification de la portée de la base de données

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - Créer une source de données externe pour un blob de stockage Azure

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - Créer un format de fichier externe, d’un fichier csv. Données non compressées et les champs sont séparés par le caractère.

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - Créer prix externe et les tables de voyage pour NYC taxi dataset dans le stockage blob Azure.

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Charger des données à partir de tables externes dans le stockage blob Azure à SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Créer un exemple de table de données (NYCTaxi_Sample) et insérer des données à celle-ci à partir de la sélection des requêtes SQL sur les tables de voyage et des frais. (Certaines étapes de cette procédure pas à pas a besoin d’utiliser cet exemple de table).

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

L’emplacement géographique de vos comptes de stockage affecte le temps de chargement.

>[AZURE.NOTE] En fonction de l’emplacement géographique de votre compte de stockage blob privée, le processus de copie des données à partir d’un blob publique à votre compte de stockage privé peut prendre environ 15 minutes, ou même plus, et le processus de chargement des données à partir de votre compte de stockage pour votre du Data Warehouse de SQL Azure peut prendre 20 minutes ou plus.  

Vous devez décider quelles si vous avez des fichiers en double sur la source et de destination.

>[AZURE.NOTE] Si le .csv fichiers à copier à partir du stockage blob publique à votre compte de stockage blob privée existent déjà dans votre compte de stockage privé blob, AzCopy vous demandera si vous souhaitez les remplacer. Si vous ne souhaitez pas les remplacer, d’entrée de **n** lorsque vous y êtes invité. Si vous souhaitez remplacer **toutes** les, d’entrée **un** lorsque vous y êtes invité. Vous pouvez également d’entrée **y** pour remplacer les fichiers .csv individuellement.

![Tracer #21][21]

Vous pouvez utiliser vos propres données. Si vos données sont dans votre ordinateur sur site dans votre application de la vie réelle, vous pouvez toujours utiliser AzCopy à télécharger les données sur site du stockage privé blob Azure. Vous ne devez modifier l’emplacement de la **Source** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, dans la commande AzCopy du fichier de script PowerShell pour le répertoire local qui contient vos données.


>[AZURE.TIP] Si vos données sont déjà dans votre espace de stockage des blob Azure privée dans votre application de la vie réelle, vous pouvez ignorer l’étape de AzCopy dans le script PowerShell et téléchargez directement les données du Data Warehouse de SQL Azure. Cela nécessitera des modifications supplémentaires du script pour l’adapter au format de vos données.


Ce script Powershell également s’intègre dans les informations du Data Warehouse de SQL Azure dans les fichiers d’exemple de prospection données SQLDW_Explorations.sql, SQLDW_Explorations.ipynb et SQLDW_Explorations_Scripts.py afin que ces trois fichiers sont prêtes à être testée instantanément une fois le script PowerShell terminé.

Après l’exécution réussie, vous verrez un écran comme ci-dessous :

![][20]

## <a name="dbexplore"></a>Exploration de données et de l’ingénierie de fonctionnalité dans le magasin de données SQL Azure

Dans cette section, nous effectuer une exploration de données et la génération de la fonctionnalité en exécutant des requêtes SQL contre du Data Warehouse de SQL Azure directement à l’aide **Des outils de données Visual Studio**. Toutes les requêtes SQL sont utilisés dans cette section, vous pouvez trouver dans l’exemple de script nommé *SQLDW_Explorations.sql*. Ce fichier a déjà été téléchargé dans votre répertoire local par le script PowerShell. Vous pouvez également récupérer à partir de [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mais le fichier dans Github ne dispose pas des informations du Data Warehouse de SQL Azure branchées.

Connectez-vous à votre du Data Warehouse de SQL Azure à l’aide de Visual Studio avec le nom de connexion du Data Warehouse de SQL et un mot de passe et ouvrir l' **Explorateur d’objets SQL** pour confirmer que la base de données et les tables n’ont pas été importés. Récupérer le fichier *SQLDW_Explorations.sql* .

>[AZURE.NOTE] Pour ouvrir un éditeur de requête de magasin de données parallèle (PDW), utilisez la commande **Nouvelle requête** alors que votre Assistant Empaquetage et déploiement est sélectionné dans l' **Explorateur d’objets SQL**. L’éditeur de requête SQL standard n’est pas pris en charge par l’Assistant Empaquetage et déploiement.

Voici le type de données exploration et la fonctionnalité tâches de génération effectuées dans cette section :

- Explorez les distributions de données de certains champs dans différentes fenêtres.
- Examinez la qualité des données des champs de latitude et de longitude.
- Générer des étiquettes de classification binaire et multiclass en fonction de la **Conseil\_volume**.
- Générer des fonctionnalités et de calcul/comparer les distances de voyage.
- Joignez les deux tables et extraire un échantillon aléatoire qui sera utilisé pour construire des modèles.

### <a name="data-import-verification"></a>Vérification des données d’importation

Ces requêtes fournissent une vérification rapide du nombre de lignes et de colonnes dans les tables remplis précédemment en bloc parallèle de Polybase importation,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Sortie :** Vous devez obtenir des 173,179,759 lignes et 14 colonnes.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploration : Distribution de voyage par medallion

Cet exemple de requête identifie les medallions (numéros de taxi) effectuer des voyages de plus de 100 dans un laps de temps spécifié. La requête pourra bénéficier de l’accès à la table partitionnée dans la mesure où il est conditionné par le schéma de partition de **capteur\_datetime**. Interroger le groupe de données complet vérifiera également l’utilisation de la table partitionnée et/ou d’analyse d’index.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Sortie :** La requête doit retourner un tableau avec des lignes indiquant les 13,369 medallions (taxis) et le nombre de voyage effectué par eux dans 2013. La dernière colonne contient le décompte du nombre de déplacements terminée.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploration : Distribution de voyage par medallion et hack_license

Cet exemple identifie les medallions (numéros de taxi) et hack_license numéros (pilotes) que terminé plus 100 voyages dans un laps de temps spécifié.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Sortie :** La requête doit retourner un tableau avec des 13,369 lignes spécifiant l’ID terminées que 100 les déplacements dans 2013 voiture/pilote 13,369. La dernière colonne contient le décompte du nombre de déplacements terminée.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Évaluation de la qualité : vérifier les enregistrements de longitude incorrecte ou latitude

Cet exemple examine si tous les champs de longitude ou latitude de contient une valeur non valide (les degrés en radians doivent être comprise entre -90 et 90), ou vous avez (0, 0) coordonnées.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Sortie :** La requête renvoie 837,467 voyages qui ont des champs de longitude ou latitude non valides.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploration : Bonhomme de neige et distribution de voyages pas Bonhomme de neige

Cet exemple recherche le nombre de déplacements qui ont été Bonhomme de neige ou le nombre qui ne étaient pas Bonhomme de neige dans un laps de temps spécifié (ou, dans le groupe de données complet si couvrant l’année complète, comme il est défini ici). Cette distribution reflète la distribution binaire d’étiquette à utiliser ultérieurement pour la modélisation de la classification binaire.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Sortie :** La requête doit retourner Bonhomme de neige les fréquences de conseil suivant pour l’année 2013 : 90,447,622 et 82,264,709 non-Bonhomme de neige.

### <a name="exploration-tip-classrange-distribution"></a>Exploration : Distribution de plage de la classe de Conseil

Cet exemple calcule la distribution des plages d’info-bulle dans un laps de temps donné (ou, dans le groupe de données complet si portant sur l’année complète). Il s’agit de la distribution des classes étiquette qui sera utilisé ultérieurement pour la modélisation de classification multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Sortie :**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploration : Calculer et comparer la distance de déplacement

Cet exemple convertit la remise en mains propres et la remise de longitude et latitude géographie de SQL pointe, calcule la distance de déplacement à l’aide de la différence de points de géographie SQL et renvoie un échantillon aléatoire des résultats de la comparaison. L’exemple de limite les résultats aux coordonnées valides uniquement à l’aide de la requête d’évaluation de qualité données évoquée précédemment.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Ingénierie de fonctionnalité à l’aide des fonctions SQL

Les fonctions SQL peuvent parfois être une option efficace pour l’ingénierie de la fonctionnalité. Dans cette procédure pas à pas, nous avons défini une fonction SQL pour calculer la distance directe entre les emplacements de prélèvement et de cette chute. Vous pouvez exécuter les scripts SQL suivants dans les **Outils de données Visual Studio**.

Voici le script SQL qui définit la fonction de la distance.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

Voici un exemple d’appeler cette fonction pour générer des fonctionnalités dans votre requête SQL :

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Sortie :** Cette requête génère une table (avec 2,803,538 lignes) avec la remise en mains propres et cette chute latitudes et longitudes et les distances directs correspondantes en miles. Voici les résultats pour les 3 premières lignes :

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>Préparer les données pour la construction d’un modèle

La requête suivante jointures la **nyctaxi\_voyage** et **nyctaxi\_tarif** tables, génère un classement binaire étiquette **Bonhomme de neige**, une étiquette de classement de multiples **Conseil\_classe**et extrait un échantillon du jeu de données complet joint. L’échantillonnage est effectuée en récupérant un sous-ensemble de l’ADPIC en fonction de l’heure d’extraction.  Cette requête peut être copiée puis collée directement dans le [Studio de formation Azure Machine](https://studio.azureml.net) [Importer des données] [ import-data] module de réception de données directe à partir de l’instance de base de données SQL Azure. La requête exclut les enregistrements avec incorrect (0, 0) coordonnées.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Lorsque vous êtes prêt à passer à la formation de Machine d’Azure, vous pouvez :  

1. Enregistrez la requête SQL finale pour extraire et échantillonner les données et copier-coller la requête directement dans une [Importation de données] [ import-data] module d’apprentissage de Machine d’Azure, ou
2. Persistance des données échantillonnées et ouvragées vous prévoyez d’utiliser pour le modèle de création dans une nouvelle table SQL DW et utiliser la nouvelle table dans les [Données d’importation] [ import-data] module dans la formation de Machine Azure. Le script PowerShell à l’étape précédente a fait cela pour vous. Vous pouvez lire directement à partir de cette table dans le module d’importation de données.


## <a name="ipnb"></a>Exploration de données et de l’ingénierie de fonctionnalité dans le carnet d’IPython

Dans cette section, nous allons exécuter d’exploration de données et la génération de la fonctionnalité en utilisant les Python et les requêtes SQL contre le Data Warehouse SQL créé précédemment. Un bloc-notes de IPython exemple nommé **SQLDW_Explorations.ipynb** et un fichier de script de Python **SQLDW_Explorations_Scripts.py** ont été téléchargés dans votre répertoire local. Ils sont également disponibles sur [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Ces deux fichiers sont identiques dans les scripts de Python. Le fichier de script de Python est fourni au cas où vous ne disposez pas d’un serveur IPython bloc-notes. Ces deux exemples de fichiers de Python sont conçus sous les **Python 2.7**.

Les informations du Data Warehouse de SQL Azure nécessaires dans l’échantillon IPython portable et le fichier de script Python téléchargement sur votre ordinateur local a été branchées précédemment par le script PowerShell. Elles peuvent être exécutées sans modification.

Si vous avez déjà défini un espace de travail de AzureML, vous pouvez directement télécharger l’échantillon IPython bloc-notes au service AzureML IPython portable et démarrer son exécution. Voici la procédure pour télécharger vers le service de carnet d’IPython de AzureML :

1. Connectez-vous à votre espace de travail AzureML et cliquez sur « Studio » dans la partie supérieure, cliquez sur « Portables » sur le côté gauche de la page web.

    ![Tracer #22][22]

2. Cliquez sur « Nouveau » dans le coin inférieur gauche de la page web, puis sélectionnez « Python 2 ». Ensuite, entrez un nom pour l’ordinateur portable et cliquez sur la case à cocher pour créer le nouveau bloc-notes IPython vide.

    ![Tracer #23][23]

3. Cliquez sur le symbole « Jupyter » dans le coin supérieur gauche de la IPython du nouvel ordinateur portable.

    ![Tracer #24][24]

4. Faites glisser et déposez l’échantillon IPython bloc-notes à la page de **l’arborescence** de votre service de AzureML IPython bloc-notes et cliquez sur **Télécharger**. Ensuite, l’exemple Qu'ipython portable seront téléchargé vers le service de carnet d’IPython de AzureML.

    ![Tracer #25][25]

Pour exécuter l’exemple IPython bloc-notes ou les Python fichier de script, les Python suivant les packages sont nécessaires. Si vous utilisez le service de carnet d’IPython de AzureML, ces packages ont été pré-installés.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

La séquence recommandée lors de la création de solutions d’analyse avancées sur AzureML avec une grande quantité de données est la suivante :

- Lire dans un petit échantillon des données dans un bloc de données en mémoire.
- Effectuer certaines visualisations et les explorations à l’aide des données échantillonnées.
- Expérimenter ingénierie de fonctionnalité à l’aide des données échantillonnées.
- Pour l’exploration de données plus grande, la manipulation des données et ingénierie de fonctionnalité, utilisez les Python pour émettre des requêtes SQL directement sur le Data Warehouse SQL.
- Décidez de la taille de l’échantillon pour convenir à la création de modèles de formation de Machine Azure.

Les types suivants sont quelques fonctionnalité exemples d’ingénierie, exploration de données et visualisation de données. Vous trouverez plus explorations de données dans l’exemple IPython portable et le fichier de script exemple Python.

### <a name="initialize-database-credentials"></a>Initialiser les informations d’identification de la base de données

Initialiser les paramètres de connexion de base de données dans les variables suivantes :

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Créer la connexion de base de données

Voici la chaîne de connexion qui crée la connexion à la base de données.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Numéro d’état de lignes et de colonnes dans la table < nyctaxi_trip >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Nombre total de lignes = 173179759  
- Nombre total de colonnes = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Numéro d’état de lignes et de colonnes dans la table < nyctaxi_fare >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Nombre total de lignes = 173179759  
- Nombre total de colonnes = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Lecture dans un échantillon de données de petite taille à partir de la base de données du magasin de données SQL

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Temps de lecture de que la table d’exemple est 14.096495 secondes.  
Nombre de lignes et de colonnes récupérés = (1000, 21).

### <a name="descriptive-statistics"></a>Statistiques descriptives

Vous êtes maintenant prêt à Explorer les données échantillonnées. Commençons par examiner certaines statistiques descriptives de le **voyage\_distance** (ou tout autre champ que vous choisissez de spécifier).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualisation : Exemple de traçage zone

Nous examinons ensuite le traçage de la distance de déplacement visualiser l’étendue de la zone.

    df1.boxplot(column='trip_distance',return_type='dict')

![Tracer #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualisation : Exemple de tracé de Distribution

Parcelles qui visualiser la distribution et un histogramme pour les distances de voyage échantillonnées.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tracer #2][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisation : Barre et ligne de trace

Dans cet exemple, nous l’emplacement de la distance de déplacement dans cinq emplacements et visualiser les résultats de mise au rebus.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Nous pouvons tracer la distribution emplacement ci-dessus dans une barre ou tracé avec la ligne :

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Tracer #3][3]

et

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Tracer #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualisation : Exemples de Scatterplot

Nous affichons le nuage de points entre **voyage\_temps\_dans\_s** et **voyage\_distance** pour voir s’il existe une corrélation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Tracer #6][6]

De la même façon, nous pouvons vérifier la relation entre **taux\_code** et **voyage\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Tracer #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploration de données sur des données échantillonnées en utilisant des requêtes SQL dans IPython bloc-notes

Dans cette section, nous explorons les distributions de données à l’aide de données échantillonnées qui sont rendu persistant dans la nouvelle table que nous avons créés ci-dessus. Notez que les explorations similaires peuvent être effectuées en utilisant les tables d’origine.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploration : Signaler le nombre de lignes et de colonnes de la table d’échantillonnage

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploration : Distribution de dépassé, non Bonhomme de neige

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Exploration : Distribution de classe Conseil

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploration : Tracer la distribution de l’info-bulle par classe

    tip_class_dist['tip_freq'].plot(kind='bar')

![Tracer #26][26]


#### <a name="exploration-daily-distribution-of-trips"></a>Exploration : Distribution quotidienne de déplacements

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploration : Distribution de voyage par medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploration : Distribution de voyage par licence medallion et piratage

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Exploration : Distribution de temps de voyage

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploration : Distribution de distance de voyage

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Exploration : Distribution du type de paiement

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Vérifiez que la forme finale de la table featurized

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Créer des modèles dans la formation de Machine Azure

Nous sommes maintenant prêts à procéder à la construction du modèle et le modèle déploiement dans [Azure Machine Learning](https://studio.azureml.net). Les données sont prêtes à être utilisées dans un des problèmes de prévision identifiées précédemment, à savoir :

1. **Classement binaire**: pour prévoir ou non une info-bulle a été payée pour un voyage.

2. **Multiclass classification**: pour prédire la plage de pourboire, en fonction des classes définies précédemment.

3. **Tâche de régression**: prévoir le montant du pourboire pour un voyage.  



Pour commencer l’exercice de modélisation, connectez-vous à votre espace de travail de **Formation de Machine d’Azure** . Si vous n’avez pas encore créé une machine de formation d’espace de travail, voir [créer un espace de travail Azure ML](machine-learning-create-workspace.md).

1. Pour commencer avec formation de Machine d’Azure, consultez [Nouveautés Azure Machine Learning Studio ?](machine-learning-what-is-ml-studio.md)

2. Se connecter à la [Machine Azure Studio de formation](https://studio.azureml.net).

3. La page d’accueil de Studio fournit une mine d’informations, des vidéos, des didacticiels, des liens vers la référence de Modules et d’autres ressources. Pour plus d’informations sur la formation de Machine d’Azure, consultez le [Centre de Documentation de formation de Machine Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Une expérience de formation classique se compose des étapes suivantes :

1. Créer une expérience **+ de nouveau** .
2. Importer les données dans Azure ML.
3. Prétraitement, de transformer et de manipuler les données en fonction des besoins.
4. Générer les fonctionnalités en fonction des besoins.
5. Fractionner les données en groupes de données formation/validation/test (ou avoir des groupes de données distinct pour chaque).
6. Sélectionnez un ou plusieurs algorithmes d’apprentissage machine en fonction du problème de la formation à résoudre. Par exemple, une classification binaire, classification multiclass, régression.
7. Former un ou plusieurs modèles à l’aide de la formation dataset.
8. Le groupe de données de validation à l’aide des formation ou les modèles de score.
9. Évaluer l’ou les modèles pour calculer les mesures appropriées pour le problème de la formation.
10. Bien régler l’ou les modèles, puis sélectionnez le meilleur modèle à déployer.

Dans cet exercice, nous avons déjà exploré conçu les données SQL Data Warehouse et a décidé, sur la taille de l’échantillon en ML d’Azure d’acquisition. Voici la procédure pour créer un ou plusieurs des modèles de prévision :

1. Obtenir les données en ML Azure en utilisant les [Données d’importation] [ import-data] module, disponible dans la section **données d’entrée et de sortie** . Pour plus d’informations, consultez [Importation de données] [ import-data] page de référence du module.

    ![Données Azure ML][17]

2. Sélectionnez la **Base de données de SQL Azure** comme **source de données** dans le panneau **Propriétés** .

3. Entrez le nom DNS de base de données dans le champ **nom du serveur de base de données** . Format :`tcp:<your_virtual_machine_DNS_name>,1433`

4. Entrez le **nom de la base de données** dans le champ correspondant.

5. Dans le **nom de compte utilisateur de serveur**et le *mot de passe* mot de **passe du compte serveur**, entrez le *nom d’utilisateur SQL* .

6. Cochez l’option **accepter n’importe quel certificat du serveur** .

7. Dans la zone de texte modifiable de **requête de base de données** , collez la requête qui extrait les nécessaires (y compris les champs calculés, tels que les étiquettes) des champs de base de données d’et vers le bas échantillonne les données à la taille de l’échantillon de votre choix.

Un exemple d’une expérience de classification binaire que lire les données directement à partir de la base de données SQL Data Warehouse est dans la figure ci-dessous (n’oubliez pas de remplacer la table noms nyctaxi_trip et nyctaxi_fare par le nom de schéma et les noms de table que vous avez utilisé dans votre procédure pas à pas). Expériences similaires peuvent être construits pour la classification de multiclass et des problèmes de régression.

![Train de ML Azure][10]

> [AZURE.IMPORTANT] Dans les données de modélisation d’extraction et d’échantillonnage des exemples de la requête fournies dans les sections précédentes, **toutes les étiquettes pour les modélisation de trois exercices sont inclus dans la requête**. Une étape importante (obligatoire) dans chacun des exercices de modélisation est à **Exclure** les étiquettes inutiles pour les deux autres problèmes et toutes autres **fuites de cible**. Par exemple, lorsque vous utilisez la classification binaire, utilisez l’étiquette **Bonhomme de neige** et exclure les champs **Conseil\_classe**, **pointe\_montant**, et **total\_montant**. Ces derniers sont des fuites de cible dans la mesure où ils impliquent la pointe payé.
>
> Pour exclure les colonnes inutiles ou fuites de cible, vous pouvez utiliser la [Sélection des colonnes dans un groupe de données] [ select-columns] module ou [Modifier les métadonnées][edit-metadata]. Pour plus d’informations, reportez-vous à la section [Sélectionner des colonnes dans un groupe de données] [ select-columns] et [Modifier les métadonnées] [ edit-metadata] pages de référence.

## <a name="mldeploy"></a>Déployer des modèles dans l’apprentissage automatique de Azure

Lorsque votre modèle est prêt, vous pouvez le déployer facilement comme un service web directement à partir de l’expérience. Pour plus d’informations sur le déploiement de services web de ML d’Azure, voir [déploiement d’un service web de formation de Machine Azure](machine-learning-publish-a-machine-learning-web-service.md).

Pour déployer un service web, vous devez :

1. Créer une expérience d’évaluation.
2. Déployer le service web.

Pour créer une expérience d’évaluation à partir d’une expérience de formation **terminé** , cliquez sur **Créer évaluation tester** dans la barre d’actions inférieure.

![Score Azure][18]

Formation de Machine Azure va tenter de créer une expérience d’évaluation basée sur les composants de l’expérience de formation. En particulier, il sera :

1. Enregistrer le modèle formé et retirez les modules de formation de modèle.
2. Identifier un **port d’entrée** de logique pour représenter le schéma des données d’entrée attendu.
3. Identifier un **port de sortie** de logique pour représenter le schéma de sortie du service web attendu.

Lors de la création de l’expérience d’évaluation, examiner et rendre l’ajuster en fonction des besoins. Un réglage par défaut est de remplacer le dataset d’entrée ou la requête par un qui exclut les champs de l’étiquette, celles-ci ne seront pas disponibles lorsque le service est appelé. Il est également conseillé de réduire la taille des dataset d’entrée ou de la requête à quelques enregistrements, juste assez pour indiquer le schéma d’entrée. Il est commun à exclure tous les d’entrée des champs et d’inclure uniquement les **Étiquettes d’un score** et **Un score de probabilités** dans la sortie à l’aide de la [Sélection des colonnes dans un groupe de données] pour le port de sortie,[ select-columns] module.

Un exemple de notation expérience est fourni dans la figure ci-dessous. Lorsque vous êtes prêt à déployer, cliquez sur le bouton **Publier le SERVICE WEB** dans la barre d’actions inférieur.

![Publication d’Azure ML][11]


## <a name="summary"></a>Résumé
Pour résumer ce que nous l’avons fait dans ce didacticiel de procédure pas à pas, vous avez créé un environnement science de données Azure, a travaillé avec un dataset grand public, en guidant dans le processus de Science données Team, d’acquisition de données pour modéliser la formation et au déploiement d’un service web de formation de Machine Azure.

### <a name="license-information"></a>Informations de licence

Cette procédure pas à pas l’échantillon et l’accompagne les scripts et les IPython notebook(s) sont partagées par Microsoft sous licence MIT. Veuillez vérifier le fichier LICENSE.txt le répertoire de l’exemple de code sur GitHub pour plus de détails.

## <a name="references"></a>Références

• La [Page de téléchargement de Monroy de Andrés NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/)  
• [Mise en échec des données de voyage du NYC Taxi par Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi briefings Commission recherche et statistiques](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
