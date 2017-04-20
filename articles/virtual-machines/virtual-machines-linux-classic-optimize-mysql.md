<properties
    pageTitle="Optimiser les performances de MySQL sur Linux VM | Microsoft Azure"
    description="Découvrez comment optimiser MySQL en cours d’exécution sur une machine virtuelle Azure (VM) fonctionnant sous Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>Optimisation des performances de MySQL sur Linux Azure VM

Il existe de nombreux facteurs ayant un impact sur les performances de MySQL sur Azure, à la fois dans configuration de logiciels et de la sélection du matériel virtuel. Cet article se concentre sur l’optimisation des performances par le biais de stockage, du système et les configurations de base de données.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>Utilisation de RAID sur un ordinateur virtuel Azure
Le stockage est le facteur clé qui influe sur les performances de base de données dans les environnements de cloud.  Par rapport à un disque, RAID peut fournir un accès plus rapide par le biais d’accès concurrentiel.  Pour plus d’informations, consultez [Niveaux de RAID Standard](http://en.wikipedia.org/wiki/Standard_RAID_levels) .   

Débit d’e/s disque et le temps de réponse des e/s dans Azure peuvent être considérablement améliorés via RAID. Nos tests de laboratoire afficher les e/s disque peut doubler le débit et les temps de réponse d’e/s peuvent être réduit de moitié en moyenne lorsque le nombre de disques RAID est doublé (de 2 à 4, de 4 à 8, etc.). Pour plus d’informations, consultez [l’annexe A](#AppendixA) .  

En plus d’e/s disque, MySQL performances s’améliorent lorsque vous augmentez le niveau RAID.  Pour plus d’informations, voir [L’annexe B](#AppendixB) .  

Vous pouvez également tenir compte de la taille du segment. En règle générale lorsque vous disposez d’une plus grande taille de fragment, vous obtiendrez inférieur de surcharge, en particulier pour les écritures de grands. Toutefois, lorsque la taille de segment est trop importante, il peut ajouter une charge supplémentaire et vous ne pouvez pas tirer parti de RAID. La taille par défaut actuel est de 512 Ko, ce qui est révélé optimal pour la plupart des environnements de production. Pour plus d’informations, consultez [L’annexe C](#AppendixC) .   

Veuillez noter qu’il existe des limites sur le nombre de disques que vous pouvez ajouter des types de machine virtuelle différente. Ces limites sont détaillées dans la [Machine virtuelle et les tailles de Service Cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Vous aurez besoin de 4 disques de données attachées à l’exemple de RAID dans cet article, bien que vous pouvez choisir de définir avec moins de disques RAID.  

Cet article suppose que vous avez déjà créé une machine virtuelle Linux et MYSQL installé et configuré. Pour plus d’informations sur la mise en route font référence à la procédure d’installation de MySQL sur Azure.  

###<a name="setting-up-raid-on-azure"></a>Configuration RAID sur Azure
Les étapes suivantes indiquent comment créer RAID sur Azure via le portail classique Azure. Vous pouvez également configurer RAID à l’aide de scripts de Windows PowerShell.
Dans cet exemple, nous allons configurer RAID 0 à 4 disques.  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>Étape 1 : Ajouter un disque de données à votre Machine virtuelle  

Dans la page ordinateurs virtuels du portail Azure classique, cliquez sur l’ordinateur virtuel auquel vous voulez ajouter un disque de données. Dans cet exemple, l’ordinateur virtuel est mysqlnode1.  

![][1]

Sur la page pour l’ordinateur virtuel, cliquez sur **tableau de bord**.  

![][2]


Dans la barre des tâches, cliquez sur **attacher**.

![][3]

Et puis cliquez sur **attacher le disque vide**.  

![][4]

Pour les disques de données, la **Préférence de Cache hôte** doit indiquer **Aucune**.  

Un disque vide est ajouté dans votre machine virtuelle. Répétez cette étape trois fois de plus pour que vous ayez 4 disques de données RAID.  

Vous pouvez voir les disques ajoutés dans l’ordinateur virtuel en consultant le journal des messages du noyau. Par exemple, pour voir cela sur Ubuntu, utilisez la commande suivante :  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>Étape 2 : Créer un RAID avec les autres disques
Suivez cet article pour obtenir la procédure détaillée d’installation RAID :  

[Configurez le logiciel RAID sous Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] Si vous utilisez le système de fichiers XFS, suivez les étapes ci-dessous après avoir créé des RAID.

Pour installer XFS sur Debian, Ubuntu, ou menthe de Linux, utilisez la commande suivante :  

    apt-get -y install xfsprogs  

Pour installer XFS sur Fedora, CentOS ou RHEL, utilisez la commande suivante :  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>Étape 3 : Configurer un nouveau chemin d’accès de stockage
Utilisez la commande suivante :  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>Étape 4 : Copie les données d’origine vers le nouveau chemin d’accès de stockage
Utilisez la commande suivante :  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Étape 5 : Modifier les autorisations pour que MySQL peut accéder (en lecture et en écriture) le disque de données
Utilisez la commande suivante :  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>Ajuster l’algorithme de planification d’e/s disque
Linux implémente quatre types d’e/s algorithmes de planification :  

-   Algorithme de commande NOOP (No Operation)
-   Algorithme de date d’échéance (échéance)
-   Algorithme de file d’attente juste complètement (CFQ)
-   Algorithme de période de budget (Anticipatory)  

Vous pouvez sélectionner différents planificateurs d’e/s sous différents scénarios pour optimiser les performances. Dans un environnement à accès totalement aléatoire, il n’existe pas une grande différence entre les algorithmes CFQ et de la date d’échéance pour les performances. Il est généralement recommandé pour la valeur date d’échéance pour la stabilité de l’environnement de base de données MySQL. S’il existe un grand nombre d’e/s séquentielles, CFQ peut réduire les performances d’e/s de disque.   

Pour SSD et autres équipements, à l’aide de la commande NOOP ou date d’échéance peut obtenir de meilleures performances que le planificateur par défaut.   

Du noyau 2.5, l’algorithme de planification d’e/s par défaut est la date d’échéance. À partir du noyau 2.6.18, CFQ devenait l’algorithme de planification d’e/s par défaut.  Vous pouvez spécifier ce paramètre au moment du démarrage du noyau ou modifier ce paramètre de manière dynamique lorsque le système est en cours d’exécution.  

L’exemple suivant montre comment vérifier et définir le planificateur par défaut pour l’algorithme de commande NOOP.  

Pour la famille de distribution Debian :

###<a name="step-1view-the-current-io-scheduler"></a>Étape 1 le Planificateur de l’e/s en cours d’affichage
Utilisez la commande suivante :  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Vous verrez à la suite de sortie, ce qui indique au planificateur actuel.  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Étape 2. Changer le périphérique actuel (/ dev/sda) d’e/s de l’algorithme de planification
Utilisez les commandes suivantes :  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Ce paramétrage pour/dev/sda seul n’est pas utile. Il doit être défini sur tous les disques de données contenant la base de données.  

Vous devez voir la sortie suivante, qui indique que grub.cfg a été reconstruite avec succès et que le planificateur par défaut a été mis à jour NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Pour la famille de distribution Redhat, vous devez uniquement la commande suivante :   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>Configurer les paramètres d’opérations système fichier
Une meilleure solution consiste à désactiver la fonctionnalité de journalisation atime sur le système de fichiers. Atime est l’heure de dernier accès fichier. À chaque accès à un fichier, le système de fichiers enregistre l’horodatage dans le journal. Toutefois, cette information est rarement utilisée. Vous pouvez le désactiver si vous n’avez pas besoin, qui permet de réduire le temps d’accès disque global.  

Pour désactiver atime journalisation, vous devez modifier le fstab trouver fichier de configuration de système fichier et ajoutez l’option **noatime** .  

Par exemple, modifiez le fichier/etc/fstab vim, ajout de la noatime comme indiqué ci-dessous.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Remontez le système de fichiers avec la commande suivante :  

    mount -o remount /RAID0

Tester le résultat modifié. Notez que lorsque vous modifiez le fichier de test, le temps d’accès n'est pas à jour.  

Avant d’exemple :     

![][5]

Après l’exemple :

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Augmenter le nombre maximal de handles du système d’accès concurrentiel haute
MySQL est la base de données de haute simultanéité. Nombre de handles simultanées est 1024 pour Linux, qui n’est pas toujours suffisante. **Utilisez les étapes suivantes pour augmenter les poignées simultanées maximum du système pour prendre en charge la haute simultanéité de MySQL**.

###<a name="step-1-modify-the-limitsconf-file"></a>Étape 1 : Modifier le fichier limits.conf
Ajoutez les quatre lignes suivantes dans le fichier /etc/security/limits.conf pour augmenter les poignées simultanées autorisées maximum. Notez que le nombre maximal que le système peut prendre en charge 65536.   

    * nofile souple 65536
    * nofile dur 65536
    * nproc souple 65536
    * nproc dur 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>Étape 2 : Mise à jour du système pour les nouvelles limites
Exécutez les commandes suivantes :  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>Étape 3 : S’assurer que les limites sont mis à jour au démarrage
Placez les commandes de démarrage suivant dans le fichier /etc/rc.local afin qu’il prendra effet lors de chaque démarrage.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>Optimisation de base de données MySQL
Vous pouvez utiliser la même stratégie de réglage de performances pour configurer MySQL sur Azure comme sur un ordinateur local.  

Les règles d’optimisation d’e/s principales sont les suivantes :   

-   Augmenter la taille du cache.
-   Réduire le temps de réponse d’e/s.  

Pour optimiser les paramètres du serveur MySQL, vous pouvez mettre à jour le fichier my.cnf, qui est le fichier de configuration par défaut pour les ordinateurs serveur et client.  

Les éléments de configuration suivants sont les principaux facteurs qui affectent les performances de MySQL :  

-   **innodb_buffer_pool_size**: le pool de mémoire tampon contient des données mises en mémoire tampon et l’index. Cela est généralement définie à 70 % de la mémoire physique.
-   **innodb_log_file_size**: il s’agit de la taille du journal de rétablissement. Journaux de rétablissement vous permet de vous assurer que les opérations d’écriture sont rapides, fiables et récupérables après une panne. Cette option est définie à 512 Mo, ce qui vous donne suffisamment d’espace disque pour la journalisation des opérations d’écriture.
-   **max_connections**: parfois applications ne pas ferment les connexions correctement. Une valeur plus grande vous accordez au serveur davantage de temps pour recycler des connexions inactif. Le nombre maximal de connexions est 10000, mais la valeur maximale recommandée est de 5000.
-   **Innodb_file_per_table**: ce paramètre Activer ou désactiver la possibilité de InnoDB pour stocker des tables dans des fichiers distincts. Activer l’option permet de garantir que plusieurs opérations d’administration avancées peuvent être appliquées efficacement. À partir du point de vue des performances, il peut accélérer la transmission d’espace table et optimiser les performances de gestion de débris. Par conséquent, le paramètre recommandé pour ce est activée.</br>
    À partir de MySQL 5.6, le paramètre par défaut est ON. Par conséquent, aucune action n’est requise. Pour les autres versions, qui est antérieure à 5.6, paramètres par défaut est OFF. Cette suite de l’activation est obligatoire. Et doit appliquer avant le chargement de données, dans la mesure où seules les tables nouvellement créées sont concernées.
-   **innodb_flush_log_at_trx_commit**: valeur par défaut est 1, avec la portée la valeur 0 ~ 2. La valeur par défaut est l’option la plus appropriée pour autonome de base de données MySQL. Le paramètre 2 permet l’intégrité des données la plupart des et est adapté dans MySQL cluster maître. La valeur 0 permet de perte de données, ce qui peut affecter la fiabilité, dans certains cas avec de meilleures performances et est adapté aux esclaves dans un cluster de MySQL.
-   **Innodb_log_buffer_size**: le tampon du journal permet d’exécuter sans avoir à le vider le journal sur le disque avant que les transactions sont validées les transactions. Toutefois, s’il existe des objets binaires volumineux ou un champ de texte, le cache sera consommé très rapidement et e/s du disque fréquentes sera déclenchée. Il est mieux augmenter la taille de la mémoire tampon si la variable d’état de Innodb_log_waits n’est pas 0.
-   **query_cache_size**: la meilleure option consiste à désactiver dès le début. Query_cache_size la valeur 0 (qui est maintenant le paramètre par défaut dans MySQL 5.6) et utiliser d’autres méthodes pour accélérer les requêtes.  

Pour comparer les performances après l’optimisation, consultez [L’annexe D](#AppendixD) .


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Activez le journal de requêtes lentes MySQL pour analyser le goulot d’étranglement de performances
Le journal des requêtes lentes MySQL peut vous aider à identifier les requêtes lentes de MySQL. Après avoir activé le journal des requêtes lentes MySQL, vous pouvez utiliser des outils de MySQL comme **mysqldumpslow** pour identifier le goulot d’étranglement de performances.  

Notez que par défaut il n'est pas activé. Activer le journal de requêtes lentes, certaines ressources de l’UC peut consommer. Par conséquent, il est recommandé d’activer cette temporairement pour résoudre les goulots d’étranglement des performances.

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>Étape 1 : Modifier le fichier de my.cnf en ajoutant les lignes suivantes à la fin   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>Étape 2 : Redémarrez le serveur mysql
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>Étape 3 : Vérifiez si le paramètre est pris en compte à l’aide de la commande « Afficher »

![][7]   

![][8]

Dans cet exemple, vous pouvez voir que la fonctionnalité de requête lente a été activée. Vous pouvez ensuite utiliser l’outil **mysqldumpslow** pour déterminer les goulots d’étranglement et d’optimiser les performances, comme l’ajout d’index.





##<a name="appendix"></a>Appendice

Voici des exemples de données test performances produits sur l’environnement lab ciblée, ils fournissent la générales sur les tendances de données de performances avec différentes approches, d’optimisation des performances, mais les résultats peuvent varier dans différentes versions d’environnement ou d’un produit.

<a name="AppendixA"></a>Annexe a :  
**Performances de disque (IOPS) avec des niveaux RAID différents**


![][9]

**Commandes de test :**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE. Remarque : La charge de travail de ce test utilise les 64 threads, essayez d’atteindre la limite supérieure de RAID.

<a name="AppendixB"></a>Annexe b :  
**Comparaison des performances (débit) de MySQL avec des niveaux RAID différents**   
(Système de fichiers XFS)


![][10]  
![][11]

**Commandes de test :**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparaison des performances (OLTP) MySQL avec des niveaux RAID différents**  
![][12]

**Commandes de test :**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Annexe c :   
**Comparaison des performances (IOPS) de disque pour les tailles de segment différent**  
(Système de fichiers XFS)


![][13]

**Commandes de test :**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Notez la taille du fichier utilisée pour ce test est respectivement de 30 Go et 1 Go, avec RAID 0(4 disks) XFS fichier système.


<a name="AppendixD"></a>Annexe d :  
**Comparaison des performances (débit) MySQL avant et après l’optimisation**  
(Système de fichiers XFS)


![][14]

**Commandes de test :**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Le paramètre de configuration pour optmization et par défaut est la suivante :**

|Paramètres |Par défaut    |optmization
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |Aucun   |7G
|**innodb_log_file_size**   |5M |512 MO
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8 MO |128M
|**query_cache_size**   |16M    |0


Paramètres de configuration de l’optimisation de plus en plus détaillées, consultez les instructions officielles de mysql.  

[http://dev.MySQL.com/doc/refman/5.6/en/InnoDB-configuration.HTML](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.MySQL.com/doc/refman/5.6/en/InnoDB-Parameters.HTML#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Environnement de test**  

|Matériel   |Détails
|-----------|-------
|Unité centrale    |AMD Opteron(tm) processeur 4171 HE / 4 cœurs
|Mémoire |14G
|disque   |10G/disque
|système d’exploitation |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png
