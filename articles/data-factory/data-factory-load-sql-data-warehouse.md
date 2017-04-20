<properties 
    pageTitle="Charger des téraoctets de données dans Data Warehouse de SQL | Microsoft Azure" 
    description="Montre comment 1 To de données peuvent être chargée dans l’entrepôt de données SQL Azure moins de 15 minutes avec Azure Data Factory" 
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
    ms.date="10/28/2016" 
    ms.author="jingwang"/>

# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-azure-data-factory-copy-wizard"></a>Charge de 1 To dans le magasin de données SQL Azure sous 15 minutes avec Azure Data Factory [Assistant copie]
[Entrepôt de données SQL Azure](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) est une nuage cloisonnée de base de données capable de traiter de très gros volumes de données, relationnelles et non relationnelles.  Bâti sur une architecture de traitement massivement parallèle (MPP), SQL Data Warehouse est optimisé pour les charges de travail entrepôt de données entreprise.  Il offre l’élasticité nuage grâce à la flexibilité à l’échelle de stockage et de calculer indépendamment.

Mise en route avec le magasin de données SQL Azure est désormais plus facile que jamais à l’aide de la **Fabrique de données Azure**.  Azure Data Factory est un service d’intégration de données de nuage entièrement géré, qui peut être utilisé pour remplir un entrepôt de données SQL avec les données de votre système existant et de gagner un temps précieux lors de l’évaluation SQL Data Warehouse et la création de vos solutions d’analytique dessus.  Voici les principaux avantages de chargement de données dans l’entrepôt de données de SQL Azure à l’aide de la fabrique de données Azure :

- **Facile à configurer**: Assistant intuitif de 5-étape sans écriture de script requis.
- **Prise en charge du magasin de données riches**: prise en charge intégrée d’un vaste ensemble de locaux et de magasins de données basées sur le nuage.
- **Sécurisé et conforme**: les données sont transférées via HTTPS ou ExpressRoute, et présence de service global garantit que vos données ne quittent jamais la limite géographique
- **Des performances inégalées à l’aide de PolyBase** : à l’aide de Polybase est la manière la plus efficace pour transférer des données dans l’entrepôt de données SQL Azure. À l’aide de la fonctionnalité de blob intermédiaire, vous pouvez obtenir des vitesses de charge élevée de tous les types de magasins de données en dehors du stockage Azure Blob, qui prend en charge de la Polybase par défaut.

Cet article vous montre comment utiliser l’Assistant de copie de données en usine pour charger des données de 1 To de stockage des objets Blob Azure dans l’entrepôt de données SQL Azure sous 15 minutes, à 1.2 sur GBps débit.

Cet article fournit des instructions détaillées pour le déplacement de données dans l’entrepôt de données de SQL Azure en utilisant l’Assistant de copie. 

> [AZURE.NOTE] Article de [déplacer des données vers et à partir de l’entrepôt de données de SQL Azure à l’aide de la fabrique de données Azure](data-factory-azure-sql-data-warehouse-connector.md) voir pour des informations générales sur les fonctionnalités de Data Factory dans le déplacement des données vers/à partir de l’entrepôt de données SQL Azure. 
> 
> Vous pouvez également créer des pipelines à l’aide d’Azure portal, Visual Studio, PowerShell, etc.. Consultez [didacticiel : copier des données d’Azure Blob dans la base de données de SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour une procédure pas à pas rapide avec des instructions pour l’utilisation de l’activité de copie dans Azure Data Factory.  

## <a name="prerequisites"></a>Conditions préalables
- Stockage de Blob Azure : cette expérience utilise stockage de Blob Azure (GRS) pour le stockage du groupe de données test TPC-H.  Si vous ne disposez pas d’un compte de stockage Azure, apprenez [à créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account).
- Données [TPC-H](http://www.tpc.org/tpch/) : nous allons utiliser le TPC-H comme le groupe de données de test.  Pour ce faire, vous devez utiliser `dbgen` à partir de la boîte à outils de TPC-H, qui vous permet de générer le groupe de données.  Vous pouvez soit télécharger le code source de `dbgen` à partir des [Outils de TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) et compilez-le vous-même, ou téléchargez le fichier binaire compilé à partir de [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Exécutez dbgen.exe avec les commandes suivantes pour générer un fichier plat de 1 To pour `lineitem` table de propagation sur 10 fichiers :
    - `Dbgen -s 1000 -S **1** -C 10 -T L -v`
    - `Dbgen -s 1000 -S **2** -C 10 -T L -v`
    - …
    - `Dbgen -s 1000 -S **10** -C 10 -T L -v` 

    Maintenant, copiez les fichiers générés pour les Blob Azure.  Reportez-vous au [déplacement des données vers et à partir d’un système de fichiers local à l’aide d’Azure Data Factory](data-factory-onprem-file-system-connector.md) pour la procédure à suivre à l’aide de la copie du chargeur automatique de documents.   
- SQL Azure Data Warehouse : cette expérience charge les données dans l’entrepôt de données SQL Azure avec 6 000 DWUs

    Pour des instructions détaillées sur la création d’une base de données SQL Data Warehouse, consultez [créer un entrepôt de données SQL Azure](../sql-data-warehouse/sql-data-warehouse-get-started-provision/) .  Pour obtenir les meilleures performances de charge possibles dans l’entrepôt de données SQL à l’aide de Polybase, nous choisissons un nombre maximal d’unités de magasin de données (DWUs) autorisés dans le paramètre de Performance, ce qui est de 6 000 DWUs.

    > [AZURE.NOTE] 
    > Lors du chargement à partir d’Azure Blob, les données de performances de chargement sont directement proportionnelles au nombre de DWUs vous configurez sur le Data Warehouse de SQL :
    > 
    > Chargement de 1 To dans 1 000 DWU SQL Data Warehouse prend 87min (~ 200MBps débit) du chargement de 1 To à 2 000 DWU SQL Data Warehouse prend 46min. (~ 380MBps débit) du chargement de 1 To dans 6 000 DWU SQL Data Warehouse prend 14min (débit de ~1.2GBps) 

    Pour créer un entrepôt de données SQL avec 6 000 DWUs, déplacez le curseur de performances la plus à droite :

    ![Curseur des performances](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Pour une base de données existante qui n’est pas configuré avec 6 000 DWUs, mettre à l’échelle à l’aide d’Azure portal.  Accédez à la base de données Azure portal, et il existe un bouton de **l’échelle** dans le panneau de **vue d’ensemble** illustré dans l’image suivante :

    ![Bouton d’échelle](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Cliquez sur le bouton pour ouvrir le panneau suivant, **échelle** de déplacer le curseur à la valeur maximale et cliquez sur le bouton **Enregistrer** .

    ![Boîte de dialogue échelle](media/data-factory-load-sql-data-warehouse/scale-dialog.png)
    
    Charge les données dans l’entrepôt de données SQL Azure utilise cette expérience `xlargerc` classe de ressource.

    Pour obtenir les meilleurs résultats possibles, copie doit être effectuée à l’aide d’un utilisateur SQL Data Warehouse appartenant à `xlargerc` classe de ressource.  Découvrez comment le faire en suivant [un exemple de classe de ressource utilisateur de modification](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).  

- Créer le schéma de table de destination dans la base de données de l’entrepôt de données de SQL Azure, en exécutant l’instruction DDL suivante :

        CREATE TABLE [dbo].[lineitem]
        (
            [L_ORDERKEY] [bigint] NOT NULL,
            [L_PARTKEY] [bigint] NOT NULL,
            [L_SUPPKEY] [bigint] NOT NULL,
            [L_LINENUMBER] [int] NOT NULL,
            [L_QUANTITY] [decimal](15, 2) NULL,
            [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
            [L_DISCOUNT] [decimal](15, 2) NULL,
            [L_TAX] [decimal](15, 2) NULL,
            [L_RETURNFLAG] [char](1) NULL,
            [L_LINESTATUS] [char](1) NULL,
            [L_SHIPDATE] [date] NULL,
            [L_COMMITDATE] [date] NULL,
            [L_RECEIPTDATE] [date] NULL,
            [L_SHIPINSTRUCT] [char](25) NULL,
            [L_SHIPMODE] [char](10) NULL,
            [L_COMMENT] [varchar](44) NULL
        )
        WITH
        (
            DISTRIBUTION = ROUND_ROBIN,
            CLUSTERED COLUMNSTORE INDEX
        )

Avec les étapes préliminaires terminées, nous sommes maintenant prêts à configurer l’activité de copie à l’aide de l’Assistant copie.

## <a name="launch-copy-wizard"></a>Lancer l’Assistant de copie

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Cliquez sur **+ Nouveau** à partir du coin supérieur gauche et cliquez sur **Intelligence + analytique**, cliquez sur le **Factory de données**. 
6. Dans la **nouvelle fabrique de données** blade :
    1. Entrez **LoadIntoSQLDWDataFactory** pour le **nom**.
        Le nom de la fabrique de données Azure doit être globalement unique. Si vous recevez l’erreur : **nom d’usine de données « LoadIntoSQLDWDataFactory » n’est pas disponible**, essayez à nouveau de créer et de modifier le nom de la fabrique de données (par exemple, yournameLoadIntoSQLDWDataFactory). Voir la rubrique [Data Factory - règles d’appellation](data-factory-naming-rules.md) pour les règles d’affectation de noms pour les artefacts de données usine.  
     
    2. Sélectionnez votre **abonnement**d' Azure.
    3. Groupe de ressources, effectuez l’une des étapes suivantes : 
        1. Sélectionnez **utiliser l’existant** pour sélectionner un groupe de ressources existant.
        2. Sélectionnez **Créer nouveau** pour entrer un nom pour un groupe de ressources.
    3. Sélectionnez un **emplacement** pour le factory de données.
    4. Cochez **Ajouter au tableau de bord** en bas de la lame.  
    5. Cliquez sur **créer**.
10. Une fois la création terminée, vous voyez la lame **Factory de données** comme indiqué dans l’image suivante :

    ![Page d’accueil de fabrique de données](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
11. Sur la page d’accueil en usine de données, cliquez sur la mosaïque pour lancer **l’Assistant de copie**de **Copier les données** . 

    > [AZURE.NOTE] Si vous voyez que le navigateur web est bloqué au niveau de la « Autorisation... », désactiver/désactiver le paramètre **bloquer les cookies tierce partie et les données du site** (ou) conserver activé et créer une exception pour **login.microsoftonline.com** et puis réessayez de démarrer l’Assistant à nouveau.


## <a name="step-1-configure-data-loading-schedule"></a>Étape 1 : Configurez la planification de chargement des données
La première étape consiste à configurer les planification de chargement des données.  

Dans la page de **Propriétés** :
1. Entrez **CopyFromBlobToAzureSqlDataWarehouse** pour le **nom de la tâche**
2. Sélectionnez l’option **exécuter une seule fois maintenant** .   
3. Cliquez sur **suivant**.  

![Copier l’Assistant page de propriétés](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Étape 2 : Configurer la source
Cette section décrit les étapes pour configurer la source : fichiers d’élément Blob Azure contenant la ligne TB TPC-H 1.

Permet de sélectionner le **Stockage d’objets Blob Azure** comme les données stockent et cliquez sur **suivant**.

![Assistant copie - sélectionner la page source](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

Renseignez les informations de connexion pour le compte de stockage Azure Blob, puis cliquez sur **suivant**.

![Assistant de copie - les informations de connexion Source](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

Choisissez le **dossier** contenant les fichiers d’élément de ligne TPC-H, puis cliquez sur **suivant**.

![Copier l’Assistant, sélectionnez le dossier d’entrée](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

Après avoir cliqué sur **suivant**, les paramètres de format de fichier sont détectés automatiquement.  Vérifiez que ce délimiteur de colonne est ' | 'au lieu de la virgule par défaut','.  Une fois que vous avez affiché un aperçu des données, cliquez sur **suivant** .

![Assistant de copie - les paramètres de format de fichier](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Étape 3 : Configuration de destination
Cette section vous indique comment configurer la destination : `lineitem` table dans la base de données de l’entrepôt de données SQL Azure.

Choisissez **Magasin de données SQL Azure** comme la banque de destination et cliquez sur **suivant**.

![Assistant copie - banque de données de destination select](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

Renseignez les informations de connexion pour l’entrepôt de données SQL Azure.  Veillez à spécifier l’utilisateur qui est membre du rôle `xlargerc` (voir la section **conditions requises** pour obtenir des instructions détaillées), puis cliquez sur **suivant**. 

![Assistant de copie - les informations de connexion de destination](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

Choisissez la table de destination et cliquez sur **suivant**.

![Copier l’Assistant page de mappage de table](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

Acceptez les paramètres par défaut pour le mappage de colonne et cliquez sur **suivant**.

![Copier l’Assistant page de mappage de schéma](media/data-factory-load-sql-data-warehouse/schema-mapping.png)


## <a name="step-4-performance-settings"></a>Étape 4 : Les paramètres de performances

**Autoriser polybase** est activée par défaut.  Cliquez sur **suivant**.

![Copier l’Assistant page de mappage de schéma](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Étape 5 : Déployer et surveiller les résultats de la charge
Cliquez sur le bouton **Terminer** pour déployer. 

![Assistant copie - page Résumé](media/data-factory-load-sql-data-warehouse/summary-page.png)

Une fois le déploiement terminé, cliquez sur `Click here to monitor copy pipeline` pour analyser la copie exécutée la progression.

Sélectionnez le pipeline de copie que vous avez créé dans la liste **Activité Windows** .

![Assistant copie - page Résumé](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

Vous pouvez afficher la détails de l’exécution de l' **Activité de fenêtre Explorer** dans le panneau droit, y compris le volume de données lues à partir de la source et écrites dans la destination, la durée et le débit moyen pour l’exécution de copie.

Comme vous pouvez le voir dans la capture d’écran suivante, copie de 1 To de stockage des objets Blob Azure dans SQL Data Warehouse a duré 14 minutes, atteindre efficacement les 1,22 Gbits/s débit !

![Assistant copie - boîte de dialogue a réussi](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Meilleures pratiques
Voici quelques-unes des meilleures pratiques pour l’exécution de votre base de données de l’entrepôt de données SQL Azure :

- Utilisez une classe de ressources plus importante lors du chargement dans un INDEX COLUMNSTORE ordonné en clusters.
- Pour des jointures plus efficaces, pensez à l’aide de la distribution de hachage en une colonne select plutôt que par défaut round robin distribution.
- Pour des vitesses de chargement plus rapides, envisagez d’utiliser des tas de données transitoires.
- Créer des statistiques après avoir terminé le chargement d’entrepôts de données SQL Azure.

Pour plus d’informations, consultez [méthodes conseillées pour l’entrepôt de données SQL Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md) . 

## <a name="next-steps"></a>Étapes suivantes
- [Assistant de copie de données en usine](data-factory-copy-wizard.md) - cet article fournit des détails à propos de l’Assistant de copie. 
- [Activité de copie de guide des performances et optimisation](data-factory-copy-activity-performance.md) - cet article contient les mesures de performances de référence et guide de réglage.

