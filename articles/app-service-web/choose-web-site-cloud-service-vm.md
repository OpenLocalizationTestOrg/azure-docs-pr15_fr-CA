<properties
    pageTitle="Comparaison de Service d’application, les ordinateurs virtuels, les Fabric de Service et les Services en nuage Azure | Microsoft Azure"
    description="Découvrez comment choisir entre le Service d’application Azure, Machines virtuelles, Fabric de Service et les Services en nuage pour l’hébergement d’applications web."
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparaison de Service d’application, les ordinateurs virtuels, les Fabric de Service et les Services en nuage Azure

## <a name="overview"></a>Vue d’ensemble

Azure offre plusieurs méthodes pour héberger des sites web : [Service d’application Azure][], [Machines virtuelles][], [Fabric de Service][]et [Les Services en nuage][]. Cet article vous aide à comprendre les options et de faire le bon choix pour votre application web.

Service d’application Azure est le meilleur choix pour la plupart des applications web. Déploiement et gestion sont intégrées à la plate-forme, sites peuvent évoluer rapidement pour gérer des charges de trafic élevé, et le Gestionnaire de trafic et de l’équilibrage de la charge intégré une haute disponibilité. Vous pouvez déplacer facilement des sites existants pour le Service d’application Azure avec un [outil de migration en ligne](https://www.migratetoazure.net/), utiliser une application open source à partir de la galerie d’applications Web ou créer un nouveau site à l’aide de la structure et les outils de votre choix. La fonctionnalité [WebJobs][] facilite ajouter la tâche d’arrière-plan de traitement à votre application Service web app.

Service de Fabric est un bon choix si vous créez une nouvelle application ou ré-écriture d’une application existante pour utiliser une architecture microservice. Les applications qui s’exécutent sur un pool partagé de machines, peuvent commencez petites et évoluez à grande échelle avec des centaines ou des milliers d’ordinateurs en fonction des besoins. Avec état services facilitent l’utilisation de manière cohérente et fiable stocker l’état de l’application et Service de Fabric gère automatiquement la partition du service, l’évolutivité et la disponibilité pour vous.  TISSU de service prend également en charge les WebAPI avec Open Interface Web pour .NET (OWIN) et le noyau d’ASP.NET.  Comparé au Service de l’application, Service Fabric fournit également plus contrôler, ou un accès direct à l’infrastructure sous-jacente. Vous pouvez à distance sur vos serveurs ou que vous configurez les tâches de démarrage du serveur. Services en nuage est similaire au Fabric du Service dans le degré de contrôle et la simplicité d’utilisation, mais il est désormais un service hérité et Fabric du Service est recommandé pour un nouveau développement.

Si vous avez une application qui nécessite des modifications substantielles à exécuter dans le Service d’application ou Service de Fabric, vous pouvez choisir des Machines virtuelles afin de simplifier la migration vers le nuage. Cependant, correctement configuration, sécurisation et maintenance de machines virtuelles nécessite beaucoup plus de temps et de ressources informatiques par rapport au Service d’application Azure et Service de Fabric. Si vous envisagez d’ordinateurs virtuels Azure, assurez-vous de que prendre en compte l’effort de maintenance continue pour les corriger, mettre à jour et gérer votre environnement de machine virtuelle. Les Machines virtuelles Azure est Infrastructure-as-a-Service (IaaS), tandis que le Service de l’application et le Service Fabric sont plate-forme-as-a-Service (Paas). 

## <a name="features"></a>Comparaison des fonctionnalités

Le tableau suivant compare les fonctionnalités de Service d’application, les Services en nuage, Machines virtuelles et Service de Fabric, pour vous aider à effectuer le meilleur choix. Pour obtenir des informations en cours sur le contrat SLA pour chaque option, consultez [Les contrats de niveau de Service Azure](/support/legal/sla/).

Fonctionnalité|Service d’application (applications web)|Services en nuage (rôles web)|Ordinateurs virtuels|TISSU de service|Notes
---|---|---|---|---|---
Déploiement de quasi instantanée|X|||X|Déploiement d’une application ou une mise à jour de l’application à un Service en nuage ou de la création d’une machine virtuelle, il prend plusieurs minutes au moins ; déploiement d’une application à une application web prend secondes.
Évoluer vers des machines plus grandes sans redéploiement|X|||X|
Les instances de serveur Web partagent du contenu et configuration, ce qui signifie que vous n’êtes pas obligé de redéployer ou de reconfigurer la mise à l’échelle.|X|||X|
Plusieurs environnements de déploiement (de production et de mise en attente)|X|X||X|TISSU de service vous permet d’avoir plusieurs environnements pour vos applications ou de déployer différentes versions de votre application côte à côte.
Gestion automatique de la mise à jour du système d’exploitation|X|X|||Mises à jour automatiques de système d’exploitation sont planifiés pour un tissu de Service futur version.
Changement de plate-forme transparente (déplacer facilement entre 32 et 64 bits)|X|X|||
Déployer le code avec GIT, FTP|X||X||
Déployer le code avec le déploiement Web|X||X||Les Services en nuage prend en charge l’utilisation de Web Deploy pour déployer des mises à jour sur des instances de rôle individuel. Toutefois, vous ne pouvez pas l’utiliser pour le déploiement initial d’un rôle, et si vous utilisez l’option déploiement Web pour une mise à jour vous devez déployer séparément pour chaque instance d’un rôle. Plusieurs instances sont nécessaires pour pouvoir bénéficier du nuage de Service SLA pour les environnements de production.
Prise en charge de WebMatrix|X||X||
Accès à des services tels que le Bus de Service, le stockage de la base de données SQL|X|X|X|X|
Web de l’hôte ou de la couche de services web d’une architecture à plusieurs niveaux|X|X|X|X|
Niveau intermédiaire de l’hôte d’une architecture à plusieurs niveaux|X|X|X|X|Applications de Service de l’application web peuvent héberger facilement d’un intermédiaire de l’API REST, et la fonction [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) peut héberger le traitement des tâches en arrière-plan. Vous pouvez exécuter WebJobs dans un site Web dédié à l’évolutivité indépendante de la couche. La fonction [d’API applications](../app-service-api/app-service-api-apps-why-best-platform.md) aperçu fournit davantage de fonctionnalités pour l’hébergement de services REST.
Prise en charge intégrée de MySQL-as-a-service|X|X|X||Les Services en nuage peuvent intégrer MySQL-as-a-service grâce à ses solutions de ClearDB, mais pas dans le cadre du flux de travail Azure Portal.
Prise en charge de ASP.NET, classique ASP, Node.js, PHP, Python|X|X|X|X|TISSU de service prend en charge la création d’un site web frontal [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) ou vous permettent de déployer tout type d’application (Node.js, Java, etc.) en tant qu' [invité exécutable](../service-fabric/service-fabric-deploy-existing-app.md).
Évolutivité à plusieurs instances, sans redéploiement|X|X|X|X|Machines virtuelles peut évoluer à plusieurs instances, mais les services s’exécutant sur ceux-ci doivent être écrit pour gérer cette montée. Vous devez configurer un équilibreur de charge pour acheminer les requêtes sur les ordinateurs et de créer un groupe d’affinité pour empêcher le redémarrage simultané de toutes les instances en raison d’échecs de maintenance ou de matériel.
Prise en charge de SSL|X|X|X|X|Pour les applications du Service d’application web, SSL pour les noms de domaine personnalisé est uniquement pris en charge pour le mode de base et Standard. Pour plus d’informations sur l’utilisation de SSL avec les applications web, consultez [configuration d’un certificat SSL pour un site Web d’Azure](../app-service-web/web-sites-configure-ssl-certificate.md).
Intégration de Visual Studio|X|X|X|X|
Le débogage distant|X|X|X||
Déployer le code avec TFS|X|X|X|X|
Isolement de réseau à [Réseau virtuel d’Azure](/services/virtual-network/)|X|X|X|X|Voir aussi [intégration réseau virtuel de sites Web Azure](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Prise en charge de [Gestionnaire de trafic Azure](/services/traffic-manager/)|X|X|X|X|
Surveillance des postes clients intégrée|X|X|X||
Accès Bureau à distance à des serveurs||X|X|X|
Installez tout MSI personnalisé||X|X|X|TISSU de service vous permet d’héberger n’importe quel fichier exécutable en tant qu' [invité exécutable](../service-fabric/service-fabric-deploy-existing-app.md) ou vous pouvez installer n’importe quelle application sur les ordinateurs virtuels.
Possibilité de définir et d’exécution des tâches de démarrage||X|X|X|
Pouvez écouter des événements ETW||X|X|X|

## <a name="scenarios"></a>Les recommandations et les scénarios

Voici quelques scénarios d’application courants avec des recommandations en ce qui concerne le Azure web option d’hébergement peut être plus approprié pour chaque.

- [J’ai besoin d’un site web frontal avec le serveur principal de base de données et de traitement en arrière-plan pour exécuter des applications d’entreprise intégrées à des actifs du site.](#onprem)
- [J’ai besoin d’un moyen fiable pour l’hôte Mon site Web d’entreprise qui est évolutif et offre globale atteint.](#corp)
- [J’ai une application IIS6 s’exécutant sur Windows Server 2003.](#iis6)
- [Je suis un propriétaire de petite entreprise, et j’ai besoin d’un moyen économique d’hôte Mon site, mais avec la croissance à venir à l’esprit.](#smallbusiness)
- [Je suis un site ou un concepteur graphique, et je veux concevoir et créer des sites web de mes clients.](#designer)
- [Je suis migrer mon application à plusieurs niveaux avec un site web frontal vers le nuage.](#multitier)
- [Mon application dépend fortement personnalisées de Windows ou les environnements Linux et souhaitez déplacer vers le nuage.](#custom)
- [Mon site utilise le logiciel open source, et je souhaite héberger dans Azure.](#oss)
- [J’ai une application de métier qui doit se connecter au réseau d’entreprise.](#lob)
- [Je souhaite héberger une API REST ou un service web pour les clients mobiles.](#mobile)


### <a id="onprem"></a>J’ai besoin d’un site web frontal avec le serveur principal de base de données et de traitement en arrière-plan pour exécuter des applications d’entreprise intégrées à des actifs du site.

Service d’application Azure est une solution idéale pour les applications d’entreprise complexes. Il vous permet de développer des applications qui adapte automatiquement sur une plate-forme de charge équilibrée, sont sécurisées avec Active Directory et vous connecter à vos ressources sur site. Il facilite la gestion de ces applications faciles via un portail hors pair et d’une API et vous permet de comprendre comment les clients les utilisent avec les outils d’application insight. La fonction [Webjobs][] vous permet d’exécuter des processus d’arrière-plan et des tâches dans le cadre de votre couche web, lors de la connectivité des hybrides et des fonctionnalités VNET facilitent l’utilisation pour se connecter aux ressources de locaux. Service d’application Azure fournit SLA du trois 9 pour les applications web et vous permet de :

* Exécuter vos applications de manière fiable sur une plate-forme de nuage d’auto-adaptation, correction automatique.
* Sur un réseau mondial de centres de données à l’échelle automatiquement.
* Sauvegarde et restauration de reprise après sinistre.
* Être conformes ISO, SOC2 et PCI.
* Intégrer avec Active Directory

### <a id="corp"></a>J’ai besoin d’un moyen fiable pour l’hôte Mon site Web d’entreprise qui est évolutif et offre globale atteint.

Service d’application Azure est une excellente solution pour l’hébergement de sites Web d’entreprise. Il permet à des applications web à évoluer rapidement et facilement pour répondre à la demande sur un réseau mondial de centres de données. Il offre une portée locale, tolérance de pannes et la gestion du trafic intelligent. Sur une plateforme qui fournit des outils de gestion de niveau international, vous permettant ainsi de comprendre dans le fonctionnement du site et le trafic du site rapidement et facilement. Service d’application Azure fournit SLA du trois 9 pour les applications web et vous permet de :

* Fonctionner de manière fiable vos sites Web sur une plate-forme de nuage d’auto-adaptation, correction automatique.
* Sur un réseau mondial de centres de données à l’échelle automatiquement.
* Sauvegarde et restauration de reprise après sinistre.
* Gérer les journaux et le trafic avec des outils intégrés.
* Être conformes ISO, SOC2 et PCI.
* Intégrer avec Active Directory

### <a id="iis6"></a>J’ai une application IIS6 s’exécutant sur Windows Server 2003.

Service d’application Azure facilite l’afin d’éviter les coûts d’infrastructure liés à la migration d’anciennes applications IIS6. Microsoft a créé des [Outils de migration facile à utiliser et les instructions de migration détaillées](https://www.movemetowebsites.net/) qui vous permettent de vérifier la compatibilité et à identifier les modifications qui doivent être apportées. Intégration avec les outils courants de CMS, Visual Studio et TFS rend facile à déployer des applications IIS6 directement vers le nuage. Une fois déployé, le portail Azur fournit des outils de gestion robustes qui vous permettent de mettre à l’échelle vers le bas pour gérer les coûts et à répondre à la demande si nécessaire. Avec l’outil de migration, vous pouvez :

* Rapidement et facilement migrer votre application de web de Windows Server 2003 existant vers le nuage.
* Choisir de laisser votre attaché SQL de base de données sur site pour créer une application hybride.
* Déplacer automatiquement votre base de données SQL avec votre application héritée.

### <a id="smallbusiness"></a>Je suis un propriétaire de petite entreprise, et j’ai besoin d’un moyen économique d’hôte Mon site, mais avec la croissance à venir à l’esprit.

Service d’application Azure est une excellente solution pour ce scénario, car vous pouvez l’utiliser gratuitement et ensuite ajouter des fonctionnalités supplémentaires lorsque vous en avez besoin. Chaque application web gratuit est fourni avec un domaine fourni par Azure (*your_company*. azurewebsites.net), et la plate-forme comprend des outils de déploiement et de gestion intégrées, mais aussi une galerie d’applications qui facilitent la mise en route. Il existe de nombreux autres services et options de mise à l’échelle qui permettent au site d’évoluer avec la demande de l’utilisateur. Avec le Service d’application Azure, vous pouvez :

- Commencer par la couche libre et puis évoluer en fonction des besoins.
- La galerie d’applications permet de configurer rapidement des applications web les plus courants, par exemple WordPress.
- Ajouter des services Azure supplémentaires et des fonctionnalités à votre application en fonction des besoins.
- Sécuriser votre application web avec le protocole HTTPS.

### <a id="designer"></a>Je suis un site ou un concepteur graphique et vous voulez à concevoir et à créer des sites Web de mes clients

Pour les développeurs et concepteurs web, Service d’application Azure s’intègre facilement avec une variété d’outils et d’infrastructures, déploiement prend en charge Git et FTP et offre une intégration étroite avec des outils et des services tels que Visual Studio et de la base de données SQL. Avec le Service de l’application, vous pouvez :

- Utilisez les outils de ligne de commande de [tâches automatisées de][scripting].
- Travailler avec des langages courants tels que [.net][dotnet], [PHP][], [Node.js][nodejs]et les [Python][].
- Sélectionnez les trois différents niveaux de mise à l’échelle pour la mise à l’échelle capacités très élevées.
- Intégration avec d’autres services, tels que [De la base de données SQL]Azure[sqldatabase], [Bus de Service de] [ servicebus] et de [stockage][], ou de nos partenaires à partir de la [Banque d’Azure][azurestore], comme MySQL et MongoDB.
- Intégration avec les outils de Visual Studio, Git, WebMatrix, WebDeploy, TFS et FTP.

### <a id="multitier"></a>Je suis migrer mon application à plusieurs niveaux avec un site web frontal vers le nuage

Si vous exécutez une application multicouche, telle qu’un serveur web qui se connecte à une base de données, Service d’application Azure est une bonne option qui offre une intégration étroite avec la base de données de SQL Azure. Et bien, vous pouvez utiliser la fonction WebJobs pour l’exécution des processus de back-end.

Choisissez Service Fabric pour une ou plusieurs de vos niveaux si vous avez besoin plus de contrôle sur l’environnement de serveur, telles que la capacité à distance à votre serveur ou configurer les tâches de démarrage du serveur.

Choisir des Machines virtuelles pour un ou plusieurs de vos niveaux si vous souhaitez utiliser votre propre image d’ordinateur ou d’exécuter le logiciel serveur ou les services que vous ne pouvez pas configurer sur Fabric du Service.

### <a id="custom"></a>Mon application dépend fortement personnalisées de Windows ou les environnements Linux et souhaitez déplacer vers le nuage.

Si votre application nécessite une installation complexe ou configuration du logiciel et du système d’exploitation, les Machines virtuelles est probablement la meilleure solution. Avec les Machines virtuelles, vous pouvez :

- Utilisez la galerie de Machine virtuelle pour démarrer un système d’exploitation, comme Windows ou Linux et puis les personnaliser en fonction de vos besoins d’application.
- Créer et télécharger une image personnalisée d’un serveur local pour s’exécuter sur une machine virtuelle dans Azure.

### <a id="oss"></a>Mon site utilise le logiciel open source, et je souhaite héberger dans Azure

Si votre infrastructure open source est pris en charge dans le Service d’application, les langues et les infrastructures requises par votre application sont configurés automatiquement pour vous. Service d’application vous permet de :

- Utiliser les nombreuses open source langages courants, tels que [.NET][dotnet], [PHP][], [Node.js][nodejs]et les [Python][].
- Permet de paramétrer WordPress, Drupal, Umbraco, DNN et bien d’autres applications web tiers.
- Migration d’une application existante ou créer une nouvelle à partir de la bibliothèque de l’Application.

Si votre infrastructure open source n’est pas pris en charge sur le Service de l’application, vous pouvez l’exécuter sur l’un des autre Azure web options pour abriter. Avec les Machines virtuelles, vous installez et configurez le logiciel sur l’image de l’ordinateur, qui peut être Windows ou Linux.

### <a id="lob"></a>Je possède une application de métier qui doit se connecter au réseau d’entreprise

Si vous souhaitez créer une application de métier, votre site Web peut nécessiter un accès direct à des services ou des données sur le réseau d’entreprise. Cela est possible sur le Service d’application et des Machines virtuelles à l’aide du [service réseau virtuel d’Azure](/services/virtual-network/)Fabric du Service. Service de l’application, vous pouvez utiliser la [fonctionnalité d’intégration de VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), qui permet à vos applications Azure pour s’exécuter comme si elles étaient sur votre réseau d’entreprise.

### <a id="mobile"></a>Je souhaite héberger une API REST ou un service web pour les clients mobiles

Services web basés sur HTTP permettent de prendre en charge un large éventail de clients, y compris les clients mobiles. Intégrant des structures telles que les API de Web ASP.NET avec Visual Studio pour le rendre plus facile à créer et consommer des services REST.  Ces services sont exposés à partir d’un point de terminaison web, il est possible d’utiliser un site web hébergeant technique sur Azure pour prendre en charge ce scénario. Toutefois, le Service d’application est un excellent choix pour d’autres API d’hébergement. Avec le Service de l’application, vous pouvez :

- Créer rapidement une [application mobile](../app-service-mobile/app-service-mobile-value-prop.md) ou une [API app](../app-service-api/app-service-api-apps-why-best-platform.md) pour héberger le service web HTTP dans un des centres de données d’Azure réparti à échelle mondiale.
- Migrer des services existants ou créer de nouveaux.
- Respecter les SLA pour une disponibilité et une seule instance ou évoluer vers plusieurs ordinateurs dédiés.
- Utiliser le site publié pour fournir des API de reste à tous les clients HTTP, y compris les clients mobiles.

> [AZURE.NOTE]
> Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement créer une application de démarrage de courte durée dans le Service d’application Azure gratuitement. Aucune carte de crédit ne nécessaire, aucun engagement.

## <a id="nextsteps"></a>Étapes suivantes

Pour plus d’informations sur le web de trois options d’hébergement, consultez [Présentation de Azure](../fundamentals-introduction-to-azure.md).

Pour vous familiariser avec les options que vous choisissez pour votre application, consultez les ressources suivantes :

* [Service d’application Azure](/documentation/services/app-service/)
* [Services en nuage Azure](/documentation/services/cloud-services/)
* [Machines virtuelles Azure](/documentation/services/virtual-machines/)
* [TISSU de service](/documentation/services/service-fabric)

<!-- URL List -->

[Service d’application Azure]: /services/app-service/
[Services en nuage]: http://go.microsoft.com/fwlink/?LinkId=306052
[Ordinateurs virtuels]: http://go.microsoft.com/fwlink/?LinkID=306053
[TISSU de service]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Stockage]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
