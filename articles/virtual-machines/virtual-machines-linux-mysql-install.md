<properties
    pageTitle="Configurer MySQL sur une VM Linux | Microsoft Azure "
    description="Pour savoir comment installer la pile de MySQL sur une machine virtuelle de Linux (Ubuntu ou RedHat la famille du système d’exploitation) dans Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>L’installation de MySQL sur Azure


Dans cet article, vous apprendrez comment installer et configurer MySQL sur une machine virtuelle Azure fonctionnant sous Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>Installation de MySQL sur votre machine virtuelle

> [AZURE.NOTE] Vous devez déjà disposer d’une machine virtuelle de Microsoft Azure exécutant Linux pour terminer ce didacticiel. Veuillez consulter le [didacticiel de la machine virtuelle Linux de Azure](virtual-machines-linux-quick-create-cli.md) pour créer et configurer une VM Linux avec `mysqlnode` comme nom de machine virtuelle et `azureuser` en tant qu’utilisateur avant de continuer.

Dans ce cas, utilisez 3306 port que le port MySQL.  

Se connecter à l’ordinateur virtuel que vous avez créé à l’aide de putty de Linux. Si c’est la première fois que vous utilisez Azure VM de Linux, voir comment utiliser putty se connecter à une VM Linux [ici](virtual-machines-linux-mac-create-ssh-keys.md).

Nous allons utiliser le package du référentiel pour installer MySQL5.6 comme exemple dans cet article. En fait, MySQL5.6 a plus d’amélioration de performances que MySQL5.5.  Plus d’informations [ici](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###<a name="how-to-install-mysql56-on-ubuntu"></a>Comment faire pour installer MySQL5.6 sur Ubuntu
Nous utiliserons ici Linux VM avec Ubuntu d’Azure.

- Étape 1 : Installer MySQL Server 5.6 basculer vers `root` utilisateur :

            #[azureuser@mysqlnode:~]sudo su -

    Installer le serveur mysql 5.6 :

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Lors de l’installation, vous verrez une poping de fenêtre de boîte de dialogue à demander à définir MySQL racine mot de passe ci-dessous et vous devez définir le mot de passe ici.

    ![image](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Entrez le mot de passe pour le confirmer.

    ![image](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Étape 2 : Connexion MySQL Server

    Lorsque vous avez terminé de l’installation du serveur MySQL, MySQL service démarrera automatiquement. Vous pouvez ouvrir une session serveur MySQL avec `root` utilisateur.
    Utilisez le sous la commande d’ouverture de session et l’entrée de mot de passe.

             #[root@mysqlnode ~]# mysql -uroot -p

- Étape 3 : Gérer le service MySQL en cours d’exécution

    (a) obtenir l’état du service de MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) Démarrer le Service MySQL

             #[root@mysqlnode ~]# service mysql start

    (c) arrêter le service MySQL

             #[root@mysqlnode ~]# service mysql stop

    (d) de redémarrer le service MySQL

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>L’installation de MySQL sur la famille du système d’exploitation de Red Hat comme CentOS, Oracle Linux
Nous utiliserons ici les VM Linux CentOS ou Oracle Linux.

- Étape 1 : Ajouter le référentiel MySQL Yum commutateur à `root` utilisateur :

            #[azureuser@mysqlnode:~]sudo su -

    Téléchargez et installez le package MySQL :

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Étape 2 : Modifiez ci-dessous le fichier à activer le référentiel MySQL pour télécharger le package MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Mise à jour chaque valeur de ce fichier ci-dessous :

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Étape 3 : Installer les MySQL MySQL référentiel installation de MySQL :

           #[root@mysqlnode ~]#yum install mysql-community-server

    Package RPM de MySQL et tous les packages associés seront installés.

- Étape 4 : Gérer le service MySQL en cours d’exécution

    (un) vérifie l’état du service du serveur MySQL :

           #[root@mysqlnode ~]#service mysqld status

    (b) vérifie si le serveur de port de MySQL par défaut est en cours d’exécution :

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) de démarrer le serveur MySQL :

           #[root@mysqlnode ~]#service mysqld start

    (d) d’arrêter le serveur MySQL :

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL ensemble à démarrer lors du démarrage système à distance :

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>L’installation de MySQL sous SUSE Linux
Nous allons utiliser Linux VM avec OpenSUSE ici.

- Étape 1 : Télécharger et installer le serveur MySQL

    Basculez vers `root` utilisateur par le biais de dessous de commande :  

           #sudo su -

    Téléchargez et installez le package de MySQL :

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Étape 2 : Gérer le service MySQL en cours d’exécution

    (un) vérifie l’état du serveur MySQL :

           #[root@mysqlnode ~]# rcmysql status

    (b) à cocher si le port par défaut du serveur MySQL :

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) de démarrer le serveur MySQL :

           #[root@mysqlnode ~]# rcmysql start

    (d) d’arrêter le serveur MySQL :

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL ensemble à démarrer lors du démarrage système à distance :

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>Étape suivante
Trouver plus d’informations sur MySQL [ici](https://www.mysql.com/)et l’utilisation.
