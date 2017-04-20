<properties
    pageTitle="Clusterize MySQL avec équilibrage des ensembles | Microsoft Azure"
    description="Le programme d’installation d’une équilibrage de charge haute disponibilité Linux MySQL cluster créé avec le modèle de déploiement classique sur Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>À l’aide de jeux d’équilibrage à clusterize MySQL sur Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


L’objectif de cet article est d’Explorer et d’illustrer les différentes approches disponibles pour déployer des services hautement disponibles basés sur Linux sur Microsoft Azure, exploration de haute disponibilité du serveur de MySQL comme une introduction. Une vidéo qui illustre cette approche est disponible sur [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Nous décrivons une solution de haute disponibilité de MySQL de maître unique sans partage deux nœuds en fonction de DRBD, de Corosync et de STIMULATEUR CARDIAQUE. Un seul nœud s’exécute MySQL à la fois. Lecture et écriture à partir de la ressource DRBD sont également limité à un seul nœud à la fois.

Dans la mesure où les jeux d’équilibrage de charge de Microsoft Azure nous permet de fournir à la fois la fonctionnalité de répétition alternée et la détection de point de terminaison, suppression et récupération ordonnée de l’adresse IP virtuelle n’est pas nécessaire pour une solution VIP comme les VL. L’adresse VIP est une adresse IPv4 globalement routable attribuée par Microsoft Azure, lorsque nous créons tout d’abord le service en nuage.

Autres architectures possibles pour MySQL, y compris le jour ouvrable suivant Cluster, Percona et Galera ainsi que plusieurs solutions middleware, y compris au moins sont disponibles sous la forme d’une machine virtuelle sur [VM Depot](http://vmdepot.msopentech.com). Tant que ces solutions peuvent répliquer sur monodiffusion ou multidiffusion ou diffusion et ne s’appuient sur le stockage partagé ou de plusieurs interfaces réseau, les scénarios doivent être faciles à déployer, sur Microsoft Azure.

Bien entendu ces architectures de mise en cluster peuvent être étendues à d’autres produits tels que PostgreSQL et OpenLDAP de manière similaire. Par exemple, cette procédure équilibrage avec rien partagé a été testé avec succès avec plusieurs maîtres OpenLDAP, et vous pouvez le visionner sur notre blog de Channel 9.

## <a name="getting-ready"></a>Préparation

Vous devez disposer d’un compte Microsoft Azure avec un abonnement valide en mesure de créer des ordinateurs virtuels au moins deux (2) (XS a été utilisé dans cet exemple), définissez un réseau et un sous-réseau, un groupe d’affinité et disponibilité, ainsi que la possibilité de créer des disques durs virtuels de nouveau dans la même région, comme le service en nuage et les joindre aux ordinateurs virtuels Linux.

### <a name="tested-environment"></a>Environnement testé

- Ubuntu 13.10
  - DRBD
  - Serveur MySQL
  - Corosync et STIMULATEUR CARDIAQUE

### <a name="affinity-group"></a>Groupe d’affinité

Un groupe d’affinité pour la solution est créé par le Azure classique portail défilement vers les paramètres de connexion et de création d’un nouveau groupe d’affinité. Les ressources allouées créés ultérieurement seront attribuées à ce groupe d’affinité.

### <a name="networks"></a>Réseaux

Un nouveau réseau est créé, et un sous-réseau est créé dans le réseau. Nous avons choisi un réseau 10.10.10.0/24 avec le sous-réseau qu’une seule /24 à l’intérieur.

### <a name="virtual-machines"></a>Ordinateurs virtuels

La machine virtuelle de première Ubuntu 13.10 est créée à l’aide d’une image de la galerie d’Ubuntu Endorsed et appelée `hadb01`. Un nouveau service en nuage est créé dans le processus, appelé hadb. Nous appelez de cette façon pour illustrer la nature partagée, avec équilibrage de charge qui le service aura lorsque nous ajoutons le plus de ressources. La création de `hadb01` est se déroule normalement et l’utilisation du portail. Un point de terminaison pour SSH est créé automatiquement, et notre réseau créé est sélectionné. Nous avons également créer une nouvelle disponibilité pour les ordinateurs virtuels.

Une fois que la machine virtuelle premier est créée (techniquement, lorsque le service cloud est créé) nous procéder pour créer le deuxième ordinateur virtuel, `hadb02`. Pour la deuxième VM nous utiliserons également Ubuntu 13.10 VM à partir de la galerie à l’aide du portail, mais nous allons choisir d’utiliser un service cloud existant, `hadb.cloudapp.net`, au lieu de créer un nouveau. L’ensemble du réseau et la disponibilité doit être automatiquement sélectionnée pour nous. Un point de terminaison SSH sera créé, trop.

Une fois que les deux ordinateurs virtuels ont été créés, nous tient compte de la voie SSH pour `hadb01` (TCP 22) et `hadb02` (attribué automatiquement par Azure)

### <a name="attached-storage"></a>Stockage en attachement

Nous attacher un nouveau disque pour les deux ordinateurs virtuels et créer de nouveaux disques de 5 Go dans le processus. Les disques seront hébergés dans le conteneur de disque dur virtuel en cours d’utilisation de nos disques du système d’exploitation principal. Une fois que les disques sont créés et joint n’est pas nécessaire pour que nous puissions redémarrer Linux comme le noyau verra le nouveau périphérique (généralement `/dev/sdc`, vous pouvez vérifier `dmesg` pour la sortie)

Sur chaque ordinateur virtuel nous procéder pour créer une nouvelle partition à l’aide `cfdisk` (partition principale, de Linux) et écrire la nouvelle table de partition. **Ne créez pas un système de fichiers sur cette partition** .

## <a name="setting-up-the-cluster"></a>Configuration de cluster

Dans les deux ordinateurs virtuels Ubuntu, nous devons utiliser APT pour installer Corosync, de STIMULATEUR CARDIAQUE et de DRBD. À l’aide de `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**N’installez pas de MySQL en ce moment** . Debian et les scripts d’installation Ubuntu initialisera un répertoire de données de MySQL sur `/var/lib/mysql`, mais étant donné que le répertoire sera remplacé par un système de fichiers DRBD, nous devons effectuer cette opération ultérieurement.

À ce stade, nous devons vérifier également (à l’aide de `/sbin/ifconfig`) que les deux ordinateurs virtuels utilisent les adresses du sous-réseau 10.10.10.0/24 et qu’ils peuvent ping mutuellement par nom. Si vous le souhaitez vous pouvez également utiliser `ssh-keygen` et `ssh-copy-id` pour vous assurer que les deux ordinateurs virtuels peuvent communiquer via le protocole SSH sans mot de passe.

### <a name="setting-up-drbd"></a>Configuration de DRBD

Nous allons créer une ressource DRBD qui utilise sous-jacent `/dev/sdc1` partition pour produire un `/dev/drbd1` ressource puissent être formaté à l’aide d’ext3 et utilisées dans les nœuds principaux et secondaires. Pour ce faire, ouvrez `/etc/drbd.d/r0.res` et copiez la définition suivante de la ressource. Procédez comme suit sur les deux ordinateurs virtuels :

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Après cela, initialiser la ressource à l’aide de `drbdadm` sur les deux ordinateurs virtuels :

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Et enfin, sur le serveur primaire (`hadb01`) force possession (primaire) de la ressource DRBD :

    sudo drbdadm primary --force r0

Si vous examinez le contenu de/proc/drbd (`sudo cat /proc/drbd`) sur les deux ordinateurs virtuels, vous devez voir `Primary/Secondary` sur `hadb01` et `Secondary/Primary` de `hadb02`, compatibles avec la solution à ce stade. Le disque de 5 Go sera synchronisé sur le réseau 10.10.10.0/24 sans surcoût pour les clients.

Une fois que le disque est synchronisé, vous pouvez créer le système de fichiers sur `hadb01`. Aux fins de test, nous avons utilisé ext2, mais l’instruction suivante crée un système de fichiers ext3 :

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>Montage de la ressource DRBD

Sur `hadb01` nous sommes maintenant prêts à monter les ressources DRBD. Utilisation de Debian et dérivés `/var/lib/mysql` comme répertoire de données de MySQL. Dans la mesure où nous n’avons pas installé MySQL, nous créer le répertoire et monter la ressource DRBD. On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>Configuration de MySQL

Maintenant vous êtes prêt à installer MySQL sur `hadb01`:

    sudo apt-get install mysql-server

Pour `hadb02`, vous disposez de deux options. Vous pouvez installer mysql-serveur maintenant, qui /var/lib/mysql de créer et remplir avec un nouveau répertoire de données, puis passez à supprimer le contenu. On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La seconde option consiste à basculer vers `hadb02` , puis installez le serveur mysql il (scripts d’installation remarquerez l’installation existante et ne touchez)

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Si vous ne prévoyez pas maintenant à basculement DRBD, la première option est la plus facile, bien que sans doute moins élégant. Une fois que vous définissez ceci, vous pouvez commencer à travailler sur votre base de données MySQL. Sur `hadb02` (ou une seule des serveurs est active, selon DRBD) :

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Avertissement**: cette dernière instruction désactive efficacement l’authentification de l’utilisateur racine dans cette table. Il doit être remplacé par votre niveau de production accorder des instructions et est fourni uniquement à titre d’illustration.

Vous devez également activer la mise en réseau de MySQL si vous souhaitez effectuer des requêtes à partir de l’extérieur les ordinateurs virtuels, qui est l’objectif de ce guide. Sur les deux ordinateurs virtuels, ouvrez `/etc/mysql/my.cnf` et accédez à `bind-address`, remplaçant 127.0.0.1 à 0.0.0.0. Après avoir enregistré le fichier, vous devez émettre une `sudo service mysql restart` sur votre serveur primaire en cours.

### <a name="creating-the-mysql-load-balanced-set"></a>Création de la charge de MySQL équilibrée de jeu

Nous retourner au portail et accédez à le `hadb01` VM, puis les points de terminaison. Nous va créer un nouveau point de terminaison, sélectionnez MySQL (TCP 3306) à partir de la liste déroulante et la graduation sur la zone de *jeu avec équilibrage de charge de nouveau de créer* . Nous appellerons notre point de terminaison d’équilibrage de la charge `lb-mysql`. Nous conservons la plupart des options uniquement, à l’exception de temps ce qui nous permet de réduire à 5 (secondes minimum)

Après avoir créé le point de terminaison nous atteindre `hadb02`, points de terminaison et créer un nouveau point de terminaison, mais nous allons choisir `lb-mysql`, puis sélectionnez MySQL dans le menu déroulant. Vous pouvez également utiliser la CLI Azure pour cette étape.

Pour l’instant, nous avons tout ce dont nous avons besoin pour une opération manuelle du cluster.

### <a name="testing-the-load-balanced-set"></a>Test de la charge équilibrée de jeu

Tests peuvent être exécutés à partir d’un ordinateur extérieur, à l’aide de n’importe quel client MySQL, ainsi que des applications (par exemple, phpMyAdmin en cours d’exécution sous la forme d’un site Web d’Azure) dans ce cas, nous avons utilisé outil de ligne de commande de MySQL sur une autre zone de Linux :

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Déployée

Vous pouvez simuler des basculements maintenant par arrêt MySQL, commutation principal du DRBD et redémarrer MySQL.

Sur hadb01 :

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Ensuite, sur le hadb02 :

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Une fois le basculement manuellement vous pouvez répéter votre requête à distance et qu’il doivent fonctionne parfaitement.

## <a name="setting-up-corosync"></a>Configuration de Corosync

Corosync est l’infrastructure sous-jacente de cluster requis pour STIMULATEUR fonctionner. Pour les utilisateurs de v1 et v2 pulsation (et d’autres méthodologies telles que Ultramonkey) Corosync est un fractionnement des fonctionnalités CRM, tandis que STIMULATEUR reste plus semblable à passer les pulsations de fonctionnalité.

La principale contrainte pour Corosync sur Azure est que Corosync préfère multidiffusion diffusion sur les communications de monodiffusion, mise en réseau de Microsoft Azure prend uniquement en charge monodiffusion.

Heureusement, Corosync dispose d’un mode de monodiffusion de travail, et la seule véritable contrainte est que, étant donné que tous les nœuds ne communiquent pas entre eux *State*, vous devez définir les nœuds dans vos fichiers de configuration, y compris leurs adresses IP. Nous pouvons utiliser les fichiers d’exemple de Corosync de monodiffusion et modifier uniquement lier les adresses, les listes de nœuds et répertoire d’enregistrement (Ubuntu utilise `/var/log/corosync` lors de l’utilisation de fichiers de l’exemple `/var/log/cluster`) et l’activation des outils de quorum.

**Remarque le `transport: udpu` la directive ci-dessous et les adresses IP manuellement définis pour les nœuds de**.

Sur `/etc/corosync/corosync.conf` pour les deux nœuds :

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Nous copier ce fichier de configuration sur les deux ordinateurs virtuels et que vous démarrez Corosync dans les deux nœuds :

    sudo service start corosync

Peu de temps après le démarrage du service cluster doit être établi de l’anneau en cours et doit constituer un quorum. Nous pouvons vérifier cette fonctionnalité en passant en revue les journaux ou :

    sudo corosync-quorumtool –l

Une sortie similaire à l’image ci-dessous doit suivre :

![exemple de sortie corosync-quorumtool - l](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>Configuration de STIMULATEUR CARDIAQUE

STIMULATEUR CARDIAQUE utilise le cluster à surveiller pour les ressources, définir lorsque descendre les couleurs primaires et utiliser ces ressources secondaires. Ressources peuvent être définies à partir d’un ensemble de scripts disponibles ou de scripts (init-like) LSB parmi les autres choix.

Nous souhaitons STIMULATEUR CARDIAQUE à « propriétaire » de la ressource DRBD, le point de montage et le service MySQL. STIMULATEUR CARDIAQUE peut activer et désactiver la DRBD, montez-le / umount et démarrer/arrêter MySQL dans le bon ordre lorsque quelque chose d’incorrect se produit avec le système principal, notre programme d’installation est terminée.

Lorsque vous installez STIMULATEUR pour la première fois, votre configuration doit être suffisamment simple pour quelque chose comme :

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Vérifier en exécutant `sudo crm configure show`. À présent, créez un fichier (par exemple, `/tmp/cluster.conf`) avec les ressources suivantes :

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Et maintenant le charger dans la configuration (vous suffit pour cela dans un seul nœud) :

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Assurez-vous également que le STIMULATEUR démarre à l’amorçage dans les deux nœuds :

    sudo update-rc.d pacemaker defaults

Après quelques secondes et l’utilisation de `sudo crm_mon –L`, vérifiez qu’une de vos nœuds est devenu le maître pour le cluster et toutes les ressources est en cours d’exécution. Vous pouvez utiliser le montage et ps pour vérifier que les ressources sont en cours d’exécution.

L’exemple de capture d’écran suivant `crm_mon` avec un nœud arrêté (sortie à l’aide de CTRL + C)

![nœud crm_mon s’est arrêté](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

Et cette capture d’écran montre les deux nœuds, avec un serveur maître et un esclave :

![crm_mon opérationnelle maître/esclave](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>Test de

Nous sommes prêts pour la simulation d’un basculement sur incident automatique. Il existe deux façons de procéder : souple et strict. La façon souple à l’aide de fonction d’arrêt du cluster : ``crm_standby -U `uname -n` -v on``. Utilisez cette option sur le maître, l’esclave prend le relais. N’oubliez pas que ce réglage OFF (crm_mon vous indiquera un nœud est en mode veille)

Disque dur façon est arrêter la VM principale (hadb01) via le portail ou modifier le niveau d’exécution sur l’ordinateur virtuel (arrêt, arrêt), puis nous aidons Corosync et STIMULATEUR CARDIAQUE par la signalisation permanente de la forme vers le bas. Nous pouvons tester (utiles pour les fenêtres de maintenance), mais nous pouvons également forcer le scénario par congélation simplement de la machine virtuelle.

## <a name="stonith"></a>STONITH

Il doit être possible d’émettre un arrêt de la machine virtuelle via la CLI Azure au lieu d’un script STONITH qui contrôle un périphérique physique. Vous pouvez utiliser `/usr/lib/stonith/plugins/external/ssh` en tant que base et activer STONITH dans les configuration du cluster. CLI Azure doit être installé globalement et paramètres/profil de la publication doit être chargé pour les utilisateurs du cluster.

Exemples de code pour la ressource disponible sur [GitHub](https://github.com/bureado/aztonith). Vous devez modifier la configuration du cluster en ajoutant le code suivant à `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Remarque :** le script n’effectue pas de haut/bas de chèques. La ressource SSH d’origine avait 15 chèques de ping, mais des temps de récupération pour un ordinateur virtuel d’Azure peuvent être variable plus.

## <a name="limitations"></a>Limitations

Les limitations suivantes s’appliquent :

- Le script de ressources DRBD linbit qui gère les DRBD en tant que ressource dans les utilisations de STIMULATEUR CARDIAQUE `drbdadm down` lors de l’arrêt d’un nœud, même si le nœud est sur le point en attente. Ce n’est pas idéal car l’esclave ne sera pas synchroniser la ressource DRBD alors que le maître Obtient des écritures. Si le masque n’échoue pas gracieusement, l’esclave peut prendre la relève d’un état de système de fichiers plus ancien. Il existe deux façons possibles de résoudre ce :
  - Appliquer un `drbdadm up r0` dans tous les nœuds du cluster via un pilote local (pas clusterized), ou,
  - Modifier la DRBD de linbit script assurant que `down` n’est pas appelée, dans `/usr/lib/ocf/resource.d/linbit/drbd`.
- Équilibreur de charge doit être d’au moins 5 secondes pour répondre, afin que les applications doivent être compatible avec les clusters et être plus tolérant de délai d’attente ; autres architectures peut vous aider, par exemple dans l’application files, requête middlewares, etc..
- Réglage de MySQL est nécessaire pour garantir l’écriture est effectuée à un rythme concentré et caches sont vidées sur disque aussi souvent que possible afin de minimiser les pertes de mémoire
- Écrire les performances seront dépendant de la machine virtuelle d’interconnexion de switch virtuel comme c’est le mécanisme utilisé par DRBD pour répliquer le périphérique
