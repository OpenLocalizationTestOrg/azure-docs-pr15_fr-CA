<properties 
    pageTitle="Scénario de solution de véhicule télémétrie analytique : approfondie de la solution | Microsoft Azure" 
    description="Les capacités d’Intelligence de Cortana permet d’obtenir des analyses prédictives et en temps réel sur la santé du véhicule et à la conduite habitudes." 
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
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Scénario de solution de véhicule télémétrie analytique : approfondie de la solution

Cette **menu** contient des liens vers les sections de ce manuel : 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Cette section explore en chacune des étapes figurant dans l’Architecture de la Solution avec des instructions et des pointeurs pour la personnalisation. 

## <a name="data-sources"></a>Sources de données

La solution utilise deux sources de données différentes :

- **les signaux simulés de véhicule et groupe de données de diagnostic** et 
- **catalogue de véhicule**

Un simulateur de télématique du véhicule est inclus dans le cadre de cette solution. Il émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au modèle de conduite à un moment donné dans le temps. Cliquez sur [Simulateur de VEHICULE télématique](http://go.microsoft.com/fwlink/?LinkId=717075) pour télécharger la **Solution de Visual Studio de véhicule télématique Simulator** pour les personnalisations selon vos besoins. Le catalogue de véhicule contient un dataset de référence avec un numéro d’immatriculation au mappage du modèle.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Figure 2 : simulateur de VEHICULE télématique*

Il s’agit d’un groupe de données au format JSON qui contient le schéma suivant.

Colonne | Description | Valeurs 
 ------- | ----------- | --------- 
IDENTIFICATION DU VÉHICULE | Numéro d’Identification du véhicule générée de manière aléatoire | Celle-ci est obtenue à partir d’une liste de numéros d’identification de véhicule générée de manière aléatoire 10 000.
Température extérieure | La température extérieure où le véhicule est conduite | Nombre généré de manière aléatoire entre 0 et 100
Température du moteur | La température du moteur du véhicule | Nombre généré de manière aléatoire à partir de 0 à 500.
Vitesse | La vitesse de rotation à laquelle le véhicule est conduite. | Nombre généré de manière aléatoire entre 0 et 100
Carburant | Le niveau de carburant du véhicule | Nombre généré de manière aléatoire entre 0 et 100 (indique le pourcentage du niveau de carburant)
EngineOil | Le niveau d’huile de moteur de véhicule | Nombre généré de manière aléatoire entre 0 et 100 (indique le pourcentage du niveau de huile moteur)
Pression des pneus | La pression des pneus de véhicule | Généré de manière aléatoire nombre de 0-50 (indique le pourcentage du niveau de pression pneumatique)
Compteur kilométrique | Le compteur kilométrique du véhicule | Nombre généré de manière aléatoire à partir de 0-200 000
Accelerator_pedal_position | L’accélérateur en position pédale de véhicule | Nombre généré de manière aléatoire entre 0 et 100 (indique le pourcentage du niveau accélérateur)
Parking_brake_status | Indique si le véhicule y séjourne ou non | Valeur True ou False
Headlamp_status | Indique où le projecteur est activé ou non | Valeur True ou False
Brake_pedal_status | Indique si la pédale de frein est enfoncée ou non | Valeur True ou False
Transmission_gear_position | La position de vitesse de transmission de véhicule | États : premier, deuxième, troisième, quatrième, cinquième, sixième, septième, huitième
Ignition_status | Indique si le véhicule est en cours d’exécution ou arrêté | Valeur True ou False
Windshield_wiper_status | Indique si l’essuie-glace est activée ou non | Valeur True ou False
ABS | Indique si ABS est activé ou non | Valeur True ou False
Horodatage | L’horodatage lorsque le point de données est créé. | Date
Ville | L’emplacement du véhicule | 4 villes dans cette solution : Bellevue, Redmond, Sammamish, Seattle


Le dataset de référence de modèle de véhicule contient le VIN pour le mappage de modèle. 

IDENTIFICATION DU VÉHICULE | Modèle |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Berline |
8J0U8XCPRGW4Z3NQE | Hybride |
WORG68Z2PLTNZDBI7 | Famille berline |
JTHMYHQTEPP4WBMRN | Berline |
W9FTHG27LZN1YWO0Y | Hybride |
MHTP9N792PHK08WJM | Famille berline |
EI4QXI2AXVQQING4I | Berline |
5KKR2VB4WHQH97PF8 | Hybride |
W9NSZ423XZHAONYXB | Famille berline |
26WJSGHX4MA5ROHNL | Convertible |
GHLUB6ONKMOSI7E77 | Wagon de station |
9C2RHVRVLMEJDBXLP | Petite voiture |
BRNHVMZOUJ6EOCP32 | Petite SUV |
VCYVW0WUZNBTM594J | Voiture de sport |
HNVCE6YFZSA5M82NY | SUV de taille moyenne |
4R30FOR7NUOBL05GJ | Wagon de station |
WYNIIY42VKV6OQS1J | Gros SUV |
8Y5QKG27QET1RBK7I | Gros SUV |
DF6OX2WSRA6511BVG | Coupé |
Z2EOZWZBXAEW3E60T | Berline |
M4TV6IEALD5QDS3IR | Hybride |
VHRA1Y2TGTA84F00H | Famille berline |
R0JAUHT1L1R3BIKI0 | Berline |
9230C202Z60XX84AU | Hybride |
T8DNDN5UDCWL7M72H | Famille berline |
4WPYRUZII5YV7YA42 | Berline |
D1ZVY26UV2BFGHZNO | Hybride |
XUF99EW9OIQOMV7Q7 | Famille berline
8OMCL3LGI7XNCC21U | Convertible |
…….  |   |


### <a name="to-generate-simulated-data"></a>Pour générer des données simulées
1.  Pour télécharger le package de simulateur de données, cliquez sur la flèche dans le coin supérieur droit du nœud Simulator télématique de véhicule. Enregistrer et extraire les fichiers localement sur votre ordinateur. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png)*Figure 3 : modèle de Solution de véhicule télémétrie Analytique*

2.  Sur votre ordinateur local, accédez au dossier où vous avez extrait le package de simulateur de VEHICULE télématiques. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png)*Figure 4 – dossier Simulator télématique de véhicule*

3.  Exécutez l’application **CarEventGenerator.exe**.

### <a name="references"></a>Références

[Solution de véhicule télématique simulateur de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Concentrateur d’événements Azure](https://azure.microsoft.com/services/event-hubs/)

[Usine de données Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## <a name="ingestion"></a>Réception
Combinaisons de concentrateurs d’événement Azure Analytique de flux de données et Data Factory sont exploitées pour les signaux du véhicule, les événements de diagnostic, d’acquisition et en temps réel et par lots analytique. Tous ces composants sont créés et configurés dans le cadre du déploiement de la solution. 

### <a name="real-time-analysis"></a>Analyse en temps réel
Les événements générés par le simulateur de télématique du véhicule sont publiés dans le concentrateur d’événements à l’aide du SDK de concentrateur événement. La tâche de flux de données Analytique ingests ces événements à partir du concentrateur de l’événement et traite les données en temps réel afin d’analyser l’état de santé du véhicule. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png) 

*Figure 5 - tableau de bord de concentrateur d’événements*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png) 

*Figure 6 : traitement des données d’une tâche flux analytique*

Le travail d’analytique de flux ;

- ingests des données à partir du concentrateur d’événements 
- effectue une jointure avec les données de référence pour mapper le numéro d’identification du véhicule au modèle correspondant 
- les rend persistantes dans le stockage blob Azure pour l’analytique des lots riche. 

La requête analytique de flux suivant est utilisée pour rendre persistantes les données dans le stockage blob Azure. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*Figure 7 : requête de travail analytique de flux pour la réception de données*

### <a name="batch-analysis"></a>Analyse en mode batch
Aussi, nous générons un volume supplémentaire de signaux de véhicule simulé et diagnostic dataset pour l’analytique de lot plus riche. Cela est nécessaire pour assurer un volume de données représentatives pour le traitement par lots. À cette fin, nous utilisons un pipeline nommé « PrepareSampleDataPipeline » dans le flux de travail Azure Data Factory pour générer année une de signaux de véhicule simulé et jeu de données de diagnostic. Cliquez sur [activité personnalisée de Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) pour télécharger l’activité DotNet Data Factory personnalisée solution Visual Studio pour les personnalisations selon vos besoins. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*Figure 8 : préparer les données d’exemple de processus de traitement par lots*

Le pipeline est composé du chargeur automatique de documents .net personnalisé activité, afficher ici :

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png) 

*Figure 9 - PrepareSampleDataPipeline*

Une fois que le pipeline s’exécute correctement et de groupe de données « RawCarEventsTable » est marqué « Prêt », un an intéressant de signaux de véhicule simulé et diagnostic des données sont produites. Vous consultez le dossier et le fichier créé dans votre compte de stockage sous le conteneur « connectedcar » suivants :

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*Figure 10 - sortie de PrepareSampleDataPipeline*

### <a name="references"></a>Références

[Azure SDK de concentrateur d’événement pour l’ingestion de flux](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Capacités de déplacement de données Data Factory Azure](../data-factory/data-factory-data-movement-activities.md)
[Azure données usine DotNet activité](../data-factory/data-factory-use-custom-activities.md)

[Solution de visual studio Azure données usine DotNet activité pour la préparation des exemples de données](http://go.microsoft.com/fwlink/?LinkId=717077) 


## <a name="partition-the-dataset"></a>Partition du groupe de données

Le brut véhicule semi-structurés signaux et un groupe de données de diagnostic sont partitionnés à l’étape de préparation des données dans un format année/mois. Ce partitionnement favorise une interrogation plus efficace et évolutif du stockage à long terme en activant sur panne à partir du compte d’un blob à l’autre que le premier compte se remplit. 

>[AZURE.NOTE] Cette étape de la solution est applicable uniquement pour le traitement par lots.

Entrée et sortie gestion des données :

- Les **données de sortie** (nommée *PartitionedCarEventsTable*) est à conserver pendant une longue période de temps que le formulaire de base / « rawest » de données dans le client « données lac ». 
- Les **données d’entrée** pour ce pipeline seront éliminées en général que les données de sortie ont toujours une fidélité optimale à l’entrée, il est seulement stocké (partitionner) mieux pour une utilisation ultérieure.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*La figure 11 – workflow d’événements de voiture de Partition*

Les données brutes sont partitionnées à l’aide d’une activité de la HDInsight de la ruche dans « PartitionCarEventsPipeline ». Les exemples de données générés à l’étape 1 pour une année sont partitionnés par année et le mois. Les partitions sont utilisées pour générer des signaux du véhicule et les données de diagnostic pour chaque mois (12 partitions au totale) de l’année. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Figure 12 : PartitionCarEventsPipeline*

Le script suivant de la ruche, nommé « partitioncarevents.hql », est utilisé pour le partitionnement et se trouve dans le dossier « \demo\src\connectedcar\scripts » de la zip téléchargé. 


    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string,
                YearNo                          int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Figure 13 - Script de la ruche de PartitionConnectedCarEvents*

Une fois que le pipeline est exécuté avec succès, vous voyez les partitions suivantes générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Figure 14 : sortie partitionnée*

Les données a été optimisé, il est plus facile à gérer et prêt pour un traitement supplémentaire pour obtenir des idées de lot riche. 

## <a name="data-analysis"></a>Analyse des données

Dans cette section, vous allez apprendre à combiner Analytique de flux d’Azure, formation de Machine d’Azure, Azure Data Factory et Azure HDInsight destinées à des avancées analytique sur la santé du véhicule et à la conduite des habitudes. Il existe trois sous-sections ici :

1.  **Apprentissage automatique**: cette sous-section contient des informations sur l’expérience de détection des anomalies que nous avons utilisée dans cette solution pour prédire les véhicules exigeant une maintenance maintenance et les véhicules exigeant des rappels en raison de problèmes de sécurité.
2.  **Analyse en temps réel**: cette sous-section contient des informations concernant l’analytique en temps réel en utilisant le langage de requête Analytique flux et amélioration de l’expérience d’apprentissage automatique en temps réel à l’aide d’une application personnalisée.
3.  **Analyse par lot**: cette sous-section contient des informations concernant la transformation et le traitement des données par lots à l’aide de HDInsight d’Azure et formation de Machine Azure operationalized par Azure Data Factory.

### <a name="machine-learning"></a>Apprentissage automatique

Notre objectif ici est de prédire les véhicules exigeant une maintenance ou rappel selon certaines statistiques de la santé. Nous faire part des principes suivants

- Si une des trois conditions suivantes est remplie, les véhicules nécessitent la **maintenance de maintenance**:
    - Pression des pneus est faible
    - Niveau d’huile de moteur est faible
    - Température du moteur est élevé

- Si une des conditions suivantes est remplie, les véhicules peuvent présentent un **problème de sécurité** et nécessiter le **rappel**:
    - Température du moteur est élevée mais la température extérieure est faible
    - Température du moteur est faible mais la température extérieure est élevée

Selon les exigences précédentes, nous avons créé deux modèles distincts pour détecter des anomalies, un pour la détection de maintenance de véhicule et un pour la détection de rappel de véhicule. Dans ces deux modèles, l’algorithme Principal composant analyse (PCA) intégré est utilisé pour la détection des anomalies. 

**Modèle de détection de maintenance**

Si un des trois indicateurs de pression des pneus, huile moteur ou température du moteur - satisfait à sa condition respectif, le modèle de détection de maintenance signale une anomalie. Par conséquent, nous devons uniquement tenir compte de ces trois variables dans la construction du modèle. Dans notre expérience d’apprentissage d’ordinateur Azure, nous utilisons tout d’abord un module de **Sélectionner des colonnes dans un groupe de données** pour extraire ces trois variables. Ensuite, nous utilisons le module de détection des anomalies de basée sur l’Assistant Compatibilité des programmes pour construire le modèle de détection des anomalies. 

Analyse par composantes principales (PCA) est une technique établie dans l’apprentissage automatique qui peut être appliqué à la sélection des fonctionnalités, de classification et de détection des anomalies. PCA convertit un ensemble de cas contenant des variables éventuellement en corrélation, dans un ensemble de valeurs, appelés les composants de l’entité de sécurité. L’idée principale de modélisation basée sur l’Assistant Compatibilité des programmes est de données de projet vers un espace de dimension inférieure afin que les fonctionnalités et les anomalies peuvent être plus facilement identifiés.
 
Pour chaque nouvelle entrée pour le modèle de la détection, le détecteur d’anomalie calcule d’abord sa projection sur les vecteurs propres et calcule alors l’erreur reconstruction normalisée. Cette erreur normalisée est le score de l’anomalie. L’instance n’est plus l’erreur, plus il est anormal. 

Problème de détection de maintenance, chaque enregistrement peut être considéré comme un point dans un espace 3D est défini par la température du moteur, la pression des pneus et huile moteur coordonnées. Pour capturer ces anomalies, nous pouvons projet les données d’origine dans l’espace 3D sur un espace 2D à l’aide d’Assistant Compatibilité des programmes. Par conséquent, nous avons défini le paramètre nombre de composants à utiliser dans l’Assistant Compatibilité des programmes à 2. Ce paramètre joue un rôle important dans l’application de détection d’anomalie de basée sur l’Assistant Compatibilité des programmes. Après la projection des données à l’aide d’Assistant Compatibilité des programmes, nous pouvons identifier ces anomalies plus facilement.

**Modèle de détection des anomalies de rappel** Dans le modèle de détection des anomalies de rappel, nous utilisons la sélection des colonnes dans le Dataset et basée sur l’Assistant Compatibilité des programmes des anomalies de la même manière, les modules de détection. En particulier, nous avons tout d’abord extraire trois variables - température du moteur, la température extérieure et vitesse - à l’aide du module de **Sélectionner des colonnes dans un groupe de données** . Nous avons également inclure la variable de la vitesse dans la mesure où la température du moteur est généralement corrélée à la vitesse. Ensuite, nous utilisons les module de détection des anomalies de basée sur l’Assistant Compatibilité des programmes pour les données à partir de l’espace en 3 dimensions du projet sur un espace 2D. Les critères de rappel sont satisfaites et que le véhicule exige rappel lorsque la température du moteur et la température extérieure sont en corrélation très négatif. Utilisez l’algorithme de détection d’anomalie de basée sur l’Assistant Compatibilité des programmes, nous pouvons capturer les anomalies après avoir exécuté l’Assistant Compatibilité des programmes. 

Lors de l’apprentissage d’un modèle, il faut utiliser des données de normale, qui ne nécessitent pas de maintenance ou rappel comme données d’entrée à former le modèle de détection d’anomalie de basée sur l’Assistant Compatibilité des programmes. Dans l’expérience d’évaluation, nous utilisons le modèle de détection des anomalies formés pour détecter si le véhicule nécessite la maintenance ou rappel ou non. 


### <a name="real-time-analysis"></a>Analyse en temps réel

La requête SQL flux Analytique est utilisée pour obtenir la moyenne de tous les paramètres de véhicule importants tels que la vitesse du véhicule, niveau de carburant, température du moteur, compteur kilométrique, la pression des pneus, du niveau d’huile moteur et d’autres personnes. Les moyennes sont utilisés pour détecter des anomalies, émettre des alertes et de déterminer les conditions de santé globale de véhicules utilisés dans une région spécifique et puis l’associer à des données démographiques. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

La figure 15 – requête analytique de flux pour le traitement en temps réel

Toutes les moyennes sont calculées en fonction d’un TumblingWindow de 3 secondes. Nous utilisons TubmlingWindow dans ce cas puisque nous avons besoin d’intervalles de temps contigus et non superposés. 

Pour en savoir plus sur les fonctionnalités de « Windowing » Azure flux Analytique, cliquez sur [Windowing (Analytique de flux Azure)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Prévision en temps réel**

Une application est incluse dans le cadre de la solution à mettre le modèle d’apprentissage automatique en temps réel. Cette application appelée « RealTimeDashboardApp » est créée et configurée dans le cadre du déploiement de la solution. L’application effectue les opérations suivantes :

1.  Écoute à une instance d’événement concentrateur où Analytique de flux publie les événements dans un modèle en permanence. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Figure 16 – requête analytique de flux pour les données de publication pour une sortie instance du concentrateur d’événements* 

2.  Pour chaque événement qui reçoit de cette application : 

    - Traite les données à l’aide du point de terminaison de notation de requête-réponse formation Machine (RR). Le point de terminaison de RR est automatiquement publiée dans le cadre du déploiement.
    - La sortie de RR est publiée dans un groupe de données PowerBI à l’aide de l’API de l’émission.

Ce modèle est également applicable aux scénarios dans lesquels vous souhaitez intégrer une application métier (LoB) avec le flux analytique en temps réel, pour des scénarios tels que des alertes, de notifications et de messagerie.

Cliquez sur [RealtimeDashboardApp téléchargement](http://go.microsoft.com/fwlink/?LinkId=717078) pour télécharger la solution Visual Studio de RealtimeDashboardApp pour les personnalisations. 

**Pour exécuter l’Application de tableau de bord en temps réel**

1.  Cliquez sur le nœud PowerBI sur la vue de diagramme et cliquez sur le lien « Télécharger Application de tableau de bord en temps réel » dans le volet Propriétés. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png)*Figure 17 – instructions de configuration de tableau de bord de PowerBI*
2.  Extraire et enregistrer localement ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *Figure 18 – dossier de RealtimeDashboardApp*
3.  Exécutez l’application RealtimeDashboardApp.exe
4.  Fournir des informations d’identification valides de puissance BI, connectez-vous et cliquez sur Accepter ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png)![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figure 19 – RealtimeDashboardApp : Se connecter à PowerBI*

>[AZURE.NOTE] Si vous souhaitez vider le groupe de données PowerBI, exécutez la RealtimeDashboardApp avec le paramètre « flushdata » : 

    RealtimeDashboardApp.exe -flushdata

### <a name="batch-analysis"></a>Analyse en mode batch

L’objectif ici est de montrer la comment Contoso auto utilise les capacités de calcul Azure pour exploiter des données volumineuses pour Familiarisez-vous riche sur la conduite de modèle, le comportement de l’utilisation et la santé de véhicule. Cela permet de :

- Améliorer l’expérience client et de le rendre moins coûteux en fournissant des informations sur la conduite des habitudes et des comportements de conduite efficace de carburant
- En savoir plus proactive concernant les clients et leurs conduites connues régissent les décisions commerciales et de fournir les meilleurs produits et services

Dans cette solution, nous ciblons les mesures suivantes :

1.  **Comportement de conduite agressive**: identifie la tendance des modèles, des emplacements, conduite conditions et l’heure de l’année pour obtenir des informations sur les modèles de conduite agressives. Moteurs de Contoso peut Utilisez ces informations pour les campagnes marketing, nouvelles fonctionnalités personnalisées et l’assurance de l’utilisation de la commande.
2.  **Comportement de conduite efficace de carburant**: identifie la tendance des modèles, des emplacements, conduite conditions et moment de l’année pour obtenir des informations sur les modèles de conduite efficace de carburant. Moteurs de Contoso ces idées de campagnes marketing conduite de nouvelles fonctionnalités et de reporting proactive pour les pilotes de coût effectif et environnement convivial habitudes de conduite. 
3.  **Rappeler des modèles**: identifie les modèles nécessitant des rappels par la machine de détection des anomalies expérience de formation à mettre en application

Nous allons voir en détail chacune de ces mesures,


**Modèle de conduite agressive**

Les signaux de véhicule partitionnées et les données de diagnostic sont traitées dans le pipeline nommé « AggresiveDrivingPatternPipeline » qui s’à l’aide de la ruche pour déterminer les modèles, emplacement, véhicule, conditions de conduite et d’autres paramètres qui présente un motif de conduite agressive.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) 
*La figure 20 – agressif conduite workflow de modèle*

Le script de la ruche nommé « aggresivedriving.hql » utilisés pour l’analyse du modèle de condition de conduite agressive se trouve au dossier « \demo\src\connectedcar\scripts » de la zip téléchargé. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                vin                         string, 
                model                       string,
                timestamp                   string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,
                brake_pedal_status          string,
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Figure 21 – agressif conduite requête ruche de modèle*

Il utilise la combinaison de la position d’engrenage de transmission, état de pédale de frein et la vitesse du véhicule pour détecter des reckless/agressif conduite basé sur le modèle à haute vitesse de freinage. 

Une fois que le pipeline est exécuté avec succès, vous voyez les partitions suivantes générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png) 

*La figure 22 – sortie de AggressiveDrivingPatternPipeline*


**Modèle de pilotage efficace de carburant**

Les signaux de véhicule partitionnées et les données de diagnostic sont traitées dans le pipeline nommé « FuelEfficientDrivingPatternPipeline ». Ruche est utilisé pour déterminer les modèles, emplacement, véhicule, conditions de conduite et autres propriétés qui exposent le modèle de pilotage efficace de carburant.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*Figure 23 – carburant conduite modèle flux de travail efficace*

Le script de la ruche nommé « fuelefficientdriving.hql » utilisés pour l’analyse du modèle de condition de conduite agressive se trouve au dossier « \demo\src\connectedcar\scripts » de la zip téléchargé. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                vin                         string, 
                model                       string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,                
                brake_pedal_status          string,            
                accelerator_pedal_position  string,                             
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Figure 24 – efficace conduite requête de ruche modèle carburant*

Il utilise la combinaison d’une position de vitesse de transmission du véhicule, état de pédale de frein et accélérateur vitesse pédale de position pour détecter des combustibles efficace conduite en fonction de l’accélération, de freinage et à accélèrent les modèles. 

Une fois que le pipeline est exécuté avec succès, vous voyez les partitions suivantes générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*Figure 25 – sortie de FuelEfficientDrivingPatternPipeline*


**Rappeler les prévisions**

L’expérience d’apprentissage de machine est mis en service et publiée sous la forme d’un service web dans le cadre du déploiement de la solution. Le lot de notation de point de terminaison est exploité dans ce flux de travail, enregistré sous la forme d’un service de fabrique liée de données et d’operationalized à l’aide du lot de fabrique des données de notation activité.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png) 

*La figure 26 – Machine de formation de point de terminaison enregistré comme service lié dans l’usine de données*

Le service lié enregistré est utilisé dans le DetectAnomalyPipeline pour évaluer les données en utilisant le modèle de détection des anomalies. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png) 

*La figure 27 – Azure Machine Learning lot de score d’activité dans l’usine de données* 

Il existe quelques étapes effectuées dans ce pipeline pour préparer les données afin qu’elle peut être operationalized avec le traitement par lots score de service web. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png) 

*Figure 28 – DetectAnomalyPipeline pour prévoir les véhicules exigeant des rappels* 

Une fois l’évaluation terminée, une activité de la HDInsight est utilisée pour traiter et agréger les données qui sont classés en tant que les anomalies du modèle avec un score de probabilité de 0,60 ou supérieur.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                         string,
                model                       string,
                gendate                     string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                fuel                        string,
                engineoil                   string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position  string,
                parking_brake_status        string,
                headlamp_status             string,
                brake_pedal_status          string,
                transmission_gear_position  string,
                ignition_status             string,
                windshield_wiper_status     string,
                abs                         string,
                maintenanceLabel            string,
                maintenanceProbability      string,
                RecallLabel                 string,
                RecallProbability           string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                         string,
                model                       string,
                city                        string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                Year                        string,
                Month                       string              
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Une fois que le pipeline est exécuté avec succès, vous voyez les partitions suivantes générées dans votre compte de stockage sous le conteneur « connectedcar ».

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*La figure 30 – Figure 30 – DetectAnomalyPipeline sortie*


## <a name="publish"></a>Publier

### <a name="real-time-analysis"></a>Analyse en temps réel

Une des requêtes dans le travail d’analytique de flux publie les événements sur une sortie instance du concentrateur de l’événement. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*La figure 31 : flux de travail d’analytique publie vers une sortie instance du concentrateur d’événements*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figure 32 : flux de requête analytique pour publier la sortie instance du concentrateur d’événements*

Ce flux d’événements est consommé par le RealTimeDashboardApp inclus dans la solution. Cette application utilise le service web de formation de Machine demande-réponse pour les scores en temps réel et publie les données résultantes à un dataset PowerBI destinés à la consommation. 

### <a name="batch-analysis"></a>Analyse en mode batch

Les résultats du traitement par lots et traitement en temps réel sont publiés dans les tables de la base de données de SQL Azure destinés à la consommation. Le SQL Server Azure, base de données et les tables sont créées automatiquement dans le cadre du script d’installation. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*La figure 33 – copie des résultats de flux de travail données mart de traitement par lots*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figure 34 : flux de travail d’analytique publie vers data mart*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figure 35 – Data mart dans la tâche de flux de données analytique*


## <a name="consume"></a>Consommer

Alimentation BI donne à cette solution un tableau de bord riche pour les données en temps réel et les visualisations analytique prédictive. 

Cliquez ici pour obtenir des instructions détaillées sur la configuration de tableau de bord et les rapports de PowerBI. Le tableau de bord final ressemble à ceci :

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Figure 36 - tableau de bord de PowerBI*

## <a name="summary"></a>Résumé

Ce document contient un zoom détaillée de la Solution d’Analytique de télémétrie de véhicule. Ceci présente un modèle d’architecture de lambda pour en temps réel et analytique avec les prévisions et les actions de traitement par lots. Ce modèle s’applique à un grand nombre de cas d’usage qui requièrent chemin réactif (en temps réel) et analytique de chemin à froid (batch). 
