<properties
   pageTitle="Présentation microservices | Microsoft Azure"
   description="Une vue d’ensemble de pourquoi il est important pour le développement d’applications modernes de création d’applications en nuage grâce à une approche microservices et comment Azure Fabric de Service fournit une plate-forme pour atteindre cet objectif"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="mfussell"/>

# <a name="why-a-microservices-approach-to-building-applications"></a>Pourquoi un microservices approche à la génération d’applications ?
En tant que développeurs de logiciels, il n’est pas nouveau dans notre vision de factorisation d’une application en composants. Il est le paradigme central de composants réutilisables, abstractions logicielles et orientation objet. Aujourd'hui, cette factorisation a tendance à prendre la forme de classes et les interfaces entre les couches de technologie et de bibliothèques partagées. En règle générale, une approche par paliers est prise avec un stockage back-end, la logique métier de couche intermédiaire et une interface utilisateur frontale. Quel *a* changé au cours des quelques dernières années, c’est que nous, en tant que développeurs, créons des applications distribuées pour le cloud, piloté par l’entreprise.

L’évolution des besoins métiers sont les suivants :

- Créer et utiliser un service à l’échelle afin d’atteindre les clients dans nouvelles régions géographiques (par exemple).
- Livraison plus rapide des fonctionnalités et capacités à être en mesure de répondre aux demandes des clients, d’une manière agile.
- Meilleure utilisation des ressources pour réduire les coûts.

*Comment* créer des applications affectent ces besoins d’entreprise.

Pour plus d’informations sur l’approche d’Azure microservices lire [Microservices : une révolution de l’application par le nuage](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolithique et une approche de conception microservice
Toutes les applications évoluent dans le temps. Applications réussies évoluent en étant utiles aux utilisateurs. Les applications échouées évoluer pas et finalement la sont déconseillées. La question se pose : combien connaissez-vous vos besoins aujourd'hui et qu’ils seront à l’avenir ? Par exemple, supposons que vous créez une application de génération de rapports pour un service. Vous êtes certain que l’application reste dans la portée de votre société et que les rapports sont courtes. Le choix de l’approche est différent, par exemple, création d’un service de diffusion de contenu vidéo sur des dizaines de millions de clients. Parfois, un élément à la porte comme preuve de concept est le facteur déterminant, sachant que l’application peut être modifiée ultérieurement. Il est peu trop d’ingénierie quelque chose qui ne sont jamais utilisées. Il est le compromis technique habituel. D’autre part, lorsque les entreprises parlent de création pour le nuage, l’attente est l’utilisation et la croissance. Le problème est que la croissance et l’évolutivité sont imprévisibles. Nous souhaitons pouvoir prototype rapidement tout aussi savoir que nous sommes sur un chemin d’accès pour gérer avec succès futurs. Il s’agit de l’approche de la production au plus juste de démarrage : générer, mesurer, apprendre, effectuer une itération.

Au cours de l’ère client-serveur, nous avaient tendance à se concentrer sur la création d’applications hiérarchisées en utilisant des technologies spécifiques de chaque niveau. Le terme application « monolithiques » a vu le jour ces approches. Les interfaces avaient tendance à être entre les niveaux et plus étroitement la conception utilisée entre les composants au sein de chaque niveau. Les développeurs conçu et pris en compte les classes compilées dans les bibliothèques, quelques fichiers exécutables et des DLL, reliés entre eux. Il existe des avantages à une telle approche de conception monolithique. Il est souvent plus simple de concevoir et a des appels plus rapides entre les composants, dans la mesure où ces appels sont souvent sur IPC. En outre, tout le monde teste un seul produit, ce qui tend à être plus personnes-ressources efficace. L’inconvénient est qu’un couplage étroit entre les résultats de plusieurs niveaux de calques et que vous ne pouvez pas réduire des composants individuels. Si vous avez besoin exécuter les correctifs ou mises à niveau, vous devez attendre que d’autres personnes à leur test terminé. Il est plus difficile d’être agile.

Microservices adresse ces inconvénients et plus étroitement en rapport avec les besoins de l’entreprise précédent, mais ils ont également des inconvénients et des avantages que représenterait. Les avantages de microservices sont que chacun d’eux en général encapsule les fonctionnalités d’entreprise plus simple, qui vous mettre à l’échelle vers le haut ou vers le bas, test, déployez et gérer de manière indépendante. L’un des avantages d’une approche microservice important sont que les équipes sont motivées davantage de scénarios d’entreprise que par technologie, ce qui encourage de l’approche par niveau. Dans la pratique, les petites équipes développent un microservice basée sur un scénario de client, à l’aide de toutes les technologies qu’ils choisissent. En d’autres termes, l’organisation n’a pas besoin de normaliser tech pour mettre à jour les applications monolithiques. Équipes des services propres peuvent faire ce qui leur convient en fonction de l’expertise de l’équipe ou à ce qui est plus approprié pour le problème à résoudre. Dans la pratique, un ensemble de technologies recommandées, infrastructure d’application, est préférable comme un stockage NoSQL ou un site web particulier.

L’inconvénient de la microservices est fourni dans la gestion de l’augmentation du nombre des entités distinctes et de gestion des déploiements plus complexes et de contrôle de version. Le trafic réseau entre les microservices augmente, ainsi que les latences du réseau correspondant. Beaucoup de bavardages, services granulaires est une recette pour un cauchemar en termes de performances. Sans les outils permettant d’afficher ces dépendances, il est difficile de « voir » l’ensemble du système. Les normes sont les apporter l’approche microservice travail en s’accordant sur la façon de communiquer et de tolérance des choses que vous avez besoin d’un service, et non rigides contrats. Il est important de définir ces contacts en amont dans la conception, dans la mesure où les services de mises à jour indépendamment un de l’autre. Une autre description inventée pour concevoir une approche microservices est « SOA fine ».


***Dans sa plus simple expression, l’approche de conception microservices est sur une fédération découplée de services indépendantes à chacun, et les modifications standards convenus pour la communication.***


En plus des applications de cloud sont produites, personnes découvrir que cette décomposition de l’application générale des services indépendants, en fonction d’un scénario est une meilleure approche à plus long terme.

## <a name="comparison-between-application-development-approaches"></a>Comparaison entre les approches de développement d’application

![Développement d’applications de plate-forme de service Fabric][Image1]

1. Une application monolithique contient des fonctionnalités propres au domaine et est normalement divisée par couches fonctionnelles, telles que le web, métier et données.

2. Vous mettre à l’échelle une application monolithique en dupliquant sur plusieurs machines virtuelles/serveurs/conteneurs.

3. Une application de microservice sépare les fonctionnalités dans des services distincts plus petits.

4. Cette approche peut évoluer par le déploiement de chaque service séparément, créer des instances de ces services sur les serveurs/VM/conteneurs.


Conception avec un microservice approche n’est pas la panacée pour tous les projets, mais il aligne plus étroitement avec les objectifs d’entreprise décrits précédemment. Commençant par une approche monolithique peut être acceptable si vous savez que plus tard vous aura pas la possibilité de retravailler le code dans un modèle de microservice, si nécessaire. Plus fréquemment, vous commencez avec une application monolithique et lentement fragmentez en plusieurs étapes, à partir des domaines qui doivent être plus évolutives et plus agile.

Pour résumer, la microservice consiste à composer votre application de nombreux services plus petits.  Les services s’exécutent dans des conteneurs déployés sur un cluster d’ordinateurs. Petites équipes développement d’un service qui se concentre sur un scénario et tester indépendamment, version, déploiement et mettre à l’échelle chaque service afin que l’application dans son ensemble peut évoluer.

## <a name="what-is-a-microservice"></a>En quoi consiste un microservice ?

Il existe différentes définitions de microservices, et fournit des nombreuses bonnes ressources qui fournissent leurs propres points de vue et les définitions de recherche sur Internet. Toutefois, la plupart des caractéristiques suivantes des microservices est largement convenue :

- Encapsuler un scénario client ou commercial. Quel est le problème à résoudre ?
- Développé par une petite équipe d’ingénierie des procédés.
- Écrit dans n’importe quel langage de programmation et utiliser n’importe quelle infrastructure.
- Composé de code et l’état (le cas échéant), qui sont tous deux indépendamment une version déployée et mise à l’échelle.
- Interagir avec les autres microservices sur les protocoles et les interfaces bien définies.
- Avoir des noms uniques (URL) permet de résoudre leur emplacement.
- Rester cohérents et disponibles en présence de défaillances.

Vous pouvez résumer ces caractéristiques dans :

***Microservice applications sont composées de petites, créées de façon indépendante et évolutives services axés sur le client qui communiquent entre eux via les protocoles standard avec des interfaces bien définies.***


Nous avons abordé les deux premiers points dans la section précédente, et maintenant nous développer et à clarifier les autres.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Écrit dans n’importe quel langage de programmation et utiliser n’importe quelle infrastructure
En tant que développeurs, nous devons être libres de choisir n’importe quel langage ou un framework nous, en fonction de nos compétences ou les besoins du service. Dans certains services, vous pouvez valeur les avantages de performance de C++ avant tout autre. Dans d’autres services, la facilité de développement managé dans C# ou Java peut être plus importante. Dans certains cas, vous devrez peut-être utiliser une bibliothèque spécifique de tiers, technologie de stockage de données ou moyens d’exposer le service aux clients.

Une fois que vous avez choisi une technologie, vous sont fournis à la gestion opérationnelle ou du cycle de vie et la mise à l’échelle du service.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permet de code et l’état de version indépendamment, déploiement et mise à l’échelle  

Toutefois vous choisissez d’écrire vos microservices, le code et éventuellement l’état doit indépendamment déployer, mettre à niveau et mise à l’échelle. Il s’agit en fait un des problèmes plus difficiles à résoudre, car il se résume à votre choix de technologies. Pour la mise à l’échelle, de comprendre comment une partition (ou partagé) à la fois le code et l’état est difficile. Lorsque le code et l’état utilisent des technologies distinctes (qui aujourd'hui, ils ont tendance à faire), les scripts de déploiement pour votre microservice doivent pouvoir faire face à la mise à l’échelle toutes les deux. Cela est également sur la réactivité et de flexibilité, vous pouvez mettre à niveau certains de la microservices sans avoir à mettre à niveau simultanément tous les.
Retournez à la monolithique par rapport à l’approche de microservice pendant un moment, le diagramme suivant illustre les différences dans l’approche de stockage de l’état.

#### <a name="state-storage-between-application-styles"></a>Stockage de l’état entre les styles d’application
![Stockage de l’état service Fabric plate-forme][Image2]

***Sur la gauche est l’approche monolithique, avec une seule base de données et niveaux de technologies spécifiques.***

***Sur le côté droit est l’approche de microservices, un graphique de microservices interconnectés où état est généralement associé à le microservice et différentes technologies sont utilisées.***

Dans une approche monolithique, existe en général une seule base de données utilisée par l’application. L’avantage est qu’il s’agit d’un emplacement unique, ce qui vous permet de déployer. Chaque composant peut avoir une seule table pour stocker son état. Les équipes doivent être stricte séparation d’état, ce qui constitue un véritable défi. Il y a inévitablement temptations pour ajouter une nouvelle colonne à une table de clients existante, faire une jointure entre les tables et créer des dépendances au niveau de la couche de stockage. Dans ce cas, vous ne peut pas mettre à l’échelle des composants individuels. Dans l’approche microservices, chaque service gère et stocke son propre état. Chaque service est responsable de la mise à l’échelle à la fois le code et l’état pour répondre aux besoins du service. Un inconvénient est que lorsqu’il est nécessaire de créer des vues ou des requêtes, des données de votre application vous devez exécuter des requêtes dans les magasins d’état de disparates. En général, cela est résolu en ayant un microservice séparé qui génère une vue sur une collection de microservices. Si vous devez exécuter plusieurs requêtes ad-hoc sur les données, chaque microservice doit envisager d’écrire ses données dans un data warehouse service pour analytique en mode hors connexion.

Gestion des versions sont spécifique à la version déployée d’un microservice ainsi que plusieurs, différentes versions, déployer et exécuter côte à côte. Versioning des aborde les scénarios où une version plus récente d’un microservice échoue au cours de la mise à niveau et doit revenir à une version antérieure. L’autre scénario pour le versioning effectue les tests de type A/B, où différents utilisateurs confrontés à des versions différentes du service. Par exemple, il est courant de mettre à niveau un microservice pour un ensemble spécifique de clients pour tester la nouvelle fonctionnalité avant de la déployer plus largement. Après la gestion du cycle de vie de microservices, est désormais nous amène à la communication entre eux.


### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagit avec les autres microservices sur les protocoles et les interfaces bien définies

Cette rubrique doit grande attention ici, dans la mesure où il existe une documentation complète sur l’architecture orientée service publié au cours des 10 dernières années décrivant les modèles de communication. En général, communication de service utilise une approche reste avec les protocoles HTTP et TCP ou JSON et XML comme format de sérialisation. Il s’agit du point de vue de l’interface, adopter l’approche de la conception web. Mais rien ne vous empêche d’utiliser des protocoles binaires ou vos propres formats de données. Préparez-vous pour les personnes à avoir un temps plus difficile à l’aide de votre microservices si ceux-ci sont librement accessibles.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Possède un nom unique (URL) permet de résoudre son emplacement

N’oubliez pas la façon dont nous utiliserons indiquant que l’approche de microservice est comme le web ? Comme le web, votre microservice doit être adressable partout où il est en cours d’exécution. Si vous vous focalisez sur des machines et celui qui est en cours d’exécution un microservice particulier, choses iront rapidement incorrects. De la même manière que le système DNS résout une URL particulière pour un ordinateur particulier, votre microservice doit avoir un nom unique afin que son emplacement actuel est détectable. Microservices besoin de noms adressables qui les rendent indépendantes de l’infrastructure qu’ils exécutent sur. Cela implique qu’il existe une interaction entre la façon dont votre service est déployé et comment il est découvert, dans la mesure où il doit être un Registre de service. De même, lorsqu’un ordinateur tombe en panne le Registre service doit vous indiquer où le service est en cours d’exécution. Ceci nous amène à la rubrique suivante : la résilience et la cohérence.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Reste cohérente et disponible en présence de défaillances

Traitement des défaillances inattendues est un des problèmes plus difficiles à résoudre, en particulier dans un système distribué. La plupart du code que nous écrivons en tant que développeurs est gestion des exceptions, et c’est aussi où le plus de temps passé dans le test. Mais le problème est plus complexe que d’écrire du code pour gérer les défaillances. Que se passe-t-il en cas d’échec de la machine exécutant le microservice ? Non seulement vous devez détecter cet échec microservice (problème sur son propre disque dur), mais vous devez également quelque chose pour redémarrer votre microservice. Un microservice doit être résistant aux pannes et redémarrez souvent sur un autre ordinateur pour des raisons de disponibilité. Il est également fourni à l’état a été enregistré au nom de la microservice, où peut il récupérer cet état à partir de, et si elle est capable de redémarrer correctement. En d’autres termes, il doit être de résilience dans le calcul (le processus redémarre) ainsi que la résistance de l’état ou les données (sans perte de données et des données reste cohérent).

Les problèmes de résilience sont décuplées pendant les autres scénarios, par exemple lorsque des échecs se produisent au cours d’une mise à niveau de l’application. Le microservice, qui fonctionne avec le système de déploiement n’a pas besoin de récupérer. Il doit également décider ensuite si elle peut continuer à se déplacer en avant vers la version la plus récente ou à la place de restaurer une version précédente pour maintenir un état cohérent. Des questions telles que s’il existe suffisamment d’ordinateurs conserver le mouvement vers l’avant et comment récupérer des versions précédentes de la nécessité de microservice à prendre en compte. Ceci requiert le microservice afin d’émettre les informations de santé pour être en mesure de prendre ces décisions.

### <a name="reports-health-and-diagnostics"></a>Les rapports état de santé et diagnostics

Cela peut paraître évident et il est souvent négligé, mais il est essentiel qu’un microservice signale sa santé et les tests de diagnostic. Dans le cas contraire, il est de peu d’informations à partir d’un point de vue des opérations. Mise en corrélation des événements de diagnostic sur un ensemble de services indépendants et le traitement des décalages d’horloge de machine au sens de l’ordre des événements sont difficile. De la même manière que vous interagissez avec un microservice sur convenu de protocoles et de formats de données, il émerge nécessité d’une standardisation dans comment connecter la santé et des événements de diagnostic qui finissent dans un récepteur d’événements pour l’interrogation et l’affichage. Dans une approche microservices, il est clé que les différentes équipes s’accordent sur un format d’enregistrement unique.  Il doit être une approche cohérente à l’affichage des événements de diagnostic de l’application dans son ensemble.

La santé est différente de diagnostics. La santé est sur le microservice rapport son état actuel à prendre les mesures appropriées. Un bon exemple fonctionne avec les mécanismes de mise à niveau et de déploiement pour maintenir la disponibilité. Un service peut être actuellement en mauvais état en raison d’un arrêt de processus ou redémarrage, mais encore opérationnel de l’ordinateur. Il est la dernière chose que vous devez faire cela pire en effectuant une mise à niveau. La meilleure approche consiste à faire une enquête en premier, ou attendez le microservice à récupérer. Événements d’état à partir d’un microservice nous permettent de prendre des décisions informées et, en effet, aider à créer des services de réparation automatique.

## <a name="service-fabric-as-a-microservices-platform"></a>TISSU de service comme une plate-forme de microservices

Azure Fabric de Service issue des passage de Microsoft proposant des produits de zone, qui étaient généralement monolithiques de style, à la fourniture de services. L’expérience de construction et d’exploitation des services volumineux, tels que les bases de données SQL d’Azure et DocumentDB, en forme de tissu de Service. La plate-forme au fil du temps, comme les services de plus adopté. Surtout, Fabric du Service devait exécuter anywhere : non seulement dans Azure, mais aussi dans les déploiements de Windows Server autonome.

***Le Service Fabric vise à résoudre les problèmes de création et d’exécution d’un service et utiliser des ressources d’infrastructure efficacement, afin que les équipes peuvent résoudre les problèmes de l’entreprise à l’aide d’une approche de microservices.***

TISSU de service offre deux grands domaines pour vous aider à créer des applications avec une approche microservices :

- Une plate-forme qui offre des services système pour déployer, de mettre à niveau, de détecter et de redémarrer les services n’a pas pu découvrir l’emplacement du service, gérer l’état et surveiller l’état de santé. Ces services système permettent en effet la plupart des caractéristiques de microservices décrite précédemment.

-  API de programmation, ou des infrastructures, pour vous aider à créer des applications en tant que microservices : [acteurs fiables et services fiables](service-fabric-choose-framework.md). Bien sûr, vous pouvez utiliser n’importe quel code de votre choix pour créer votre microservice. Ces API rendre la tâche plus simple, mais ils s’intègrent à la plate-forme à un niveau inférieur. De cette manière, par exemple, vous pouvez obtenir des informations de santé et diagnostics ou vous pouvez tirer parti de haute disponibilité intégrée.

***Service de Fabric est agnostique sur la manière dont vous générez votre service, et vous pouvez utiliser n’importe quelle technologie. Toutefois, il fournit des API de programmation intégrés qui le rendent plus facile à build microservices.***

### <a name="are-microservices-right-for-my-application"></a>Sont microservices droite pour mon application ?

Peut-être. Ce que nous avons rencontré était qu’en plus des équipes dans Microsoft ont commencé à créer pour le nuage pour des raisons professionnelles, bon nombre d'entre eux bénéficié d’une approche de type microservice. Bing, par exemple, a développé microservices de recherche pour les années. Pour les autres équipes, l’approche microservices était nouvelle. Ils qui y trouvent dur pose un problème à résoudre en dehors de leurs zones principales de la résistance. C’est pourquoi le Service Fabric acquise traction comme la technologie de choix pour la création de services.

L’objectif de Service Fabric est de réduire la complexité de la création d’applications avec une approche microservice, afin que vous n’avez pas à effectuer en tant que nombre coûteuses redesigns. Commencez petit, mettre à l’échelle lorsque cela est nécessaire, services de déconseiller, ajouter de nouveaux et évoluer avec l’activité du client, qui est l’approche. Nous savons également que, en réalité, il existe de nombreux autres problèmes encore à être résolus pour que microservices soit plus abordable pour la plupart des développeurs. Conteneurs et le modèle de programmation acteur sont des exemples de petites étapes dans cette direction, et nous sommes certains que plusieurs innovations verront le jour pour faciliter cette opération.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations :
    * [Vue d’ensemble de la terminologie de tissu de service](service-fabric-technical-overview.md)
    * [Microservices : Une révolution application par le nuage](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)


[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
