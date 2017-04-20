<properties 
    pageTitle="Étude de cas Developer recherche Azure : Comment WhatToPedia a créé un portail d’infomedia sur Microsoft Azure | Microsoft Azure | Service de recherche de nuage hébergé" 
    description="Apprenez à créer un informations meta et portail moteur de recherche à l’aide de la recherche d’Azure, un service de recherche de nuage hébergée pour les développeurs." 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Étude de cas de développement recherche Azure

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>Comment [WhatToPedia.com](http://whattopedia.com/) a créé un portail d’infomedia sur Microsoft Azure

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">L’idée</font> 


Notre objectif est de créer un portail d’information qui aide les clients à se connecter avec les détaillants, grâce à un très pertinent, la portée de la recherche, similaire à comment remonter les touristes de correspondance de portails avec les hôtels, restaurants et divertissement en territoire encore inexploré. 

Le portail que nous envisageons fournira une expérience de la recherche d’une qualité exceptionnelle sur les données du distributeur sur un marché donné, aide les acheteurs à trouver magasins basée sur l’emplacement et les autres équipements le détaillant propose. Nous amorcera le moteur de recherche avec un groupe de données initial, mais la hausse de la valeur qui doit être généré avec le temps, à l’aide d’abonnés détaillant publier des informations relatives à leur activité. Promotions, nouvelle marchandise, marques courantes, les services spécialisés en interne-– tous sont des exemples de données qui ajoute de la valeur sur notre site. Ces données sont automatiquement signalées et intégrées dans le corpus de recherche, une fois que le détaillant s’inscrit en tant qu’abonné. Publicité, ainsi que le modèle d’abonnement, fournit le chiffre d’affaires pour nos nouveaux clients.

Recherche sera le modèle d’interaction utilisateur prédominante, sur une plate-forme de nuage pure. À des fins d’échelle et de coûts-faible, presque tout ce que nous faisons, à partir de l’utilisation du portail pour le contrôle de code source, sera grâce à un service en ligne. À l’aide d’un moteur de recherche qui fournit les fonctionnalités que nous devons prêts à l’emploi, nous pouvons créer une application de recherche rapidement, sans avoir à créer et gérer un moteur de recherche nous-mêmes.

## <a name="what-we-built"></a>Ce que nous avons créé

WhatToPedia est une société d’infomedia de démarrage. Nous avons créé des [WhatToPedia.com](http://whattopedia.com/) – - actuellement en test-marché en Europe du Nord avec une date de mise en service du 2 février 2015. Notre clientèle est principalement des magasins de brique traditionnels qui ont besoin d’une présence en ligne abordable et facile à gérer et à maintenir.

Notre tâche consiste à attirer les clients grâce à une expérience de recherche en ligne exceptionnelle, amélioration des résultats en fonction de ville ou de voisinage, de marques, stocker les noms ou stocker des types. Attirer les clients a un effet boule de neige, motivant détaillants pour vous abonner à notre site de portail. Les abonnements sont payants, à un taux d’un prix abordable.

 ![][7] 

Après l’inscription d’un abonnement, un détaillant prend en charge son profil existant (créé initialement par nous à partir de données achetées), mise à jour avec des données supplémentaires à propos des annonces, des promotions ou des marques à une. Des capacités internes, comme les langues parlées, devises acceptées, achats détaxés, peut être automatiquement signalée pour mieux attirer les acheteurs qui recherchent de ces équipements.

## <a name="who-we-are"></a>Qui sommes-nous

Mon nom est Thomas Segato (Microsoft Consulting) et j’ai travaillé avec Jesper Boelling, entraîner un développeur à WhatToPedia, à la conception de la solution. 

WhatToPedia est une start-up, test marketing de ses activités nouveau portail en Suède, où la plupart des 60 000 détaillants est les briques traditionnels PME (petites et moyennes entreprises). Parce que nous savons que vos achats en Europe parle plusieurs langues et d’une personne exécute plusieurs devises, nous créer des solutions qui s’adapter à un client multilingue. Nous nécessaires et trouvé, un moteur de recherche qui prend en charge nos besoins multilingues dans Azure recherche.

Recherche de Azure a un changeur de jeu pour notre projet. Avant la commercialisation de recherche d’Azure, nous avons consenti énergie considérable par le défaut de créer notre propre moteur de recherche. En Azure comme un service en ligne a supprimé le plus gros obstacle technique et administrative de notre solution, ce qui signifie obtenir sur le marché plus rapidement et avec une expérience de recherche plus robuste.  

## <a name="how-we-did-it"></a>Comment nous l’avons fait

Notre vision a été de créer une infrastructure complète basée sur les services en nuage uniquement. Microsoft a été choisi comme la plate-forme stratégique car il a été le fournisseur qui a proposé la nécessaire services (pour la collaboration et développement), évoluent à la demande et des prix abordables.
 
### <a name="high-level-components"></a>Composants de haut niveau

Nous avons créé une entreprise, et pas seulement un site. Prise en charge de tous les efforts nécessaires à une gamme complète d’outils et d’applications. Nous avons adopté Visual Studio et Visual Studio Team Services pour la gestion de scrum, Office 365 pour la communication et la collaboration et bien entendu Microsoft Azure pour toutes les opérations relatives au site et de stockage et de développement, Online Team Foundation Service (TFS) pour le contrôle de code source. Avec Visual Studio, l’environnement IDE fourni provisionnement direct vers Azure, grâce à l’intégration à TFS Online offrant un gain de productivité supplémentaires.

Le diagramme ci-dessous illustre les composants de haut niveau utilisés dans l’infrastructure WhatToPedia.

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>Comment nous utilisons Microsoft Azure

En examinant les cases vertes dans le diagramme précédent, vous verrez que la solution de WhatToPedia s’appuie sur ces services :

- [Recherche Azure](https://azure.microsoft.com/services/search/)
- [Sites Azure à l’aide de MVC 4](https://azure.microsoft.com/services/websites/)
- [WebJobs Azure pour les tâches planifiées](../app-service-web/websites-webjobs-resources.md)
- [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [Stockage des objets BLOB Azure](https://azure.microsoft.com/services/storage/)
- [Remise de courrier électronique de SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Le cœur de la solution est de données et recherche. Le flux de données à partir du fournisseur de revendeur pour le client final est illustré ci-dessous :

  ![][9]

Stockage de données principal est le revendeur et les données de gestion de base de données de SQL Azure. Il est constitué par le groupe de données initial, ainsi que les données spécifiques détaillant ajoutées au fil du temps. Nous utilisons un WebJob Azure pour publier des mises à jour à partir de la base de données SQL pour le corpus de recherche dans recherche d’Azure.

### <a name="presentation-layer"></a>Couche de présentation

Le portail est un site Web d’Azure, implémenté dans MVC 4 et [Twitter des données d’amorçage](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Nous avons choisi MVC car il offre une approche beaucoup plus propre au format HTML de développement basée sur les formulaires d’ASP.NET. Pour éviter d’avoir à créer des applications pour plusieurs périphériques et mettre à jour de plusieurs plates-formes mobiles, Twitter de programme d’amorçage a été choisi pour prendre en charge tous les périphériques et plates-formes.

### <a name="authentication-permissions-and-sensitive-data"></a>L’authentification, les autorisations et les données sensibles

Acheteurs parcourir le site de façon anonyme. En tant que tel, aucune ouverture de session est requise pour les clients, ni nous stockent les données sur les consommateurs. 

Les détaillants sont une autre histoire. Ici, nous stockons les informations de profil accessible au public, informations de facturation et le contenu multimédia qu’ils souhaitent exposer sur le site. Chaque détaillant qui s’abonnent au site d’obtenir une connexion de l’utilisateur, utilisée pour authentifier l’utilisateur avant de mettre à jour le profil de l’abonné.  Nous stocker en toute sécurité toutes les données de l’abonné dans le stockage de base de données de SQL Azure et d’Azure BLOB.
Nous avons opté pour un modèle d’authentification basé sur l’authentification basée sur les formulaires de .NET. Nous avons choisi cette approche pour sa simplicité ; Nous n’avions pas besoin des rôles, d’une prise en charge de l’interface utilisateur et d’autres fonctionnalités superflues qui accompagnent d’autres approches. 

Pour vous assurer que les détaillants ne voient que les données qui appartiennent à leur, nous avons créé un détaillant ID pour chacun d’eux par la suite utilisée sur toutes les lire et écrire des opérations impliquant des données spécifiques au détaillant. Avec cette approche, nous avons constaté que nous ne pas besoin accorder des autorisations de base de données à des détaillants. Tous les détaillants interagissent avec le système sous un rôle de base de données unique, avec l’ID de revendeur comme notre technique d’isolation de données.

Étant donné que notre activité concerne tous les effets en aval (conduite d’entreprise plus aux détaillants, création d’incentive pour publier et s’abonner), nous pouvons tracer la ligne au niveau de la gestion des achats sur le web. Par conséquent, vous ne trouverez pas un panier d’achat sur notre site, ce qui simplifie les nos exigences en matière de sécurité. 

Un autre simplification employée a été à l’externalisation de nos opérations de fournisseurs de facturation et des comptes. Par informations de paiement client routage directement à une tierce partie ([SveaWebPay](http://www.sveawebpay.se/)), nous réduisons les risques à associer avec le stockage et la protection des données sensibles dans nos magasins de données. 

### <a name="search-engine"></a>Moteur de recherche

Le cœur de notre solution est le moteur de recherche basé sur le service de recherche d’Azure. Au départ, nous avons créé un moteur de recherche personnalisé, mais pendant ce processus, nous avons réalisé la complexité effort a été très bien et que vous êtes invité à envisager d’autres alternatives. 

Fonctionnalités de base qui ont été plus importantes pour nous inclus :

- Filtres
- Navigation à facettes
- Amélioration des résultats
- Mise en page AJAX

Une recherche sur internet nous mis à la vidéo suivante, qui nous d’essayer Azure recherche inspiré : [Plongée à TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

Après observation de la vidéo, nous avons prêts à créer un prototype en fonction de ce que nous avons vu. Nous avions déjà un modèle de données dans MVC, création du prototype n’a pas été simple car les données contenues les termes de recherche, et que nous avions déjà travaillé les exigences pour la façon dont nous souhaitions trier, facette et filtrer les données. 

Voici comment nous avons créé le prototype.

**Configurer le Service de recherche Azure**

1. Connexion au portail classique d’Azure et ajouté le service de recherche à notre abonnement. Nous avons utilisé la version partagée (gratuite avec notre abonnement).
2. Créer un index. Pour le prototype, nous avons utilisé le portail de l’interface utilisateur pour définir les champs de recherche et à créer les profils de score. Notre profil d’évaluation est basée sur les données de localisation : pays | Ville | adresse (voir : ajouter des profils d’évaluation).
3. Copiez l’URL du service et admin-clé api pour les fichiers de configuration. Cette clé se trouve sur la page de service de recherche dans le portail, et il est utilisé pour authentifier le service.
    
**Développer un travail d’indexeur de recherche – Console Windows**

1. Lire tous les revendeurs à partir de la base de données.
2. Appeler l’API du Service de recherche Azure pour télécharger un par un les revendeurs (voir : http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Définir une propriété de base de données que le revendeur est indexé pour l’indexation incrémentielle. Nous avons fait cela en ajoutant un champ 'indexeur' qui stocke l’état de l’index de chaque profil (indexé ou non). 

Consultez l’annexe pour l’extrait de code qui crée la tâche de l’indexeur.

**Développer un portail Web de recherche – MVC**

1. Appelez le Service de recherche d’Azure pour obtenir tous les documents de recherche (voir : http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extrait suivant à partir de la réponse de service de recherche (en utilisant json.net http://james.newtonking.com/json)
   - Résultats
   - Facettes
   - Compte de résultat
   - Développer une interface utilisateur pour l’affichage des résultats de la recherche, facettes et le nombre de (nous avions déjà cela).

Voici le code que nous avons utilisé pour obtenir les résultats de la recherche d’Azure :

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**AMPLIFICATION par emplacement**

Probablement plus important à ce niveau pour vérifier dans le prototype inclus en ajoutant le mot clé de recherche emplacement à la requête. Il est essentiel de notre portail que si un utilisateur entre un nom de ville dans la recherche de requête, qui les revendeurs dans la ville donnée seraient un rang plus élevé que le revendeur ayant le mot clé de ville dans la description. De cette exigence, nous avons utilisé un profil d’évaluation pour classer le champ Ville plus élevé que les autres champs.

**Prise en charge de plusieurs langues**

Nous avions besoin d’afficher les résultats de recherche corrects dans les langues appropriées et proposent une option permettant de trouver les mêmes résultats dans des langues différentes. Les deux côtés à ce problème sont les suivants : 

- Rechercher des mots dans plusieurs langues
- Afficher les résultats de recherche dans la langue correcte

Nous avons résolu la partie de la présentation en ajoutant un document pour chaque langue, avec le texte localisé et une propriété avec la langue. Lorsqu’un utilisateur entre un terme de recherche, nous utilisateur `$filter` expressions pour filtrer sur la langue de l’utilisateur a choisi.

Chaque document a une propriété masquée appelée « villes » basés sur le type de collection. Cette propriété stocke des noms de ville dans toutes les langues, ce qui permet de rechercher dans plusieurs langues.

###<a name="data-storage"></a>Stockage de données

Toutes les données (comptables, abonnements et profil) sont stockées dans la base de données de SQL. Tous les fichiers multimédias sont stockés dans le stockage BLOB d’Azure, y compris les images et les vidéos fournies par le détaillant. Utilisation du stockage BLOB distinct isole les effets du téléchargement de fichiers ; les fichiers ne sont jamais mixtes avec le site Web, nous n’avez pas besoin de reconstruire le site chaque fois que nous ajoutons des fichiers.

Un avantage important de notre conception de stockage est que plusieurs développeurs peuvent partager un stockage de développement unique. L’une des exigences pour le projet WhatToPedia était d’être en mesure de créer un environnement de développement dans les 15 minutes, y compris les vidéos, les images et les données de revendeurs. Par l’obtention de données les plus récentes de TFS en ligne, exécution d’un script SQL et l’exécution de la tâche d’importation, un environnement complet peut être relevé pas du tout. Cette pratique améliore également le processus de pré-production.

###<a name="webjobs"></a>WebJobs

WebJobs d’Azure nous permet de mettre à jour les données de l’index. En créant un travail d’indexeur de recherche, la partie indexation a été très facile d’intégrer à notre solution. La seule modification de code, nous avons était pour s’adapter à la tâche d’indexeur pour ajouter une `Indexed` champ à notre modèle de données pour indiquer l’état de l’index. Lorsqu’un nouveau profil est ajouté ou mis à jour, la `Indexed` champ est définie sur false. En va de même si le détaillant les modifications des données de profil de son via le portail.  

Le travail de recherche de toutes les lignes ayant `Indexed` la valeur false. Lorsqu’il trouve la ligne, le document est validé à la recherche d’Azure, puis le `Indexed` champ est défini sur true. Nous n’avions pas à planifier l’ajout par rapport à la mise à jour des données, car le service de recherche d’Azure effectivement s’occupe de cela. Si vous ajoutez un document qui est déjà présent, le service de faire une mise à jour automatiquement.

Tous les travaux web ont été développés en tant qu’applications de console qui peuvent être téléchargées vers des sites web Azure sous forme de fichiers ZIP, décompressées et ensuite planifiées.

La tâche est planifiée pour s’exécuter comme une tâche planifiée web toutes les 5 minutes. Nous avons calculé que le service prend environ 3 minutes à télécharger 3 000 documents, qui était au sein de nos besoins. 

> [AZURE.NOTE] Il existe une fonctionnalité d’indexeur de prototype qui a récemment été introduite dans la recherche d’Azure. Cette fonctionnalité est trop tard pour que nous puissions utiliser dans notre première version, mais elle semble résoudre le même problème que nous avons utilisé notre travail de l’indexeur, qui consiste à automatiser les opérations de chargement de données.


###<a name="backup-strategy"></a>Stratégie de sauvegarde

Nous avons conçu une stratégie de sauvegarde multicouches pour récupérer à partir d’un éventail de scénarios, à partir d’un échec catastrophique, à la restauration d’une transaction individuelle. Les actifs à protéger comprennent trois sortes de données (site web, données d’abonné et fichiers multimédias). 

Tout d’abord, en conservant le code source de site web dans TFS en ligne, nous savons que si le site tombe en panne, nous pouvons le recréer en republiant de TFS. 

Données de l’abonné dans la base de données de SQL Azure sont bien plus sensibles. Nous retourner cette intégrés à l’aide de fonctionnalité (voir [restauration et sauvegarde de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)). La planification de sauvegarde est la sauvegarde de base de données complète une fois par semaine, une fois par jour des sauvegardes différentielles de base de données et sauvegardes du journal des transactions toutes les 5 minutes.  Compte tenu de la taille des données, cette solution est plus qu’adéquate pour nos volumes de données immédiats et futurs.

Troisièmement, nous stockons les fichiers image et vidéo dans le stockage des objets BLOB Azure. Nous sommes en cours d’évaluation le plan de sauvegarde intégrale pour ces données, en tenant compte d’Explorateur de mûre de marais pour Azure comme solution potentielle. Pour le moment, nous utilisons un WebJob pour copier des images et des vidéos vers un autre emplacement.

##<a name="what-we-learned"></a>Ce que nous avons appris

Nous avions déjà des données, il n’a pas été facile d’établir la preuve de concept. Dans les heures, nous avions un prototype à facettes et profils occupe la place au classement des compteurs, la pagination, des résultats de recherche. Les résultats de recherche ont donc précis, que nous avons décidé de supprimer certains des filtres présentés à l’utilisateur final. 

La surprise des plus agréables pour nous a été la vitesse à laquelle nous pouvons apprendre recherche d’Azure, et combien nous avons obtenu. Littéralement, nous établi la preuve de concept en quelques heures (voir la Remarque ci-dessous), remplaçant les 500 lignes de code par 3 lignes de code dans l’application front-end (plus un nouveau WebJob) et l’obtention de meilleurs résultats. 

Précédemment, notre code mis en œuvre la pagination, le nombre et les comportements qui sont standard pour rechercher. À l’aide de la recherche d’Azure, les résultats que nous obtenons incluent les résultats de la recherche, facettes, données, comptes--toutes les choses que nous nécessaires et que vous rencontrez de nous fournir la pagination. Il également inclus la navigation à facettes amplification et intégrée, nous n’avions pas dans notre solution d’origine.

Le plus grand défi au cours de la mise en oeuvre a été que c’était une version d’évaluation et il était difficile de trouver des informations et des expériences partagées. Une fois que nous avons connecté quelques points, nous avons détecté que le service Search Azure était relativement simple en raison de son format de données API REST et JSON. Nous pouvons appeler le framework directement à partir de plug-ins de source plus ouverts comme JQuery JSON.Net, et nous pouvons utiliser des outils comme Fiddler pour une expérimentation rapide et le débogage. 

> [AZURE.NOTE] Outre les données prêt, il a permis que ceux d'entre nous création du prototype déjà entendu fonctionnement de la technologie, nous rendre plus productifs et plus appreciative des fonctions intégrées de la recherche. Si vous avez besoin progresser sur la construction de requête de recherche, une navigation à facettes, filtres, etc. vous devriez prototypage prend plus de temps. 

###<a name="controlling-facets-in-the-search-presentation-page"></a>Facettes de contrôle dans la page de présentation de la recherche

Un des nos apprentissages au cours de la preuve de concept a été de planifier soigneusement dès le départ des facettes. Après le chargement d’un grand nombre de données dans la solution, nous avons vu que le volume des facettes était trop élevé pour présenter aux utilisateurs. 

Nous a résolu par la limitation du paramètre nombre de facettes. Le paramètre count impose une limite du nombre de facettes renvoyée à l’utilisateur. Un lien qui inclut une présentation du paramètre count se trouve [ici](search-faceted-navigation.md).

###<a name="webjobs-for-scheduling-tasks"></a>WebJobs pour la planification des tâches

Recherche Azure n’était pas uniquement agréable surprise pour nous. Nous avons découvert que l’utilisation de WebJobs pour automatiser nos opérations de chargement de données à la recherche d’Azure a été largement supérieure à notre approche précédente, qui a entraîné l’utilisation une VM dédiée exécutant le Planificateur de tâches de Windows, avec les tâches planifiées pour la mise à jour de l’index de recherche. WebJobs a été plus facile à configurer et plus facile à déboguer et bien sûr bien moins cher que d’avoir à payer pour un ordinateur virtuel dédié.

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Explorateur de stockage BLOB Azure de mise à jour d’images

Nous avons constaté qu’à l’aide de l' [Explorateur de stockage BLOB Azure](https://azurestorageexplorer.codeplex.com/) (disponible sur codeplex) peuvent être très utiles dans la gestion des images et des vidéos mises à jour du site. Nous l’utiliser comme un outil de développement pour mettre à jour manuellement les images et les vidéos qui font partie de notre site principal. Nous a déterminé qu’il est plus flexible que le déploiement des modifications sur le portail et élimine une itération de test complet chaque fois que nous avons besoin de mettre à jour une image. 

##<a name="a-few-final-words"></a>Quelques mots finals

Grâce à l’excellents nos collègues de WhatToPedia pour ce qui nous permet de partager leur histoire !  

Nous espérons que vous avez trouvé cette étude de cas utile. Si vous passez à utiliser la recherche d’Azure, je vous recommande de quelques ressources pour vous le long de la vitesse :

- [Forum MSDN dédié à la recherche d’Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow dispose également d’une balise](http://stackoverflow.com/questions/tagged/azure-search)
- [Page de la documentation sur Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Documentation de recherche Azure sur MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>Appendice : Recherche indexeur WebJob

Le code suivant construit l’indexeur mentionné dans la section sur la création du prototype.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
