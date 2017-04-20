<properties
    pageTitle="Mise en route de la recherche d’Azure dans NodeJS | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Passez en revue la création d’une application de recherche sur un service de recherche de nuage hébergé sur Azure comme votre langage de programmation à l’aide de NodeJS."
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

# <a name="get-started-with-azure-search-in-nodejs"></a>Mise en route de la recherche d’Azure dans NodeJS
> [AZURE.SELECTOR]
- [Portail](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Découvrez comment générer une application de recherche NodeJS personnalisée qui utilise la recherche d’Azure pour son expérience de recherche. Ce didacticiel utilise l' [API REST de Azure Search Service](https://msdn.microsoft.com/library/dn798935.aspx) pour construire les objets et les opérations utilisées dans cet exercice.

Nous avons utilisé [NodeJS](https://nodejs.org) et NPM, [Sublime texte 3](http://www.sublimetext.com/3)et de Windows PowerShell sur Windows 8.1 pour développer et tester ce code.

Pour exécuter cet exemple, vous devez disposer d’un service de recherche d’Azure, dont vous pouvez vous connecter pour le [Portail Azure](https://portal.azure.com). Pour obtenir des instructions détaillées, reportez-vous à la section [créer un service de recherche d’Azure dans le portail](search-create-service-portal.md) .

## <a name="about-the-data"></a>Sur les données

Cet exemple d’application utilise les données de [United States géologique Services (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtré sur l’état de Rhode Island pour réduire la taille du groupe de données. Nous allons utiliser ces données pour créer une application de recherche renvoyant des bâtiments d’éléments géographiques comme hôpitaux et les écoles, ainsi que les caractéristiques géologiques comme flux, lacs et sommets.

Dans cette application, le programme **DataIndexer** crée et charge l’index à l’aide d’une construction [d’indexeur](https://msdn.microsoft.com/library/azure/dn798918.aspx) , récupérer le groupe de données de groupes de sécurité universels filtré à partir d’une base de données de SQL Azure public. Informations d’identification et de connexion à la source de données en ligne des informations sont fournies dans le code de programme. Aucune configuration supplémentaire n’est nécessaire.

> [AZURE.NOTE] Nous avons appliqué un filtre sur ce groupe de données à rester dans la limite de 10 000 documents de la couche de tarification libre. Si vous utilisez la couche standard, cette limite ne s’applique pas. Pour plus d’informations sur les capacités de chaque niveau de tarification, consultez [limites de service de recherche](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Trouver le nom du service et la clé d’api de votre service de recherche d’Azure

Après avoir créé le service, retourner au portail pour obtenir l’URL ou `api-key`. Les connexions à votre service de recherche vous devez disposer à la fois l’URL et une `api-key` pour l’authentification de l’appel.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre de raccourcis, cliquez sur le **service de recherche** pour répertorier tous les services de recherche d’Azure mis en service pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4. Sur le tableau de bord de service, vous verrez des mosaïques pour les informations essentielles, ainsi que l’icône de clé pour les clés de l’administration de l’accès à.

    ![][3]

5. Copiez l’URL du service, une clé d’administration et une clé de la requête. Vous aurez besoin des trois plus tard, lorsque vous les ajoutez au fichier config.js.

## <a name="download-the-sample-files"></a>Téléchargez les fichiers exemples

Utilisez l’une des approches suivantes pour télécharger l’exemple.

1. Accédez à [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Cliquez sur **Télécharger le code postal**, enregistrer le fichier .zip et extraire tous les fichiers qu’il contient.

Toutes les modifications de fichier et les instructions d’exécution sont effectuées par rapport à des fichiers dans ce dossier.


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Mettre à jour le config.js. avec votre URL de service de recherche et de la clé d’api

À l’aide de l’URL et la clé api que vous avez copiés précédemment, de spécifier l’URL, clé admin et requête-clé dans le fichier de configuration.

Clés de l’administration accordent un contrôle total sur les opérations de service, y compris la création ou la suppression d’un index et le chargement des documents. En revanche, les clés de la requête sont pour les opérations en lecture seule, généralement utilisées par les applications clientes qui se connectent à la recherche d’Azure.

Dans cet exemple, nous incluons la clé de requête pour vous aider à renforcer la meilleure pratique de l’utilisation de la clé de requête dans les applications clientes.

La capture d’écran suivante montre **config.js** ouverts dans un texte, l’éditeur avec les entrées pertinentes délimitées afin que vous puissiez voir où mettre à jour le fichier avec les valeurs qui sont valides pour votre service de recherche.

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Un environnement d’exécution de l’exemple d’hôte

L’exemple requiert un serveur HTTP, que vous pouvez installer globalement à l’aide de npm.

Utiliser une fenêtre PowerShell pour les commandes suivantes.

1. Accédez au dossier qui contient le fichier **package.json** .
2. Type de `npm install`.
2. Type de `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Construction de l’index et exécuter l’application

1. Type de `npm run indexDocuments`.
2. Type de `npm run build`.
3. Type de `npm run start_server`.
4. Dirigez votre navigateur à`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Effectuez une recherche sur les données de groupes de sécurité universels

Le jeu de données de groupes de sécurité universels inclut les enregistrements qui sont pertinentes à l’état de Rhode Island. Si vous cliquez sur **Rechercher** sur une zone de recherche, vous obtenez les entrées supérieure de 50, qui est la valeur par défaut.

Entrer un terme de recherche donnera le moteur de recherche quelque chose pour continuer. Essayez d’entrer un nom régionaux. « Roger Williams » a été le premier gouverneur de Rhode Island. Plusieurs des parcs, des bâtiments et des écoles sont nommés d’après lui.

![][9]

Vous pouvez également essayer une de ces conditions :

- Pawtucket
- Pembroke
- OIE + Cap


## <a name="next-steps"></a>Étapes suivantes

C’est le premier didacticiel Azure recherche basé sur NodeJS et le groupe de données de groupes de sécurité universels. Au fil du temps, nous allons étendre ce didacticiel pour démontrer les fonctionnalités de recherche supplémentaires que vous souhaitez utiliser dans vos solutions personnalisées.

Si vous avez déjà une idée dans Azure recherche, vous pouvez utiliser cet exemple comme une impulsion salutaire pour essayer suggesters (requêtes de type transfert ou de la saisie semi-automatique), des filtres et navigation à facettes. Vous pouvez également améliorer sur la page de résultats de recherche en ajoutant des nombres et le traitement par lots des documents afin que les utilisateurs peuvent parcourir les résultats.

Nouvelle recherche Azure ? Nous vous recommandons d’essayer les autres didacticiels pour développer une compréhension de ce que vous pouvez créer. Visitez notre [page de documentation](https://azure.microsoft.com/documentation/services/search/) pour rechercher d’autres ressources. Vous pouvez également afficher les liens dans notre [vidéo et didacticiel liste](search-video-demo-tutorial-list.md) d’accéder à plus d’informations.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
