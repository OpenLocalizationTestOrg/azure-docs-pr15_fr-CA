<properties
   pageTitle="Application web évolutive | Architecture de référence Azure | Microsoft Azure"
   description="Amélioration de l’évolutivité d’une application web en cours d’exécution dans Microsoft Azure."
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>Amélioration de l’évolutivité d’une application web 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article présente une architecture recommandée pour améliorer l’évolutivité et les performances d’une application web s’exécutant sur Microsoft Azure. S’appuie sur l’architecture [architecture de référence Azure : application web de base][basic-web-app]. Les recommandations et les considérations relatives à cet article s’appliquent à cette architecture ainsi.

>[AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : le Gestionnaire de ressources et classique. Cet article utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

## <a name="architecture-diagram"></a>Diagramme d’architecture

![[0]][0]

L’architecture comprend les éléments suivants :

- **Groupe de ressources**. Un [groupe de ressources] [ resource-group] est un conteneur logique pour les ressources d’Azure. 

- ** [Web app] [ app-service-web-app] ** et ** [API app][app-service-api-app]**. Une application moderne typique peut inclure à la fois un site Web et un ou plusieurs web API RESTful. Une API web susceptibles d’être utilisé par les clients de navigateur via AJAX, par les applications client natif ou par des applications côté serveur. Pour des considérations sur la conception web API, consultez [directives de conception d’API][api-guidance].    

- **WebJob**. Utilisez [Azure WebJobs] [ webjobs] pour exécuter des tâches de longue durée d’exécution en arrière-plan. WebJobs pouvez exécuter selon une planification, en permanence, ou en réponse à un déclencheur, telles que le placement d’un message dans une file d’attente. Un WebJob s’exécute comme un processus d’arrière-plan dans le contexte d’une application de Service de l’application. 

- **File d’attente**. Dans l’architecture illustrée ici, les files d’attente de l’application tâches en arrière-plan en plaçant un message vers une [File d’attente le stockage Azure] [ queue-storage] file d’attente. Le message déclenche une fonction dans le WebJob. 

    Vous pouvez également utiliser des files d’attente de Bus de Service. Pour une comparaison, consultez [files d’attente Azure et les files d’attente de Bus de Service - comparées et contrairement][queues-compared].

- **Cache**. Stocker des données semi-structurées statiques dans [Le Cache de Redis Azure][azure-redis].  

- **CDN**. Utiliser le [Réseau de diffusion de contenu Azure] [ azure-cdn] (CDN) pour cacher le contenu accessible au public, pour le temps de latence réduits et une livraison rapide de contenu.

- **Stockage de données.** Utiliser la [Base de données de SQL Azure] [ sql-db] pour les données relationnelles. Pour des données non relationnelles, envisagez un stockage NoSQL, telles que le stockage par Table Azure ou [DocumentDB][documentdb].

- **Recherche d’azure**. Utiliser la [Recherche d’Azure] [ azure-search] pour ajouter des fonctionnalités de recherche, y compris des suggestions de recherche, recherche floue et la recherche de spécifiques à une langue. Azure recherche est généralement utilisée conjointement avec un autre magasin de données, en particulier si le magasin de données principal nécessite la cohérence stricte. Dans cette approche, vous les données faisant autoritées dans l’autre magasin de données et placez l’index de recherche en recherche d’Azure. Recherche Azure peut également servir à consolider un index unique de recherche à partir de plusieurs magasins de données.  

- **E-mail/SMS**. Si votre application a besoin envoyer des messages électroniques ou SMS, utilisez un service tiers tels que SendGrid ou Twilio, plutôt que la création de cette fonctionnalité directement dans l’application.

## <a name="recommendations"></a>Recommandations

### <a name="app-service-apps"></a>Applications de Service d’application 

Nous vous recommandons de créer l’application web et l’API web comme des applications de Service d’application distinctes. Cette conception vous permet de les exécuter dans des plans de Service d’application distincts, qui à son tour vous permet de les faire évoluer indépendamment. Si vous n’avez pas besoin que niveau d’évolutivité à tout d’abord, vous pouvez déployer les applications dans le même plan et les déplacer dans des plans distincts par la suite, si nécessaire. (Pour les plans de Basic, Standard et Premium, vous êtes facturé pour les instances de la machine virtuelle dans le plan, pas par l’application. Voir [Service d’application de tarification][app-service-pricing])

Si vous envisagez d’utiliser *Facilement des Tables* ou des fonctions *API facile* d’applications de Mobile Application Service, créez une application de Service d’application distinct à cet effet.  Ces fonctionnalités reposent sur une infrastructure d’application spécifiques pour les activer.

### <a name="webjobs"></a>WebJobs

Si le WebJob est beaucoup de ressources, envisagez de déployer sur une application de Service de l’application vide dans un plan de Service d’application distinct, de fournir des instances dédiées pour la WebJob. Consultez les [conseils de travaux d’arrière-plan][webjobs-guidance].  

### <a name="cache"></a>Mémoire cache

Vous pouvez améliorer les performances et l’évolutivité à l’aide du [Cache de Azure Redis] [ azure-redis] pour mettre en cache des données. Envisagez d’utiliser des Redis de Cache pour :

- Données de transaction semi-statiques.

- État de session.

- Sortie HTML. Cela peut être utile dans les applications qui restituent la sortie HTML complexe. 

Pour plus d’instructions sur la conception d’une stratégie de mise en cache, consultez le [Guide de mise en cache][caching-guidance].

### <a name="cdn"></a>PAIE CANADA-FICHIER 

Utilisez [Azure CDN] [ azure-cdn] en cache le contenu statique. Le principal avantage d’un CDN est de réduire la latence pour les utilisateurs, car le contenu est mis en cache sur un *serveur de transport edge* qui soit géographiquement proche de l’utilisateur. CDN peut également réduire la charge sur l’application, parce que ce trafic n’est pas géré par l’application.

- Si votre application se compose principalement de pages statiques, envisagez d’utiliser le CDN pour mettre en cache l’ensemble de votre application. Reportez-vous à la section [utiliser Azure CDN dans le Service d’application Azure][cdn-app-service].

- Dans le cas contraire, placer du contenu statique, tels que les images, CSS et HTML des fichiers, dans le stockage Azure et utilisation CDN pour mettre en cache les fichiers. Voir [intégrer un compte de stockage avec CDN][cdn-storage-account].

> [AZURE.NOTE] Azure CDN ne peut pas fournir du contenu qui nécessite une authentification.

Pour plus d’instructions, consultez l’article [conseils de réseau CDN (Content Delivery)][cdn-guidance]. 

### <a name="storage"></a>Stockage

Applications modernes traitent souvent des grandes quantités de données. Pour mettre à l’échelle pour le cloud, il est important de choisir le type de stockage. Voici quelques recommandations de base.  Pour plus d’instructions, reportez-vous à la section [Évaluation de la capacité de stockage pour les Solutions de persistance Polyglot][polyglot-storage].

Ce que vous souhaitez stocker | Exemple | Stockage recommandée
--- | --- | ---
Fichiers | Images, documents, fichiers PDF | Stockage des objets Blob Azure
Paires clé/valeur. | Données de profil utilisateur recherchées par le code utilisateur | Stockage par Table Azure
Messages courts destinées à se déclencher le traitement | Demandes de commande | Stockage de la file d’attente Azure, file d’attente de Bus de Service ou rubrique de Bus de Service
Données non relationnelles, avec un schéma souple, nécessitant une interrogation de base | Catalogue de produits | Base de données de document, des DocumentDB d’Azure, MongoDB, CouchDB d’Apache
Données relationnelles, nécessitant une prise en charge de requête plus riche, schéma stricte et/ou la cohérence renforcée | Inventaire des produits | Base de données SQL Azure

## <a name="scalability-considerations"></a>Évolutivité

### <a name="app-service-app"></a>Application de Service de l’application

Si votre solution comprend plusieurs applications de Service d’application, envisagez de les déployer pour séparer des plans de Service de l’application. Cette approche vous permet de les faire évoluer indépendamment, car ils s’exécutent sur des instances distinctes. Pour plus d’informations sur la mise à l’échelle, consultez l' [évolutivité] [ basic-web-app-scalability] section dans [l’architecture de l’application web de base][basic-web-app].

De même, pensez à placer une WebJob dans son propre plan, afin que les tâches en arrière-plan ne s’exécutent pas sur les mêmes instances qui gèrent les demandes HTTP.  

### <a name="sql-database"></a>Base de données SQL

Augmenter l’évolutivité d’une base de données SQL en *ont* la base de données &mdash; , partitionnement horizontalement la base de données. Ont vous permet de faire évoluer horizontalement, de la base de données à l’aide des [Outils de base de données élastique][sql-elastic]. Les avantages potentiels d’ont :

- Meilleur débit de transaction.

- Requêtes peuvent s’exécuter plus rapidement sur un sous-ensemble de données. 

### <a name="azure-search"></a>Recherche Azure

Recherche Azure supprime la charge de l’exécution de recherches de données complexes à partir de la banque de données principale, et il peut évoluer pour gérer la charge. Voir les [niveaux d’échelle des ressources pour les requêtes et les charges de travail d’indexation dans Azure recherche][azure-search-scaling].

## <a name="security-considerations"></a>Considérations sur la sécurité

### <a name="cross-origin-resource-sharing-cors"></a>Ressource d’origine croisée partage (CORS)

Si vous créez un site Web et une API web comme des applications distinctes, le site Web ne peut pas effectuer les appels AJAX côté client de l’API, sauf si vous activez les CORS. 

> [AZURE.NOTE] Sécurité du navigateur empêche une page web d’effectuer des demandes AJAX dans un autre domaine. Cette restriction est appelée stratégie de même origine et un site malveillant empêche la lecture des données de sentitive à partir d’un autre site. CORS est une norme W3C qui permet à un serveur assouplir la stratégie de même origine et permettre à certaines demandes d’origine croisée lors du refus d’autres personnes.

Services d’application a une prise en charge intégrée pour les CORS, sans avoir à écrire de code d’application. Reportez-vous à [consommer une application API à partir de JavaScript à l’aide de CORS][cors]. Ajouter le site Web à la liste des origines autorisés pour l’API. 

### <a name="sql-database-encryption"></a>Cryptage de la base de données SQL

Utilisez le [Chiffrement de données Transparent] [ sql-encryption] si vous avez besoin crypter les données au repos dans la base de données. Cette fonction effectue en temps réel chiffrer et déchiffrer une base de données entière (y compris les sauvegardes et les fichiers journaux des transactions), sans nécessiter de modifications à l’application. Le cryptage ajoute une latence, il est recommandé de séparer les données qui doivent être sécurisés dans sa propre base de données et activer le cryptage uniquement pour cette base de données.  

## <a name="next-steps"></a>Étapes suivantes

- Pour une plus grande disponibilité, déployer l’application dans plusieurs régions et utiliser le [Gestionnaire de trafic Azure] [ tm] pour le basculement. Pour plus d’informations, consultez [architecture de référence Azure : application Web à haute disponibilité][web-app-multi-region].    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Applications Web dans Azure avec une plus grande évolutivité"