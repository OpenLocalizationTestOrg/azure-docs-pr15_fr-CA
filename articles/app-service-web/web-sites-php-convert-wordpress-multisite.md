<properties 
    pageTitle="Convertir WordPress Multisite dans le Service d’application Azure" 
    description="Apprenez à tirer d’une application de web WordPress existante créée par le biais de la galerie dans Azure et la convertir en WordPress Multisite" 
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



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Convertir WordPress Multisite dans le Service d’application Azure

## <a name="overview"></a>Vue d’ensemble

*Par [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Dans ce didacticiel, vous allez apprendre à prendre une application de web WordPress existante créée par le biais de la galerie dans Azure et le convertir en une installation WordPress Multisite. En outre, vous apprendrez comment affecter un domaine personnalisé à chacun des sous-sites dans votre installation.

Il est supposé que vous disposez d’une installation existante de WordPress. Si vous ne le faites pas, veuillez suivre les instructions fournies dans la [Création d’un site web de WordPress à partir de la galerie dans Azure][website-from-gallery].

Convertir un WordPress existant seul site installation Multisite est généralement relativement simple, et la plupart des étapes initiales ici proviennent directement [Créer un réseau] [ wordpress-codex-create-a-network] page sur le [WordPress Codex](http://codex.wordpress.org).

Allons-y.

## <a name="allow-multisite"></a>Autoriser la fonctionnalité Multisite

Vous devez d’abord activer le Multisite par le biais de la `wp-config.php` de fichiers avec le **WP\_autoriser\_MULTISITE** constante. Il existe deux méthodes pour modifier les fichiers de votre application web : le premier est par le biais de FTP et la deuxième à Git. Si vous ne connaissez pas comment installer une ou l’autre de ces méthodes, consultez les didacticiels suivants :

* [Site web PHP avec MySQL et FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site web PHP avec MySQL et Git][website-w-mysql-and-git-git-setup]

Ouvrir le `wp-config.php` de fichier avec l’éditeur de votre choix et ajoutez le code suivant ci-dessus le `/* That's all, stop editing! Happy blogging. */` ligne.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Veillez à enregistrer le fichier et le télécharger sur le serveur.

## <a name="network-setup"></a>Programme d’installation du réseau

Ouvrez une session dans vers la zone de *wp-admin* de votre site web app et vous devez voir un nouvel élément dans le menu **Outils** appelé **Le programme d’installation de réseau**. Cliquez sur **Mise en réseau** et remplissez les détails de votre réseau.

![Écran de configuration de réseau][wordpress-network-setup]

Ce didacticiel utilise le schéma de site de *sous-répertoires* , car il doit toujours fonctionner, et nous configurerons domaines personnalisés pour chaque sous-site Web plus loin dans le didacticiel. Toutefois, il doit être possible pour un sous-domaine installer si vous mappez un domaine par le biais de caractères génériques [Azure Portal](https://portal.azure.com) et la configuration DNS correctement l’installation.

Pour plus d’informations sur le sous-domaine vs paramétrages du sous-répertoire voir les [Types de réseau multisite] [ wordpress-codex-types-of-networks] l’article sur le WordPress Codex.

## <a name="enable-the-network"></a>Activer le réseau

Le réseau est à présent configuré dans la base de données, mais il existe une étape restante pour activer la fonctionnalité réseau. Finalisation de la `wp-config.php` paramètres et assurez-vous que `web.config` achemine correctement de chaque site.


Après avoir cliqué sur le bouton **installer** sur la page de *Configuration réseau* , WordPress va tenter de mettre à jour la `wp-config.php` et `web.config` fichiers. Toutefois, vous devez toujours vérifier les fichiers pour vous assurer que les mises à jour ont réussi. Si ce n’est pas le cas, cet écran vous présente les mises à jour nécessaires. Modifier et enregistrer les fichiers.


Une fois ces mises à jour, que vous devez fermer la session et ouvrez une session en différé dans le tableau de bord wp-admin.

Il doit maintenant être un menu supplémentaire sur l’administration de la barre intitulée **Mes Sites**. Ce menu vous permet de contrôler votre réseau via le tableau de bord **d’Administration de réseau** .

## <a name="adding-custom-domains"></a>Ajout de domaines personnalisés

Le [Mappage de domaine WordPress MU] [ wordpress-plugin-wordpress-mu-domain-mapping] plug-in permet d’ajouter des domaines personnalisés pour n’importe quel site de votre réseau. Pour le plug-in fonctionner correctement, vous devez faire une installation supplémentaire sur le portail et également à votre registraire de domaine.

## <a name="enable-domain-mapping-to-the-web-app"></a>Activer le mappage de domaine à l’application web

Le mode plan de [Service d’application](http://go.microsoft.com/fwlink/?LinkId=529714) **libre** ne supporte pas l’ajout de domaines personnalisés pour les applications Web. Vous devez passer en mode **partagé** ou **Standard** . Pour ce faire :

* Connectez-vous au portail Azure et recherchez votre application web. 
* Cliquez sur l’onglet **mettre à l’échelle** **paramètres**.
* Sous **Général**, sélectionnez *SHARED* ou *STANDARD*
* Cliquez sur **Enregistrer**

Vous pouvez recevoir un message vous invitant à vérifier le changement et accuser réception de que votre application web a maintenant peut entraîner un coût, en fonction de l’utilisation et la configuration que vous définissez.

Il faut quelques secondes pour traiter les nouveaux paramètres, n’est à présent temps de commencer à configurer votre domaine.

## <a name="verify-your-domain"></a>Vérifiez que votre domaine

Avant Azure Web Apps vous permettra de mapper un domaine sur le site, vous devez d’abord vérifier que vous disposez de l’autorisation pour mapper le domaine. Pour ce faire, vous devez ajouter un nouvel enregistrement CNAME à votre DNS.

* Connectez-vous au Gestionnaire de DNS de votre domaine
* Créer un CNAME nouvelle *awverify*
* Point de *awverify* à *awverify. YOUR_DOMAIN.azurewebsites.NET*

Il peut prendre un certain temps pour que les modifications DNS passer en plein effet, donc si les étapes suivantes ne fonctionnent pas immédiatement, aller prendre un café, puis y revenir et réessayez.

## <a name="add-the-domain-to-the-web-app"></a>Ajouter le domaine à l’application web

Revenir à votre application web via le portail d’Azure, cliquez sur **paramètres**, puis cliquez sur **Custom domains et SSL**.

Lorsque les *paramètres SSL* sont affichés, vous voyez les champs où vous allez entrer tous les domaines que vous souhaitez attribuer à votre application web. Si un domaine n’est pas répertorié ici, il ne sera pas disponible pour le mappage dans WordPress, quelle que soit la façon dont le domaine DNS est le programme d’installation.

![Gérer la boîte de dialogue domaines personnalisés][wordpress-manage-domains]

Après avoir tapé votre domaine dans la zone de texte, Azure vérifie l’enregistrement CNAME, que vous avez créé précédemment. Si le serveur DNS n’a pas été propagé entièrement, affiche un indicateur rouge. Si elle réussit, vous verrez une coche verte. 

Prenez note de l’adresse IP indiquée au bas de la boîte de dialogue. Vous aurez besoin pour configurer l’enregistrement A de votre domaine.

## <a name="setup-the-domain-a-record"></a>Configurer le domaine d’un enregistrement

Si les autres étapes ont réussi, vous pouvez maintenant affecter le domaine à votre application de web Azure via un enregistrement DNS A. 

Il est important de noter ici que les applications Azure web acceptent les CNAME et les enregistrements A, cependant, vous *devez* utiliser un enregistrement A pour activer le mappage de domaine approprié. Un enregistrement CNAME Impossible de transférer vers un autre CNAME, qui est ce que Azure créé pour vous avec YOUR_DOMAIN.azurewebsites.net.

À l’aide de l’adresse IP à partir de l’étape précédente, revenir à votre Gestionnaire DNS et la configuration de l’enregistrement A pour pointer vers cette adresse IP.


## <a name="install-and-setup-the-plugin"></a>Installer et configurer le plug-in

WordPress Multisite n’a actuellement pas de méthode intégrée permettant de mapper les domaines personnalisés. Il existe toutefois un plug-in appelé [Mappage de domaine WordPress MU] [ wordpress-plugin-wordpress-mu-domain-mapping] qui ajoute la fonctionnalité pour vous. Connectez-vous à la partie de l’administration du réseau de votre site et d’installer le plug-in de **Mappage de domaine WordPress MU** .

Après l’installation et l’activation du plug-in, consultez **paramètres de** > pour configurer le plug-in de**Mappage de domaine** . Dans la première zone de texte, *Adresse IP du serveur*, entrez l’adresse IP que vous avez utilisé pour configurer l’enregistrement A pour le domaine. Définissez les *Options du domaine* vous souhaitez (les valeurs par défaut sont souvent fines) et cliquez sur **Enregistrer**.

## <a name="map-the-domain"></a>Carte du domaine

Visitez le site du **tableau de bord** pour le site que vous souhaitez mapper le domaine. Cliquez sur **Outils** > **Mappage de domaine** et tapez le nouveau domaine dans la zone de texte et cliquez sur **Ajouter**.

Par défaut, le nouveau domaine sera réécrit pour le domaine du site généré automatiquement. Si vous souhaitez que tout le trafic envoyé vers le nouveau domaine, cochez la case de *domaine principal pour ce blog* avant de l’enregistrer. Vous pouvez ajouter un nombre illimité de domaines à un site, mais un seul peut être primaire.

## <a name="do-it-again"></a>Recommencer

Azure Web Apps permettent d’ajouter un nombre illimité de domaines pour une application web. Pour ajouter un autre domaine, vous devrez exécuter les sections **vérifier votre domaine** et de la **configuration du domaine un enregistrement** pour chaque domaine.  

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
