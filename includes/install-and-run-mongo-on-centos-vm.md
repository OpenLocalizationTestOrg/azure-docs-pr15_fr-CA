Suivez ces étapes pour installer et exécuter MongoDB sur une machine virtuelle exécutant CentOS Linux.

> [AZURE.WARNING] Les fonctionnalités de sécurité de MongoDB, comme l’authentification et le lien de l’adresse IP, ne sont pas activées par défaut. Fonctionnalités de sécurité doivent être activées avant le déploiement de MongoDB dans un environnement de production.  Pour plus d’informations, reportez-vous à la section [sécurité et authentification](http://www.mongodb.org/display/DOCS/Security+and+Authentication) .

1. Configurer le système de gestion de Package (YUM) de sorte que vous pouvez installer MongoDB. Créez un fichier */etc/yum.repos.d/10gen.repo* pour contenir les informations à propos de votre référentiel et ajoutez le code suivant :

        [10gen]
        name=10gen Repository
        baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
        gpgcheck=0
        enabled=1

2. Enregistrez le fichier mis en pension, puis exécutez la commande suivante pour mettre à jour de la base de données de package local :

        $ sudo yum update

3. Pour installer le package, exécutez la commande suivante pour installer la dernière version disponible de MongoDB et les outils associés :

        $ sudo yum install mongo-10gen mongo-10gen-server

    Attendez que MongoDB télécharge et installe.

4. Créez un répertoire de données. Par défaut MongoDB stocke les données dans le répertoire */data/db* , mais vous devez créer ce répertoire. Pour la créer, exécutez :

        $ sudo mkdir -p /srv/datadrive/data
        $ sudo chown `id -u` /srv/datadrive/data

    Pour plus d’informations sur l’installation de MongoDB sur Linux, consultez [Démarrage rapide Unix][QuickstartUnix].

5. Pour démarrer la base de données, exécutez :

        $ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

    Tous les messages du journal seront dirigés vers le fichier */srv/datadrive/data/mongod.log* comme MongoDB serveur démarre et pré-alloue des fichiers journaux. Elle peut prendre plusieurs minutes pour MongoDB allouer les fichiers journal et démarrer l’écoute des connexions.

6. Pour démarrer l’environnement d’administration MongoDB, ouvrir une fenêtre distincte de SSH ou PuTTY et exécutez :

        $ mongo
        > db.foo.save ( { a:1 } )
        > db.foo.find()
        { _id : ..., a : 1 }
        > show dbs  
        ...
        > show collections  
        ...  
        > help  

    La base de données est créée par l’instruction insert.

7. Une fois MongoDB est installé, vous devez configurer un point de terminaison afin que MongoDB accessible à distance. Dans le portail de gestion, cliquez sur des **Machines virtuelles**, puis cliquez sur le nom de votre nouvel ordinateur virtuel, puis cliquez sur les **points de terminaison**.
    
    ![Points de terminaison][Image7]

8. Cliquez sur **Ajouter un point de terminaison** au bas de la page.
    
    ![Points de terminaison][Image8]

9. Ajouter un point de terminaison avec les paramètres suivants :

 - **Nom**: Mongo
 - **Protocole**: TCP
 - **Port Public**: 27017
 - **Port de privée**: 27017
 
 Cela permettra de MongoDB soit accessible à distance.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png
