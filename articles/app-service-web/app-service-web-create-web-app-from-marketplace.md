<properties
    pageTitle="Créer une application web à partir du marché Azure | Microsoft Azure"
    description="Apprenez à créer une nouvelle application web de WordPress à partir du marché Azure via le portail d’Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>Créer une application web à partir de Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Le marché Azure met à disposition un large éventail d’applications web populaire développé par Microsoft, des entreprises tierces et initiatives de logiciels open source. Par exemple, WordPress, Umbraco CMS, Drupal, etc. Ces applications web sont construites sur une large gamme de structures répandues, telles que [PHP] dans ce WordPress exemple [.NET], [Node.js], [Java]et [Python], pour ne citer. Pour créer une application web à partir du marché Azure le seul logiciel dont vous avez besoin est le navigateur que vous utilisez pour le [Portail Azure].

Dans ce didacticiel, vous allez apprendre comment :

* Rechercher et créer l’application web dans le Service d’application Azure qui est basé sur un modèle Azure Marketplace.
* Configurer les paramètres de Service d’application Azure pour la nouvelle application web.
* Lancer et gérer votre application web.

Pour ce didacticiel, vous allez déployer un site de blog WordPress à partir du marché Azure. Lorsque vous avez terminé les étapes de ce didacticiel, vous aurez votre propre site WordPress haut et s’exécutant dans le nuage.

![Tableau de bord application exemple WordPress wep][WordPressDashboard1]

Le site de WordPress que vous allez déployer dans ce didacticiel utilise MySQL pour la base de données. Si vous souhaitez utiliser à la place de la base de données SQL pour la base de données, reportez-vous à la section [Nami du projet], qui est également disponible par le biais de l’Azure Marketplace.

> [AZURE.NOTE]
> Pour terminer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [activer vos avantages d’abonné Visual Studio] [ activate] ou [Inscrivez-vous pour un essai gratuit de][free trial].
>
> Si vous souhaitez commencer avec le Service d’application Azure avant de vous inscrivez pour un compte Azure, accédez à [Essayer le Service application]. À partir de là, vous pouvez immédiatement créer une application web de courte durée starter dans le Service d’application : aucune carte de crédit n’est requise, et il n’y a aucun engagement.

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Trouver et créer une application Web dans le Service d’application Azure

1. Connectez-vous au [portail Azure].

1. Cliquez sur **Nouveau**.
    
    ![Créer une nouvelle ressource Azure][MarketplaceStart]
    
1. Recherchez **WordPress**, puis cliquez sur **WordPress**. (Si vous souhaitez utiliser de la base de données SQL au lieu de MySQL, recherchez **Nami du projet**.)

    ![Recherche de WordPress sur le marché][MarketplaceSearch]
    
1. Après avoir lu la description de l’application WordPress, cliquez sur **créer**.

    ![Créer l’application web de WordPress][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configurer les paramètres de Service d’application Azure pour votre nouvelle application Web

1. Après avoir créé une nouvelle application web, la lame de paramètres WordPress s’affiche, que vous utiliserez pour effectuer les opérations suivantes :

    ![Configurer les paramètres de l’application web WordPress][ConfigStart]

1. Entrez un nom pour l’application web dans la zone de **l’application Web** .

    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera *{nom}*. azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge s’affiche dans la zone de texte.

    ![Configurer le nom de l’application web WordPress][ConfigAppName]

1. Si vous avez plus d’un abonnement, cliquez sur celui que vous souhaitez utiliser. 

    ![Configurer l’abonnement pour l’application web][ConfigSubscription]

1. Sélectionnez un **Groupe de ressources** ou créez-en un nouveau.

    Pour plus d’informations sur les groupes de ressources, consultez [vue d’ensemble du Gestionnaire de ressources Azure][ResourceGroups].

    ![Configurer le groupe de ressources pour l’application web][ConfigResourceGroup]

1. Sélectionnez un **Emplacement de plan de Service** d’application/ou créez-en un nouveau.

    Pour plus d’informations sur les plans de Service d’application, consultez [vue d’ensemble des plans de Service d’application Azure][AzureAppServicePlans]. 

    ![Configurer le plan de service pour l’application web][ConfigServicePlan]

1. Cliquez sur la **base de données**et ensuite dans la **Nouvelle base de données MySQL** lame fournir les valeurs requises pour la configuration de votre base de données MySQL.

    une barre d’outils. Entrez un nouveau nom ou laissez le nom par défaut.

    b. Laissez le **Type base de données** **partagée**.

    c. Choisissez le même emplacement que celui que vous avez choisi pour l’application web.

    d. Choisissez un niveau de tarification. **Mercure** , qui est disponible avec les connexions minimales et l’espace disque - est parfait pour ce didacticiel.

    e. Dans la **Nouvelle base de données MySQL** lame, accepter les conditions juridiques, puis cliquez sur **OK**. 

    ![Configurer les paramètres de base de données de l’application web][ConfigDatabase]

1. Dans la lame **WordPress** , accepter les conditions juridiques et puis cliquez sur **créer**. 

    ![Terminer les paramètres d’application web et cliquez sur OK][ConfigFinished]

    Service d’application Azure crée l’application web, généralement en moins d’une minute. Vous pouvez surveiller la progression en cliquant sur l’icône de la clochette en haut de la page du portail.

    ![Indicateur de progression][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Lancer et gérer votre application de web WordPress
    
1. Lors de la création d’applications web est terminée, naviguer dans le portail Azure au groupe de ressources dans lequel vous avez créé l’application, et vous pouvez voir l’application web et la base de données.

    La ressource supplémentaire avec l’icône d’ampoule est [Perspectives d’Application][ApplicationInsights], qui fournit des services de surveillance pour votre application web.

1. Dans la lame du **groupe de ressources** , cliquez sur la ligne d’application web.

    ![Sélectionnez votre application de web WordPress][WordPressSelect]

1. De la lame d’application Web, cliquez sur **Parcourir**.

    ![Accédez à votre application de web WordPress][WordPressBrowse]

1. Si vous êtes invité à sélectionner la langue de votre blog WordPress, sélectionnez la langue souhaitée, puis sur **Continuer**.

    ![Configurer la langue de votre application de web WordPress][WordPressLanguage]

1. Dans la page **Bienvenue** WordPress, entrez les informations de configuration requises par WordPress, puis cliquez sur **Installer un WordPress**.

    ![Configurer les paramètres de votre application de web WordPress][WordPressConfigure]

1. Connectez-vous en utilisant les informations d’identification que vous avez créé sur la page **d’accueil** .  

1. Votre page de tableau de bord du site s’ouvre et affiche des informations que vous avez fournies.    

    ![Afficher votre tableau de bord WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez vu comment créer et déployer une application web d’exemple depuis le site Marketplace d’Azure.

Pour plus d’informations sur l’utilisation de l’application de Service Web Apps, consultez les liens sur le côté gauche de la page (pour les fenêtres du navigateur large) ou en haut de la page (pour les fenêtres du navigateur étroit).

Pour plus d’informations sur le développement d’applications web de WordPress sur Azure, consultez [Développement de WordPress sur le Service d’application Azure][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Essayez le Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure Portal]: https://portal.azure.com/
[Nami de projet]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
