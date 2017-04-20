<properties 
    pageTitle="Créer une application web de PHP-MySQL dans le Service d’application Azure et de déployer à l’aide de FTP" 
    description="Un didacticiel qui montre comment créer une application web PHP qui stocke les données dans MySQL et utilisation de déploiement FTP vers Azure." 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Créer une application web de PHP-MySQL dans le Service d’application Azure et de déployer à l’aide de FTP

Ce didacticiel vous montre comment créer une application web de PHP-MySQL et comment le déployer à l’aide de FTP. Ce didacticiel suppose que vous avez [PHP][install-php], [MySQL][install-mysql], un serveur web et un client FTP installé sur votre ordinateur. Vous pouvez suivre les instructions de ce didacticiel sur n’importe quel système d’exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous disposerez d’une application web PHP/MySQL exécute dans Azure.
 
Vous allez apprendre :

* Comment créer une application web et une base de données MySQL via le portail d’Azure. Étant donné que PHP est activé dans les applications Web par défaut, rien de spécial est nécessaire pour exécuter votre code PHP.
* Comment publier votre application Azure à l’aide de FTP.
 
En suivant ce didacticiel, vous allez créer une application web de simple inscription dans PHP. L’application sera hébergée dans une application Web. Une capture d’écran de l’application terminée est inférieure à :

![Site Web PHP Azure][running-app]

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte, allez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit ne nécessaire, aucun engagement. 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>Créer une application web et de publication FTP

Suivez ces étapes pour créer une application web et une base de données MySQL :

1. Connexion au [portail Azure][management-portal].
2. Cliquez sur l’icône **+ nouvelle** sur la partie supérieure gauche du portail Azure.

    ![Créer le nouveau Site Web Azure][new-website]

3. Dans la recherche, tapez **application Web + MySQL** , puis cliquez sur **application Web + MySQL**.

    ![Personnalisé créer un nouveau Site Web][custom-create]

4. Cliquez sur **créer**. Entrez un nom de service d’application unique, un nom valide pour le groupe de ressources et d’un nouveau service.

    ![Nom de groupe de ressources de jeu][resource-group]


6. Entrez les valeurs pour votre nouvelle base de données, y compris les acceptez les conditions juridiques.

    ![Créer la nouvelle base de données MySQL][new-mysql-db]
    
7. Lorsque l’application web a été créée, vous verrez la nouvelle blade de service d’application.


6. Cliquez sur **paramètres** > **les informations d’identification de déploiement**. 

    ![Informations d’identification du déploiement de jeu][set-deployment-credentials]

7. Pour activer la publication FTP, vous devez fournir un nom d’utilisateur et le mot de passe. Enregistrer les informations d’identification et prenez note du nom d’utilisateur et vous créez un mot de passe.

    ![Créer des informations d’identification de publication][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>Générer et tester votre application localement

La demande d’enregistrement est une simple application PHP qui permet de vous inscrire pour un événement en fournissant votre nom et adresse e-mail. Informations inscrites précédents s’affiche dans un tableau. Informations d’enregistrement sont stockées dans une base de données MySQL. L’application se compose de deux fichiers :

* **index.php**: affiche un formulaire pour l’enregistrement et une table contenant des informations de l’abonné.
* **CreateTable.php**: crée la table MySQL pour l’application. Ce fichier seulement sera utilisé qu’une seule fois.

Pour générer et exécuter l’application localement, suivez les étapes ci-dessous. Notez que ces étapes supposent que vous avez PHP, MySQL, et un serveur web configuré sur votre ordinateur local et que vous avez activé l' [extension PDO pour MySQL][pdo-mysql].

1. Créer une base de données MySQL appelée `registration`. Pour cela, à partir de l’invite de commande MySQL avec cette commande :

        mysql> create database registration;

2. Dans le répertoire racine de votre serveur web, créez un dossier appelé `registration` et créer deux fichiers dans il - un appelé `createtable.php` et l’autre appelé `index.php`.

3. Ouvrir le `createtable.php` de fichiers dans un éditeur de texte ou un IDE et ajoutez le code ci-dessous. Ce code sera utilisé pour créer le `registration_tbl` la table dans le `registration` base de données.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    > [AZURE.NOTE] 
    > Vous devez mettre à jour les valeurs de <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur MySQL local et le mot de passe.

4. Ouvrez un navigateur web et accédez à [http://localhost/registration/createtable.php][localhost-createtable]. Cette opération va créer le `registration_tbl` la table dans la base de données.

5. Ouvrez le fichier **index.php** dans un éditeur de texte ou un IDE et ajouter le code HTML et CSS de base pour la page (le code PHP sera ajouté dans les étapes suivantes).

        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php

        ?>
        </body>
        </html>

6. Dans les balises PHP, ajoutez du code PHP pour la connexion à la base de données.

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > À nouveau, vous devez mettre à jour les valeurs de <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur MySQL local et le mot de passe.

7. Selon le code de connexion de base de données, ajoutez du code pour insérer des informations d’inscription dans la base de données.

        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }

8. Enfin, après le code précédent, ajoutez du code pour récupérer des données à partir de la base de données.

        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
            echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

Vous pouvez maintenant accéder à [http://localhost/registration/index.php] [ localhost-index] pour tester l’application.

##<a name="get-mysql-and-ftp-connection-information"></a>Obtenir des informations de connexion MySQL et FTP

Pour vous connecter à la base de données MySQL qui est en cours d’exécution dans les applications Web, votre avez besoin des informations de connexion. Pour obtenir des informations de connexion MySQL, procédez comme suit :

1. À partir du service d’application lame d’application web, cliquez sur le lien de groupe de ressources :

    ![Sélectionner le groupe de ressources][select-resourcegroup]

1. À partir de votre groupe de ressources, cliquez sur la base de données :

    ![Sélectionnez la base de données][select-database]

2. À partir de la base de données est résumé, sélectionnez **paramètres** > **Propriétés**.

    ![Sélectionnez Propriétés][select-properties]
    
2. Prenez note des valeurs pour `Database`, `Host`, `User Id`, et `Password`.

    ![Remarque Les propriétés][note-properties]

3. À partir de votre application web, cliquez sur le lien de **téléchargement publier le profil** en bas à droite de la page :

    ![Téléchargement publier le profil][download-publish-profile]

4. Ouvrir le `.publishsettings` le fichier dans un éditeur XML. 

3. Trouver la `<publishProfile >` élément avec `publishMethod="FTP"` qui ressemble à ceci :

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
Prenez note de la `publishUrl`, `userName`, et `userPWD` les attributs.

##<a name="publish-your-app"></a>Publier votre application

Après avoir testé votre application localement, vous pouvez le publier pour votre application web à l’aide de FTP. Toutefois, vous devez tout d’abord mettre à jour les informations de connexion de base de données dans l’application. En utilisant les informations de connexion de base de données que vous avez obtenu précédemment (dans la section **obtenir de MySQL et les informations de connexion FTP** ), de mettre à jour les informations suivantes dans **à la fois** la `createdatabase.php` et `index.php` les fichiers avec les valeurs appropriées :

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

Vous êtes maintenant prêt à publier votre application à l’aide de FTP.

1. Ouvrez votre client FTP de choix.

2. Entrez la *partie de nom d’hôte* de le `publishUrl` attribut que vous avez noté ci-dessus dans votre client FTP.

3. Entrez la `userName` et `userPWD` les attributs que vous avez noté ci-dessus inchangé dans votre client FTP.

4. Établir une connexion.

Après que vous être connecté, vous serez en mesure de télécharger des fichiers si nécessaire. Assurez-vous que vous téléchargez des fichiers dans le répertoire racine, qui est `/site/wwwroot`.

Après avoir téléchargé les deux `index.php` et `createtable.php`, accédez à **http://[site name].azurewebsites.net/createtable.php** pour créer la table de MySQL pour l’application, puis naviguez vers **http://[site name].azurewebsites.net/index.php** à l’utilisation de l’application.
 
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
