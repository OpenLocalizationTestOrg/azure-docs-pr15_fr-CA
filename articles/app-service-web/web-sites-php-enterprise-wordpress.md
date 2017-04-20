<properties
    pageTitle="WordPress d’entreprise sur le Service d’application Azure | Microsoft Azure"
    description="Découvrez comment héberger un site WordPress d’entreprise sur le Service d’application Azure"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>WordPress d’entreprise sur le Service d’application Azure

Service d’application Azure offre un environnement d’évolutif, sécurisé et facile à utiliser pour mission critique à grande échelle [WordPress] [ wordpress] sites. Microsoft lui-même exécute des sites d’entreprise telles que l' [Office] [ officeblog] et [Bing] [ bingblog] blogs. Ce document vous explique comment vous pouvez utiliser Azure Application Service Web Apps pour établir et maintenir une entreprise, le site WordPress nuage capable de gérer un grand nombre de visiteurs.

## <a name="architecture-and-planning"></a>Planification et architecture

Une installation de base WordPress a uniquement deux exigences.

* **Base de données MySQL** - disponible par le biais de [ClearDB sur le marché d’Azure][cdbnstore], ou vous pouvez gérer votre propre installation de MySQL sur Machines virtuelles Azure à l’aide de [Windows] [ mysqlwindows] ou [Linux][mysqllinux].

  > [AZURE.NOTE] ClearDB propose plusieurs configurations de MySQL, avec les caractéristiques de performances différentes pour chaque configuration. Consultez la [Banque d’Azure] [ cdbnstore] pour plus d’informations sur les offres par l’intermédiaire de la banque d’Azure, ou directement, comme indiqué sur le [site Web de ClearDB](http://www.cleardb.com/pricing.view).

* **PHP 5.2.4 ou supérieur** -Service d’application Azure fournit actuellement des [versions PHP 5.4, 5.5 et 5.6][phpwebsite].

    > [AZURE.NOTE] Nous vous recommandons de toujours en cours d’exécution sur la dernière version de PHP que vous disposez des derniers correctifs de sécurité.

### <a name="basic-deployment"></a>Déploiement de base

En utilisant uniquement la configuration de base, vous pouvez créer une solution de base dans une région d’Azure.

![une application Azure web et la base de données MySQL hébergé dans une seule région Azure][basic-diagram]

Bien que cela vous permettrait de cloisonnée de votre application en créant plusieurs instances d’applications Web du site, tout ce qui est hébergé dans les centres de données dans une zone géographique spécifique. Les visiteurs en dehors de cette région peuvent voir des temps de réponse lent lors de l’utilisation du site, et si les centres de données dans cette région de tomber en panne, c’est votre application.


### <a name="multi-region-deployment"></a>Déploiement de plusieurs région

À l’aide d' Azure [Traffic Manager][trafficmanager], il est possible d’adapter votre site WordPress dans plusieurs régions géographiques, tout en fournissant une seule URL pour les visiteurs. Tous les visiteurs arrivent via le Gestionnaire de trafic et sont ensuite routés vers une région basée sur la configuration de l’équilibrage de charge.

![une application web d’Azure, hébergée dans plusieurs régions, à l’aide de routeur de CDBR de haute disponibilité pour router vers MySQL sur plusieurs régions][multi-region-diagram]

Dans chaque région, le site WordPress devrait toujours être distribuée sur plusieurs instances d’applications Web, mais cette mise à l’échelle est la région spécifique ; les zones de fort trafic peuvent évoluer différemment de ceux de faible trafic.

Routage à plusieurs bases de données MySQL et réplication peuvent être effectuées à l’aide de l' de ClearDB [CDBR haute disponibilité routeur] [ cleardbscale] (illustré gauche) ou de [MySQL Cluster CGE][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Déploiement de plusieurs région avec des supports de stockage et de mise en cache
Si le site accepte les téléchargements ou les fichiers de support d’hôte, utilisez le stockage des objets Blob Azure. Si vous avez besoin de la mise en cache, envisagez de [cache de Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)ou des autres offres de mise en cache dans le [Magasin d’Azure](http://azure.microsoft.com/gallery/store/).

![une application web d’Azure, hébergée dans plusieurs régions, à l’aide de routeur de CDBR de haute disponibilité pour MySQL, avec gestion de Cache, stockage des objets Blob et CDN][performance-diagram]

Stockage des objets BLOB est géo-réparti sur les régions par défaut, sans que vous ayez à vous soucier de la réplication de fichiers sur tous les sites. Vous pouvez également activer le [Réseau de Distribution de contenu (CDN)] d' Azure[ cdn] pour le stockage des objets Blob, qui distribue des fichiers à la fin des nœuds de plus près à vos visiteurs.

### <a name="planning"></a>Planification

#### <a name="additional-requirements"></a>Exigences supplémentaires

Pour ce faire... | Utilisez cette option...
------------------------|-----------
**Télécharger ou stocker des fichiers volumineux** | [Plug-in WordPress pour l’utilisation de stockage des objets Blob][storageplugin]
**Envoyer un e-mail** | [SendGrid] [ storesendgrid] et le [plug-in WordPress pour l’utilisation de SendGrid][sendgridplugin]
**Noms de domaine personnalisé** | [Configurer un nom de domaine personnalisé dans le Service d’application Azure][customdomain]
**HTTPS** | [Activer HTTPS pour une application web dans le Service d’application Azure][httpscustomdomain]
**Validation de pré-production** | [Configurer la mise en attente des environnements pour les applications web dans le Service d’application Azure][staging] <p>Passage d’une application web à partir de l’intermédiaire vers production également déplace la configuration WordPress. Vous devez vous assurer que tous les paramètres sont mis à jour à la configuration requise pour votre application de production avant de passer l’application intermédiaire dans la production.</p>
**Surveillance et dépannage** | [Activer la journalisation de diagnostics pour les applications web dans le Service d’application Azure] [ log] et [Moniteur Web Apps dans le Service d’application Azure][monitor]
**Déployez votre site** | [Déployer une application web dans le Service d’application Azure][deploy]

#### <a name="availability-and-disaster-recovery"></a>Disponibilité et reprise après sinistre

Pour ce faire... | Utilisez cette option...
------------------------|-----------
**Sites d’équilibre de charge** ou **géo-distribuer des sites** | [Acheminement du trafic avec le Gestionnaire de trafic Azure][trafficmanager]
**Sauvegarder et restaurer** | [Sauvegarder une application web dans le Service d’application Azure] [ backup] et de la [restauration d’une application web dans le Service d’application Azure][restore]

#### <a name="performance"></a>Performances

Performances dans le nuage sont obtenue principalement par le biais de la mise en cache et horizontale ; Toutefois la mémoire, la bande passante et autres attributs des applications Web d’hébergement doivent être considéré comme.

Pour ce faire... | Utilisez cette option...
------------------------|-----------
**Comprendre les fonctionnalités de l’instance Service de l’application** | [Détails de la tarification, y compris les fonctionnalités des niveaux de Service de l’application][websitepricing]
**Ressources du cache** | [Cache de redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/)ou des autres offres de mise en cache dans le [Magasin d’Azure](/gallery/store/)
**Mettre à l’échelle votre application** | [Mettre à l’échelle une application web dans le Service d’application Azure] [ websitescale] et le [Routage de disponibilité élevée ClearDB][cleardbscale]. Si vous choisissez d’héberger et de gérer votre propre installation de MySQL, vous devez envisager de [MySQL Cluster CGE] [ cge] pour l’évolutivité

#### <a name="migration"></a>Migration

Il existe deux méthodes de migration d’un site WordPress existant pour le Service d’application Azure.

* ** [WordPress exporter] [ export] ** -cette commande exporte le contenu de votre blog, ce qui peut ensuite être importé vers un nouveau site WordPress sur le Service d’application Azure en utilisant le [plug-in d’importateur WordPress][import].

    > [AZURE.NOTE] Lors de ce processus vous permet de faire migrer votre contenu, elle ne migre pas les plug-ins, de thèmes ou d’autres personnalisations. Ils doivent être installés à nouveau manuellement.

* **Migration manuelle** - [sauvegarder votre site] [ wordpressbackup] et [base de données][wordpressdbbackup], puis manuellement le restaurer à une application web dans le Service d’application Azure et associé à la base de données MySQL pour migrer des sites hautement personnalisés et éviter le fastidieuses d’installation manuelle des plug-ins, de thèmes et d’autres personnalisations.

## <a name="step-by-step-instructions"></a>Instructions pas à pas

### <a name="create-a-wordpress-site"></a>Créer un site WordPress

1. Utilisez l' [Azure Marketplace] [ cdbnstore] pour créer une base de données MySQL de la taille que vous avez identifié dans la section [planification et Architecture](#planning) , dans l’ou les régions que vous allez héberger votre site.

2. Suivez les étapes de [Création d’une application web de WordPress dans le Service d’application Azure] [ createwordpress] pour créer une application web de WordPress. Lors de la création de l’application web, sélectionnez **utiliser une base de données MySQL existante** et sélectionnez la base de données créée à l’étape 1.

Si vous migrez un site WordPress existant, voir [migrer un site WordPress existant vers Azure](#Migrate-an-existing-WordPress-site-to-Azure) après la création d’une nouvelle application web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migrer un site WordPress existant vers Azure

Comme mentionné dans la section [planification et Architecture](#planning) , il existe deux manières de migrer un site WordPress.

* **exportation et importation** - pour les sites sans personnalisation, ou dans lequel vous souhaitez simplement déplacer le contenu.

* **sauvegarde et restauration** - pour les sites avec de nombreuses personnalisations dans lequel vous souhaitez déplacer tous les éléments.

Pour migrer votre site, utilisez une des sections suivantes.

#### <a name="the-export-and-import-method"></a>La méthode d’importation et d’exportation

1. Utiliser [WordPress d’exportation] [ export] pour exporter votre site existant.

2. Créer une application web à l’aide de la procédure dans la section [Création d’un site WordPress](#Create-a-new-WordPress-site) .

3. Se connecter à votre site WordPress sur les applications Web et cliquez sur **plug-ins** -> **Nouveau**. Recherchez et installez, le plug-in **WordPress importateur** .

4. Après avoir installé le plug-in de l’importateur, cliquez sur **Outils** -> **importation**et puis sélectionnez **WordPress** pour utiliser le plug-in de WordPress importateur.

5. Dans la page **Importation WordPress** , cliquez sur **Fichier**. Naviguez jusqu’au fichier WXR exporté à partir de votre site WordPress existant, puis cliquez sur **Télécharger le fichier et importer**.

6. Cliquez sur **Envoyer**. Vous êtes invité que l’importation a réussi.

8. Une fois toutes ces étapes terminées, redémarrez votre site à partir de la blade d’application web dans [Azure portal][mgmtportal].

Après avoir importé le site, vous devrez peut-être effectuer les étapes suivantes pour activer les paramètres ne figurant ne pas dans le fichier d’importation.

Si vous utilisiez cette... | Procédez comme suit...
------------------ | ----------
**Liens permanents** | Dans le tableau de bord WordPress du nouveau site, cliquez sur **paramètres** -> **liens permanents** et mise à jour la structure de liens permanents
**liens de supports d’image** | Pour mettre à jour les liens vers le nouvel emplacement, utilisez le [plug-in de l’URL de mise à jour de Blues de velours][velvet], un outil de recherche et de remplacement, ou manuellement dans votre base de données
**Thèmes** | Accédez à **l’apparence** -> **thème** et une mise à jour du thème du site en fonction des besoins
**Menus** | Si votre thème prend en charge des menus, des liens vers votre page d’accueil ait le sous-répertoire ancien incorporé dans les. Accédez à **l’apparence** -> **Menus** et mettez-les à jour

#### <a name="the-backup-and-restore-method"></a>La méthode de sauvegarde et de restauration

1. Sauvegarder votre WordPress existant du site en utilisant les informations sur [les sauvegardes WordPress][wordpressbackup].

2. Sauvegarder votre base de données existante en utilisant les informations au niveau de la [sauvegarde de votre base de données][wordpressdbbackup].

3. Créer une base de données et restaurer la sauvegarde.

    1. Achat d’une nouvelle base de données à partir de [Azure Marketplace][cdbnstore], ou le programme d’installation d’une base de données MySQL sur un [Windows] [ mysqlwindows] ou [Linux] [ mysqllinux] VM.

    2. À l’aide d’un client de MySQL comme [MySQL Workbench][workbench], se connecter à la nouvelle base de données et d’importer votre base de données WordPress.

    3. Mettre à jour la base de données pour modifier les entrées de domaine pour votre nouveau domaine de Service d’application Azure. Par exemple, mywordpress.azurewebsites.net. Utiliser [recherche et de remplacement pour le Script de bases de données WordPress] [ searchandreplace] pour changer en toute sécurité toutes les instances.

4. Créer une application web sur le portail Azure et publier la sauvegarde WordPress.

    1. Créer une application web dans [Azure portal] [ mgmtportal] avec une base de données à l’aide du **Nouveau** -> **Web + Mobile** -> **Azure Marketplace** -> **Web Apps** -> **Web app + SQL** (ou **application Web + MySQL**) -> **créer**. Configurer tous les paramètres requis pour créer une application web vide.

    2. Dans votre sauvegarde WordPress, recherchez le fichier **config.php-wp** et ouvrez-le dans un éditeur. Remplacer les entrées suivantes avec les informations pour votre nouvelle base de données MySQL.

        * **DB_NAME** - le nom d’utilisateur de la base de données

        * **DB_USER** - le nom d’utilisateur utilisé pour accéder à la base de données

        * **DB_PASSWORD** - le mot de passe utilisateur

        Après la modification de ces entrées, enregistrez et fermez le fichier **config.php-wp** .

    3. Utiliser le [déploiement d’une application web dans le Service d’application Azure] [ deploy] informations pour activer la méthode de déploiement que vous souhaitez utiliser, puis déployer votre sauvegarde WordPress sur votre application web dans le Service d’application Azure.

5. Une fois le site WordPress a été déployé, vous devez être en mesure d’accéder le nouveau site (sous la forme d’une application web du Service de l’application) à l’aide du *. azurewebsite.net des URL pour le site.

### <a name="configure-your-site"></a>Configuration de votre site

Une fois le site WordPress a été créé ou de migration, utilisez les informations suivantes pour améliorer les performances ou d’activer des fonctionnalités supplémentaires.

Pour ce faire... | Utilisez cette option...
------------- | -----------
**Définir le mode plan de Service d’application, la taille et la mise à l’échelle de l’activation** | [Mettre à l’échelle une application web dans le Service d’application Azure][websitescale]
**Activer les connexions de base de données persistante** | Par défaut, WordPress n’utilise pas de connexions permanentes de la base de données, qui peuvent entraîner la connexion à la base de données pour devenir limité après plusieurs connexions. Pour activer les connexions permanentes, installez le (connexions permanentes carte Plug-in) [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]. 
**Améliorer les performances** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Désactiver le cookie ARR</a> - peut améliorer les performances lors de l’exécution de WordPress sur plusieurs instances de Web Apps</p></li><li><p>Activer la mise en cache. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis de cache</a> (aperçu) peut être utilisé avec le <a href="https://wordpress.org/plugins/redis-object-cache/">Redis plug-in d’objet cache WordPress</a>, ou utilisez une des autres offres mise en cache de la <a href="/gallery/store/">Banque d’Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Comment accélérer WordPress avec Wincache</a> - Wincache est activée par défaut pour les applications Web</p></li><li><p><a href="../web-sites-scale/">Échelle d’une application web dans le Service d’application Azure</a> et utilisation <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB haute disponibilité</a> ou <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Utiliser des objets BLOB pour le stockage** | <ol><li><p><a href="../storage-create-storage-account/">Créer un compte de stockage Azure</a></p></li><li><p>Découvrez comment <a href="../cdn-how-to-use/">utiliser le réseau de Distribution de contenu (CDN)</a> en géo-distribuer des données stockées dans les objets BLOB.</p></li><li><p>Installez et configurez le <a href="https://wordpress.org/plugins/windows-azure-storage/">Stockage Azure WordPress du plug-in</a>.</p><p>Pour le programme d’installation détaillée et des informations de configuration pour le plug-in, consultez le <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guide de l’utilisateur</a>.</p> </li></ol>
**Activer la messagerie électronique** | Activer la <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> à l’aide de la banque d’Azure. Installez le <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in de SendGrid</a> pour WordPress.
**Configurer un nom de domaine personnalisé** | [Configurer un nom de domaine personnalisé dans le Service d’application Azure][customdomain]
**Activer HTTPS pour un nom de domaine personnalisé** | [Activer HTTPS pour une application web dans le Service d’application Azure][httpscustomdomain]
**Charger le solde ou géo-distribuer votre site** | [Router le trafic avec le Gestionnaire de trafic Azure][trafficmanager]. Si vous utilisez un domaine personnalisé, consultez [configurer un nom de domaine personnalisé dans le Service d’application Azure] [ customdomain] pour plus d’informations sur l’utilisation du Gestionnaire de trafic avec des noms de domaine personnalisé
**Activer les sauvegardes automatiques** | [Sauvegarder une application web dans le Service d’application Azure][backup]
**Activer l’enregistrement des Diagnostics** | [Activer la journalisation de diagnostics pour les applications web dans le Service d’application Azure][log]

## <a name="next-steps"></a>Étapes suivantes

* [Optimisation de WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Convertir WordPress Multisite dans le Service d’application Azure](web-sites-php-convert-wordpress-multisite.md)

* [Assistant pour Azure de la mise à niveau des ClearDB](http://www.cleardb.com/store/azure/upgrade)

* [Hébergement WordPress dans un sous-dossier de votre application web dans le Service d’application Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Pas à pas : Créer un site dans WordPress Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Héberger votre blog WordPress existant sur Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [L’activation de belles permanents dans WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Comment migrer et exécuter votre blog WordPress sur le Service d’application Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [L’exécution de libre WordPress sur le Service d’application Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress sur Azure dans deux minutes ou moins](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Déplacement d’un blog WordPress vers Azure - partie 1 : création d’un blog WordPress sur Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Déplacement d’un blog WordPress vers Azure - partie 2 : transfert de votre contenu.](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Déplacement d’un blog WordPress vers Azure - partie 3 : configuration de votre domaine personnalisé](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Déplacement d’un blog WordPress vers Azure - partie 4 : presque permanents et les règles de réécriture d’URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Déplacement d’un blog WordPress vers Azure - partie 5 : déplacement d’un sous-dossier à la racine](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Comment faire pour configurer une application web de WordPress dans votre compte Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping des WordPress sur Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Conseils pour WordPress sur Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
