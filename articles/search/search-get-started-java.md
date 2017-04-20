<properties
    pageTitle="Mise en route de la recherche d’Azure dans Java | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Comment créer une application de recherche de nuage hébergé sur Azure à l’aide de Java comme langage de programmation."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-java"></a>Mise en route de la recherche d’Azure dans Java
> [AZURE.SELECTOR]
- [Portail](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Apprenez à créer une application de recherche Java personnalisée qui utilise la recherche d’Azure pour son expérience de recherche. Ce didacticiel utilise l' [API REST de Azure Search Service](https://msdn.microsoft.com/library/dn798935.aspx) pour construire les objets et les opérations utilisées dans cet exercice.

Pour exécuter cet exemple, vous devez disposer d’un service de recherche d’Azure, dont vous pouvez vous connecter pour le [Portail Azure](https://portal.azure.com). Pour obtenir des instructions détaillées, reportez-vous à la section [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) .

Nous avons utilisé le logiciel suivant pour générer et tester cet exemple :

- [Eclipse IDE pour les développeurs Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Veillez à télécharger la version EE. L’une des étapes de vérification nécessite une fonctionnalité qui se trouvent uniquement dans cette édition.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Sur les données

Cet exemple d’application utilise les données de [United States géologique Services (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtré sur l’état de Rhode Island pour réduire la taille du groupe de données. Nous allons utiliser ces données pour créer une application de recherche renvoyant des bâtiments d’éléments géographiques comme hôpitaux et les écoles, ainsi que les caractéristiques géologiques comme flux, lacs et sommets.

Dans cette application, le programme **SearchServlet.java** crée et charge l’index à l’aide d’une construction [d’indexeur](https://msdn.microsoft.com/library/azure/dn798918.aspx) , récupérer le groupe de données de groupes de sécurité universels filtré à partir d’une base de données de SQL Azure public. Les informations d’identification prédéfinies et les informations de connexion à la source de données en ligne sont fournis dans le code de programme. En termes d’accès aux données, aucune configuration supplémentaire n’est nécessaire.

> [AZURE.NOTE] Nous avons appliqué un filtre sur ce groupe de données à rester dans la limite de 10 000 documents de la couche de tarification libre. Si vous utilisez la couche standard, cette limite ne s’applique pas, et vous pouvez modifier ce code pour utiliser un plus grand groupe de données. Pour plus d’informations sur les capacités de chaque niveau de tarification, voir [les contraintes et les limites](search-limits-quotas-capacity.md).

## <a name="about-the-program-files"></a>Sur les fichiers de programme

La liste suivante décrit les fichiers qui sont pertinentes à cet exemple.

- Search.jsp : Fournit l’interface utilisateur
- SearchServlet.java : Fournit des méthodes (similaires à un contrôleur MVC)
- SearchServiceClient.java : Demandes de poignées HTTP
- SearchServiceHelper.java : Une classe d’assistance qui fournit des méthodes statiques
- Document.Java : Fournit le modèle de données
- config.Properties : définit la clé d’api et l’URL du service de recherche
- Pom.XML : Une dépendance Maven

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Trouver le nom du service et la clé d’api de votre service de recherche d’Azure

Tous les appels d’API REST dans Azure recherche nécessitent que vous fournissez l’URL du service et une clé d’api. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre de raccourcis, cliquez sur le **service de recherche** pour répertorier tous les services de recherche d’Azure mis en service pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4. Sur le tableau de bord de service, vous verrez des mosaïques pour les informations essentielles, ainsi que l’icône de clé pour les clés de l’administration de l’accès à.

    ![][3]

5. Copiez l’URL du service et une clé de l’admin. Vous en aurez besoin ultérieurement, lorsque vous les ajoutez au fichier **config.Properties** .

## <a name="download-the-sample-files"></a>Téléchargez les fichiers exemples

1. Accédez à [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) sur Github.

2. Cliquez sur **Télécharger le code postal**et enregistrer le fichier .zip sur le disque puis extrayez tous les fichiers qu’il contient. Envisagez d’extraction des fichiers dans votre espace de travail pour faciliter la recherche ultérieure de projet Java.

3. Les exemples de fichiers sont en lecture seule. Cliquez sur les propriétés du dossier et supprimer l’attribut en lecture seule.

Toutes les modifications de fichier et les instructions d’exécution sont effectuées par rapport à des fichiers dans ce dossier.  

## <a name="import-project"></a>Importer un projet

1. Dans Eclipse, choisissez **fichier** > **importation** > **générale** > **Des projets existants dans l’espace de travail**.

    ![][4]

2. Dans **Sélectionner le répertoire racine**, accédez au dossier contenant les fichiers d’exemple. Sélectionnez le dossier qui contient le dossier .project. Le projet doit s’afficher dans la liste **projets** , sous la forme d’un élément sélectionné.

    ![][12]

3. Cliquez sur **Terminer**.

4. Utilisez **l’Explorateur de projets** pour afficher et modifier les fichiers. S’il n’est pas déjà ouvert, cliquez sur **fenêtre** > **Affichage des** > **Explorateur de projets** ou utilisez le raccourci pour l’ouvrir.

## <a name="configure-the-service-url-and-api-key"></a>Configurer l’URL du service et la clé de l’api

1. Dans l' **Explorateur de projets**, double-cliquez sur **config.Properties** pour modifier les paramètres de configuration qui contient le nom du serveur et la clé d’api.

2. Reportez-vous aux étapes plus haut dans cet article, où vous avez trouvé l’URL du service et la clé d’api dans [Azure Portal](https://portal.azure.com), pour obtenir les valeurs que vous entrerez dans **config.Properties**.

3. Dans **config.Properties**, remplacer « Clé Api » avec la clé d’api pour votre service. Ensuite, le nom du service (le premier composant de l’URL http://servicename.search.windows.net) remplace « nom de service » dans le même fichier.

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurer les environnements de projet, de génération et d’exécution

1. Dans Eclipse, dans l’Explorateur de projets, cliquez sur le projet > **Propriétés** > **Facettes du projet**.

2. Sélectionnez le **Module dynamique Web**, **Java**et **JavaScript**.

    ![][6]

3. Cliquez sur **Appliquer**.

4. Sélectionnez la **fenêtre** > **Préférences** > **Server** > **environnements Runtime** > **Ajouter..**.

5. Développez Apache et sélectionnez la version du serveur Apache Tomcat que précédemment installé. Dans notre système, nous avons installé la version 8.

    ![][7]

6. Sur la page suivante, spécifiez le répertoire d’installation de Tomcat. Sur un ordinateur Windows, il s’agira probablement C:\Program Files\Apache Software Foundation\Tomcat *version*.

6. Cliquez sur **Terminer**.

7. Sélectionnez la **fenêtre** > **Préférences** > **Java** > **installé JRE** > **Ajouter**.

8. Dans **Ajouter un JRE**, sélectionnez **Ordinateur virtuel Standard**.

10. Cliquez sur **suivant**.

11. Dans la définition de JRE dans JRE chez vous, cliquez sur **répertoire**.

12. Accédez à **Program Files** > **Java** et sélectionnez le JDK vous avez précédemment installé. Il est important de sélectionner le JDK comme le JRE.

13. Dans JRE installé, choisissez le **JDK**. Vos paramètres doivent ressembler à la capture d’écran suivante.

    ![][9]

14. Le cas échéant, sélectionnez **fenêtre** > **Navigateur Web** > **Internet Explorer** pour ouvrir l’application dans une fenêtre de navigateur externe. À l’aide d’un navigateur externe vous donne une meilleure expérience d’application Web.

    ![][8]

Vous avez maintenant terminé les tâches de configuration. Ensuite, vous allez générer et exécuter le projet.

## <a name="build-the-project"></a>Générez le projet

1. Dans l’Explorateur de projets, cliquez sur le nom du projet et choisissez **Exécuter en tant que** > **Maven build...** pour configurer le projet.

    ![][10]

8. Modification de configuration, objectifs, tapez « nouvelle installation », puis cliquez sur **exécuter**.

Messages d’état sont affichés dans la fenêtre de console. Vous devriez voir la réussite des builds indiquant le projet généré sans erreurs.

## <a name="run-the-app"></a>Exécuter l’application

Dans cette dernière étape, vous allez exécuter l’application dans un environnement d’exécution de serveur local.

Si vous n’avez pas encore spécifié un environnement d’exécution de serveur dans Eclipse, vous devez d’abord faire.

1. Dans l’Explorateur de projets, développez le **contenu Web**.

5. Droit **Search.jsp** > **Exécuter en tant que** > **s’exécutent sur le serveur**. Sélectionnez le serveur Apache Tomcat, puis cliquez sur **exécuter**.

> [AZURE.TIP] Si vous avez utilisé un espace de travail par défaut pour stocker votre projet, vous devrez modifier la **Configuration d’exécution** pour pointer vers l’emplacement du projet pour éviter une erreur de démarrage du serveur. Dans l’Explorateur de projets, cliquez sur **Search.jsp** > **Exécuter en tant que** > **Les Configurations de série**. Sélectionnez le serveur Apache Tomcat. Cliquez sur **Arguments**. Cliquez sur **espace de travail** ou de **Système de fichiers** pour définir le dossier contenant le projet.

Lorsque vous exécutez l’application, vous devez voir une fenêtre de navigateur, une zone de recherche permettant d’entrer des termes.

Attendez environ une minute avant de cliquer sur **recherche** pour donner le temps de service pour créer et charger l’index. Si vous obtenez une erreur HTTP 404, il vous suffit d’attendre un peu plus avant d’essayer à nouveau.

## <a name="search-on-usgs-data"></a>Effectuez une recherche sur les données de groupes de sécurité universels

Le jeu de données de groupes de sécurité universels inclut les enregistrements qui sont pertinentes à l’état de Rhode Island. Si vous cliquez sur **Rechercher** sur une zone de recherche, vous obtenez les entrées supérieure de 50, qui est la valeur par défaut.

Entrer un terme de recherche donnera le moteur de recherche quelque chose pour continuer. Essayez d’entrer un nom régionaux. « Roger Williams » a été le premier gouverneur de Rhode Island. Plusieurs des parcs, des bâtiments et des écoles sont nommés d’après lui.

![][11]

Vous pouvez également essayer une de ces conditions :

- Pawtucket
- Pembroke
- OIE + Cap

## <a name="next-steps"></a>Étapes suivantes

C’est le premier didacticiel Azure recherche basé sur Java et le groupe de données de groupes de sécurité universels. Au fil du temps, nous allons étendre ce didacticiel pour démontrer les fonctionnalités de recherche supplémentaires que vous souhaitez utiliser dans vos solutions personnalisées.

Si vous avez déjà une idée dans Azure recherche, vous pouvez utiliser cet exemple comme une impulsion salutaire pour davantage d’expérimentation, peut-être l’augmentation de la [page de recherche](search-pagination-page-layout.md), ou l’implémentation de [navigation à facettes](search-faceted-navigation.md). Vous pouvez également améliorer sur la page de résultats de recherche en ajoutant des nombres et le traitement par lots des documents afin que les utilisateurs peuvent parcourir les résultats.

Nouvelle recherche Azure ? Nous vous recommandons d’essayer les autres didacticiels pour développer une compréhension de ce que vous pouvez créer. Visitez notre [page de documentation](https://azure.microsoft.com/documentation/services/search/) pour rechercher d’autres ressources. Vous pouvez également afficher les liens dans notre [vidéo et didacticiel liste](search-video-demo-tutorial-list.md) d’accéder à plus d’informations.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
