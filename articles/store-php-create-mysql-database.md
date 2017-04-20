<properties
    pageTitle="Créer et vous connecter à une base de données MySQL dans Azure"
    description="Apprenez à utiliser le portail Azure pour créer une base de données MySQL et puis connecter à partir d’une application web PHP dans Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Créer et vous connecter à une base de données MySQL dans Azure

Ce didacticiel vous montre comment créer une base de données MySQL dans [Azure portal](https://portal.azure.com) (fournisseur est [ClearDB](http://www.cleardb.com/)) et comment s’y connecter à partir d’une application web PHP en cours d’exécution dans le [Service d’application Azure](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] Vous pouvez également créer une base de données de MySQL en tant que partie d’un [modèle d’application Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Créer une base de données MySQL dans Azure portal

Pour créer une base de données MySQL dans Azure portal, effectuez les opérations suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, cliquez sur **Nouveau** > **données + stockage** > **De la base de données MySQL**.

    ![Créer une base de données MySQL dans Azure - début](./media/store-php-create-mysql-database/create-db-1-start.png)

2. Dans la nouvelle base de données MySQL [lame](azure-portal-overview.md), configurez votre nouvelle base de données MySQL comme suit (*lame*: une page de portail qui s’ouvre horizontalement) :

    - **Nom de la base de données**: entrez un nom identifiable de façon unique
    - **Abonnement**: choisissez l’abonnement à utiliser
    - **Type de base de données**: sélectionnez **partagé** pour des niveaux peu onéreux ou gratuits, ou **dédié** obtenir des ressources dédiées. 
    - **Groupe de ressources**: ajouter la base de données MySQL vers un existant, [groupe de ressources](../azure-resource-manager/resource-group-overview.md) ou de le placer dans un autre. Ressources du même groupe peuvent être facilement gérés ensemble.
    - **Emplacement**: sélectionner un emplacement près de vous. Lors de l’ajout à un groupe de ressources existant, vous êtes verrouillé à l’emplacement du groupe de la ressource.
    - **Niveau de tarification**: **Niveaux de prix**, puis sur une option de tarification (couche de**mercure** est gratuite), puis cliquez sur **Sélectionner**. 
    - **Les termes juridiques**: cliquez sur **Termes juridiques**, examinez les détails de l’achat et cliquez sur **acheter**.
    - **Fixer au tableau de bord**: sélectionnez si vous souhaitez y accéder directement depuis le tableau de bord. Ceci est particulièrement utile si vous n’êtes pas familiarisé avec la navigation de portail encore.
    
    La capture d’écran suivante est qu’un exemple de comment vous pouvez configurer votre base de données MySQL.  
    ![Créer une base de données MySQL dans Azure - configuration](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Lorsque vous avez terminé configuration, cliquez sur **créer**.

    ![Créer une base de données MySQL dans Azure - créer](./media/store-php-create-mysql-database/create-db-3-create.png)

    Vous verrez un pop-up de notification pour vous informer que le déploiement a commencé.

    ![Créer une base de données MySQL dans Azure - en cours](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Vous obtenez un autre contextuel une fois que le déploiement a réussi. Le portail s’ouvre également automatiquement votre lame de la base de données MySQL.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Se connecter à votre base de données MySQL

Pour afficher les informations de connexion pour votre nouvelle base de données MySQL, cliquez sur les **Propriétés** dans les lames de votre application web.
    
![Créer une base de données MySQL dans Azure - lame de base de données MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Vous pouvez désormais utiliser ces informations de connexion dans n’importe quelle application web. Un exemple qui montre comment utiliser les informations de connexion à partir d’une simple application PHP est disponible [ici](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Se connecter à une application web de Laravel (à partir du didacticiel PHP get démarré)

Supposons que vous venez de terminer le didacticiel [créer, configurer et de déployer une application web PHP Azure](./app-service-web/app-service-web-php-get-started.md) et avoir une application web de [Laravel](https://www.laravel.com/) en cours d’exécution dans Azure. Vous pouvez facilement ajouter des fonctionnalités de base de données à votre application Laravel. Suivez simplement les étapes ci-dessous :

>[AZURE.NOTE] Les étapes suivantes supposent que vous avez terminé le didacticiel [créer, configurer et de déployer une application web PHP Azure](./app-service-web/app-service-web-php-get-started.md).

1. Configurer l’application Laravel dans votre environnement de développement local pour pointer vers la base de données MySQL. Pour ce faire, ouvrez `.env` répertoire racine de votre application de Laravel et configurer les options de base de données MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] La lame de **Propriétés** , le nom de votre base de données MySQL peut ou ne peut ne pas être celui indiqué dans le champ **Nom de la base de données** . Il est préférable de vérifier le paramètre de base de données dans le champ de **Chaîne de connexion** . 
    >
    >![Créer une base de données MySQL dans Azure - en cours](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. Le moyen le plus rapide pour vérifier que vous avez maintenant accès de MySQL est d’utiliser [la structure de l’authentification de Laravel par défaut](https://laravel.com/docs/5.2/authentication#authentication-quickstart). Dans le terminal de ligne de commande, exécutez les commandes suivantes à partir du répertoire racine de votre application Laravel :

         php artisan migrate
         php artisan make:auth

    La première commande crée les tables dans Azure basée sur les migrations prédéfinies dans le `database/migrations` répertoire et la deuxième commande micro-capsules les vues de base et les itinéraires pour l’authentification et enregistrement de l’utilisateur.

3. Exécuter le serveur de développement maintenant :

        php artisan serve

4. Dans le navigateur, accédez à http://localhost:8000 et inscrire un nouvel utilisateur comme indiqué :

    ![Se connecter à la base de données MySQL dans Azure - Registre utilisateur](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Suivez l’invite d’interface utilisateur complète l’enregistrement. Une fois l’inscription terminée, vous serez connecté.
    
    ![Se connecter à la base de données MySQL dans Azure - Registre utilisateur](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Vous développez votre application sur la base de données MySQL dans Azure maintenant.

5. Maintenant, vous devez uniquement répliquer votre `.env` paramètres à votre application web Azure. Exécuter les commandes CLI d’Azure suivantes :

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Découvrez comment cela fonctionne dans la [configuration de l’application web Azure](./app-service-web/app-service-web-php-get-started.md#configure).

6. Ensuite, validez et pousser vers Azure les modifications locales apportées précédemment lors de l’exécution `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Accédez à l’application web Azure.

        azure site browse

8. Connectez-vous en utilisant les informations d’identification utilisateur que vous avez créé précédemment.

    ![Se connecter à la base de données MySQL dans Azure - accédez à Azure web app](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Après que vous être connecté, vous devriez voir l’écran de connexion après convivial.
    
    ![Se connecter à la base de données MySQL dans Azure - connecté](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Félicitations, votre application de web PHP dans Azure est désormais l’accès aux données de votre base de données MySQL. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).
