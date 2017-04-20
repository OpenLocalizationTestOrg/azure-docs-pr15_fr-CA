<properties
    pageTitle="Configuration d’Oracle Data Guard dans des machines virtuelles | Microsoft Azure"
    description="Parcourir un didacticiel pour le paramétrage et la mise en œuvre d’Oracle Data Guard sur Azure ordinateurs virtuels pour une haute disponibilité et reprise après sinistre."
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

#<a name="configuring-oracle-data-guard-for-azure"></a>Configuration d’Oracle Data Guard pour Azure


Ce didacticiel montre comment définir et mettre en œuvre Oracle Data Guard dans un environnement d’ordinateurs virtuels Azure pour la haute disponibilité et reprise après sinistre. Le didacticiel est axé sur la réplication à sens unique pour les bases de données Oracle RAC non.

Oracle Data Guard prend en charge la reprise après sinistre et protection des données pour la base de données Oracle. Il est une simple, hautes performances dans le désordre solution de reprise après sinistre, la protection des données et haute disponibilité pour l’ensemble de la base de données Oracle.

Ce didacticiel suppose que vous avez déjà des connaissances théoriques et pratiques sur les concepts de reprise après sinistre et de disponibilité de base de données Oracle. Pour plus d’informations, consultez le [site web d’Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) , ainsi que les [Concepts de Oracle Data Guard et Guide d’Administration](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm).

En outre, le didacticiel suppose que vous avez déjà implémenté les conditions préalables suivantes :

- Vous avez déjà révisé la section haute disponibilité et des considérations sur la récupération après incident dans la rubrique [images Oracle Virtual Machine - considérations divers](virtual-machines-windows-classic-oracle-considerations.md) . Azure prend en charge les instances de base de données Oracle autonome mais pas Oracle Real Application Clusters (RAC Oracle) actuellement.


- Vous avez créé deux Machines virtuelles (VM) dans Azure à l’aide de la même plate-forme fournie image d’Oracle Enterprise Edition. Assurez-vous que les ordinateurs virtuels sont dans le [même service de cloud](virtual-machines-windows-load-balance.md) et dans le même réseau virtuel pour garantir qu’ils peuvent accéder à eux sur l’adresse IP privée permanente. En outre, il est recommandé de placer les ordinateurs virtuels dans le même [jeu de disponibilité](virtual-machines-windows-manage-availability.md) pour permettre d’Azure pour les placer dans des domaines d’erreur distinct et la mise à niveau des domaines. Oracle Data Guard est uniquement disponible avec l’édition entreprise de base de données Oracle. Chaque ordinateur doit avoir au moins 2 Go de mémoire et 5 Go d’espace disque. Pour obtenir les informations les plus récentes sur la plate-forme fournie de tailles de machine virtuelle, voir [Taille de Machine virtuelle pour Azure](virtual-machines-windows-sizes.md). Si vous avez besoin de volume de disque supplémentaires pour vos ordinateurs virtuels, vous pouvez attacher des disques supplémentaires. Pour plus d’informations, consultez [comment joindre un disque de données à une Machine virtuelle](virtual-machines-windows-classic-attach-disk.md).



- Vous avez défini les noms de Machine virtuelle comme « Machine1 » pour la machine virtuelle et le « Ordinateur2 » de la machine virtuelle de secours principal sur le portail classique Azure.

- Vous avez défini la variable d’environnement **ORACLE_HOME** pour pointer vers le même chemin installation racine oracle dans la principale et la mise en veille de Machines virtuelles, telles que `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Vous ouvrez une session sur votre serveur Windows en tant que membre du groupe **administrateurs** ou membre du groupe **ORA_DBA** .

Dans ce didacticiel, vous allez :

Mettre en œuvre l’environnement physique de la base de données en attente

1. Créer une base de données primaire

2. Préparer la base de données principale pour la création de la base de données en attente

    1. Activer l’enregistrement forcé

    2. Créer un fichier de mot de passe

    3. Configurer un journal de rétablissement en attente

    4. Activer l’archivage

    5. Définir les paramètres d’initialisation de base de données primaire

Créer une base de données de secours physique

1. Préparer un fichier de paramètres d’initialisation de base de données en attente

2. Configurer le récepteur et le fichier tnsnames pour prendre en charge la base de données sur les ordinateurs principales et de secours

    1. Configuration de listener.ora sur les deux serveurs pour contenir les entrées pour les deux bases de données

    2. Pour contenir les entrées pour les bases de données principales et de secours, configurez tnsnames.ora sur les ordinateurs virtuels principal et de secours. 

    3. Démarrez le récepteur et vérifiez les tnsping sur les deux ordinateurs virtuels pour les deux services.

3. Démarrer l’instance en attente dans un état de nomount

4. Utiliser RMAN pour cloner la base de données et pour créer une base de données en attente

5. Démarrer la base de données de secours physique en mode de récupération gérés

6. Vérifiez que la base de données de secours physique

> [AZURE.IMPORTANT] Ce didacticiel a été configuré et testé par rapport à la configuration matérielle et logicielle suivante :
>
>|                      | **Base de données primaire**                      | **Base de données en attente**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Version d’Oracle**   | Oracle11g Enterprise version (11.2.0.4.0) | Oracle11g Enterprise version (11.2.0.4.0) |
>| **Nom de l’ordinateur**     | Machine1                                  | Machine2                                  |
>| **Système d'exploitation** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle SID**       | TEST                                      | TEST\_STBY                                |
>| **Mémoire**           | Min 2 Go                                  | Min 2 Go                                  |
>| **Espace disque**       | Min 5 Go                                  | Min 5 Go                                  |

Pour les versions ultérieures de la base de données Oracle et Oracle Data Guard, il existe peut-être des modifications que vous devez implémenter. Pour des informations spécifiques à la version plus récentes, consultez la documentation de [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [Base de données Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) sur le site web de Oracle.

##<a name="implement-the-physical-standby-database-environment"></a>Mettre en œuvre l’environnement physique de la base de données en attente
### <a name="1-create-a-primary-database"></a>1. créer une base de données primaire

- Créer une base de données primaire « TEST » dans l’ordinateur virtuel principal. Pour plus d’informations, consultez Création et configuration d’une base de données Oracle.
- Pour afficher le nom de votre base de données, vous connecter à votre base de données que l’utilisateur SYS avec le rôle SYSDBA dans le SQL * Plus d’invite de commande et d’exécuter l’instruction suivante :

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- La requête suivante, les noms des fichiers de base de données à partir de la vue système dba_data_files :

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2. préparer la base de données principale pour la création de la base de données en attente

Avant de créer une base de données de secours, il est recommandé de vérifier que la base de données primaire est correctement configuré. Voici une liste des étapes que vous devez effectuer :

1. Activer l’enregistrement forcé
2. Créer un fichier de mot de passe
3. Configurer un journal de rétablissement en attente
4. Activer l’archivage
5. Définir les paramètres d’initialisation de base de données primaire

#### <a name="enable-forced-logging"></a>Activer l’enregistrement forcé

Pour implémenter une base de données de secours, nous devons activer « Forcé d’enregistrement » dans la base de données principale. Cette option garantit que même si une opération de « NoLogging. », enregistrement de force est prioritaire, et que toutes les opérations sont consignées dans les journaux de rétablissement. Par conséquent, nous assurer que tous les éléments de la base de données primaire sont connecté et réplication de secours comprend toutes les opérations dans la base de données principale. Pour activer la journalisation de force, exécutez l’instruction alter database :

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>Créer un fichier de mot de passe

Pour être en mesure d’expédier et d’appliquer les journaux archivés à partir du serveur principal au serveur de mise en veille, le mot de passe sys doit être identique sur les serveurs principal et secondaire. C’est la raison pour laquelle vous créez un fichier de mot de passe sur la base de données primaire et copiez-le sur le serveur de secours.

>[AZURE.IMPORTANT] Lors de l’utilisation de la base de données Oracle 12c, il existe un nouvel utilisateur, **SYSDG**, que vous pouvez utiliser pour administrer Oracle Data Guard. Pour plus d’informations, consultez [modifications de base de données Oracle 12 c version](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404).

En outre, assurez-vous que ORACLE\_environnement d’accueil est déjà défini dans ordinateur1. Dans le cas contraire, le définir comme une variable d’environnement à l’aide de la boîte de dialogue Variables d’environnement. Pour accéder à cette boîte de dialogue, démarrez l’utilitaire **système** en double-cliquant sur l’icône système dans le **Panneau de configuration**. Cliquez sur l’onglet **Avancé** , puis cliquez sur **Variables d’environnement**. Pour définir les variables d’environnement, cliquez sur le bouton **Nouveau** sous **Variables système**. Après avoir défini les variables d’environnement, fermez l’invite de commande Windows existant et ouvrir un nouveau.

Exécutez l’instruction suivante pour basculer vers Oracle\_répertoire de base, par exemple, C:\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\base de données.

    cd %ORACLE_HOME%\database

Ensuite, créez un fichier de mot de passe à l’aide de l’utilitaire de création de fichier mot de passe, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). À l’invite de commande Windows même dans Machine1, exécutez la commande suivante en définissant la valeur password comme mot de passe de **SYS**:

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Cette commande crée un fichier de mot de passe, nommé en tant que PWDTEST.ora, dans ORACLE\_HOME\\répertoire de base de données. Vous devez copier ce fichier à % ORACLE\_accueil %\\base de données d’annuaire dans Machine2 manuellement.

#### <a name="configure-a-standby-redo-log"></a>Configurer un journal de rétablissement en attente

Ensuite, vous devez configurer un journal de rétablissement en attente afin que le serveur principal peut recevoir correctement le rétablissement lorsqu’elle devient une mise en veille. Création préalable les ici permet également les journaux de rétablissement en attente à créer automatiquement la mise en veille. Il est important de configurer la mise en veille rétablir les journaux (SRL) avec la même taille que les journaux de rétablissement en ligne. La taille des fichiers journaux de rétablissement en attente en cours doit correspondre exactement à la taille des fichiers de journaux de rétablissement en ligne principal de la base de données en cours.

Exécutez l’instruction suivante dans le SQL\*PLUS Machine1 invite de commande. Le fichier de journal v$ est une vue système qui contient des informations sur les fichiers journaux de rétablissement.

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


Notez que 52428800 est de 50 Mo.

Puis, dans le SQL\*Plus de fenêtre, exécutez les instructions suivantes pour ajouter un nouveau groupe de fichiers de journal de rétablissement en attente pour une base de données de secours et spécifiez un nombre qui identifie le groupe à l’aide de la clause GROUP. Numéros de groupe permet de rendre l’administration de groupes de fichiers de journal de rétablissement en attente plus faciles :

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

Ensuite, exécutez la vue système suivante pour lister les informations sur le rétablissement des fichiers de journal. Cette opération vérifie également que les groupes de fichiers de journal de rétablissement en attente ont été créés :

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>Activer l’archivage

Ensuite, activez l’archivage en exécutant les instructions suivantes pour placer la base de données principale en mode du journal d’archive et activer l’archivage automatique. Vous pouvez activer le mode de journal d’archive en montage de la base de données, puis en exécutant la commande de journal d’archive.

Tout d’abord, ouvrez une session en tant que sysdba. À l’invite de commande Windows, exécutez :

    sqlplus /nolog

    connect / as sysdba

Ensuite, arrêter la base de données dans le SQL\*Plus d’invite de commande :

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

Ensuite, exécutez la commande de démarrage de montage pour monter la base de données. Cela garantit que le Oracle associe l’instance à la base de données spécifiée.

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

Ensuite, exécutez :

    SQL> alter database archivelog;
    Database altered.

Ensuite, exécutez le Alter instruction de base de données avec la clause ouverte pour rendre la base de données disponibles pour une utilisation normale :

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>Définir les paramètres d’initialisation de base de données primaire

Pour configurer la protection des données, vous devez créer et configurer les paramètres de mise en veille sur un fichier pfile la normale (fichier de paramètres d’initialisation de texte) première. Lorsque le fichier pfile est prêt, vous devez le convertir en un fichier de paramètre serveur (SPFILE).

Vous pouvez contrôler l’environnement de protection des données en utilisant les paramètres dans l’INIT. Fichier de ORA. Lors de la suite de ce didacticiel, vous devez mettre à jour la base de données primaire INIT. ORA afin qu’elle peut contenir les deux rôles : primaire ou secondaire.

    SQL> create pfile from spfile;
    File created.

Ensuite, vous devez modifier le fichier pfile pour ajouter les paramètres de mise en veille. Pour ce faire, ouvrez le INITTEST. OU d’un fichier à l’emplacement % ORACLE\_accueil %\\base de données. Ensuite, ajoutez les instructions suivantes au fichier INITTEST.ora. La convention d’affectation de noms pour votre INIT. ORA sont INIT\<Votre_nom_base_de_données\>. ORA.

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


Le bloc d’instruction précédente comprend trois éléments importants de l’installation :
-   **LOG_ARCHIVE_CONFIG... :** Vous définissez l’ID unique de la base de données à l’aide de cette instruction.
-   **LOG_ARCHIVE_DEST_1... :** Vous définissez l’emplacement du dossier archives locales à l’aide de cette instruction. Nous vous recommandons de créer un nouveau répertoire pour les besoins d’archivage de votre base de données et de spécifier l’emplacement d’archivage local à l’aide de cette instruction explicite plutôt que de l’utilisation par défaut dossier % d’Oracle ORACLE_HOME%\database\archive.
-   LOG_ARCHIVE_DEST_2 de **... LGWR asynchrone... :** vous définissez un processus d’écriture de journal asynchrones (LGWR) pour collecter les données de transaction de rétablissement et transmettre à des destinations en attente. Ici, le DB_UNIQUE_NAME spécifie un nom unique pour la base de données sur le serveur de secours de destination.

Une fois que le nouveau fichier de paramètres est prêt, vous devez créer le spfile à partir de celui-ci.

Tout d’abord, fermeture de la base de données :

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

Ensuite, exécutez le commande de démarrage sans montage comme suit :

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

Maintenant, créez un spfile :

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

Arrêt puis, la base de données :

    SQL> shutdown immediate;

    ORA-01507: database not mounted

Ensuite, utilisez la commande de démarrage pour démarrer une instance :

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>Créer une base de données de secours physique
Cette section se concentre sur les étapes que vous devez effectuer dans Machine2 pour préparer la base de données en attente physique.

Vous devez tout d’abord, Bureau à distance à l’ordinateur2 via le portail classique Azure.

Ensuite, sur le serveur de secours (Machine2), créer tous les dossiers nécessaires pour la base de données en attente, par exemple, C:\\\<YourLocalFolder\>\\TEST. En suivant ce didacticiel, assurez-vous que la structure de dossier correspond à la structure de dossier sur la Machine1 pour conserver tous les fichiers nécessaires, tels que les fichiers controlfile, fichiers de données, redologfiles, udump, bdump et cdump. En outre, définir la ORACLE\_accueil et ORACLE\_variables d’environnement de BASE ordinateur2. Dans le cas contraire, les définir comme une variable d’environnement à l’aide de la boîte de dialogue Variables d’environnement. Pour accéder à cette boîte de dialogue, démarrez l’utilitaire **système** en double-cliquant sur l’icône système dans le **Panneau de configuration**. Cliquez sur l’onglet **Avancé** , puis cliquez sur **Variables d’environnement**. Pour définir les variables d’environnement, cliquez sur le bouton **Nouveau** sous **Variables système**. Après avoir défini les variables d’environnement, vous devez fermer l’invite de commande Windows existant et ouvrir une nouvelle pour voir les modifications.

Ensuite, procédez comme suit :

1. Préparer un fichier de paramètres d’initialisation de base de données en attente

2. Configurer le récepteur et le fichier tnsnames pour prendre en charge la base de données sur les ordinateurs principales et de secours

    1. Configuration de listener.ora sur les deux serveurs pour contenir les entrées pour les deux bases de données

    2. Configurer tnsnames.ora sur les ordinateurs virtuels principal et de secours pour contenir les entrées pour les bases de données principales et de secours

    3. Démarrez le récepteur et vérifiez les tnsping sur les deux ordinateurs virtuels pour les deux services.

3. Démarrer l’instance en attente dans un état de nomount

4. Utiliser RMAN pour cloner la base de données et pour créer une base de données en attente

5. Démarrer la base de données de secours physique en mode de récupération gérés

6. Vérifiez que la base de données de secours physique

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1. Préparez un fichier de paramètres d’initialisation de base de données en attente

Cette section montre comment préparer un fichier de paramètres d’initialisation de la base de données en attente. Pour ce faire, copiez d’abord le INITTEST. OU d’un fichier à partir de la Machine 1 à l’ordinateur2 manuellement. Vous devez être en mesure de voir le INITTEST. OU d’un fichier dans le % ORACLE\_accueil %\\dossier de base de données sur les deux ordinateurs. Ensuite, modifiez le fichier INITTEST.ora dans Machine2 à configurer pour le rôle de secondaire comme indiqué ci-dessous :

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


Le bloc d’instruction précédente comprend deux éléments importants de l’installation :

-   **\*. LOG_ARCHIVE_DEST_1 :** vous devez créer manuellement le dossier c:\OracleDatabase\TEST_STBY\archives ordinateur2.
-   **\*. LOG_ARCHIVE_DEST_2 :** il s’agit d’une étape facultative. Vous définissez ce qu’il peut être nécessaire lorsque l’ordinateur principal est dans la maintenance et l’ordinateur de secours devient une base de données principale.

Ensuite, vous devez démarrer l’instance mise en veille. Sur le serveur de base de données de secours, entrez la commande suivante à une invite de commande Windows pour créer une instance Oracle par la création d’un service Windows :

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

La commande **Oradim** crée une instance d’Oracle, mais ne la démarre pas. Vous pouvez le trouver dans le répertoire C:\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\répertoire BIN.

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>Configurer le récepteur et le fichier tnsnames pour prendre en charge la base de données sur les ordinateurs principales et de secours
Avant de créer une base de données de secours, vous devez vous assurer que les bases de données principales et de secours dans votre configuration peuvent communiquer entre eux. Pour ce faire, vous devez configurer le port d’écoute et TNSNames, soit manuellement, soit à l’aide de l’utilitaire de configuration réseau NETCA. Il s’agit d’une tâche obligatoire lorsque vous utilisez l’utilitaire Recovery Manager (RMAN).

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>Configuration de listener.ora sur les deux serveurs pour contenir les entrées pour les deux bases de données

Bureau distant Ordinateur1 et modifier le fichier listener.ora comme indiqué ci-dessous. Lorsque vous modifiez le fichier listener.ora, assurez-vous toujours que l’ouverture et la parenthèse fermante alignent dans la même colonne. Vous trouverez le fichier listener.ora dans le dossier suivant : c:\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\réseau\\ADMIN\\.

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

Bureau à distance, suivant Machine2 et modifier le listener.ora de fichier comme suit : # listener.ora fichier de Configuration de réseau : C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>Configurer tnsnames.ora sur les ordinateurs virtuels principal et de secours pour contenir les entrées pour les bases de données principales et de secours

Bureau distant Ordinateur1 et modifier le fichier tnsnames.ora comme indiqué ci-dessous. Vous trouverez le fichier tnsnames.ora dans le dossier suivant : c:\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\réseau\\ADMIN\\.

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

Bureau à distance à l’ordinateur2 et modifier le tnsnames.ora de fichier comme suit :

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>Démarrez le récepteur et vérifiez les tnsping sur les deux ordinateurs virtuels pour les deux services.

Ouvrez une nouvelle invite de commande Windows sur des Machines virtuelles principal et de secours et exécutez les instructions suivantes :

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>Démarrer l’instance en attente dans un état de nomount
Pour configurer l’environnement pour la prise en charge de la base de données en attente sur l’ordinateur virtuel en attente (MACHINE2).

Tout d’abord, copiez le fichier de mot de passe de l’ordinateur principal (Machine1) sur l’ordinateur de secours (Machine2) manuellement. Ceci est nécessaire car le mot de passe **sys** doit être identique sur les deux ordinateurs.

Ensuite, ouvrez l’invite de commande Windows en Machine2 et configurer les variables d’environnement pour pointer vers la base de données secondaire comme suit :

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

Ensuite, démarrer la base de données de mise en veille dans un état de nomount et ensuite générer un spfile.

Démarrez la base de données :

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>Utiliser RMAN pour cloner la base de données et pour créer une base de données en attente
Vous pouvez utiliser l’utilitaire Recovery Manager (RMAN) pour effectuer une copie de sauvegarde de la base de données principale pour créer la base de données en attente physique.

Bureau à distance à l’ordinateur virtuel de secours (MACHINE2) et exécuter l’utilitaire RMAN en spécifiant une connexion complète de chaîne pour à la fois la cible (base de données principale, Machine1) et auxiliaire (en attente de base de données, Machine2) instances.

>[AZURE.IMPORTANT] N’utilisez pas l’authentification du système d’exploitation car il n’existe encore aucune base de données sur le serveur de secours.

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>Démarrer la base de données de secours physique en mode de récupération gérés
Ce didacticiel montre comment créer une base de données en attente physique. Pour plus d’informations sur la création d’une base de données logique de la méthode de mise en veille, consultez la documentation Oracle.

Ouvrez SQL\*Plus invite de commandes et activer la protection des données sur le serveur (MACHINE2) ou de secours Virtual Machine comme suit :

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Lorsque vous ouvrez la base de données de secours en mode **montage** , le journal continue de livraison et le processus de récupération gérés continue le journal d’application sur la base de données en attente. Cela garantit que la base de données secondaire reste à jour avec la base de données principale. Notez que la base de données de secours ne peut pas être accessible pour les rapports pendant ce temps.

Lorsque vous ouvrez la base de données de secours en mode **Lecture seule** , envoi de journaux de l’archive se poursuit. Mais le processus de récupération gérés s’arrête. Ainsi, la base de données en attente de plus en plus de devenir obsolètes jusqu'à ce que le processus de récupération gérés est repris. Vous pouvez accéder à la base de données de secours à des fins de création de rapports pendant ce temps mais les données peuvent ne pas reflètent les dernières modifications apportées.

En général, nous vous recommandons de conserver la base de données de secours en mode de **Monter** à jour les données dans la base de données de secours en cas de panne de la base de données principale. Toutefois, vous pouvez conserver la base de données de secours en mode **Lecture seule** pour les rapports en fonction des spécifications de votre application. Les étapes suivantes expliquent comment activer la protection des données en mode lecture seule à l’aide de SQL\*Plus :

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>Vérifiez que la base de données de secours physique
Cette section montre comment vérifier la configuration haute disponibilité en tant qu’administrateur.

Ouvrez SQL\*Plus de la fenêtre d’invite de commande et à cocher archivés rétablissement le journal sur la Machine virtuelle mise en veille (Machine2) :

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

Ouvrez SQL * Plus logfiles de commutateur et de la fenêtre invite de commandes sur l’ordinateur principal (Machine1) :

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

Vérifiez les journaux de rétablissement archivés sur la Machine virtuelle mise en veille (Machine2) :

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

Vérifiez tout écart sur la Machine virtuelle mise en veille (Machine2) :

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

Une autre méthode de vérification impossible à basculement vers la base de données de secours et ensuite tester s’il est possible d’effectuer un retour arrière à la base de données principale. Pour activer la base de données de secours sous la forme d’une base de données principale, utilisez les instructions suivantes :

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Si vous n’avez pas activé flashback sur la base de données primaire d’origine, il est recommandé de déplacer la base de données primaire d’origine et de le recréer sous la forme d’une base de données en attente.

Nous vous recommandons d’activer la base de données flashback sur le serveur principal et les bases de données de secours. Lorsqu’un basculement se produit, la base de données principale peut être flashé sauvegarder à la fois avant le basculement et rapidement convertir une base de données en attente.

##<a name="additional-resources"></a>Ressources supplémentaires
[Images de Machine virtuelle d’Oracle pour Azure](virtual-machines-windows-classic-oracle-images.md)
