<properties
    pageTitle="Créer une application web de Java dans le Service d’application Azure | Microsoft Azure"
    description="Ce didacticiel vous montre comment déployer une application web de Java au Service d’application Azure."
    services="app-service\web"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Créer une application web de Java dans le Service d’application Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel explique comment créer une [application web dans le Service d’application Azure] Java à l’aide du [Portail Azure]. Le portail Azure est une interface web que vous pouvez utiliser pour gérer vos ressources Azure.

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [activer vos avantages d’abonné de Visual Studio] , ou [Inscrivez-vous pour un essai gratuit].
>
> Si vous souhaitez commencer avec le Service d’application Azure avant de vous inscrivez pour un compte Azure, accédez à [Essayer le Service application]. Vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application ; Aucune carte de crédit n’est obligatoire et aucun des engagements.

## <a name="java-application-options"></a>Options de l’application Java

Il existe plusieurs méthodes que vous pouvez configurer une application Java dans une application web de Service de l’application. 

1. Créer une application, puis configurez **les paramètres d’Application**.

    Service d’application fournit plusieurs versions de Tomcat et jetée, avec la configuration par défaut. Si l’application que vous allez héberger fonctionnera avec une des versions intégrées, cette méthode de configuration d’un conteneur de web est la plus simple et est idéale lorsque vous souhaitez faire est de télécharger un fichier war dans un conteneur du web. Pour cette méthode, vous créez une application dans Azure Portal et passez à la blade de **paramètres d’Application** pour votre application choisir votre version de Java en même temps que le conteneur web Java souhaité. Lorsque vous utilisez cette méthode à la fois le conteneur de votre web et Java sont exécutés à partir de fichiers de programme. Les autres méthodes de placent le conteneur web et potentiellement la JVM dans l’espace de votre disque. Lorsque vous utilisez ce modèle, vous n’avez pas accès pour modifier des fichiers dans cette partie du système de fichiers. Cela signifie que vous ne pouvez pas faire les choses comme configurer le fichier *server.xml* ou placer des fichiers de bibliothèque dans le *dossier/lib* . Pour plus d’informations, consultez la [créer et configurer une application web de Java](#appsettings) section plus loin dans ce didacticiel.
    
2. Utiliser un modèle à partir de Azure Marketplace.

    L’Azure Marketplace comprend des modèles automatiquement créer et configurer les applications web Java avec des conteneurs web Tomcat ou jetée. Les conteneurs web qui créent des modèles sont configurables. Pour plus d’informations, reportez-vous à la section [utiliser un modèle de Java à partir de Azure Marketplace](#marketplace) de ce didacticiel.
  
3. Créer une application, puis copier manuellement et modifier les fichiers de configuration 

    Vous souhaiterez peut-être héberger une application Java personnalisée qui ne se déploie pas dans tous les conteneurs web fournies par le Service de l’application. Par exemple :
    
    * Votre application Java nécessite une version de Tomcat ou jetée qui n’est pas directement pris en charge par le Service d’application ou proposée dans la galerie.
    * Votre application Java accepte les demandes HTTP et ne déploie pas comme une guerre dans un conteneur web existant.
    * Vous souhaitez configurer le conteneur web entièrement vous-même. 
    * Vous souhaitez utiliser une version de Java n’est pas pris en charge par le Service de l’application et souhaitez charger vous-même.

    Dans ce cas-là, vous pouvez créer une application à l’aide du portail Azure et ensuite fournir les fichiers d’exécution approprié manuellement. Dans ce cas, les fichiers seront imputées sur vos quotas d’espace de stockage pour votre plan de Service de l’application. Pour plus d’informations, consultez [télécharger une application web Java personnalisée Azure].

## <a name="portal"></a>Créer et configurer une application web de Java

Cette section indique comment créer une application web et le configurer pour Java à l’aide de la lame de **paramètres d’Application** du portail.

1. Connectez-vous au [portail Azure].

2. Cliquez sur **Nouveau > Web + Mobile > Web App**.

    ![Nouvelle application Web][newwebapp]

4. Entrez un nom pour l’application web dans la zone de **l’application Web** .

    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}. azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge s’affiche dans la zone de texte.

5. Sélectionnez un **Groupe de ressources** ou créez-en un nouveau.

    Pour plus d’informations sur les groupes de ressources, consultez [l’aide du portail Azure pour gérer vos ressources Azure].

6. Sélectionnez un **Emplacement de plan de Service** d’application/ou créez-en un nouveau.

    Pour plus d’informations sur les plans de Service d’application, consultez [vue d’ensemble des plans de Service d’application Azure].

7. Cliquez sur **créer**.

    ![Créer l’application Web][newwebapp2]
 
8. Lorsque l’application web a été créée, cliquez sur **Web Apps > {votre application web}**.
 
    ![Sélectionnez l’application Web][selectwebapp]

9. De la lame **d’application Web** , cliquez sur **paramètres**.

10. Cliquez sur **paramètres de l’Application**.

11. Choisissez la **version de Java**de votre choix. 

12. Choisissez le la **version mineure de Java**. Si vous sélectionnez **plus récent**, votre application utilise la dernière version mineure qui est disponible dans le Service d’application pour cette version majeure de Java. L’élément **plus récent** est unique pour les applications Java créées à partir des **paramètres de l’Application**. Si vous créez votre application Java à partir de la galerie, vous devez gérer votre propre conteneur et les modifications de la machine virtuelle Java. 

12. Choisissez le **conteneur Web**de votre choix. Si vous sélectionnez un nom de conteneur qui commence par le **plus récent**, votre application est conservée à la dernière version de cette version majeure de conteneur web qui est disponible dans le Service d’application. 

    ![Versions de conteneur Web][versions]

13. Cliquez sur **Enregistrer**.

    Dans quelques instants, votre application web sera basé sur Java et configuré pour utiliser le conteneur web que vous avez sélectionné.

14. Cliquez sur **Web apps > {votre nouvelle application web}**.

15. Cliquez sur l' **URL** pour accéder au nouveau site.

    La page web confirme que vous avez créé une application web basée sur Java.

## <a name="marketplace"></a>Utiliser un modèle de Java à partir de Azure Marketplace

Cette section indique comment utiliser le marché Azure pour créer une application web de Java. Le même flux général peut également servir à créer un basée sur Java de mobile ou l’API application. 

1. Connectez-vous au [portail Azure]

2. Cliquez sur **Nouveau > marché**.

    ![Nouveau marché][newmarketplace]

3. Cliquez sur **Web + Mobile**.

    Vous devrez peut-être faire défiler vers la gauche pour voir la lame du **marché** dans lequel vous pouvez sélectionner **Web + Mobile**.

4. Dans la zone de texte Rechercher, entrez le nom d’un serveur d’applications Java, tels que **Apache Tomcat** ou **jetée**et appuyez sur ENTRÉE.

5. Dans les résultats de la recherche, cliquez sur le serveur d’applications Java.

    ![Jetée de Mobile Web][webmobilejetty]

6. Dans la première lame **Apache Tomcat** ou **jetée** , cliquez sur **créer**.

    ![Lame de portail jetty][jettyblade]

7. De la lame **d’Apache Tomcat** ou **jetée** suivante, entrez un nom pour l’application web dans la zone de **l’application Web** .

    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}. azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge s’affiche dans la zone de texte.

8. Sélectionnez un **Groupe de ressources** ou créez-en un nouveau.

    Pour plus d’informations sur les groupes de ressources, consultez [l’aide du portail Azure pour gérer vos ressources Azure].

9. Sélectionnez un **Emplacement de plan de Service** d’application/ou créez-en un nouveau.

    Pour plus d’informations sur les plans de Service d’application, consultez [vue d’ensemble des plans de Service d’application Azure].

10. Cliquez sur **créer**.

    ![Création de portail jetty][jettyportalcreate2]

    En un temps très court, généralement moins d’une minute, Azure termine la création de la nouvelle application web.

11. Cliquez sur **Web apps > {votre nouvelle application web}**.

12. Cliquez sur l' **URL** pour accéder au nouveau site.

    ![URL jetty][jettyurl]

    Tomcat est livré avec un ensemble par défaut de pages ; Si vous avez choisi de Tomcat, vous voyez une page similaire à l’exemple suivant.

    ![Application Web en utilisant Apache Tomcat][tomcat]

    Si vous avez choisi jetée, vous voyez une page similaire à l’exemple suivant. Jetée n’a pas un ensemble de page par défaut, donc le même JSP qui est utilisé pour un site vide de Java est réutilisé ici.

    ![Application Web à l’aide de la jetée][jetty]

Maintenant que vous avez créé l’application web avec un conteneur d’application, consultez la section [étapes suivantes](#next-steps) pour plus d’informations sur le chargement de votre application à l’application web.

## <a name="next-steps"></a>Étapes suivantes

À ce stade, vous disposez d’un serveur d’applications Java en cours d’exécution dans votre application web dans le Service d’application Azure. Pour déployer votre propre code dans l’application web, consultez [Ajouter une application ou une page Web à votre application web de Java].

Pour plus d’informations sur le développement d’applications Java dans Azure, consultez le [Centre pour développeurs Java].

<!-- URL List -->

[Ajouter une application ou une page Web à votre application web de Java]: ./web-sites-java-add-app.md
[Vue d’ensemble des plans de Service d’application Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Azure Portal]: https://portal.azure.com/
[activer vos avantages d’abonné de Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Inscrivez-vous pour une version d’évaluation gratuite]: http://go.microsoft.com/fwlink/?LinkId=623901
[Essayez le Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751
[application Web dans le Service d’application Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centre pour développeurs Java]: /develop/java/
[À l’aide du portail Azure pour gérer vos ressources Azure]: ../azure-portal/resource-group-portal.md
[Téléchargement d’une application web de Java personnalisée vers Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
