<properties
    pageTitle="Copier une activité guide des performances et optimisation | Microsoft Azure"
    description="Obtenir des informations sur les facteurs essentiels qui affectent les performances de transfert de données dans une usine de données Azure, lorsque vous utilisez l’activité de la copie."
    services="data-factory"
    documentationCenter=""
    authors="linda33wj"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="jingwang"/>


# <a name="copy-activity-performance-and-tuning-guide"></a>Copier une activité guide des performances et optimisation
Activité de copie de fabrique de données Azure offre une solution de chargement des données première classe sécurisées, fiables et hautes performances. Il vous permet de copier des dizaines de téraoctets de données tous les jours sur une grande variété de nuage et des banques de données sur site. Données obtenir éblouissantes performances de chargement sont clé afin de vous concentrer sur le principal problème de « données volumineuses » : création de solutions d’analytique avancée et l’obtention des informations fouillées à partir de toutes les données.

Azure fournit un ensemble d’entreprise solutions d’entrepôt décisionnel stockage et des données, et copie activité offre une expérience qui est facile à configurer et paramétrer de chargement des données hautement optimisées. Avec seulement une activité de copie unique, vous pouvez obtenir :

- Chargement de données dans l' **Entrepôt de données SQL Azure** à **1,2 GBps**
- Chargement des données dans le **stockage des objets Blob Azure** sur **1.0 GBps**
- Chargement des données dans le **Magasin de LAC de données Azure** sur **1.0 GBps**


Cet article décrit :

- Stocke les [numéros de référence des performances](#performance-reference) pour source pris en charge et récepteur de données pour vous aider à planifier votre projet.
- Fonctionnalités qui peuvent améliorer le débit dans différents scénarios, y compris la [copie parallèle](#parallel-copy), [unités de déplacement de données de nuage](#cloud-data-movement-units)et [intermédiaire de copie](#staged-copy);
- [Conseils de réglage de performances](#performance-tuning-steps) sur la manière d’optimiser les performances et les facteurs clés qui peuvent avoir un impact sur les performances de copie.

> [AZURE.NOTE] Si vous n’êtes pas familiarisé avec l’activité de la copie en général, voir [déplacer des données à l’aide de Copy activité](data-factory-data-movement-activities.md) avant la lecture de cet article.

## <a name="performance-reference"></a>Référence de performances

![Tableau de performances](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE] Vous pouvez obtenir un débit plus élevé grâce à plusieurs unités de déplacement de données (DMUs) que celui par défaut DMUs maximales, ce qui est de 8 pour une activité de copie de cloud-nuage exécuter. Par exemple, avec 100 DMUs, vous pouvez copier données d’Azure Blob Azure données lac magasin au taux de 1 gigaoctet par seconde. Reportez-vous à la section [d’unités de déplacement de données de nuage](#cloud-data-movement-units) pour plus d’informations sur cette fonctionnalité. Contacter le [support d’Azure](https://azure.microsoft.com/support/) pour demander plus de DMUs.

Points à noter :

- Le débit est calculé à l’aide de la formule suivante : [lecture de la taille des données à partir de la source] / [activité de copie de durée d’exécution].
- Les numéros de référence des performances de la table ont été mesurées à l’aide du jeu de données [TPC-H](http://www.tpc.org/tpch/) dans une activité de copie unique exécutée.
- Pour copier entre les banques de données de nuage, définissez **cloudDataMovementUnits** à 1 et 4 (ou 8) pour la comparaison. **parallelCopies** n’est pas spécifié. Consultez la section [copie parallèle](#parallel-copy) pour plus d’informations sur ces fonctionnalités.
- Dans les magasins de données Azure, la source et le récepteur sont dans la même région d’Azure.
- Pour les hybrides (local cloud ou nuage local) mouvement des données, une seule instance de passerelle était en cours d’exécution sur un ordinateur qui a été séparé à partir du magasin de données local. La configuration est répertoriée dans le tableau suivant. Lorsqu’une seule activité était en cours d’exécution sur la passerelle, l’opération de copie ne consommée qu’une petite partie du processeur de l’ordinateur de test, la mémoire ou la bande passante du réseau.
    <table>
    <tr>
        <td>UNITÉ CENTRALE</td>
        <td>32 cœurs 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Mémoire</td>
        <td>128 GO</td>
    </tr>
    <tr>
        <td>Réseau</td>
        <td>Interface Internet : 10 Gbits/s ; interface intranet : Gbits/40 s</td>
    </tr>
    </table>

## <a name="parallel-copy"></a>Copie parallèle
Vous pouvez lire les données de la source ou écrire des données sur la destination **en parallèle au sein d’une activité de copie de s’exécuter**. Cette fonctionnalité améliore le débit d’une opération de copie et réduit le temps que nécessaire pour déplacer des données.

Ce paramètre est différent de la propriété de la **concurrence d’accès** dans la définition de l’activité. La propriété de la **concurrence d’accès** détermine le nombre de **Qu'activité de copie simultanée s’exécute** pour traiter les données à partir des fenêtres de différentes activités (1 h à 2 h, 2 h à 3 h, 3 h et 4 h, etc.). Cette fonctionnalité est utile lorsque vous effectuez une charge historique. La fonctionnalité de copie parallèle s’applique à une **seule activité de s’exécutée**.

Examinons un exemple de scénario. Dans l’exemple suivant, plusieurs tranches dans le passé doivent être traités. Usine de données exécute une instance de l’activité de copie (une exécution d’activité) pour chaque section :

- La tranche de données à partir de la première fenêtre d’activité (1 h à 2 heures du matin) == > exécution de l’activité 1
- La tranche de données à partir de la deuxième fenêtre de l’activité (2 heures du matin à 3 h) == > exécutez l’activité 2
- La tranche de données à partir de la deuxième fenêtre de l’activité (03 h 00 à 4 h) == > activité exécuter 3

Et ainsi de suite.

Dans cet exemple, lorsque la valeur de la **concurrence d’accès** est définie à 2, **exécuter du activité 1** et **activité 2** copier des données à partir de deux activités windows **simultanément** pour améliorer les performances de transfert de données. Toutefois, si plusieurs fichiers sont associés aux activités exécutée 1, le service de mouvement de données copie les fichiers à partir de la source dans le fichier de destination une à la fois.

### <a name="parallelcopies"></a>parallelCopies
Vous pouvez utiliser la propriété **parallelCopies** pour indiquer que vous souhaitez que l’activité de copie à utiliser le parallélisme. Vous pouvez considérer cette propriété comme le nombre maximal de threads dans l’activité de copie qui peuvent lire à partir de votre source ou écrire dans vos magasins de données récepteur en parallèle.

Pour chaque activité de copie exécuter, usine de données détermine le nombre de copies parallèles à utiliser pour copier les données de la source de données stockent ainsient les données de destination. Le nombre par défaut de copies parallèles qu’il utilise dépend du type de source et le récepteur que vous utilisez.  

Source et le récepteur |   Nombre de copie parallèle par défaut déterminée par le service
------------- | -------------------------------------------------
Copier des données entre les bases orientées fichier (stockage Blob. Magasin de données lac ; S3 d’Amazon ; un système de fichiers local ; un très sur site) | Entre 1 et 32. Dépend de la taille des fichiers et le nombre d’unités de déplacement de données cloud (DMUs) permet de copier des données entre deux banques de données de nuage ou de la configuration physique de l’ordinateur de la passerelle utilisée pour obtenir une copie hybride (pour copier les données vers ou à partir d’un magasin de données local).
Copier des données de **stockage Azure Table de stocker toutes les données source** | 4
Toutes les autres paires source et le récepteur | 1

En règle générale, le comportement par défaut devrait vous donner le meilleur débit. Toutefois, pour contrôler la charge sur les ordinateurs qui hébergent vos données stocke, ou pour régler les performances de la copie, vous pouvez choisir de substituer la valeur par défaut et spécifier une valeur pour la propriété **parallelCopies** . La valeur doit être comprise entre 1 et 32 (tous deux inclus). Au moment de l’exécution, pour des performances optimales, activité de copie utilise la valeur qui est inférieure ou égale à la valeur que vous définissez.

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "parallelCopies": 8
            }
        }
    ]

Points à noter :

- Lorsque vous copiez des données entre les bases orientées fichier, parallélisme se produit au niveau du fichier. Il n’y a pas de segmentation dans un seul fichier. Le nombre de copies parallèles du service de mouvement de données utilise pour l’opération de copie en cours d’exécution n’est pas plus que le nombre de fichiers que vous avez. Si le comportement de copie est **mergeFile**, activité de copie ne peuvent pas tirer avantage du parallélisme.
- Lorsque vous spécifiez une valeur pour la propriété **parallelCopies** , envisagez l’augmentation de la charge sur vos magasins de données source et le récepteur et à la passerelle s’il s’agit d’une copie hybride. Cela produit particulièrement lorsque vous avez des activités multiples ou exécutions simultanées des mêmes activités qui exécutent la même base de données. Si vous remarquez que le magasin de données ou de la passerelle est surchargé avec la charge, diminuez la valeur de **parallelCopies** pour alléger la charge.
- Lorsque vous copiez des données à partir de magasins qui ne sont pas à base de fichiers pour les magasins qui sont basés sur un fichier, le service de mouvement de données ignore la propriété **parallelCopies** . Même si le parallélisme n’est spécifié, il n’est appliqué dans ce cas.

> [AZURE.NOTE] Vous devez utiliser la passerelle de gestion des données de version 1.11 ou ultérieure pour utiliser la fonctionnalité de **parallelCopies** lorsque vous effectuez une copie de l’hybride.

### <a name="cloud-data-movement-units"></a>Unités de déplacement de données de nuage
Une **unité de déplacement de données cloud (DMU)** est une mesure qui représente la puissance (il s’agit d’une combinaison de l’unité centrale, de mémoire et d’allocation de ressources réseau) d’une seule unité dans une usine de données. Un DMU peut être utilisé dans une opération de copie de cloud-nuage, mais pas dans une copie hybride.

Par défaut, Data Factory utilise un seul nuage DMU pour effectuer une activité de copie unique exécuté. Pour substituer cette valeur par défaut, spécifiez une valeur pour la propriété **cloudDataMovementUnits** comme suit. Pour plus d’informations sur le niveau de gain de performances que vous pouvez obtenir lorsque vous configurez plus d’unités pour une source de la copie spécifique et d’un récepteur, consultez la [référence des performances](#performance-reference).

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "cloudDataMovementUnits": 4
            }
        }
    ]

Les **valeurs autorisées** pour la propriété **cloudDataMovementUnits** sont 1 (par défaut), 2, 4 et 8. Le **nombre réel de nuage DMUs** que l’opération de copie utilise au moment de l’exécution est égale ou inférieure à la valeur configurée, en fonction de votre modèle de données. 

> [AZURE.NOTE] Si vous avez besoin de plus de nuage DMUs pour un débit plus élevé, contactez [l’assistance technique de Azure](https://azure.microsoft.com/support/). Définition de 8 et ci-dessus fonctionne actuellement uniquement lorsque vous copiez plusieurs fichiers depuis le stockage Blob pour le stockage des objets Blob, le magasin de données lac ou de base de données SQL Azure, et la taille du fichier est supérieure ou égale à 16 Mo individuellement.

Pour mieux utiliser ces deux propriétés et afin d’améliorer le débit de transfert de données, consultez l' [exemple de cas d’usage](#case-study-use-parallel-copy). Vous n’avez pas besoin de configurer **parallelCopies** afin de tirer parti du comportement par défaut. Si vous configurez et **parallelCopies** est trop petite, nuage plusieurs DMUs ne peut-être pas entièrement exploité.  

Son **important** de se souvenir que vous sont facturés en fonction du temps total de l’opération de copie. Si un travail de copie permet de prendre une heure avec l’unité d’un nuage et maintenant il prend 15 minutes avec quatre unités de nuage, la facture globale reste presque le même. Par exemple, vous utilisez quatre unités de nuage. La première unité de nuage passe à 10 minutes, le second exemple, 10 minutes, la troisième, 5 minutes et la quatrième, 5 minutes, tous dans une activité de copie exécutés. Vous êtes chargé de l’heure de la copie totale (déplacement des données), qui est de 10 + 10 + 5 + 5 = 30 minutes. À l’aide de **parallelCopies** n’affecte pas la facturation.

## <a name="staged-copy"></a>Copie intermédiaire
Lorsque vous copiez des données à partir d’un magasin de données source à une banque de données du récepteur, vous pouvez choisir d’utiliser le stockage Blob comme magasin de transit temporaire. Mise en attente est particulièrement utile dans les cas suivants :

1.  **Vous souhaitez intégrer des données provenant de différentes banques de données dans l’entrepôt de données SQL via PolyBase**. SQL Data Warehouse utilise PolyBase comme un mécanisme de haut débit pour charger une grande quantité de données dans Data Warehouse de SQL. Toutefois, la source de données doit être dans le stockage Blob, et il doit satisfaire aux critères supplémentaires. Lorsque vous chargez des données à partir d’un magasin de données que le stockage des objets Blob, vous pouvez activer la copie via des intermédiaire stockage intermédiaire de Blob de données. Dans ce cas, Data Factory effectue les transformations de données requis pour vous assurer qu’elle satisfait aux prescriptions du PolyBase. Il utilise ensuite PolyBase pour charger des données dans l’entrepôt de données SQL. Pour plus d’informations, consultez [PolyBase utilisé pour charger les données dans l’entrepôt de données SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
2.  **Parfois il faut du temps pour procéder à un mouvement de données hybride (c'est-à-dire, à copier entre des données non locaux banque et un nuage de données stockent) sur une connexion réseau lente**. Pour améliorer les performances, vous pouvez compresser des données sur site afin qu’il prenne moins de temps à déplacer des données vers le magasin de données de zone de transit dans le nuage. Puis, vous pouvez décompresser les données de la banque intermédiaire avant de le charger dans le magasin de données de destination.
3.  **Vous ne souhaitez pas ouvrir les ports autres que les ports 80 et 443 sur votre pare-feu, en raison des stratégies d’informatique d’entreprise**. Par exemple, lorsque vous copiez des données à partir d’une banque de données locale à un récepteur de base de données de SQL Azure ou un récepteur d’entrepôt de données de SQL Azure, vous devez activer les communications TCP sortantes sur le port 1433 à la fois le pare-feu Windows et le pare-feu de l’entreprise. Dans ce scénario, tirer parti de la passerelle pour la première copie des données à une instance de test du stockage Blob via HTTP ou HTTPS sur le port 443. Ensuite, charger les données de la base de données SQL ou de l’entrepôt de données SQL à partir de la mise en œuvre du stockage Blob. Dans ce flux, vous n’avez pas besoin d’activer le port 1433.

### <a name="how-staged-copy-works"></a>Fonctionnement de la copie intermédiaire comment
Lorsque vous activez la fonctionnalité de zone de transit, tout d’abord les données sont copiées à partir du magasin de données source dans le magasin de données intermédiaire (mettre votre propre). Ensuite, les données sont copiées à partir du magasin de données intermédiaire dans le magasin de données du récepteur. Usine de données gère automatiquement le flux de deux étapes pour vous. Usine de données nettoie également les données temporaires du stockage de zone de transit après que le déplacement des données est terminé.

Dans le scénario de copie du nuage (données source et le récepteur sont des magasins dans le nuage), la passerelle n’est pas utilisée. Le service de fabrique de données effectue les opérations de copie.

![Mis en place de la copie : scénario de nuage](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

Dans le scénario de copie hybride (source est local et récepteur dans le nuage), la passerelle déplace les données à partir du magasin de données source vers un magasin de données de zone de transit. Service Factory de données déplace les données du magasin de données de zone de transit vers le magasin de données du récepteur. Copie de données à partir d’un magasin de données de nuage pour un magasin de données local via la mise en œuvre également est pris en charge avec le flux inversé.

![Mis en place de la copie : scénario hybride](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Lorsque vous activez le transfert de données à l’aide d’une banque intermédiaire, vous pouvez spécifier si vous souhaitez que les données compressées avant de déplacer des données à partir du magasin de données source à une banque de données intermédiaires ou de zone de transit, et puis décompressées avant de déplacer des données à partir d’une version préliminaire ou mis en œuvre du magasin de données pour le magasin de données du récepteur.

Actuellement, vous ne peut pas copier des données entre deux banques de données sur site à l’aide d’une banque intermédiaire. Nous pensons que cette option soit disponible bientôt.

### <a name="configuration"></a>Configuration de
Configurez le paramètre de **enableStaging** dans l’activité de copie permet de spécifier si vous souhaitez que les données à tester dans le stockage Blob avant de le charger dans une banque de données de destination. Lorsque vous définissez **enableStaging** sur TRUE, spécifiez les propriétés supplémentaires répertoriées dans le tableau suivant. Si vous n’en avez pas, vous devez également créer un stockage Azure ou stockage partagé service lié à la signature d’accès pour la mise en attente.

Propriété | Description | Valeur par défaut | Obligatoire
--------- | ----------- | ------------ | --------
**enableStaging** | Spécifiez si vous souhaitez copier des données via une intérimaire magasin de la zone de transit. | False | N°
**linkedServiceName** | Spécifiez le nom d’un [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [d’AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) services liés, qui fait référence à l’instance de stockage que vous utilisez comme magasin de transit temporaire. <br/><br/> Vous ne pouvez pas utiliser le stockage avec une signature d’accès partagé pour charger les données dans l’entrepôt de données SQL via PolyBase. Vous pouvez l’utiliser dans tous les autres scénarios. | N/A | Oui, lorsque **enableStaging** a la valeur TRUE
**chemin d’accès** | Spécifiez le chemin d’accès du stockage Blob qui contiendra les données nouvellement créées. Si vous ne fournissez pas un chemin d’accès, le service crée un conteneur pour stocker des données temporaires. <br/><br/> Spécifiez un chemin d’accès que si vous utilisez le stockage avec une signature d’accès partagé, ou vous avez besoin de données temporaires dans un emplacement spécifique. | N/A | N°
**enableCompression** | Spécifie si les données doivent être compressées avant d’être copiée vers la destination. Ce paramètre permet de réduire le volume de données transférées. | False | N°

Voici un exemple de définition de l’activité de la copie avec les propriétés qui sont décrites dans le tableau précédent :

    "activities":[  
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [{ "name": "OnpremisesSQLServerInput" }],
        "outputs": [{ "name": "AzureSQLDBOutput" }],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob",
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
    ]


### <a name="billing-impact"></a>Impact de la facturation
Vous sont facturés en fonction de deux étapes : durée de copier et de copier un type. 

- Lorsque vous utilisez la zone de transit lors d’une copie de nuage (copie de données à partir d’un magasin de données de nuage vers une autre banque de données de nuage), vous sont facturés [somme de la durée de copie pour les étapes 1 et 2] x [prix d’unité copie de nuage].
- Lorsque vous utilisez la zone de transit lors d’une copie hybride (copie des données à partir d’un magasin de données local vers un magasin de données de nuage), vous sont facturés [durée de copie hybride] x [prix d’unité hybride copie] + [cloud copie durée] x [prix d’unité copie de nuage].


## <a name="performance-tuning-steps"></a>Étapes de réglage de performances
Nous recommandons d’effectuer les étapes suivantes pour optimiser les performances de votre service Factory de données avec l’activité de copie :

1.  **Établir une planification initiale**. Pendant la phase de développement, testez votre pipeline à l’aide d’activité de la copie par rapport à un échantillon de données représentatives. Vous pouvez utiliser le Factory de données [modèle de séparation](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) pour limiter la quantité de données que vous manipulez.

    Collecter les temps d’exécution et les caractéristiques de performances à l’aide de l' **analyse et gestion App**. Choisissez **de moniteurs et de gérer** sur votre page d’accueil de Data Factory. Dans l’arborescence, sélectionnez le **dataset de sortie**. Dans la liste **Activité** , cliquez sur l’activité de copie de s’exécuter. **Activité Windows** répertorie la durée d’activité de copie et de la taille des données qui sont copiées. Le débit est répertorié dans **l’Explorateur de Windows d’activité**. Pour en savoir plus sur l’application, reportez-vous à la section [moniteur et de gérer les pipelines Azure Data Factory à l’aide de l’analyse et l’application de gestion des](data-factory-monitor-manage-app.md).

    ![Détails de l’exécution des activités](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

    Plus loin dans cet article, vous pouvez comparer les performances et la configuration de votre scénario à l' d’activité de copie [référence des performances](#performance-reference) de nos tests.
2. **Diagnostiquer et optimiser les performances de**. Si les performances que vous observez ne répondent pas à vos attentes, vous devez identifier les goulots d’étranglement de performances. Ensuite, optimiser les performances pour supprimer ou réduire les effets des goulets d’étranglement. Une description complète des diagnostics de performances est au-delà de la portée de cet article, mais voici quelques considérations courantes :
    - Fonctionnalités de performance :
        - [Copie parallèle](#parallel-copy)
        - [Unités de déplacement de données de nuage](#cloud-data-movement-units)
        - [Copie intermédiaire](#staged-copy)   
    - [Source](#considerations-for-the-source)
    - [Récepteur](#considerations-for-the-sink)
    - [Sérialisation et la désérialisation](#considerations-for-serialization-and-deserialization)
    - [Compression](#considerations-for-compression)
    - [Mappage de colonnes](#considerations-for-column-mapping)
    - [Passerelle de gestion des données](#considerations-for-data-management-gateway)
    - [Autres considérations](#other-considerations)

3. **Développer la configuration à votre ensemble de données**. Lorsque vous êtes satisfait des résultats de l’exécution et aux performances, vous pouvez développer la définition et la période active de pipeline pour couvrir l’ensemble de vos données.

## <a name="considerations-for-the-source"></a>Considérations pour la source
### <a name="general"></a>Général
Veillez à ce que le magasin de données sous-jacent n’est pas saturé par les autres charges de travail qui sont en cours d’exécution sur ou contre elle. 

Pour les banques de données de Microsoft, reportez-vous à la section [analyse et réglage des rubriques](#performance-reference) spécifiques aux magasins de données et vous aident à que comprendre les caractéristiques de performance de la banque de données, minimiser les temps de réponse et maximiser le débit.

Si vous copiez les données depuis le stockage Blob SQL Data Warehouse, envisagez d’utiliser **PolyBase** pour améliorer les performances. Pour plus d’informations, reportez-vous à la section [Utilisation des PolyBase pour charger les données dans l’entrepôt de données SQL Azure](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) .


### <a name="file-based-data-stores"></a>Banques de données basée sur un fichier
*(Inclut le stockage Blob, banque de données lac, Amazon S3, les systèmes de fichiers locaux et sur site très)*

- **Taille de fichier moyenne et le nombre de fichiers**: activité de copie transfère un fichier de données à la fois. Avec la même quantité de données à déplacer, le débit global est inférieur si les données sont composées de nombreux petits fichiers plutôt que de quelques fichiers volumineux en raison de la phase d’amorçage pour chaque fichier. Par conséquent, si possible, combiner plusieurs petits fichiers dans des fichiers de grande taille pour obtenir un débit plus élevé.
- **Compression et format de fichier**: d’autres façons d’améliorer les performances, consultez les sections de [Considérations relatives à la sérialisation et la désérialisation](#considerations-for-serialization-and-deserialization) et [Considérations pour la compression](#considerations-for-compression) .
- Pour le scénario de **système de fichiers local** , dans lequel la **Passerelle de gestion des données** est requise, reportez-vous à la section [Considérations relatives à la passerelle de gestion des données](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Magasins de données relationnels
*(Inclut la base de données SQL ; SQL Data Warehouse ; Redshift d’Amazon ; Bases de données SQL Server ; et Oracle, bases de données MySQL, DB2, Teradata, Sybase et PostgreSQL, etc.).*

- **Modèle de données**: le schéma de votre table affecte le débit. Une taille importante vous donne de meilleures performances de taille petite ligne, copier la même quantité de données. La raison est que la base de données peut extraire plus efficacement les lots de moins de données qui contiennent moins de lignes.
- **Requête ou procédure stockée**: optimiser la logique de la requête ou la procédure stockée que vous spécifiez dans la source de l’activité de copie pour récupérer les données plus efficacement.
- Pour les **bases de données relationnelles locales**, telles que SQL Server et Oracle, qui exige l’utilisation de la **Passerelle de gestion des données**, reportez-vous à la section [Considérations relatives à la passerelle de gestion des données](#considerations-on-data-management-gateway) .

## <a name="considerations-for-the-sink"></a>Considérations pour le récepteur

### <a name="general"></a>Général
Veillez à ce que le magasin de données sous-jacent n’est pas saturé par les autres charges de travail qui sont en cours d’exécution sur ou contre elle. 

Pour les banques de données de Microsoft, reportez-vous à [surveillance et le réglage des rubriques](#performance-reference) spécifiques aux magasins de données. Ces rubriques peuvent vous aider à comprendre les caractéristiques de performances de banque de données et comment les minimiser les temps de réponse et d’optimiser le débit.

Si vous copiez des données depuis le **stockage Blob** **SQL Data**Warehouse, envisagez d’utiliser **PolyBase** pour améliorer les performances. Pour plus d’informations, reportez-vous à la section [Utilisation des PolyBase pour charger les données dans l’entrepôt de données SQL Azure](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) .


### <a name="file-based-data-stores"></a>Banques de données basée sur un fichier
*(Inclut le stockage Blob, banque de données lac, Amazon S3, les systèmes de fichiers locaux et sur site très)*

- **Problème de copie**: Si vous copiez des données d’un magasin de données de fichiers différents, activité de copie a trois options via la propriété **copyBehavior** . Il préserve la hiérarchie, aplatit la hiérarchie ou fusionne des fichiers. Mise à plat de la hiérarchie soit en conservant a peu ou aucune baisse des performances, mais la fusion de fichiers entraîne une surcharge augmenter.
- **Compression et format de fichier**: voir les sections [Considérations pour la compression](#considerations-for-compression) et les [Considérations relatives à la sérialisation et la désérialisation](#considerations-for-serialization-and-deserialization) d’autres façons d’améliorer les performances.
- **Stockage des objets BLOB**: actuellement, prise en charge du stockage Blob ne bloque BLOB pour transfert de données optimisées et le débit.
- Pour les scénarios **sur site des systèmes de fichiers** qui requièrent l’utilisation de la **Passerelle de gestion des données**, reportez-vous à la section [Considérations relatives à la passerelle de gestion des données](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Magasins de données relationnels
*(Inclut les bases de données Oracle, bases de données SQL Server, de la base de données SQL et SQL Data Warehouse)*

- **Copier le comportement**: en fonction des propriétés que vous avez définie pour **sqlSink**, activité de copie écrit des données dans la base de données de destination de différentes manières.
    - Par défaut, le mouvement de données service utilise l’API de copie en bloc pour insérer des données dans ajoute mode, qui offre les meilleures performances.
    - Si vous configurez une procédure stockée dans le récepteur, la base de données applique le données ligne par ligne à la fois au lieu de sous la forme d’un chargement en bloc. Performances diminuent de manière significative. Si votre jeu de données est volumineux, le cas échéant, pensez à passer à l’utilisation de la propriété **sqlWriterCleanupScript** .
    - Si vous configurez la propriété **sqlWriterCleanupScript** pour chaque activité de copie de s’exécuter, le service déclenche le script, puis vous utilisez l’API de copie en bloc pour insérer les données. Par exemple, pour remplacer l’intégralité du tableau avec les données les plus récentes, vous pouvez spécifier un script pour tout d’abord supprimer tous les enregistrements avant des nouvelles données de la source de chargement en bloc.
- **Taille de modèle et de la feuille de données**:
    - Votre schéma de table affecte le débit. Pour copier la même quantité de données, une taille importante vous offre performances supérieures à celles d’une taille de petite ligne parce que la base de données peut valider plus efficacement les lots de moins de données.
    - Activité de copie insère des données dans une série de lots. Vous pouvez définir le nombre de lignes dans un lot à l’aide de la propriété **writeBatchSize** . Si vos données contiennent des petites lignes, vous pouvez définir la propriété **writeBatchSize** avec une valeur plus élevée pour bénéficier d’un débit plus élevé et de réduire les frais par lots. Si la taille de la ligne de vos données est importante, soyez prudent lorsque vous augmentez la **writeBatchSize**. Une valeur élevée peut entraîner une défaillance de copie due à la surcharge de la base de données.
- Pour les **bases de données relationnelles locales** , telles que SQL Server et Oracle, qui nécessitent l’utilisation de la **Passerelle de gestion des données**, reportez-vous à la section [Considérations relatives à la passerelle de gestion des données](#considerations-for-data-management-gateway) .


### <a name="nosql-stores"></a>Magasins de NoSQL
*(Inclut le stockage de Table et de DocumentDB d’Azure)*

- Pour le **stockage de Table**:
    - **Partition**: écriture de données dans les partitions entrelacées considérablement dégrade les performances. Trier vos données source par clé de partition afin que les données sont insérées efficacement dans une partition après l’autre, ou ajuster la logique pour écrire les données à une partition unique.
- Pour **DocumentDB**:
    - **Taille de lot**: la propriété **writeBatchSize** définit le nombre de requêtes parallèles au service DocumentDB pour créer des documents. Vous pouvez vous attendre à améliorer les performances lorsque vous augmentez **writeBatchSize** parce que plus de requêtes parallèles sont envoyés à DocumentDB. Toutefois, surveiller la limitation lorsque vous écrivez à DocumentDB (le message d’erreur est « Demande de taux est grande »). Divers facteurs peuvent entraîner la limitation, y compris la taille du document, le nombre de termes dans les documents et stratégie d’indexation de la collection de la cible. Pour obtenir un débit plus élevé, envisagez d’utiliser une collection mieux, par exemple, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considérations relatives à la sérialisation et la désérialisation
Sérialisation et désérialisation peuvent se produire lorsque votre jeu de données d’entrée ou un jeu de données de sortie est un fichier. Actuellement, les activités de copie prend en charge Avro et le texte (par exemple, CSV et TSV) des formats de données.

**Copier le comportement**:

-   Copie des fichiers entre les banques de données de fichiers :
    - Lorsque d’entrée et de sortie des ensembles de données les deux ont le même ou aucun paramètre de format de fichier, le service de transfert de données exécute une copie binaire sans sérialisation ou la désérialisation. Vous consultez un débit plus élevé par rapport au scénario, dans lequel les paramètres de format de fichier source et le récepteur sont différents entre eux.
    - Lorsque l’entrée et les deux ensembles de données sortie sont au format texte et seulement le codage de type est différent, le service de mouvement de données seulement exécute la conversion de codage. Il ne fait pas la sérialisation et la désérialisation, ce qui entraîne des performances temps système par rapport à une copie binaire.
    - Lorsque d’entrée et de sortie des ensembles de données à la fois avoir différents formats de fichiers ou des configurations différentes, comme délimiteurs, le service de mouvement de données désérialise les données source pour les flux de données, transformer et ensuite de le sérialiser dans le format de sortie que vous avez indiqué. Cette opération se traduit par un temps système par rapport à d’autres scénarios de performances beaucoup plus importante.
- Lorsque vous copiez des fichiers vers/à partir d’un magasin de données qui n’est pas basé sur un fichier (par exemple, à partir d’une banque basée sur un fichier vers un magasin relationnel), l’étape de la sérialisation ou la désérialisation est requise. Cette étape entraîne une surcharge de performance significatif.

**Format de fichier**: le format de fichier que vous choisissez peut affecter les performances de la copie. Par exemple, Avro est un format binaire compact qui stocke des métadonnées avec des données. Qu’il a prise en charge étendue de l’écosystème Hadoop pour le traitement et l’interrogation. Cependant, Avro est plus coûteuse pour la sérialisation et la désérialisation, ce qui se traduit par un débit inférieur par rapport au format texte. Faites votre choix de format de fichier dans le flux de traitement de façon holistique. Démarrer avec les données de forme est stockée dans des magasins de données source ou à extraire à partir de systèmes externes ; le format le plus approprié pour le stockage, traitement analytique et l’interrogation ; et dans quel format les données doivent être exportées dans les data marts pour des outils de reporting et de visualisation. Parfois un format de fichier non optimaux pour lire et performances en écriture peut être un bon choix lorsque vous considérez le processus global d’analyse.

## <a name="considerations-for-compression"></a>Considérations pour la compression
Lorsque votre jeu de données d’entrée ou de sortie est un fichier, vous pouvez définir les activités de copie pour effectuer la compression ou la décompression lorsqu’il écrit des données vers la destination. Lorsque vous choisissez la compression, vous faire un compromis entre d’entrée/sortie (e/s) et le processeur. Compresser les coûts de données supplémentaires dans les ressources de calcul. Mais, en retour, il réduit les e/s réseau et du stockage. Selon vos données, vous pouvez constater une augmentation du débit de copie globale.

**Codec**: activité de copie prend en charge les types de compression Deflate, bzip2 et gzip. HDInsight Azure peut utiliser les trois types de traitement. Chaque codec de compression a des avantages. Par exemple, bzip2 est le débit le plus faible, mais vous obtenez les meilleures performances de requête de ruche avec bzip2 car vous pouvez le fractionner pour traitement. Gzip est l’option la plus équilibrée, et il est utilisé le plus souvent. Choisissez le codec qui correspond le mieux à votre scénario de bout en bout.

**Niveau**: vous avez le choix entre deux options pour chaque codec de compression : compressé plus rapide et optimale compressé. L’option la plus rapide compressée compresse les données aussi rapidement que possible, même si le fichier résultant n’est pas compressé de manière optimale. L’option optimale compressée consacre plus de temps sur la compression et donne une quantité minimale de données. Vous pouvez tester ces deux options pour voir qui offre de meilleures performances globales dans votre cas.

**Une considération**: pour copier une grande quantité de données entre un magasin local et le nuage, envisagez d’utiliser stockage blob intermédiaire grâce à la compression. Stockage provisoire peut s’avérer utile lorsque la bande passante de votre réseau d’entreprise et de vos services Azure est le facteur de limitation et vous souhaitez que le jeu de données d’entrée et sortie groupe de données à la fois sous forme non compressée. Plus spécifiquement, vous pouvez annuler une activité de copie unique dans les activités de copie deux. La première activité de copie copie à partir de la source pour un blob temporaire ou de transit sous forme compressée. La seconde activité de copie copie les données compressées en transit et puis le décompresse pendant qu’il écrit sur le récepteur.

## <a name="considerations-for-column-mapping"></a>Considérations pour le mappage de colonnes
Vous pouvez définir la propriété **columnMappings** dans l’activité de copie à tous de la carte ou un sous-ensemble de colonnes d’entrée pour les colonnes de sortie. Une fois que le service de mouvement de données lit les données à partir de la source, il doit effectuer un mappage de colonne sur les données avant qu’il écrit les données dans le récepteur. Ce traitement supplémentaire réduit le débit de la copie.

Si le magasin de votre source de données est requêtable, par exemple, s’il est un magasin relationnel de la base de données SQL ou de SQL Server, ou s’il s’agit d’un magasin de NoSQL comme stockage de Table ou de la DocumentDB, pensez à pousser le filtrage de colonne et de réorganisation de logique à la propriété de **requête** au lieu d’utiliser le mappage de colonnes. De cette façon, la projection se produit alors que le service de mouvement de données lit des données à partir du magasin de données source, où il est beaucoup plus efficace.

## <a name="considerations-for-data-management-gateway"></a>Considérations relatives à la passerelle de gestion des données
Pour obtenir des recommandations d’installation Gateway, consultez [Considérations sur l’utilisation de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Environnement de passerelle machine**: nous vous conseillons d’utiliser un ordinateur dédié pour un hôte passerelle de gestion des données. Utilisez les outils tels que PerfMon pour examiner l’utilisation du processeur, la mémoire et la bande passante au cours d’une opération de copie sur votre ordinateur de la passerelle. Basculer vers un ordinateur plus puissant processeur, la mémoire ou la bande passante réseau devient un goulet d’étranglement.

**Activité simultanée de copie s’exécute**: une instance unique de la passerelle de gestion des données peut prendre en charge plusieurs activités de copie s’exécute en même temps, ou en même temps. Le nombre maximal de tâches simultanées est calculé en fonction de la configuration matérielle de l’ordinateur de la passerelle. Tâches supplémentaires sont mises en attente jusqu'à ce qu’ils sont pris en charge par la passerelle ou jusqu'à ce qu’une autre tâche arrive à expiration. Pour éviter les conflits de ressources sur l’ordinateur de la passerelle, vous pouvez organiser votre planification d’activité de copie afin de réduire le nombre de tâches dans la file d’attente à la fois, ou pensez à fractionner la charge sur plusieurs ordinateurs de passerelle.


## <a name="other-considerations"></a>Autres considérations
Si la taille des données que vous souhaitez copier est importante, vous pouvez ajuster votre logique métier pour partitionner les données en utilisant le mécanisme de découpage dans une usine de données. Planifiez ensuite un activité de copie pour exécuter plus fréquemment afin de réduire la taille des données pour chaque activité de copie de s’exécuter.

Soyez prudent sur le nombre d’ensembles de données et les activités de copie nécessitant des données usine au connecteur à la même base de données en même temps. De nombreuses tâches simultanées peuvent limiter un magasin de données et entraîner la dégradation des performances, copie de travail interne tentatives et dans certains cas, les erreurs d’exécution.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exemple de scénario : copie à partir d’une de SQL Server sur site pour le stockage des objets Blob
**Scénario**: un pipeline est construit pour copier des données à partir d’une de SQL Server sur site pour le stockage des objets Blob au format CSV. Pour accélérer le travail de copie, les fichiers CSV doivent être compressées au format de bzip2.

**Test et analyse**: le débit de l’activité de copie est inférieure à 2 Mbits/s, qui est beaucoup plus lent que le banc d’essai de performances.

**Analyse des performances et réglage**: pour résoudre le problème de performances, nous allons voir comment les données sont traitées et déplacées.

1.  **Lecture de données**: passerelle ouvre une connexion à SQL Server et envoie la requête. SQL Server répond en envoyant le flux de données à passerelle via l’intranet.
2.  **Serialize et compression des données**: passerelle sérialise le flux de données au format CSV et compresse les données dans un flux bzip2.
3.  **Écrire les données**: passerelle télécharge le flux bzip2 pour le stockage des objets Blob via Internet.

Comme vous pouvez le voir, les données sont traitées et déplacé de manière séquentielle en continu : SQL Server > LAN > passerelle > WAN > stockage Blob. **Les performances globales sont contrôlées par le débit minimal dans le pipeline**.

![Flux de données](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Un ou plusieurs des facteurs suivants peuvent provoquer le goulot d’étranglement de performances :

-   **Source**: SQL Server lui-même a faible débit en raison des charges lourdes.
-   **Passerelle de gestion des données**:
    -   **LAN**: passerelle se trouve loin de l’ordinateur SQL Server et qu’il dispose d’une connexion à faible bande passante.
    -   **Gateway**: passerelle a atteint ses limites de charge pour effectuer les opérations suivantes :
        -   **Sérialisation**: sérialisation du flux de données au format CSV a débit lent.
        -   **Compression**: vous avez choisi un codec de compression lente (par exemple, bzip2, c'est-à-dire Mbits/2,8 s avec Core i7).
    -   **WAN**: la bande passante entre le réseau d’entreprise et de vos services Azure est faible (par exemple, T1 = Kbits/s 1,544 ; T2 = 6,312 de Kbits/s).
-   **Récepteur**: stockage des objets Blob a faible débit. (Ce scénario est peu probable car son contrat SLA garantit un minimum de 60 MBps).

Dans ce cas, la compression de données bzip2 peut être ralentir le pipeline entier. Ce goulot d’étranglement peut faciliter la de commutation pour un codec de compression gzip.


## <a name="sample-scenarios-use-parallel-copy"></a>Exemples de scénarios : utiliser copie parallèle  

**Scénario i :** Copier les 1 000 fichiers de 1 Mo à partir du système de fichiers local pour le stockage des objets Blob.

**Analyse et optimisation des performances**: pour obtenir un exemple, si vous avez installé la passerelle sur un ordinateur à quatre cœurs, Data Factory utilise 16 copies parallèles pour déplacer des fichiers du système de fichiers pour le stockage des objets Blob simultanément. Cette exécution en parallèle devrait se traduire par un débit élevé. Également, vous pouvez spécifier explicitement le nombre de copies parallèle. Lorsque vous copiez plusieurs petits fichiers, copie parallèle aide considérablement le débit en utilisant les ressources plus efficacement.

![Scénario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scénario II**: copier 20 BLOB de 500 Mo depuis le stockage Blob de données lac banque Analytique et ensuite régler les performances.

**Analyse et optimisation des performances**: dans ce scénario, Data Factory copie les données depuis le stockage Blob magasin lac de données à l’aide de seul exemplaire (**parallelCopies** la valeur 1) et unités de déplacement de données de simple-nuage. Le débit que vous observez sera proche de celle décrite dans la [section de référence de performances](#performance-reference).   

![Scénario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scénario III**: taille de fichier est supérieure à des dizaines de mégaoctets et le volume total est important.

**Analyse et activation des performances**: augmentation de la **parallelCopies** ne produit pas de meilleures performances de copie en raison des limitations de ressource d’une DMU unique-nuage. Au lieu de cela, vous devez spécifier plus de nuage DMUs pour obtenir plus de ressources pour effectuer le déplacement des données. Ne spécifiez pas de valeur pour la propriété **parallelCopies** . Usine de données gère le parallélisme pour vous. Dans ce cas, si vous définissez **cloudDataMovementUnits** à 4, un débit d’environ quatre fois se produit.

![Scénario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Référence
Vous trouverez ici les performances d’analyse et le réglage de références pour certaines banques de données pris en charge :

- Stockage Azure (y compris le stockage des objets Blob et le stockage de Table) : [cibles de stockage Azure l’évolutivité](../storage/storage-scalability-targets.md) et [les performances de stockage Azure et liste de contrôle de l’évolutivité](../storage//storage-performance-checklist.md)
- Base de données SQL Azure : Vous pouvez [Analyseur de performances](../sql-database/sql-database-service-tiers.md#monitoring-performance) et de vérifier le pourcentage d’unité (DTU) de transaction de base de données
- Magasin de données SQL Azure : Sa capacité est mesurée en unités de magasin de données (DWUs) ; reportez-vous à la section [Gérer compute d’alimentation dans le magasin de données SQL Azure (présentation)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
- DocumentDB Azure : [des niveaux de Performance dans DocumentDB](../documentdb/documentdb-performance-levels.md)
- De SQL Server sur site : la [surveillance et le réglage des performances](https://msdn.microsoft.com/library/ms189081.aspx)
- Serveur de fichiers local : [réglage des performances pour les serveurs de fichiers](https://msdn.microsoft.com/library/dn567661.aspx)
