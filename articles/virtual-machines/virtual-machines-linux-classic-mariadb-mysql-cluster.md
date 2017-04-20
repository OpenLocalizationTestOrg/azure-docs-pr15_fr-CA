<properties
    pageTitle="Un cluster de MariaDB (MySQL) en cours d’exécution sur Azure"
    description="Créer un MariaDB + Galera MySQL cluster Azure machines virtuelles en fonctionnement"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>MariaDB (MySQL) cluster - didacticiel Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  Cluster de MariaDB Enterprise est désormais disponible sur le marché d’Azure.  La nouvelle offre de déployer automatiquement un cluster de MariaDB Galera sur ARM. Vous devez utiliser la nouvelle offre de https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 

Nous créons un cluster multimaître de [Galera](http://galeracluster.com/products/) de [MariaDBs](https://mariadb.org/en/about/), un remplacement dans le désordre robuste, évolutive et fiable pour MySQL, pour travailler dans un environnement hautement disponible sur Azure Virtual Machines.

## <a name="architecture-overview"></a>Présentation de l’architecture

Cette rubrique effectue les étapes suivantes :

1. Créer un cluster à 3 nœuds
2. Séparer les disques de données à partir du disque du système d’exploitation
3. Créer les disques de données dans le paramètre de RAID 0/agrégés pour augmenter l’IOPS
4. L’équilibreur de charge Azure permet d’équilibrer la charge pour les 3 nœuds
5. Pour réduire le travail répétitive, créer une image de machine virtuelle contenant MariaDB + Galera et l’utiliser pour créer le cluster autre machines virtuelles.

![Architecture](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  Cette rubrique utilise les outils [CLI d’Azure](../xplat-cli-install.md) , veillez à les télécharger et de les connecter à votre abonnement Azure en suivant les instructions. Si vous avez besoin d’une référence pour les commandes disponibles dans l’infrastructure du langage commun Azure, consultez ce lien pour la [référence de la commande CLI d’Azure](../virtual-machines-command-line-tools.md). Aussi vous devez [créer un code SSH pour l’authentification] et prenez note de l' **emplacement du fichier .pem**.


## <a name="creating-the-template"></a>Création du modèle

### <a name="infrastructure"></a>Infrastructure

1. Créer un groupe d’affinité pour contenir les ressources entre eux

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Créer un réseau virtuel

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Créer un compte de stockage pour héberger tous nos disques. Notez que vous ne devez pas placer plus de 40 largement utilisé disques sur le même compte de stockage pour éviter de passer par la limite de compte 20 000 IOPS stockage. Dans ce cas, nous sommes éloignées de ce numéro afin que nous allons stocker toutes les informations sur le même compte pour plus de simplicité

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Trouver le nom de l’image de la Machine virtuelle de 7 CentOS

        azure vm image list | findstr CentOS
Vous obtiendrez quelque chose comme `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Utilisez le nom de l’étape suivante.

4. Créer le modèle de machine virtuelle remplaçant **/path/to/key.pem** avec le chemin d’accès où vous avez enregistré le code SSH généré .pem

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Attacher des disques de données de 4 x 500 Go de la machine virtuelle pour une utilisation dans la configuration RAID

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. SSH dans le modèle de machine virtuelle que vous avez créée à le **mariadbhatemplate.cloudapp.net:22** se connecter à l’aide de votre clé privée.

### <a name="software"></a>Logiciel

1. Obtenir la racine

        sudo su

2. Installer la prise en charge RAID :

     - Installation de mdadm

                yum install mdadm

     - Créer la configuration RAID0/répartition avec un système de fichiers EXT4

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - Créer le répertoire de point de montage

                mkdir /mnt/data

     - Récupérer l’UUID du périphérique RAID nouvellement créé

                blkid | grep /dev/md0

     - Modifier/etc/fstab

                vi /etc/fstab

     - Ajouter le périphérique là pour activer la mouting automatique lors du redémarrage de remplacement de l’UUID par la valeur obtenue à partir de la commande **blkid** avant

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Monter la nouvelle partition

                mount /mnt/data

3. Installation de MariaDB :

     - Créez le fichier MariaDB.repo :

                vi /etc/yum.repos.d/MariaDB.repo

     - Remplissez-le avec le dessous de contenu

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - Supprimer le suffixe existant et mariadb-libs pour éviter les conflits

            yum remove postfix mariadb-libs-*

     - Installer MariaDB avec Galera

            yum install MariaDB-Galera-server MariaDB-client galera

4. Déplacer le répertoire de données de MySQL vers le périphérique en mode bloc RAID

     - Copiez le répertoire de MySQL en cours dans son nouvel emplacement et supprimer l’ancien répertoire.

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - Définir des autorisations sur le nouveau répertoire en conséquence

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Créer un lien symbolique pointant vers le répertoire ancien vers le nouvel emplacement sur la partition RAID

            ln -s /mnt/data/mysql /var/lib/mysql

5. Étant donné [que SELinux interférera avec les opérations de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), il est nécessaire de le désactiver pour la session en cours (jusqu'à ce qu’une version compatible s’affiche). Modifier `/etc/selinux/config` pour désactiver les redémarrages suivants :

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. Valider des séries de MySQL

    - Démarrer MySQL

            service mysql start

    - Sécuriser l’installation de MySQL, définir le mot de passe racine, supprimez les utilisateurs anonymes, désactivation de la connexion à distance racine et la suppression de la base de données de test

            mysql_secure_installation

    - Créer un utilisateur dans la base de données pour les opérations de cluster et, éventuellement, de vos applications

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Arrêter de MySQL

            service mysql stop

7. Créer l’espace réservé de configuration

    - Modifier la configuration de MySQL pour créer un espace réservé pour les paramètres de cluster. Ne remplacez pas le **`<Vairables>`** ou annulez le commentaire maintenant. Cette opération est effectuée une fois que nous créons une machine virtuelle à partir de ce modèle.

            vi /etc/my.cnf.d/server.cnf

    - Modifiez la section **[galera]** et nettoyez-le

    - Modifiez la section **[mariadb]**

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Ouvrir les ports requis sur le pare-feu (à l’aide de la FirewallD sur CentOS 7)

    - MySQL :`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA :`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - IST DE GALERA :`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC :`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Rechargez le pare-feu :`firewall-cmd --reload`

9.  Optimiser le système pour des performances. Reportez-vous à cet article sur la [stratégie d’optimisation des performances](virtual-machines-linux-classic-optimize-mysql.md) pour plus de détails

    - Modifiez de nouveau le fichier de configuration de MySQL

            vi /etc/my.cnf.d/server.cnf

    - Modifiez la section **[mariadb]** et ajoutez le ci-dessous

    > [AZURE.NOTE] Il est recommandé que **innodb\_tampon\_pool_size** à 70 % de la mémoire de vos machines virtuelles. Il a été défini à 2,45 Go ici pour le support Azure VM de 3,5 Go de RAM.

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. Arrêter MySQL, désactiver le service MySQL de s’exécuter au démarrage pour éviter d’avoir à réorganiser le cluster lors de l’ajout d’un nouveau nœud et deprovision de l’ordinateur.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. Capturer la machine virtuelle via le portail. (Actuellement, outils de [problème 1268 # dans l’infrastructure du langage commun Azure] décrit le fait que les images capturées par les outils Azure CLI ne capturent pas les disques de données).

    - Arrêt de l’ordinateur par le biais du portail
    - Cliquez sur Capture et spécifier le nom de l’image en tant que **mariadb-galera-image** et fournir une description et vérifier « J’ai exécuté waagent ».
    ![Capture de la Machine virtuelle](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![Capture la Machine virtuelle](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>Création du cluster

Permet de créer des ordinateurs virtuels de 3 sur le modèle que vous venez de créer et puis configurez et démarrez le cluster.

1. Créer la première machine virtuelle des 7 CentOS à partir de l’image de **mariadb-galera-image** que vous avez créé, en fournissant le nom de réseau virtuel **mariadbvnet** et le sous-réseau **mariadb**, machine de taille **moyenne**, en passant le Service en nuage nom à **mariadbha** (ou le nom que vous souhaitez accéder via mariadbha.cloudapp.net), définissant le nom de cette machine pour être **mariadb1** et le nom d’utilisateur pour être **azureuser**et les certificats de l’activation de l’accès SSH et en passant le SSH .pem fichier et le remplacement de **/path/to/key.pem** avec le chemin d’accès où vous stocké le SSH .pem généré.

    > [AZURE.NOTE] Les commandes ci-dessous sont réparties sur plusieurs lignes pour plus de clarté, mais vous devez entrer chacun une seule ligne.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. Créer 2 plus de Machines virtuelles en _vous connectant_ à la actuellement créées **mariadbha** Service en nuage, modifier le **nom de la machine virtuelle** , ainsi que la **voie SSH** à un port unique pas en conflit avec les autres ordinateurs virtuels dans le même Service de Cloud.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
et pour MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. Vous devez obtenir l’adresse IP interne de chacune des 3 ordinateurs virtuels pour l’étape suivante :

    ![Obtention de l’adresse IP](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. SSH dans les ordinateurs 3 virtuels et et modifier le fichier de configuration sur chaque

        sudo vi /etc/my.cnf.d/server.cnf

    commentaire de **`wsrep_cluster_name`** et **`wsrep_cluster_address`** en supprimant la **#** au début et à la validation qu’ils sont en effet que vous souhaitez.
    En outre, remplacez **`<ServerIP>`** de **`wsrep_node_address`** et **`<NodeName>`** dans **`wsrep_node_name`** d’adresses IP de l’ordinateur et nom respectivement et supprimez les commentaires de ces lignes ainsi.

5. Démarrer le cluster sur MariaDB1 et laissez-le s’exécuter au démarrage

        sudo service mysql bootstrap
        chkconfig mysql on

6. Démarrez MySQL sur MariaDB2 et MariaDB3 et laissez-le s’exécuter au démarrage

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>Le cluster d’équilibrage de la charge
Lorsque vous avez créé les ordinateurs virtuels en cluster, vous les avez ajoutées dans une disponibilité ensemble appelé **clusteravset** pour s’assurer qu’ils sont placés sur les différentes pannes et mise à jour de domaines et que Azure jamais effectue la maintenance sur toutes les machines à la fois. Cette configuration répond à la configuration requise pour la prise en charge par cet accord de niveau de Service (SLA) Azure.

Maintenant, vous utilisez l’équilibrage de charge Azure afin d’équilibrer les demandes entre nos 3 nœuds.

Exécuter le sous commandes sur votre ordinateur à l’aide de la CLI d’Azure.
La structure de paramètres de commande est la suivante :`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Enfin, étant donné que l’infrastructure du langage commun définit l’intervalle de sonde d’équilibrage de la charge à 15 secondes (qui peut être un peu trop longs), modifiez-la dans le portail sous des **points de terminaison** pour un des ordinateurs virtuels

![Modifier le point de terminaison](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

Cliquez ensuite sur la valeur Reconfigure The Load-Balanced et aller

![RECONFIGURE charge équilibrée](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

modifier l’intervalle de sonde à 5 secondes, puis enregistrer

![Modification de l’intervalle sonde](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>Validation du cluster

Le travail est terminé. Le cluster est à présent accessible à `mariadbha.cloudapp.net:3306` qui atteint l’équilibreur de charge et acheminer les demandes entre les ordinateurs 3 virtuels efficacement et sans heurts.

Utiliser le client MySQL favori pour vous connecter, ou simplement vous connecter à partir d’un des ordinateurs virtuels pour vérifier que l’utilisation de ce cluster.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Puis créez une nouvelle base de données et le remplir avec des données

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Le tableau ci-dessous à

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un 3 nœud MariaDB + Galera cluster hautement disponible sur les ordinateurs virtuels Azure CentOS 7 en cours d’exécution. Les ordinateurs virtuels sont équilibrées avec l’équilibreur de charge Azure.

Vous souhaiterez peut-être Examinons [un autre moyen de cluster MySQL sur Linux](virtual-machines-linux-classic-mysql-cluster.md) et les moyens [d’optimiser et de test des performances de MySQL sur Azure Linux VM](virtual-machines-linux-classic-optimize-mysql.md).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[créer un code SSH pour l’authentification]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[problème #1268 dans l’infrastructure du langage commun Azure]:https://github.com/Azure/azure-xplat-cli/issues/1268
