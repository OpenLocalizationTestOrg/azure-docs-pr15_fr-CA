<properties
    pageTitle="Créer une application web de PHP-MySQL dans le Service d’application Azure et de déployer à l’aide de Git"
    description="Un didacticiel qui montre comment créer une application web PHP qui stocke les données dans MySQL et utiliser le déploiement Git sur Azure."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Créer une application web de PHP-MySQL dans le Service d’application Azure et de déployer à l’aide de Git

Ce didacticiel vous montre comment créer une application web de PHP-MySQL et comment le déployer au [Service de l’application](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide de Git. Vous allez utiliser [PHP][install-php], l’outil de ligne de commande de MySQL (partie de [MySQL][install-mysql]) et [Git] [ install-git] installé sur votre ordinateur. Vous pouvez suivre les instructions de ce didacticiel sur n’importe quel système d’exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous disposerez d’une application web PHP/MySQL exécute dans Azure.

Vous allez apprendre :

* Comment créer une application web et une base de données MySQL via le [Portail Azure][management-portal]. Par défaut, PHP est activé dans [l’Application de Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) , rien de spécial est tenu d’exécuter votre code PHP.
* Comment publier et publier à nouveau votre application Azure à l’aide de Git.
* Comment faire pour activer l’extension de Composer automatiser Composer des tâches à chaque `git push`.

En suivant ce didacticiel, vous allez créer une application web de simple inscription dans PHP. L’application sera hébergée dans les applications Web. Une capture d’écran de l’application terminée est inférieure à :

![Site de web PHP Azure][running-app]

## <a name="set-up-the-development-environment"></a>Configurer l’environnement de développement

Ce didacticiel suppose que vous avez [PHP][install-php], l’outil de ligne de commande de MySQL (partie de [MySQL][install-mysql]) et [Git] [ install-git] installé sur votre ordinateur.

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>Créer une application web et de publication de Git

Suivez ces étapes pour créer une application web et une base de données MySQL :

1. Connexion au [portail Azure][management-portal].
2. Cliquez sur l’icône **Nouveau** .

3. Cliquez sur **Afficher tous les** en regard du **marché**. 

4. Cliquez sur **Web + Mobile**, puis sur **Web app + MySQL**. Ensuite, cliquez sur **créer**.

4. Entrez un nom valide pour votre groupe de ressources.

    ![Nom de groupe de ressources de jeu][resource-group]

5. Entrez les valeurs pour votre nouvelle application web.

    ![Créer l’application web][new-web-app]

6. Entrez les valeurs pour votre nouvelle base de données, y compris les acceptez les conditions juridiques.

    ![Créer la nouvelle base de données MySQL][new-mysql-db]

7. Lorsque l’application web a été créée, vous verrez la nouvelle blade d’application web.

7. Dans les **paramètres** cliquez sur **Continue de déploiement**, puis cliquez sur _configurer les paramètres requis_.

    ![Configurer la publication Git][setup-publishing]

8. Sélectionnez **Un référentiel Git Local** pour la source.

    ![Configurer le référentiel de Git][setup-repository]


9. Pour activer la publication de Git, vous devez fournir un nom d’utilisateur et le mot de passe. Notez le nom d’utilisateur et le mot de passe que vous créez. (Si vous avez défini un référentiel Git avant, cette étape sera ignorée.)

    ![Créer des informations d’identification de publication][credentials]


## <a name="get-remote-mysql-connection-information"></a>Obtenir des informations de connexion à distance MySQL

Pour vous connecter à la base de données MySQL qui est en cours d’exécution dans les applications Web, votre avez besoin des informations de connexion. Pour obtenir des informations de connexion MySQL, procédez comme suit :

1. À partir de votre groupe de ressources, cliquez sur la base de données :

    ![Sélectionnez la base de données][select-database]

2. Dans le **paramètres de**base de données, cliquez sur **Propriétés**.

    ![Sélectionnez Propriétés][select-properties]

2. Prenez note des valeurs pour `Database`, `Host`, `User Id`, et `Password`.

    ![Remarque Les propriétés][note-properties]

## <a name="build-and-test-your-app-locally"></a>Générer et tester votre application localement

Maintenant que vous avez créé une application web, vous pouvez développer votre application localement, puis le déployer après le test.

La demande d’enregistrement est une simple application PHP qui permet de vous inscrire pour un événement en fournissant votre nom et adresse e-mail. Informations inscrites précédents s’affiche dans un tableau. Informations d’enregistrement sont stockées dans une base de données MySQL. L’application se compose d’un fichier (code de copier/coller ci-dessous) :

* **index.php**: affiche un formulaire pour l’enregistrement et une table contenant des informations de l’abonné.

Pour générer et exécuter l’application localement, suivez les étapes ci-dessous. Notez que ces étapes supposent que vous avez le PHP et MySQL Line Tool (partie de MySQL) sur votre ordinateur local, et que vous avez activé l' [extension PDO pour MySQL][pdo-mysql].

1. Se connecter au serveur MySQL distant, à l’aide de la valeur de `Data Source`, `User Id`, `Password`, et `Database` que vous avez récupérée précédemment :

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. L’invite MySQL s’affiche :

        mysql>

3. Coller la suivante `CREATE TABLE` pour créer le `registration_tbl` table dans votre base de données :

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Dans la racine de votre dossier local d’application Créer fichier de **index.php** .

5. Ouvrez le fichier **index.php** dans un éditeur de texte ou un IDE et ajoutez le code suivant et effectuer les modifications nécessaires, marquées avec `//TODO:` commentaires.


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
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
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
            // Retrieve data
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
        ?>
        </body>
        </html>

4.  Dans un terminal, accédez à votre dossier d’application et tapez la commande suivante :

        php -S localhost:8000

Vous pouvez maintenant accéder à **http://localhost:8000 /** pour tester l’application.


## <a name="publish-your-app"></a>Publier votre application

Après avoir testé votre application localement, vous pouvez le publier à des applications Web à l’aide de Git. Vous initialisez votre référentiel Git local et publier l’application.

> [AZURE.NOTE]
> Ce sont les mêmes étapes affichées dans le portail Azure à la fin de la création d’une application web et le jeu Git publication section ci-dessus.

1. (Facultatif)  Si vous avez oublié ou égaré votre URL de repostitory à distance Git, accédez aux propriétés application web sur le portail Azure.

1. Ouvrez GitBash (ou un terminal, se Git dans votre `PATH`), changez les répertoires pour le répertoire racine de votre application et exécutez les commandes suivantes :

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Vous demandera le mot de passe que vous avez créé précédemment.

    ![Envoi vers Azure via Git][git-initial-push]

2. Accédez à **http://[site name].azurewebsites.net/index.php** à l’utilisation de l’application (ces informations seront stockées sur votre tableau de bord de compte) :

    ![Site de web PHP Azure][running-app]

Après la publication de votre application, vous pouvez apporter des modifications à celui-ci et Git permet de les publier.

## <a name="publish-changes-to-your-app"></a>Publier les modifications apportées à votre application

Pour publier les modifications sur votre application, procédez comme suit :

1. Apporter des modifications à votre application localement.
2. Ouvrez GitBash (ou un terminal, informatique Git est dans votre `PATH`), changez les répertoires pour le répertoire racine de votre application et exécutez les commandes suivantes :

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Vous demandera le mot de passe que vous avez créé précédemment.

    ![Distribution de modifications du site vers Azure via Git][git-change-push]

3. Accédez à **http://[site name].azurewebsites.net/index.php** de voir votre application et les modifications que vous avez apportées :

    ![Site de web PHP Azure][running-app]

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>Automatisation de Composer avec l’extension de l’éditeur

Par défaut, le processus de déploiement git dans le Service d’application ne fait rien avec composer.json, si vous en avez dans votre projet PHP. Vous pouvez activer composer.json de traitement au cours de `git push` en activant l’extension de l’éditeur.

1. Dans votre PHP web lame de l’application dans [Azure portal][management-portal], cliquez sur **Outils** > **Extensions**.

    ![Paramètres d’Extension composer][composer-extension-settings]

2. Cliquez sur **Ajouter**, puis cliquez sur **Composer**.

    ![Ajout de l’Extension de l’éditeur][composer-extension-add]
    
3. Cliquez sur **OK** pour accepter les conditions juridiques. Cliquez sur **OK** pour ajouter l’extension.

    La lame **installé les extensions** s’affichent à présent l’extension de l’éditeur.  
    ![Mode d’Extension de composer][composer-extension-view]
    
4. Maintenant, `git add`, `git commit`, et `git push` comme dans la section précédente. Vous allez maintenant voir que Composer installe les dépendances définies dans composer.json.

    ![Succès d’Extension composer][composer-extension-success]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
