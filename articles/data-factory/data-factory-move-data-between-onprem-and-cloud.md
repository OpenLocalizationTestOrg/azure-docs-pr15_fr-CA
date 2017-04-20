<properties 
    pageTitle="Déplacer les données - passerelle de gestion des données | Microsoft Azure"
    description="Configurer une passerelle de données pour déplacer les données entre local et le nuage. Utiliser la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données." 
    keywords="passerelle de données, intégration de données, déplacer les données, les informations d’identification de passerelle"
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
    ms.date="10/18/2016" 
    ms.author="jingwang"/>

# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Déplacement des données entre des sources local et le nuage avec la passerelle de gestion des données
Cet article fournit une vue d’ensemble de l’intégration de données entre les magasins de données locaux et cloud des magasins de données à l’aide de la fabrique de données. Il s’appuie sur l’article [d’Activités de déplacement des données](data-factory-data-movement-activities.md) et autres articles de concepts de base données usine : [groupes de données](data-factory-create-datasets.md) et [pipelines](data-factory-create-pipelines.md). 

## <a name="data-management-gateway"></a>Passerelle de gestion des données
Vous devez installer la passerelle de gestion des données sur votre ordinateur local pour permettre le déplacement des données vers/à partir d’un magasin de données local. La passerelle peut être installée sur le même ordinateur que le magasin de données ou sur une autre machine que la passerelle peut se connecter au magasin de données. 

> [AZURE.IMPORTANT] Consultez l’article de [La passerelle de gestion des données](data-factory-data-management-gateway.md) pour plus d’informations sur la passerelle de gestion des données.   

La procédure suivante vous montre comment créer une fabrique de données avec un pipeline qui déplace les données à partir d’une base de données de **SQL Server** en local à un stockage blob Azure. Dans le cadre de la procédure pas à pas, vous installez et configurez la passerelle de gestion des données sur votre ordinateur. 

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Procédure pas à pas : copier des données sur site vers le cloud
  
## <a name="create-data-factory"></a>Création de la fabrique de données
Dans cette étape, vous utilisez le portail Azure pour créer une instance d’Azure Data Factory nommée **ADFTutorialOnPremDF**. 

1.  Connectez-vous au [portail Azure](https://portal.azure.com). 
2.  Cliquez sur **+ Nouveau**et cliquez sur **Intelligence + analytique**, cliquez sur le **Factory de données**.

    ![Nouveau -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
2. Dans la **nouvelle fabrique de données** lame, entrez **ADFTutorialOnPremDF** pour le nom.

    ![Ajouter au Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

    > [AZURE.IMPORTANT] 
    > Le nom de la fabrique de données Azure doit être globalement unique. Si vous recevez l’erreur : **nom d’usine de données « ADFTutorialOnPremDF » n’est pas disponible**, essayez à nouveau de créer et de modifier le nom de la fabrique de données (par exemple, yournameADFTutorialOnPremDF). Lors de l’exécution des étapes de ce didacticiel, utilisez ce nom à la place de ADFTutorialOnPremDF.
    > 
    > Le nom de la fabrique de données peut être enregistré sous la forme d’un nom **DNS** à l’avenir et, par conséquent, devenir visible publiquement.
3. Sélectionnez l' **abonnement Azure** où vous souhaitez le factory de données à créer. 
4.  Sélectionnez **groupe de ressources** ou de créer un groupe de ressources. Dans le didacticiel, créez un groupe de ressource nommé : **ADFTutorialResourceGroup**. 
5.  Sur la lame de **nouvelle fabrique de données** , cliquez sur **créer** .

    > [AZURE.IMPORTANT] Pour créer des instances de la fabrique de données, vous devez être un membre du rôle [Collaborateur usine](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) au niveau du groupe d’abonnement/de la ressource. 
11. Une fois la création terminée, vous voyez la lame **Factory de données** comme indiqué dans l’image suivante :

    ![Page d’accueil de fabrique de données](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Créer la passerelle
5. Dans la lame **Factory de données** , cliquez sur **auteur et déployer** mosaïque pour lancer l' **éditeur** de la fabrique de données.

    ![Créer et déployer la mosaïque](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.  Dans l’éditeur de fabrique de données, cliquez sur **... Plus** sur la barre d’outils, puis cliquez sur **nouvelle passerelle de données**. Vous pouvez également droit des **Passerelles de données** dans l’arborescence et cliquez sur **nouvelle passerelle de données**. 

    ![Nouvelle passerelle de données sur la barre d’outils](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Dans la lame de **créer** , entrez **adftutorialgateway** pour le **nom**, puis cliquez sur **OK**.    

    ![Créer des lames de passerelle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)
3. De la lame à **configurer** , cliquez sur **installer directement sur cet ordinateur**. Cette action télécharge le package d’installation de la passerelle, installe, configure et enregistre la passerelle sur l’ordinateur.  

    > [AZURE.NOTE] 
    > Utilisez Internet Explorer ou un navigateur compatible Microsoft ClickOnce.
    > 
    > Si vous utilisez Chrome, accédez à la [Boutique web de Chrome](https://chrome.google.com/webstore/), rechercher avec « ClickOnce » mot-clé, choisissez l’une des extensions de ClickOnce et l’installer. 
    >  
    > Procédez de même pour Firefox (complément installation). Cliquez sur le bouton de **Menu Ouvrir** dans la barre d’outils (**trois lignes horizontales** dans le coin supérieur droit), cliquez sur **modules complémentaires**, recherche par mot clé de « ClickOnce », choisissez l’une des extensions de ClickOnce et installez-le.    

    ![Passerelle - configurer les lames](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    De cette façon est le moyen le plus simple (un clic) pour télécharger, installer, configurer et enregistrer la passerelle en une seule étape. Vous pouvez voir que l’application **Gestionnaire de Configuration de passerelle de gestion de données de Microsoft** est installée sur votre ordinateur. Vous pouvez également trouver l' exécutable **ConfigManager.exe** dans le dossier : **C:\Program Files\Microsoft données Gestion Gateway\2.0\Shared**.

    Vous pouvez également télécharger et installer manuellement de passerelle en utilisant les liens dans cette blade et enregistrez-le à l’aide de la clé indiquée dans la zone de texte **Nouvelle clé** .
    
    Consultez l’article de [La passerelle de gestion des données](data-factory-data-management-gateway.md) pour tous les détails sur la passerelle.

    >[AZURE.NOTE] Vous devez être un administrateur sur l’ordinateur local pour installer et configurer la passerelle de gestion des données avec succès. Vous pouvez ajouter des utilisateurs au groupe local Windows **Utilisateurs de passerelle de gestion des données** . Les membres de ce groupe peuvent utiliser l’outil Gestionnaire de Configuration de passerelle de gestion des données pour configurer la passerelle. 

5. Attendez quelques minutes, ou patienter jusqu'à ce que vous voyez le message de notification suivant :

    ![Installation de la passerelle réussie](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png) 
6. Lancer l’application **Gestionnaire de Configuration de passerelle de gestion des données** sur votre ordinateur. Dans la fenêtre de **recherche** , tapez la **Passerelle de gestion des données** pour accéder à cet utilitaire. Vous pouvez également trouver l' exécutable **ConfigManager.exe** dans le dossier : **C:\Program Files\Microsoft données Gestion Gateway\2.0\Shared** 

    ![Gestionnaire de Configuration de passerelle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
6. Vérifiez que vous consultez `adftutorialgateway is connected to the cloud service` message. L’état de la barre du bas affiche **connecté au service cloud** avec une **coche verte**.

    Sous l’onglet **accueil** , vous pouvez également effectuer les opérations suivantes : 
    - **Enregistrer** une passerelle avec une clé à partir du portail Azure en utilisant le bouton Enregistrer. 
    - **Arrêter** le Service de hôte de passerelle de gestion des données en cours d’exécution sur votre ordinateur de la passerelle. 
    - **Mises à jour de la planification** pour être installé à une heure spécifique de la journée. 
    - Vue de la passerelle a été **mis à jour**.
    - Spécifier l’heure à laquelle une mise à jour vers la passerelle peut être installée. 

8. Basculez vers l’onglet **paramètres** . Le certificat spécifié dans la section de **certificat** est utilisé pour chiffrer/déchiffrer les informations d’identification pour le magasin de données local que vous spécifiez sur le portail. (facultatif) Cliquez sur **Modifier** pour utiliser à la place de votre propre certificat. Par défaut, la passerelle utilise le certificat qui est automatiquement généré par le service Data Factory.

    ![Configuration de certificats de passerelle](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Vous pouvez également effectuer les actions suivantes sous l’onglet **paramètres** : 
    - Permet d’afficher ou d’exporter le certificat utilisé par la passerelle.
    - Modifier le point de terminaison HTTPS utilisé par la passerelle.    
    - Définir un proxy HTTP à utiliser par la passerelle.   
9. (facultatif) Basculez vers l’onglet **Diagnostics** , cochez l’option **Activer l’enregistrement détaillé** si vous voulez activer la journalisation détaillée que vous pouvez utiliser pour résoudre les problèmes avec la passerelle. Vous trouverez les informations de journalisation dans l' **Observateur d’événements** sous **journaux des Services des Applications et** -> **Passerelle de gestion des données de** nœud. 

    ![Onglet Diagnostics](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Vous pouvez également effectuer les actions suivantes dans l’onglet **Diagnostics** : 
    
    - Utilisez la section de **Tester la connexion** à une source de données en local à l’aide de la passerelle.
    - Cliquez sur **Afficher les journaux** pour consulter le journal de la passerelle de gestion des données dans une fenêtre de l’Observateur d’événements. 
    - Cliquez sur **Envoyer les journaux** pour télécharger un fichier zip contenant les journaux des 7 derniers jours à Microsoft pour faciliter le dépannage de vos problèmes. 
10. Sous l’onglet **Diagnostics** , dans la section **Tester la connexion** , sélectionnez **SqlServer** pour le type de la banque de données, entrez le nom du serveur de base de données, nom de la base de données, spécifiez le type d’authentification, entrez le nom d’utilisateur et mot de passe et cliquez sur **tester** pour tester si la passerelle peut se connecter à la base de données. 
11. Basculez vers le navigateur web et dans **Azure portal**, cliquez sur **OK** sur la lame de **configurer** , puis sur la lame de **nouvelle passerelle de données** .
6. Vous devez voir **adftutorialgateway** sous **Passerelles de données** dans l’arborescence sur la gauche.  Si vous cliquez dessus, vous devez voir le JSON associé. 
    

## <a name="create-linked-services"></a>Créer des services liés 
Dans cette étape, vous créez deux services liés : **AzureStorageLinkedService** et **SqlServerLinkedService**. Le **SqlServerLinkedService** lie une base de données de SQL Server sur site et les liens de service liés **AzureStorageLinkedService** un blob Azure stocker sur le factory de données. Vous créez un pipeline plus loin dans cette procédure pas à pas qui copie les données à partir de la base de données de SQL Server sur site pour la banque de blob Azure. 

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Ajouter un service lié à une base de données de SQL Server sur site
1.  Dans l' **Éditeur de fabrique de données**, cliquez sur **nouveau magasin de données** dans la barre d’outils, puis sélectionnez **SQL Server**. 

    ![Nouveau service de SQL Server liée](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.  Dans l' **éditeur de JSON** sur la droite, effectuez les opérations suivantes : 
    1. Pour le **nom de la passerelle**, spécifiez **adftutorialgateway**. 
    2. Dans le **connectionString**, procédez comme suit : 
        1. Pour le **nom du serveur**, entrez le nom du serveur qui héberge la base de données SQL Server.
        2. **Databasename**, tapez le nom de la base de données.
        3. Cliquez sur le bouton **crypter** sur la barre d’outils. Il télécharge et lance l’application de gestionnaire d’informations d’identification.
        
            ![Application de gestionnaire d’informations d’identification](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
        5. Dans la boîte de dialogue **Informations d’identification du paramètre de** spécifier le type d’authentification, nom d’utilisateur et mot de passe, puis cliquez sur **OK**. Si la connexion est réussie, les informations d’identification cryptées sont stockées dans le JSON et la boîte de dialogue se ferme. 
        6. Fermer l’onglet navigateur vide s’il n’est pas fermé automatiquement de lancer la boîte de dialogue et revenir à l’onglet avec le portail Azure. 
  
            Sur l’ordinateur de la passerelle, ces informations d’identification sont **cryptées** en utilisant un certificat possédant le service Data Factory. Si vous souhaitez utiliser le certificat qui est associé à la passerelle de gestion des données au lieu de cela, voir [définir les informations d’identification en toute sécurité](#set-credentials-and-security).    
    1.  Dans la barre de commande pour déployer le service SQL Server liée, cliquez sur **déployer** . Vous devez voir le service lié dans l’arborescence. 
        
        ![Service de SQL Server liée dans l’arborescence](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)  

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Ajout d’un service lié pour un compte de stockage Azure
 
1. Dans l' **Éditeur de fabrique de données**, cliquez sur **nouveau magasin de données** sur la barre de commandes et cliquez sur **stockage Azure**.
2. Entrez le nom de votre compte de stockage Azure pour le **nom du compte**.
3. Entrez la clé de votre compte de stockage Azure pour la **clé de compte**.
4. Cliquez sur **Deploy** pour déployer **AzureStorageLinkedService**.
   
 
## <a name="create-datasets"></a>Créer des groupes de données
Dans cette étape, vous créez d’entrée et de sortie des groupes de données qui représentent des données d’entrée et de sortie pour l’opération de copie (base de données de site sur SQL Server = > stockage blob Azure). Avant de créer des groupes de données, procédez comme suit (les étapes détaillées suit la liste) :

- Créez une table nommée **employés** dans la base de données de SQL Server vous est ajouté sous la forme d’un service lié à l’usine de données et insérer quelques exemples d’entrées dans la table.
- Créer un conteneur blob nommé **adftutorial** dans le compte de stockage blob Azure que vous avez ajouté sous la forme d’un service lié à l’usine de données.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Préparation de SQL Server sur site pour le didacticiel

1. Dans la base de données que vous avez spécifié pour le SQL Server local service lié (**SqlServerLinkedService**), utilisez le script SQL suivant pour créer la table **emp** dans la base de données.

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL, 
            FirstName varchar(50),
            LastName varchar(50),
            CONSTRAINT PK_emp PRIMARY KEY (ID)
        )
        GO 
2. Insérer des exemples dans la table : 

        INSERT INTO emp VALUES ('John', 'Doe')
        INSERT INTO emp VALUES ('Jane', 'Doe')

### <a name="create-input-dataset"></a>Créer le groupe de données d’entrée

1. Dans l' **Éditeur de fabrique de données**, cliquez sur **... Plus**et cliquez sur **Nouveau groupe de données** dans la barre de commande, cliquez sur **la table SQL Server**. 
2.  Remplacez le JSON dans le volet droit par le texte suivant :
        
            {       
                "name": "EmpOnPremSQLTable",
                "properties": {
                    "type": "SqlServerTable",
                    "linkedServiceName": "SqlServerLinkedService",
                    "typeProperties": {
                        "tableName": "emp"
                    },
                    "external": true,
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "policy": {
                        "externalData": {
                            "retryInterval": "00:01:00",
                            "retryTimeout": "00:10:00",
                            "maximumRetry": 3
                        }
                    }
                }
            }    

    Notez les points suivants : 

    - **type** est défini sur **SqlServerTable**.
    - **tableName** est définie sur **emp**.
    - **linkedServiceName** est défini sur **SqlServerLinkedService** (vous aviez créé ce service lié précédemment dans cette procédure pas à pas.).
    - Pour un groupe de données d’entrée qui n’est pas généré par une autre dans une usine de données Azure, vous devez définir l’option **externe** à **true**. Il indique que les données d’entrée sont produites externe au service Factory de données Azure. Vous pouvez éventuellement spécifier des stratégies de données externes à l’aide de l’élément **externalData** dans la section **stratégie** .    

    Pour plus d’informations sur les propriétés JSON, consultez [déplacer des données vers/à partir de SQL Server](data-factory-sqlserver-connector.md) .
2. Dans la barre de commande pour déployer le groupe de données, cliquez sur **déployer** .  


### <a name="create-output-dataset"></a>Créer le dataset de sortie

1.  Dans l' **Éditeur de fabrique de données**, cliquez sur **Nouveau groupe de données** dans la barre de commande, cliquez sur le **stockage des objets Blob Azure**.
2.  Remplacez le JSON dans le volet droit par le texte suivant : 

            {
                "name": "OutputBlobTable",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "AzureStorageLinkedService",
                    "typeProperties": {
                        "folderPath": "adftutorial/outfromonpremdf",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            }
  
    Notez les points suivants : 
    
    - **type** est défini sur **AzureBlob**.
    - **linkedServiceName** est défini sur **AzureStorageLinkedService** (vous aviez créé ce service lié à l’étape 2).
    - **folderPath** a **adftutorial/outfromonpremdf** où outfromonpremdf est le dossier dans le conteneur adftutorial. Créez le conteneur **adftutorial** s’il n’existe pas déjà. 
    - La **disponibilité** est définie pour **toutes les heures** (**fréquence** définie sur **l’heure** et **intervalle** la valeur **1**).  Le service de données fabrique génère une tranche de données sortie toutes les heures dans la table **emp** dans la base de données de SQL Azure. 

    Si vous ne spécifiez pas de **nom de fichier** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : données. <Guid>.txt (par exemple : : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Pour définir les **folderPath** et dynamiquement en fonction du temps de **SliceStart** de **nom de fichier** , utilisez la propriété partitionedBy. Dans l’exemple suivant, folderPath utilise l’année, mois et jour à partir de le SliceStart (heure de début de la section en cours de traitement) et nom de fichier utilise l’heure à partir de la SliceStart. Par exemple, si une tranche est fabriquée pour 2014-10-20T08:00:00, NomDossier est définie à wikidatagateway/wikisampledataout/2014/10/20 et le nom de fichier est définie sur 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 
    Pour plus d’informations sur les propriétés JSON, consultez [déplacer des données vers/depuis le stockage Blob Azure](data-factory-azure-blob-connector.md) .
2.  Dans la barre de commande pour déployer le groupe de données, cliquez sur **déployer** . Vérifiez que vous consultez les groupes de données dans l’arborescence.  

## <a name="create-pipeline"></a>Créer des opportunités
Dans cette étape, vous créez un **pipeline** avec une **Activité de copie** qui utilise **EmpOnPremSQLTable** comme entrée et **OutputBlobTable** en tant que sortie.

1.  Dans l’éditeur de fabrique de données, cliquez sur **... Plus**, cliquez sur le **nouveau pipeline**. 
2.  Remplacez le JSON dans le volet droit par le texte suivant : 
    
            {
                "name": "ADFTutorialPipelineOnPrem",
                "properties": {
                "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
                "activities": [
                {
                    "name": "CopyFromSQLtoBlob",
                    "description": "Copy data from on-prem SQL server to blob",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "EmpOnPremSQLTable"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "OutputBlobTable"
                      }
                    ],
                    "typeProperties": {
                      "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from emp"
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                    },
                    "Policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "NewestFirst",
                      "style": "StartOfInterval",
                      "retry": 0,
                      "timeout": "01:00:00"
                    }
                  }
                ],
                "start": "2016-07-05T00:00:00Z",
                "end": "2016-07-06T00:00:00Z",
                "isPaused": false
              }
            }

    > [AZURE.IMPORTANT]
    > Remplacez la valeur de la propriété **start** avec la valeur actuelle de jour et à la **fin** avec le jour suivant.

    Notez les points suivants :
 
    - Dans la section activités, il est uniquement les activités dont le **type** est définie pour la **copie**.
    - **EmpOnPremSQLTable** a la valeur **entrée** pour l’activité et la **sortie** de l’activité est définie sur **OutputBlobTable**.
    - Dans la section **typeProperties** , **SqlSource** est spécifié comme **type de source** et **BlobSink **est spécifié comme **type de récepteurs**.
    - Les requêtes SQL `select * from emp` n’est spécifiée pour la propriété **sqlReaderQuery** de le **SqlSource**.

     Les deux début et dates/heures de fin doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de **fin** est facultative, mais nous l’utilisons dans ce didacticiel. 
    
    Si vous ne spécifiez pas de valeur pour la propriété **end** , il est calculé en tant que «**début + 48 heures**». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** , comme valeur de la propriété **end** . 
    
    Vous définissez le délai dans lequel les tranches de données sont traitées en fonction des propriétés de **disponibilité** qui ont été définies pour chaque jeu de données Azure Data Factory.
    
    Dans l’exemple, il existe des tranches de données 24 chaque tranche de données se produit toutes les heures.     
2. Cliquez sur **déployer** la barre de commandes pour déployer le groupe de données (table est un groupe de données rectangulaire). Confirmez que le pipeline s’affiche dans l’arborescence sous le nœud de **Pipelines** .  
5. Maintenant, cliquez sur le **X** deux fois pour fermer les lames pour revenir à la lame **Factory de données** pour **ADFTutorialOnPremDF**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, services liés, des groupes de données et un tuyau et planifiée du pipeline.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Afficher le factory de données dans un affichage de tâches 
1. Dans le **portail Azure**, cliquez sur mosaïque de **diagramme** dans la page d’accueil de la fabrique de données **ADFTutorialOnPremDF** . :

    ![Lien de diagramme](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Vous devez voir le diagramme image semblable à la suivante :

    ![Affichage des diagrammes](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Vous pouvez effectuer un zoom avant, zoom arrière, zoom à 100 %, zoom pour ajuster automatiquement positionner des tuyaux et des groupes de données et afficher les informations de lignage (met en surbrillance les éléments en amont et en aval des éléments sélectionnés).  Vous pouvez double-cliquer sur un objet (groupe de données d’entrée/sortie ou pipeline) pour afficher les propriétés. 

## <a name="monitor-pipeline"></a>Pipeline de moniteur
Dans cette étape, vous utilisez le portail Azure pour contrôler ce qui se passe dans une usine de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour analyser des groupes de données et pipelines. Pour plus d’informations sur l’analyse, consultez le [surveiller et gérer les Pipelines](data-factory-monitor-manage-pipelines.md).

5. Dans le diagramme, double-cliquez sur **EmpOnPremSQLTable**.  

    ![Tranches de EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Notez que toutes les tranches de données jusqu'à l’état **prêt** , car la durée de pipeline (heure de début à l’heure de fin) est dans le passé. Il est également parce que vous avez inséré les données dans la base de données SQL Server et il est il tout le temps. Vérifiez qu’aucuns tranches n’apparaissent dans la section **tranches de problème** en bas. Pour afficher toutes les sections, cliquez sur **Voir plus** en bas de la liste des tranches. 
7. Maintenant, dans la lame de **groupes de données** , cliquez sur **OutputBlobTable**.

    ![Tranches de OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
9. Cliquez sur une tranche de données à partir de la liste, et vous devriez voir la lame de la **Tranche de données** . Vous consultez l’activité s’exécute pour la tranche. Vous ne voyez qu’une seule activité généralement exécutées.  

    ![Lame de tranche de données](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Si la section n’est pas dans l’état **prêt** , vous pouvez voir les tranches en amont qui ne sont pas prêtes et bloquent la tranche en cours d’exécution dans la liste **des tranches en amont qui ne sont pas prêtes** .

10. Cliquez sur l' **activité exécutée** à partir de la liste du bas pour voir les **Détails d’activité l'exécution**.

    ![Lame de détails de l’exécution d’activité](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

    Vous devez voir les informations de débit, de durée et de la passerelle utilisée pour transférer les données. 
11. Cliquez sur **X** pour fermer toutes les blades jusqu'à ce que vous 
12. revenir à la lame domestique pour **ADFTutorialOnPremDF**.
14. (facultatif) Cliquez sur **canalisations**et cliquez sur **ADFTutorialOnPremDF**, effectuer des recherches dans les tables d’entrée (**utilisé**) ou les jeux de données de sortie (**production**).
15. Utilisez des outils tels que des outils tels que [Microsoft Explorateur de stockage](http://storageexplorer.com/) pour vérifier qu’un fichier blob/est créé pour chaque heure.

    ![Explorateur de stockage Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article de [La passerelle de gestion des données](data-factory-data-management-gateway.md) pour tous les détails sur la passerelle de gestion des données.
- Voir [Copier les données de Blob Azure pour Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour en savoir plus sur l’utilisation des activités de copie pour transférer des données à partir d’un magasin de données source vers un magasin de données du récepteur. 
