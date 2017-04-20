<properties
    pageTitle="Configurer PostgreSQL sur une VM Linux | Microsoft Azure"
    description="Découvrez comment installer et configurer PostgreSQL sur une machine virtuelle de Linux dans Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>Installer et configurer PostgreSQL sur Azure

PostgreSQL est une avancée open source de base de données similaire à Oracle et DB2. Il inclut des fonctionnalités de prêt à l’emploi comme la conformité complète d’acide, traitement transactionnel fiable et contrôle d’accès concurrentiel de plusieurs versions. Il prend également en charge les normes telles que ANSI SQL et SQL/MED (y compris les wrappers de données étrangères pour Oracle, MySQL, MongoDB et bien d’autres encore). Il est hautement extensible avec une prise en charge de plus de 12 langues procédurales, index GIN et idée, prise en charge de données spatiales et plusieurs fonctionnalités NoSQL JSON ou applications basées sur une valeur-clé.

Dans cet article, vous apprendrez comment installer et configurer PostgreSQL sur une machine virtuelle Azure fonctionnant sous Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>Installer PostgreSQL

> [AZURE.NOTE] Vous devez déjà disposer d’un ordinateur virtuel Azure exécutant Linux pour terminer ce didacticiel. Pour créer et configurer une VM Linux avant de continuer, consultez le [didacticiel d’Azure VM de Linux](virtual-machines-linux-quick-create-cli.md).

Dans ce cas, utilisez le port 1999 comme port PostgreSQL.  

Se connecter à l’ordinateur virtuel que vous avez créé à l’aide de PuTTY de Linux. Si c’est la première fois que vous utilisez une machine virtuelle Linux de Azure, voir [comment utiliser SSH avec Linux sur Azure](virtual-machines-linux-mac-create-ssh-keys.md) pour savoir comment utiliser PuTTY pour se connecter à une VM de Linux.

1. Exécutez la commande suivante pour passer à la racine (admin) :

        # sudo su -

2. Certaines distributions ont des dépendances que vous devez installer avant d’installer PostgreSQL. Recherchez votre distro dans cette liste, exécutez la commande appropriée :

    - Linux de base Red Hat :

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian Linux de base :

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux :

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Télécharger PostgreSQL dans le répertoire racine, puis décompressez le package :

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    La commande ci-dessus sont un exemple. Vous pouvez trouver l’adresse de téléchargement plus détaillée dans l' [Index de source / / pub /](https://ftp.postgresql.org/pub/source/).

4. Pour démarrer la build, exécutez ces commandes :

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. Si vous souhaitez générer tous les éléments qui peuvent être créés, y compris la documentation (HTML et les pages man) et les modules complémentaires (cotisation), exécutez la commande suivante à la place :

        # gmake install-world

    Vous devez recevoir le message de confirmation suivant :

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurer PostgreSQL

1. (Facultatif) Créez un lien symbolique pour raccourcir la référence PostgreSQL pour ne pas inclure le numéro de version :

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Créez un répertoire pour la base de données :

        # mkdir -p /opt/pgsql_data

3. Créer un utilisateur non racine et modifier le profil de cet utilisateur. Ensuite, passez à ce nouvel utilisateur (appelé le *postgres* dans notre exemple) :

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] Pour des raisons de sécurité, PostgreSQL utilise un utilisateur non racine pour initialiser, de démarrer ou d’arrêter la base de données.


4. Modifiez le fichier *bash_profile* en entrant les commandes ci-dessous. Ces lignes seront ajoutées à la fin du fichier *bash_profile* :

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. Exécutez le fichier *bash_profile* :

        $ source .bash_profile

6. Valider votre installation à l’aide de la commande suivante :

        $ which psql

    Si votre installation est réussie, vous verrez la réponse suivante :

        /opt/pgsql/bin/psql

7. Vous pouvez également vérifier la version PostgreSQL :

        $ psql -V

8. Initialiser la base de données :

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    Vous devriez recevoir la sortie suivante :

![image](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurer PostgreSQL

<!--    [postgres@ test ~]$ exit -->

Exécutez les commandes suivantes :

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modifier les deux variables dans le fichier /etc/init.d/postgresql. Le préfixe est défini sur le chemin d’accès de l’installation de PostgreSQL : **/opt/pgsql**. PGDATA est défini sur le chemin d’accès de stockage de données de PostgreSQL : **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/virtual-machines-linux-postgresql-install/no2.png)

Modifiez le fichier pour le rendre exécutable :

    # chmod +x /etc/init.d/postgresql

Démarrer PostgreSQL :

    # /etc/init.d/postgresql start

Vérifiez si le point de terminaison de PostgreSQL est activée :

    # netstat -tunlp|grep 1999

Vous devez voir la sortie suivante :

![image](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Se connecter à la base de données Postgres

Encore une fois pour basculer vers l’utilisateur postgres :

    # su - postgres

Créer une base de données Postgres :

    $ createdb events

Se connecter à la base de données d’événements que vous venez de créer :

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Créer et supprimer une table Postgres

Maintenant que vous êtes connecté à la base de données, vous pouvez créer des tables qu’il contient.

Par exemple, créer une nouvelle table de Postgres exemple à l’aide de la commande suivante :

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

Vous avez maintenant définir un tableau à quatre colonnes avec les noms de colonne suivants et les restrictions :

1. La colonne « nom » a été limitée par la commande VARCHAR comporter 20 caractères.
2. La colonne « food » indique l’élément de denrées alimentaires qui mettra de chaque personne. VARCHAR limite ce texte à moins de 30 caractères.
3. La colonne « confirmée » enregistre si la personne a RSVP'd pour le buffet. Les valeurs acceptables sont « Y » et « N ».
4. La colonne « date » indique le lors de leur inscription pour l’événement. Postgres exige que les dates soient écrits sous la forme aaaa-mm-jj.

Si la table a été créée avec succès, vous devez voir les éléments suivants :

![image](./media/virtual-machines-linux-postgresql-install/no4.png)

Vous pouvez également vérifier la structure de la table à l’aide de la commande suivante :

![image](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Ajouter des données à une table

Tout d’abord, insérez des informations dans une ligne :

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Vous devez voir ce résultat :

![image](./media/virtual-machines-linux-postgresql-install/no6.png)

Vous pouvez ajouter plus de deux personnes à la table. Voici quelques options, ou vous pouvez créer votre propre :

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Afficher les tables

Pour afficher une table, utilisez la commande suivante :

    select * from potluck;

Le résultat est :

![image](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Supprimer des données dans une table

Pour supprimer des données dans une table, utilisez la commande suivante :

    delete from potluck where name=’John’;

Cette opération supprime toutes les informations dans la ligne « Jean ». Le résultat est :

![image](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Mise à jour des données dans une table

Utilisez la commande suivante pour mettre à jour les données dans une table. Dans cet exemple, Sandy a confirmé qu’elle participe, afin que nous allons modifier son RSVP à partir des « N » à « Y » :

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>Obtenir plus d’informations sur PostgreSQL
Maintenant que vous avez terminé l’installation de PostgreSQL dans une machine virtuelle Linux de Azure, vous pouvez profiter de l’utiliser dans Azure. Pour en savoir plus sur PostgreSQL, visitez le [site Web de PostgreSQL](http://www.postgresql.org/).
