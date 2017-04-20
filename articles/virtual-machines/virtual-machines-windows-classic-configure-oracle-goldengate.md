<properties
    pageTitle="Configuration d’Oracle GoldenGate dans des machines virtuelles | Microsoft Azure"
    description="Parcourir un didacticiel pour le paramétrage et la mise en œuvre d’Oracle GoldenGate sur VMs de serveur Windows Azure pour la haute disponibilité et reprise après sinistre."
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />


#<a name="configuring-oracle-goldengate-for-azure"></a>Configuration d’Oracle GoldenGate pour Azure


Ce didacticiel montre comment le programme d’installation d’Oracle GoldenGate pour environnement d’ordinateurs virtuels Azure pour la haute disponibilité et reprise après sinistre. Le didacticiel est axé sur la [réplication bidirectionnelle](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) pour les bases de données Oracle RAC non - et nécessite que les deux sites sont actifs.

Oracle GoldenGate prend en charge la distribution des données et l’intégration de données. Il vous permet de configurer une distribution des données et une solution de synchronisation de données par le biais de la configuration de réplication Oracle et Oracle et fournit une solution flexible de haute disponibilité. Oracle GoldenGate complète Oracle Data Guard grâce à ses fonctions de réplication pour permettre des migrations et des mises à niveau sans interruption de service et de distribution d’informations à l’échelle de l’entreprise. Pour plus d’informations, consultez [L’aide de GoldenGate Oracle avec Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contient les composants principaux suivants : extraire, traces de pompe, Replicat, de données ou extraire les fichiers, les points de contrôle, le gestionnaire et collecteur. Pour une réplication bidirectionnelle entre deux sites, vous devez créer et démarrer tous les composants sur les deux sites. Pour obtenir des informations détaillées sur l’architecture d’Oracle GoldenGate, voir le [Guide de GoldenGate d’Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

Ce didacticiel suppose que vous avez déjà des connaissances théoriques et pratiques sur les concepts de reprise après sinistre et de disponibilité de base de données Oracle comme [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Pour plus d’informations, consultez le [site web d’Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

En outre, le didacticiel suppose que vous avez déjà implémenté les conditions préalables suivantes :

- Vous avez déjà révisé la section haute disponibilité et des considérations sur la récupération après incident dans la rubrique [images Oracle Virtual Machine - considérations divers](virtual-machines-windows-classic-oracle-considerations.md) . Notez que Azure prend en charge les instances de base de données Oracle autonome mais pas Oracle Real Application Clusters (RAC Oracle) actuellement.

- Vous avez téléchargé le logiciel Oracle GoldenGate à partir du site web [Téléchargements d’Oracle](http://www.oracle.com/us/downloads/index.html) . Vous avez sélectionné le Middleware de Fusion produit Pack Oracle – intégration de données. Ensuite, vous avez sélectionné Oracle GoldenGate sur Oracle v11.2.1 Media Pack pour Microsoft Windows (64 bits) de x64 pour une base de données de Oracle 11 g. Téléchargez ensuite V11.2.1.0.3 de GoldenGate d’Oracle pour Oracle 11g 64 bits sous Windows 2008 (64 bits).

- Vous avez créé deux Machines virtuelles (VM) dans Azure à l’aide d’Oracle Enterprise Edition sur Windows Server. Assurez-vous que les ordinateurs virtuels sont dans le [même service de cloud](virtual-machines-linux-load-balance.md) et dans le même [Réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour garantir qu’ils peuvent accéder à eux sur l’adresse IP privée permanente.

- Vous avez défini le nom de l’ordinateur virtuel en tant que « MachineGG1 » pour le Site A et les « MachineGG2 » pour le Site B sur le portail classique Azure.

- Vous avez créé les bases de données de test « TestGG1 » sur le Site A et les « TestGG2 » sur le Site B.

- Vous ouvrez une session sur votre serveur Windows en tant que membre du groupe Administrateurs ou membre du groupe **ORA_DBA** .

Dans ce didacticiel, vous allez :

1. Le programme d’installation de base de données sur le Site A et Site B  

    1. Exécuter le chargement initial des données

2. Préparer le Site A et B du Site pour la réplication de la base de données

3. Créer tous les objets nécessaires pour prendre en charge la réplication DDL

4. Configurer le Gestionnaire de GoldenGate sur les sites A et B

5. Créer un groupe d’extraire et de Data Pump processus sur le Site A et Site B

    1. Créer des processus d’extraction et Data Pump du Site A

    2. Créer une table de point de contrôle de GoldenGate sur le Site B

    3. Ajouter REPLICAT sur le Site B

    4. Créer un processus d’extraction et Data Pump sur le Site B

    5. Créer une table de point de contrôle de GoldenGate sur le Site A

    6. Ajouter REPLICAT sur le Site A

    7. Ajouter trandata sur le Site A et Site B

    8. Démarrer les processus de Data Pump et les extrait du Site A

    9. Démarrer des processus d’extraction et Data Pump sur le Site B

    10. Lancer le REPLICAT sur le Site A

    11. Démarrer le processus de REPLICAT sur le Site B

6. Vérifiez si le processus de réplication bidirectionnelle

>[AZURE.IMPORTANT] Ce didacticiel a été configuré et testé par rapport à la configuration logicielle suivante :
>
>|                        | **Une base de données du site**              | **Base de données du site B**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Version d’Oracle**     | Oracle11g version 2-(11.2.0.1) | Oracle11g version 2-(11.2.0.1) |
>| **Nom de l’ordinateur**       | MachineGG1                       | MachineGG2                       |
>| **Système d'exploitation**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle SID**         | TESTGG1                          | TESTGG2                          |
>| **Schéma de réplication** | SCOTT                            | SCOTT                            |

Pour les versions ultérieures de la base de données Oracle et Oracle GoldenGate, il existe peut-être des modifications que vous devez implémenter. Pour la plus récente version d’informations, consultez la documentation [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) et de la [Base de données Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) sur le site web de Oracle. Par exemple, une base de données source de la version 11.2.0.4 et les versions ultérieures, la capture de DDL est exécutée par le serveur de logmining de façon asynchrone et nécessite aucun déclencheur spécial, tables ou autres objets de base de données doit être installé. Les mises à niveau Oracle GoldenGate peuvent être effectuées sans arrêt des applications de l’utilisateur. L’utilisation d’un déclencheur DDL et la prise en charge des objets est requise lors de l’extraction est en mode intégré avec un Oracle 11g source de base de données qui est antérieure à la version 11.2.0.4. Pour des instructions détaillées, reportez-vous à la section [installation et configuration de Oracle GoldenGate pour la base de données Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##<a name="1-setup-database-on-site-a-and-site-b"></a>1. le programme d’installation de la base de données sur le Site A et Site B
Cette section explique comment exécuter les composants de base de données sur le Site A et Site B. Vous devez effectuer toutes les étapes de cette section sur les deux sites : Site A et Site B.

Bureau à distance, premier Site A et Site B via le portail classique Azure. Ouvrez une invite de commandes Windows et créer un répertoire de base pour les fichiers de configuration Oracle GoldenGate :

    mkdir C:\OracleGG

Ensuite, décompresser et installer le logiciel Oracle GoldenGate dans ce dossier. Après cette étape, vous pouvez lancer l’interpréteur de commande des logiciels de GoldenGate (GGSCI) en exécutant la commande suivante :

    C:\OracleGG\.\ggsci

Vous pouvez utiliser [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) pour exécuter plusieurs commandes de configurer, de contrôler et de surveiller Oracle GoldenGate.

Ensuite, exécutez la commande suivante pour créer tous les sous-dossiers à partir du package d’installation :

    GGSCI (Hostname) 1> CREATE SUBDIRS

Exécutez la commande suivante pour quitter l’invite de commande GGSCI :

    GGSCI (Hostname) 1> EXIT

Ensuite, vous devez créer un utilisateur de base de données, qui sera utilisé par les processus de réplication, extrait et Oracle GoldenGate Manager. Notez que vous pouvez créer des utilisateurs individuels pour chaque processus ou configurer qu’un seul utilisateur courants. Dans ce didacticiel, nous créons un utilisateur, qui est appelé en tant que ggate. Ensuite, nous accorder à cet utilisateur les privilèges nécessaires. Notez que vous devez effectuer les opérations suivantes sur le Site A et Site B.

Ouvrez SQL\*Plus la fenêtre de commande sur le Site A et Site B avec des privilèges d’administrateur de base de données à l’aide de **SYSDBA**, telles que :

    Enter username: / as sysdba

Et exécutez :

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

Ensuite, localisez l’INIT\<DatabaseSID\>. OU d’un fichier dans le % ORACLE\_accueil %\\base de données du dossier sur le Site A et Site B et y ajouter les paramètres suivants de la base de données INITTEST.ora :

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Pour obtenir une liste complète de toutes les commandes Oracle GoldenGate GGSCI, consultez la [référence pour Oracle GoldenGate pour Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Exécuter le chargement initial des données

Vous pouvez effectuer le chargement initial des données dans la base de données en suivant plusieurs méthodes. Par exemple, vous pouvez utiliser le [Chargement Direct de Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) ou régulières utilitaires d’importation et d’exportation pour exporter les données de la table du Site A au Site B.

Pour illustrer le processus de réplication Oracle GoldenGate, ce didacticiel illustre la création d’une table à l’aide des commandes suivantes sur le Site A et site B.

Tout d’abord, ouvrez SQL\*ainsi la fenêtre de commande et exécutez la commande suivante pour créer une table d’inventaire sur les bases de données de Site A et Site B :

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Ensuite, ajoutez une contrainte à la table créée sur le Site A et les bases de données de Site B :

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Ensuite, accorder tous les privilèges sur la table de nouveau à la ggate de l’utilisateur sur le Site A et Site b :

    grant all on scott.inventory to ggate;

Ensuite, créez et permettent à un déclencheur de base de données, INVENTORY_CDR_TRG, sur la nouvelle table pour vous assurer que toutes les transactions pour la nouvelle table sont enregistrées si l’utilisateur n’est pas ggate. Effectuer cette opération sur le Site A et Site B.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. préparer le Site A et B du Site pour la réplication de la base de données
Cette section explique comment préparer le Site A et B du Site pour la réplication de la base de données. Vous devez effectuer toutes les étapes de cette section sur les deux sites : Site A et Site B.

Bureau à distance, premier Site A et Site B via le portail classique Azure. Passer de la base de données en mode de journal d’archive en utilisant le SQL * Plus la fenêtre de commande :

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Ensuite, activez le minimale [journalisation supplémentaire](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) comme suit :

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Ensuite, préparez la base de données pour prendre en charge la réplication DDL (langage de définition de données) :

    SQL> alter system set recyclebin=off scope=spfile;

Ensuite, arrêt et redémarrage de la base de données :

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. créer tous les objets nécessaires pour prendre en charge la réplication DDL
Cette section répertorie les scripts dont vous avez besoin pour créer tous les objets nécessaires pour prendre en charge la réplication DDL. Vous devez exécuter les scripts spécifiés dans cette section sur le Site A et Site B.

Ouvrez une invite de commandes Windows et accédez au dossier Oracle GoldenGate, par exemple, C:\\OracleGG. Démarrer SQL\*Plus d’invite de commandes avec des privilèges d’administrateur de base de données, telles que l’utilisation de **SYSDBA** sur le Site A et Site B.

Ensuite, exécutez les scripts suivants :

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Outil de GoldenGate Oracle nécessite une connexion de niveau table pour la prise en charge DDL (langage de définition de données). C’est pourquoi, activer supplémentaire journalisation au niveau de la table à l’aide de la commande ADD TRANDATA. Ouvrir fenêtre d’interpréteur de commande de GoldenGate d’Oracle, la connexion à la base de données, puis exécutez la commande ADD TRANDATA

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. Configuration de gestionnaire de GoldenGate sur le Site A et Site B
Le Gestionnaire de GoldenGate Oracle exécute un certain nombre de fonctions, telles que démarrer les autres processus GoldenGate, gestion de fichiers de journal de traçage et de rapports.

Vous devez configurer le processus Oracle GoldenGate Manager sur le Site A et Site B. Pour ce faire, effectuez les opérations suivantes sur le Site A et Site B.

Fenêtres ouvertes, fenêtre de commande et lancer l’interpréteur de commandes Oracle GoldenGate :

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Enregistre la session GGSCI dans une base de données de sorte que vous pouvez exécuter des commandes qui affectent la base de données :

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Afficher l’état et pour tous les processus Gestionnaire, extrait et Replicat sur un système de retard (le cas échéant) :

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Ouvrez le fichier de paramètre à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Afficher l’état et pour tous les processus Gestionnaire, extrait et Replicat sur un système de retard (le cas échéant) :

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Enregistre la session GGSCI dans une base de données de sorte que vous pouvez exécuter des commandes qui affectent la base de données :

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Démarrez le Gestionnaire de processus :

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. créer Extract Data Pump et groupe de processus sur le Site A et Site B

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>Créer des processus d’extraction et Data Pump du Site A

Vous devez créer les processus extrait et Data Pump sur Site A et Site B. Bureau à distance pour le Site A et Site B via le portail classique Azure. Ouvrir fenêtre d’interpréteur de commande GGSCI. Exécutez les commandes suivantes sur Site :

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Ouvrez le fichier de paramètre à l’aide de la commande modifier les paramètres, puis y ajoute les informations suivantes : GGSCI (MachineGG1) 18 > modifier ggate params ext1 extrait d’ext1 nom d’utilisateur, mot de passe ggate scott.inventory TABLE de ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER, GETBEFORECOLS (KEYINCLUDING de mise à jour de (prod_category, qty_in_stock, last_dml), sur Supprimer KEYINCLUDING (prod_category, qty_in_stock, last_dml)) ;

Ouvrez le fichier de paramètre à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Créer une table de point de contrôle de GoldenGate sur le Site B

Ensuite, vous devez ajouter un tableau de points de contrôle sur le Site B. Pour ce faire, vous devez ouvrir une fenêtre d’interpréteur de commande GoldenGate et exécuter :

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Et puis, ajoutez la table du point de contrôle à la base de données, où ggate est le propriétaire :

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Ajoutez le nom de la table de point de vérification dans le fichier global sur le serveur cible, qui est le Site B dans cette étape. Modifier le fichier global sur le Site b :

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Puis, ajoutez le paramètre CHECKPOINTTABLE vers le fichier global existant :

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

En tant que dernière étape, enregistrez et fermez le fichier de paramètre GLOBALS.


###<a name="add-replicat-on-site-b"></a>Ajouter REPLICAT sur le Site B
Cette section décrit comment ajouter un processus REPLICAT « REP2 » sur le Site B.

La commande ADD REPLICAT permet de créer un groupe de Replicat sur le Site b :

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Ouvrez le fichier de paramètre à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>Créer un processus d’extraction et Data Pump sur le Site B

Cette section décrit comment créer un nouveau processus extrait « EXT2 » et un nouveau processus de pompe de données « DPUMP2 » sur le Site b :

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Ouvrez le fichier de paramètre à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Ouvrez le fichier de paramètre à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Créer une table de point de contrôle de GoldenGate sur le Site A

Ouvrir fenêtre interpréteur de commande Oracle GoldenGate et créer une table de point de contrôle :

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

Vous devez également ajouter le nom de la table de point de vérification dans le fichier global sur le Site A.

Ouvrir fenêtre interpréteur de commande Oracle GoldenGate et modifiez le fichier GLOBALS sur Site :

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Enregistrez et fermez le fichier de paramètre global.

###<a name="add-replicat-on-site-a"></a>Ajouter REPLICAT sur le Site A

Cette section décrit comment ajouter un processus REPLICAT « REP1 » sur le Site A.

La commande suivante crée un rep1 de groupe de Replicat avec le nom d’une piste et le checkpointtable associé.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Ouvrez le fichier de paramètre à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Ajouter trandata sur le Site A et Site B

Activer la journalisation supplémentaire au niveau de la table à l’aide de la commande ADD TRANDATA. Ouvrir fenêtre d’interpréteur de commande de GoldenGate d’Oracle, la connexion à la base de données, puis exécutez la commande ADD TRANDATA

Bureau à distance à MachineGG1, ouvrez d’interpréteur de commandes Oracle GoldenGate et exécutez :

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Bureau à distance à MachineGG2, ouvrez d’interpréteur de commandes Oracle GoldenGate et exécutez :

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Affiche des informations sur l’état de niveau table supplémentaire de journalisation :

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>Ajouter trandata sur le Site A et Site B

Activer la journalisation supplémentaire au niveau de la table à l’aide de la commande ADD TRANDATA. Ouvrir fenêtre d’interpréteur de commande de GoldenGate d’Oracle, la connexion à la base de données, puis exécutez la commande ADD TRANDATA

Bureau à distance à MachineGG1, ouvrez d’interpréteur de commandes Oracle GoldenGate et exécutez :

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Bureau à distance à MachineGG2, ouvrez d’interpréteur de commandes Oracle GoldenGate et exécutez :

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>Démarrer les processus de Data Pump et les extrait du Site A

Démarrez l’ext1 du processus extrait sur Site :

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Démarrer le dpump1 de processus de pompe de données sur Site :

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Afficher des informations sur l’ext1 de groupe extrait : GGSCI (MachineGG1) 32 > info extraire ext1 extraire EXT1 dernière a démarré 2013-11-25 08:03 état le décalage du point de contrôle en cours d’exécution 00:00:00 (mise à jour 00:00:02 auparavant) journal en lecture du point de contrôle Oracle rétablir journaux 2013-11-25 08:03:18 Seqno 6, 3230720 de RBA SCN 0.1074371 (1074371)

Afficher l’état et pour tous les processus Gestionnaire, extrait et Replicat sur un système de retard (le cas échéant) :

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>Démarrer des processus d’extraction et Data Pump sur le Site B

Démarrer l’ext2 processus extrait sur le Site b :

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Démarrer le dpump2 de processus de pompe de données sur le Site b :

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Afficher l’état et pour tous les processus Gestionnaire, extrait et Replicat sur un système de retard (le cas échéant) :

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Lancer le REPLICAT sur le Site A

Cette section décrit comment démarrer le processus REPLICAT « REP1 » sur le Site A.

Démarrer le processus de Replicat sur Site :

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Afficher le statut d’un groupe de Replicat :

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>Démarrer le processus de REPLICAT sur le Site B

Cette section décrit comment démarrer le processus REPLICAT « REP2 » sur le Site B.

Démarrer le processus de Replicat sur le Site b :

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Afficher le statut d’un groupe de Replicat :

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6. Vérifiez le processus de réplication bidirectionnelle

Pour vérifier la configuration Oracle GoldenGate, insérez une ligne dans la base de données au Bureau à distance de Site a au Site A. Ouvrez SQL * Plus la fenêtre de commande et exécutez : SQL > sélectionnez nom de la base de données de v$ ;

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Ensuite, vérifiez si cette ligne est répliquée sur le Site B. Pour ce faire, le Bureau à distance pour le Site B. ouvrir jusqu'à la fenêtre SQL Plus et exécutez :

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Insérer un nouvel enregistrement au Site b :

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Bureau à distance pour le Site A et de vérifier si la réplication a eu lieu :

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>Ressources supplémentaires
[Images de Machine virtuelle d’Oracle pour Azure](virtual-machines-linux-classic-oracle-images.md)
