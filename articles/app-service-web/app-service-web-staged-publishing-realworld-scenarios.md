<properties
  pageTitle="Utiliser efficacement les environnements de DevOps pour votre application web"
  description="Apprenez à utiliser les emplacements de déploiement pour configurer et gérer plusieurs environnements de développement de votre application"
  services="app-service\web"
  documentationCenter=""
  authors="sunbuild"
  manager="yochayk"
  editor=""/>

<tags
  ms.service="app-service"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="web"
  ms.date="10/24/2016"
  ms.author="sumuth"/>

# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Utiliser efficacement les environnements de DevOps pour vos applications web

Cet article vous montre comment définir et gérer les déploiements d’applications web pour plusieurs versions de votre application, telles que le développement, la zone de transit, assurance qualité et production. Chaque version de votre application peut être considérée comme environnement de développement pour un besoin spécifique au sein de votre processus de déploiement. Par exemple environnement d’assurance qualité peut être utilisé par votre équipe de développeurs pour tester la qualité de l’application avant de vous envoyer les modifications à la production.
Configuration de plusieurs environnements de développement peut être une tâche difficile car vous devrez suivre, de gérer les ressources (calcul, application web, base de données, cache, etc.) et de déployer du code dans des environnements.

## <a name="setting-up-a-non-production-environment-stagedevqa"></a>Configuration d’un environnement hors production (scène, dév., CQ)
Une fois que vous disposez d’une application web de production opérationnel, l’étape suivante consiste à créer un environnement de non-production. Pour utiliser les emplacements de déploiement Assurez-vous que vous exécutez en mode plan **Standard** ou **Premium** application Service. Les emplacements de déploiement sont des applications web résident avec leurs propres noms d’hôtes. Éléments de configuration et de contenu Web app peuvent être transférées entre deux emplacements de déploiement, y compris l’emplacement de production. Déploiement de votre application à un emplacement de déploiement présente les avantages suivants :

1. Vous pouvez valider les modifications d’application web dans un emplacement de déploiement intermédiaire avant qu’il échange avec l’emplacement de production.
2. Déploiement d’une application web vers un emplacement de premier et il échange en production garantissent que toutes les instances de l’emplacement sont échauffés avant permutés en production. Cela élimine les interruptions de service lors du déploiement de votre application web. La redirection du trafic est transparente, et aucune demande n’est abandonnées en raison d’opérations de swap. Ce flux de travail entière peut être automatisé en configurant [Automatiquement remplacer](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app) lors de la validation de swap avant n’est pas nécessaire.
3. Après un remplacement, l’emplacement avec une application web précédemment nouvellement créé a maintenant l’application web de production précédente. Si les modifications échangées dans l’emplacement de production sont pas comme vous le souhaitiez, vous pouvez effectuer l’échange même immédiatement pour obtenir votre « application web bien connues dernier » précédent.

Pour configurer un emplacement de déploiement intermédiaire, consultez [Définir mise en environnements pour les applications web dans le Service d’application Azure](web-sites-staged-publishing.md). Chaque environnement doit inclure son propre ensemble de ressources, par exemple si vous web app utilise une base de données puis la production et la zone de transit web app doivent être à l’aide de différentes bases de données. Ajouter des ressources d’environnement développement intermédiaire comme cache pour la définition de votre environnement de développement de zone de transit, de stockage ou de base de données.

## <a name="examples-of-using-multiple-development-environments"></a>Exemples d’utilisation de plusieurs environnements de développement

Tout projet doit suivre une gestion du code source au moins deux environnements, un environnement de développement et de production, mais lorsque l’utilisation de systèmes de gestion de contenu, etc., nous pouvons exécuter dans les infrastructures d’Application émet où l’application ne gère pas ce scénario en dehors de la zone. Cela est vrai pour certaines infrastructures les plus courants abordés ci-dessous. Combien de questions viennent à l’esprit lorsque vous travaillez avec un CMS/infrastructures telles que

1. Comment diviser à différents environnements
2. Quels fichiers puis-je modifier et fonctionne un impact sur les mises à jour des versions de framework
3. Comment faire pour gérer la configuration par d’environnement
4. Comment faire pour gérer les mises à jour de la version de modules/plug-ins, mises à jour des versions de framework core

Il existe de nombreuses façons de configurer un environnement pour votre projet et les exemples ci-dessous concernent une seule de ces méthodes les applications respectifs.

### <a name="wordpress"></a>WordPress
Dans cette section, vous allez apprendre à paramétrer un workflow de déploiement à l’aide de connecteurs pour WordPress. WordPress comme la plupart des solutions CMS ne gère pas de travailler avec plusieurs environnements de développement prêts à l’emploi. Application Service Web Apps dispose de quelques fonctionnalités qui le rendent plus facile de stocker les paramètres de configuration en dehors du code.

Avant de créer un emplacement intermédiaire, configurer votre code d’application pour prendre en charge des environnements multiples. Pour prendre en charge plusieurs environnements dans WordPress, vous devez modifier `wp-config.php` sur votre application web de développement local, ajoutez le code suivant au début du fichier. Cela vous permet de choisir la configuration correcte basée sur l’environnement sélectionné votre application.

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
// local development
 $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
//single file for all azure development environments
 $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path. $config_file;
```

Créez un dossier sous la racine de l’application web appelée `config` et ajoutez des deux fichiers : `wp-config.azure.php` et `wp-config.local.php` représentant votre environnement local et azure respectivement.

Copiez le texte suivant dans `wp-config.local.php` :

```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', 'yourdatabasename');

/** MySQL database username */
define('DB_USER', 'yourdbuser');

/** MySQL database password */
define('DB_PASSWORD', 'yourpassword');

/** MySQL hostname */
define('DB_HOST', 'localhost');
/**
 * For developers: WordPress debugging mode.
 * * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', true);

//Security key settings
define('AUTH_KEY', 'put your unique phrase here');
define('SECURE_AUTH_KEY','put your unique phrase here');
define('LOGGED_IN_KEY','put your unique phrase here');
define('NONCE_KEY', 'put your unique phrase here');
define('AUTH_SALT', 'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT', 'put your unique phrase here');
define('NONCE_SALT', 'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wp_';
```

Définissant les clés de sécurité ci-dessus peut aider à empêcher le piratée de votre application web, donc utiliser des valeurs uniques. Si vous avez besoin générer la chaîne de clés de sécurité mentionnés ci-dessus, vous pouvez atteindre le générateur automatique pour créer de nouvelles clés/valeurs à l’aide de ce [lien] (https://api.wordpress.org/secret-key/1.1/salt)

Copiez le code suivant dans `wp-config.azure.php`:


``` <?php
    // MySQL settings
    /** The name of the database for WordPress */
    
    define('DB_NAME', getenv('DB_NAME'));
    
    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));
    
    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));
    
    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));
    
    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */
    
    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);
    
    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));
    
    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>Utiliser des chemins relatifs
Une dernière chose doit configurer l’application WordPress pour utiliser des chemins d’accès relatifs. WordPress stocke les informations de l’URL dans la base de données. Cela permet de déplacement de contenu à partir d’un environnement vers un autre plus difficile car vous devrez mettre à jour la base de données chaque fois que vous changez de local sur la scène ou de scène pour les environnements de production. Pour réduire le risque de problèmes dus au déploiement d’une base de données chaque fois que vous déployez dans un environnement à un autre utiliser le [plug-in de racine relatif des liens](https://wordpress.org/plugins/root-relative-urls/) qui peuvent être installés à l’aide du tableau de bord WordPress administrateur ou télécharger manuellement à partir [d’ici](https://downloads.wordpress.org/plugin/root-relative-urls.zip).


Ajoutez les entrées suivantes à votre `wp-config.php` de fichiers avant du `That's all, stop editing!` commentaire :

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Activer le plug-in par le biais de la `Plugins` menu dans tableau de bord WordPress administrateur. Enregistrer les paramètres de lien permanent pour WordPress app.

#### <a name="the-final-wp-configphp-file"></a>La dernière `wp-config.php` fichier
Les mises à jour de la base de WordPress n’affectera pas votre `wp-config.php`, `wp-config.azure.php` et `wp-config.local.php` fichiers. En fin de cette procédure `wp-config.php` fichier ressemble à ceci

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Configurer un environnement de test
En supposant que vous disposez déjà d’une application web de WordPress exécute sur Azure Web, connexion au [portail d’aperçu de gestion Azure](http://portal.azure.com) et accéder à votre application de web WordPress. Applications If pas vous pouvez en créer un à partir du marché. Pour en savoir plus, [Cliquez ici](web-sites-php-web-site-gallery.md).
Cliquez sur Paramètres -> déploiement emplacements -> Ajouter pour créer un emplacement de déploiement avec l’étape nom. Un emplacement de déploiement est une autre application web partageant les mêmes ressources que l’application web principale créé ci-dessus.

![Créez l’emplacement de déploiement de scène](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Ajouter une base de données MySQL, par exemple `wordpress-stage-db` pour votre groupe de ressources `wordpressapp-group`.

 ![Ajouter la base de données MySQL pour le groupe de ressources](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Les chaînes de connexion de votre emplacement de déploiement de scène pointer vers la nouvelle base de données, de mettre à jour `wordpress-stage-db`. Notez que votre production web app, `wordpressprodapp` et la zone de transit web app `wordpressprodapp-stage` doit pointer vers les bases de données différentes.

#### <a name="configure-environment-specific-app-settings"></a>Configurer les paramètres de l’application d’environnement spécifiques
Les développeurs peuvent stocker des paires de chaînes clé-valeur dans Azure dans le cadre des informations de configuration associées à une application web appelée de paramètres d’application. Lors de l’exécution, application Service Web Apps récupère ces valeurs pour vous automatiquement et les rend disponibles pour le code exécuté dans votre application web. Une sécurité perspective est un bon côté bénéficier depuis des informations sensibles telles que les chaînes de connexion de base de données avec les mots de passe jamais s’afficher sous forme de texte clair dans un fichier tel que `wp-config.php`.

Ce processus défini ci-dessous est utile lorsque vous effectuez qu’elle comprenne les modifications apportées au fichier et les modifications de base de données pour l’application de WordPress :
- Mise à niveau de version WordPress
- Ajouter, modifier ou mettre à niveau les plug-ins
- Ajouter, modifier ou mettre à niveau les thèmes

Configurer les paramètres d’application pour :

- informations de base de données
- Activer/désactiver la journalisation WordPress
- Paramètres de sécurité WordPress

![Paramètres de l’application pour l’application web de Wordpress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Assurez-vous que vous avez ajouté les paramètres d’application suivants de votre emplacement d’étape et application web de production. Notez que l’application web de production et test web app utilisent des bases de données différentes.
Désactivez la case à cocher de **l’Emplacement de paramètre** pour tous les paramètres de paramètres à l’exception de WP_ENV. Cela Permute la configuration de votre application web, ainsi que le contenu et les fichiers de base de données. Si le **Paramètre d’emplacement** est **activée**, les paramètres d’application de l’application web et de configuration de chaîne de connexion ne seront pas dans des environnements lors d’une opération de permutation et, par conséquent, si les modifications de la base de données sont présentes cela ne permettra pas de rupture votre application web de production.

Déployer l’application web d’environnement développement local à la scène web app et de base de données à l’aide de WebMatrix ou outils de votre choix, tels que FTP, Git ou PhpMyAdmin.

![Web Matrix publier boîte de dialogue pour WordPress web app](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Parcourir et tester votre application web intermédiaire. Compte tenu d’un scénario dans lequel le thème de l’application web doit être mis à jour, c’est l’application web intermédiaire.

![Parcourir les application web intermédiaire avant le remplacement des connecteurs](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Si tout vous paraît bon, cliquez sur le bouton **Permuter** sur votre application web intermédiaire pour déplacer votre contenu vers l’environnement de production. Dans ce cas vous remplacez l’application web et la base de données dans les environnements au cours de chaque opération de **permutation** .

![Remplacez un aperçu des modifications de WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 > [AZURE.NOTE]
 >Si vous avez un scénario où vous devez uniquement les fichiers de commande (aucune mise à jour de base de données), puis **Vérifiez** le **Paramètre d’emplacement** pour la base de données liés *paramètres d’application* et de *paramètres de chaînes de connexion* de lame de paramètre d’application web au sein du portail Azure Aperçu avant de procéder à l’échange. Dans ce cas DB_NAME, DB_HOST, DB_PASSWORD, DB_USER, paramètre de chaîne de connexion par défaut ne doit pas s’affichent dans l’aperçu des modifications lors d’un **échange**. À ce moment, avoir terminé l’opération de **remplacement** de l’application web WordPress aura les mises à jour **uniquement**les fichiers.

Avant de procéder à un échange, c’est l’application web de WordPress production ![application web de Production avant le remplacement des connecteurs](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Après l’opération d’échange, le thème a été mis à jour sur votre application web de production.

![Application web de production après le remplacement des connecteurs](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

Dans une situation lorsque vous avez besoin de **restaurer**, vous pouvez atteindre les paramètres production de l’application web et cliquez sur le bouton de **permutation** disponible pour le remplacement de l’application web et la base de données de production dans un emplacement intermédiaire. Une chose importante à retenir est que si des modifications de base de données sont incluses dans une opération de **remplacement** à un moment donné, puis la prochaine fois que vous déployez de nouveau à votre application web intermédiaire, que vous devez déployer la base de données devient la base de données en cours pour votre application web zone de transit de l’ancienne base de données de production ou de la base de données de l’étape.

#### <a name="summary"></a>Résumé
Généraliser le processus pour n’importe quelle application avec une base de données

1. Installer l’application sur votre environnement local
2. Inclure la configuration spécifique de l’environnement (local et Azure Web App)
3. Configurer vos environnements en application Service Web applications – pré-production, Production
4. Si vous avez une application de production déjà en cours d’exécution sur Azure, synchroniser votre contenu de production (fichiers/code + de la base de données) à l’environnement local et la zone de transit.
5. Développez votre application sur votre environnement local
6. Placez votre application web de production sous maintenance ou mode verrouillé et base de données de synchronisation de contenu à partir de la production pour les environnements de test et de dév.
7. Déployer dans l’environnement intermédiaire et Test
8. Déployer dans l’environnement de Production
9. Répétez les étapes 4 à 6

### <a name="umbraco"></a>Umbraco
Dans cette section, vous apprendrez comment le CMS Umbraco utilise un module personnalisé pour déployer à partir de plusieurs DevOps environnement. Cet exemple fournit une approche différente à la gestion de plusieurs environnements de développement.

[Umbraco CMS](http://umbraco.com/) est une des popular.NET SSFM solutions utilisées par de nombreux développeurs qui fournit le module [Courier2](http://umbraco.com/products/more-add-ons/courier-2) à déployer à partir de développement mis en œuvre pour les environnements de production. Vous pouvez facilement créer un environnement de développement local pour une application web de Umbraco CMS à l’aide de Visual Studio ou WebMatrix.

1. Créer une application web de Umbraco avec Visual Studio, [Cliquez ici](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget).
2. Pour créer une application web de Umbraco avec WebMatrix, [Cliquez ici](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix).

N’oubliez pas de supprimer le `install` dossier dans votre application et ne jamais télécharger vers des applications web étape ou de production. Pour ce didacticiel, j’utiliserai WebMatrix

#### <a name="set-up-a-staging-environment"></a>Configurer un environnement de test
- Créer un emplacement de déploiement, comme indiqué ci-dessus, pour l’application web de Umbraco CMS, si que vous disposez déjà d’une application web de Umbraco CMS et en cours d’exécution. Si ce n’est pas le cas, vous pouvez en créer un à partir du marché.

- Mettre à jour la chaîne de connexion de votre emplacement de déploiement de scène pointer vers la nouvelle base de données, **umbraco stade-base de données**. Votre production web app (umbraositecms-1) et la zone de transit web app (umbracositecms-1-étape) **doit** pointent vers différentes bases de données.

![Mettre à jour la chaîne de connexion pour l’application web avec la nouvelle base de données intermédiaire la zone de transit](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

- Cliquez sur **obtenir de publier les paramètres** pour l' emplacement de déploiement **étape**. Il télécharge un fichier de paramètres de publication qui stockent toutes les informations requises par Visual Studio ou de Web Matrix pour publier votre application à partir de l’application web de développement local à Azure web app.

 ![Obtenir le paramètre de la zone de transit web app de publication](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Ouvrez votre application de web de développement local dans **WebMatrix** ou **Visual Studio**. Dans ce didacticiel, j’utilise Web Matrix et vous devez d’abord importer le fichier de paramètres de publication de votre application web intermédiaire

![Importer les paramètres de publication de Umbraco à l’aide de Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Passez en revue les modifications dans la boîte de dialogue et déployer votre application web local pour votre application web d’Azure, *umbracositecms-1-étape*. Lorsque vous déployez des fichiers directement sur votre application web intermédiaire vous omet tous les fichiers dans le `~/app_data/TEMP/` dossier tel qu’il seront régénérés lors de l’application web de phase a démarré. Vous devez aussi omettre la `~/app_data/umbraco.config` fichier, trop, va être régénérée.

![Passez en revue les modifications de publier dans web matrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Après la publication avec succès l’application web locale Umbraco à la zone de transit web app, accédez à votre application web intermédiaire et exécuter quelques tests pour éliminer les problèmes.

#### <a name="set-up-courier2-deployment-module"></a>Paramétrage du module de déploiement Courier2
Avec module de [Courier2](http://umbraco.com/products/more-add-ons/courier-2) vous pouvez distribuer le contenu, les feuilles de style, les modules de développement et bien plus encore avec un simple droit à partir d’une application web intermédiaire à l’application web de production pour un déploiement gratuit tracas plus et réduire le risque de rupture de votre application web de production lors du déploiement d’une mise à jour.
Acheter une licence pour Courier2 pour le domaine `*.azurewebsites.net` et votre domaine personnalisé (par exemple, http://abc.com) une fois que vous avez acheté la licence, placez la licence téléchargée (. Fichier LIC) dans le `bin` dossier.

![Supprimer le fichier de licence dans le dossier bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Télécharger le package Courier2 à partir [d’ici](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Ouvrez une session sur votre application web de scène, par exemple http://umbracocms-site-stage.azurewebsites.net/umbraco et cliquez sur Menu **développeur** , sélectionnez **Packages**. Cliquez sur **installer** un package local

![Le programme d’installation de Package de Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Télécharger le package courier2 en utilisant le programme d’installation.

![Télécharger le package pour module de courrier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Pour configurer vous devez mettre à jour le fichier courier.config sous le dossier de **configuration** de votre application web.

```xml
<!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1.azurewebsites.net</url>
      <user>0</user>
      <!--<login>user@email.com</login> -->
      <!-- <password>user_password</password>-->
      <!-- <passwordEncoding>Clear</passwordEncoding>-->
      </repository>
 </repositories>
 ```

Sous `<repositories>`, entrez les informations utilisateur et URL du site production. Si vous utilisez le fournisseur Umbraco Membership par défaut, puis ajouter l’ID de l’utilisateur de l’Administration dans <user> section. Si vous utilisez un fournisseur d’appartenances de Umbraco personnalisé, utilisez `<login>`,`<password>` Courier2 module savoir comment vous connecter au site de production. Pour plus d’informations, consultez la [documentation](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) pour le module de courrier.

De même, installer le module de courrier sur votre site de production et le configurer point à phase web app dans son fichier courier.config respectifs comme indiqué ici

```xml
 <!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1-stage.azurewebsites.net</url>
      <user>0</user>
      </repository>
 </repositories>
```

Cliquez sur l’onglet Courier2 dans le tableau de bord Umbraco CMS web app et sélectionner des emplacements. Vous devriez voir le nom du référentiel comme indiqué dans `courier.config`. Pour ce faire, sur la production et la mise en attente des applications web.

![Référentiel de vue destination web app](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Vous permet de déployer du contenu à partir du site intermédiaire vers le site de production. Accéder à du contenu et sélectionner une page existante ou créer une nouvelle page. Je sélectionner une page existante à partir de mon application web où le titre de la page est modifié en **Route – nouveau** et cliquez sur **Enregistrer et publier**.

![Modifier le titre de page et publier](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Sélectionnez maintenant la page modifiée et *cliquez le bouton droit* pour afficher toutes les options. Cliquez sur **courrier** pour afficher la boîte de dialogue déploiement. Cliquez sur **déployer** pour lancer le déploiement

![Boîte de dialogue déploiement de module par courrier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Passez en revue les modifications et cliquez sur Continuer.

![Courier module déploiement dialogue réviser les modifications](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Journal de déploiement montre si le déploiement a réussi.

 ![Afficher les journaux de déploiement de module de courrier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Accédez à votre application web de production pour voir si les modifications sont répercutées.

 ![Recherchez l’application web de production](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Pour en savoir plus sur l’utilisation du courrier, consultez la documentation.

#### <a name="how-to-upgrade-umbraco-cms-version"></a>Comment mettre à niveau la version de Umbraco CMS

Courier ne permettra pas de déployer la mise à niveau d’une version de Umbraco CMS vers un autre. Lors de la mise à niveau de version de Umbraco CMS, vous devez vérifier les incompatibilités avec vos modules personnalisés ou des modules tiers et les bibliothèques principales de Umbraco. En tant que meilleure pratique

1. Sauvegardez toujours votre application web et la base de données avant de procéder à une mise à niveau. Sur Azure Web App, vous pouvez configurer des sauvegardes automatiques de la fonctionnalité de vos sites Web à l’aide de la sauvegarde et restauration de votre site si nécessaire à l’aide de restauration de fonctionnalité. Pour plus d’informations, consultez [comment sauvegarder votre application web](web-sites-backup.md) et [la restauration de votre application web](web-sites-restore.md).

2. Vérifiez si les packages tiers que vous utilisez sont compatibles avec la version que vous mettez à niveau vers. Page de téléchargement sur du package, vérifier la compatibilité du projet avec la version de Umbraco CMS.

Pour plus d’informations sur la mise à niveau de votre application web localement, suivez les instructions, comme indiqué [ici](https://our.umbraco.org/documentation/getting-started/set up/upgrading/general).

Une fois la mise à niveau de votre site de développement local, publier les modifications à la zone de transit web app. Tester votre application et si tout vous paraît bon, cliquez sur bouton de **remplacement** pour **Remplacer** votre site intermédiaire à l’application web de production. Lors de l’exécution de l’opération de **remplacement** , vous pouvez afficher les modifications qui seront affectées dans les configuration de votre application web. Avec cette opération de **remplacement** , nous sommes échange les applications web et les bases de données. Cela signifie que, après le remplacement de l’application web de production pointe maintenant sur umbraco stade-base de données de base de données et de zone de transit web app pointera vers umbraco prod-base de données de base de données.

![Aperçu de swap pour le déploiement Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

L’avantage de l’échange de l’application web et la base de données :
1. Vous donne la possibilité de restaurer la version antérieure de votre application web avec un autre **remplacement** , s’il y a des problèmes d’application.
2. Une mise à niveau, vous avez besoin déployer des fichiers et la base de données à partir de l’application web à l’application web de production et de la base de données de la zone de transit. Il existe de nombreuses choses peuvent mal se passer lors du déploiement de fichiers et base de données. À l’aide de la fonctionnalité **d’échange** de créneaux horaires, nous pouvons réduire les temps morts au cours d’une mise à niveau et réduire les risques d’erreurs qui peuvent survenir lors du déploiement de modifications.
3. Vous donne la possibilité de faire **A / B test** à l’aide de la fonctionnalité de [test de production](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

Cet exemple montre la souplesse de la plate-forme où vous pouvez créer des modules personnalisés similaires au module Umbraco Courier pour gérer le déploiement dans des environnements.

## <a name="references"></a>Références
[Développement Agile avec le Service d’application Azure](app-service-agile-software-development.md)

[Configurer la mise en attente des environnements pour les applications web dans le Service d’application Azure](web-sites-staged-publishing.md)

[Comment faire pour bloquer l’accès web aux emplacements de déploiement non-production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
